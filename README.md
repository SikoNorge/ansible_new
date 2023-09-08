# ansible_new    

## Git Commands to commit your work

`git status`  
`git add .`  
`git commit -m "Description"`  
`git push origin main`  



### Inventory
Go into your Ansible folder and add a new document `nano inventory`  
Your `Inventory` contains all your Server IP's 
For example:

	192.0.0.0
 	192.0.0.1
  	192.0.0.2
  
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
  
	[web_servers]
 	192.0.0.0
 	[db_servers]
  	192.0.0.1
  	[file_servers]
   	192.0.0.2
  
Copy your `install_apache.yml`to a new oen `site.yml`  
`cp install_apache.yml site.yml`  
Now you can work on certain server with certain things in your [Playbook](#Playbook) via the comment `-host`  
<details>
<summary>The new code can look like following:</summary>
	
	---
	
	- hosts: all
 	  become: true
	  tasks:

   	  -name install updates (Ubunut, Debian)
      	    apt:
	      upgrade: dist
       	      update_cache: yes
	    when: ansible_distribution in ["Ubuntu", "Debian"]

	- hosts: web_servers
 	  become: true
    	  tasks:
	  - name: install apache2 and php package for Ubuntu
 	    apt:
  	      name: 
   	        - apache2
	        - libapache2-mod-php
   	   state: latest
   	   when: ansible_distribution in ["Ubuntu", "Debian"]
       
</details>

### Tags

You can add tags to you Playbook to sort your servers  
Add `tags: [your tags]` right under `name`  
Now run `ansible-playbook --tags [your tag] --ask-become-pass install_apache.yml`  
It will only show the processes with the named tag  
For more tags you can use `--tags "tag1,tag2"`  

## Copying files to the Server  

Create a file in our example it will be an `.html`file.  
for that we create a new folder in ansible_new  
`mkdir files`, then go into the file with create a file in my case it is `default_site.html`
Paste a common html code in it  
<details>
	<summary>Click</summary>
	
	<html>
		<head>
      			<title>Welcome to your_domain!</title>
  		</head>
   		<body>
       			<h1>Success!  The Silko virtual host is working!</h1>
  		</body>
	</html>
 
</details>
  
To copy files to your server you have to go into your Playbook.
Paste the following code in:

	- name: copy default html file for site
 	  tags: apache,apache2,debian
    	  copy:
            src: default_site.html
	    dest: /var/www/html/index.html
     	    owner: root
	    group: root
	    mode: 0644

> default_site.html and index.html doesnt need matching names

<details>
<summary>Updated version of Playbook V1.2</summary>
	
	---

	- hosts: all
	  become: true
	  tasks:
	
 	 - name: install updates
 	   tags: always
 	   apt:
 	     upgrade: dist
 	     update_cache: yes
  	  when: ansible_distribution in ["Ubuntu", "Debian"]
	
	
 	 - name: install apache2 and php package for Ubuntu
 	   tags: apache,apache2,debian
 	   apt:
 	     name:
  	      - apache2
  	      - libapache2-mod-php
  	    state: latest
  	  when: ansible_distribution in ["Ubuntu", "Debian"]
	
 	 - name: copy default html file for site
 	   tags: apache,apache2,debian
 	   copy:
  	    src: default_site.html
   	   dest: /var/www/Silko/index.html
  	    owner: root
    	  group: root
    	  mode: 0644

</details>
  
### Changing lines in files

Just watch this youtube video:
[Getting started with Ansible 12 - Managing Services](https://youtu.be/soeBHGAMkoQ?list=PLT98CRl2KxKEUHie1m24-wkyHpEsa4Y70)  


## Adding Users

Go into your [Playbook](#Playbook) and add the following command:  

	- hosts: all
 	  become: true
    	  tasks:
       	  - name: create user (user)
	    tags: always
     	    user:
	      name: siko
       	      groups: root
	      
	  - name: add ssh key for siko
	    tags: always
    	    authorized_key:
     	      user: siko
      	      key: "(your ssh key)"

   	  - name: add sudoers file for siko
      	    tags: always
	    copy:
     	      src: sudoer_siko
	      dest: /etc/sudoers.d/siko
       	      owner: root
	      group: root

!DONT MESS UP IT WILL BE VERY PROBLAMATIC TO RESET THIS ON SERVER!
---
In case you forgot how to copy the ssh key click here: [Set up SSH-Key](https://github.com/ProjectDean/ansible_test#set-up-ssh-keys)  
  
Now go into your files folder `cd files` and add a new file `nano sudoer_siko`  

Write in it: `siko ALL=(ALL) NOPASSWD: ALL`  
Add in `ansible.cfg`, `remote_user = siko`  

After setting this up you don't have to write  
`ansible-playbook --ask-become-pass install_apache.yml`  
you can shorten it by  
`ansible-playbook install_apache.yml`

## Bootstrap the User

Now we copy the `install_apache.yml` to a `bootstrap.yml`  
`cp install_apache.yml bootstrap.yml`  
You can delete in the `install_apache.yml` it was only for show purpose  

	- name: create user (user)
	    tags: always
     	    user:
	      name: siko
       	      groups: root

and

	- name: add sudoers file for siko
   	  tags: always
   	  copy:
    	    src: sudoer_siko
     	    dest: /etc/sudoers.d/siko
    	    owner: root
     	    group: root

Now everytime you set up new servers you will automaticly add the user `siko` and give  him the authority to connect to the server without a password  
you only have to wirte the sudo password once. The user can also now use `sudo` arguments.  
