---
layout: post
title: Ansible Advanced lineinfile
category: note
---

[Ansible is great](/note/ansible/), it's really simple to setup and easy to use. There are still some hidden tricks that can make your workflow more efficient.

I was setting up new machines with PHP-FPM support. I'd like to change several FPM parameters using Ansible, you can copy (transfer) the whole configurations from your Ansible task to remote machines via `synchronize` or `template`, it's fast but you can't handle config changes from program updates. So my idea is dynamically replace the parameters I want to change via `lineinfile`:

After googling around, I found [this answer by Ben Whaley](https://stackoverflow.com/a/24345892/412385) fit the bill:

```yml
- name: Set some kernel parameters
  lineinfile:
    dest: /etc/sysctl.conf
    regexp: "{% raw %}{{ item.regexp }}{% endraw %}"
    line: "{% raw %}{{ item.line }}{% endraw %}"
  with_items:
    - { regexp: '^kernel.shmall', line: 'kernel.shmall = 2097152' }
    - { regexp: '^kernel.shmmax', line: 'kernel.shmmax = 134217728' }
    - { regexp: '^fs.file-max', line: 'fs.file-max = 65536' }
```

That snippet works but it's tedious. `regexp` and `line` has the duplicated parameter key names, let's clean up the code:

```yml
- name: set PHP-FPM parameters
  lineinfile:
    dest: /etc/php-fpm.conf
    regexp: "^{% raw %}{{ item.param }}{% endraw %}"
    insertafter: "^;{% raw %}{{ item.param }}{% endraw %}"
    line: "{% raw %}{{ item.param }} = {{ item.value }}{% endraw %}"
  with_items:
    - { param: 'error_log', value: '/var/log/php-fpm/error.log' }
    - { param: 'log_level', value: 'error' }
    - { param: 'emergency_restart_threshold', value: '10' }
```

Everything works fine untill I meet the following config:

```nginx
php_value[session.save_handler] = 'files'
```

I can't target keys like this with previous `regexp`, and cannot match square brackets directly or Ansible will fall running this task. So I have to escape speciall characters:

```yml
- name: set PHP-FPM pool parameters
  lineinfile:
    dest: /etc/php-fpm.d/www.conf
    regexp: {% raw %}'^{{ item.param|replace("[", "\[")|replace("]", "\]") }} *?='{% endraw %}
    insertafter: {% raw %}'^;{{ item.param|replace("[", "\[")|replace("]", "\]") }} *?='{% endraw %}
    line: "{% raw %}{{ item.param }} = {{ item.value }}{% endraw %}"
  with_items:
    - { param: 'php_value[session.save_handler]', value: 'memcache' }
    - ...
```

Cool for the summer.
