---
date: 2010-03-01
update: 2015-12-17
author: Uchio
email: udzura@udzura.jp
title: 'Guides: Basic Projects'
sidebar: 'guides/sidebar'
---

# Basic Projects

Be sure to read the [Installation](/guides/installation "Installation") instructions first. You
might also want to check out the [Getting Started](/guides/getting-started "Getting Started")
guide for a better understanding of Sinatra and Padrino if you are new to the
stack.

---


## Generating a Project

To generate a new Padrino project using its defaults (RSpec for testing and Haml
for rendering) and no database adapter, simply invoke the following command:


~~~ shell
$ padrino g project my_project
~~~


Padrino has also built-in support for several different mocking, testing,
rendering, ORM, and JavaScript components.


~~~ shell
$ padrino g project custom_project -t rspec -d activerecord -s prototype
~~~


For a breakdown of all the available components options please refer to the
[Generators](/guides/generators "Generators") page.


### Persistence Engine

Whenever you are creating a new project, Padrino will assume by default that a
database is not required for your project.

To add support for a persistence engine, specify a supported ORM of your choice
to use by flagging the `padrino g` command with the `-d` option followed by the
name of your ORM:


~~~ shell
$ padrino g project your_project -d activerecord # Uses ActiveRecord
$ padrino g project your_project -d datamapper   # Uses Datamapper
$ padrino g project your_project -d mongomapper  # Uses MongoMapper
$ padrino g project your_project -d sequel       # Uses Sequel
$ padrino g project your_project -d couchrest    # Uses CouchRest
~~~


For the SQL-based persistence engines, you can even specify the RDBMS adapter to
use with the `-a` option followed by the name of the adapter:


~~~ shell
$ padrino g project your_project -d datamapper   -a mysql    # Uses Datamapper and MySQL
$ padrino g project your_project -d activerecord -a postgres # Uses ActiveRecord and Postgres
$ padrino g project your_project -d sequel       -a sqlite   # Uses Sequel and Sqlite3
~~~


The adapters currently supported are `sqlite`, `mysql`, and `postgres` for use
with `datamapper`, `activerecord`, or `sequel`.

---


## Generating Applications

Padrino’s main concept is to generate a default "project" or "core application":


~~~ shell
$ padrino g project my_project
~~~


You can then add, if needed, sub-applications to your existing Padrino "project":


~~~ shell
$ cd my_project
$ padrino g app gallery
~~~


You can also generate your own controllers, mailers, models, etc. for your
"gallery" app as well.


~~~ shell
$ padrino g controller sample get:index --app gallery
~~~


Whenever generating a "mounted" app, Padrino will mount that application
automatically. As a reference, the above example "gallery" application will be
mounted to: `/gallery`.

You can easily change and configure your "mounted" application path and decide
where your applications will be mounted, by editing your `config/apps.rb` file.

---


## Generating the Admin Section

Let’s start by creating a new Padrino project using Active Record:


~~~ shell
$ padrino g project blog -d activerecord
~~~


Install all project dependencies:


~~~ shell
$ cd blog
$ bundle install
~~~


Padrino ships with a beautiful Admin interface, highly inspired by the
[web-app-theme](http://github.com/pilu/web-app-theme "web-app-theme").

Remember that Padrino has been principally structured and designed for mounting
multiple applications at the same time. Under this perspective, our **admin**
section is nothing but a new Padrino **application**:


~~~ shell
$ padrino g admin
~~~


You need to configure your database settings in `config/database.rb` and run
your migrations to add tables and columns to your database:


~~~ shell
$ padrino rake ar:migrate
~~~


Create your first admin account; this is easily achieved by seeding your
database with default admin data, stored in your `seed.rb` file:


~~~ shell
$ padrino rake seed
~~~


You will see this in your terminal:


~~~ shell
Which email do you want use to log into admin? info@padrino.local
Tell me the password to use: foobar

Perfect! Your account was created.

Now you can start your server with Padrino start and then log into /admin with:
    email: info@padrino.local
    password: foobar
~~~


You are now ready to start your webserver:


~~~ shell
$ padrino start
~~~

Point your browser to `http://localhost:3000/admin` and log in by using the
email and password provided while seeding your database:

---


## Adding a model

Let’s add a new `Post` model to our blog:


~~~ shell
$ padrino g model post name:string body:text
~~~


Run the migrations to add database table columns to our database for our newly
created Post model:


~~~ shell
$ padrino rake ar:migrate
~~~


Create a new admin section for managing (creating, updating, deleting) our blog
posts:


~~~ shell
$ padrino g admin_page post
~~~


That’s all! Start your webserver and begin adding some posts.

[Next Section &ndash; Generators](/guides/generators){: .button}
{: .excerpt}
