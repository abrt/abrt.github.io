---
layout: post
title:  "Self-contained FAF in Openshift"
date:   2018-03-05 11:30:00 +0100
author: mkutlak
visible: 1
categories: FAF container OpenShift
---

We've made it possible to run FAF and required database inside Openshift in two separated containers. In this blog post, you will find information about the containers and how to deploy them in Openshift.

In the previous article [FAF in Openshift](https://abrt.github.io/faf/container/openshift/2017/10/17/faf-in-openshift/) we showed you how to set up FAF to run inside the Openshift.
Though it required FAF and the database to be inside the same container. To address this problem we've made changes in the code to enable the FAF and the database to run in two different containers.

The following text describes how you can deploy the FAF with the new changes.

#### Versions of the used software
- Openshift Origin [v3.7.1](https://github.com/openshift/origin/releases/tag/v3.7.1)
- Docker [v1.13.1](https://docs.docker.com/cs-engine/1.13/release-notes/)
- FAF [v1.3.0](https://github.com/abrt/faf/tree/1.3.0)
- abrt/postgres-semver:[9.6.6.0](https://hub.docker.com/r/abrt/postgres-semver/)
- abrt/faf-image:[1.3.0](https://hub.docker.com/r/abrt/faf-image/)

## Database

FAF is using a [PostgreSQL](https://github.com/sclorg/postgresql-container/blob/master/latest/Dockerfile.fedora) database extended by semver extension. Because of this, we can't use the official PostgreSQL image as the semver extension isn't available there.
So we've created a [new image](https://hub.docker.com/r/abrt/postgres-semver/) with the database and the semver extension.

### Set up the database

To make it as easy as possible to deploy the database in Openshift we've created a template for it.<br>
[faf-database-template.yml](https://github.com/abrt/faf/blob/master/docker/openshift/faf-database-template.yml)


Use either a terminal or web interface to create the template from the yaml file.

To create the template from a file in the terminal execute the following command:
```
 oc create -f faf-database-template.yml
```

To import the file through the web interface click on ***Add to Project*** and select ***Import YAML/JSON***.
[![empty-project][empty-project]][empty-project]

Once you have the template created, you can use it to deploy the database.
[![db-create][db-create]][db-create]

The template sets up all necessary items to run the container (persistent volume, services, secrets, image streams).

Part of the deployment is a creation of the new user in the database to allow FAF to operate with it (there is 15s delay to allow the database to fully start).

Environmental variables used inside the container:
- ``` POSTGRESQL_ADMIN_PASSWORD, (FAF_USER, FAF_PASSWORD) ```

## FAF

If you have the database ready, you can create the FAF application. The process of doing it is exactly the same as with the database.<br>

The template for FAF is available here - [faf-template.yml](https://github.com/abrt/faf/tree/master/docker/openshift/faf-template.yml)

### Set up the FAF
Use either terminal or web interface to import the template to Openshift. To create a template from the file in the terminal execute:
```
 oc create -f faf-template.yml
```

Once the template is created you can use it to deploy the FAF application.
[![faf-create][faf-create]][faf-create]

The starting script of the container sets up the database and creates cron jobs for actions that will automatically process any incoming reports.

Environmental variables used inside the container for configuration:
- ``` PGUSER, PGPASSWORD, PGDATABASE, PGHOST, PGPORT ```

The Docker image for the [FAF](https://hub.docker.com/r/abrt/faf-image/) is available on [our Docker Hub](https://hub.docker.com/r/abrt).


[empty-project]: /assets/faf-in-oc/empty_project.png "Add the template to the project."
[db-create]: /assets/faf-in-oc/db_create.png "Create database for FAF using the template."
[faf-create]: /assets/faf-in-oc/faf_create.png "Create FAF application using the template."
[final-screen]: /assets/faf-in-oc/final_screen.png "Database and FAF successfully deployed."
