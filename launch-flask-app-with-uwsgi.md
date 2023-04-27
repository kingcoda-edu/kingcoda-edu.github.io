# Launch Flask App with uWSGI on Apache2

Install the required things.

```sh
sudo apt update
sudo apt upgrade -y
sudo apt install apache2 uwsgi libapache2-mod-wsgi-py3 libapache2-mod-ssl
```

Create Virtual Host:

Under the following folder `/etc/apache2/sites-enabled/`
Create a file called `myapp.conf`

Make sure it ends with `.conf`

```conf
<VirtualHost *:80>
ServerName myapp.local

WSGIDaemonProcess myapp python-home=/path/to/virtualenv python-path=/path/to/myapp

<Directory /path/to/myapp>
WSGIProcessGroup myapp
WSGIApplicationGroup %{GLOBAL}
Require all granted
</Directory>

ProxyPass / uwsgi://127.0.0.1:8000/
ProxyPassReverse / uwsgi://127.0.0.1:8000/

</VirtualHost>
```

In your `app.py`

```py
.
.
if __name__ == '__main__':
	app.run()

def create_app():
	return app

uwsgi_app = create_app()
```

Create a uwsgi init file:

Go to `/path/to/myapp`

Create a file `uwsgi.ini`
Enter the following:

```ini
[uwsgi]
module = app
master = true
processes = 5
vacuum = true
chmod-socket = 660
socket = /tmp/myapp.sock
die-on-term = true
env = PATH=/path/to/virtualenv/bin
chdir = /path/to/app
callable = app
```
Now run it:

```sh
uwsgi --ini uwsgi.ini
```
