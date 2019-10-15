{
    "title": "Secure API Portal",
    "linkTitle": "Secure API Portal",
    "weight": "8",
    "date": "2019-08-09",
    "description": "Secure and harden your API Portal environment after installation."
}

Perform the following steps after installation to ensure that your API Portal environment is secure from internal and external threats:

1. Apply the latest service pack (SP) available for this version, as it might contain important security updates. For details, see [Update API Portal](/docs/apiportal_install/install_service_pack/).
2. Search Axway Support at [https://support.axway.com](https://support.axway.com/) for any KB articles relating to this version, as these might contain valuable security recommendations.
3. Change the default Joomla! administrator credentials for logging in to the Joomla! Administrator Interface (JAI) (`https://<API Portal host>/administrator`). Use a different user name and a strong password.
4. Complete all of the procedures detailed in the following sections.

## Configure the SSL certificate

To enable SSL on API Portal, you must configure Apache to use the correct certificate:

1. Open the `/etc/httpd/conf.d/apiportal.conf` file.
2. Change `SSLCertificateFile` and `SSLCertificateKeyFile` to point to your CA certificate and key files.
3. Restart Apache.

For more details on API Portal certificate management, see the [API Management Security Guide](/bundle/APIGateway_77_SecurityGuide_allOS_en_HTML5).

## Disable TLS 1.0 and TLS 1.1 on Apache

On an API Portal software installation, the Apache web server has TLS versions 1.0 and 1.1 enabled in addition to the TSL 1.2 that API Portal uses. Because TLS 1.0 and 1.1 have security vulnerabilities, it is recommended to disable them.

1. To check which TLS versions are enabled, scan your API Portal port: 
    ```
    sslscan <API Portal IP address>:<your https port>
    ```
   By default, API Portal uses port `443` for secure connections.
2. To disable TLS 1.0. and 1.1, open the following file: `/etc/httpd/conf.d/apiportal.conf`
3. Add the following SSL protocol definition for the secure connection:
   ```
   <VirtualHost *:443>
       SSLEngine on
       SSLCertificateFile "/etc/httpd/conf/server.crt"
       SSLCertificateKeyFile "/etc/httpd/conf/server.key"
       SSLProtocol TLSv1.2
       Header always append X-Frame-Options SAMEORIGIN
        ...
   </VirtualHost>
   ```
4. Restart Apache.
5. Run the `sslscan` again on your API Portal port to check that TLS 1.0 and 1.1 have been disabled.

## Protect Joomla! from direct Internet access

To counter a session fixation vulnerability in Joomla!, it is recommended that you protect the Joomla! Administrator Interface (JAI) from direct Internet access.

1. Open the file `/etc/httpd/conf.d/security.conf`.
2. Add an access restriction directive for the `/administrator` location. Specify the internal IP address range that is allowed to access JAI. For example:
   ```
   ServerTokens ProductOnly
   ServerSignature Off
       <Location /administrator>
           Order deny,allow
           deny from all
           allow from 10.232.14.
       </Location>
   ```
3. To restart the web server configuration, enter the following:
   ```
   # /etc/init.d/apache2 reload
   ```

## Limit the number of failed login attempts

To protect API Portal and Joomla! from brute force attacks, you can limit the number of failed login attempts that API Portal or Joomla! allows:

1. In the JAI, click **Components > API Portal > Login Protection**.
2. Click **Yes** to enable login protection for API Portal.
3. Enter a value for the number of failed login attempts before a ReCaptcha is displayed.
4. Enter a value for the number of failed login attempts before the user account is locked.
5. Enter a value in seconds for how long the user account is locked.
6. Click **Yes** to enable locking by IP address. When this setting is enabled login attempts are blocked from the same IP address for the lock time specified even if correct user credentials are entered.

    You can enable user account locking and IP address locking independently or in combination. For example, if you enable user account locking and IP address locking for 5 minutes after 2 failed login attempts, `UserA` will be locked for 5 minutes after entering 2 incorrect passwords, and any other user (for example, `UserB`) will also be unable to log in for 5 minutes from the same IP address, even if they provide correct user credentials.
7. Click **Save**.

## Add trusted OAuth hosts

To restrict API Portal users from accessing unauthorized OAuth endpoints, you can enter a list of permitted OAuth hosts in the OAuth whitelist:

1. In the JAI, click **Components > API Portal > Whitelisting**.
2. In the **OAuth Whitelist** field, enter the host names or IP addresses of the trusted OAuth hosts (separated by new lines). Do not enter API Manager hosts as these are added to the whitelist automatically.
3. Click **Save**.

If you do not add your trusted OAuth hosts to this field, all requests to those hosts will be rejected by API Portal.

## Add trusted API hosts

If you have APIs that are virtualized and published on a host other than an API Manager host, you can enter a list of permitted API hosts in the API whitelist:

1. In the JAI, click **Components > API Portal > Whitelisting**.
2. In the **API Whitelist** field, enter the host names or IP addresses of the trusted API hosts (separated by new lines). Do not enter API Manager hosts as these are added to the whitelist automatically.
3. Click **Save**.

If you do not add your trusted API hosts to this field, all requests to those hosts will be rejected by API Portal.

## Change the location of API Portal log files

By default, API Portal saves the Apache log files in the `htdocs` directory. For increased security, you can configure a different location to save the log files:

1. In the JAI, click **System > Global Configuration**.
2. On the **System** tab, enter the new location in the **Path to Log Folder** field. Apache must have permission to write to the new location.
3. Click **Save**.

## Configure Apache

### Update apiportal.conf

Add security headers to the `apiportal.conf` file (located in `/etc/httpd/conf.d/`).

In the virtual host directive add the following headers:

```
Header always append X-Frame-Options SAMEORIGIN

Header set X-XSS-Protection "1; mode=block"

Header always set Strict-Transport-Security "max-age=63072000; includeSubdomains;"

Header set X-Content-Type-Options nosniff
```

You should only use the HSTS header if you have configured SSL.

### Update security.conf

Ensure that the `security.conf` file (also located in `/etc/httpd/conf.d/`) exists and that it contains the following directives:

```
ServerTokens ProductOnly

ServerSignature Off

HostnameLookups Off

TraceEnable off

UseCanonicalName Off
```

### Set ServerName to proper FQDN

To protect your web server from a vulnerability giving remote attackers the ability to attain your internal IP address or internal network name, set `ServerName` to a proper FQDN.

### Restart Apache

Restart Apache after modifying the `apiportal.conf` and `security.conf` files.

## Configure PHP

Find the location of your `php.ini` file. For example, run the command:

```
php –i | grep php.ini
```

In the resulting list of files, the `php.ini` listed as the `Loaded Configuration File` is the correct file to edit.

Update the file with the following options:

```
- expose_php = 0

- display_errors = 0

- disable_functions = exec,passthru,shell_exec,system

- allow_url_include = 0

- session.cookie_httponly = 1

- session.cookie_secure = On

- open_basedir = “/opt/axway/apiportal/htdoc:/tmp”
```

You should only set `session.cookie_secure` to `On` if you have configured SSL.

Set `open_basedir` to a list of directories (use `:` to separate directories):

* API Portal root directory
* Value of `upload_tmp_dir` or `/tmp` if it is empty
* New location of log files if you changed them according to [Change the location of API Portal log files](#change-the-location-of-api-portal-log-files)

After updating `php.ini`, restart Apache.

## Configure MySQL

MySQL comes with a hardening script to check database server security and remove some default settings. You can run it with the command:

```
mysql_secure_installation
```

If you do not need to access your database from another machine, bind the MySQL service on localhost only (of the host from which you are going to access it). For example, edit the configuration file `my.cnf` and set:

```
bind-address = 127.0.0.1
```

The user for API Portal should only have access to those databases that it needs to run.

## Configure Joomla! Administrator Interface (JAI)

1. Log in to JAI.
2. Select **System > Global Configuration**.
3. Click the **Site** tab.
4. In **Cookie Settings** enter your API Portal domain name (for example, `myapiportal.com`) for **Cookie Domain** and enter `/` for **Cookie Path**.
5. In **Metadata Settings** set **Show Joomla Version** to `No`.
6. Click the **System** tab and in **Debug Settings** set **Debug System** to `No`.
7. Click the **Server** tab and in **Server Settings** set **Error Reporting** to `None` and **Force HTTPS** to `Entire site` (if you have configured SSL).
8. Select **Users** from the left navigation bar.
9. Click the **User Options** tab and set **Allow User Registration** to `No`.

## Do not allow web browsers to save login and password

When you log in to Joomla! Administrator Interface (JAI) do not allow the web browser to save or remember your login and password.

## Reject requests containing unexpected or missing content type headers

It is best practice to reject requests containing unexpected or missing content type headers with the HTTP response status `406 Unacceptable` or `415 Unsupported Media Type`.

The Content-Type header specifies what media type is being sent with the request. If the Content-Type header is missing, empty, or unexpected the server must refuse to serve the request with an appropriate response, as allowing the request might lead to Cross-Site Request Forgery (CSRF) or even remote code execution (RCE).

Add the configuration in your `.htaccess` file, virtual host file, or global web server configuration. The following code snippet gives an example for a server processing only `application/json` and `application/xml` data.

```
# Check if the Content-Type header is missing or empty
RewriteCond %{HTTP:Content-Type} ^$
# AND the method type is POST, PUT or PATCH
RewriteCond %{REQUEST_METHOD} ^(POST|PUT|PATCH) [OR]
# OR Content-Type header is present
RewriteCond %{HTTP:Content-Type} !^$
# AND Content-Type value doesn't match one of the following, case-insensitive
RewriteCond %{HTTP:Content-Type} !^(application/json|application/xml) [NC]
# Then redirect with response 415 Unsupported Media Type and stop processing other conditions
RewriteRule ^ - [R=415,L]
```

## Where to go next

For more information on securing the underlying API Manager and API Gateway, see [Manage API Gateway security](/csh?context=109&product=prod-api-gateway-77) in the [API Gateway Administrator Guide](/bundle/APIGateway_77_AdministratorGuide_allOS_en_HTML5/).

For more information on the security features of API Management products and best practices for strengthening their security, see the [API Management Security Guide](/bundle/APIGateway_77_SecurityGuide_allOS_en_HTML5).
