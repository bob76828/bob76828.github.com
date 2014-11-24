---
layout:     post
title:      "Setup Ruby on Rails Production Environment"
subtitle:   "Ubuntu 12.04 + Ruby 2.1.2 with RVM deployment"
date:       2014-08-13 12:00:00
author:     "bob76828"
---

# Ubuntu 12.04 + Ruby 2.1.2 with RVM deployment
_根據[xdite](https://github.com/xdite)的[setup-production-development](https://github.com/rocodev/guides/wiki/setup-production-development)重新編修_

## Update and upgrade system

* `sudo apt-get update`
* `sudo apt-get upgrade`
* `sudo apt-get install vim`
* `sudo apt-get install ssh`
* `sudo apt-get install curl`
* `sudo apt-get install aptitude`

## Create user: apps

```bash
sudo adduser apps
```

**make apps have sudo power**

```bash
sudo usermod -g sudo apps
```

## switch to apps

```bash
sudo su apps
```

## Changing the Time Zone

`sudo dpkg-reconfigure tzdata`

## Install Build Tools and Library for Ruby

`sudo apt-get install build-essential zlib1g-dev libssl-dev libreadline5 libyaml-dev`

## Install ruby

```bash
curl -L https://get.rvm.io | bash -s stable --ruby=2.1.2
source /home/apps/.rvm/scripts/rvm
```

## Install XML parser

`sudo apt-get install libxml2 libxml2-dev libxslt1-dev`

## Install node.js

`sudo apt-get install nodejs`

## Install DB (自行安裝專案使用的DB)

* 安裝PostgreSQL|installing postgresql 9.3.x on ubuntu 12.04

## Install git

`sudo apt-get install git`

## Install Bundler

`gem install bundler`

## Nokogiri

`gem install nokogiri`

## Install imagemagick

```bash
sudo apt-get remove imagemagick
sudo apt-get install libmagickcore-dev libmagickwand-dev
```

### 下載 ImageMaigck

```bash
sudo wget http://www.imagemagick.org/download/ImageMagick.tar.gz
sudo tar xvzf ImageMagick.tar.gz
cd ImageMagick-x.x.x
sudo ./configure
sudo make
sudo make install
```

然後 vim ~/.bash_profile 加入

```
LD_LIBRARY_PATH=/usr/local/lib
```

## Install rmagick

`gem install rmagick`

## Install libcurl4-openssl-dev for passenger

`sudo aptitude install libcurl4-openssl-dev`

## Install passenger

`gem install passenger`

## Install nginx

複製底下的 output
```
which passenger-install-nginx-module
```

然後把 output 用 `rvmsudo` 執行

```
rvmsudo OUTPUT
```

```
# Choose "download, compile, and install Nginx for me"
# Accept defaults for any other questions it asks you
```

## setup a script to control Nginx

```bash
sudo git clone git://github.com/jnstq/rails-nginx-passenger-ubuntu.git
sudo mv rails-nginx-passenger-ubuntu/nginx/nginx /etc/init.d/nginx
sudo chown root:root /etc/init.d/nginx
sudo /etc/init.d/nginx restart
```

## set up nginx.conf

`vim /opt/nginx/conf/nginx.conf`

#### insert following codes into the /opt/nginx/conf/nginx.conf

```
server {
      listen 80;
      server_name my_project.cc;
      root /home/apps/my_project/current/public;
      passenger_enabled on;
      rails_env production;
    }
```

## create folder for the project

```bash
cd ~
mkdir ~/my_project
```

## set production database.yml and secrets.yml

```bash
cd ~
mkdir ~/my_project/shared
mkdir ~/my_project/shared/config
```
  
**加入database.yml**  

`vim /home/apps/my_project/shared/config/database.yml`

```
production:
  adapter: my_project_use_adapter
  encoding: utf8
  reconnect: false
  database: my_project_database
  pool: 5
  username: username
  password: password
```
  
**加入secrets.yml**  

`vim /home/apps/my_project/shared/config/secrets.yml`

```
production:
  secret_key_base: <%= ENV["SECRET_KEY_BASE"] %>
```

## set up deploy key for apps 

```bash
ssh-keygen
more ~/.ssh/id_rsa.pub
複製！！
```
#### Add the SSH key to GitHub

[https://help.github.com/articles/generating-ssh-keys](https://help.github.com/articles/generating-ssh-keys)

## Set up authorized keys for developers

`vim /home/apps/.ssh/authorized_keys`

Paste the ssh public keys into the "/home/apps/.ssh/authorized_keys" file

（複製本機的id_rsa.pub貼在server的authorized_keys檔案內）

## Set up Capistrano 

**Add the following Gems to your Gemfile**

```ruby
gem 'capistrano', '3.1.0'
gem 'capistrano-rails', '~> 1.1.0'
gem 'capistrano-bundler'
gem 'capistrano-rvm'
```
  
**run**

```bash
bundle install
bundle exec cap install
```
  
**Paste following codes into the "/my_project/Capfile" file**

```ruby
# Load DSL and Setup Up Stages
require 'capistrano/setup'

# Includes default deployment tasks
require 'capistrano/deploy'

# Includes tasks from other gems included in your Gemfile
#
# For documentation on these, see for example:
#
#   https://github.com/capistrano/rvm
#   https://github.com/capistrano/rbenv
#   https://github.com/capistrano/chruby
#   https://github.com/capistrano/bundler
#   https://github.com/capistrano/rails
#
require 'capistrano/rvm'
require 'capistrano/rails'
require 'capistrano/bundler'
#require 'capistrano/rbenv'
#require 'capistrano/chruby'
#require 'capistrano/rails/assets'
#require 'capistrano/rails/migrations'

# Loads custom tasks from `lib/capistrano/tasks' if you have any defined.
Dir.glob('lib/capistrano/tasks/*.cap').each { |r| import r }
```

**Paste following codes into the "/my_project/config/deploy.rb" file**

```ruby
# Ensure that bundle is used for rake tasks
SSHKit.config.command_map[:rake] = "bundle exec rake"

set :application, 'my_project'
set :scm, :git
set :repo_url, 'git@github.com:yourself/my_project.git'
set :deploy_via, :remote_cache
set :stages, ["production"]
set :deploy_to, '/home/apps/my_project'

# how many old releases do we want to keep
set :keep_releases, 5

# files we want symlinking to specific entries in shared.
set :linked_files, %w{config/database.yml config/secrets.yml}


namespace :deploy do

  # make sure we're deploying what we think we're deploying

  desc 'Restart application'
  task :restart do
    on roles(:app), in: :sequence, wait: 5 do
      # Your restart mechanism here, for example:
      # execute :touch, release_path.join('tmp/restart.txt')
    end
  end

  after :publishing, :restart

  after :restart, :clear_cache do
    on roles(:web), in: :groups, limit: 3, wait: 10 do
      # Here we can do anything such as:
      # within release_path do
      #   execute :rake, 'cache:clear'
      # end
    end
  end

  after :finishing, 'deploy:cleanup'

end

```

**Paste following codes into the "/my_project/deploy/production.rb" file  
(請自行修改使用ssh_key登入或是使用password登入,官方建議使用ssh_key,設定方式為上面的_Set up authorized keys for developers_)**

```ruby
server "your_production_ip", user: 'apps', roles: %w{app web db},
       ssh_options: {
           user: 'apps', # overrides user setting above
           keys: %w(/home/user_name/.ssh/id_rsa),
           forward_agent: false,
           auth_methods: %w(publickey password),
           #password: 'password'
       }
```

**開始佈署**

`cap production deploy`