cd /Users/raynovarina/Sites/AtomProjects/Rails5/BobBot/verify-service

per: https://guides.rubyonrails.org/getting_started.html
     https://www.railstutorial.org/book/beginning
     https://devcenter.heroku.com/articles/getting-started-with-rails5

Created and initialized rails 5 app:

cd /Users/raynovarina/Sites/AtomProjects/Rails5/
$ md GettingStarted
$ cd GettingStarted/blog

$ ruby -v
ruby 2.4.0p0
$ gem install rails
Fetching: activesupport-5.2.1.gem
...
$ rails new blog
create
create  README.md
create  Rakefile
...
create  Gemfile
  run  git init from "."
  Initialized empty Git repository in /Users/raynovarina/Sites/AtomProjects/Rails5/GettingStarted/blog/.git/
create  package.json
create  app
...
run  bundle install
Resolving dependencies.............................
Fetching rake 12.3.1
...
Using sqlite3 1.3.13
...
run  bundle exec spring binstub --all
* bin/rake: spring inserted
* bin/rails: spring inserted

$ ls
blog
Rays-MacBook-Pro:GettingStarted raynovarina$ cd blog
Rays-MacBook-Pro:blog raynovarina$ ls
Gemfile         Rakefile        config          lib             public          tmp
Gemfile.lock    app             config.ru       log             storage         vendor
README.md       bin             db              package.json    test

Rays-MacBook-Pro:blog raynovarina$ rails server
=> Booting Puma
=> Rails 5.2.1 application starting in development
..
* Listening on tcp://0.0.0.0:3000
Use Ctrl-C to stop

Access via localhost:3000
============================
Says "Yay, you are on rails"

Add landing page and route for basic rails app:
-----------------------------------------------
$ rails generate controller Welcome index

/config/routes.rb
Rails.application.routes.draw do
  get 'welcome/index'
  # For details on the DSL available within this file, see http://guides.rubyonrails.org/routing.html

  root 'welcome#index'
end
/app/views/welcome/index.html.erb
<h1>Hello, Rails!</h1>

Access via localhost:3000
Says "Hello, Rails!"

Create GitHub repository:
--------------------------
At github account, create new repository: rails-heroku-bobbot-verify-service
 and then locally do:
  $ git init
  $ git remote add origin https://github.com/RayNovarina/rails-heroku-bobbot-verify-service.git

Create Heroku app:
-------------------
// make sure Heroku toolbelt utils are installed and we are logged into Heroku.
$ heroku --version
heroku/7.18.9 darwin-x64 node-v11.1.0
$ heroku login
$ heroku create rails-bobbot-verify-service-94
Rays-MacBook-Pro:blog raynovarina$ heroku create rails-bobbot-verify-service-94
Creating ⬢ rails-bobbot-verify-service-94... done
https://rails-bobbot-verify-service-94.herokuapp.com/ | https://git.heroku.com/rails-bobbot-verify-service-94.git
Rays-MacBook-Pro:blog raynovarina$ git remote -v
heroku  https://git.heroku.com/rails-bobbot-verify-service-94.git (fetch)
heroku  https://git.heroku.com/rails-bobbot-verify-service-94.git (push)
origin  https://github.com/RayNovarina/rails-heroku-bobbot-verify-service.git (fetch)
origin  https://github.com/RayNovarina/rails-heroku-bobbot-verify-service.git (push)


Deploy changes to github and heroku:
------------------------------------
$ git add .
Rays-MacBook-Pro:blog raynovarina$ git commit -am "First commit"
  [master (root-commit) a9c4ee6] First commit

$ git push origin master
  Counting objects: 95, done.
  ...
$ git push heroku master
  Counting objects: 95, done.
  ...
  remote: -----> Ruby app detected
  remote: -----> Compiling Ruby/Rails
  remote: -----> Using Ruby version: ruby-2.4.0
  remote: -----> Installing dependencies using bundler 1.15.2
  ...
  remote:        Gem::Ext::BuildError: ERROR: Failed to build gem native extension.
  remote:
  remote:        current directory:
  remote:        /tmp/build_e0101be72c563001dbbb2ebfe1fc8cba/vendor/bundle/ruby/2.4.0/gems/sqlite3-1.3.13/ext/sqlite3
  remote:        /tmp/build_e0101be72c563001dbbb2ebfe1fc8cba/vendor/ruby-2.4.0/bin/ruby -r
  remote:        ./siteconf20181126-232-1dveves.rb extconf.rb
  remote:        checking for sqlite3.h... no
  remote:        sqlite3.h is missing. Try 'brew install sqlite3',
  ...
  remote:        An error occurred while installing sqlite3 (1.3.13), and Bundler cannot
  remote:        continue.
  ...
  remote: !       Push rejected to rails-bobbot-verify-service-94.
  remote:

Solution was to use different rails new app syntax or manually add postgres gem file and configuration:
-----------------------------
Should have done:
$ rails new myapp --database=postgresql

Now have to Add the pg gem to app/Gemfile
If you’re using an existing app that was created without specifying --database=postgresql, you need to add the pg gem to your Rails project. Edit your Gemfile and change this line:
gem 'sqlite3'
To this:
gem 'pg'

Now re-install your dependencies (to generate a new Gemfile.lock):
$ bundle install
..
Fetching pg 1.1.3
Installing pg 1.1.3 with native extensions
....

/config/database.yml
In addition to using the pg gem, ensure that your config/database.yml file is using the postgresql adapter. The development section of your config/database.yml file should look something like this:

$ cat config/database.yml
# PostgreSQL. Versions 9.1 and up are supported.
#
# Install the pg driver:
#   gem install pg
# On OS X with Homebrew:
#   gem install pg -- --with-pg-config=/usr/local/bin/pg_config
# On OS X with MacPorts:
#   gem install pg -- --with-pg-config=/opt/local/lib/postgresql84/bin/pg_config
# On Windows:
#   gem install pg
#       Choose the win32 build.
#       Install PostgreSQL and put its /bin directory on your path.
#
# Configure Using Gemfile
# gem 'pg'
#
default: &default
  adapter: postgresql
  encoding: unicode
  # For details on connection pooling, see Rails configuration guide
  # http://guides.rubyonrails.org/configuring.html#database-pooling
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
  username: postgres
  password:

development:
  <<: *default
  database: db/development_postgresql

  # The specified database role being used to connect to postgres.
  # To create additional roles in postgres see `$ createuser --help`.
  # When left blank, postgres will use the default role. This is
  # the same name as the operating system user that initialized the database.
  #username: verify-service

  # The password associated with the postgres role (username).
  #password:

  # Connect on a TCP socket. Omitted by default since the client uses a
  # domain socket that doesn't need configuration. Windows does not have
  # domain sockets, so uncomment these lines.
  #host: localhost

  # The TCP port the server listens on. Defaults to 5432.
  # If your server runs on a different port number, change accordingly.
  #port: 5432

  # Schema search path. The server defaults to $user,public
  #schema_search_path: myapp,sharedapp,public

  # Minimum log levels, in increasing order:
  #   debug5, debug4, debug3, debug2, debug1,
  #   log, notice, warning, error, fatal, and panic
  # Defaults to warning.
  #min_messages: notice

# Warning: The database defined as "test" will be erased and
# re-generated from your development database when you run "rake".
# Do not set this db to the same as development or production.
test:
  <<: *default
  database: test_postgresql

staging:
  <<: *default
  database: db/production_postgresql

# As with config/secrets.yml, you never want to store sensitive information,
# like your database password, in your source code. If your source code is
# ever seen by anyone, they now have access to your database.
#
# Instead, provide the password as a unix environment variable when you boot
# the app. Read http://guides.rubyonrails.org/configuring.html#configuring-a-database
# for a full rundown on how to provide these environment variables in a
# production deployment.
#
# On Heroku and other platform providers, you may have a full connection URL
# available as an environment variable. For example:
#
#   DATABASE_URL="postgres://myuser:mypass@localhost/somedatabase"
#
# You can use this database configuration with:
#
#   production:
#     url: <%= ENV['DATABASE_URL'] %>
#
production:
  <<: *default
  #database: db/production_postgresql
  #username: verify-service
  #password: <%= ENV['VERIFY-SERVICE_DATABASE_PASSWORD'] %>
  url: <%= ENV['DATABASE_URL'] %>


Now re-install your dependencies (to generate a new Gemfile.lock):
$ bundle install
..

View on Heroku:

  https://rails-bobbot-verify-service-94.herokuapp.com shows:

  Hello, Rails!
