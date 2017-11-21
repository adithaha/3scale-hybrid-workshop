# LAB-2 APIcast on premise

This lab will provide you procedure on how to setup on premise APIcast with RHEL. This assume you already have configured API on 3Scale.

There are 3 main steps on how to setup APIcast:
1. Setup openresty
2. Setup APIcast
3. Run APIcast

In this lab, you will install APIcast v3.1.0 in directory /home/jboss/apicast using jboss user
----------------------------

1. Install required packages using root user
   ```sh
   su
   yum-config-manager --enable rhel-7-server-optional-rpms
   yum-config-manager --add-repo https://openresty.org/package/rhel/openresty.repo
   yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
   ```
2. Install OpenResty using root user
   ```sh
   su
   yum install httpd httpd-devel pcre pcre-devel libxml2-devel 
   yum install openresty
   yum install openresty-resty
   ```
3. Install apicast using jboss user
   ```sh
   cd /home/jboss
   mkdir apicast
   cd apicast
   wget https://github.com/3scale/apicast/archive/v3.1.0.zip
   unzip v3.1.0.zip
   cd apicast-3.1.0
   sudo luarocks make apicast/*.rockspec --tree /usr/local/openresty/luajit
   ```
4. Generate your 3scale token
   ```sh
   1. Go to https://<account>-admin.3scale.net
   2. On top right, click gear icon (setting) -> personal settings
   3. Tokens -> Add Access Token | name: APIcast service | Scope: check all | Permissions: read & write | Create Access token
   4. Save generated token to be used later
   ```
5. Start APIcast with debug log using jboss user with default port 8080
   ```sh
   cd /home/jboss/apicast/apicast-3.1.0
   THREESCALE_PORTAL_ENDPOINT=https://<token>@<account>-admin.3scale.net bin/apicast -v -v -v
   ```
6. Define APIcast self-managed in 3scale manager
   ```sh
   1. Go to https://<account>-admin.3scale.net
   2. APIs -> Integration -> edit integration settings
   3. Choose APIcast self-managed
   4. Go down, Authentication -> choose API key
   5. Update Service
   ```
7. Register APIcast self-managed into 3scale manager
   ```sh
   1. Go to https://<account>-admin.3scale.net
   2. APIs -> Integration -> edit APIcast configuration
   3. Staging Public Base URL: http://<ip-apicast>:8080
   4. Production Public Base URL: http://<ip-apicast>:8080
   5. Update the Staging Environment
   ```
8. Deploy into production
   ```sh
   1. Go to https://<account>-admin.3scale.net
   2. APIs -> Integration
   3. Environment -> Deploy into production
   ```
   
Test your API with command below:
```sh
curl "http://<ip-apicast>:8080/<complete path>"
```
