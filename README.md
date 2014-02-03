Flask uWSGI
===
This tutorial shows you to set up a simple Flask app with uWSGI + Nginx.
- The Flask app is installed in a Virtualenv.
- The uWSGI instance is an Upstart service.
- The uWSGI instance's files (UNIX sockets, logs, config) are stored in 
  standard Ubuntu locations (/var/run, /var/log, /etc).

Python
---
    # Install Python dev packages (for compiling C extensions)
    sudo apt-get install build-essential python-dev

    # Install Pip for system-wide commands
    sudo apt-get install python-pip

    # Install uWSGI as a system-wide command
    # (uWSGI does not recommend the use of the distro's binary package.)
    sudo pip install uwsgi
    
Virtualenv
---
    # Install Virtualenv
    sudo apt-get install python-virtualenv

    # Create the Virtualenv
    virtualenv env
    source env/bin/activate

Flask
---
    # Install Flask in the Virtualenv
    pip install flask
    
    # flask_uwsgi.py is the sample app.

Upstart
---
    # Create a directory for the UNIX sockets
    sudo mkdir /var/run/flask-uwsgi
    chown www-data:www-data /var/run/flask-uwsgi

    # Create a directory for the logs
    sudo mkdir /var/log/flask-uwsgi
    chown www-data:www-data /var/log/flask-uwsgi

    # Based off this repository's flask-uwsgi.ini,
    # create /etc/flask-uwsgi/flask-uwsgi.ini.

    # Based off this repository's flask-uwsgi.conf,
    # create /etc/init/flask-uwsgi.conf.
    # This should contain as few config settings as possible.
    # (Config changes should be made in config files, not init scripts.)
    
    # Start uWSGI
    sudo service flask-uwsgi start

Nginx
---
    # Install Nginx
    sudo apt-get install nginx

    # Based off this repository's flask-uwsgi file,
    # create /etc/nginx/sites-available/flask-uwsgi.

    # Enable config
    sudo ln -sf /etc/nginx/sites-available/flask-uwsgi /etc/nginx/sites-enabled
    sudo service nginx reload

    # Visit ubuntu.local and flask-uwsgi.ubuntu.local in a browser

    # To reload code or uWSGI config:
    sudo service flask-uwsgi reload
    # This sends the HUP signal to the uWSGI instance.

DNS Entries for Host
---
    # Simulate DNS entries (for example, if to visit a Linux guest on a Mac 
    # host)
    # /etc/hosts
    <IP> ubuntu.local flask-uwsgi.ubuntu.local

References
---
- [uWSGI Quickstart](http://uwsgi-docs.readthedocs.org/en/latest/WSGIquickstart.html)
- [uWSGI Configuration](http://uwsgi-docs.readthedocs.org/en/latest/Configuration.html)
- [uWSGI Upstart](http://uwsgi-docs.readthedocs.org/en/latest/Upstart.html)
