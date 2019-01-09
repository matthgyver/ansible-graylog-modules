# ansible-graylog-modules
Ansible modules for the Graylog API

### In Progress

* Pipelines
* Pipeline rules
* Indices

### Modules

There are currently 3 modules with the following actions:

* graylog_users
  * create
  * update - updates username, email, full_name, password, roles, permissions, timezone
  * delete
  * list
* graylog_roles
  * create
  * update - updates name, description, permissions, read_only
  * delete
  * list
* graylog_streams
  * create
  * create_rule
  * update - updates name, description, permissions, read_only
  * update_rule - updates field, type, value, inverted, description
  * delete
  * delete_rule
  * list
  * query_streams - query by stream name

### Examples

More examples can be found in `main.yml`.

#### Create User

```
- name: Create Graylog user
  graylog_users:
    action: create
    endpoint: "{{ endpoint }}"
    graylog_user: "{{ graylog_user }}"
    graylog_password: "{{ graylog_password }}"
    username: "{{ username }}"
    full_name: "Ansible User"
    password: "{{ password }}"
    email: "test-email@aol.com"
    roles:
      - "ansible_role"

- name: Get Graylog users
  graylog_users:
    endpoint: "{{ endpoint }}"
    graylog_user: "{{ graylog_user }}"
    graylog_password: "{{ graylog_password }}"
  register: graylog_users

- name: List users
  debug:
    msg: "{{ graylog_users.json }}"
```

#### Create Role

```
- name: Create Graylog role
  graylog_roles:
    action: create
    endpoint: "{{ endpoint }}"
    graylog_user: "admin"
    graylog_password: "{{ graylog_password }}"
    name: "ansible_role"
    description: "Ansible test role"
    permissions:
      - "dashboards:read"
    read_only: "true"

- name: Get Graylog roles
  graylog_roles:
    endpoint: "{{ endpoint }}"
    graylog_user: "{{ graylog_user }}"
    graylog_password: "{{ graylog_password }}"
  register: graylog_roles

- name: List roles
  debug:
    msg: "{{ graylog_roles.json }}"    
```

#### Create Streams and Stream Rules

```
- name: Create stream
  graylog_streams:
    action: create
    endpoint: "{{ endpoint }}"
    graylog_user: "{{ graylog_user }}"
    graylog_password: "{{ graylog_password }}"
    title: "test_stream"
    description: "Windows and IIS logs"
    matching_type: "AND"
    remove_matches_from_default_stream: False
    rules:
      - {"field":"message","type":1,"value":"test_stream rule","inverted": false,"description":"test_stream rule"}

- name: Get Graylog streams
  graylog_streams:
    endpoint: "{{ endpoint }}"
    graylog_user: "{{ graylog_user }}"
    graylog_password: "{{ graylog_password }}"
  register: graylog_streams

- name: Get stream from stream name query
  graylog_streams:
    action: query_streams
    endpoint: "{{ endpoint }}"
    graylog_user: "{{ graylog_user }}"
    graylog_password: "{{ graylog_password }}"
    stream_name: "test_stream"
  register: stream

- name:  List single stream by ID
  graylog_streams:
    endpoint: "{{ endpoint }}"
    graylog_user: "{{ graylog_user }}"
    graylog_password: "{{ graylog_password }}"
    stream_id: "{{ stream.json.id }}"

- name: Create stream rule
  graylog_streams:
    action: create_rule
    endpoint: "{{ endpoint }}"
    graylog_user: "{{ graylog_user }}"
    graylog_password: "{{ graylog_password }}"
    stream_id: "{{ stream.json.id }}"
    description: "Windows Security Logs"
    field: "winlogbeat_log_name"
    type: "1"
    value: "Security"
    inverted: False       
```