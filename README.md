# Ansible Role: Borg Server

[![Ansible Galaxy Badge](https://img.shields.io/ansible/role/41595.svg)](https://galaxy.ansible.com/yurihs/borg_server)

- Installs BorgBackup.
- Sets up SSH `authorized_keys` to allow clients to use their repositories (and nothing else).

Made to work with my other role, [yurihs.borg_client](https://github.com/yurihs/ansible-role-borg-client).


## Role variables (default values)

~~~yaml
borg_server_user: borg
borg_server_group: borg
~~~

Who will be able to access the keys and repos.

~~~yaml
borg_server_dir: /srv/borg
~~~

Where the repos will be stored.

~~~yaml
borg_server_clients: []
~~~

A list of clients that may connect. Each client should have a `name` and a `key`, and may have `append_only` set to `true` to restrict it to "append only" mode (if `borg_server_append_only` is `true`, setting this to `false` will have no effect).

~~~yaml
borg_server_append_only: false
~~~

Restrict all clients to "append only" mode.


## Example

~~~yaml
- hosts: backup
  vars:
    borg_server_clients:
      - name: client_a
        key: "{{ lookup('file', playbook_dir + '/keys/id_client_a.pub') }}"
      - name: client_b
        key: "{{ lookup('file', playbook_dir + '/keys/id_client_b.pub') }}"
        append_only: true
  roles:
    - role: yurihs.borg_server
      become: true
~~~

"Client A" may create a repo using the following command:

~~~sh
borg init borg@backup.example.com:my_repo
~~~

Their repo will be created at `/srv/borg/repos/client_a/my_repo`.

"Client B" may do the same, but it will only be able to access the repo in "append only" mode.
