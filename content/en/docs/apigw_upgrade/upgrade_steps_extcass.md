{
    "title": "Upgrade from API Gateway 7.5.x or 7.6.x",
    "linkTitle": "Upgrade from API Gateway 7.5.x or 7.6.x",
    "weight": 2,
    "date": "2019-10-07",
    "description": "Upgrade from API Gateway 7.5.1 or later to API Gateway 7.8."
}

In API Gateway 7.5.1 and later versions, the Apache Cassandra database is fully separated from the API Gateway (in earlier versions it was embedded with the API Gateway). This simplifies the upgrade process when upgrading from API Gateway 7.5.1 or later, as the data contained in Apache Cassandra does not need to be exported and imported along with the other configuration data.

In an upgrade from API Gateway 7.5.1 and later versions:

* Your existing Apache Cassandra deployment can remain in place for use with API Gateway 7.8. There is no need to install a new Apache Cassandra deployment.
* No data changes are necessary in the Apache Cassandra database, which means it can remain running throughout the upgrade, serving any upgraded API Gateways when they come online.

{{< alert title="Caution" color="warning" >}}

* During an upgrade, including zero downtime upgrade (ZDU), quotas are reset, meaning that your quotas might be too lenient for a period after the upgrade.
* During a ZDU the old quota count and new quota count are in use at the same time.
* Quota could be reduced during ZDU to protect individual instances from being overloaded.
{{< /alert >}}

## Before you upgrade

This section provides a checklist of the tasks that you must perform on your old API Gateway installation, and on your new API Gateway 7.8 installation, before you upgrade.

{{< alert title="Note" color="primary" >}}API Gateway 7.8 supports Apache Cassandra version 2.2.12. If you are upgrading from API Gateway 7.5.x (which supported Apache Cassandra 2.2.5 and 2.2.8) it is recommended that you upgrade Apache Cassandra to version 2.2.12 *before* you upgrade to API Gateway 7.8. For more information on upgrading Apache Cassandra, see [Upgrade Apache Cassandra](/docs/apigw_upgrade/upgrade_cassandra/).{{< /alert >}}

### Checklist for the old API Gateway installation

Perform the following in your old API Gateway installation.

#### Back up the old API Gateway installation

Back up the old API Gateway installation on each node. At a minimum:

* Back up the `apigateway` directory.
* Back up any databases used by API Gateway. This includes external databases used for OAuth or KPS, and your metrics database if this has been configured (for example, for monitoring in API Gateway Analytics or API Manager).

#### Check that old API Gateway groups are consistent

Before you upgrade, ensure that all API Gateway groups in the old installation are consistent, meaning that all API Gateways in a group have the same configuration deployed. Upgrade is not supported for inconsistent groups.

You can use Policy Studio or API Gateway Manager to deploy configuration to API Gateway groups.

#### Do not update the old API Gateway installation

Do not make any changes to the old API Gateway installation after the upgrade process has begun. For example, if you have run any `sysupgrade` commands, do not perform any of the following on the old installation:

* Do not make any topology changes (for example, add new API Gateway instances)
* Do not deploy any configuration
* Do not update the API Gateway admin user store
* Do not update API Manager configuration (for example, add new APIs, organizations, or applications)

See also [What happens if you change the old API Gateway installation after running export?](/docs/apigw_upgrade/upgrade_faq/#what-happens-if-you-change-the-old-api-gateway-installation-after-running-export)

#### Check that ext/lib customizations in the old installation are compatible

If you have customizations (for example, third-party JAR files) in the `ext/lib`
directory of your old API Gateway installation, the `sysupgrade` command copies any third-party JARs to the new installation. However, you must verify that all third-party JARs are compatible with API Gateway 7.8.

Before you upgrade, you should confirm that any third-party JARs are not already present in the new installation under directory `apigateway/system/lib`. You should also test any custom JARs to ensure that they work correctly in API Gateway 7.8.

#### Upgrade API Gateway Analytics version 7.4.0 or later

If you are using API Gateway Analytics version 7.4.0 or later, you can upgrade API Gateway Analytics before you run the `sysupgrade` command. For more information, see [Upgrade API Gateway Analytics](/docs/apigw_upgrade/upgrade_analytics/).

If you are using a metrics database with API Manager and not API Gateway Analytics, see [Upgrade your metrics database for API Manager](/docs/apigw_upgrade/upgrade_metrics/).

#### Identify components and configuration requiring manual upgrade steps

Not all components and configuration from earlier API Gateway versions can be upgraded automatically with the `sysupgrade` command. However, you can upgrade these items manually.

Check your old installation and identify if you are using any of the following:

* Redaction files – For more information on migrating redaction files, contact Axway Support.
* Customizations to OAuth sample `.md` files – For more information on upgrading these files, contact Axway Support.
* API firewalling – For more information on upgrading API firewalling, contact Axway Support.
* QuickStart tutorial – For more information on migrating the QuickStart tutorial, see [Migrate the QuickStart tutorial](#migrate-the-quickstart-tutorial).
* API Gateway services – If you are running API Gateway processes as services on Linux, you must upgrade these manually. See [Upgrade services](#upgrade-services).

### Checklist for the new API Gateway 7.8 installation

Perform the following in your new API Gateway 7.8 installation.

#### Install the latest service pack

Install the latest available service pack for your new installation. Service packs are available from [Axway Support](https://support.axway.com/).

#### Do not start any Node Managers or API Gateways in the new installation

Do not create or start any Node Managers, groups, or API Gateways in the new installation. These are started automatically by the `sysupgrade` process.

#### Open the new Apache Cassandra client port in the firewall

API Gateway version 7.8 includes the Datastax Cassandra client, which uses a default port of 9042 to communicate with Cassandra over the native protocol. Earlier API Gateway versions included the Hector Cassandra client, which used a default port of 9160 to communicate with Cassandra over the Apache Thrift protocol.

When upgrading from 7.5.1 or later to API Gateway 7.8 all Cassandra hosts are updated to use port 9042 for client communication. You must open the port 9042 on your firewall to enable API Gateway to communicate with Apache Cassandra.

Alternatively, to continue to use the same port as you used in your old installation, you can perform some manual steps after the upgrade completes. For more information, see [Configure a different Apache Cassandra client port](#configure-a-different-apache-cassandra-client-port).

#### Move third-party JDBC JARs to the new installation

If your old API Gateway installation uses external third-party databases for OAuth and KPS, you must copy the JDBC JAR files to the following location in your new 7.8 installation:

```
/apigateway/upgrade/lib
```

For example, if your new installation is at `/opt/Axway/7.8`, copy the JDBC drivers to `/opt/Axway/7.8/apigateway/upgrade/lib`.

This enables the `sysupgrade apply` step to upgrade the databases.

## Single-node upgrade example

This section provides an example of a single-node domain upgrade from API Gateway version 7.5.x or 7.6.x (in this case, 7.5.1) to API Gateway 7.8.

{{< alert title="Tip" color="primary" >}}You can use the steps in this example as a guide when upgrading a single-node domain from API Gateway 7.5.x or 7.6.x to 7.8. However, you must remember to modify the steps appropriately for your version and topology.{{< /alert >}}

### Sample single-node upgrade topology

In this example, API Gateway has a single-node topology that includes one Admin Node Manager and one API Gateway instance. There is a single API Gateway group.

The sample topology is as follows:

![Single-node topology](/Images/UpgradeGuide/APIgw_single%20node%20upgrade%20topology.png)

### Check the old installation

Perform the checks on your old API Gateway 7.5.1 installation, as detailed in [Checklist for the old API Gateway installation](#checklist-for-the-old-api-gateway-installation).

### Install API Gateway 7.8

1. Select the **Custom** option in the installer and select to install the following components:

    * Admin Node Manager.
    * API Gateway Server.
    * Policy Studio – Select this only if you want to run Policy Studio on the local machine.
    * API Manager – Select this only if you are upgrading API Manager.

    Do not select the following components:

    * QuickStart tutorial - The QuickStart tutorial creates and starts processes in the new installation. `sysupgrade` requires that no processes are running in the new installation.
    * Cassandra - The external Cassandra configuration is retained when upgrading from 7.5.x or 7.6.x.

2. When prompted for an installation directory, enter a new directory (for example, `/opt/Axway-7.8`). A warning message displays if you try to install 7.8 in the same directory as the old installation.
3. When prompted to set an administrator user name and password, enter the same Admin Node Manager credentials that you use for the old API Gateway installation.

### Check the new installation

When the installation is complete, perform the new installation checks detailed in [Checklist for the new API Gateway 7.8 installation](#checklist-for-the-new-api-gateway-7-8-installation).

### Run `export` and `upgrade`

Before running `export`, ensure that the old API Gateway processes are running. This includes, for example, all Admin Node Managers, Node Managers, and API Gateway instances. These processes must be running in the old installation to export the API Gateway configuration data.

The following example shows how to run the `export` and `upgrade` commands:

```
cd /opt/Axway-7.8/apigateway/upgrade/bin
./sysupgrade export --old_install_dir /opt/Axway-7.5.1/apigateway/
./sysupgrade upgrade
```

If there are any errors or warnings, you are prompted to examine the `sysupgrade` log files. For more details on errors and warnings that can occur during upgrade and recommended actions, see [sysupgrade error reference](/docs/apigw_upgrade/upgrade_errors/). You can also use Policy Studio to resolve issues with the configuration. For more details, see [Resolve upgrade issues in Policy Studio](/docs/apigw_upgrade/upgrade_log_analysis_ps/).

You must resolve any errors before proceeding. You can rerun `export` and `upgrade` multiple times until all issues are resolved.

### Run `apply`

Before running `apply`, stop the API Gateway processes in the old installation and ensure that Cassandra is already running.

The following example shows how to run the `apply` command:

```
cd /opt/Axway-7.8/apigateway/upgrade/bin
./sysupgrade apply
```

This upgrades the external OAuth and KPS databases (if necessary), creates a new system that matches the old topology, and imports the upgraded data.

When all steps have completed successfully, the new API Gateway version 7.8 processes should be running.

### Verify the upgrade

To verify that the upgrade has been successful:

* Connect to API Gateway Manager (for example, on `https://HOST:8090/`), and view the API Gateway group topology, administrator users, and Key Property Stores.
* Start Policy Studio, and create a new project based on the running API Gateway. You can view the upgraded configuration (for example, policies, settings, and so on).
* If you were using OAuth client applications in your old installation, start the Client Application Registry web interface, and view the client applications.

## Multi-node upgrade example

This topic provides an example of a multi-node domain upgrade from API Gateway version 7.5.x or 7.6.x (in this case, 7.5.1) to API Gateway 7.8.

{{< alert title="Tip" color="primary" >}}You can use the steps in this example as a guide when upgrading a multi-node domain from API Gateway 7.5.x or 7.6.x to 7.8. However, you must remember to modify the steps appropriately for your version and topology.{{< /alert >}}

The following diagram shows an example flow for a multi-node upgrade.

![Example multi-node upgrade flow](/Images/UpgradeGuide/APIgw_sample_flow.png)

### Sample multi-node upgrade topology

In this example, API Gateway has a three-node topology that includes two Admin Node Managers (on NodeA and NodeC), and one Node Manager (on NodeB). A single API Gateway instance runs on each node. There are two API Gateway groups as follows:

* `GatewayGroup` has an API Gateway instance running on NodeA and NodeB.
* `GatewayManagerGroup` has a single API Manager-enabled API Gateway instance running on NodeC.

The sample topology is as follows:

![Example multi-node topology](/Images/UpgradeGuide/APIgw_upgrade%20topology.png)

* API Gateway NodeA: This node runs an Admin Node Manager and a single API Gateway named `APIGateway1` in a group named `GatewayGroup`.
* API Gateway NodeB: This node runs a Node Manager and a single API Gateway named `APIGateway2` in a group named `GatewayGroup`.
* API Manager-enabled NodeC: This node runs an Admin Node Manager and a single API Manager-enabled API Gateway named `APIManagerGateway` in a group named `GatewayManagerGroup`.

### Check the old installation on each node

Perform the checks on your old API Gateway 7.5.1 installation, as detailed in [Checklist for the old API Gateway installation](#checklist-for-the-old-api-gateway-installation).

### Install API Gateway 7.8 on each node

Install API Gateway 7.8 on each node in the multi-node topology where your old API Gateway domain is running.

1. Select the **Custom** option in the installer and select to install the following components:

    * Admin Node Manager – Select this on NodeA, NodeB and NodeC for the sample topology.
    * API Gateway Server – Select this on NodeA, NodeB and NodeC for the sample topology. If you are installing on a node that does not run any API Gateways (running an Admin Node Manager only), do not select this.
    * Policy Studio – Select this on the nodes on which you will run Policy Studio.
    * API Manager – Select this on NodeC for the sample topology. Select it on other nodes if required.

    Do not select the following components:

    * QuickStart tutorial - The QuickStart tutorial creates and starts processes in the new installation. `sysupgrade` requires that no processes are running in the new installation.
    * Cassandra - The external Cassandra configuration is retained when upgrading from 7.5.x or 7.6.x.

2. When prompted for an installation directory, enter a new directory (for example, `/opt/Axway-7.8`). A warning message displays if you try to install 7.8 in the same directory as the old installation.

3. When prompted to set an administrator user name and password, enter the same Admin Node Manager credentials that you use for the old API Gateway installation.

### Check the new installation on each node

When the installation is complete, perform the new installation checks detailed in [Checklist for the new API Gateway 7.8 installation](#checklist-for-the-new-api-gateway-7-8-installation).

### Run `export` and `upgrade` on each node

Run the `export` and `upgrade` commands on each node (NodeA, NodeB, and NodeC). You can run and rerun these commands on NodeA, NodeB, and NodeC in any node order.

{{< alert title="Note" color="primary" >}}The sample topology has two Admin Node Managers (running on NodeA and NodeC). Because there are multiple Admin Node Managers, you must specify which Admin Node Manager to use for `export` with the `--anm_host` option, and this Admin Node Manager must also be the first node you upgrade. We recommend that you specify the first Admin Node Manager (see [Which is the first Admin Node Manager?](/docs/apigw_upgrade/upgrade_faq/#which-is-the-first-admin-node-manager)). The value of `--anm_host` must be an exact match of the host name in the topology (`--anm_host NodeA` in this case) and you must specify the same `--anm_host` on all nodes.{{< /alert >}}

Ensure that the old API Gateway processes are running on all nodes. This includes, for example, all Admin Node Managers, Node Managers, and API Gateway instances. These processes must be running in the old installation on all nodes to export the API Gateway configuration data.

The following example shows how to run the `export` and `upgrade` commands on each node:

```
cd /opt/Axway-7.8/apigateway/upgrade/bin
./sysupgrade export --old_install_dir /opt/Axway-7.5.1/apigateway/ --anm_host NodeA
./sysupgrade upgrade --cass_host NodeA
```

The commands are exactly the same on each node. You must specify `--anm_host Node A` as a parameter to the `export` command on NodeA, NodeB, and NodeC.

The sample topology uses Apache Cassandra to store API Manager data, and you must specify the host name or IP address of the new external Cassandra database cluster to the `upgrade` command. If you are not using Apache Cassandra, you must run the `upgrade` command with the `--no_cassandra` option

If there are any errors or warnings, you are prompted to examine the `sysupgrade` log files. For more details on errors and warnings that can occur during upgrade and recommended actions, see [sysupgrade error reference](/docs/apigw_upgrade/upgrade_errors/). You can also use Policy Studio to resolve issues with the configuration. For more details, see [Resolve upgrade issues in Policy Studio](/docs/apigw_upgrade/upgrade_log_analysis_ps/).

You must resolve any errors before proceeding. You can rerun `export` and `upgrade` multiple times until all issues are resolved.

### Run `apply` on the first Admin Node Manager

Run `apply` on the first Admin Node Manager node (NodeA).

Before running `apply`, stop the API Gateway processes in the old installation on NodeA, NodeB, and NodeC, and ensure that Cassandra is already running.

The following example shows how to run the `apply` command on NodeA:

```
cd /opt/Axway-7.8/apigateway/upgrade/bin
./sysupgrade apply --anm_host NodeA
```

The version 7.8 Admin Node Manager and API Gateway are now running on NodeA. You can launch the version 7.8 API Gateway Manager web console on `https://NodeA:8090`.

### Run `apply` on the other nodes

Run `apply` on the other nodes in turn (NodeB and then NodeC).

The following example shows how to run the `apply` command on each of the other nodes:

```
cd /opt/Axway-7.8/apigateway/upgrade/bin
./sysupgrade apply --anm_host NodeA
```

`sysupgrade` is now complete on all nodes. All the API Gateway 7.8 processes are running on all nodes in the topology.

### Verify the multi-node upgrade

Verify the upgrade as detailed in [Verify the upgrade](#verify-the-upgrade).

For the sample topology you can also perform the following checks to verify the API Manager upgrade:

1. Connect to the API Manager web console (for example, on `https://HOST:8075/`).
2. Log in as an administrator user and view the organizations, application developers, and applications.
3. Log in as a non-administrator user and view the applications.

## After you upgrade

This section includes post-upgrade steps that you might need to perform after running `sysupgrade` to upgrade from API Gateway 7.5.x or 7.6.x to 7.8.

### Configure a different Apache Cassandra client port

If you upgraded from API Gateway 7.5.1 or later to version 7.8 all Cassandra hosts are updated to use port 9042 for client communication. To use a different port (for example, to use the same port as you used in your old installation), follow these steps:

1. For each Cassandra host, update the setting `native_transport_port` in the `CASSANDRA_HOME/conf/cassandra.yaml` file. Set the value to the port number to use for client communication.
2. Update the details for each Cassandra host in Policy Studio. Select **Server Settings > Cassandra > Hosts**, and update the port for each host.

{{< alert title="Note" color="primary" >}}If you change the configuration to use the same port as you used in your old installation, you cannot leave any API Gateways in your old installation running during the `apply` step, as the port uses a different Cassandra protocol.{{< /alert >}}

### Upgrade API Gateway projects

Each API Gateway group has a configuration that is typically deployed as a `.fed` file. When you upgrade from an earlier version of API Gateway, configuration for all API Gateway groups is automatically upgraded during `sysupgrade`. However, you might have configuration files that were originally created in Policy Studio in a development environment that also need to be upgraded. You can upgrade the configuration in your development environment in one of the following ways:

* In Policy Studio:
    * Choose the **From an API Gateway instance** option to create a new project from the configuration in an already upgraded API Gateway.
    * Choose the **From existing configuration** option to create a new project from an old configuration. The configuration is upgraded to version 7.8 automatically.

    For more information on creating projects in Policy Studio, see the .

* If you upgraded from version 7.5.1 or later and you have several projects to upgrade (these projects might be independent of one another, or could include shared projects and their dependencies), you can use the `projupgrade` tool. This tool upgrades several projects at once. For more information, see [Upgrade an API Gateway project](/csh?context=461&product=prod-api-gateway-77) in the [API Gateway DevOps Deployment Guide](/bundle/APIGateway_77_PromotionGuide_allOS_en_HTML5/).

### Upgrade services

If you were running the API Gateway and Node Manager processes as services in your old installation, you must update the service scripts manually after the upgrade completes.

Complete the following steps after running `sysupgrade apply`:

1. Switch user to `root` to enable you to modify files in `/etc/init.d`. Typically, Axway services file names start with `vshell-`.
2. Edit the Node Manager script and update the `VDISTDIR` variable to point to the `apigateway` folder in the new installation.
    For example, on a machine called XUbuntu02, edit the file `/etc/init.d/vshell-Node-Manager-on-XUbuntu02`.
    * Update the `VDISTDIR` variable (for example, change `VDISTDIR="/opt/Axway-7.2.2/apigateway` to `VDISTDIR="/opt/Axway-7.8/apigateway`).
3. Edit each of the relevant API Gateway scripts, and update the `VDISTDIR` and the `VINSTDIR` variables to point to the `apigateway` folder in the new installation.
    For example, on a machine called XUbuntu02 with one API Gateway called `Gateway1` that is a member of a group called `Default Group`, edit the file `/etc/init.d/vshell-Default-Group-Gateway1`.
    * Update the `VDISTDIR` variable (for example, change `VDISTDIR="/opt/Axway-7.2.2/apigateway` to `VDISTDIR="/opt/Axway-7.8/apigateway`).
    * Update the `VINSTDIR` variable (for example, change `VINSTDIR="/opt/Axway-7.2.2/apigateway/groups/group-2/instance-1` to `VINSTDIR="/opt/Axway-7.8/apigateway/groups/group-2/instance-1`).
4. Save the changes to the files and restart the machine. When the machine restarts the new services are started.

Alternatively, your Linux administrator can remove the old services using the preferred Linux utility and delete the old `init.d` service files, and you can use `managedomain` to recreate the services after running `sysupgrade`.

### Migrate the QuickStart tutorial

`sysupgrade` does not migrate the Quickstart tutorial from your old installation. To migrate it, copy the `/apigateway/webapps/quickstart` directory from your old installation (for example, `/opt/Axway/7.4.1/apigateway/webapps/quickstart`) to the same location in the new 7.8 installation (for example, `/opt/Axway/7.8/apigateway/webapps/quickstart`).
