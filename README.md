pair-up
=======

A Vagrantfile and Ansible provisioner for launching a pairing instance on AWS.

Getting Started
---------------

Install [Vagrant][1], [Ansible][5], and then the vagrant-aws plugin via:

    $ vagrant plugin install vagrant-aws

Set up an AWS security group called 'vagrant'.  As a base I recommend opening
up SSH to everyone, but tweak this as needed (I like to open up the ports for
[mosh][4] as well).  Presuming you have the [AWS CLI][2] installed, you can do
this for a given [VPC][3] via:

    $ aws ec2 create-security-group \
        --group-name vagrant \
        --description "vagrant" \
        --vpc-id my-vpc-id 

    $ aws ec2 authorize-security-group-ingress \
        --group-id my-group-id \
        --protocol tcp --port 22 \
        --cidr 0.0.0.0/0

Similary, create an EC2 key pair called 'vagrant' and store the private key in
the file 'vagrant.pem' in the `pair-up` directory.  Then `chmod 400
vagrant.pem` it.  Again you can handle this via the AWS CLI with

    $ aws ec2 create-key-pair --key-name vagrant > vagrant.pem \
        && chmod 400 vagrant.pem

Set the variables in `vars/user.yml` and `Vagrantfile` to match your personal
config.  Create a crypted password for the pair user by something like

    $ echo 'import crypt,getpass; print crypt.crypt(getpass.getpass(), "$6$YOURSALT")' | python -

Launch the instance via:

    $ vagrant up --provider=aws

If you need to re-provision, just use:

    $ vagrant provision

Collaborating
-------------

The provisioner will set up an instance with the following main things in
place:

* your specified dev suite
* your dotfiles of choice (mine as a default)
* a user named 'pair', with the specified crypted password
* a running tmux session called 'pair'

For anyone to join in the fun, all they need to do is add their public key to
`authorized_keys` (password authentication is enabled):

    $ ssh-copy-id pair@my-ec2-instance-public-dns

And then connect via ssh or mosh:

    $ ssh -t pair@my-ec2-instance-public-dns tmux attach -t pair
    $ mosh pair@my-ec2-instance-public-dns -- tmux attach -t pair

When you're finished, halt the instance via `vagrant halt` or terminate it with
`vagrant destroy`.

Enjoy!

[1]: http://www.vagrantup.com  "Vagrant"
[2]: http://docs.aws.amazon.com/cli/latest/userguide/cli-chap-welcome.html "AWS CLI"
[3]: http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_Introduction.html "AWS VPC"
[4]: http://mosh.mit.edu/ "mosh"
[5]: http://www.ansible.com/home "Ansible"
