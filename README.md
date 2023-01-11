# ansible_role_signage_player
This is an Ansible role which can be used to set up a digital signage player, kiosk system, or anything else which requires:

1. A fullscreen web browser, starting on system boot with no login
2. Which loads a specified page at a specified time
3. And doesn't stop doing so until you tell it to.

# Documentation!
Documentation (and a more in-depth quickstart) for this role lives in `docs/` as well as at https://ansible-role-signage-player.readthedocs.io. 

# Basic Usage
#. Clone this repository into your `roles/` directory
#. You will need a `playbook.yml`:

    ```yaml
    - hosts: signage_players
    roles:
    - role: 'signage_player'
    ```

#. And a `hosts.yml`:

    ```yaml
    its_sign_test:
    vars:
        ansible_user: administrator
        signage_schedule:
        - { days: "Mon,Tue,Wed,Thu,Fri,Sat,Sun", start_time: "08:00", end_time: "17:00", url: "https://example.com" }
    hosts:
        change-me-to-your-signage-player-address
    ```

#. Run your playbook with `ansible-playbook -i hosts.yml playbook.yml`.
