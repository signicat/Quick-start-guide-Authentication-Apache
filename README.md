# Quick-start-guide-Authentication-Apache
Configure apache with openid connect using the demo service in Signicat

Assuming you're on debian buster.

::: tip Using Docker?
If you have docker installed and want to run this guide in docker run the following command, and do all the remaining steps in the resulting shell
`docker run -ti -p 5000:5000 debian:buster`
:::


Install necessary packages:
```
apt-get update&& apt-get install -y --no-install-recommends apache2 libapache2-mod-auth-openidc libapache2-mod-php ca-certificates
```


Edit /etc/apache2/ports.conf and add the following line at the bottom of the file:
```apacheconf
Listen 5000
```

Add the following content to a new file called /etc/apache2/sites-available/signicat-quickstart.conf

```apacheconf
<VirtualHost *:5000>
    OIDCProviderMetadataURL https://preprod.signicat.com/oidc/.well-known/openid-configuration
    OIDCClientID demo-preprod
    OIDCClientSecret mqZ-_75-f2wNsiQTONb7On4aAZ7zc218mrRVk1oufa8
#    OIDCPathAuthRequestParams acr_values=signicat:nbid
    OIDCProviderTokenEndpointAuth client_secret_basic
    OIDCScope "openid profile"

    OIDCRedirectURI http://localhost:5000/redirect
    OIDCCryptoPassphrase SomeSecretValue

    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/html
    DirectoryIndex index.php
    LogLevel debug
    <Location />
        AuthType openid-connect
        Require valid-user
    </Location>
</VirtualHost>
```

Add the following to a new file called /var/www/html/index.php
```php
<?php
print("hello " . $_SERVER["OIDC_CLAIM_name"]);
```

Enable the site and start apache:
```
a2ensite signicat-quickstart
service apache2 start # if already running run: service apache2 reload
```

point your browser to http://localhost:5000

