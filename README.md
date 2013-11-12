windows-vagrant-boxes
=====================

Resources and guide to creating Windows vagrant base boxes for use with the [vagrant-windows](https://github.com/WinRb/vagrant-windows) plugin

Prerequisites
-------------

- [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
- [Vagrant](http://downloads.vagrantup.com/)
- The `bindler` plugin

```
vagrant plugin install bindler
vagrant bindler setup
```

Creating windows vagrant base boxes
-----------------------------------

To prepare a Windows VM for use as a vagrant base box

- Install windows in a VirtualBox VM with the following options
  - user: `vagrant`
  - password: `vagrant`
  - install only important updates
- Install important updates through windows update (restart many times)
- Ensure the following services are set to automatic from the `Services` panel
  - `Base Filtering Engine`
  - `Remote Procedure Call (RPC)`
  - `DCOM Server Process Launcher`
  - `RPC Endpoint Mapper`
  - `Windows Firewall`
- Run a command prompt as administrator and run the following to enable `Windows Remote Management (WS-Management)` with the correct settings

```
winrm quickconfig -q
winrm set winrm/config/winrs @{MaxMemoryPerShellMB="512"}
winrm set winrm/config @{MaxTimeoutms="1800000"}
winrm set winrm/config/service @{AllowUnencrypted="true"}
winrm set winrm/config/service/auth @{Basic="true"}
sc config WinRM start= auto
```

- Go to `User Account Control Settings` and select never notify to turn off UAC
- Go to `Local Security Policy` and under `Security Settings/Account Policy/Password Policy` disable the `Password must meet complexity requirements` option
- Restart the VM to ensure all settings have been applied
- Shut down the VM


To create the vagrant base box from this directory

```
vagrant package --vagrantfile Vagrantfile --base VM_NAME --output BOX_FILE
```

where:

- VM_NAME is the name of the VM in VirtualBox
- BOX_FILE is the name of the output file, eg. `windows.box`

Then to add the box to vagrant

```
vagrant box add BOX_NAME BOX_FILE
```

where

- BOX_NAME could be `windows` if the BOX_FILE was `windows.box`
