Vagrant.configure("2") do |config|
  config.vm.box = "trombik/ansible-freebsd-13.0-amd64"
  config.vm.network "forwarded_port", guest: 80, host: 8000
  config.vm.synced_folder "../data", "/vagrant_data", disabled: true
  config.vm.provider "virtualbox" do |vb|
    vb.gui = false
    vb.memory = "8192"
  end
  config.vm.provision "ansible" do |ansible|

    ansible.playbook = "playbook.yml"
    ansible.compatibility_mode = "2.0"
  end
end
