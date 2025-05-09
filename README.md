# odoo-instructions

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
    - cd opt
    - git clone https://github.com/odoo/odoo.git

- Create a odoo user and set him a password:
    - sudo useradd -d /opt/odoo -U -r -s /bin/bash odoo
    - sudo passwd odoo (I think this user doesnt even need a password)
    - sudo chown -R odoo:odoo /opt/odoo-venv

- Install odoo dependencies:
    - sudo apt-get install python3-pip
    - sudo apt install python3-virtualenv python3-venv
    - sudo apt install python3 python3-pip python3-dev python3-venv postgresql libpq-dev libxml2-dev libxslt1-dev zlib1g-dev libsasl2-dev libldap2-dev libssl-dev libjpeg-dev libjpeg8-dev libfreetype6-dev liblcms2-dev libwebp-dev libtiff5-dev libopenjp2-7-dev libharfbuzz-dev libfribidi-dev libxcb1-dev libx11-dev xfonts-75dpi xfonts-base wkhtmltopdf -y
    - (optional, may give an error) sudo apt install nodejs npm

- Install this dependencies in the venv (while active):
	- sudo python3 -m venv odoo-venv
    - sudo chown -R root:root /opt/odoo-venv (give priviliges to the root user)
	- source odoo-venv/bin/activate
    - pip install --upgrade pip Wheel
    - pip install -r /opt/odoo/requirements.txt

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
            addons_path = /opt/odoo/addons
            logfile = /var/log/odoo/odoo.log

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
            ExecStart=/opt/odoo-venv/bin/python3 /opt/odoo/odoo-bin -c /etc/odoo.conf -u base,web
            StandardOutput=journal+console

            [Install]
            WantedBy=multi-user.target

- Now we start and enable odoo:
    - sudo systemctl daemon-reload
    - sudo systemctl start odoo
    - sudo systemctl enable odoo 
