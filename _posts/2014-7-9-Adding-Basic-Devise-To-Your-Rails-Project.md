---
layout: post
title: Adding Basic Devise to your Rails Project
---

I was playing with Rails this weekend and set up Devise, thought I would
do a quick note about how I did the basics for authorization!

## Install [Devise](https://github.com/plataformatec/devise)
Add the below line to your Gemfile:

```
gem 'devise'
```

From your terminal:

```
bundle install
```

## Run Generators
From your terminal:

```
rails generate devise:install
```

The above generator command will install an initializer which describes ALL of Devise's configuration options. It is super imporant to stop here and look around before moving on, as you want to understand what is happening at all angles.

Mostly the warnings I saw were for routing and views, again reading
through these was helpful to know what was going on before moving to the
next step.

Now we're at the model phase, so we've seen what's happened with our views and now we're ready to add devise to our Models, hooray!

```
rails generate devise MODEL
```

We're going to replace MODEL with the name we're using for our users, in my case it's User, but it could be Admin/Subscribers/Memberships, whatever works best for your project. This command creates a model and will create it with all the default Devise modules. This also puts something in your config/routes to point to the Devise controller. Again, at this point, stop, take a moment, look around and make sure everything is working the way you thought it would before moving to the next step. Devise does a lot for you under the hood, so it's good to understand it as best you can.

After ensuring everything is all set, check the MODEL you created for any additional configuration options you might want to add, like confirmable or lockable. (Devise)[https://github.com/plataformatec/devise] has great documentation to follow if you want to go further with this.

- run command

```
rake:db:migrate
```

Now we need to set up the default URL for the Devise mailer in each environment. Here is a possible configuration for your ```config/environments/development.rb``` :

```
config.action_mailer.default_url_options = { host: 'localhost', port: 3000 }
```

Restart your server.

## Controller filters and helpers

Devise creates some helpers to use inside our controllers and views. To set up a controller with user authentication, just add this before_action (whatever you named your model subscriber, member, user) We'll use User for this example :

```
before_action :authenticate_user!
```

To verify if a user is signed in, use the following helper:

```
user_signed_in?
```

I used the above in my layout view so I stopped users on all pages
before they came to my site to login. However, pending on your
application, you may want to only stop this on specific pages, so be
sure to work that in.

For the current signed-in user, this helper is available:

```
current_user
```

You can access the session for this scope:

```
user_session
```

After signing in a user, confirming the account or updating the password, Devise will look for a scoped root path to redirect to. For instance, when using a :user resource, the user_root_path will be used if it exists; otherwise, the default root_path will be used. This means that you need to set the root inside your routes:

```
root to: "home#index"
```

## Configuring Models
Specific to your application, review the User model and devise modules for which suites you best. Ie, ```:registerable``` for allowing your users to sign up on their own.

## Generate Views
If you want to boostrap-ify your view, you can use the following command:
From your terminal:

```
rails generate devise:views
```

The above creates a directory in your views for devise with confirmations, mailer, passwords, registrations, sessions, shared, unlocks. To limit this generate to only the necessities, flag ```-V``` with the models you want.

Example usage:

```rails g devise:views -V confirmations registrations```

There are a lot more configuration options and things you can do with
Devise, so be sure to check (Devise)[https://github.com/plataformatec/devise] for additonal options!
