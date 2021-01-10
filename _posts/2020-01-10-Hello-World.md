---
title: Hello World!
tags: uncategorized
show_author_profile: true
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
  pre_tasks:

  - name: install updates (RHEL/CentOS)
    tags: always
    dnf: 
      update_cache: yes
      update_only: yes
    when: ansible_distribution in ["RedHat", "CentOS"]
{% endhighlight %}
