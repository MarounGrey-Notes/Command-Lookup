# Server setup with digital ocean and virtualmin / webmin

### Create the droplet in digital ocean
###### I prefer 4gb of ram since I had some memory issues installing magento on 2gb. Wordpress was fine though
##### The hostname have to be the domain with the subdomain
###### Example:
`dev.maroungrey.com`
###### Have to consider the subdomain compatibility with cloudflare, so far I only had success with dev or test subdomains for some reason

### Ssh to the droplet and add user
##### After the droplet is created, grab the ipv4 in the top left corner and ssh to the root
###### Example:
`ssh root@123.456.78.90`
##### Then add the user
###### Example:
`adduser maroun`
##### Add sudo rights to a user
###### Example:
`usermod -aG sudo maroun`
##### Now you can exit the root
`exit`

### Check if the hostname setted up correctly
##### Now you can ssh to your user by only executing:
`ssh root@123.456.78.90`
##### Check if the hostname is correct and edit it if its wrong
`sudo nano /etc/hostname`
###### Here you should see your hostname, not the FQDN, for example if you see <dev.maroungrey.com> change it to <dev>
##### Next, you want to check if /etc/hosts file is right:
`sudo nano /etc/hosts`
###### Here you should see something like:
`127.0.1.1   dev.maroungrey.com dev`
`127.0.0.1   localhost`
###### If you had to edit anything, run:
`sudo reboot`

### Next install virtualmin
##### Get the package and install it:

`wget http://software.virtualmin.com/gpl/scripts/install.sh`
`sudo /bin/sh install.sh`

### DNS setup
##### Next step is setting up the DNS record in cloudflare
###### Add site and go to DNS tab. Now you need to set up few records.
###### Here is the example of configs that you need:

| Type | Name | Content | TTL | Proxy status |
| --- | --- | --- | --- | --- |
| A | maroungrey.com | 123.456.78.90 | Auto | DNS only |
| --- | --- | --- | --- | --- |
| CNAME | dev | maroungrey.com | Auto | DNS only |
| --- | --- | --- | --- | --- |
| CNAME | www | maroungrey.com | Auto | DNS only |
| --- | --- | --- | --- | --- |
| CNAME | www.dev | maroungrey.com | Auto | DNS only |
| --- | --- | --- | --- | --- |
| TXT | nxdomain | _acme-challenge.www.dev.maroungrey.com | Auto | DNS only |
| --- | --- | --- | --- | --- |

### Virtualmin
###### After you created the dns records you should be able to access the virtualmin by this url:
`https://dev.maroungrey.com:10000/`
###### or ip:
`https://123/456/78/90:10000/`
###### Now virtualmin will walk you through the post installation configs. After you finish that you need to create ssl certificate in:
`Virtualmin -> Server Configuration -> SSL Certificate -> Let's Encrypt`
###### After that you need to enable the SSL Certificate in Webmin:
`Webmin -> Webmin -> Webmin Configuration -> SSL Encryption`
###### Enable the SSL and create a new SSL certificate in Let's Encrypt

### After steps
##### Now the setup is pretty much good to go, you can set up the backups now and if you need to install different php version here are the steps:
###### Ssh to the root:
`ssh root@123.456.78.90`
###### Get the PPA
`add-apt-repository ppa:ondrej/php && apt-get update`
###### Install PHP packages:
`apt-get install php7.1 php7.1-mysql php7.1-cgi php7.1-cli php7.1-fpm`
`apt-get install php7.2 php7.2-mysql php7.2-cgi php7.2-cli php7.2-fpm`
###### Now you can choose different php version in:
`Virtualmin -> Server Configuration -> PHP Options -> Versions`

