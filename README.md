# ansible_new

Ansible repository

Install packages with Ansible: <br>

    ansible all -m apt -a 'package' --become --aks-become-pass
	     ^   ^      ^         	^             	 ^ 
	     |   |      |	        |             	 | 
    [all hosts] [module] [argument] [get sudo privileges] [sudo password] 
   
Edit Playbook for Ansble:
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
    
   	 
Playbook:  
	` ansible-playbook --ask-become-pass install_apache.yml `  
 You don't need the `--become`anymore, because its in the `instal_apache.yml`  
 
 
