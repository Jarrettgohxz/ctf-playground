# Introduction
This challenge requires ...
Custom challenge link: https://tryhackme.com/room/defcon2026ntusentinels

**1. Network port enumeration**

```shell
nmap <TARGET_IP> -sS -p-
```
- Note that the `-F` (fast scan) port scan option will not work
- We have to explicitly specify the `-p-` option to scan the entire range of ports
<img width="790" height="357" alt="image" src="https://github.com/user-attachments/assets/d771741f-5f04-4c21-92d1-9c165f396dcb" />

**2. Port 8080**
- Navigating to port 8080, we should see the following:
<img width="1901" height="760" alt="image" src="https://github.com/user-attachments/assets/c886398b-ca99-45f6-8e08-44d4801a85c8" />

- `/config` page:
<img width="1901" height="760" alt="image" src="https://github.com/user-attachments/assets/e3f67948-d0ce-44e2-8d6c-4a8196ec6252" />


**3. Port 22222**
- We are presented with a boot log screen
<img width="1894" height="760" alt="image" src="https://github.com/user-attachments/assets/42e4c2c4-78d5-43b9-b2d0-e2b0df763f2f" />

- Suspicious looking commands:
<img width="613" height="68" alt="image" src="https://github.com/user-attachments/assets/309d9ba1-41d9-47e9-8417-b3e2ca9ebeb3" />
It appears that ...


**3. Interacting with web configuration page on port 8080 `/config` page**
<img width="714" height="422" alt="image" src="https://github.com/user-attachments/assets/67f24218-da2f-46d4-b9e5-b383f610f286" />

- Notice that the value `val3` (input to "Router IP" field) appears in the boot logs:
<img width="654" height="619" alt="image" src="https://github.com/user-attachments/assets/aca116ef-0887-4019-9c79-df517d020e3e" />

- Also, we get an error message: "Failed to connect to Central Management Server at XXXX":
<img width="654" height="309" alt="image" src="https://github.com/user-attachments/assets/71bf3e1e-5805-4d74-b473-85f0bca9d5e7" />

This error message indicates that we have given an invalid IP address value to the "Central Management Server IP" field in the web configuration page:


<img width="654" height="429" alt="image" src="https://github.com/user-attachments/assets/173be35f-4e14-439a-8d05-d2739ba1a847" />
<img width="654" height="428" alt="image" src="https://github.com/user-attachments/assets/a6fdd0e1-4f35-4d01-a2bc-298a7997c48d" />
<img width="654" height="331" alt="image" src="https://github.com/user-attachments/assets/ce8fc0e9-65d7-4a86-9991-dcbf11e464ea" />
<img width="736" height="439" alt="image" src="https://github.com/user-attachments/assets/6760d15b-d4be-4be1-a064-cd87b7902e4b" />
<img width="736" height="336" alt="image" src="https://github.com/user-attachments/assets/3e9b96f1-6560-4c4b-acc5-de81a994d977" />
<img width="438" height="89" alt="image" src="https://github.com/user-attachments/assets/b8028d4b-d88b-40bc-a1bd-922c00e96520" />

<img width="657" height="344" alt="image" src="https://github.com/user-attachments/assets/1158ad73-72f4-4da5-b1c2-601356c98397" />
<img width="436" height="53" alt="image" src="https://github.com/user-attachments/assets/abe464bb-d7a0-4b5c-9803-47bc2d3ef9fc" />
<img width="783" height="25" alt="image" src="https://github.com/user-attachments/assets/7044c7f9-bdab-4d57-9a09-202d2d9d2600" />

<img width="627" height="46" alt="image" src="https://github.com/user-attachments/assets/5e90481f-54b7-4dcb-a5aa-36fcb05d02bc" />
<img width="627" height="75" alt="image" src="https://github.com/user-attachments/assets/b9c3a048-af57-45a7-a3bc-5a8299173e0c" />
<img width="392" height="37" alt="image" src="https://github.com/user-attachments/assets/2e4a52e3-4c4f-47cc-814b-c11b5a166e7f" />

<img width="830" height="88" alt="image" src="https://github.com/user-attachments/assets/ec39db4c-30ea-4532-b779-30c1e4fde411" />
<img width="431" height="40" alt="image" src="https://github.com/user-attachments/assets/7f46584d-f210-4a93-944e-b4602318f451" />
<img width="477" height="47" alt="image" src="https://github.com/user-attachments/assets/e0aeb911-8a52-45fc-93b5-b3828c284f7b" />























