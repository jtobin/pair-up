# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = '2'

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box     = 'tmatilai/debian-7'
  config.vm.box_url = 'https://vagrantcloud.com/tmatilai/debian-7'

  config.vm.provider :aws do |aws, override|
    aws.ami               = 'ami-edc75cd7'   # change me if you'd like
    aws.region            = 'ap-southeast-2' # ditto
    aws.instance_type     = 't1.micro'       # ditto
    aws.access_key_id     = FIXME
    aws.secret_access_key = FIXME
    aws.keypair_name      = 'vagrant'
    aws.security_groups   = [ 'vagrant' ]

    override.ssh.username         = 'admin'
    override.ssh.private_key_path = 'vagrant.pem'
  end

  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "provision.yml"
  end
 
end

