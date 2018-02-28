# Ansible: Ruby on Rails Server
Use this ansible playbook to setup a fresh server with the following components:

* Nginx
* Puma App Server
* Certbot (Let's Encrypt)
* MySQL
* Memcached
* Redis
* Sidekiq
* Monit (to keep Puma and Sidekiq runnig)
* Elasticsearch
* ruby-install
* chruby
* Directories to deploy Rails with Capistrano and Puma App Server (see below)
* Swapfile (useful for small DO instances)
* Locales
* Tools (tmux, vim, htop, git, wget, curl etc.)

## Prerequisites & Config

1. Rename ```hosts.example``` to ```hosts``` and modify the contents.
2. Rename ```group_vars/all.example``` to ```group_vars/all``` and modify the contentes.

	There are a bunch of things you can set in ```group_vars/all```. Don't forget to add your host address to ```hosts```.

## Install Playbook

Run ```ansible-playbook site.yml -i hosts```.

## Rails Setup

This is just a loose guideline for what you need to deploy your app with this playbook and server config. Please keep in mind, that you need to modify some values depending on your setup (**especially passwords and paths!**)

### Gemfile

Add the following gems to your Gemfile and install via ```bundle install```:

```ruby
group :development do
  gem 'capistrano', '~> 3.6'
  gem 'capistrano-rails', '~> 1.2'
  gem 'capistrano-chruby'
  gem 'capistrano3-puma'
  gem 'capistrano-sidekiq'
  gem 'capistrano-npm'
end
```

### Capfile

Add the following lines to your Capfile:

```ruby
# General
require 'capistrano/rails'
require 'capistrano/bundler'
require 'capistrano/rails/migrations'
require 'capistrano/rails/assets'
require 'capistrano/chruby'
require 'capistrano/npm'

# Puma
require 'capistrano/puma'
install_plugin Capistrano::Puma  # Default puma tasks
install_plugin Capistrano::Puma::Workers  # if you want to control the workers (in cluster mode)
# install_plugin Capistrano::Puma::Jungle # if you need the jungle tasks
install_plugin Capistrano::Puma::Monit  # if you need the monit tasks
install_plugin Capistrano::Puma::Nginx  # if you want to upload a nginx site template

# Sidekiq
require 'capistrano/sidekiq'
require 'capistrano/sidekiq/monit'
```

### config/deploy.rb

Please edit "deploy\_app\_name", "repo\_url", "deploy\_to" and "chruby\_ruby" (if you've changed the Ruby version in `group_vars/all`).

Your ```config/deploy.rb``` should look similar to this example:

```ruby
set :application, 'deploy_app_name'
set :repo_url, 'YOUR_GIT_REPO'
set :deploy_to, '/home/deploy/deploy_app_name'
set :chruby_ruby, 'ruby-2.3.3'
set :nginx_use_ssl, true
set :puma_init_active_record, true
set :linked_files, fetch(:linked_files, []).push('config/database.yml', 'config/secrets.yml')
set :linked_dirs, fetch(:linked_dirs, []).push('log', 'tmp/pids', 'tmp/cache', 'tmp/sockets', 'public/system')
set :keep_releases, 5
```

### config/deploy/production.rb

Add the target host:

```ruby
server 'your_host_address', user: 'deploy', roles: %w{app db web}
```

## Feedback

Feel free to send feedback or report problems via GitHub issues!
