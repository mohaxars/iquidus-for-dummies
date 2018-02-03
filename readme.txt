Pre-requisites:
- Rent Server
- Connect with SSH/PuTTY

Iquidus
"An open source block explorer"
https://github.com/iquidus/explorer

Node and Iquidus Explorer Setup for Dummies
https://gist.github.com/zeronug/5c66207c426a1d4d5c73cc872255c572

1. Install & Configure BiblePay https://www.reddit.com/r/BiblePay/comments/6ummuj/how_to_mine_biblepay_on_linux/

After Installing the coin, Add RPC & Server settings:

vi biblepay.conf

rpcuser=XXXX
rpcpassword=XXXX
rpcport=XXXX
listen=1
server=1
2. Install MongoDB
https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/

sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6

echo "deb [ arch=amd64,arm64 ] http://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list

sudo apt-get update

sudo apt-get install -y mongodb-org

sudo service mongod start

cd /var/log/mongodb
tail mongod.log
# [initandlisten] waiting for connections on port <port>
# Port 27017 by default
3. Setup MongoDB

mongo
use explorerdb
db.createUser( { user: "iquidus", pwd: "3xp!0reR", roles: [ "readWrite" ] } )
exit
4. Install Node.js

sudo apt-get update
sudo apt-get install nodejs nodejs-legacy -y
sudo apt-get install npm
5. Install Iquidis Block Explorer

cd home/username
git clone https://github.com/iquidus/explorer explorer

# gyp build errors
# https://github.com/nodejs/node-gyp/issues/809
sudo apt-get install libkrb5-dev

cd explorer && npm install --production

cp ./settings.json.template ./settings.json
6. Configure Iquidis

vi settings.json
a. Name, Symbol, Theme

b. Port (and Open for Firewall)

c. MongoDB Credentials

d. RPC Wallet Credentials

e. Genesis Block (showblock 0, hash=block, tx=tx)
https://en.bitcoin.it/wiki/Genesis_block

f. CCEX Market
https://coinigy.freshdesk.com/support/solutions/articles/1000203427-how-do-i-find-my-api-key-on-the-c-cex-exchange-

e. Icon and Logo
/images/logo.png 128x128
/public/facicon.ico 16x16
Upload files online and use "wget URL" command to download
http://digitalagencyrankings.com/iconogen/

7. Sync Initial Database

cd home/username/biblepay/src  
./biblepayd -daemon -txindex  

cd home/username/explorer
npm start
Open a 2nd SSH/Putty session and connect, in 2nd window run:

cd home/username/explorer
sudo node scripts/sync.js index update 
Open web browser and enter in your servers address: IPAddress:Port

8. Troubleshooting

Ctrl + C to stop npm process

__

If Settings/Config is wrong:
Edit explorer/settings.json

__

If Database is corrupt:
mongo
use explorerdb
show collections
db.collectionName.find()
db.collectionName.remove({}) db.collectionName.drop()

__

"Trying to reindex and getting error Script already running"
https://github.com/iquidus/explorer/issues/11
rm tmp/index.pid

__

Run npm start in explorer folder to start explorer again

9. Add Crontab and Run!

sudo crontab -e
Add lines:

*/1 * * * * cd /path/to/explorer && /usr/bin/nodejs scripts/sync.js index update > /dev/null 2>&1
*/2 * * * * cd /path/to/explorer && /usr/bin/nodejs scripts/sync.js market > /dev/null 2>&1
*/5 * * * * cd /path/to/explorer && /usr/bin/nodejs scripts/peers.js > /dev/null 2>&1

If the BiblePay isnt already running, run it

cd home/username/biblepay/src  
./biblepayd -daemon -txindex  
If Explorer isnt already running, run it

cd home/username/explorer
npm start
Extra:
This Iquidis for Dummides guide also adds:
https://gist.github.com/zeronug/5c66207c426a1d4d5c73cc872255c572

Upstart, to have MongoDB auto start after reboots

Forever, to make sure Explorer is always running
