# Introduction
This is a guide to setup a new installation of Wagtail + CodeRed Extensions and Postgres, running in an Ubuntu 22.04 Vagrant Box. Install Vagrant and Virtualbox before proceeding.

---

## Clone Repository
Clone Github repository to your local development machine:

```
git clone git@github.com:hamishau/vagrant.wagtailcrx.git
```

## Project Name
Search for `yourapp` in all files and replace with your preferred project name.

## Provision Vagrant Box

```
vagrant up
```

## Edit Hosts

Windows Powershell: `notepad c:\Windows\System32\Drivers\etc\hosts`
Ubuntu Terminal: `sudo nano /etc/hosts`

```
192.168.56.10 yourapp.local
```
## Database Connection
Replace the `DATABASES = ...` section in `yourapp/settings/base.py` with the following:

```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'yourapp',
        'USER': 'postgres',
        'PASSWORD': 'admin',
        'HOST': 'localhost',
        'PORT': '',
    }
}
```
## Run Migrations

```
vagrant ssh -c 'cd /yourapp && python3 manage.py migrate'
```

## Create Superuser

```
vagrant ssh -c 'cd /yourapp && python3 manage.py createsuperuser'
```

## Compile CSS

```
vagrant ssh -c 'cd /yourapp && python3 manage.py sass website/static/website/src/custom.scss website/static/website/css/custom.css --watch'
```

## Start Development Server

```
vagrant ssh -c 'cd /yourapp && python3 manage.py runserver 0.0.0.0:8080'
```

Preview in browser:

```
http://yourapp.local:8080
```

---

### Post Installation
These are not required as a part of the Vagrant deployment process - just a collection of helpful commands that might be required at some point in your Vagrant box.

#### Import PostgreSQL Database
```
vagrant ssh -c 'cd /yourapp && sudo -u postgres psql yourapp < yourapp.psql'
```

#### Export PostgreSQL Database
```
vagrant ssh -c 'cd /yourapp && sudo -u postgres pg_dump yourapp > yourapp.psql'
```

#### Drop PostgreSQL Database and Create New Database
```
vagrant ssh -c 'cd /yourapp && sudo -u postgres dropdb yourapp && sudo -u postgres createdb yourapp'
```

#### Upgrade Packages
```
vagrant ssh -c 'cd /yourapp && pip3 install --upgrade -r requirements.txt'
```

#### Restart Vagrant Box
```
vagrant reload
```

#### Destroy Vagrant Box
```
vagrant destroy
```