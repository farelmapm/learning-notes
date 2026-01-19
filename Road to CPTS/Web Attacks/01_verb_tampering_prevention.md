# Verb Tampering Prevention

## Insecure Configuration
- Apache <br>
<ul>

Configurations are stored in files such as `000-default.conf`, `.htaccess`, etc.
```
<Directory "/var/www/html/admin">
    AuthType Basic
    AuthName "Admin Panel"
    AuthUserFile /etc/apache2/.htpasswd
    <Limit GET>
        Require valid-user
    </Limit>
</Directory>
```
Because `<Limit GET>` is used, `Require valid-user` will only apply to `GET` requests. Other methods are not covered and are therefore vulnerable to verb tampering. 
</ul>

- Tomcat <br>
<ul> 

Configurations are stored in files such as `web.xml`. <br>
```
<security-constraint>
    <web-resource-collection>
        <url-pattern>/admin/*</url-pattern>
        <http-method>GET</http-method>
    </web-resource-collection>
    <auth-constraint>
        <role-name>admin</role-name>
    </auth-constraint>
</security-constraint>
```
In this case, authorization is again limited only to the `GET` method with `http-method`.
</ul>

- ASP.NET<br> <ul>

Configurations are stored in files such as `web.config`.
```
<system.web>
    <authorization>
        <allow verbs="GET" roles="admin">
            <deny verbs="GET" users="*">
        </deny>
        </allow>
    </authorization>
</system.web>
```
The `allow` and `deny` scope is limited to the `GET` method.
</ul>

If it is necessary to specify a single method, use safe keywords like `LimitExcept` in Apache, `http-method-omission` in Tomcat, and `add/remove` in ASP.NET. 

Generally, <b>consider disabling/denying all</b> `HEAD` <b>requests</b> unless specifically required by the web application.

## Insecure Coding
- PHP Example <ul>

```
if (isset($_REQUEST['filename'])) {
    if (!preg_match('/[^A-Za-z0-9. _-]/', $_POST['filename'])) {
        system("touch " . $_REQUEST['filename']);
    } else {
        echo "Malicious Request Denied!";
    }
}
```
Here, `preg_match` matches the filter with `$_POST['filename']`. If the request is a `GET` request, for example, `$_POST['filename']` is empty, so `preg_match` is filtering nothing, finds nothing, and continues to the `system` command. </ul>

- Java Example <ul>

`request.getParameter('param')`
</ul>

- C# Example <ul>

`Request['param']`
</ul>
