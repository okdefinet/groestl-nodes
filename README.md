## Groestl Nodes

Groestl Nodes is a crawler that attempts to map out all Groestlcoin nodes for mainnet and testnet

A flask web server is included to display the data.

### Usage
```

# Install packages (Python 3)
sudo apt-get install python3-pip python3-dev

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

# run Production Server
python3.7 app.py --prod

```

The `--seed` parameter is only needed for the first run or when adding a new network. It will hit all the DNS seeds specified in the config file, as well as all individual seeder nodes (if applicable)

The `--crawl` parameter iterates through all known nodes and stores them in the specified database

The `--dump` parameter writes all data to disk in json, csv, and txt format for ingestion by the webserver

IPv6 Nodes will only be reachable if you have IPv6 Routing available. To set up IPv6 routing on an AWS deployment see [here](https://www.dogsbody.com/blog/setting-up-ipv6-on-your-ec2/)

Onion Nodes will only be reachable if you have a Tor server running (`apt install tor`)

### Deployment
The crawler is best run via cron jobs, `--dump` instances should be scheduled separately from `--crawl` jobs.

`flock` should be used to prevent multiple instances from running concurrently
