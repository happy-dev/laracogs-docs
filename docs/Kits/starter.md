# Application Starter Kit

Laracogs provides an elegant solution for starting an application by building the most basic views, controllers, repositories, models and migrations for your application. No need to use the `php artisan make:auth` because now you can easily start your whole application with this single command:

```
php artisan laracogs:starter
```

The command will overwrite any existing files with the starter kits version of them:

## Setup

### Installation

Start a new Laravel project:

Then run the following to add Laracogs
```php
composer require "yab/laracogs"
```

Add this to the `config/app.php` in the providers array:
```php
Yab\Laracogs\LaracogsProvider::class
```

Time to publish those assets!
```php
php artisan vendor:publish --provider="Yab\Laracogs\LaracogsProvider"
```

You now have Laracogs installed. Looking to try the *Starter Kit* look below.

### Starter
In order to make use of the <u>starter kit</u> you will need to modify some files. Check out the modifications below:

Add the following to your `app/Http/Kernel.php` $routeMiddleware array.

```php
'admin' => \App\Http\Middleware\Admin::class,
```

Update the `App\User::class` in: 'config/auth.php' and 'database/factory/ModelFactory.php' to this:

```php
App\Repositories\User\User::class
```

Add the following to 'app/Providers/AuthServiceProvider.php' in the boot method

```php
$gate->define('admin', function ($user) {
    return ($user->roles->first()->name === 'admin');
});

$gate->define('team-member', function ($user, $team) {
    return ($user->teams->find($team->id));
});
```

You will want to create an sqlite memory test database in the `config/database.php`

```php
'testing' => [
    'driver'   => 'sqlite',
    'database' => ':memory:',
    'prefix'   => '',
],
```

Add the following line to the 'phpunit.xml' file
```xml
<env name="DB_CONNECTION" value="testing"/>
<env name="MAIL_DRIVER" value="log"/>
```

##### For Laravel 5.2
You will also need to set the location of the email for password reminders. (app/auth.php - at the bottom)

```php
'passwords' => [
    'users' => [
        'provider' => 'users',
        'email' => 'emails.password',
        'table' => 'password_resets',
        'expire' => 60,
    ],
],
```

##### Things to note
You may try and start quickly by testing the registration but please make sure your app's <u>email</u> is configured or it will throw an exception.
You can do this in the `.env` file easily by setting it to 'log' temporarily

```php
MAIL_DRIVER=log
```

##### Last Steps

Once you've added in all these parts you will want to run the starter command!

```php
php artisan laracogs:starter
```

Then you'll need to migrate to add in the users, user meta, roles and teams tables. The seeding is run to set the initial roles for your application.

```php
php artisan migrate --seed
```

Once you get the starter kit running you can register and login to your app. You can then you can visit the settings section of the app and set your role to admin to take full control of the app.

## What Starter Publishes

##### Controllers
Laracogs updated the basic controllers to handle things like creating an profile when a user is registered, as well as setting default return routes to `dashboard` etc. It also provides contollers for handling profile modifications and pages, team management etc. The admin controller handles the admin of users, modifying a user provided the user has the admin role.

* *app/Http/Controllers/*
    * User/PasswordController.php
    * User/SettingsController.php
    * Admin/UserController.php
    * Auth/AuthController.php
    * Auth/PasswordController.php
    * PagesController.php
    * TeamController.php

##### Middleware
Laracogs overwrites the default middleware due to changes in the redirects. It also provides the `Admin` middleware for route level protection relative to roles.

* *app/Http/Middleware/*
    * Admin.php
    * Authenticate.php
    * RedirectIfAuthenticated.php

##### Requests
There are requests provided for handling the creation of Teams and updating of all components. Here we integrate the rules required that are able to run the validations and return errors. (If you're using Laracogs FormMaker Facade then it will even handling accepting the errors and highlighting the appropriate fields.)

* *app/Http/Requests/*
    * TeamRequest.php
    * UpdateUserMetaRequest.php
    * UpdatePasswordRequest.php
    * UpdateTeamRequest.php

##### Routes
Given that there are numerous routes added to handle teams, profiles, password etc all routes are overwritten with the starter kit.

* app/Http/routes.php

##### Repositories
Repositories are build in order to handle the models and customized requests on the models. This structure encourages a SOLID approach to your code. Allowing your Models to remain clean without them getting consumed with extra methods. The repository acts as the performer of actions to the Model. We then integrate Services below which handle all the buisness logic etc which make the calls to the repositories, and finally the Controller which calls the service. Once these have been integrated please ensure you delete the `User.php` model file and ensure that you have followed the installation and config instructions.

* *app/Repositories/*
    * UserMeta/UserMeta.php
    * UserMeta/UserMetaRepository.php
    * Role/Role.php
    * Role/RoleRepository.php
    * Team/Team.php
    * Team/TeamRepository.php
    * User/User.php
    * User/UserRepository.php

##### Services
Service structure allows us to keep the buisness logic outside of the repositories, models, and controllers. This approach is best suited for apps that may wish to integrate an API down the road or other things. It also allows for a highly testable structure to the application.

* *app/Services/*
    * UserService.php
    * TeamService.php

##### Database
Please ensure that all migrations and seeds are run post installation. These seeds set the default roles available in your application.

* *database/migrations/*
    * 2015_11_30_191713_create_user_meta_table.php
    * 2015_11_30_215038_create_roles_table.php
    * 2015_11_30_215040_create_role_user_table.php
    * 2015_12_04_155900_create_teams_table.php
    * 2015_12_04_155900_create_teams_users_table.php
* *database/seeds/*
    * DatabaseSeeder.php
    * RolesTableSeeder.php
    * UserTableSeeder.php

##### Factories
Factories for each of the repositories are appended to the `database/factories/ModelFactory.php` file.

##### Views
The views consist of as little HTML as possible to perform the logical actions. These are intended to be the most basic, and all of which are intended to be modified.

* *resources/views/*
    * user/meta.blade.php
    * user/password.blade.php
    * user/settings.blade.php
    * admin/users/edit.blade.php
    * admin/users/index.blade.php
    * admin/users/invite.blade.php
    * auth/login.blade.php
    * auth/password.blade.php
    * auth/register.blade.php
    * auth/reset.blade.php
    * dashboard.blade.php
    * emails/new-user.blade.php
    * emails/password.blade.php
    * errors/404.blade.php
    * errors/503.blade.php
    * partials/errors.blade.php
    * partials/message.blade.php
    * team/create.blade.php
    * team/edit.blade.php
    * team/index.blade.php
    * team/show.blade.php

##### Tests
Laracogs starter kit provides the basic unit tests for each of its own parts. This provides some great exmaples of testing for building an application quickly.

* *tests/*
    * UserMetaRepositoryTest.php
    * UserServiceTest.php
    * TeamIntegrationTest.php
    * TeamRepositoryTest.php
    * TeamServiceTest.php
    * UserRepositoryTest.php

## After Setup

### User

The user model is expanded with Laracogs Starter Kit. It adds to the basic user model: roles, teams, and user meta. The relationships are as follows:

Meta: hasOne
Roles: belongsToMany
Team: belongsToMany

It also provides the following methods:

```
meta() // The relationship method
roles() // The relationship method
hasRole(role_name) // checks if user has role
teams() // The relationship method
isTeamMember(team_id) // checks if user is member
isTeamAdmin(team_id) // checks if user is admin level member
```

### Middleware

##### Admin
The Admin middleware acts as a tool for setting admin level permissions on the routes or controller level.

```
['middleware' => 'admin']
```

This simple addition to a route will ensure the user has access to the admin level, if not it will return them from where they came.

<script>
  (function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
  (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
  m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
  })(window,document,'script','//www.google-analytics.com/analytics.js','ga');

  ga('create', 'UA-39444410-8', 'auto');
  ga('send', 'pageview');

</script>