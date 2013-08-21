windows-vagrant-boxes
=====================



Prerequisites
-------------

- [VirtualBox 4.2.16](https://www.virtualbox.org/wiki/Downloads)
- [Vagrant 1.2.7](http://downloads.vagrantup.com/)
- The `bindler` plugin

```
$ vagrant plugin install bindler
$ vagrant bindler setup
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
$ winrm quickconfig -q
$ winrm set winrm/config/winrs @{MaxMemoryPerShellMB="512"}
$ winrm set winrm/config @{MaxTimeoutms="1800000"}
$ winrm set winrm/config/service @{AllowUnencrypted="true"}
$ winrm set winrm/config/service/auth @{Basic="true"}
$ sc config WinRM start= auto
```

- Go to `User Account Control Settings` and select never notify to turn off UAC
- Go to `Local Security Policy` and under `Security Settings/Account Policy/Password Policy` disable the `Password must meet complexity requirements` option
- Restart the VM to ensure all settings have been applied
- Shut down the VM


To create the vagrant base box from this directory run

```
$ vagrant package --base VM_NAME --output BOX_NAME --vagrantfile Vagrantfile
```

where:

- VM_NAME is the name of the VM in VirtualBox
- BOX_NAME is the name of the output file, eg. `windows.box`

Building the `vagrant-windows` plugin for use with Vagrant 1.2
--------------------------------------------------------------

Currently the Vagrant 1.2 compatible `vagrant-windows` plugin has not been released and is under development (last updated: 21/08/2013). To build the plugin I have forked the project and added vagrant support to make things simpler (ie. not having to maintain a ruby environment).

- Clone the project fork and switch to the `vagrant-1.2` branch

```
$ git clone https://github.com/pghalliday/vagrant-windows.git
$ cd vagrant-windows
$ git checkout vagrant-1.2
```

- Install the required plugins using the `bindler` plugin

```
$ vagrant plugin bundle
```

- Start the vagrant VM and connect to it

```
$ vagrant up
$ vagrant ssh
```

- (Optional) override the git protocol which if it is blocked by your firewall (this is required for the `bundle install` step)

```
vagrant@vagrant-windows:~$ git config --global url."https://".insteadOf git://
```

- Build the plugin and exit the vagrant VM

```
vagrant@vagrant-windows:~$ cd /vagrant/
vagrant@vagrant-windows:/vagrant$ bundle install
vagrant@vagrant-windows:/vagrant$ bundle exec rake
```

- exit the vagrant VM and install the `vagrant-windows` plugin in the host

```
vagrant@vagrant-windows:/vagrant$ exit
$ vagrant plugin install pkg/vagrant-windows-1.2.0.gem
```




