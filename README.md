# Ansible Conventions & Style Guide

> **Note:** Every rule can have exceptions!

## Table of Contents

* [1. General](#1-general)
  * [1.1 Double Quotes](#11-double-quotes)
* [2. Role Structure](#2-role-structure)
  * [2.1 Tasks Structure](#21-tasks-structure)
* [3. Variables](#3-variables)
  * [3.1 Colon Alignment](#31-colon-alignment)
  * [3.2 Variable Naming Prefix](#32-variable-naming-prefix)
  * [3.3 Variables within Variable Values](#33-variables-within-variable-values)
  * [3.4 Main Comment Blocks](#34-main-comment-blocks)
  * [3.5 Variable Declarations](#35-variable-declarations)
  * [3.6 Variable Naming Transparency](#36-variable-naming-transparency)
  * [3.7 Lists and Dictionaries Naming](#37-lists-and-dictionaries-naming)
  * [3.8 Generic Variable Values](#38-generic-variable-values)
  * [3.9 Initializing Lists and Dictionaries](#39-initializing-lists-and-dictionaries)
  * [3.10 Dictionary Element Access Syntax](#310-dictionary-element-access-syntax)
  * [3.11 Quote Style for Dictionary Keys](#311-quote-style-for-dictionary-keys)
  * [3.12 Boolean Variables Naming](#312-boolean-variables-naming)
* [4. Files and Templates](#4-files-and-templates)
  * [4.1 File Extensions](#41-file-extensions)
  * [4.2 Comments in Template Files](#42-comments-in-template-files)
  * [4.3 References and Links in Files](#43-references-and-links-in-files)
* [5. Tasks Style](#5-tasks-style)
  * [5.1 Registered Variable Names](#51-registered-variable-names)
  * [5.2 Special Characters in Task Names](#52-special-characters-in-task-names)
  * [5.3 Import Tasks vs Include Tasks](#53-import-tasks-vs-include-tasks)
  * [5.4 Tags in main.yaml](#54-tags-in-mainyaml)
  * [5.5 Reserved and Special Tags](#55-reserved-and-special-tags)
  * [5.6 Service File Creation](#56-service-file-creation)
  * [5.7 File and Directory Permissions](#57-file-and-directory-permissions)
  * [5.8 Error Handling](#58-error-handling)
  * [5.9 Loops Syntax](#59-loops-syntax)
  * [5.10 Task Parameter Ordering](#510-task-parameter-ordering)
* [6. Documentation](#6-documentation)
  * [6.1 Role README Template](#61-role-readme-template)

---

## 1. General

### 1.1 Double Quotes

Always use double quotes for strings.

**Bad:**

```yaml
'Hello world!'
```

**Good:**

```yaml
"Hello world!"
```

---

## 2. Role Structure

### 2.1 Tasks Structure

The `tasks/` directory must contain the following core files:

* `main.yaml`
* `install.yaml`
* `uninstall.yaml`

---

## 3. Variables

### 3.1 Colon Alignment

Align colons at character column 50 (i.e., variable name + spaces + colon = 50 characters).

**Bad:**

```yaml
role_flush_all: false
```

**Good:**

```yaml
role_flush_all                                   : false
```

### 3.2 Variable Naming Prefix

All variable names must start with the role prefix.

**Bad:**

```yaml
flush_all                                        : false
```

**Good:**

```yaml
role_flush_all                                   : false
```

### 3.3 Variables within Variable Values

Enclose a variable value in double quotes if it references another variable or Jinja2 template.

**Bad:**

```yaml
role_host_port                                  : 8080
role_container_port                             : {{ role_host_port }}
```

**Good:**

```yaml
role_host_port                                  : 8080
role_container_port                             : "{{ role_host_port }}"
```

### 3.4 Main Comment Blocks

Organize default variables using standard block headers: `# common`, `# service`, and `# config`.

* `# common` always goes at the top of the file.
* `# config` pertains to the configuration of the deployed application.

**Bad:**

```yaml
#
# defaults
#

role_flush_all                                   : false
role_service_name                                : role
role_config                                      : configuration
```

**Good:**

```yaml
#
# common
#

role_flush_all                                   : false

#
# service
#

role_service_name                                : role

#
# config
#

role_config                                      : configuration
```

### 3.5 Variable Declarations

All variables used within a role must be explicitly declared in `defaults/main.yaml`.

### 3.6 Variable Naming Transparency

The variable name should clearly reflect its purpose. Append the target configuration key to the role prefix. If needed for clarity, include the parent configuration key (pre-key).

**Bad:**

```yaml
# configuration.yaml.j2
prekey:
  key: "{{ role_value }}"

# defaults/main.yaml
role_value                                       : value
```

**Good:**

```yaml
# configuration.yaml.j2
prekey:
  key: "{{ role_prekey_key }}"

# defaults/main.yaml
role_prekey_key                                  : value
```

### 3.7 Lists and Dictionaries Naming

Name list and dictionary variables using plural forms (appending `s` or `es`).

**Bad:**

```yaml
role_key_list:
  - item1
  - item2
```

**Good:**

```yaml
role_key_values:
  - item1
  - item2
```

### 3.8 Generic Variable Values

Avoid environment-specific or sensitive defaults. Use generic/sanitized default values or values from public documentation.

**Bad:**

```yaml
role_container_image                             : internal-registry.company.ru/role:1.20.2
```

**Good:**

```yaml
role_container_image                             : docker-hub.binary.domain.com/role:1.20.2
```

### 3.9 Initializing Lists and Dictionaries

Place list elements or dictionary keys on new lines with a 2-space indentation level. Avoid inline lists/dicts.

**Bad:**

```yaml
role_key_values                                  : [item1, item2]
role_dict                                        : {key: { inner_key: value } }
```

**Good:**

```yaml
role_key_values:
  - item1
  - item2

role_dict:
  key:
    inner_key                                    : value
```

### 3.10 Dictionary Element Access Syntax

Access dictionary elements exclusively using bracket notation (`dict['key']`) rather than dot notation (`dict.key`).

**Bad:**

```yaml
{{ dict.key }}
```

**Good:**

```yaml
{{ dict['key'] }}
```

### 3.11 Quote Style for Dictionary Keys

Use single quotes inside brackets when accessing dictionary elements.

**Bad:**

```yaml
{{ dict["key"] }}
```

**Good:**

```yaml
{{ dict['key'] }}
```

### 3.12 Boolean Variables Naming

All boolean variable names must end with the `_enable` suffix.

**Bad:**

```yaml
role_something                                   : true
```

**Good:**

```yaml
role_something_enable                            : true
```

---

## 4. Files and Templates

### 4.1 File Extensions

Always use the `.yaml` extension for YAML files (avoid `.yml` or uppercase extensions).

**Bad:**

```
tasks/
├── install.yml
├── main.YML
└── uninstall.YAML
```

**Good:**

```
tasks/
├── install.yaml
├── main.yaml
└── uninstall.yaml
```

### 4.2 Comments in Template Files

Do not leave commented-out block sections inside template files.

### 4.3 References and Links in Files

Include precise references/links to official documentation in configuration template files when applicable. Wrap references in structured comment blocks.

**Bad:**

```nginx
# https://nginx.org/en/docs/ngx_core_module.html
worker_processes {{ nginx_worker_processes }};
error_log {{ nginx_container_log_dir }}/error.log warn;
```

**Good:**

```nginx
#
# https://nginx.org/en/docs/ngx_core_module.html
#
worker_processes {{ nginx_worker_processes }};
error_log {{ nginx_container_log_dir }}/error.log warn;
```

---

## 5. Tasks Style

### 5.1 Registered Variable Names

Name registered variables according to their scope:

* Internal task file scope: `_<module_name>_result`
* Shared across multiple task files: `<module_name>_result`

**Bad:**

```yaml
- name: stop role service
  ansible.builtin.systemd:
    name: "{{ role_service_name }}"
    state: stopped
    enabled: false
  register: result
```

**Good:**

```yaml
- name: stop role service
  ansible.builtin.systemd:
    name: "{{ role_service_name }}"
    state: stopped
    enabled: false
  register: _systemd_result
```

### 5.2 Special Characters in Task Names

Do not use special characters in task names. Parentheses `()`, curly braces `{}`, and double quotes `""` are permitted.

**Bad:**

```yaml
- name: hello world!
```

**Good:**

```yaml
- name: hello world
```

### 5.3 Import Tasks vs Include Tasks

Prefer static task imports over dynamic task includes. Use `ansible.builtin.import_tasks` instead of `ansible.builtin.include_tasks`.

**Bad:**

```yaml
- name: install role
  ansible.builtin.include_tasks: install.yaml
```

**Good:**

```yaml
- name: install role
  ansible.builtin.import_tasks: install.yaml
```

### 5.4 Tags in main.yaml

Task tags in `main.yaml` should match the exact name of the role.

**Bad:**

```yaml
- name: install role
  ansible.builtin.import_tasks: install.yaml
  tags:
    - install_exporter

- name: uninstall role
  ansible.builtin.import_tasks: uninstall.yaml
  tags:
    - exporter
```

**Good:**

```yaml
- name: install role
  ansible.builtin.import_tasks: install.yaml
  tags:
    - role

- name: uninstall role
  ansible.builtin.import_tasks: uninstall.yaml
  tags:
    - role
```

### 5.5 Reserved and Special Tags

Avoid using special Ansible tags like `always` or `never` unless strictly necessary.

### 5.6 Service File Creation

System service configuration files (e.g., systemd units) must be created with `root` ownership.

### 5.7 File and Directory Permissions

Explicitly declare permissions when managing file system objects:

* Directories: `0755`
* Files: `0644`

### 5.8 Error Handling

Do not use `ignore_errors: true`. Handle expected failures explicitly using `failed_when` conditions.

### 5.9 Loops Syntax

Do not use legacy `with_*` loop constructs. Always use the modern `loop` keyword.

### 5.10 Task Parameter Ordering

Maintain a standard and consistent parameter ordering in tasks.

**Good Structure Example:**

```yaml
- name: hello world
  become: true
  run_once: true
  delegate_to: "{{ groups['deploy'][0] }}"
  ansible.builtin.command:
    cmd: echo "Hello world!"
  register: _command_result
  until: _command_result['rc'] == 0
  retries: 3
  delay: 30
  changed_when: "'applied' in _command_result['stdout']"
  failed_when: _command_result['rc'] != 0 and _command_result['rc'] != 2
  when:
    - not ansible_check_mode
    - inventory_hostname == groups['db'][0]
  notify:
    - command result handler
  tags:
    - echo
```

---

## 6. Documentation

### 6.1 Role README Template

All roles should follow a unified README structure.

#### Standard Template:

```markdown
# <role> Role

<Description for the role. The role has next features:>
* <feature_1>
* <feature_2>
* <...>

> **NOTE**
>
> Using the role is your own responsibility. With great power comes great responsibility.
>
> We use Git tags for role versioning, so it is recommended to always define the latest version.
>
> To remove the result of the role, set the variable `<role>_flush_all: true`.

## Important variables

| Variable | Description |
| --- | --- |
| `example_mandatory_var` | **[Mandatory]** Example of mandatory var description |
| `example_notable_var` | Example of notable var description |

## Documentation links

* [Official documentation](<url>)
* [Github](<url>)
* [Container image](<url>)
```
