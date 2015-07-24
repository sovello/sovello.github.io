Deploying a Django App on Heroku
=================

#### Local app settings
This guide assumes you run _`django-admin startproject myapp`_ to start your project and that you have a directory structure looking like this:
<pre>
myapp
  manage.py
  myapp
    \__init\__.py
    heroku_settings.py
    urls.py
    settings.py
    wsgi.py
</pre>
##### You will need to have the following files for heroku
1. <code>requirements.txt</code>
  * This will have the normal requirements
2. <code>dev_requirements.txt</code>
  * It is advisable to separate development requirements like django toolbar from production requirements
3. <code>Procfile</code>
  *  A `Procfile` is a text file placed in the root directory of your application, that lists the process types in an application. 
    *  Each process type is a declaration of a command that is executed when a dyno of that process type is started.
    *  On heroku, all language and frameworks on the Cedar stack declare a `web` process type, which starts the application server
    *  e.g. __web: gunicorn PATH.TO.wsgi --log-file -__
  * The Procfile directs the server to the wsgi. In this file you need to set the path to wsgi module, which means in the traditional way, if you are running `manage.py` the traditional way then you'll have have it like _`web: gunicorn myapp.myapp.wsgi`_. 
4. <code>runtime.txt</code>
  * This file is for specifying the Python runtime you are going to use for your app.
    * e.g. `python-2.7.10` if your application uses Python version 2.7.10
5. `production_settings.py` [take a look here](https://github.com/sovello/statracker/blob/master/stattracker/heroku_settings.py) for the structure.
  * This file can assume any other name, what matters you need to make it available to the settings module. Place it anywher but advisable to put it together with the default _settings.py_ module
6. The rest of the configurations is just normal except you will need to have a file 

#### Heroku Configurations
Using these configurations, we need to set some global variables for the app and heroku to read.
* `heroku config:set DJANGO_SETTINGS_MODULE=APPNAME.production_settings`
* `heroku config:set PYTHONPATH=APPNAME`
* `heroku config:set SECRET_KEY=$(date | md5 | base64)`<sup>1</sup>

* You will have to have a working `git` branch. (Not necessarily on launchpad)
  * So, if you have not you have to
    * `git init`
    * `git add .`
    * `git commit . m 'Commit message'`
* To host an app on heroku you have to have an account. So assuming you have an account, you need to login first
  * `heroku login`
* Then create the heroku application
  * `heroku create APPNAME`
* Push your code up to heroku
  * `git push heroku master`
* Relax while code is being pushed. Mind you: watch for any errors/warnings during the process

One the code has been pushed, you may need to make sure the app is up and running by running <pre>`heroku ps:scale web=1`</pre>

If all is OK, access your app from the browser with the address <pre>`APPNAME.heroku.com`</pre>.

##### WARNINGS:
When pushing to launchpad, you may face a problem if you have more than one app frameworks e.g. Node and Django (Python). Heroku will identify them all and will pick one randomly to install by default.

Heroku uses the ___buildpacks___ concept to solve this challenge. You will need to add all the buildpacks so that when you push your app to heroku it installs them all.
You use the command:
<pre>heroku config:add BUILDPACK_URL=https://github.com/APPNAME/heroku-buildpack-FRAMEWORK\_NAME</pre>

*FRAMEWORK_NAME* can be either of node, python, etc.


DATABASES
============
Postgresql is like the default version of RDBMS well supported by heroku. You have to subscribe for advanced use, but you get a ___free 10,000-rows limited___ database.

To create a database for your app:

1. Go to your heroku account dashboard and click to view dashboard details for the app you want to create the database for.

2. Under __Resources__ click on the _button with a plus_ and search for Postgres. You should select Heroku Postgres from the auto-suggest

3. Now that you have Postgres under addons, select the package based on your subscription plan. (But here we are using the default _Hobby Dev_ instance. Then click ___Save___ button just to above to the right of addons heading.

4. Click on Heroku Postgres now and you should be taken to a page that lists all the database. [Sincerely, I can't explain the magic here, but this process create a database and ties it to your application.]

5. To confirm the database created magically above, from the active app dashboard, click on _Settings_ then under Config Variables click Reveal Config Vars and you should see DATABASE_URL set.

6. Now that you have the DATABASE URL, it is time to migrate your __local__ database to heroku. Here is how
7. Run
  * `heroku pg:push *LOCAL_DB_NAME* DATABASE_URL --app APPNAME`
   
    ___Note:___ 
      - Don't try to replace DATABASE_URL with anything
      - *LOCAL_DB_NAME* is the name of database that you are hosting locally and you want to push up to launchpad
      - I faced issues pushing the database when the Postgresql user was different from the operating system user. So, it maybe better to make sure the default Postgresql user is the same as the loged in OS user.

      
#### NOTES:
* Make sure to initialize the project with a virtual environment
* Everytime you make a change in your code or whatereve
  * Run 
    * `git commit -am 'message'`
  * Then
    * `git push heroku master`
* To access the log file: `heroku logs`
* To run django commands: `heroku run python APPNAME/manage.py COMMAND_NAME`
  * e.g. _`heroku run python statracker/manage.py migrate`_
* To run any other commands: `heroku run COMMAND_NAME`
* The procedures will be the same for any app you want to deploy to Heroku. Don't use anything that is Python/Django Specific from this doc.

<sup>1</sup><sub><sup>Run this so we avoid having the SECRET_KEY value in the settings file which we may commit to github which makes it public.</sup></sub>
