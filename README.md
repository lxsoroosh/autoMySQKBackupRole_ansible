# Role Name

Installs and configures the automysqlbackup utility on RHEL/CentOS or Debian/Ubuntu servers.

## Requirements

No special requirements; note that this role requires root access, so either run it in a playbook with a global `become: yes`, or invoke the role in your playbook like:

    - hosts: mysql-database
      roles:
        - role: automysqlbackup_role
          become: yes

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

    automysqlbackup_backup_directory: /var/lib/automysqlbackup
    automysqlbackup_username: "$(grep user ~/.my.cnf | tail -n 1 | cut -d'=' -f2 | awk '{print $1}')"
    automysqlbackup_password: "$(grep password ~/.my.cnf | tail -n 1 | cut -d'=' -f2 | awk '{print $1}')"

By default this role will look for the username and password from ".my.cnf" in the users home directory currently just "root"

## Dependencies

None

## Example Playbook

    - hosts: db-servers
      become: yes
      vars_files:
        - vars/main.yml
      roles:
        - { role: automysqlbackup_role }

_Inside `vars/main.yml`_:

    automysqlbackup_cron:
      minute: 30
      hour: 19
      day: "*"
      month: "*"
      weekday: "*"
    # Setup postbackup - example: rsync backups to another host.
    automysqlbackup_postbackup: true
    automysqlbackup_postbackup_file: /etc/mysql-backup-post
    automysqlbackup_postbackup_content: |
      #!/bin/bash
      /usr/bin/rsync -avz --no-perms --no-owner --no-group -e "ssh -p 22" --delete /var/lib/automysqlbackup/ an.up.add.ress:/mnt/samba/backups/mysql_backups/
    # Above appends the rsync to your existing automysqlbackup. No need to schedule another action.

## License

MIT

## Author Information

This role was created in 2017 by Lxsoroosh
