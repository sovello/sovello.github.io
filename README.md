Sovello Github Pages
=================

### Deploying a Django App on Heroku

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
5. `myapp/myapp/heroku_settings` [take a look here](https://github.com/sovello/statracker/blob/master/stattracker/heroku_settings.py) for the structure.
6. The rest of the configurations is just normal except you will need to have a file 
#### Heroku Configurations
