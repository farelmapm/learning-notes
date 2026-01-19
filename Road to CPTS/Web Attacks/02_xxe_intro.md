# Intro to XXE

<b> XML External Entity (XXE) Injection </b> occur when XML data is taken from a user-controlled input without properly sanitizing or safely parsing it, which may allow users to perform malicious actions. 

## XML
Basic exapmle of an XML document:
```
<?xml version="1.0" encoding="UTF-8"?>
<email>
  <date>01-01-2022</date>
  <time>10:00 am UTC</time>
  <sender>john@inlanefreight.com</sender>
  <recipients>
    <to>HR@inlanefreight.com</to>
    <cc>
        <to>billing@inlanefreight.com</to>
        <to>payslips@inlanefreight.com</to>
    </cc>
  </recipients>
  <body>
  Hello,
      Kindly share with me the invoice for the payment made on January 1, 2022.
  Regards,
  John
  </body> 
</email>
```

| Key         | Definition                                                                                                           | Example                                |
|-------------|----------------------------------------------------------------------------------------------------------------------|----------------------------------------|
| Tag         | The keys of an XML document, usually wrapped with (`</>`) characters.                                               | `<date>`                               |
| Entity      | XML variables, usually wrapped with (`&/;`) characters.                                                              | `&lt;`                                 |
| Element     | The root element or any of its child elements, and its value is stored in between a start-tag and an end-tag.       | `<date>01-01-2022</date>`              |
| Attribute   | Optional specifications for any element that are stored in the tags, which may be used by the XML parser.           | `version="1.0"` / `encoding="UTF-8"`   |
| Declaration | Usually the first line of an XML document, and defines the XML version and encoding to use when parsing it.         | `<?xml version="1.0" encoding="UTF-8"?>` |

## XML DTD
<b>XML Document Type Defintion (DTD)</b> validates an XML document against a pre-defined document structure. 

Example of a DTD:
```
<!DOCTYPE email [
  <!ELEMENT email (date, time, sender, recipients, body)>
  <!ELEMENT recipients (to, cc?)>
  <!ELEMENT cc (to*)>
  <!ELEMENT date (#PCDATA)>
  <!ELEMENT time (#PCDATA)>
  <!ELEMENT sender (#PCDATA)>
  <!ELEMENT to  (#PCDATA)>
  <!ELEMENT body (#PCDATA)>
]>
```
`PCDATA` denotes raw data.

- Can be placed within the XML document itself, right after the XML declaration. 

- <ul> 
Can be stored in an external file (ex: `email.dtd`) and then referenced within the XML document with the `SYSTEM` keyword:
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE email SYSTEM "email.dtd">
```
</ul>

- <ul>
Can be referenced through a URL:
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE email SYSTEM "http://inlanefreight.com/email.dtd">
```
</ul>

## XML Entities
XML allows definition of custom entities (i.e. XML variables) in XML DTDs to allow refactoring of variables and reduce repetitive data. 
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE email [
  <!ENTITY company "Inlane Freight">
]>
```
An entity can be referenced in an XML document (ex:`&company;`). When an entity is referenced, it will be replaced with its value by the parser. 

External XML entities can be referenced with `SYSTEM`:
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE email [
  <!ENTITY company SYSTEM "http://localhost/company.txt">
  <!ENTITY signature SYSTEM "file:///var/www/html/signature.txt">
]>
```
> Note: `PUBLIC` should also be able to be used instead of `SYSTEM`.

