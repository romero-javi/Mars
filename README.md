# Zimbra Installation

This is a guide to reproduce a vulnerable zimbra containing the [CVE-2022-37042](https://nvd.nist.gov/vuln/detail/CVE-2022-37042) and [CVE-2022-37393](https://nvd.nist.gov/vuln/detail/CVE-2022-37393) vulnerabilities.

### Future Deprecation Solution

> [!NOTE]
> Continue to [normal installation](#normal-instalation) unless you already did and installation failed because of **missing packages**.

If one or more needed packages have been deleted from the official zimbra 8 repository, you can refer to this [google drive location](https://drive.google.com/file/d/1HkM6MKY9Gfv94TY2hVmWfXnR8GejD1Fh/view?usp=sharing) to download a ```tar.gz``` file which contains all needed packages for the successful installation of zimbra 8.8.15 patch 32.

After having downloaded the file, move it inside the cloned repository and extract its contents as following, you will end up with a directory named ```zimbra-8.8.15.p32-packages```.

```bash
tar -xzf zimbra-8p32-packages.tar.gz zimbra-8.8.15.p32-packages
```

> [!IMPORTANT]
> The name of the directory containing the packages **MUST** be ```zimbra-8.8.15.p32-packages```

Now you can proceed with the normal installation process.

### Normal Instalation

> [!IMPORTANT]
> The following must run with root user privileges

Being inside the cloned repository, run the installation script.

```bash
sudo ./install-zimbra.sh
```

Wait until the ```Starting zimbra installation...``` message appears, you will be asked for input like ```Y``` for **Yes** or ```N``` for **NO** and more, be sure to do it as follows.

- First two inputs can be either ```Y``` or ```N``` as they are related to policies and agreements.

- Then it will be prompted ```Use Zimbra's package repository [Y]``` to which we MUST enter ```N``` in order for this to work.

> [!WARNING]
> If you enter ```Y``` the official zimbra 8 repository will be constructed and it will take precedence over the local repository for patch 32.

- Then packages installation will be prompted out, input has to be as in the following table in order for the installation to complete successfully.

| Package                   | Input |
|---------------------------|-------|
|        zimbra-ldap        |   Y   |
|       zimbra-logger       |   Y   |
|         zimbra-mta        |   Y   |
|      zimbra-dnscache      |   N   |
|        zimbra-snmp        |   Y   |
|        zimbra-store       |   Y   |
|       zimbra-apache       |   Y   |
|        zimbra-spell       |   Y   |
|      zimbra-convertd      |   Y   |
|      zimbra-memcached     |   Y   |
|        zimbra-proxy       |   Y   |
|      zimbra-archiving     |   N   |
|        zimbra-drive       |   N   |
|        zimbra-imapd       |   N   |
| zimbra-network-modules-ng |   N   |
|        zimbra-chat        |   N   |


- It will be prompted ```The system will be modified.  Continue? [N]```, enter has to be ```Yes``` for the installation to continue.

- Then ```Change domain name? [Yes]```, press the ```return``` key or enter ```Yes``` and then press the ```return``` key.

- Now you will be prompted ```Create domain: [mail.example.com]``` and to which you will have to input the domain name part of the computer's hostname, e.g for hostname ```mail.company.net```, enter has to be ```company.net```. 

- A menu will be displayed, enter ```6``` to select zimbra store then enter ```4``` to set a password for the admin console, introduce a password and enter ```r``` before pressing the ```return``` key to go to previous menu.

- Now you have to open another terminal instance and edit ```/opt/zimbra/bin/zmcertmgr```, you will need root privileges to be able to edit the file.

```bash
sudo vim /opt/zimbra/bin/zmcertmgr
```

- Edit line 1821 and add "-nomac" to the array.

```bash
1817    @out = $self->run(
1818        $self->Openssl, "pkcs12", "-inkey", $keyf,
1819        "-in",          $crtf,    "-name",  $server,
1820        "-export",      "-out",   $pkcsf,   "-passout",
1821        "pass:$kpass",  "-nomac", "2>&1"
1822    );
```
- Do the same for line 1882. 

```bash
1878    @out = $self->run(
1879        $self->Openssl, "pkcs12", "-inkey", $keyf,
1880        "-in",          $crtf,    "-name",  $server,
1881        "-export",      "-out",   $pkcsf,   "-passout",
1882        "pass:$kpass",  "-nomac", "2>&1"
1883    );
```

- Save changes and return to the installation process where prompt ```Select from menu, or press 'a' to apply config (? - help) ``` was displayed, input ```a``` to apply config.

- Then ```Save configuration data to a file? [Yes]``` will be display to which you can press the ```return``` key or enter ```Yes``` and press the ```return``` key.

- When ```Save config in file: [/opt/zimbra/config.83161]``` is displayed, press the ```return``` key.

- When ```The system will be modified - continue? [No]``` is displayed, enter ```Yes``` before pressing the ```return``` key.

Wait until installation finishes and when `````` is displayed, press the ```return``` key.
