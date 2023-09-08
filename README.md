# ansible_new    


### Inventory
Go into your Ansible folder and add a new document `nano inventory`  
Your `Inventory` contains all your Server IP's 
For example:

	[192.0.0.0]
 	[192.0.0.1]
  	[192.0.0.2]
  
Ansible repository

Install packages with Ansible: <br>

    ansible all -m apt -a 'package' --become --aks-become-pass
	     ^   ^      ^         	^             	 ^ 
	     |   |      |	        |             	 | 
    [all hosts] [module] [argument] [get sudo privileges] [sudo password] 
   
Edit Playbook for Ansible:
    `nano [filename].yml`  
 	!IMPORTANT!: Careful with spacing, align it correct  
    
      --- 
	- hosts: all 
 	  become: true 
 	  tasks: 
 	  - name: update repository index 
   	    apt: 
   	       update_cache: yes 
  	  - name: install apache2 package 
  	     apt: 
   	       name: apache2
	  - name: install php packages
   	     apt:
	       name: libapache2-mod-php

For a better reading you can combine both package installings:  

      --- 
	- hosts: all 
 	  become: true 
 	  tasks: 
 	  - name: update repository index 
   	    apt: 
   	       update_cache: yes 
  	  - name: install apache2 and php package 
  	     apt: 
   	       name: apache2
	          - apache2
	   	  - libapache2-mod-php
       	       update_cache: yes
    
### Playbook:  
	` ansible-playbook --ask-become-pass install_apache.yml `  
 You don't need the `--become`anymore, because its in the `instal_apache.yml`  

To install the latest packages add after `name:`    
`state: latest`    

 Ansible installation on Ubuntu and Debian by using `apt`  
 	Installation fails when you will use other OS, to prevent that use  
  `when: ansible_destribution == "Ubuntu" `  
  just for Ubuntu and  
  `when: ansible_destribution in ["Ubuntu", "Debian"]`  
  for both.  
  For other OS then Ubuntu and Debian it wont connect in Webpage use this Video for fixes:  
  [Ansible for other OS then Ubuntu and Debian](https://youtu.be/BF7vIk9no14?list=PLT98CRl2KxKEUHie1m24-wkyHpEsa4Y70&t=955)  

  <details>
<summary>Updated version of Playbook V1.1</summary>
  
    
	---
	
	- hosts: all
 	 become: true
	  tasks:
	
	  - name: install apache2 and php package for Ubuntu
 	   apt:
  	    name: 
   	     - apache2
	        - libapache2-mod-php
   	   state: latest
   	   update_cache: yes
   	   when: ansible_distribution in ["Ubuntu", "Debian"]
    
</details>    
  
## Playbook (Any OS):  

  Its usefull when using more then one OS on your server.
  
    ---
	
	- hosts: all
 	 become: true
	  tasks:
	
 	 - name: install apache and php
 	   package:
     	     name:
	       - "{{ apache_package }}"
	       - "{{ php_package }}"
	     state: latest
  	     update_cache: yes
    
 `package:`referes to all modules (apt, yum, etc.).  
 Go to your `inventory`und edit your IP's  
 `[your_IP] apache_package=[name of your OS apache] php_package=[name of your OS apache-php] ` 
  
 ## Working with diffrent servers

When you work on different server types you should group them in your [Inventory](#Inventory).  
