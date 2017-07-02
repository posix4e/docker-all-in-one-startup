# Everything for production with just configfiles, sql and js.

There is an example app deployed to google with kubernetes at
https://github.com/posix4e/reserve-my-life
The google load balancer for the nginx is at
http://35.188.144.93

We include the auto backend postgrest
https://postgrest.com but a user could easily provide whatever backend in front of the database they want. 

The frontend can be built into a nginx container and deployed
via ecs ,docker-compose or kubernetes. If you need to change the database locally, but by default a frontend developer points to staging.

Although we use a docker-compose locally, we automatically support
deployment to kubernetes with kompose or ecs for staging and production. 

Rules to live by
--------
- Someone has to understand how the cluster is managed, but this depends totally on your choice of deployment.
- No one should deploy to production k8s without being a kubectl expert or having it hooked up to CI. We choose to do continuous deployment and delivery.
- Hopefully you aren't using the included docker postgresql in production as it's ephemeral an not backed up. Docker-compose will keep it's state if you want. Our scripts generally delete the docker-compose leftoverness . 
- You already have the k8s files so you'll have no trouble using them directly

Preinstall
--------

You will need 
 - npm hopefully with nvm
 - yarn
 - pip
 - A new version docker https://docs.docker.com/engine/installation/linux/ubuntu/#install-docker

install docker compose using pip

```
pip install docker-compose
```
You will need an external load balancer ip. Also you will want to put cloudflare in front of everything
This will give you ok ssl support, but it's not super secure as the link to cloudflare would be unencrypted.
They terminate it and are owned by the CIA anyway :)

Using
--------
Everything you run is in the bin directory. You can start dev with ./bin/dev. You can stop it with

```
docker-compose stop
```

If you want to create kubernetes files you will want to install
kompose with:

```
curl -L https://github.com/kubernetes-incubator/kompose/releases/download/v0.7.0/kompose-linux-amd64 -o kompose
```

Configuration
--------
We try to keep it easy for you.
You'll need to fill in the image names in docker-compose.yaml and
 in the bin scripts, and you really should understand those scripts 
 before using this at all. Notice in our example we had to change 
 the config scripts to back to google. Every kubernetes config is a bit
 special which is why we keep our scripts simple.


Frontend
--------
We generated this frontend using
```
npm install -g create-react-app
```
You can use yarn to manage the server.
```
  yarn start
```
    Starts the development server. On our example app we point our backend at staging with this.  

```
  yarn build
```
    Bundles the app into static files for production. Here's where we should be enabling sourcemap building, web workers, etc.

```
  yarn test
```
    Starts the test runner.

```
  yarn eject
```
    Removes this tool and copies build dependencies, configuration files
    and scripts into the app directory. If you do this, you can’t go back!


Backend Interace
--------

Postgrest totally unconfigured. Checkout the example for a use with jwt in reserve-my-life.

Nginx Proxy
--------

This is what actually servers our site. The generated source maps are proected by password. The current password username and password is password. You can change them by updating the htaccess file
https://www.web2generators.com/apache-tools/htpasswd-generator


Postgresql
--------

The database we store our data in. There's an init directory for everything you need to initialize your database. The example app includes how to build your application with sql files.https://github.com/posix4e/reserve-my-life/tree/master/postgres/init and google cloud sql in production https://github.com/posix4e/reserve-my-life/blob/master/README.md. In production we recommend a provided sql server if you data is small and you don't need extensions. I am also working on a versionsetup with postgresql 10 that will eventually be production worthy on k8s.  


TODO
--------
- We will eventually provide an ops tool repo for people who are managing there own kubernetes cluster. For now this project relies on google and stack driver. We also use travis and gpg for secrets and I expect the ops tool repo will handle secrets management as well.
- SSL is still a joke in this repo
