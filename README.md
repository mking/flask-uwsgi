Flask uWSGI
===
This tutorial shows you to set up a simple Flask app with uWSGI + Nginx.

With this tutorial, uWSGI is wrapped in an Upstart service, and uWSGI's files (UNIX sockets, logs, config) are stored in standard Ubuntu locations (/var/run, /var/log, /etc). Note that each deployed app should have its own Upstart service (and related file locations).

Setting up the Python environment
---
First, install the following packages.
    
    sudo apt-get install build-essential python-dev python-pip

`build-essential` and `python-dev` are used to compile C extensions. Pip is used to install system commands.

Next, install the following Pip packages.

    sudo pip install virtualenv uwsgi 

I use the system Pip for non-app-specific tools, while I use Virtualenv for app-specific packages.

Where possible, I use the Pip version over the distro's binary version. There are a few reasons for this:

- uWSGI recommends that you do not use the distro's binary package.
- It is not a common practice to use the distro's binary package. You will find
  less help in the wild for doing this.

Next, create the Virtualenv for this tutorial.

    cd flask-uwsgi
    
    virtualenv env
    source env/bin/activate

    pip install flask

Next, let's check that we can reach the Flask app. Run the Flask app using wsgi. 
    uwsgi --http 0.0.0.0:8080 --home env --wsgi-file flask_uwsgi.py --callable app --master

Next, visit http://ubuntu.local:8080. (Throughout this tutorial, use your machine's domain name instead of ubuntu.local.) You should see "Hello from Flask!".

Creating an Upstart service for uWSGI
---
First, set up the directories the service will use.

    # Create a directory for the UNIX sockets
    sudo mkdir /var/run/flask-uwsgi
    chown www-data:www-data /var/run/flask-uwsgi

    # Create a directory for the logs
    sudo mkdir /var/log/flask-uwsgi
    chown www-data:www-data /var/log/flask-uwsgi

    # Create a directory for the configs
    sudo mkdir /etc/flask-uwsgi

Next, create the init script.

- Copy the init script template to /etc/init/flask-uwsgi.conf.
- Replace /path/to/flask-uwsgi with the path to this directory.

Next, create the config file.

- Copy the config template to /etc/flask-uwsgi/flask-uwsgi.ini.
- Set uid and gid to the numeric uid and gid for www-data.

Next, start uWSGI.

    sudo service flask-uwsgi start

Next, let's check that uWSGI is running.

    sudo less /var/log/flask-uwsgi/flask-uwsgi.log

You should see messages from uWSGI.

Connecting uWSGI to Nginx
---
First, install Nginx (if you haven't already).

    sudo apt-get install nginx    

Next, create the Nginx config.

- Copy the config template to /etc/nginx/sites-available/flask-uwsgi.
- Replace ubuntu.local with your machine's name.

Next, enable the config.

    sudo ln -sf /etc/nginx/sites-available/flask-uwsgi /etc/nginx/sites-enabled
    sudo service nginx reload

Next, check that the app is reachable through Nginx. Visit http://flask-uwsgi.ubuntu.local.

If you make a change to the app (or to the uWSGI config), reload uWSGI to pick up the changes.

    sudo service flask-uwsgi reload

This sends the HUP signal to uWSGI.

References
---
- [uWSGI Quickstart](http://uwsgi-docs.readthedocs.org/en/latest/WSGIquickstart.html)
- [uWSGI Configuration](http://uwsgi-docs.readthedocs.org/en/latest/Configuration.html)
- [uWSGI Upstart](http://uwsgi-docs.readthedocs.org/en/latest/Upstart.html)
