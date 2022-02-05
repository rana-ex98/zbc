 # **🐧 Linux Task**

> setting up a Virtualbox machine with Ubuntu LTS

- Created an Ubuntu EC2 instance to do the task on as it didn't make much sense to run virutalbox on a VM.

> install `apache`, `node.js` and `mongodb` (latest LTS versions)

- Apache & Mongo have been setup using Ubuntu's default package manbager `apt`.

- Used [nvm](https://github.com/nvm-sh/nvm) for installing `nodejs`, as its the most reliable source for installing and managing multiple nodejs versions (node lts as well)

  ```bash
  apt update
  apt install apache2 mongodb-server
  systemctl status apache2
  systemctl enable apache2
  systemctl status mongodb
  systemctl enable  mongodb

  curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash
  source ~/.bashrc
  nvm install --lts
  ```

> create a hello-world application in nodejs

- A hello-world app using `express.js` has been setup to run on port `3000`

  ref: https://github.com/rana-ex98/zbc/blob/main/index.js

- To `auto-restart` the application on events such as `reboot`, a systemd service is configured.

  ref: https://github.com/rana-ex98/zbc/blob/main/helloo.service

- This systemd service is configured to call a `bash` script which calls `node` to run the express.js server `index.js`.
  
  ref: https://github.com/rana-ex98/zbc/blob/main/start.sh

  ```bash
  cd ~
  git clone https://github.com/rana-ex98/zbc.git
  cd zbc

  # install dependencies
  npm install express

  # copy service file to systemd service configuration directory
  cp helloo.service /etc/systemd/system/helloo.service
  systemctl deamon-reload
  systemctl status helloo
  systemctl start helloo
  systemctl enable helloo
  ```

> create the hello-world application reachable from node with apache proxy in a virtual host

- Configured apache `virtual host` in /etc/apache2/sites-enabled/000-default.conf

  ref: https://github.com/rana-ex98/zbc/blob/main/vHost.conf

  ```bash
  cp vHost.conf /etc/apache2/sites-enabled/000-default.conf

  # Enable proxy module:
  a2enmod proxy
  a2enmod proxy_http
  a2enmod ssl

  ```

> generate and add a self-signed ssl certificate in the apache virtual host

- generated SSL in `/etc/apache2/ssl` and restart apache2 server

  ```bash

  cd /etc/apache2/ssl
  # To generate certificate
  openssl req -x509 -newkey rsa:4096 -keyout key.pem -out file.pem -sha256 -days 365

  # Since certs have now been generated, apache will be able to read the cert and key from the file location which we have configured in the virtualhost config file (/etc/apache2/sites-enabled/000-default.conf)
  # To check apache config
  apachectl configtest

  # To restart apache2 server
  systemctl restart apache2

  # curl the app via apche proxy over https
  curl -k https://localhost
  ```
