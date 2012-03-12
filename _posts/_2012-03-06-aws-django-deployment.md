### Initial deployment
sudo yum update / upgrade
sudo easy_install pip
sudo yum install nginx
sudo yum install gcc-c++
sudo yum install python-devel
sudo yum install libxml2-python libxml2-devel
sudo pip install uwsgi
sudo yum install mysql-server mysql mysql-devel

chckconfig's
mysql_secure_installation

### Setting up your environment
sudo pip install virtualenv
sudo yum install git

### Configuring the servers

nginx.conf
	include /etc/nginx/sites-enabled/*;

domain
	server {
	    listen 80;
	    server_name finantsit.com www.finantsit.com;
	    rewrite ^(.*) https://finantsit.com$1 permanent;
	}

	server {
	    listen 443;
	    server_name finantsit.com;

	    client_max_body_size 50M;

	    ssl on;
	    ssl_certificate /srv/finantsit/ssl/finantsitchain.crt;
	    ssl_certificate_key /srv/finantsit/ssl/finantsit.key;

	    access_log /srv/finantsit/log/access.log;
	    error_log /srv/finantsit/log/error.log;

	    location / {
	        uwsgi_pass 127.0.0.1:8000;
	        include /etc/nginx/uwsgi_params;
	    }

	    location /static {
	        root /srv/finantsit/app/finantsit;
	    }
	}

upstart
	description "uWSGI server for finantsit.com"

	start on runlevel [2345]
	stop on runlevel [!2345]
	respawn

	# home - is the path to our virtualenv directory
	# pythonpath - the path to our django application
	# module - the wsgi handler python script

	exec uwsgi --uid madis --ini /srv/finantsit/app/finantsit/uwsgi.ini


