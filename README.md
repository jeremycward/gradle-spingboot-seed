# gradle-spingboot-seed
This project is an application skeleton for a typical spring boot web app that is built with gradle. You can use it to quickly bootstrap your webapp projects and dev environment for these projects.
The seed contains a sample spring boot application exposing http resources on port 8080.

The seed app doesn't do much, by default it is listening on path ```/hello``` and returns a String value.

## Getting Started
To get you started you can simply clone repository and run ./gradlew bootRun to start server. Gradle and spring will start with downloading dependencies.

## Setting up openshift projects
```
# Fire up cluster
oc cluster up

# Log in as developer in to the Web Console and CLI. Links and instructions are provided by oc cluster up command.
# Create development project for demo:
oc new-project contacts-be-dev

```

### Demo database (NoSQL, Mongodb)

```
# Create new mongodb-ephemeral from template. It will create mongodb app in contacts-be-dev
oc new-app https://raw.githubusercontent.com/sadhal/openshift-config/master/mongodb-ephemeral-template.json -p MONGODB_DATABASE=sampledb -p MONGODB_USER=sadhal -p MONGODB_PASSWORD=sadhal -n contacts-be-dev

# Start deployment
oc rollout latest mongodb

```
### Demo back end app (Java 8, Gradle, REST, Spring)

```
# Create new RESTful API backed up by Springboot.
oc new-app jorgemoralespou/s2i-java~https://github.com/sadhal/gradle-spingboot-seed#pipelines

```

### Demo frontend app (Angularjs, Nodejs)

```
# Create new angularjs app backed up by nodejs
oc new-app https://github.com/sadhal/mean-contactlist.git -p DEBUG="contacts-fe,express* node server.js" -p LOG_LEVEL=verbose
oc expose svc mean-contactlist

```

## Setting up Q&A and CI/CD projects
```
# Create projects:
oc new-project contacts-be-test
oc new-project jenkins

# Enter the jenkins project from Web Console. Add to project - jenkins pipeline. Even ephemeral works for this demo. Or from CLI:
oc new-app --template=jenkins-ephemeral -n jenkins

# Create our back end demo app in Q&A in order to show CI/CD pipeline
oc project contacts-be-test
oc new-app jorgemoralespou/s2i-java~https://github.com/sadhal/gradle-spingboot-seed#pipelines

# Log in as system:admin in CLI in order to edit user policies for jenkins:
oc login -u system:admin
oc adm policy add-role-to-user edit system:serviceaccount:jenkins:jenkins -n contacts-be-dev
oc adm policy add-role-to-user edit system:serviceaccount:jenkins:jenkins -n contacts-be-test
oc login -u sadhal -p sadhal

# Create jenkins pipeline
# In Web Console, jenkins project, click on Add to Project -> Import YAML/JSON -> choose appropriate BuildConfig -> Create
```


### Prerequisites
Latest openshift origin CLI client (>= 1.4), docker (>= 1.12) and virtualization enabled.

### CLI creation of pipeline for oc ver 1.5
```
# Create jenkins pipeline
oc project jenkins
oc new-app https://github.com/sadhal/gradle-spingboot-seed#pipelines --strategy=pipeline --context-dir='pipeline/cd' --name gradlespringboot-pipeline-cd
```
