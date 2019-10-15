---
title: Create and start API Gateway Docker container
linkTitle: Create and start API Gateway Docker container
weight: 4
date: 2019-09-18
description: Steps to build an API Gateway Docker image and start an API Gateway Docker container.
---

## Create an API Gateway Docker image

Use the `build_gw_image.py` script to create an API Gateway Docker image.

### Build API Gateway image script options

You must specify the following as options when using the `build_gw_image.py` script:

* Domain certificate, private key, and password.
* API Gateway license. Your license must also include any optional licensed features that you are using (for example, API Manager, FIPS mode).

This script also supports additional options when generating an API Gateway image. For example, you can:

* Specify a group ID for the API Gateway group. All containers started from this image are part of this group.
* Build an image from existing API Gateway configuration by specifying an existing `fed` file (or existing `pol` and `env` files). 
  If OAuth or API Manager are enabled in the `fed`, they are enabled in the API Gateway Docker image.
* Specify a merge directory to add to the API Gateway Docker image. This merge directory can include custom configuration, JAR files, and so on.
* Enable FIPS mode for the API Gateway Docker image.

For the latest script usage and options, run the script with no options, or with the `-h` option.

```
cd emt_containers-<version>
./build_gw_image.py -h
```

### Create an API Manager or OAuth enabled API Gateway image using domain certificate

The following example creates an API Manager enabled API Gateway Docker image using a deployment package exported from Policy Studio that has API Manager configured.

You can create an OAuth-enabled API Gateway Docker image in the same way (using a deployment package exported from Policy Studio that has OAuth configured).

```
cd emt_containers-<version>
./build_gw_image.py \
--license=/tmp/api_gw_api_mgr.lic \
--domain-cert=certs/mydomain/mydomain-cert.pem \
--domain-key=certs/mydomain/mydomain-key.pem \
--domain-key-pass-file=/tmp/pass.txt \
--parent-image=my-gw-base:1.0 \
--fed=api-mgr-group-fed.fed \
--fed-pass-file=/tmp/api-mgr-group-fedpass.txt \
--group-id=api-mgr-group \
--merge-dir=/tmp/apigateway
```

This example creates an API Gateway Docker image named `api-gateway-api-mgr-group` with a tag of `latest`. This image has the following characteristics:

* Based on the `my-gw-base:1.0` image.
* Uses a specified certificate and key.
* Uses a specified `fed` that contains API Manager configuration that was exported from Policy Studio.
* Belongs to the API Gateway group `api-mgr-group`. All containers started from this image belong to this group.
* Uses a specified merge directory (containing the JDBC driver JAR file for the metrics database) that is merged into the API Gateway image

To create an API Manager enabled image:

* You must have a valid API Manager license file to create an API Manager image.
* Use the `--merge-dir` option to specify the `apigateway` directory containing the JDBC driver JAR file for the metrics database in the `ext/lib` directory:
  * The merge directory must be called `apigateway` and must have the same directory structure as in an API Gateway installation.
  * Copy the JAR file to a new directory `/tmp/apigateway/ext/lib/` and specify `/tmp/apigateway` to the `--merge-dir` option.
* Before running the `build_gw_image.py` script you must first create a project in Policy Studio, configure API Manager in that project, and export the configuration from Policy Studio as a `fed` file (or `pol` and `env` files). For more information, see below [Configure API Manager in Policy Studio](#apimgrps).
* You must specify the configuration exported from Policy Studio to the `build_gw_image.py` script when building the API Gateway Docker image.

To create an OAuth-enabled image:

* Before running the `build_gw_image.py` script you must first create a project in Policy Studio, configure OAuth in that project, and export the configuration from Policy Studio as a `fed` file (or `pol` and `env` files). For more information, see below [Configure OAuth in Policy Studio](#oauthps).
* You must specify the configuration exported from Policy Studio to the `build_gw_image.py` script when building the API Gateway Docker image.

#### Configure API Manager in Policy Studio {#apimgrps}

Follow these steps:

* Open Policy Studio and open or create a new project.
* Select **File > Configure API Manager**.
* If you do not have any Cassandra hosts configured, you must add a Cassandra host before you can continue:
  * Enter a name for the Cassandra server (for example, `container_cassandra`).
  * Enter the name of the Cassandra container as the host name (for example, `cassandra228`).
  * Enter the port of the Cassandra container (for example, `9042`).

* Click **Next**.
* Enter the appropriate API Manager settings. For full details, see [Enable API Manager](/csh?context=1026&product=prod-api-manager-77) in the [API Manager User Guide](/bundle/APIManager_77_APIMgmtGuide_allOS_en_HTML5/).

{{< alert title="Note" color="primary" >}}The default API administrator user name and password set in Policy Studio are used only when creating the administrator account in Apache Cassandra. After the account has been created in Cassandra, you cannot change the credentials in Policy Studio. You must use API Manager to change the administrator credentials. You can also reset the administrator password by running the `setup-apimanager` script with the option `--resetPassword` inside the Admin Node Manager container. For details, see [Reset the default API administrator password](/docs/container_topics/container_troubleshoot#Reset).{{< /alert >}}

* Click **Finish**.
* Configure additional API Manager settings under **Server Settings > API Manager**. For example, you can specify custom policies that are called as traffic passes through API Manager.
* Select **File > Export** and select a package to export the configuration as a package (`fed`, `pol`, or `env`).

#### Configure OAuth in Policy Studio {#oauthps}

Follow these steps:

* Open Policy Studio and open or create a new project.
* Select **File > Configure OAuth**.
* If you do not have any Cassandra hosts configured, you must add a Cassandra host before you can continue:
  * Enter a name for the Cassandra server (for example, `container_cassandra`).
  * Enter the name of the Cassandra container as the host name (for example, `cassandra228`).
  * Enter the port of the Cassandra container (for example, `9042`).
* Click **Next**.
* Select the OAuth deployment type. For full details, see
    [Deploy OAuth configuration](/csh?context=400&product=prod-api-gateway-77)
    in the
    [API Gateway OAuth User Guide](/bundle/APIGateway_77_OAuthUserGuide_allOS_en_HTML5/).
* Click **Finish**.
* Select **File > Export** and select a package to export the configuration as a package (`fed`, `pol`, or `env`).

{{< alert title="Note" color="primary" >}}When you configure OAuth in Policy Studio, this does not register the sample client applications in the Client Application Registry. You must import the sample client applications manually, as detailed in
[Import sample client applications](/csh?context=402&product=prod-api-gateway-77) in the [API Gateway OAuth User Guide](/bundle/APIGateway_77_OAuthUserGuide_allOS_en_HTML5/).{{< /alert >}}

## Other options to create an API Gateway image

The following are additional examples of using the `build_gw_image.py` script to build API Gateway Docker images.

### Create an API Gateway image using defaults

The following example creates an API Gateway Docker image using default certificates and a default factory `fed`.

Do not use default options on production systems. The `--default-cert` option is provided only as a convenience for development environments.

```
cd emt_containers-<version>
./build_gw_image.py \
--license=/tmp/api_gw_license_complete.lic \
--default-cert \
--factory-fed
```

This example creates an API Gateway Docker image named `api-gateway-defaultgroup` with a tag of `latest`. This image has the following characteristics:

* Uses a default certificate and key (generated from running `./gen_domain_cert.py --default-cert`)
* Uses a default factory `fed`

### Create an API Manager image using defaults

The following example creates an API Manager Docker image using default certificates and a default factory `fed` with samples.

Do not use default options on production systems. The `--default-cert` and `--api-manager` options are provided only as a convenience for development environments.

<!--comment to force code block to left margin-->
```
cd emt_containers-<version>
./build_gw_image.py \
--license=/tmp/api_gw_api_mgr.lic \
--merge-dir /tmp/apigateway \
--default-cert \
--api-manager
```

This example creates an API Gateway Docker image named `api-gateway-defaultgroup` with a tag of `latest`. This image has the following characteristics:

* Uses a default certificate and key (generated from running `./gen_domain_cert.py --default-cert`)
* Uses a default factory `fed` with samples and with API Manager configured
* Uses a specified merge directory (containing the JDBC driver JAR file for the metrics database) that is merged into the API Gateway image

When using the `--api-manager` default option:

* You must have an Apache Cassandra server running at the host name specified by `${environment.CASS_HOST}`.
* You must have a metrics database running at `${environment.METRICS_DB_URL}`, with credentials of `${environment.METRICS_DB_USERNAME}` and `${environment.METRICS_DB_PASS}`.
* You can log in to the API Manager web console using a default user name of `apiadmin` and the default password.
* You must have a valid API Manager license file to create an API Manager image.

Use the `--merge-dir` option to specify the `apigateway` directory containing the JDBC driver JAR file for the metrics database in the `ext/lib` directory:

* The merge directory must be called `apigateway` and must have the same directory structure as in an API Gateway installation.
* Copy the JAR file to a new directory `/tmp/apigateway/ext/lib/` and specify `/tmp/apigateway` to the `--merge-dir` option.

### Create an API Gateway image using domain certificate

The following example creates an API Gateway Docker image using a specified domain certificate and a default factory `fed`.

```
cd emt_containers-<version>
./build_gw_image.py \
--license=/tmp/api_gw_license_complete.lic \
--domain-cert=certs/mydomain/mydomain-cert.pem \
--domain-key=certs/mydomain/mydomain-key.pem \
--domain-key-pass-file=/tmp/pass.txt \
--factory-fed \
--parent-image=my-gw-base:1.0 \
--out-image=my-api-gateway:1.0
```

This example creates an API Gateway Docker image named `my-api-gateway` with a tag of `1.0`. This image has the following characteristics:

* Based on the `my-gw-base:1.0` image
* Uses a specified certificate and key
* Uses a default factory `fed`

### Create a FIPS-enabled API Gateway image

The following example creates an API Gateway Docker image that runs in FIPS-compliant mode.

You must have a valid FIPS-compliant mode API Gateway license file to create an image that can run in FIPS-compliant mode.

```
cd emt_containers-<version>
./build_gw_image.py \
--license=/tmp/api_gw_fips.lic \
--domain-cert=certs/mydomain/mydomain-cert.pem \
--domain-key=certs/mydomain/mydomain-key.pem \
--domain-key-pass-file=/tmp/pass.txt \
--parent-image=my-gw-base:1.0 \
--out-image=my-fips-api-gateway:1.0 \
--fips
```

This example creates an API Gateway Docker image named `my-fips-api-gateway` with a tag of `1.0`. This image has the following characteristics:

* Based on the `my-gw-base:1.0` image.
* Uses a specified certificate and key.
* Runs in FIPS-compliant mode.

### Create an API Gateway image using existing fed and customized configuration

The following example creates an API Gateway Docker image using an existing API Gateway deployment package (`fed` file) and customized configuration from an existing API Gateway installation.

```
cd emt_containers-<version>
./build_gw_image.py \
--license=/tmp/api_gw.lic \
--domain-cert=certs/mydomain/mydomain-cert.pem \
--domain-key=certs/mydomain/mydomain-key.pem \
--domain-key-pass-file=/tmp/pass.txt \
--parent-image=my-gw-base:1.0 \
--fed=my-group-fed.fed --fed-pass-file=/tmp/my-group-fedpass.txt \
--group-id=my-group --merge-dir=/tmp/apigateway
```

This example creates an API Gateway Docker image named `api-gateway-my-group` with a tag of `latest`. This image has the following characteristics:

* Based on the `my-gw-base:1.0` image.
* Uses a specified certificate and key.
* Uses a specified `fed` that contains API Gateway 7.8 configuration.
* Belongs to the API Gateway group `my-group`. All containers started from this image belong to this group.
* Uses a specified merge directory that is merged into the API Gateway image.

Ensure that your `fed` contains the following:

* API Gateway version 7.8 configuration.
* You can upgrade existing projects (from version 7.5.1 or later) using `projupgrade`, see [Upgrade an API Gateway project](/csh?context=461&product=prod-api-gateway-77)
        in the [API Gateway DevOps Deployment Guide](/bundle/APIGateway_77_PromotionGuide_allOS_en_HTML5/).
* You can also upgrade existing `fed` files using Policy Studio or `upgradeconfig`, see the [API Gateway Upgrade Guide](/bundle/APIGateway_77_UpgradeGuide_allOS_en_HTML5).
* Only IP addresses that are accessible at runtime. For example, the `fed` cannot contain IP addresses of container-based Admin Node Managers and API Gateways, as IP addresses are usually dynamically assigned in a Docker network.

Use the `--merge-dir` option to add more files and folders to the `apigateway` directory inside the image:

* The merge directory must be called `apigateway` and must have the same directory structure as in an API Gateway installation.
* For example, to add an optional custom `envSettings.props` file to your image, copy `envSettings.props` to a new directory named `/tmp/apigateway/groups/emt-group/emt-service/conf/`, and specify `/tmp/apigateway` to the `--merge-dir` option.
* To add custom JAR files to your image, copy the JAR files to a new directory named `/tmp/apigateway/ext/lib/`, and specify `/tmp/apigateway` to the `--merge-dir` option.

{{< alert title="Note" color="primary" >}}`envSettings.props` specifies settings such as the port the Admin Node Manager listens on (default of `8090`), and the session timeout for API Gateway Manager (default of 12 hours). `envSettings.props` must contain only IP addresses and host names that are accessible at runtime. It cannot contain IP addresses of container-based Admin Node Managers and API Gateways because these are usually dynamically assigned in a Docker network.{{< /alert >}}

## Start the API Gateway Docker container

Use the `docker run` command to start the API Gateway container.

### Start an API Manager-enabled API Gateway container

The following example shows how to run an API Manager-enabled API Gateway container in the background on a specific port:

```
docker run -d --name=apimgr \
--network=api-gateway-domain \
-p 8075:8075 -p 8065:8065 -p 8080:8080 \
-v /tmp/events:/opt/Axway/apigateway/events \
-e EMT_ANM_HOSTS=anm:8090 \
-e CASS_HOST=casshost1 \
-e METRICS_DB_URL=jdbc:mysql://metricsdb:3306/metrics?useSSL=false \
-e METRICS_DB_USERNAME=root -e METRICS_DB_PASS=my_db_pwd \
-e EMT_TRACE_LEVEL=DEBUG \
api-gateway-my-group:1.0
```

This example performs the following:

* Starts an API Manager-enabled container from an image named `api-gateway-my-group:1.0`. You must specify the name of the API Gateway Docker image that you created in [Create an API Gateway Docker image](#create-an-api-gateway-docker-image).
* Runs the container in the background using the `-d` option.
* Binds the default traffic port `8080` of the container to port `8080` on the host machine, which enables you to test the API Gateway on your host machine.
* Mounts the `/tmp/events` host directory in the container using the `-v` option. This directory contains API Gateway transaction event logs. For more details, see [Mount volumes to persist logs outside the API Gateway container](#mount-volumes-to-persist-logs-outside-the-api-gateway-container). For best practice, you can parametrize this directory by way of the `quickstart.sh` script included in the Docker scripts package.
* Sets the `CASS_HOST` environment variable with the Apache Cassandra host that is used to store the API Manager data.
* Uses `METRICS_DB_URL`, `METRICS_DB_USERNAME` and `METRICS_DB_PASS` environment variables to specify connection details for the metrics database.
* Uses an environment variable `EMT_TRACE_LEVEL` to set a trace level inside the container. In the above example a trace level switches from INFO to DEBUG level during container startup.
* Sets the `EMT_ANM_HOSTS` environment variable to `anm:8090` in the container. This enables the API Gateway to communicate with the Admin Node Manager container on port `8090`. The API Gateway is now visible in the API Gateway Manager topology view.

![API Gateway container in topology view](/Images/ContainerGuide/gw_mgr_topology.png)

### Start an API Gateway container with topology logging enabled

If you have started an Admin Node Manager with topology logging enabled, you must specify the gateway host identity in the `EMT_PARENT_HOST` environment variable to identify the host of the log records, and restart the host.

The following example shows how to set `acme-DC1-server2` as the name of the parent host in the topology log records.

```
docker run -e EMT_PARENT_HOST=acme-DC1-server2 ... api-gateway-my-group:1.0
```

{{< alert title="Note" color="primary" >}} If you do not specify this variable, the topology log records for the gateway will contain the value `<UNKNOWN>` for the parent host.{{< /alert >}}

### Mount volumes to persist logs outside the API Gateway container

You can persist API Gateway trace and event logs to a directory on your host machine. For example, run the following `docker run` command to start an API Gateway container from an image named `api-gateway-my-group:1.0` and mount volumes for trace and event logs:

```
docker run -it -v /tmp/events:/opt/Axway/apigateway/events \
-v /tmp/trace:/opt/Axway/apigateway/groups/emt-group/emt-service/trace \
-e EMT_ANM_HOSTS=anm:8090 \
-p 8080:8080 \
--network=api-gateway-domain \
api-gateway-my-group:1.0
```

This example starts the API Gateway container and writes the trace and log files to `/tmp/events` and `/tmp/trace` on your host machine. The trace and log files contain the container ID of the API Gateway container in the file names.

{{< alert title="Note" color="primary" >}}To enable an Admin Node Manager container to process the event logs from API Gateway containers, you must run the Admin Node Manager container with the same volume mounted. For more details, see [Create a metrics-enabled ANM image](/docs/container_topics/containers_docker_setup/docker_script_anm/#create-a-metrics-enabled-admin-node-manager-image) and [Start a metrics-enabled Admin Node Manager container](/docs/container_topics/containers_docker_setup/docker_script_anm/#start-a-metrics-enabled-admin-node-manager-container).{{< /alert >}}

### Start a deployment-enabled API Gateway container in a development environment

The following simple example sets the `EMT_DEPLOYMENT_ENABLED` environment variable to `true` to enable you to deploy configuration directly from Policy Studio to the running API Gateway container:

```
docker run -d \
-e EMT_DEPLOYMENT_ENABLED=true \
-e EMT_ANM_HOSTS=anm:8090 \
-p 8080:8080 \
--network=api-gateway-domain api-gateway-my-group:1.0
```

{{< alert title="Caution" color="warning" >}}
The `EMT_DEPLOYMENT_ENABLED` environment variable is provided as a convenience for development environments only:

* Do not set `EMT_DEPLOYMENT_ENABLED=true` on production systems. In production environments, to deploy changes in API Gateway configuration, you must export a `.fed` file from Policy Studio, rebuild the API Gateway Docker image, and restart the API Gateway Docker container.
* The `EMT_DEPLOYMENT_ENABLED=true` setting only enables you to deploy changes to a running container from Policy Studio. You cannot deploy changes using the API Gateway Manager, `managedomain`, or `projdeploy` tools.
{{< /alert >}}

## Further information

For more information on the environment variables that you can specify at runtime, see [Environment variables reference](/docs/container_topics/container_env_variables/#environment-variables-reference).
