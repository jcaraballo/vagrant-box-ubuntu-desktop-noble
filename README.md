# vagrant-box-ubuntu-desktop-noble
Resources to create a Vagrant Base Box with an Ubuntu Desktop 24.04.1 LTS (Noble Numbat)

## Requires

* [Vagrant](https://www.vagrantup.com/downloads.html)
```
curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
sudo apt-get update && sudo apt-get install vagrant
```

* [VirtualBox](https://www.virtualbox.org/wiki/Linux_Downloads)
```
echo deb '[arch=amd64]' https://download.virtualbox.org/virtualbox/debian $( lsb_release -cs ) contrib | sudo tee -a /etc/apt/sources.list
wget -q https://www.virtualbox.org/download/oracle_vbox_2016.asc -O- | sudo apt-key add -
wget -q https://www.virtualbox.org/download/oracle_vbox.asc -O- | sudo apt-key add -
sudo apt-get update
sudo apt-get install -y virtualbox-7.0 dkms
```

## Build
* _Host_: Download [Ubuntu 24.04.1 LTS (64 bit)](https://www.ubuntu.com/download/desktop)
* _Host_: Create a new Virtual Box VM for the installation
  1. Open VM Virtual Box Manager
  2. Click New
  3. Virtual machine general details:
     0. Select Expert Mode
     1. Name: Ubuntu Desktop 24.04.1 LTS (Noble Numbat)
     2. ISO Image: (select the `.iso` from above)
     3. Leave _Type: Linux_ and _Version: Ubuntu (64-bit)_
     4. Tick _Skip Unattended Installation_
  5. Hardware
     1. Memory size: 8192
     2. Processors: 4 CPUs
     3. Leave _Enable EFI_ unticked
  6. Virtual Hard disk:
     1. Create a virtual hard disk now
     2. 80 Gb
     3. Leave _VDI_ and _Pre-allocate Full Size_ unticked
  7. Finish
  6. Open the settings of the VM
     1. General > Advanced > Shared Clipboard: Bidirectional
     2. Display > Increase video memory to 128 MB
     3. User inteface > Untick _Show in Full-screen/Seamless_
     4. OK
  9. Click on Displan > Video Memory to increase to 256 MB
  10. Start
* _Guest_: Install Ubuntu in a VirtualBox VM, including the guest additions:
  1. Select _Try or Install Ubuntu_
  2. Click on weird solid white screen
  3. Choose your language: English
  4. Accesibility in Ubuntu: Next
  5. Keyboard layout English (UK)/English (UK)
  6. Connect to the Internet: leave _Use wired connection_, Next
  7. What do you want to do...? leave _Install Ubuntu_, Next
  8. How would you like to...? Interactive installation
  9. What apps would you like to install...? Just the essentials
  10. Install recommended propietary software? Install Third Party Software..., Next
  11. How do you want to install? Erase Disk and Install Ubuntu, Advanced features, Use LVM (no encryption), Next
  12. Crate your account: Set name, computer's name, username and password to _vagrant_. Require password to log in, Next
  13. Time zone Europe/London
  14. Install
  15. Once the installation is complete, reboot
  16. Install updates, reboot
  17. Install guest additions dependencies
      ```
      sudo apt install gcc make perl
      ```
  18. NOT DOING THIS: Install guest additions (Devices > Insert Guest Additions CD image then run `autorun.sh`), eject the additions media and reboot

* _Guest_: Run some scripts to make the image Vagrant-friendly
  1. Run [prepare-base-box-root.bash](prepare-base-box-root.bash) as root (requires password for sudo)
  ```
  wget https://raw.githubusercontent.com/jcaraballo/vagrant-box-ubuntu-desktop-noble/master/prepare-base-box-root.bash -O - | sudo bash
  ```
  2. Run [prepare-base-box-vagrant.bash](prepare-base-box-vagrant.bash) as the vagrant user
  ```
  wget https://raw.githubusercontent.com/jcaraballo/vagrant-box-ubuntu-desktop-noble/master/prepare-base-box-vagrant.bash -O - | bash
  ```
  3. Send the shutdown signal and turn off the VM

* _Host_:
  (Replace `VBOX_PATH` by the path to the Virtual Box VM `.vbox` file, usually inside `~/VirtualBox\ VMs/`)
  ```
  vagrant package --base VBOX_PATH --output package.box
  ```

* _Host_:
  (Add `-f` if you've already added the box to the vagrant list and want to
  replace it)
  ```
  vagrant box add --name ubuntu-desktop-noble-24.04 package.box
  ```

* (Optional) If you'd like to upload it to vagrantup
  1. Create a new account if you don't already have one and log in
  2. New Vagrant Box
  3. Add name and description. I use
    1. Name: `jcaraballo` / `ubuntu-desktop-noble`
    2. Description:
       ```
       Vagrant/VirtualBox Base Box with an Ubuntu Desktop 24.04.1 LTS (Noble Numbat)
       * Project sources: REPO_LINK
       * For this version: VERSION_LINK
       ```
       (Replace `REPO_LINK` by a link to the repo and `VERSION_LINK` by a permanent
       link to the specific version, e.g. in Github select the commit and click
       on _browes files_)
  4. Version, e.g. 0.0.1
  5. Add a provider
     1. virtualbox
     2. Upload to Vagrant Cloud
  6. Choose file: select file `package.box` generated earlier with `vagrant package`
  7. Back to the dashboard, select the created box
  8. On the appropriate version: Release > Release version


## Binary
[Vagrant Cloud: jcaraballo/ubuntu-desktop-noble](https://app.vagrantup.com/jcaraballo/boxes/ubuntu-desktop-noble)

## Usage example
See [vagrant-ubuntu-24.04-dev](https://github.com/jcaraballo/vagrant-ubuntu-24.04-dev)
