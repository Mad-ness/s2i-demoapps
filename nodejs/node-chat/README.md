# Hands On Work. A Chat on NodeJS

In this hands on work you will use a NodeJS S2I image supplied with OpenShift to run an application.

The project is copied from here https://github.com/igorantun/node-chat but changed the listening port to 8080 to satisfy to the ___S2I nodejs___ image requirements.

## Prerequisites

* OpenShift cluster has to have access to the repository `https://github.com/Mad-ness/s2i-demoapps`.
* Image stream `nodejs` should be available:
```
$ oc get -n openshift is nodejs
```
```
NAME      DOCKER REPO                                         TAGS                            UPDATED
nodejs    docker-registry.default.svc:5000/openshift/nodejs   latest,10,8-RHOAR + 4 more...   11 days ago
```
* The application should accept incoming connections on port 8080/tcp (already configured in `config.json`).

## Create a project

Create a new project named ___nodechat___.

```
$ oc new-project nodechat
```
```
Now using project "nodechat" on server "https://master-ocp3.example.com:443".

You can add applications to this project with the 'new-app' command. For example, try:

    oc new-app centos/ruby-25-centos7~https://github.com/sclorg/ruby-ex.git

to build a new example application in Ruby.
```

## Deploying the application

```
$ oc new-app --image-stream=nodejs:latest https://github.com/Mad-ness/s2i-demoapps.git --context-dir=nodejs/node-chat
```

Options:
- `--image-stream` tells to use the particular image stream `nodejs:latest`
- `https://github.com/Mad-ness/s2i-demoapps.git` is a Git repository where the source of the application is located
- `--context-dir` is the directory in the repository where the application is located

If everything did right, the output like this should be displayed
```
--> Found image 2413420 (2 weeks old) in image stream "openshift/nodejs" under tag "latest" for "nodejs:latest"

    Node.js 10.15.3
    ---------------
    Node.js 10.15.3 available as a container is a base platform for building and running various Node.js 10.15.3 applications and frameworks. Node.js is a platform built on Chrome's JavaScript runtime for easily building fast, scalable network applications. Node.js uses an event-driven, non-blocking I/O model that makes it lightweight and efficient, perfect for data-intensive real-time applications that run across distributed devices.

    Tags: builder, nodejs, nodejs-10.15.3

    * The source repository appears to match: nodejs
    * A source build using source code from https://github.com/Mad-ness/s2i-demoapps.git will be created
      * The resulting image will be pushed to image stream tag "chat:latest"
      * Use 'start-build' to trigger a new build
    * This image will be deployed in deployment config "chat"
    * Port 8080/tcp will be load balanced by service "chat"
      * Other containers can access this service through the hostname "chat"

--> Creating resources ...
    imagestream.image.openshift.io "chat" created
    buildconfig.build.openshift.io "chat" created
    deploymentconfig.apps.openshift.io "chat" created
    service "chat" created
--> Success
    Build scheduled, use 'oc logs -f bc/chat' to track its progress.
    Application is not exposed. You can expose services to the outside world by executing one or more of the commands below:
     'oc expose svc/chat'
    Run 'oc status' to view your app.
```

## Check created resources

Run this command
```
$ oc  get all
```
```
NAME               READY     STATUS      RESTARTS   AGE
pod/chat-1-6xpd6   1/1       Running     0          16s
pod/chat-1-build   0/1       Completed   0          35s

NAME                           DESIRED   CURRENT   READY     AGE
replicationcontroller/chat-1   1         1         1         19s

NAME           TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
service/chat   ClusterIP   172.30.19.146   <none>        8080/TCP   35s

NAME                                      REVISION   DESIRED   CURRENT   TRIGGERED BY
deploymentconfig.apps.openshift.io/chat   1          1         1         config,image(chat:latest)

NAME                                  TYPE      FROM      LATEST
buildconfig.build.openshift.io/chat   Source    Git       1

NAME                              TYPE      FROM          STATUS     STARTED          DURATION
build.build.openshift.io/chat-1   Source    Git@4b590f1   Complete   36 seconds ago   17s

NAME                                  DOCKER REPO                                      TAGS      UPDATED
imagestream.image.openshift.io/chat   docker-registry.default.svc:5000/nodechat/chat   latest    20 seconds ago
```

Command `new-app` based on the provided parameters creates following objects:
Following objects are created by `new-app` command or by its daughter objects:

- `build config`
- `build config` creates a `build`
- `build config` creates the `image stream` providing that the `build` is done successfully
- `deployment config`
- `deployment config` creates a `replication controller`
- `replication controller` make sure that needed number of pods are there - creates the `pod`
- `service`

## Build config logs

During the running of the build process and after it's completed you can check the logs of it. The S2I script `assemble` knows where to find the needed files and how to run the build process.
```
$ oc logs buildconfig.build.openshift.io/chat
```
```
Cloning "https://github.com/Mad-ness/s2i-demoapps.git" ...
	Commit:	4b590f1254911c499343bee47e2181bcbefa5e74 (Updated version of some libraries)
	Author:	Dmitrii Mostovshchikov <dmadm2008@gmail.com>
	Date:	Sat Apr 27 19:59:24 2019 -0400
Using docker-registry.default.svc:5000/openshift/nodejs@sha256:cd0003f4abfa61f4e801ca498acb20120008ecd3ea7ce7bb618e06ab7b2f8b1d as the s2i builder image
---> Installing application source
---> Building your Node application from source
Current git config
url.https://github.com.insteadof=git@github.com:
url.https://.insteadof=ssh://
url.https://github.com.insteadof=ssh://git@github.com
---> Installing dependencies
---> Using 'npm install -s --only=production'
added 61 packages from 48 contributors and audited 97 packages in 1.812s
found 12 vulnerabilities (4 low, 3 moderate, 5 high)
run `npm audit fix` to fix them, or `npm audit` for details
---> Pruning the development dependencies
npm info it worked if it ends with ok
npm info using npm@6.4.1
npm info using node@v10.15.3
npm timing stage:loadCurrentTree Completed in 170ms
npm timing stage:loadIdealTree:cloneCurrentTree Completed in 3ms
npm timing stage:loadIdealTree:loadShrinkwrap Completed in 72ms
npm timing stage:loadIdealTree:loadAllDepsIntoIdealTree Completed in 40ms
npm timing stage:loadIdealTree Completed in 138ms
npm timing stage:generateActionsToTake Completed in 11ms
npm timing audit compress Completed in 7ms
npm info audit Submitting payload of 3129bytes
npm timing stage:executeActions Completed in 193ms
npm timing stage:rollbackFailedOptional Completed in 1ms
npm timing stage:runTopLevelLifecycles Completed in 542ms
npm timing audit submit Completed in 375ms
npm http fetch POST 200 https://registry.npmjs.org/-/npm/v1/security/audits/quick 249ms
npm timing audit body Completed in 2ms
audited 97 packages in 0.738s
found 12 vulnerabilities (4 low, 3 moderate, 5 high)
run `npm audit fix` to fix them, or `npm audit` for details
npm timing npm Completed in 1081ms
npm info ok
---> Cleaning up npm cache
---> Fix permissions on app-root
Pushing image docker-registry.default.svc:5000/nodechat/chat:latest ...
Pushed 0/6 layers, 8% complete
Pushed 1/6 layers, 17% complete
Push successful
```

## Pods logs

Also you can check the logs from application by takin a look at the pod's logs:

```
$ oc logs chat-1-6xpd6
```
```
git version 1.8.3.1
Environment:
	DEV_MODE=false
	NODE_ENV=production
	DEBUG_PORT=5858
Running as user uid=1000240000(default) gid=0(root) groups=0(root),1000240000
Launching via npm...
npm info it worked if it ends with ok
npm info using npm@6.4.1
npm info using node@v10.15.3
npm info lifecycle node-chat@1.0.7~prestart: node-chat@1.0.7
npm info lifecycle node-chat@1.0.7~start: node-chat@1.0.7
> node-chat@1.0.7 start /opt/app-root/src
> node app.js
[00:11:52] [Start] Listening at port 8080
```

## Creating a Route

In order to access the application in the browser we need a router.

Check the current routes. Nothing.
```
$ oc get route
```
```
No resources found.
```

Create the route 

```
$ oc create route edge --hostname=chat.apps.example.com --service=chat
route.route.openshift.io/chat created
```
Options:
- `oc create route edge` creates a route with __TLS__ termination of type __Edge__
- `--hostname` tells which _FQDN_ we need for the application. Default value would be `<service>.<project>.<cluster-subdomain>`
- `--service`, using this we specify to which service the requests should go


## Verification

Open in your favorite browser this link https://chat.apps.example.com, you should get the chat running.


## Cleaning up

To remove everything run this command:

```
$ oc delete all --all
```
```
$ oc delete project chat
```

## Authors

- Dmitrii Mostovshchikov <Dmitrii.Mostovshchikov@li9.com>, <dmadm2008@gmail.com>
