# Introduction

After creating the Vagrant machines, the next step is to provision them. For example, installing PostgreSQL 
onto the database machine, or nginx into your web machine. 

## File Provisioning

File provisioning will allow you to copy a file to the virtual machine, this is often the first step in testing
a provisioning shell script. 


### Create a deploy script

```
echo "apt-get install -y nginx" >> deploy_script.sh
chmod +x deploy_script.sh
```

### Add "file" provisioner

```
Vagrant.configure("2") do |config|

   ...

   config.vm.provision "file", source: "deploy_script.sh", destination: "deploy_script.sh"

end
```

### Create the vagrant machine and run the script

```
vagrant up
vagrant ssh
./deploy_script.sh
```

With these commands you can run the deploy script. This is of great use to test the deploy script before using the shell 
provisioner. Once it is done, you can switch it. 

 
## Shell Provisioning

Shell scripts are the simplest way to provision a vagrant machine. You can install using inline scripting, stored in the
Vagrang file, but the reality is, it is much better to have it as a shell script file. This will allow you to create 
the virtual machine, then ssh in, copy in your ssh script, run it manually until you know it works, then use it in 
the provisioning script. 


```
Vagrant.configure("2") do |config|

   ...
   config.vm.provision "shell", path: "deploy_script.sh"

end
```

## Ansible Provisioning

Ansible, like Puppet, is a software that assists with infrastruction management. Using ansible allows you to 
control your infrastructure in *code* which allows changes to infrastructure to be managed at an enterprise 
level. 

First, [install ansible for your platform](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#installing-the-control-node). 

Your host machine, the one running the provisioning operates as the ansible control machine. 

```
Vagrant.configure("2") do |config|

   ...

   config.vm.provision "ansible" do |ansible|
      ansible.become = true
      ansible.become_user = "root"
      ansible.playbook = "playbook.yml"
      ansible.extra_vars = { ansible_python_interpreter:"/usr/bin/python3" }     
   end

end
```

The best part with this method is that it will work with multiple infrastructures, and it you can run it on that infrastructure just by changing the 
hosts. 



