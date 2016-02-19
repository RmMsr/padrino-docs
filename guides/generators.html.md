---
date: 2010-03-01
update: 2015-12-17
author: Nathan
email: nesquena@gmail.com
title: Generators
---

# Generators

Padrino provides generator support for quickly creating new Padrino
applications. This provides many benefits such as constructing the recommended
Padrino application structure, auto-generating a Gemfile listing all starting
dependencies and guidelines provided within the generated files to help orient a
new user to using Padrino.

One important feature of the generators is that they were built from the ground
up to support a wide variety of tools, libraries and gems for use within your
padrino application.

This means that Padrino generators do **not** lock you into using any particular
database, ORM, testing framework, templating engine or javascript library. In
fact, when generating an application you can actually tell Padrino which
components you would like to use!

---


## Project Generator

The usage for the project generator is quite simple:


~~~ shell
$ padrino g project <the_app_name> </path/to/create/app> --<component-name> <value>
~~~


The simplest possible command to generate a base application would be:


~~~ shell
$ padrino g project demo_project
~~~


This would construct a Padrino application DemoApp (which extends from
`Padrino::Application`) inside the folder `demo\_project` at our current path.
Inside the application there would be configuration and setup performed for the
default components.

You can also define specific components to be used:


~~~ shell
$ padrino g project demo_project -t rspec -e haml -m rr -s jquery -d datamapper -c sass
~~~


You can also instruct the generator to skip a certain component to avoid using
one at all (or to use your own):


~~~ shell
$ padrino g project demo_project --test none --renderer none
~~~


You can also specify an alternate name for your core application using the
`--app` option:


~~~ shell
$ padrino g project demo_project --app alternate_app_name # alias -n
~~~


The generator uses the `bundler` gem to resolve any application dependencies
when the application is newly created. The necessary bundler command can be
executed automatically through the generator with:


~~~ shell
$ padrino g project demo_project --run_bundler # alias -b
~~~


or this can be done manually through executing command `bundle install` in the
terminal at the root of the generated application.


For more examples of using the project generator in common cases, check out the
[Basic Projects](/guides/basic-projects "Basic Projects") guide.


The generator framework within Padrino is extensible and additional components
and tools can be added easily. This would be achieved through forking our
project and reading through the code in `lib/generators/project.rb` and the
setup instructions inside the relevant files within
`lib/generators/components/`. We are happy to accept pull requests for
additional component types not originally included (although helping us maintain
them would also be appreciated).


The project generator has several available configuration options:

>
  Options|Default|Aliases|Description
  :------|:------|:------|:----------
  bundle|false|-b|execute bundler dependencies installation
  root|.|-r|the root destination path for the project
  dev|false|none|use edge version from local git checkout
  app|nil|-n|specify app name different from the project name
  tiny|false|-i|generate tiny project skeleton
  adapter|sqlite|-a|specify orm db adapter (mysql, sqlite, postgres)
{: .excerpt--small }


The available components and their default options are listed below:

>
  Component|Default|Aliases|Options
  :--------|:------|:------|:------
  orm|none|-d|mongoid, activerecord, datamapper, couchrest, mongomatic, ohm, ripple, sequel, dynamoid
  test|none|-t|bacon, shoulda, cucumber, testunit, riot, rspec, minitest, steak
  script|none|-s|prototype, rightjs, jquery, mootools, extcore, dojo
  renderer|haml|-e|erb, haml, slim, liquid
  stylesheet|none|-c|sass, less, scss, compass
  mock|none|-m|rr, mocha
{: .excerpt--small }


Note: Be careful with your naming when using generators and do not have your
project name, or any models or controllers overlap. Avoid naming your app
“Posts” and then your controller or subapp with the same name.

---


## Some examples

**Generate a project with a different application name from the project path**


~~~ shell
$ padrino g my_project -n blog
~~~


This will generate the project at path `my_project/` but the applications name
will be **Blog**.


**Generate a project with mongoid and run bundler after**


~~~ shell
$ padrino g project your_project -d mongoid -b
~~~


**Generate a project with riot test and rr mocking**


~~~ shell
$ padrino g project your_project -t riot -m rr
~~~


**Generate a project with sequel with mysql**


~~~ shell
$ padrino g project your_project -d sequel -a mysql
~~~


**Generate a tiny project skeleton**


~~~ shell
$ padrino g project your_project --tiny
~~~


**Choose a root for your project**


~~~ shell
$ padrino g project your_project -r /usr/local/padrino
~~~


This will create a new padrino project in `/usr/local/padrino/your_project/`


**Use Padrino from a git cloned repository**


~~~ shell
padrino g project your_project [--dev] # Use padrino from a git checkout
~~~


Visit [The Bleeding Edge](/guides/the-bleeding-edge "The Bleeding Edge") for more info how to setup a **dev** environment.

---


## Plugin Generator

The Plugin Generator allows you to create Padrino projects based on a template
file that contains all the necessary actions needed to create the project.
Plugins can also be executed within an existing Padrino application. The plugin
generator provides a simple DSL in addition with leveraging Thor to make
generating projects a breeze!


~~~ shell
$ padrino g project my_project --template path/to/my_template.rb
~~~


This will generate a project based on the template file provided. You can also
generate a project based on a remote url such as a
[gist](https://gist.github.com/ "gist") for an additional level of convenience:


~~~ shell
$ padrino g project my_project --template https://gist.github.com/356156
~~~


You can also execute template files directly from
[the official templates repo](http://github.com/padrino/padrino-recipes/tree/master/templates "the official templates repo"):


~~~ shell
$ padrino g project my_project --template sampleblog
~~~


You can also apply templates as plugins to existing Padrino applications:


~~~ shell
$ cd path/to/existing/padrino/app
$ padrino g plugin path/to/my_plugin.rb
~~~


You can also execute plugin files directly from
[the official plugins repo](https://github.com/padrino/padrino-recipes/tree/master/plugins/ "the official plugins repo"):


~~~ shell
$ cd path/to/existing/padrino/app
$ padrino g plugin hoptoad
~~~


You can even get a list of available plugins with the following command:


~~~ shell
$ padrino g plugin --list
~~~


A simple template (plugin) file might look like this:


~~~ ruby
# my_template.rb
project :test => :shoulda, :orm => :activerecord
generate :model, "post title:string body:text"
generate :controller, "posts get:index get:new post:new"
generate :migration, "AddEmailToUser email:string"
require_dependencies 'nokogiri'

git :init
git :add, "."
git :commit, "initial commit"

inject_into_file "app/models/post.rb","#Hello", :after => "end\n"
rake "ar:create ar:migrate"
initializer :test, "# Example"

app :testapp do
  generate :controller, "users get:index"
end
git :add, "."
git :commit, "second commit"
~~~


Keep in mind that the template file is pure Ruby and has full access to
[all available thor actions](https://github.com/erikhuda/thor/blob/master/lib/thor/actions.rb "thor actions").

---


## Controller Generator

Padrino provides generator support for quickly creating new controllers within
your Padrino application. Note that the controller tests are generated
specifically tailored towards the testing framework chosen during application
generation.

>
  Options|Default|Aliases|Description
  :------|:------|:------|:----------
  app|/app|-a|specify the application
  root|.|-r|specify the root destination
  namespace||-n|specify the name space of your padrino project
  layout||-l|specify the layout
  parent||-p|specify the parent
  provides| |-f|specify the formats for this controller
  destroy|false|-d|removes all generated files
{: .excerpt--small }


Very important to note that controller generators are intended primarily to work
within applications created through the Padrino application generator and that
follow Padrino conventions.

Using the controller generator is as simple as:


~~~ shell
$ padrino g controller Admin
~~~


If you want create a controller for a specified sub app you can:


~~~ shell
$ padrino g controller Admin -a my_sub_app
~~~


You can also specify desired actions to be added to your controller:


~~~ shell
$ padrino g controller Admin get:index get:new post:create
~~~


The controller generator will then construct the controller file within
`app/controllers/admin.rb` and also a controller test file at
`test/controllers/admin_controller_test.rb` according to the test framework
chosen during app generation. A default route will also be generated mapping to
name of the controller and the route name. For example:


~~~ shell
$ padrino g controller User get:index
~~~


will create a url route for `:index` mapping to `/user/index`.

You may also specify layout, parent and provides respectively:


~~~ shell
$ padrino g controller User -l global
$ padrino g controller User -p users
$ padrino g controller User -f :html,:json
~~~


You can destroy controllers that you created via the destroy option and setting
it to true. Default is false.


~~~ shell
$ padrino g controller User -d
~~~


This removes all created controller files.

---


## Model Generator

Padrino provides generator support for quickly creating new models within your
Padrino application. Note that the models (and migrations) generated are
specifically tailored towards the ORM component and testing framework chosen
during application generation.

>
  Options|Default|Aliases|Description
  :------|:------|:------|:----------
  root|.|-r|specify the root destination path
  app|.|-a|specify the application destination path
  skip\_migration|false|-s|skip migration generation
  destroy|false|-d|removes all generated files
{: .excerpt--small }

Very important to note that model generators are intended primarily to work
within applications created through the Padrino application generator and that
follow Padrino conventions. Using model generators within an existing
application not generated by Padrino will likely not work as expected.

Using the model generator is as simple as:


~~~ shell
$ padrino g model User
~~~


You can also specify desired fields to be contained within your `User` model:


~~~ shell
$ padrino g model User name:string age:integer email:string
~~~


The model generator will create multiple files within your application and based
on your ORM component. Usually the model file will generate files similar to the
following:


- Model definition file [`models/user.rb`]
- Migration declaration [`db/migrate/xxx\_create\_users.rb`]
- Model unit test file [`test/models/user_test.rb`]


You can define as many models as you would like in a Padrino application using
this generator.

You can destroy models that you created via the destroy option and setting it to
true. Default is false.


~~~ shell
$ padrino g model User -d
~~~

This remove all created model files.

---


## Migration Generator

Padrino provides generator for quickly generating new migrations to change or
manipulate the database schema. These migrations generated will be tailored
towards the ORM chosen when generating the application.

>
  Options|Default|Aliases|Description
  :------|:------|:------|:----------
  root|.|-r|specify the root destination path
  destroy|false|-d|removes all generated files
{: .excerpt--small }


Very important to note that migration generators are intended primarily to work
within applications created through the Padrino application generator and that
follow Padrino conventions. Using migration generators within an existing
application not generated by Padrino will likely not work as expected.

Using the migration generator is as simple as:


~~~ shell
$ padrino g migration AddFieldsToUsers
$ padrino g migration RemoveFieldsFromUsers
~~~


You can also specify desired columns to be added to the migration file:


~~~ shell
$ padrino g migration AddFieldsToUsers last_login:datetime crypted_password:string
$ padrino g migration RemoveFieldsFromUsers password:string ip_address:string
~~~


The migration generator will then construct the migration file according to your
ORM component chosen within `db/migrate/xxx_add_fields_to_users.rb` including
the columns specified in the command.

You can destroy migrations that you created via the destroy option and setting
it to true. Default is false.


~~~ shell
$ padrino g migration AddFieldsToUsers -d
~~~


This removes the migration file.

---


## Mailer Generator

Padrino provides generator support for quickly creating new mailers within your
Padrino application.

>
  Options|Default|Aliases|Description
  :------|:------|:------|:----------
  app|nil|-n|specify the application
  root|.|-r|specify the root destination path
  namespace||-n|specify the name space of your padrino project
  destroy|false|-d|removes all generated files
{: .excerpt--small }


Very important to note that mailer generators are intended primarily to work
within applications created through the Padrino application generator and that
follow Padrino conventions.

Using the mailer generator is as simple as:


~~~ shell
$ padrino g mailer UserNotifier
~~~


If you want create a mailer for a specified sub app you can:


~~~ shell
$ padrino g mailer UserNotifier -a my_sub_app
~~~


You can also specify desired delivery actions to be added to the mailer:


~~~ shell
$ padrino g mailer UserNotifier confirm_account welcome inactive_account
~~~


The mailer generator will then construct the mailer file within
`app/mailers/user_notifier.rb`

You can destroy mailer that you created via the destroy option and setting it to
true. Default is false.


~~~ shell
$ padrino g mailer UserNotifer -d
~~~


This remove all created mailer files.

---


## Sub App Generator

Unlike other Ruby frameworks, Padrino is principally designed for mounting
multiple apps at the same time.

>
  Options|Default|Aliases|Description
  :------|:------|:------|:----------
  tiny|false|-i|generate tiny app skeleton
  root|.|-r|specify the root destination path
  destroy|false|-d|removes all generated files
{: .excerpt--small }


First you need to create a project:


~~~ shell
$ padrino g project demo_project
$ cd demo_project
~~~


Now you are in `demo_project` and you can create your apps:


~~~ shell
$ padrino g app one
$ padrino g app two
~~~

By default these apps are mounted under:


- `/one`
- `/two`


but you can edit `config/apps.rb` and change it.

And create controllers:


~~~ shell
$ padrino g controller base --app foo # This will be created for app Foo
$ padrino g controller base           # This will be created for Core app
$ padrino g controller base --app bar # This will be created for app Bar
~~~


Or mailers:


~~~ shell
$ padrino g mailer registration --app foo  # This will be created for app Foo
$ padrino g mailer registration            # This will be created for Core app
$ padrino g mailer registration --app bar  # This will be created for app Bar
~~~

---


## Tiny Skeleton Generator

Both the Project Generator and Sub App Generator allow you to create an even
smaller project skeleton. Instead of the default skeleton, the tiny option
removes the need for a controllers, helpers, and mailers folder and instead
generates `controllers.rb`, `helpers.rb`, and `mailers.rb` in its place.

To use the tiny skeleton generator for project run:


~~~ shell
$ padrino g project tiny_app -d mongoid --tiny
~~~


To use the tiny skeleton generator for app run in your project:


~~~ shell
$ padrino g app tiny_app --tiny
~~~

---


## Admin Generator

Padrino also comes with a built-in admin dashboard. To generate the admin
application in your project:

>
  Options|Default|Aliases|Description
  :------|:------|:------|:----------
  admin_name|admin|-a|allows you to specify the admin app’s name
  admin_model|"Account"|-m|specify the name of model for access controlling
  root|.|-r|specify the root destination path
  theme|default|none|generate admin app with theme
  skip\_migration|false|-s|skip migration generation
  renderer||-e|the default value is a renderer used in the main app
  destroy|false|-d|removes all generated files
{: .excerpt--small }


~~~ shell
$ padrino g admin
~~~


You can specify the theme to use for your admin application using the `theme`
flag:


~~~ shell
$ padrino g admin --theme blue
~~~


The available themes are: `amro`, `bec`, `bec-green`, `blue`, `default`,
`djime-cerulean`, `kathleene`, `olive`, `orange`, `reidb-greenish`, `ruby`,
`warehouse`.

This will generate the admin application and mount this at `/admin`. For more
information, check out the
[Admin Guide](/guides/padrino-admin "Admin Guide").

---


## Component Generator

The available components and their default options are same as the Project
Generator.

>
  Options|Default|Aliases|Description
  :------|:------|:------|:----------
  root|.|-r|the root destination path for the project
  adapter|sqlite|-a|specify orm db adapter (mysql, sqlite, postgres)
{: .excerpt--small }


### Some examples:

Show help and selected components:


~~~ shell
$ padrino g component
~~~


Add to `minirecord` with `mysql` and `rspec` in your project:


~~~ shell
$ padrino g component -d minirecord -a mysql2 -t rspec
~~~

---


## Task Generator

Padrino provides generator for quickly generating new task for your app.

>
  Options|Default|Aliases|Description
  root|.|-r|the root destination path for the project
  description|nil|-d|specify the description of your application task
  namespace|nil|-n|specify the namespace of your application task
{: .excerpt--small }


### Some examples:

Show help:


~~~ shell
$ padrino g task
~~~


Using the task generator is as simple as:

~~~ shell
$ padrino g task foo
~~~


Generate the task file with namespace and description options:


~~~ shell
$ padrino g task bar --namespace=sample --description="This\ is\ a\ sample"
~~~


[Next Section &ndash; Development Commands](/guides/development-commands){: .button}
{: .excerpt}

