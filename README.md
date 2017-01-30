# Ansible: Ruby on Rails Server (Ubuntu 16.x)

Use this ansible playbook to setup a fresh server with the following components:

* Nginx
* Puma App Server
* Certbot (Let's Encrypt)
* PostgreSQL
* Memcached
* Redis
* Sidekiq
* Monit (to keep Puma and Sidekiq runnig)
* Elasticsearch
* ruby-install
* rbenv
* Directories to deploy Rails with Capistrano and Puma App Server (see below)
* Swapfile (useful for small DO instances)
* Tools (tmux, vim, htop, git, wget, curl etc.)

## Prerequisites & Config

1. Copy ```hosts.example``` to ```hosts``` and modify the contents.
2. Copy ```group_vars/all.example``` to ```group_vars/all``` and modify the contentes.

	There are a bunch of things you can set in ```group_vars/all```. Don't forget to add your host address to ```hosts```.

```
cp hosts.example hosts
cp group_vars/all.example group_vars/all
```

## Install Playbook

Run ```ansible-playbook site.yml -i hosts```.

## Rails Setup

For setuping Rails application use [Blueprint Gem](https://github.com/datarockets/blueprint)

## Feedback

Feel free to send feedback or report problems via GitHub issues!
