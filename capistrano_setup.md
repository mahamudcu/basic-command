## Capistrano Setup

#### Add Gem File

```bash
  group :development do

  gem 'capistrano'

  gem 'capistrano3-puma'

  gem 'capistrano-rails', require: false

  gem 'capistrano-bundler', require: false

  gem 'capistrano-rvm'

  gem 'capistrano-sidekiq', github: 'seuros/capistrano-sidekiq'

end
```
#### RUN Command
```bash
  bundle install
```
#### Cap-install-Command
```bash
  bundle exec cap install
```

#### Capfile Setup

```bash
  # Load DSL and set up stages

require "capistrano/setup"


# Include default deployment tasks

require "capistrano/deploy"


# Load the SCM plugin appropriate to your project:

#

# require "capistrano/scm/hg"

# install_plugin Capistrano::SCM::Hg

# or

# require "capistrano/scm/svn"

# install_plugin Capistrano::SCM::Svn

# or

require "capistrano/scm/git"

install_plugin Capistrano::SCM::Git


# Include tasks from other gems included in your Gemfile

#

# For documentation on these, see for example:

#

#   https://github.com/capistrano/rvm

#   https://github.com/capistrano/rbenv

#   https://github.com/capistrano/chruby

#   https://github.com/capistrano/bundler

#   https://github.com/capistrano/rails

#   https://github.com/capistrano/passenger

#

require "capistrano/rvm"

# require "capistrano/rbenv"

# require "capistrano/chruby"

require "capistrano/bundler"

require "capistrano/rails/assets"

require "capistrano/rails/migrations"

require 'capistrano/puma'

# require "capistrano/passenger"

require 'capistrano/sidekiq'

# Load custom tasks from `lib/capistrano/tasks` if you have any defined

install_plugin Capistrano::Puma

Dir.glob("lib/capistrano/tasks/*.rake").each { |r| import r }

```

#### Change in deploy.rb file

```bash
 # config valid for current version and patch releases of Capistrano

lock "~> 3.11.2"


set :application, "app_name"

set :repo_url, "git@github.com:user/repogitory.git"



# Default branch is :master

# ask :branch, proc { `git rev-parse --abbrev-ref HEAD`.chomp }


# Default deploy_to directory is /var/www/my_app

# set :deploy_to, '/var/www/my_app'


# Default value for :scm is :git

set :scm, :git

set :pty, false

# set :ssh_options, {

#                     :keys => '/home/nazrul/Desktop/shyftn/shyftn.pem'

#                 }

# Default value for :format is :pretty

# set :format, :pretty


# Default value for :log_level is :debug

# set :log_level, :debug


# Default value for :pty is false

# set :pty, true

server 'your server ip',

       :user => 'username',

       :roles => %w{web app db}


set :rvm_ruby_version, '2.6.3'


# Default value for :linked_files is []

set :linked_files, %w{config/application.yml config/database.yml}# config/secrets.yml}


# Default value for linked_dirs is []

set :linked_dirs, %w{log tmp/pids tmp/cache tmp/sockets vendor/bundle public/system public/assets public/uploads}# pdf_files}


# Default value for default_env is {}

# set :default_env, { path: "/opt/ruby/bin:$PATH" }


# set :assets_prefix, 'pipeline_assets'


# Default value for keep_releases is 5

set :keep_releases, 5


namespace :deploy do

  #before :deploy, "deploy:check_revision"

  #after 'deploy:symlink:shared', 'deploy:compile_assets_locally'

  after :finishing, 'deploy:cleanup'

  #before 'deploy:setup_config', 'nginx:remove_default_vhost'

  #after 'deploy:setup_config', 'nginx:reload'

  #after 'deploy:setup_config', 'monit:restart'

  #after 'deploy:publishing', 'deploy:restart'


  desc 'Restart application'

  task :restart do

    on roles(:app), in: :sequence, wait: 5 do

      # Your restart mechanism here, for example:

      # execute :touch, release_path.join('tmp/restart.txt')

    end

  end


  # after :publishing, :restart


  after :restart, :clear_cache do

    on roles(:web), in: :groups, limit: 3, wait: 10 do

      # Here we can do anything such as:

      # within release_path do

      #   execute :rake, 'cache:clear'

      # end

    end

  end

end


task :upload_secret_files do

  on roles(:all) do |host|

    begin

      execute "mkdir #{shared_path}/config"

    rescue

    end

    upload! "config/application.yml", "#{shared_path}/config/application.yml"

  end

end


desc 'Invoke a rake command on the remote server'

task :invoke, [:command] => 'deploy:set_rails_env' do |task, args|

  on primary(:app) do

    within current_path do

      with :rails_env => fetch(:rails_env) do

        rake args[:command]

      end

    end

  end

end
```

#### Change in deploy/production.rb File:

```bash
 # server-based syntax

# ======================

# Defines a single server with a list of roles and multiple properties.

# You can define all roles on a single server, or split them:


# server "example.com", user: "deploy", roles: %w{app db web}, my_property: :my_value

# server "example.com", user: "deploy", roles: %w{app web}, other_property: :other_value

# server "db.example.com", user: "deploy", roles: %w{db}


# role-based syntax

# ==================


# Defines a role with one or multiple servers. The primary server in each

# group is considered to be the first unless any hosts have the primary

# property set. Specify the username and a domain or IP for the server.

# Don't use `:all`, it's a meta role.


# role :app, %w{deploy@example.com}, my_property: :my_value

# role :web, %w{user1@primary.com user2@additional.com}, other_property: :other_value

# role :db,  %w{deploy@example.com}


# Configuration

# =============

# You can set any configuration variable like in config/deploy.rb

# These variables are then only loaded and set in this stage.

# For available Capistrano configuration variables see the documentation page.

# http://capistranorb.com/documentation/getting-started/configuration/

# Feel free to add new variables to customise your setup.


# Custom SSH Options

# ==================

# You may pass any option but keep in mind that net/ssh understands a

# limited set of options, consult the Net::SSH documentation.

# http://net-ssh.github.io/net-ssh/classes/Net/SSH.html#method-c-start

#

# Global options

# --------------

#  set :ssh_options, {

#    keys: %w(/home/rlisowski/.ssh/id_rsa),

#    forward_agent: false,

#    auth_methods: %w(password)

#  }

#

# The server-based syntax can be used to override options:

# ------------------------------------

# server "example.com",

#   user: "user_name",

#   roles: %w{web app},

#   ssh_options: {

#     user: "user_name", # overrides user setting above

#     keys: %w(/home/user_name/.ssh/id_rsa),

#     forward_agent: false,

#     auth_methods: %w(publickey password)

#     # password: "please use keys"

#   }


set :deploy_to, "/home/username/app_directory"

set :rails_env, "production"

set :branch, 'master'


Step-7: Change in environments/production.rb file

config.public_file_server.enabled = true

config.assets.compile = true 
```