# CLI docs #
```bash
ansible-doc shell
```
# Playbooks #
* Written in yaml format

## Anatomy of a Playbook ##
```yml
- name: Name of the play
  hosts: testA #all hosts, single host, host group or locally
  remote_user: root #azureuser, ec2-user
  become: yes    
  tasks:
    - name: Name my task
      dnf: name=epel-release state=present

    - name: install git
      dnf:
        name: git
        state: latest

    - name: install curl
        shell: sudo dnf install curl
```

## With & Handlers ##
```yml
- name: Example of how loops and handler
  hosts: testA #all hosts, single host, host group or locally
  remote_user: root #azureuser, ec2-user
  become: yes
  vars:
    db_port: 5310
  tasks:
    - name: Installing generic packages
      dnf:
        name: "{{item}}"
        state: present
      with_items:
        - git
        - htop
        - chrony
        - curl

    - name: Installing generic packages
      dnf:
        name: firewalld
        state: present
      register: generic_pkg_install_log
      notify: restart firewalld

  handlers:
    - name: restart firewalld
      service:
        name: firewalld
        state: restarted
```
## Template ##
```yml
- name: Example of template
  hosts: testA #all hosts, single host, host group or locally
  remote_user: root #azureuser, ec2-user
  become: yes
  vars:
    db_port: 5310
  tasks:
    - name: Template a file to /etc/file.conf
      template:
        src: foo.j2
        dest: /root/file.conf
        owner: bin
        group: wheel
        mode: '0644'
```