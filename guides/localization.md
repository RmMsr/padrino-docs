---
date: 2010-03-01
update: 2015-12-15
author: Nathan
email: nesquena@gmail.com
title: Localization
sidebar: 'guides/sidebar'
---

# Localization

Padrino supports full localization in:


- padrino-core (date formats, time formats etc ...)
- padrino-admin (admin language, orm fields, orm errors, etc ...)
- padrino-helpers (currency, percentage, precision, duration etc ...)


At the moment we support the following list of languages:


- Czech
- Danish
- German
- English
- Spanish
- French
- Italian
- Dutch
- Norwegian
- Russian
- Polish
- Brazilian
- Turkish
- Ukrainian
- Traditional Chinese
- Simplified Chinese
- Japanese

---


## Provide your translations

Download and translate these files:

- [padrino-core.yml](https://raw.github.com/padrino/padrino-framework/master/padrino-support/lib/padrino-support/locale/en.yml)
- [padrino-admin.yml](http://raw.github.com/padrino/padrino-framework/master/padrino-admin/lib/padrino-admin/locale/admin/en.yml)
- [padrino-admin-orm.yml](http://raw.github.com/padrino/padrino-framework/master/padrino-admin/lib/padrino-admin/locale/orm/en.yml)
- [padrino-helper.yml](http://raw.github.com/padrino/padrino-framework/master/padrino-helpers/lib/padrino-helpers/locale/en.yml)

zip your files and send it to [padrinorb@gmail.com](mailto:padrinorb@gmail.org)

---


## How to localize your app

The first thing that you need to do is to set your locale by appending it to
`boot.rb`:


~~~ ruby
# config/boot.rb
Padrino.before_load do
  I18n.locale = :de
end
~~~


By default Padrino will search for all `.yml` or `.rb` files located in
`app/locale`; as an example try to add the following to your
`app/locale/de.yml`:


~~~ yml
de:
  foo: Bar
~~~


in your view or controller or wherever you prefer add:


~~~ ruby
I18n.t("foo")
~~~


you will get:

    => "Bar"

---


## Translate Models (ActiveRecord)

Translating models via Padrino requires few seconds thanks to a built-in rake task!

Assuming the following Account model:


~~~ ruby
create_table :accounts do |t|
  t.string :surname
  t.string :name
  t.string :email
  t.string :salt
  t.string :crypted_password
  t.string :role
end
~~~


add this to your `boot.rb` (or anywhere else):


~~~ ruby
Padrino.before_load do
  I18n.locale = :it
end
~~~


run padrino rake task for localizing your model:


~~~ shell
padrino rake ar:translate
~~~


a new `it.yml` file will be created into `/app/locale/models/account/it.yml` with
the following:


~~~ yml
it:
  models:
    account:
      name: Account
      attributes:
        id: Id
        name: Name
        surname: Surname
        email: Email
        salt: Salt
        crypted_password: Crypted password
        role: Role
~~~


you can now edit your generated `it.yml` file to reflect your current locale
(Italian):


~~~ yml
it:
  models:
    account:
      name: Account
      attributes:
        id: Id
        name: Nome
        surname: Cognome
        email: Email
        salt: Salt
        crypted_password: Crypted password
        role: Role
~~~


padrino-admin will now use your newly created yml file for translating the
column names of grids, forms, error\_messages etc ...

---


## Bonus

Using *form\_builder* like:

~~~ haml
-form_for :account, url(:accounts_create, :format => :js), :remote => true do |f|
  %table
    %tr
      %td=f.label :name
      %td=f.text_field :name
    %tr
      %td=f.label :surname
      %td=f.text_field :surname
    %tr
      %td=f.label :role
      %td=f.select :role, :options => access_control.roles
~~~

the tag **label** automatically translates for **you** the field name!

[Next Section &ndash; Extensions](/guides/extensions){: .button}
{: .excerpt}

