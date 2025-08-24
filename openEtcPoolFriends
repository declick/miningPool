## Open Source Ethereum Mining Pool PPLNS / SOLO

### index page

![index/miners page](/screenshots/01.png?raw=true "index/miners page")

### Donations

* Donate 1% from pool fees to developers (Attention becomes automatic)


* open-etc-pool-friends wallet ETC: 0xd92fa5a9732a0aec36dc8d5a6a1305dc2d3e09e6

### Features

### Email: office.poolnode@gmail.com

### [YouTube](https://www.youtube.com/channel/UCeSEGwWB8LWtu7BM8OpH6yA).

### [hey come check out Discord with me](https://discord.gg/zdnuXm4uby). 

### [hey come check out Telegram with me](https://t.me/openetcpoolfriends).

**This pool is being further developed to provide an easy to use pool for Ethereum Classic miners. This software is functional however an optimised release of the pool is expected soon. Testing and bug submissions are welcome!**

* Support for HTTP and Stratum mining
* Detailed block stats with luck percentage and full reward
* Failover geth instances: geth high availability built in
* Separate stats for workers: can highlight timed-out workers so miners can perform maintenance of rigs
* JSON-API for stats
* New vue based UI
* Supports Ethereum Classic, Mordor, Ethereum, Ropsten, ubiq

### Building on Linux

Dependencies:

  * go >= 1.19
  * core-geth
  * redis-server >= 2.8.0
  * nodejs >= 4 LTS
  * nginx

1. Mise à jour système + dépendances

```
sudo apt-get update && sudo apt-get upgrade -y
sudo apt-get install -y git build-essential make golang redis-server nodejs npm nginx ipset rsync wget
sudo ipset create blacklist hash:ip
```

2. Installer Node.js 14 LTS et outils Node

```
curl -sL https://deb.nodesource.com/setup_14.x | sudo -E bash -
sudo apt-get install -y nodejs
sudo npm install -g bower
sudo npm install -g ember-cli@2.18
```

3. Télécharger et compiler Core-Geth

```
cd /home/<USER>
wget https://github.com/etclabscore/core-geth/archive/refs/tags/v1.12.20.tar.gz
tar -xf v1.12.20.tar.gz
cd core-geth-1.12.20
make all
```

4. Préparer dossier blockchain & wallet

```
mkdir -p /home/<USER>/classic/.ethereum
chown -R <USER>:<USER> /home/<USER>/classic
cd /home/<USER>/core-geth-1.12.20/build/bin
./geth account new --datadir /home/<USER>/classic/.ethereum/
```

# Note bien ton adresse (0x...) et le mot de passe !

5. Préparer fichier mot de passe

```
echo "ICI_TON_MOT_DE_PASSE" > /home/<USER>/.pw
chmod 600 /home/<USER>/.pw
chown <USER>:<USER> /home/<USER>/.pw
```
6. Créer le service systemd Core-Geth miner

```
sudo nano /etc/systemd/system/geth.service
```
Colle ce qui suit (modifie 0xTON_ADRESSE par l'adresse récupérée à l'étape 4 !) :

```
[Unit]
Description=Geth Ethereum Classic Miner
After=network-online.target
Wants=network-online.target

[Service]
User=<USER>
ExecStart=/home/<USER>/core-geth-1.12.20/build/bin/geth \
  --miner.threads=1 \
  --datadir /home/<USER>/classic/.ethereum/ \
  --syncmode snap \
  --cache 12000 \
  --maxpeers 150 \
  --http \
  --http.addr 0.0.0.0 \
  --http.port 8545 \
  --http.api eth,net,web3,txpool,miner,admin \
  --http.corsdomain="*" \
  --http.vhosts="*" \
  --miner.etherbase <0xTON_ADRESSE> \
  --mine \
  --password /home/<USER>/.pw \
  --allow-insecure-unlock \
  --unlock <0xTON_ADRESSE> \
  --classic \
  --snapshot=true \
  --port 30305 \
  --nat any

Restart=always
RestartSec=3
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
```

7. Activer et lancer le service

```
sudo systemctl daemon-reload
sudo systemctl enable geth
sudo systemctl start geth
sudo systemctl status geth
sudo journalctl -u geth -f
```

8. Vérifier que Core-Geth écoute

Test depuis la VM :

```
curl -X POST -H "Content-Type: application/json" \
  --data '{"jsonrpc":"2.0","method":"web3_clientVersion","params":[],"id":1}' \
  http://localhost:8545
```

9. Installer et builder open-etc-pool-friends

```
cd /home/<USER>
git clone https://github.com/yuriy0803/open-etc-pool-friends.git
cd open-etc-pool-friends
go build
```

10. Configurer le pool

    Copie api.json d’exemple, adapte : IP, ports, clés, etc.

    Adresse de daemon principale : "url": "http://127.0.0.1:8545"

Édite 
```
/home/<USER>/open-etc-pool-friends/api.json

```
avec nano ou vim.

11. Installer et builder le frontend pool

```
cd /home/<USER>/open-etc-pool-friends/www
npm install
bower install
ember install ember-truth-helpers
npm install jdenticon@2.1.0
chmod +x build.sh
./build.sh
```

Modifie config/environment.js selon ton IP/domaine.
12. Configurer nginx pour servir le site pool

Édite /etc/nginx/sites-available/default :

```
upstream api {
    server 127.0.0.1:8080;
}
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    root /home/<USER>/open-etc-pool-friends/www/dist;
    index index.html index.htm;

    server_name _;

    location / {
        try_files $uri $uri/ /index.html =404;
    }

    location /api {
        proxy_pass http://127.0.0.1:8080;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_cache_bypass $http_upgrade;
    }
}
```

Puis relance nginx :

```
sudo systemctl restart nginx
```

13. Créer le service systemd API pool

```
sudo nano /etc/systemd/system/api.service
```

Colle :

```
[Unit]
Description=Ethereum Classic Pool API
After=network-online.target

[Service]
ExecStart=/home/<USER>/open-etc-pool-friends/open-etc-pool-friends /home/<USER>/open-etc-pool-friends/api.json
User=<USER>
Restart=always
RestartSec=3

[Install]
WantedBy=multi-user.target
```

Active et démarre :

```
sudo systemctl daemon-reload
sudo systemctl enable api
sudo systemctl start api
sudo systemctl status api
sudo journalctl -u api -f
```

14. Vérifications finales

    Geth = port 8545 ouvert, mining actif, pas d’erreur unlock !

    Pool API = port 8080 ouvert, OK logs

    Nginx = http://192.168.1.30/ répond et affiche le site pool

    Firewall : ouvre les ports si besoin

```
sudo ufw allow 80
sudo ufw allow 8545
sudo ufw allow 30305
sudo ufw allow 8080
 ```
   
#### Customization

You can customize the layout using built-in web server with live reload:
```
    ember server --port 8082 --environment development
```

**Don't use built-in web server in production**.

Check out <code>www/app/templates</code> directory and edit these templates
in order to customise the frontend.

### Configuration

Configuration is actually simple, just read it twice and think twice before changing defaults.

**Don't copy config directly from this manual. Use the example config from the package,
otherwise you will get errors on start because of JSON comments.**

```javascript
{
    "threads": 4, // Number of threads used for processing
    "coin": "ETC", // Abbreviation of the supported cryptocurrency
    "name": "Ethereum-Classic", // Full name of the cryptocurrency
    "pplns": 9000, // Pay-Per-Last-N-Shares, a reward system for miners (solo "pplns": 0,)
    "network": "classic", // Network type the pool is running on mordor, classic, ethereum, ropsten or 
    // ubiq, etica, ethereumPow, ethereumFair, expanse, octaspace, canxium, universal, Zether  
    "coin-name": "etc", // Internal name of the cryptocurrency // exchange api coingecko
    "algo": "etchash", // Algorithm used for mining  etchash, ethash, ubqhash
    "proxy": {
        "enabled": true, // Indicates if the proxy is enabled
        "listen": "0.0.0.0:8888", // Address and port the proxy listens on
        "limitHeadersSize": 1024, // Maximum size of headers in bytes
        "limitBodySize": 256, // Maximum size of the body in bytes
        "behindReverseProxy": false, // Indicates if the proxy is behind a reverse proxy
        "blockRefreshInterval": "50ms", // Interval for refreshing blocks
        "stateUpdateInterval": "3s", // Interval for updating state
        "difficulty": 17179869184, // Mining difficulty
        "hashrateExpiration": "3h", // Time period after which hashrate is considered expired
        "stratumHostname": "example.org", // Hostname for Stratum connections
        "healthCheck": true, // Indicates if health checks are enabled
        "debug": true, // Indicates if debug mode is enabled
        "maxFails": 100, // Maximum number of failed attempts before closing a connection
        "stratum": {
            "enabled": true, // Indicates if Stratum is enabled
            "listen": "0.0.0.0:8008", // Address and port Stratum listens on
            "timeout": "120s", // Timeout for Stratum connections
            "maxConn": 8192, // Maximum number of concurrent connections
            "tls": false, // Indicates if TLS is used for Stratum connections
            "certFile": "/etc/letsencrypt/live/example.com/fullchain.pem", // Path to the TLS certificate file
            "keyFile": "/etc/letsencrypt/live/example.com/privkey.pem" // Path to the TLS key file
        },
        "policy": {
            "workers": 8, // Number of workers used for processing
            "resetInterval": "60m", // Interval for resetting limits
            "refreshInterval": "1m", // Interval for refreshing limits
            "blacklist_file": "/home/pool/open-etc-pool-friends/stratum_blacklist.json", // Path to the blacklist file
            "banning": {
                "enabled": true, // Indicates if IP banning is enabled
                "ipset": "blacklist", // Name of the IP set for banned IPs
                "timeout": 1800, // Timeout for banned IPs in seconds
                "invalidPercent": 30, // Percentage of invalid requests before banning
                "checkThreshold": 30, // Threshold for checking invalid requests
                "malformedLimit": 5, // Limit for malformed requests
                "fail2banCommand": "fail2ban-client" // Command to execute Fail2Ban
            },
            "limits": {
                "enabled": false, // Indicates if limits are enabled
                "limit": 30, // Maximum number of requests per minute
                "grace": "5m", // Grace period before applying limits
                "limitJump": 10 // Increase in limit upon exceeding
            }
        }
    },
    "api": {
        "enabled": true, // Indicates if the API is enabled
        "purgeOnly": false, // Indicates if the API is used only for purging
        "purgeInterval": "10m", // Interval for purging data
        "listen": "0.0.0.0:8080", // Address and port the API listens on
        "statsCollectInterval": "5s", // Interval for collecting statistics
        "hashrateWindow": "30m", // Time period for calculating hashrate
        "hashrateLargeWindow": "3h", // Longer time period for calculating hashrate
        "luckWindow": [64, 128, 256], // Time windows for calculating luck
        "payments": 30, // Number of payments to retain
        "blocks": 50, // Number of blocks to retain
        "poolCharts": "0 */20 * * * *", // Cron expression for updating pool charts
        "poolChartsNum": 74, // Number of pool charts to retain
        "minerCharts": "0 */20 * * * *", // Cron expression for updating miner charts
        "minerChartsNum": 74, // Number of miner charts to retain
        "netCharts": "0 */20 * * * *", // Cron expression for updating network charts
        "netChartsNum": 74, // Number of network charts to retain
        "shareCharts": "0 */20 * * * *", // Cron expression for updating share charts
        "shareChartsNum": 74 // Number of share charts to retain
    },
    "upstreamCheckInterval": "5s", // Interval for checking upstream connections
    "upstream": [
        {
            "name": "main", // Name of the main upstream server
            "url": "http://127.0.0.1:8545", // URL of the main upstream server connected to a Geth node
            "timeout": "10s" // Timeout for connections to the main upstream server
        },
        {
            "name": "backup", // Name of the backup upstream server
            "url": "http://127.0.0.2:8545", // URL of the backup upstream server connected to a Geth node
            "timeout": "10s" // Timeout for connections to the backup upstream server
        }
    ],
    "redis": {
        "endpoint": "127.0.0.1:6379", // Redis server endpoint
        "poolSize": 10, // Size of the Redis connection pool
        "database": 0, // Redis database number
        "password": "", // Password for the Redis connection
        "sentinelEnabled": false, // Indicates if Redis Sentinel is enabled
        "masterName": "mymaster", // Name of the Redis master
        "sentinelAddrs": [
            "127.0.0.1:26379", // Address of the first Redis Sentinel
            "127.0.0.1:26389", // Address of the second Redis Sentinel
            "127.0.0.1:26399"  // Address of the third Redis Sentinel
        ]
    },
    "exchange": {
        "enabled": true, // Indicates if the exchange service is enabled
        "name": "coingecko", // Name of the exchange service
        "url": "https://api.coingecko.com/api/v3/coins/markets?vs_currency=usd&ids=ethereum-classic", // URL for the exchange service
        "timeout": "50s", // Timeout for requests to the exchange service
        "refreshInterval": "900s" // Interval for refreshing exchange data
    },
    "unlocker": {
        "enabled": true, // Indicates if the unlocker is enabled
        "poolFee": 1.0, // Pool fee percentage
        "poolFeeAddress": "", // Address to which the pool fee is sent
        "depth": 120, // Depth of transactions considered
        "immatureDepth": 20, // Depth of immature transactions
        "keepTxFees": false, // Indicates if transaction fees are retained
        "interval": "10m", // Interval for running the unlocker
        "daemon": "http://127.0.0.1:8545", // URL of the daemon connected to a Geth node
        "timeout": "10s", // Timeout for connections to the daemon
        "isLondonHardForkEnabled": false // Indicates if the London Hard Fork is enabled
    },
    "payouts": {
        "enabled": false, // Indicates if payouts are enabled
        "requirePeers": 1, // Number of required peers for payouts
        "interval": "20m", // Interval for payouts
        "daemon": "http://127.0.0.1:8545", // URL of the daemon for payouts connected to a Geth node
        "timeout": "10s", // Timeout for connections to the daemon
        "address": "0xd92fa5a9732a0aec36dc8d5a6a1305dc2d3e09e6", // Address to which payouts are sent
        "gas": "21000", // Gas limit for payouts
        "gasPrice": "50000000000", // Gas price for payouts
        "autoGas": true, // Indicates if gas price is adjusted automatically
        "threshold": 500000000, // Threshold for payouts
        "bgsave": false, // Indicates if background saving is enabled
        "concurrentTx": 10 // Number of concurrent transactions
    },
    "newrelicEnabled": false, // Indicates if New Relic monitoring is enabled
    "newrelicName": "MyEtherProxy", // Name of the New Relic application
    "newrelicKey": "SECRET_KEY", // API key for New Relic
    "newrelicVerbose": false // Indicates if verbose logging for New Relic is enabled
}
```

If you are distributing your pool deployment to several servers or processes,
create several configs and disable unneeded modules on each server. (Advanced users)

I recommend this deployment strategy:

* Mining instance - 1x (it depends, you can run one node for EU, one for US, one for Asia)
* Unlocker and payouts instance - 1x each (strict!)
* API instance - 1x

### Notes

* Unlocking and payouts are sequential, 1st tx go, 2nd waiting for 1st to confirm and so on. You can disable that in code. Carefully read `docs/PAYOUTS.md`.
* Also, keep in mind that **unlocking and payouts will halt in case of backend or node RPC errors**. In that case check everything and restart.
* You must restart module if you see errors with the word *suspended*.
* Don't run payouts and unlocker modules as part of mining node. Create separate configs for both, launch independently and make sure you have a single instance of each module running.
* If `poolFeeAddress` is not specified all pool profit will remain on coinbase address. If it specified, make sure to periodically send some dust back required for payments.

### Mordor

To use this pool on the mordor testnet two settings require changing to "mordor"

network in your config.json (this sets backend (validation,unlocker) to mordor paramaters)
APP.Network in your www/config/environment.js (this sets the frontend to mordor paramaters)
rerun ./build.sh


### Extra) How To Secure the pool frontend with Let's Encrypt (https)

First, install the Certbot's Nginx package with apt-get

```
 sudo apt-get update
 sudo apt-get install python3-certbot-nginx
```

And then open your nginx setting file, make sure the server name is configured!

```
 sudo nano /etc/nginx/sites-available/default
. . .
server_name <your-pool-domain>;
. . .
```

Change the _ to your pool domain, and now you can obtain your auto-renewaled ssl certificate for free!

```
 sudo certbot --nginx -d <your-pool-domain>
```

Now you can access your pool's frontend via https! Share your pool link!


