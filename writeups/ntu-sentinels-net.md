# Introduction
Custom challenge link: https://tryhackme.com/room/defcon2026ntusentinels

The challenge presents a simulated environment from an IoT (wireless router) environment, such as the device boot logs (eg. via UART shell) and web configuration form.

This challenge utilizes knowledge from the following techniques:
1. Network enumeration (port scanning)
2. OS command injection
3. Privilege escalation (SUID binary)

# Steps

**1. Network port enumeration**

```shell
nmap <TARGET_IP> -sS -p-
```
- Note that the `-F` (fast scan) port scan option will not work
- We have to explicitly specify the `-p-` option to scan the entire range of ports
<img width="790" height="357" alt="image" src="https://github.com/user-attachments/assets/d771741f-5f04-4c21-92d1-9c165f396dcb" />

Found ports:

1. `8080` (likely web server)
2. `22222` (unknown)
3. `22` (likely SSH)

**2. Port 8080**
- Navigating to port 8080, we will see a wireless router version screen:
<img width="1901" height="760" alt="image" src="https://github.com/user-attachments/assets/c886398b-ca99-45f6-8e08-44d4801a85c8" />

- As given in the challenge description, we can navigate to `/config` and view the web configuration page:
<img width="1901" height="760" alt="image" src="https://github.com/user-attachments/assets/e3f67948-d0ce-44e2-8d6c-4a8196ec6252" />


**3. Port 22222**
- Boot log screen
<img width="1894" height="760" alt="image" src="https://github.com/user-attachments/assets/42e4c2c4-78d5-43b9-b2d0-e2b0df763f2f" />

Notice the following suspicious commands:
<img width="613" height="68" alt="image" src="https://github.com/user-attachments/assets/309d9ba1-41d9-47e9-8417-b3e2ca9ebeb3" />

We are able to observe the following pattern:

  1. A command is executed and stored in the `status` variable
  2. The value in that variable is sent to an IP address on port 1234 via a Netcat (TCP) connection

Given that an attacker is able to control the values inserted directly into the boot logs, they will be able to maliciously inject commands that may be executed by the system. This presents a classic case of OS command injection.


**4. Interacting with web configuration page on port 8080 `/config` page**

Now, we can attempt to discover the web configuration form fields which are directly inserted into the boot logs. Let's start off by inserting controlled values (eg. `val1`, `val2`, etc. in the respective inputs):

<img width="714" height="422" alt="image" src="https://github.com/user-attachments/assets/67f24218-da2f-46d4-b9e5-b383f610f286" />

- Notice the following results:
1. The value `val3` (input to "Router Name" field) appears in the boot logs
2. We get an error message: "Failed to connect to Central Management Server at XXXX"

<img width="659" height="103" alt="image" src="https://github.com/user-attachments/assets/cd086936-5a37-462c-befa-dca430f05d3c" />

We are able to learn the following from the results:

1. The input to the "Router Name" field appears directly in the boot logs
2. The error message indicates that we have given an invalid IP address value to the "Central Management Server IP" field in the web configuration page


We have now discovered the injection points for the OS command injection vulnerability. Since the first question asks for values found within a hidden file, we can attempt to identify the file name using the `ls -a` command. Also, we have to insert a few special shell characters such as: `;`, `|` to "break out" from the current command:

1. `; ls`
2. `| la` 

Note that the following commands above should be given to the "Router Name" form field, while the "Central Management Server IP" field should contain our attacker machine's IP address. Also, we have to start a listener on port 1234 (as identified from the boot logs previously):

```shell
$ nc -lvkp 1234
```

After submitting the form, we can navigate to the boot logs screen (port 22222) and observe the following results:
> Note that `10.49.154.80` is the IP address of the attacker machine. This value may be different from yours.

1. For both the inputs to the "Router Name" field, the special shell characters (`;`, `|`) are filtered/removed
2. The system is able to successfully connect to the "Central Management Server" 

<img width="622" height="57" alt="image" src="https://github.com/user-attachments/assets/a0b49a22-b964-4464-b361-4477f7678d46" />

Hmm ... we have to somehow find a way to bypass the filters, and trick the system to "break out" of the current command. Taking a look at the hint: "I wonder how we can make commands appear on the next line (url-encoded)?", this provides a hint toward the url-encoded version of the newline character: `%0a`. 

**5. Solving task 1**

Let's try the following input to the "Router Name" field: `%0als -a`. We can observe the following results:

1. A newline is shown on the boot logs screen with our custom command: `ls -a`
2. Our listener (on port 1234) has captured an output indicating a hidden file: `.task1`

<img width="659" height="71" alt="image" src="https://github.com/user-attachments/assets/0b4ba46b-c937-4f28-924c-e482554f88c6" />
<img width="438" height="89" alt="image" src="https://github.com/user-attachments/assets/b8028d4b-d88b-40bc-a1bd-922c00e96520" />

Read the contents of the file: `%0a cat .task1`
<img width="436" height="53" alt="image" src="https://github.com/user-attachments/assets/abe464bb-d7a0-4b5c-9803-47bc2d3ef9fc" />

With that, we have found the answer for the first task: `task2:O$_cm6_1njection`


**6. Solving task 2**

For the 2nd task, we are required to to read the content of **flag.txt**. Let's try to directly read the file using the following input ("Router Name" field): `%0a cat flag.txt`. The boot log screen shows that the command has been successfully injected. However, our listener on port 1234 does not show anything.

<img width="659" height="71" alt="image" src="https://github.com/user-attachments/assets/ff655555-f2f1-423d-8137-f89bed480219" />

Taking a look at the hint: "I wonder where we can use the found credentials?". This points us towards the SSH service running on port 22 as found previously. 

Using the found credentials for user `task2`, we will be presented with an restricted-bash (rbash) shell:
> Note that `10.49.185.193` is the IP address of the target machine in our case
<img width="783" height="25" alt="image" src="https://github.com/user-attachments/assets/7044c7f9-bdab-4d57-9a09-202d2d9d2600" />
<img width="221" height="50" alt="image" src="https://github.com/user-attachments/assets/768d69f6-ee6b-4a42-b8ba-c58d2f3e752b" />

Read the content of `flag.txt`:

<img width="627" height="46" alt="image" src="https://github.com/user-attachments/assets/5e90481f-54b7-4dcb-a5aa-36fcb05d02bc" />

We have found the flag for task 2: `NTU_Sentinels{0S_cm&_1nj#cti0n_i$_sC4ry}`

<img width="392" height="37" alt="image" src="https://github.com/user-attachments/assets/2e4a52e3-4c4f-47cc-814b-c11b5a166e7f" />


**7. Solving task 3**

For task 3, we are required to read the contents of `flag.txt` in the root directory. This means that we are required to perform privilege escalation.

We can start off with a few common privilege escalation techniques
1. sudo permissions
<img width="348" height="44" alt="image" src="https://github.com/user-attachments/assets/a59dc056-4f15-4c50-9c6d-147e29da8f57" />

- `sudo` command not found

2. SUID bit
- This command effectively attempts to find all the files with SUID permission set
```shell
$ find / -type f -perm -u=s
```
We have found that the `find` binary has SUID bit set. It also happens to be owned by the **root** user:
<img width="469" height="43" alt="image" src="https://github.com/user-attachments/assets/ed4f162d-8423-4264-989f-31c432ab962d" />

- Execute custom command to activate an rbash session:

```shell
find . -exec rbash -p\; -quit
```
<img width="431" height="40" alt="image" src="https://github.com/user-attachments/assets/7f46584d-f210-4a93-944e-b4602318f451" />

- Read content of `/root/flag.txt`:
<img width="477" height="47" alt="image" src="https://github.com/user-attachments/assets/e0aeb911-8a52-45fc-93b5-b3828c284f7b" />

Flag for task 3: `NTU_Sentinels{priv3sc_t0_ro0t}`

## Note on task 3

There exists a useful site that provides a list of techniques for privilege escalation (https://gtfobins.org). The search term is also given in the hint: "Search "site:gtfobins.org find SUID ...".

> https://gtfobins.org/gtfobins/find/
<img width="830" height="88" alt="image" src="https://github.com/user-attachments/assets/ec39db4c-30ea-4532-b779-30c1e4fde411" />























