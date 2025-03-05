# team-b-dotnet-project

key for ansible -pub:

ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQC8QFedeTf4p75oNpg6SLFaLY/WmaeXOuJcvPfWjjaRyD6dZ0Wm6qwXKgb6C+an9Dxf/VqErvZ4hm1ToYc72/IE8z8c82kkngbGOZmgHEAtlXxME2z1+7igpPU5XCRonqYAhWrFcqnLcxXIWRMOgy2FnJz4S/tHv0c0hi031/oDugXXRbMnvgQwJxhTG6myBocfwfzC/OZ9AXJeMN3Y0Uc85VD3JXe6Y3XrwS+uTikI1Hck3R5mktzxdCaeZMBN5GamwoYpM+2q5stmnLZZzc7Wh53gurh/9N1eu7zLxwOjs4FBvHTICTwDfazuZErdXAY9oTxOHa7w/7Z+2ctsU65LqTt6EbHG5YsneP4hXreop5OGCbY7pR3wjvC+QXXETWZuG5qgNClPmBDr2AIu173QyovXF0ifchV1+66aqxlwy2YFqxWemIJikKRJMyVN6e62I7VsY7dulwHDM5U+WsvEXZHK6zNNhCIl8n1gcW1wAymdjWkgmU2rtfXvHgWL6tk= sarthak@sarthak-VirtualBox

---
- name: Install Wazuh agent on all clients
  hosts: wazuh_clients
  become: yes
  tasks:
    - name: Add Wazuh repository GPG key
      apt_key:
        url: https://packages.wazuh.com/key/GPG-KEY-WAZUH
        state: present

    - name: Add Wazuh repository
      apt_repository:
        repo: deb https://packages.wazuh.com/4.x/apt/ stable main
        state: present

    - name: Update apt cache
      apt:
        update_cache: yes

    - name: Install Wazuh agent
      apt:
        name: wazuh-agent
        state: present

    - name: Configure Wazuh agent to connect to Wazuh manager
      template:
        src: wazuh_agent.conf.j2
        dest: /var/ossec/etc/ossec.conf

    - name: Enable and start Wazuh agent service
      systemd:
        name: wazuh-agent
        enabled: yes
        state: started
