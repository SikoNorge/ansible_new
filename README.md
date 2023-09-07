# ansible_new

Ansible repository

Install packages with Ansible:
	<ansible all -m apt -a 'package' --become --aks-become-pass>
		 ^    ^      ^               ^              ^
		 |    |	     |		     |              |
	  all hosts module argument get sudo privileges sudo password
