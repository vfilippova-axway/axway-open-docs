---
title: API Gateway and API Manager 7.7 January 2021 Release Notes
linkTitle: API Gateway and API Manager January 2021
weight: 100
date: 2021-01-06
---
## Summary

API Gateway is available as a software installation or a virtualized deployment in Docker containers. API Manager is a licensed product running on top of API Gateway, and has the same deployment options as API Gateway.

The software installation is available on Linux. For more details on supported platforms for software installation, see [System requirements](/docs/apim_installation/apigtw_install/system_requirements/).

Docker deployment is supported on Linux. For a summary of the system requirements for a Docker deployment, see [Set up Docker environment](/docs/apim_installation/apigw_containers/docker_scripts_prereqs/).

## New features and enhancements

The following new features and enhancements are available in this update.

### Option to disable SSL renegotiation is added for HTTPS listeners and Connection filters

We have added a new option, **Disable renegotiation in TLSv1.2 and earlier**, in Policy Studio to allow you to disable SSL renegotiation when configuring HTTPS interfaces or connecting to URL filters. This option is enabled by default, meaning that renegotiation is not allowed, and listeners do not send HelloRequest messages, and they ignore renegotiation requests via ClientHello.

For more information, see:

* [Configure Advanced SSL settings](/docs/apim_policydev/apigw_gw_instances/general_services/#configure-advanced-ssl-settings)
* [Connect to URL filter](/docs/apim_policydev/apigw_polref/routing_common/)

## Important changes

<!-- It is important, especially when upgrading from an earlier version, to be aware of the following changes in the behavior or operation of the product in this update.. -->

There are no major changes in this update.

## Deprecated features

<!-- As part of our software development life cycle we constantly review our API Management offering. In this update, the following capabilities have been deprecated. -->

No capabilities have been deprecated in this update.

## Removed features

No capabilities have been removed in this update.

<!-- To stay current and align our offerings with customer demand and best practices, Axway might discontinue support for some capabilities. As part of this review, the following features have been removed: -->

## Fixed issues

This version of API Gateway and API Manager includes:

* Fixes from all 7.5.3, 7.6.2, and 7.7 service packs released prior to this version. For details of all the service pack fixes included, see the corresponding *SP Readme* attached to each service pack on [Axway Support](https://support.axway.com).
* Fixes from all 7.7 updates released prior to this version. For details of all the update fixes included, see the corresponding [Release note](/docs/apim_relnotes/) for each 7.7 update.

### Fixed security vulnerabilities

placeholder

### Other fixed issues

placeholder

## Known issues

The following are known issues for this update.

placeholder

## Update a classic (non-container) deployment

To **update** your API Gateway, see [Update from API Gateway One Version](/docs/apim_installation/apigw_upgrade/upgrade_steps_oneversion/).

To **upgrade** from an older version, see [Upgrade from API Gateway 7.5.x or 7.6.x](/docs/apim_installation/apigw_upgrade/upgrade_steps_extcass/).

## Update a container deployment

Any custom `fed` files deployed to a container must be upgraded using [upgradeconfig](/docs/apim_installation/apigw_upgrade/upgrade_analytics#upgradeconfig-options) or [projupgrade](/docs/apim_reference/devopstools_ref#projupgrade-command-options). They must be upgraded the same way, regardless of whether they are API Manager enabled or not.

The `fed` now contains the updates for the API Manager configuration and can be used to build containers.

## Documentation

This section describes documentation enhancements and related documentation.

placeholder

### Related documentation

To find all available documentation for this product version:

1. Go to [Manuals on the Axway Documentation portal](https://docs.axway.com/bundle).
2. In the left pane Filters list, select your product or product version.

Customers with active support contracts need to log in to access restricted content.

The following reference documents are also available:

* [Supported Platforms](https://docs.axway.com/bundle/Axway_Products_SupportedPlatforms_allOS_en) - Lists the different operating systems, databases, browsers, and thick client platforms supported by each Axway product.
* [Interoperability Matrix](https://docs.axway.com/bundle/Axway_Products_InteroperabilityMatrix_allOS_en) - Provides product version and interoperability information for Axway products.

## Support services

The Axway Global Support team provides worldwide 24 x 7 support for customers with active support agreements.

Email [support@axway.com](mailto:support@axway.com) or visit [Axway Support](https://support.axway.com/).

See [Get help with API Gateway](/docs/apim_administration/apigtw_admin/trblshoot_get_help/) for the information that you should be prepared to provide when you contact Axway Support.