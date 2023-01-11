==========
Quickstart
==========

This guide should serve to get you up and running with a simple setup.

-------------
Prerequisites
-------------

You will need: 

#. Somewhere to run Ansible (if you have the ability, I suggest a virtual machine so you can just ssh in and manage it from anywhere).
#. Some other way to set the system's timezone. Most linux distros should be fine if you set this at install time, but Ubuntu Server Minimal will reset the timezone every time tzdata is updated. This role used to try to set the timezone, but it was a pitiful game of whack-a-mole and was removed.
#. At least one computer to be used as a signage player.

    * I recommend Ubuntu Server as the operating system on the player. That is the only Linux distribution I've tested. Theoretically, anything based on .deb and systemd should work, but I haven't tried it.

----------------------------
Prepare the signage player
----------------------------
This is fairly standard Ansible setup.

#. Install Ubuntu Server (make sure to enable the OpenSSH server!)
#. Set your administrative user up for SSH key authentication
    a. On your computer where you run Ansible, run :code:`ssh-keygen` to create an SSH key.
    b. Use :code:`ssh-copy-id <your-admin-username>@<your-signage-player-address>` to automatically copy your new SSH key to the signage player.
    c. Try signing in using :code:`ssh <your-admin-username>@<your-signage-player-address>`. If it doesn't ask for a password, you're done!

    .. warning:: This is a very simple overview of the process. If you're not familiar with key-based SSH authentication, it would be a good idea to read this guide: https://linuxhandbook.com/add-ssh-public-key-to-server/

#. Set your administrative user up for passwordless sudo
    a. Create a file :code:`/etc/sudoers.d/<your-admin-username>` with these contents::

        <your-admin-username> ALL=(ALL:ALL) NOPASSWD:ALL

    .. tip:: Use :code:`visudo -f /etc/sudoers.d/<your-admin-username>` to create this file. This will catch your mistakes and keep you from locking yourself out of your system. You will also need to install a text editor. I'm partial to :code:`nano`, myself.

------------------------
Install the Ansible role
------------------------
This role is not (yet) published on ansible-galaxy. Currently, I have deployed it by cloning the git repository and creating my own :code:`hosts.yml` and :code:`playbook.yml` files. Add those to .gitignore, and then updating the role is as easy as :code:`git pull`. 

#. Create a directory for your Ansible configuration and :code:`cd` into it
    .. tip:: You should place this directory under version control :)
#. Create a :code:`roles` directory and :code:`cd` into that as well
#. Clone the repository::

    git clone https://github.com/sheepman4267/ansible_role_signage_player.git

---------------------------
Configure Ansible
---------------------------
#. :code:`cd` back to your Ansible configuration directory
#. You will need a :code:`playbook.yml`::

     - hosts: signage_players
       roles:
       - role: 'signage_player'

#. And a :code:`hosts.yml`::

    its_sign_test:
    vars:
        ansible_user: administrator
        signage_schedule:
        - { days: "Mon,Tue,Wed,Thu,Fri,Sat,Sun", start_time: "08:00", end_time: "17:00", url: "https://example.com" }
    hosts:
        change-me-to-your-signage-player-address

#. At this point, your directory structure should look like this::

    whatever-you-called-your-ansible-directory
    |-> roles/
    | |-> ansible_role_signage_player
    |-> playbook.yml
    |-> hosts.yml
    
#. Change "ansible_user" to the administrative username you chose when setting up your operating system
#. Configure signage_schedule to display a page of your choice (example.com is traditional)
    .. tip:: The example schedule will display whatever you set "url" to from 8:00 AM to 5:00 PM, every day of the week. For more information on configuring the schedule, see :ref:`signage_schedule`.
#. Add your signage player's hostname or IP address to the "hosts" list
#. Copy playbook.yml.example to playbook.yml 
    .. tip:: Unless you changed the ansible group name (default: :code:`signage_players`) in hosts.yml, the example playbook should "just work" without editing anything.
#. Run the playbook::
    
    ansible-playbook -i hosts.yml playbook.yml

Wait a few minutes. The signage player should eventually display xeyes, then chrome, dismiss the chrome first-run popup, and then display whatever you set in the schedule. Once you have that much working, go ahead to :doc:`advanced-setups`.
