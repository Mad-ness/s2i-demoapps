# Hands On Work. QuizApp on NodeJS

In this hands on work you will need to run a NodeJS application. This application depends on MongoDB database.

## Prerequisities

* OpenShift cluster has to have access to this repository __https://github.com/cstodor/QuizApp__.


## Creating a project

Create the project named `quizapp`

```
$ oc new-project quizapp
```

## Run a MongoDB database

* Import the __MongoDB__ image from a public remote repository
```
$ oc import-image mongodb --from=docker.io/bitnami/mongodb --confirm
```
The output would be like this.
```
imagestream.image.openshift.io/mongodb imported

Name:			mongodb
Namespace:		quizapp
Created:		Less than a second ago
Labels:			<none>
Annotations:		openshift.io/image.dockerRepositoryCheck=2019-04-28T20:18:55Z
Docker Pull Spec:	docker-registry.default.svc:5000/quizapp/mongodb
Image Lookup:		local=false
Unique Images:		1
Tags:			1

latest
  tagged from docker.io/bitnami/mongodb

  * docker.io/bitnami/mongodb@sha256:ddcdcf922c8be00be6688613456fc502d46fdaeff27fd453efc90bf1703dbb28
      Less than a second ago

Image Name:	mongodb:latest
Docker Image:	docker.io/bitnami/mongodb@sha256:ddcdcf922c8be00be6688613456fc502d46fdaeff27fd453efc90bf1703dbb28
Name:		sha256:ddcdcf922c8be00be6688613456fc502d46fdaeff27fd453efc90bf1703dbb28
Created:	Less than a second ago
Annotations:	image.openshift.io/dockerLayersOrder=ascending
Image Size:	163.1MB in 11 layers
Layers:		22.06MB	sha256:f2aec58b1c322b13a07284f7ae8f3a46dd0abcc23a32ceab26db5208ea8bfdd0
		21.75MB	sha256:579344287f726a1929c14a6d0478cadda5ca42451e52bde6ca777a5e9041f55d
		5.206kB	sha256:e478129b97d3c5df50b898828f81668c68a3c4a4232373801f820015c0b7cb35
		12.52MB	sha256:fe9646b5749a42b7a5ddcc8b87cc51218de599321acefcfbcbff23d7206522bf
		14.59kB	sha256:64f9c9bea9b4146c87fd22843b136602fb83c37a3bacd4c38777c39737162f1b
		580.5kB	sha256:96ff51002a8b0f7075764ccea1f5933bdc2de3f79e1cf410e8d79ae9f8e5bf2f
		5.317kB	sha256:b13cf9f640bf4db13f9315117ece737ab016ce8cbdacbcb091830c15f4b57890
		480.4kB	sha256:7098394cdcc77848b172c017e3146869a177f3a80e0a06c3be89b7b0387d9986
		105.7MB	sha256:8c2805bac84b5d9065e6a7a6692bc7fbc46e382939f8f5144b541db21c970004
		115B	sha256:c448e568f1544a5bab9ae0a5a3e8a9badd63a2e7b758e250b0694bb0decd4e64
		1.679kB	sha256:4fb2941b48bdee8321de553c13495389e067d2831ef5835cff9feb2198ccfedc
Image Created:	2 days ago
Author:		<none>
Arch:		amd64
Entrypoint:	/app-entrypoint.sh
Command:	/run.sh
Working Dir:	<none>
User:		1001
Exposes Ports:	27017/tcp
Docker Labels:	maintainer=Bitnami <containers@bitnami.com>
Environment:	PATH=/opt/bitnami/mongodb/bin:/opt/bitnami/nami/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
		IMAGE_OS=debian-9
		NAMI_VERSION=1.0.0-1
		GPG_KEY_SERVERS_LIST=ha.pool.sks-keyservers.net                          hkp://p80.pool.sks-keyservers.net:80                          keyserver.ubuntu.com                          hkp://keyserver.ubuntu.com:80                          pgp.mit.edu
		TINI_VERSION=v0.13.2
		TINI_GPG_KEY=595E85A6B1B4779EA4DAAEC70B588DFF0527A9B7
		GOSU_VERSION=1.10
		GOSU_GPG_KEY=B42F6819007F00F88E364FD4036A9C25BF357DD4
		BITNAMI_IMAGE_VERSION=4.0.9-debian-9-r9
		BITNAMI_PKG_CHMOD=-R g+rwX
		HOME=/
		BITNAMI_APP_NAME=mongodb
		MONGODB_ADVERTISED_HOSTNAME=
		MONGODB_DATABASE=
		MONGODB_DISABLE_SYSTEM_LOG=no
		MONGODB_ENABLE_DIRECTORY_PER_DB=no
		MONGODB_ENABLE_IPV6=yes
		MONGODB_PASSWORD=
		MONGODB_PORT_NUMBER=27017
		MONGODB_PRIMARY_HOST=
		MONGODB_PRIMARY_PORT_NUMBER=27017
		MONGODB_PRIMARY_ROOT_PASSWORD=
		MONGODB_PRIMARY_ROOT_USER=root
		MONGODB_REPLICA_SET_KEY=
		MONGODB_REPLICA_SET_MODE=
		MONGODB_REPLICA_SET_NAME=replicaset
		MONGODB_ROOT_PASSWORD=
		MONGODB_SYSTEM_LOG_VERBOSITY=0
		MONGODB_USERNAME=
		NAMI_PREFIX=/.nami
```
We imported `mongodb` image from the image that is not a `S2I` image, nonetheless it is imported.

* Check it

```
$ oc get is
```
```
NAME      DOCKER REPO                                        TAGS      UPDATED
mongodb   docker-registry.default.svc:5000/quizapp/mongodb   latest    38 seconds ago
```

* Run the MongoDB database

Do the next command:
```
$ oc new-app --name=mongodb --image-stream=mongodb
```
We used option `--name`. This allows us to search only needed staff by label `app=mongo`.
```
--> Found image acb1112 (2 days old) in image stream "quizapp/mongodb" under tag "latest" for "mongodb"

    * This image will be deployed in deployment config "mongodb"
    * Port 27017/tcp will be load balanced by service "mongodb"
      * Other containers can access this service through the hostname "mongodb"

--> Creating resources ...
    deploymentconfig.apps.openshift.io "mongodb" created
    service "mongodb" created
--> Success
    Application is not exposed. You can expose services to the outside world by executing one or more of the commands below:
     'oc expose svc/mongodb'
    Run 'oc status' to view your app.
```

* Wait for a while and check the status

```
$ oc get pods -l app=mongo
```
```
NAME              READY     STATUS    RESTARTS   AGE
mongodb-1-sjph2   1/1       Running   0          1m
```


* Check all objects created by the `oc new-app` command

```
$ oc get all
```
```
llNAME                  READY     STATUS    RESTARTS   AGE
pod/mongodb-1-sjph2   1/1       Running   0          7m

NAME                              DESIRED   CURRENT   READY     AGE
replicationcontroller/mongodb-1   1         1         1         7m

NAME              TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)     AGE
service/mongodb   ClusterIP   172.30.72.46   <none>        27017/TCP   7m

NAME                                         REVISION   DESIRED   CURRENT   TRIGGERED BY
deploymentconfig.apps.openshift.io/mongodb   1          1         1         config,image(mongodb:latest)

NAME                                     DOCKER REPO                                        TAGS      UPDATED
imagestream.image.openshift.io/mongodb   docker-registry.default.svc:5000/quizapp/mongodb   latest    11 minutes ago
```

It looks good. The pod is running without issues. We need to configure the database to use allow authentication by a login and password.

Go to the page with the documentation on used image, https://hub.docker.com/r/bitnami/mongodb. From there we learn that if we provide some environment variables Mongo creates a user, password, and database. These are:

- `MONGODB_USERNAME`
- `MONGODB_PASSWORD`
- `MONGODB_DATABASE`


We cannot provide any environment variables within `oc new-app` command. Thus we may define needed variables right in the `deploymentconfig/mongodb` configuration or also can use `ConfigMaps` or `Secrets` for this. Since login and password are sensetive data, the most proper options would be a `Secret`. Lets create it.

* Creating a `secret/mongodb`

Create file `secret-mongodb.yaml` as follows:
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mongodb
  app: mongodb
type: Opaque
data:
  MONGODB_DATABASE: cXVpemFwcAo=
  MONGODB_USERNAME: cXVpemFwcAo=
  MONGODB_PASSWORD: VmVyeVN0cm9uZ1Bhc3N3b3JkMTIzCg==
```
* Decoded `base64` hashed value
  -  `MONGODB_DATABASE` is `quizapp` (decoded as `echo 'cXVpemFwcAo=' | base64 -d`)
  -  `MONGODB_USERNAME` is `quizapp`
  -  `MONGODB_PASSWORD` is `VeryStrongPassword123`

* Create a `Secret` object
```
$ oc create -f secret-mongodb.yaml
```
```
secret/mongodb created
```

We can also check it
```
$ oc describe secret/mongodb
```
```
Name:         mongodb
Namespace:    quizapp
Labels:       <none>
Annotations:  <none>

Type:  Opaque

Data
====
MONGODB_USERNAME:  8 bytes
MONGODB_DATABASE:  8 bytes
MONGODB_PASSWORD:  22 bytes
```

* Attaching the `Secret` variables as environment variables to `deploymentconfig.apps.openshift.io/mongodb`:

```
oc set env --from=secret/mongodb dc/mongodb
```
```
deploymentconfig.apps.openshift.io/mongodb updated
```
* Checking the database
First run this command to make sure that change of configuration caused a new deployment
```
$ oc get pod -l app=mongodb
```
We see that the pod has another name `mongodb-2-69nkx` and its age is 31 seconds old. That means that previous pod has been destroyed and the new one created.
```
NAME              READY     STATUS    RESTARTS   AGE
mongodb-2-69nkx   1/1       Running   0          31s
```

We can also check the configuration of the pod
```
$ oc describe pod mongodb-2-69nkx
```
The below output shows the defined environment variables.
```
Name:               mongodb-2-69nkx
Namespace:          quizapp
Priority:           0
PriorityClassName:  <none>
Node:               node04.ocp.local/172.31.53.11
Start Time:         Sun, 28 Apr 2019 16:42:07 -0400
Labels:             app=mongodb
                    deployment=mongodb-2
                    deploymentconfig=mongodb
Annotations:        openshift.io/deployment-config.latest-version=2
                    openshift.io/deployment-config.name=mongodb
                    openshift.io/deployment.name=mongodb-2
                    openshift.io/generated-by=OpenShiftNewApp
                    openshift.io/scc=restricted
Status:             Running
IP:                 10.130.0.127
Controlled By:      ReplicationController/mongodb-2
Containers:
  mongodb:
    Container ID:   docker://28f0d3cfe528ae79564f6dc052c39d71cb9306bade8d88776a5ccfdb1fe1e71f
    Image:          docker.io/bitnami/mongodb@sha256:ddcdcf922c8be00be6688613456fc502d46fdaeff27fd453efc90bf1703dbb28
    Image ID:       docker-pullable://docker.io/bitnami/mongodb@sha256:ddcdcf922c8be00be6688613456fc502d46fdaeff27fd453efc90bf1703dbb28
    Port:           27017/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Sun, 28 Apr 2019 16:42:10 -0400
    Ready:          True
    Restart Count:  0
    Environment:
      MONGODB_DATABASE:  <set to the key 'MONGODB_DATABASE' in secret 'mongodb'>  Optional: false
      MONGODB_PASSWORD:  <set to the key 'MONGODB_PASSWORD' in secret 'mongodb'>  Optional: false
      MONGODB_USERNAME:  <set to the key 'MONGODB_USERNAME' in secret 'mongodb'>  Optional: false
...
<output truncated>
```

We also can checks the pod's logs:
```
$  oc logs mongodb-2-69nkx | grep mongodb\ INFO
```
```
mongodb INFO  ==> Deploying MongoDB from scratch...
mongodb INFO  ==> No injected configuration files found. Creating default config files...
mongodb INFO  ==> Enabling authentication...
mongodb INFO  ==> Creating quizapp
mongodb INFO
mongodb INFO  ########################################################################
mongodb INFO   Installation parameters for mongodb:
mongodb INFO     Root Password: Not set during installation
mongodb INFO     Username: quizapp
mongodb INFO     Password: **********
mongodb INFO     Database: quizapp
mongodb INFO   (Passwords are not shown for security reasons)
mongodb INFO  ########################################################################
mongodb INFO
```
Last lines tell that the provided values are passed to _MongoDB_.


At this step we finished configuring the database.

## Running the QuizApp application

The big plus of using S2I is that the application even don't need to know how it is being run. But sometimes some modifications are still needed.

We will use the application written by someone. Check the repository __https://github.com/cstodor/QuizApp__, read the README.md information.

So run now we can try to run this application:

```
$ oc new-app --name=quizapp nodejs:10~https://github.com/cstodor/QuizApp
```
```
--> Found image 2413420 (2 weeks old) in image stream "openshift/nodejs" under tag "10" for "nodejs:10"

    Node.js 10.15.3
    ---------------
    Node.js 10.15.3 available as a container is a base platform for building and running various Node.js 10.15.3 applications and frameworks. Node.js is a platform built on Chrome's JavaScript runtime for easily building fast, scalable network applications. Node.js uses an event-driven, non-blocking I/O model that makes it lightweight and efficient, perfect for data-intensive real-time applications that run across distributed devices.

    Tags: builder, nodejs, nodejs-10.15.3

    * A source build using source code from https://github.com/cstodor/QuizApp will be created
      * The resulting image will be pushed to image stream tag "quizapp:latest"
      * Use 'start-build' to trigger a new build
    * This image will be deployed in deployment config "quizapp"
    * Port 8080/tcp will be load balanced by service "quizapp"
      * Other containers can access this service through the hostname "quizapp"

--> Creating resources ...
    imagestream.image.openshift.io "quizapp" created
    buildconfig.build.openshift.io "quizapp" created
    deploymentconfig.apps.openshift.io "quizapp" created
    service "quizapp" created
--> Success
    Build scheduled, use 'oc logs -f bc/quizapp' to track its progress.
    Application is not exposed. You can expose services to the outside world by executing one or more of the commands below:
     'oc expose svc/quizapp'
    Run 'oc status' to view your app.
```


* Wait for a while and check the pods

```
$ oc get pod -l app=quizapp
```
```
NAME              READY     STATUS    RESTARTS   AGE
quizapp-1-h6kj2   1/1       Running   0          20s
```
The output looks good. 

Let's check the created service:
```
$ oc get svc -l app=quizapp
```
```
NAME      TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
quizapp   ClusterIP   172.30.72.198   <none>        8080/TCP   5m
```
It is created and accepts connections on port `8080/tcp`.

Let's check the pod's logs:
```
$ oc logs quizapp-1-h6kj2 | tail
```
```
Launching via npm...
npm info it worked if it ends with ok
npm info using npm@6.4.1
npm info using node@v10.15.3
npm info lifecycle quizapp-v.0.5@0.5.0~prestart: quizapp-v.0.5@0.5.0
npm info lifecycle quizapp-v.0.5@0.5.0~start: quizapp-v.0.5@0.5.0
> quizapp-v.0.5@0.5.0 start /opt/app-root/src
> node server
Server started on port: 5000
Database error MongoError: failed to connect to server [localhost:27017] on first connect [MongoError: connect ECONNREFUSED 127.0.0.1:27017]
```
From here we can see two problems:

1. __Server started on port 5000__
2. __It wants to connect to a Mongo database on host `127.0.0.1`.__

* Fixing the __problem 1__

In order to fix the __problem 1__ we need to reconfigure the application to start on port `8080` as it expected by `service` and this port is exposed on the pod:
```
$ oc describe pod quizapp | grep Port | grep TCP
    Port:           8080/TCP
    Host Port:      0/TCP
```

After learning the file `server.js` on this repository we found the next line `const port = process.env.PORT || 5000`. That means that port `5000` is used  if there is no an environment variable named `PORT` defined. Let's fix it:

```
$ oc set env dc/quizapp PORT=8080
```
```
deploymentconfig.apps.openshift.io/quizapp updated 
```
After that the deployment config will run a new pod. Check it:
```
$ oc logs quizapp-2-z755p | tail -5
```
```
npm info lifecycle quizapp-v.0.5@0.5.0~start: quizapp-v.0.5@0.5.0
> quizapp-v.0.5@0.5.0 start /opt/app-root/src
> node server
Server started on port: 8080
Database error MongoError: failed to connect to server [localhost:27017] on first connect [MongoError: connect ECONNREFUSED 127.0.0.1:27017]
```
Ok now we see that the application is listening to the port `8080`. This is what we need.

* Fixing the __problem 2__

After learning the source code in file `server.js` we figured out that it uses an external file with the settings of the connection to the database:

```javascript
// Config Files
const dbConfig = require('./config/db')
```

Check this file, `cat config/db.js`:
```javascript
module.exports = {
    database: 'mongodb://localhost:27017/quizapp',
    secret: 'MySecretKey'
}
```
So all what we need to replace `localhost` to a proper hostname and change `secret`. One of possible solutions could be creating a copy of this repository and doing neccesary changes. But we will go by another pathway - we will replace this file with needed content.

Create file named `secret-quizapp.yaml` with this content
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: quizapp
  app: quizapp
type: Opaque
stringData:
  db.js: |
    module.exports = {
      database: 'mongodb://mongodb:27017/quizapp',
      secret: 'VeryStrongPassword123'
    }
```
Why do we use name `mongodb` for replacing `127.0.0.1`? Check this:
```
$ oc get service -l app=mongodb
```
```
NAME      TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)     AGE
mongodb   ClusterIP   172.30.72.46   <none>        27017/TCP   1h
```
Within the same project all pods can talk to each other using only names of services.

Here we define a new secret and put there the desired content. 

Load the secret into OpenShift:
```
$ oc create -f secret-quizapp.yaml
```
```
secret/quizapp created
```

Now we need to tell somehow the deploymentconfig to use this secret for replacing the bad file:

```
$ oc set volumes dc/quizapp --add --secret-name=quizapp --mount-path=/opt/app-root/src/config --read-only --default-mode=0400
```
Options:
- `--mount-path` this is where all keys from the secret `quizapp` will be mounted as files those keys are the names of the files.
- `--read-only` tells that this will be read-only
- `--default-mode` is access mode sounding as _owner can read_, _nobody else does not have any access_ because this file includes a username and password to access to the database.
```
info: Generated volume name: volume-pv2hs
deploymentconfig.apps.openshift.io/quizapp volume updated
```

By this time `deploymentconfig/quizapp` should have already rebuild the app pods. Check it, and get its log

```
$ oc get pod -l app=quizapp
```
```
NAME              READY     STATUS    RESTARTS   AGE
quizapp-3-lgr57   1/1       Running   0          6m
```

```
$ oc logs quizapp-3-lgr57 | tail -5
```
```
npm info lifecycle quizapp-v.0.5@0.5.0~start: quizapp-v.0.5@0.5.0
> quizapp-v.0.5@0.5.0 start /opt/app-root/src
> node server
Server started on port: 8080
Connected to database mongodb://mongodb:27017/quizapp
```

Now it looks good. It's almost ready for using!

* Creating a route to the `service/quizapp`
We need it to access the application fron anywhere.

```
$ oc create route edge --hostname quiz.apps.dmost.pro --service=quizapp
```
We use here the `Edge` type of termination so all traffic from the end use to the OpenShift platform will be encrypted by SSL.
```
route.route.openshift.io/quizapp created
```

* Checking the route

```
$ oc get route -l app=quizapp
```
```
NAME      HOST/PORT             PATH      SERVICES   PORT       TERMINATION   WILDCARD
quizapp   quiz.apps.example.com             quizapp    8080-tcp   edge          None
```

## Verification

Open your favorite browser and open the link __https://quiz.apps.example.com__. You should see the web interface of the application.

In summary you should have the objects like these:

```
$ oc get all
```
```
NAME                  READY     STATUS      RESTARTS   AGE
pod/mongodb-2-69nkx   1/1       Running     0          1h
pod/quizapp-1-build   0/1       Completed   0          32m
pod/quizapp-3-lgr57   1/1       Running     0          11m

NAME                              DESIRED   CURRENT   READY     AGE
replicationcontroller/mongodb-1   0         0         0         1h
replicationcontroller/mongodb-2   1         1         1         1h
replicationcontroller/quizapp-1   0         0         0         32m
replicationcontroller/quizapp-2   0         0         0         20m
replicationcontroller/quizapp-3   1         1         1         11m

NAME              TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)     AGE
service/mongodb   ClusterIP   172.30.72.46    <none>        27017/TCP   1h
service/quizapp   ClusterIP   172.30.72.198   <none>        8080/TCP    32m

NAME                                         REVISION   DESIRED   CURRENT   TRIGGERED BY
deploymentconfig.apps.openshift.io/mongodb   2          1         1         config,image(mongodb:latest)
deploymentconfig.apps.openshift.io/quizapp   3          1         1         config,image(quizapp:latest)

NAME                                     TYPE      FROM      LATEST
buildconfig.build.openshift.io/quizapp   Source    Git       1

NAME                                 TYPE      FROM          STATUS     STARTED          DURATION
build.build.openshift.io/quizapp-1   Source    Git@61a5edd   Complete   32 minutes ago   36s

NAME                                     DOCKER REPO                                        TAGS      UPDATED
imagestream.image.openshift.io/mongodb   docker-registry.default.svc:5000/quizapp/mongodb   latest    About an hour ago
imagestream.image.openshift.io/quizapp   docker-registry.default.svc:5000/quizapp/quizapp   latest    32 minutes ago

NAME                               HOST/PORT             PATH      SERVICES   PORT       TERMINATION   WILDCARD
route.route.openshift.io/quizapp   quiz.apps.dmost.pro             quizapp    8080-tcp   edge          None
```


## Cleaning Up

There are a couple options:

- option 1
```
$ oc delete all -l app=mongodb
$ oc delete all -l app=quizapp
```
- option 2
```
$ oc delete all --all
```
And finally, delete the project `oc delete project quizapp`.

## Authors

- Dmitrii Mostovshchikov <Dmitrii.Mostovshchikov@li9.com>

