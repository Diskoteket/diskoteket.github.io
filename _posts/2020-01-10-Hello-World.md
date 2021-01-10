---
title: Hello World!
tags: uncategorized
show_author_profile: true

variables:
- name: apache_package
  value: initialValue 

---
This is my first post! :vulcan_salute:

Trying out some formatting etc.

## Some text
Some more text! Well I am also writing THIS and THIS.
Also, some more text here.

## Code Blocks
{% highlight yml linenos %}
---
- hosts: all
  become: true
  tasks:

  - name: install apache2 and php packages
    package:
      name: 
        - "{{ variables.apache_package }}"
        - "{{ php_package }}"
      state: latest
      update_cache: yes
{% endhighlight %}
