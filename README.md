## Groestl Nodes

Groestl Nodes is a crawler that attempts to map out all Groestlcoin nodes for mainnet and testnet

A flask web server is included to display the data.

### Usage
```

# Install packages
sudo apt-get install python3-pip python3-dev nginx

#Clone repository
git clone https://github.com/Groestlcoin/groestl-nodes /groestl-nodes
cd /groestl-nodes

# psycopg2-binary is required for postgres support
# uwsgi is required for nginx/apache deployment
pip install -r requirements.txt

# setup geoip database
cd geoip && ./update.sh && cd ..

# run crawler
python3.7 crawler.py --seed --crawl --dump

# Enable https
sudo apt-get install software-properties-common
sudo add-apt-repository ppa:certbot/certbot
sudo apt-get update
sudo apt-get install certbot
certbot certonly --standalone -d nodes.groestlcoin.org

# Change the project directory ownership to www-data
chown -R www-data.www-data /groestl-nodes

# Copy uWSGI startup file
cp groestlnodes.service /etc/systemd/system/groestlnodes.service

# Start uWSGI
sudo systemctl start groestlnodes

# Check the status
sudo systemctl status groestlnodes

# You should be able to see the socket with
ls /groestl-nodes/groestlnodes.sock

# Enable it on startup
sudo systemctl enable groestlnodes

# Copy Nginx file
cp groestlnodes /etc/nginx/sites-available/

# Add symbolic link for site-enabled
ln -s /etc/nginx/sites-available/groestlnodes /etc/nginx/sites-enabled

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
