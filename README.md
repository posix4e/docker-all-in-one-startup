# A usable Production with some some configfiles, sql and js to build an airbnb clone.


We include the auto backend postgrest
https://postgrest.com but a user could easily provide whatever backend in front of the database they want.

The frontend can be built into a nginx container and deployed
via ecs ,docker-compose or kubernetes. 

Although we use a docker-compose locally, we automatically support
deployment to kubernetes with kompose to staging. 

There is an example app deployed to google with kubernetes at
https://github.com/posix4e/reserve-my-life
The google load balancer for the nginx is at
http://35.188.144.93

- No one should deploy to production k8s without being a kubectl expert
- Hopefully you aren't using a docker postgresql included as it's ephemeral an not backed up
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

Configuration
--------
We try to keep it easy for you. Pretty much everything
which is not stored secrets is done for you.
You'll need to fill in the image names in docker-compose.yaml and
 in the bin scripts, and you really should understand those scripts 
 before using this at all.


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
    Starts the development server. On our example app we point this at production this way

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
