## OK Nodes

OK Nodes is a crawler that attempts to map out all Groestlcoin nodes for mainnet and testnet

A flask web server is included to display the data.

### Usage
```

# Install packages
sudo apt-get install python3-pip python3-dev nginx

# Clone repository
git clone https://github.com/Groestlcoin/oknodes /oknodes
cd /oknodes

# psycopg2-binary is required for postgres support
# uwsgi is required for nginx/apache deployment
pip install -r requirements.txt

# Setup geoip database
cd geoip && ./update.sh && cd ..

# Run crawler in loop
python3.7 crawler.py --seed --crawl --dump
screen
python3.7 crawler.py --seed --crawl --dump --loop
ctrl+A and ctrl+D

# Enable https
sudo apt-get install software-properties-common
sudo add-apt-repository ppa:certbot/certbot
sudo apt-get update
sudo apt-get install certbot
certbot certonly -d nodes.okcash.org

# Change the project directory ownership to www-data
chown -R www-data.www-data /oknodes

# Copy uWSGI startup file
cp oknodes.service /etc/systemd/system/oknodes.service

# Start uWSGI
sudo systemctl start oknodes

# Check the status
sudo systemctl status oknodes

# You should be able to see the socket with
ls /oknodes/oknodes.sock

# Enable it on startup
sudo systemctl enable oknodes

# Copy Nginx file
cp oknodes /etc/nginx/sites-available/

# Add symbolic link for site-enabled
ln -s /etc/nginx/sites-available/oknodes /etc/nginx/sites-enabled

# Remove the default configuration of Nginx
rm /etc/nginx/sites-enabled/default

# Restart nginx
systemctl restart nginx

```

The `--seed` parameter is only needed for the first run. It will hit all the DNS seeds specified in the config file, as well as all individual seeder nodes (if applicable)

The `--crawl` parameter iterates through all known nodes and stores them in the specified database

The `--dump` parameter writes all data to disk in json, csv, and txt format for ingestion by the webserver

IPv6 Nodes will only be reachable if you have IPv6 Routing available. To set up IPv6 routing on an AWS deployment see [here](https://www.dogsbody.com/blog/setting-up-ipv6-on-your-ec2/)

Onion Nodes will only be reachable if you have a Tor server running (`apt install tor`)

### Deployment
The crawler is best run via cron jobs, `--dump` instances should be scheduled separately from `--crawl` jobs.

`flock` should be used to prevent multiple instances from running concurrently
