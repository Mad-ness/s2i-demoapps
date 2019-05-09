# Creating a template

In this hands on work we will create a template wich will run an application from a __link:https://www.djangoproject.com/[Django]__ web framework.


## Workflow

A template is a set of definition of resources and sometimes it is pretty hard to debug everything in a single place. We will split up the creation in a few steps and then combine all together in a single template.

The workflow would look like this:

1. Preparing and building n S2I image
2. Running a database 
3. Running an application
4. Creating needed service and routes
5. Verification


### Create a project

```
$ oc new-project template-demo
Now using project "template-demo" on server "https://master-ocp3.dmost.pro:8443".

You can add applications to this project with the 'new-app' command. For example, try:

    oc new-app centos/ruby-25-centos7~https://github.com/sclorg/ruby-ex.git

to build a new example application in Ruby.
```



