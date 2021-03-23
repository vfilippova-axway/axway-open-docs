---
title: API Portal 7.7 March 2021 Release Notes
linkTitle: API Portal March 2021
weight: 95
date: 2021-01-06
---
## Summary

API Portal provides an API consumer-facing interface that you can customize to match your corporate brand. API Portal is a layered product linked to API Manager, and requires both API Manager and API Gateway. For more information, see the API Gateway and API Manager documentation.

## Installation

API Portal is available as a software installation or a virtualized deployment in a Docker container. For more information, see the following options:

* If you are installing API Portal for the first time using this update, see [Install API Portal](/docs/apim_installation/apiportal_install/)
* If you are already using API Portal (7.5.x, 7.6.x, 7.7.x) and want to install this update, see [Upgrade API Portal](/docs/apim_installation/apiportal_install/upgrade_automatic/)
* If you want to deploy API Portal in Docker containers, see [Deploy API Portal in containers](/docs/apim_installation/apiportal_docker/docker_portal_upgrade/)
* If you are already using API Portal in Docker containers and you want to install this update, see [Upgrade API Portal in Docker containers](/docs/apim_installation/apiportal_docker/docker_portal_upgrade/)

## New features and enhancements

### Unattended mode enhanced to use a configuration file for parameters

API Portal unattended mode can use up to 24 parameters, all of which must be specified on the command line. From this update, this was made easier. You can specify the parameters in a configuration file by using the `--optionfile` parameter with API Portal `install` and `uninstall` scripts. For more information, see [Unattended installation](/docs/apim_installation/apiportal_install/install_unattended/).

You can also watch [How to use a configuration file for simpler installations (unattended mode)](https://youtu.be/HqQ77Cj2s5E) demo video on Axway API Management YouTube chanel.

### Further Enhancements

* New module allowing easy integration with Intercom to enable chat support on API Portal.
* Microsoft Edge browser is now supported.
* New Axway icons, color palettes, and typography incorporated.
* The name of the relevant API Manager instance is now displayed on the **API catalog** (on Grid and List view layouts) and in the **API details** page.
* A new notification has been added to alert the organization administrator on login to review applications that are pending approval.
* Labels and values for custom properties are now translatable.

## Limitations of this update

This update has the following limitations:

* API Portal 7.7.20210130 is compatible with API Gateway and API Manager 7.7.20210130 only.
* To upgrade from earlier versions (for example, 7.5.5, 7.6.2) you must first upgrade to API Portal 7.7. For more information see [API Portal single version upgrade](/docs/apim_installation/apiportal_install/upgrade_automatic/#upgrade-from-api-portal-7-6-2) to upgrade versions incrementally.
* You can use the [cumulative upgrade script](/docs/apim_installation/apiportal_install/upgrade_automatic/#upgrade-api-portal-using-the-cumulative-upgrade-script) to upgrade directly from earlier versions (for example, 7.5.5, 7.6.2) to API Portal [7.7 November](/docs/apim_relnotes/20201130_apip_relnotes/), then apply this update package to update your API Portal to the January 21 release.
* This update is not available as a virtual appliance or as a managed service on Axway Cloud.

## Important changes

<!-- It is important, especially when upgrading from an earlier version, to be aware of the following changes in the behavior or operation of the product in this update.. -->

* Internet Explorer 11 and earlier versions are no longer supported. Microsoft Edge is recommended.

## Deprecated features

<!-- As part of our software development life cycle we constantly review our API Management offering. In this update, the following capabilities have been deprecated. -->

No features were deprecated in this release.

## Removed features

<!-- To stay current and align our offerings with customer demand and best practices, Axway might discontinue support for some capabilities. As part of this review, the following features have been removed: -->

No capabilities have been removed in this update.

## Fixed issues

This version of API Portal includes:

* Fixes from all 7.5.5, 7.6.2, and 7.7 service packs released prior to this version. For details of all the service pack fixes included, see the corresponding *SP Readme* attached to each service pack on [Axway Support](https://support.axway.com).
* Fixes from all 7.7 updates released prior to this version. For details of all the update fixes included, see the corresponding [release note](/docs/apim_relnotes/) for each 7.7 update.

### Fixed security vulnerabilities

table of security issues

### Other fixed issues

table of other issues

## Known issues

The following are known issues for this update.

### When Multi Manager feature is configured, API Portal users are no longer able to login

After a recent bug fix in API Manager (RDAPI-20021), the `Authenticate to Master` policy is no longer working after upgrading from releases earlier than [API Portal July 2020](/docs/apim_relnotes/20200730_apip_relnotes/). To fix this, perform the following steps:

1. Open all slave managers configurations in Policy Studio, and click to **Edit** the `AuthenticateToMaster` policy.
2. Click the **Login to Master** (Connect to URL) filter, and enter `Accept: */*` for the **Request Protocol Header**.
3. Click the **Enter** key twice to create two blank lines after `Accept: */*`.

Alternatively you can take the updated `AuthenticateToMaster` policy and apply again your configurations.

Related Issue:IAP-3435

### Page layout and alignment for Arabic language

Changing the API Portal language to Arabic (or any other right to left language) results in issues with page layout and alignment on the API Portal Home and Pricing pages, and some buttons are not visible. As a workaround, you can turn on the development mode in JAI. Follow these steps:

1. Log in to Joomla! Admin Interface (JAI).
2. In the JAI top navigation bar, click **Extensions > Templates**.
3. Click your template style (for example, `purity_III * Default`) to open it.
4. Click the **General** tab.
5. Change **Development Mode** to `ON`.
6. Click **Save** and click **Close** to close the template style.

Related Issue: IAP-308

## Documentation

This section describes documentation enhancements and related documentation.

### Documentation enhancements

There are no major changes in this update.

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