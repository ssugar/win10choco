# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

$prereboot = <<SCRIPT
tzutil /s "SE Asia Standard Time"
(iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1')))>$null 2>&1
#choco install googlechrome -y
#choco install flashplayerplugin -y
#choco install javaruntime -y
#choco install office365proplus -y
choco install pswindowsupdate -y
powercfg -change -standby-timeout-ac 0
powercfg -change -disk-timeout-ac 0
powercfg -change -hibernate-timeout-ac 0
import-module PSWindowsUpdate
Add-WuServiceManager 7971f918-a847-4430-9279-4a52d1efe18d -confirm:$false
Get-WUList -MicrosoftUpdate | measure | select Count | fl
Get-WUInstall -AcceptAll -IgnoreReboot -MicrosoftUpdate
Get-WURebootStatus -Silent
SCRIPT

$postreboot = <<SCRIPT
import-module PSWindowsUpdate
Get-WUList -MicrosoftUpdate | measure | select Count | fl
Get-WURebootStatus -Silent
SCRIPT


Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
   config.vagrant.host = :windows
   config.windows.halt_timeout = 60   
 
   config.vm.box = "win10techprev"
   config.vm.define :win10choco do |t|
   end

   config.vm.provider :hyperv do |v|
	 v.vmname = "win10techprev"
	 v.memory = 512
	 v.cpus = 1
   end

   config.vm.communicator = "winrm"
   config.vm.network :forwarded_port, guest: 3389, host: 3391
   config.vm.network :forwarded_port, guest: 5985, host: 5987, id: "winrm", auto_correct: true

   config.winrm.username = "vagrant"
   config.winrm.password = "vagrant"

   config.vm.synced_folder ".", "/vagrant", type: "smb", disabled: false, smb_host: "10.10.10.66", create: true

   #run the pre-reboot script
   config.vm.provision "shell", inline: $prereboot

   #reboot
   #config.vm.provision :reload
   
   #run the script above
   #config.vm.provision "shell", inline: $postreboot

end
