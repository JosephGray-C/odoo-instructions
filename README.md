# odoo-instructions

- First and most important update and upgrade linux packages:
    - sudo apt update && sudo apt upgrade

- Install PostgreSQL:
    - sudo apt install postgresql postgresql-contrib

- Start PostgreSQL service: PostgreSQL should start automatically, but if not:
    - sudo systemctl start postgresql

- Enable PostgreSQL to start on boot:
    - sudo systemctl enable postgresql

- Verify installation: Check the PostgreSQL version:
    - psql –version

- Access the PostgreSQL shell: 
    - sudo -i -u postgres (Switch to the PostgreSQL user) 
    - psql

- Create a super user for odoo for the DB:
	- CREATE USER odoo WITH SUPERUSER CREATEDB CREATEROLE PASSWORD 'tu_contraseña';
    - \q (to quit psql)

- Exit the postgres user:
    - exit

- Now, cd into the opt directory and clone the odoo git repository:
    - cd opt/odoo
    - sudo git clone https://github.com/odoo/odoo.git

- Install odoo system dependencies:
    - sudo apt-get install -y python3 python3-dev python3-pip python3-venv python3-virtualenv libpq-dev libxml2-dev libxslt1-dev zlib1g-dev libsasl2-dev libldap2-dev libssl-dev libjpeg-dev libjpeg8-dev libfreetype6-dev liblcms2-dev libwebp-dev libtiff5-dev libopenjp2-7-dev libharfbuzz-dev libfribidi-dev libxcb1-dev libx11-dev xfonts-75dpi xfonts-base wkhtmltopdf

- Create a odoo user and set him a password:
    - sudo useradd -d /opt/odoo -U -r -s /bin/bash odoo
    - sudo passwd odoo (I think this user doesnt even need a password)
    - sudo chown -R odoo:odoo /opt/odoo

- venv and requiriments setup (If u use sudo to install something it will install it for all the system but if u use pip while the venv is active it will be installed in the venv enviroment):
	- su - odoo
    - python3 -m venv odoo-venv
	- source odoo-venv/bin/activate
    - pip install --upgrade pip Wheel
    - pip install -r /opt/odoo/odoo/requirements.txt
    - exit (now u go back to the main user or the user with sudo privilages to finish the configuration)

- Create de .conf file in the etc directory:
    - cd etc
    - sudo nano odoo.conf
    - put the following inside:
        -   
[options]
db_host = False
db_port = False
db_user = odoo
db_password = False
addons_path = /opt/odoo/odoo/addons

- Now we have create a odoo service file:
    - sudo nano /etc/systemd/system/odoo.service
    - put the following inside: 
        -   
[Unit]
Description=Odoo ERP
Requires=postgresql.service
After=network.target postgresql.service

[Service]
Type=simple
User=odoo
Group=odoo
ExecStart=/opt/odoo-venv/bin/python3 /opt/odoo/odoo-bin -c /etc/odoo.conf
StandardOutput=journal+console

[Install]
WantedBy=multi-user.target

- Now we start and enable odoo:
    - sudo systemctl daemon-reload
    - sudo systemctl start odoo
    - sudo systemctl enable odoo 
