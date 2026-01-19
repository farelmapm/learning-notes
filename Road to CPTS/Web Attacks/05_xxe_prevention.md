# XXE Prevention

## Avoiding Outdated Components
XXE vulnerabilities are mainly caused by outdated XML libraries. It's not handled by code developed by the developers, but rather XML libraries. 

## Using Safe XML Configurations
- Disable referencing <b>custom DTOs</b>
- Disable referencing <b>external XML entities</b>
- Disable <b>parameter entity</b> processing
- Disable support for <b>XInclude</b>
- Prevent <b>entity reference loops</b>

