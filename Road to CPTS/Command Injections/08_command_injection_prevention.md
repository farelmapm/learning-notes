# Command Injection Prevention

## System Commands
Avoid using functions that execute system commands, especially if user input is allowed. 

## Input Validation
Always validate and then sanitize user input. 

## Input Sanitization
Remove any non-necessary special characters from the user input. 

## Server Configuration
- Use the web server's built-in Web Application Firewall (e.g., in Apache mod_security), in addition to an external WAF (e.g. Cloudflare, Fortinet, Imperva..)
- Abide by the Principle of Least Privilege (PoLP) by running the web server as a low privileged user (e.g. www-data)
- Prevent certain functions from being executed by the web server (e.g., in PHP disable_functions=system,...)
- Limit the scope accessible by the web application to its folder (e.g. in PHP open_basedir = '/var/www/html')
- Reject double-encoded requests and non-ASCII characters in URLs
- Avoid the use of sensitive/outdated libraries and modules (e.g. PHP CGI)

