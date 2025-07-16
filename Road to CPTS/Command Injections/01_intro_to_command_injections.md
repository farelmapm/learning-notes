# Intro to Command Injections

Common types of injections:

| Injection                         | Description                                                                  |
|-----------------------------------|------------------------------------------------------------------------------|
| OS Command Injection              | Occurs when user input is directly used as part of an OS command.            |
| Code Injection                    | Occurs when user input is directly within a function that evaluates code.    |
| SQL Injections                    | Occurs when user input is directly used as part of an SQL query.             |
| Cross-Site Scripting/HTML Injection | Occurs when exact user input is displayed on a web page.                     |


Other types of injections:
- LDAP Injection
- NoSQL Injection
- HTTP Header Injection
- XPath Injection
- IMAP Injection
- ORM Injection

## OS Command Injections
#### PHP Example
`exec` `system` `shell_exec` `passthru` `popen`
```
<?php
if (isset($_GET['filename'])) {
    system("touch /tmp/" . $_GET['filename'] . ".pdf");
}
?>
```

#### NodeJS Example
`child_process.exec`
`child_process.spawn`
```
app.get("/createfile", function(req, res){
    child_process.exec(`touch /tmp/${req.query.filename}.txt`);
})
```

