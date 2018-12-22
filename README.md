# ND Linux Server Configuration: AWBooks Deployment
### For Full Stack Web Developer Nanodegree Program @ [Udacity](http://www.udacity.com)

This is my submission for Udacity's Linux Server Configuration project.

This readme contains some brief explanations regarding the configurations applied and software installed on our Linux web server.

## Connection Info

Our Linux server is hosted on an Amazon Lightsail VPS, as instructed. The project reviewer has been assigned a special user called `grader` which can access our server using SSH.

The public IP address of our server is : `35.158.120.152`

The reviewer can access the server through SSH on port `2200` using the following command :

```$ ssh grader@35.158.120.152 -p 2200 -i ~/.ssh/grader_key```

The SSH private key for `grader` will be submitted to the reviewer through the "Notes to Reviewer" field.

The hosted application (AWBooks) can be accessed through this URL : [`http://awbooks.ml`](http://awbooks.ml) which simply redirects the user to the following address : [`http://35.158.120.152.xip.io`](http://35.158.120.152.xip.io)

We had to use `xip.io` service in order to enable Google OAuth.

## Server Configuration

#### Our VPS runs the Ubuntu 16.04.5(Xenial) x64 Linux distribution, configured as follows :

- **Firewall** : allow incoming connections only through ports 80 `http`, 123 `ntp` and 2200 custom `ssh` port (we didn't rely on `ufw` since Lightsail provides a custom firewall)
- **SSH** : in `/etc/ssh/sshd_config` changed the default `ssh` port to 2200
- **Users** : created a new user `grader` and granted him sudo ability by adding the file : `grader` to `/etc/sudoers.d/` with permissions : `grader ALL=(ALL) NOPASSWD:ALL`
- **Access control** : generated SSH key pairs (`ssh-keygen`) for `grader` locally and transferred the public key to our server.
- **Access control** : in `/etc/ssh/sshd_config` 
    - Disallow root login : `PermitRootLogin no` 
    - Enforce key based authentication : `PasswordAuthentication no`
    - Users whitelist : `AllowUsers ubuntu grader`
- Updated pre-installed software packages : `sudo apt-get update && apt-get upgrade`

#### Installed software :

- finger : View user info. `sudo apt-get install finger`
- pip3 : Python 3 package management software. `sudo apt-get install python3-pip`
- pipenv : Our app relies on pipenv for dependency management. `pip3 install pipenv`
- postgresql : Database system used by our app. `sudo apt-get install postgresql`
    - Configured `postgres` user with password `awbooks`
    - Created a new database for our app : `awbooks_db`
- apache2 : The HTTP server for our app. `sudo apt-get install apache2`
- libapache2-mod-wsgi-py3 : Apache mod for allowing request handling by our Python 3 WSGI-compliant app. `sudo apt-get install libapache2-mod-wsgi-py3`

#### Web App configurations : 

- Disabled default apache2 site : `sudo a2dissite 000-default`
- Created new site configuration : `catalog.conf` in `/etc/apache2/sites-available/` with the following configurations :
    ```
    <VirtualHost *:80>

        ServerAdmin webmaster@localhost
        WSGIDaemonProcess catalog python-home=/home/ubuntu/.local/share/virtualenvs/catalog-d1_JKkpO/ user=ubuntu group=ubuntu
        WSGIProcessGroup catalog
        WSGIApplicationGroup %{GLOBAL}

        WSGIScriptAlias / /var/www/catalog/catalog.wsgi

        <Directory /var/www/catalog>
                Require all granted
        </Directory>

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

    </VirtualHost>
    ```
    Since our app uses a virtual environment, we must include the path to it in `python-home`
- Enable new site : `sudo a2ensite catalog`

## Third-Party resources

The following resources were used to complete this project.

- Amazon Lightsail - [Link](https://lightsail.aws.amazon.com)
- xip.io - [Link](http://xip.io)
- Freenom - [Link](http://freenom.com)
- mod_wsgi documentation - [Link](https://modwsgi.readthedocs.io/en/develop/index.html)
- Flask documentation - [Link](http://flask.pocoo.org/docs/1.0/)
- Visual Studio Code - [Link](https://code.visualstudio.com/)
- Terminus *(recommended)* - [Link](https://eugeny.github.io/terminus/)
- StackOverflow, Google and Github
