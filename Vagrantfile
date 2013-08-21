Vagrant.configure("2") do |config|
  config.windows.halt_timeout = 25
  config.winrm.username = "vagrant"
  config.winrm.password = "vagrant"
  config.vm.guest = :windows
  config.vm.network :forwarded_port, guest: 5985, host: 5985
end
