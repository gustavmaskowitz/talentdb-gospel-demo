# Talentdb
Using this repo you can build a private data shring app with contextual access and gospel.tech as back-end. There is a front-end component written in React using the React-boilerplate project. And a backend that takes care of logins, certificates, the right to be forgotten and sharing data across company boundaries. It's special because a.) You can share data with fine grained access-control as structured data or blobs(e.g. PDF), without having to copy it between orgs; b.) you get immutable read-receipts, detailed which person or account read a row or column of a table, or accessed a file, on all reads. This means you can verify after trusting data into a shared state and control it/stop sharing. It's the basis of controlling your private information, while allowing it to be used to serve you.

# PREREQS
* Google Cloud account
* Domain name and ability to add records (you can try using IPs but chrome is unhappy about certs on IPs)

# Video URL 
--- TODO

# Build a Gospel backend
* Go to https://console.cloud.google.com/
* Build a Kubernetes/GKE cluster with at least 3 x n1-standard-2 nodes [5 minutes]
* Find Gospel Technology on the Google Cloud Marketplace and install on GKE cluster [3 minutes]
* The marketplace installs an installer - find it and run in via Services [10 minutes]
* COPY/PASTE the usernames and passwords shows - you'll need it later
* Log into your new Gospel Data Platform demo in GKE
* You now have a Gospel Developer Quickstart demo "backend"

# Frontend
You'll need a domain name and a certificate else chrome complains about TLS on an IP. Good old less secure Safari allows you to use IP address...

* Make a virtual machine
* Make a DNS entry for your VM's IP
* curl icanhazip.com (this gets you the IP you are using in case you only see your private IP from insude the VM)
* (sudo su -) # apt update && apt -y upgrade && apt install -y dnsutils certbot
*  certbot certonly

I use certbot.eff.org to create a certificate (because it's easy and needs no nginx pre-config). Then install nginx, nodejs and npm

# Installing NodeJS and NPM:

I'm using Ubuntu 18.04 so followed this decent enough guide: https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-ubuntu-18-04. Once you have the latest versions of node and npm, move on
*  apt install -y nodejs npm nginx git
* (This only gets you node v8.10.0 and npm 3.5.2 from Ubuntu so follow the tut above to get to Node v10.17.0 and npm 6.11.3
*  curl -sL https://deb.nodesource.com/setup_10.x -o nodesource_setup.sh
*  bash nodesource_setup.sh
*  apt install -y nodejs

Get the code & set it up

* git clone https://github.com/gustavmaskowitz/talentdb-gospel-demo.git
* cd talentdb-gospel-demo/
* (You'll also find the SDK's download URL in the Admin UI of the Gospel back-end)
* npm install gospel-sdk-js-1.5.0-296.tgz

Configure the app
* [edit the DEV connection parameters internals/webpack/config.dev.json or PROD in app/config.js]
* This is where you set how the app works. You need to give it your VM's IP or 
* npm install
* npm run build
* Now copy the build directory to /var/www/app
* See the nginx config files to implement the reverse_proxy setup to host the app and send traffic to the back end
* Make the site live doing "ln -s /etc/nginx/sites-available/talentdb /etc/nginx/sites-enabled/talentdb"

# Next steps include
* In GKE, find and edit the 'gospel-ca' deployment on ~line 55. 
* Change all the IP addresses that refer to gospel back-end's nginx web-service with the domain name of font-end. I built talentdb.gus.io with a valid letsencrypt certificate else Chrome was unhappy. Consider doing the same. Using the VM + it's IP + DNS pointed to it was the simplest to get the cert.
* While the gospel-ca deployment works on waking up a new gospel-ca pod with 6 containers (it may take a while) do:
*   use "kubectl -n gospel delete xxxxx" to delete the pods signer-xxxxx and 3x backend-xxxx. 


# Using it
Hitting talentdb.gus.io for me, shows the index.html file of the build directory on the VM. It also proxy-passes me to the Gospel back-end for login and auth, and the CA changes means it's sends me back to the front-end to continue using the app. (Forgive me but my site is likely offline as I kill the backend and vms when not testing/demoing this to people)

Use the accounts provided when the back-end completed. Start with account viewer1

Right! Now you can start playing around and see what is connected to what. Try changing the app into a system for sensitive scanned documents for the NHS or a Bank.
