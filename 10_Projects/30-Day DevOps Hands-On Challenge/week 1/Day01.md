```YAML
- name: Install Nginx on Pathnex server
  host: all
  become: yes

  tasks:
  	- name: Install nginx
      yum:
      	name: nginx
        state: present
```

Docker

```Shell
docker --version

docker run ubuntu echo "Hello Pathnex"
```

Docker

```Shell
FROM ubuntu:22.04
CMD ["echo", "Hello Pathnex"]
```
