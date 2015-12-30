# -*- mode: ruby -*-
# vi: set ft=ruby :

$PROVISION_SCRIPT = <<SCRIPT
  echo 'StrictHostKeyChecking no' > ~/.ssh/config
  echo 'UserKnownHostsFile=/dev/null no' >> ~/.ssh/config
  apt-add-repository -y ppa:ansible/ansible
  apt-get update -q && apt-get install -y software-properties-common git ansible

  cd /home/vagrant/golang/provisioning/
  echo 'Install roles:'
  ansible-galaxy install -r requirements.yml --force
  echo 'Run asnible playbook locally:'
  PYTHONUNBUFFERED=1 ANSIBLE_FORCE_COLOR=true ansible-playbook \
  dev.yml \
  -i inventory \
  -u vagrant \
  -c local
SCRIPT

Vagrant.configure(2) do |config|
  # Development VM
  config.vm.provider 'virtualbox' do |v|
    v.memory = 512
    v.cpus = 1
  end

  config.vm.define 'dev', primary: true do |dev|
    dev.vm.box = 'ubuntu/trusty64'
    dev.vm.hostname = 'golang'
    dev.ssh.forward_agent = true
    dev.vm.synced_folder './', '/vagrant', disabled: true
    dev.vm.synced_folder './', '/home/vagrant/golang', owner: 'vagrant', group: 'vagrant'
    dev.vm.post_up_message = 'Ready to development.'

    dev.vm.provision 'shell', keep_color: true, inline: $PROVISION_SCRIPT
  end

  # Use vagrant-cachier to cache apt-get, gems and other stuff across machines
  config.cache.scope = :box if Vagrant.has_plugin?('vagrant-cachier')
end
