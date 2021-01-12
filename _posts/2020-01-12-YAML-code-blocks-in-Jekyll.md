---
title: YAML code blocks in Jekyll
tags: jekyll
show_author_profile: true

apache_package: "{{ apache_package }}"
php_package: "{{ php_package }}"

hello-world-start_highlight: "{% highlight yaml linenos %}"
hello-world-end_highlight: "{% endhighlight %}"

hello-world-apache_package: "{{ page.apache_package }}"
hello-world-php_package: "{{ page.php_package }}"
---
## The Problem
So, I am trying to create YAML code blocks for upcoming Ansible projects but Jekyll does not seem to render my code as I inteded.

When referencing variables they do not render in the ordinary code blocks.

This is probably because Jekyll needs actual varibles to render them or something in that fashion. 

See the lines **24/9** and **25/10** below.

### Post content before rendering
{% highlight markdown linenos %}
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
{{ page.hello-world-start_highlight }}
---
- hosts: all
  become: true
  tasks:

  - name: install apache2 and php packages
    package:
      name: 
        - "{{ page.apache_package }}"
        - "{{ page.php_package }}"
      state: latest
      update_cache: yes
{{ page.hello-world-end_highlight }}

{% endhighlight %}

### Faulty render
{% highlight yaml linenos %}
---
- hosts: all
  become: true
  tasks:

  - name: install apache2 and php packages
    package:
      name:
        - "{{ apache_package }}"
        - "{{ php_package }}"
      state: latest
      update_cache: yes
{% endhighlight %}

## The Solution
If I declare some variables in the beginning of the post file and then reference them in the codeblock as **page.apache_package** for example, Jekyll renders them as I inteded.

See the lines **6/28** and **7/29**.

This might not be the best solution, especially not if you want to render large code blocks with a lot of variables but it works for now!

### Post content before rendering
{% highlight markdown linenos %}
---
title: Hello World!
tags: uncategorized
show_author_profile: true

apache_package: "{{ page.apache_package }}"
php_package: "{{ page.php_package }}"

---
This is my first post! :vulcan_salute:

Trying out some formatting etc.

## Some text
Some more text! Well I am also writing THIS and THIS.
Also, some more text here.

## Code Blocks
{{ page.hello-world-start_highlight }}
---
- hosts: all
  become: true
  tasks:

  - name: install apache2 and php packages
    package:
      name: 
        - "{{ page.hello-world-apache_package }}"
        - "{{ page.hello-world-php_package }}"
      state: latest
      update_cache: yes
{{ page.hello-world-end_highlight }}

{% endhighlight %}

### Correct render with variables
{% highlight YAML linenos %}
---
- hosts: all
  become: true
  tasks:

  - name: install apache2 and php packages
    package:
      name:
        - "{{ page.apache_package }}"
        - "{{ page.php_package }}"
      state: latest
      update_cache: yes
{% endhighlight %}
