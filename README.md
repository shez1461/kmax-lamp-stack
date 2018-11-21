README - Install LAMP-stack for KMAX using Ansible
==================================================

----------------------------
Setup Control-Machine (Host)
----------------------------
(1). Install Ansible on your host/master machine (Separate Server) -
$ sudo apt update && sudo apt upgrade -y
$ sudo apt install ansible -y


(2). Copy & replace the '/etc/ansible/ansible.cfg' with the 'ansible.cfg.tar.gz' provided in the 'cloud.kaleao.com' link below:
$ wget --no-check-certificate https://cloud.kaleao.com/index.php/s/F2uN3qSGMAh09FQ/download

Assuming your working from current directory -
$ sudo mv -vf download ansible.cfg.tar.gz
$ sudo tar -xvf ansible.cfg.tar.gz
$ sudo cp -vf ansible.cfg /etc/ansible/


(3). Set the Ansible 'Host Key Checking' to 'False' in '/etc/ansible/ansible.cfg'
$ export ANSIBLE_HOST_KEY_CHECKING=False


(4). Define/Change the production ip's in 'hosts' file & assign them with groups if needed, but not neccessary.
[kmax-servers]
10.10.x.x
10.10.x.x

[wordpress-servers]
10.10.x.x
10.10.x.x


(5). Type this command on Host/Master to setup passwordless-ssh [neccessary*]
$ ssh-keygen -t rsa -b 4096

if you already have this generated with your previous setup, just add the keys using ssh-copy-id by -
$ ssh-copy-id -i kaleao@10.10.x.x
$ ssh-copy-id -i kaleao@10.10.x.x

If you haven't, then complete both steps above.

Enter password for all the KMAX-servers, then check if one of the server works by -
$ ssh kaleao@10.10.x.x

if you can login without the password prompt, then it works!


------------------
Setup Managed Node
------------------
(a). Install python dependencies on Managed nodes from Host Machine using Ansible's raw module by typing the command below on host -
$ ansible kmax-servers -m raw -a "apt install -y python-pip" --ask-become-pass
SUDO password: kaleao

wait for the output, this could take few minutes to install required python modules remotely on every KMAX-server defined.


(b). Check if Ansible works, by using raw ansible command -
$ ansible kmax-servers -m ping --ask-become-pass
SUDO password: kaleao

Output:
10.10.x.x | SUCCESS => {
    "changed": false, 
    "ping": "pong"
}

check for the results coming back with Success, otherwise re-check the '/etc/ansible/ansible.cfg' for any discrepancies.


(c). Type the command below to run the playbook and look for any errors or warnings on console:
$ ansible-playbook -i hosts kmax-lamp.yml

Directory structure:
|
├── kmax-lamp-stack/
│   ├── .
│   ├── ..
│   ├── hosts
│   ├── kmax-lamp.yml
│   ├── README.md
│   └── install-ssh-keys.sh
│
# kmax-lamp-stack
