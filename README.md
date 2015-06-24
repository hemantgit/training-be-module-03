# Backbase Training Exercises

## Portal Backend - Module 3: Security

In this module, we will see how to extend standard authentication functionality with third party system integration. 
You will create custom LDAP authentication configuration which will fetch users information from Training Server LDAP.

### Prerequisites

All exercises use the [standard portal set-up for backbase training](https://my.backbase.com/resources/how-to-guides/getting-your-first-launchpad-based-portal-set-up/).

### Contents

This module contains the following components:

1. security-ldap : LDAP integration module which allows to connect to Training Server LDAP during authentication. For details check 
[security-ldap](https://github.com/Backbase/training-be-module-03/blob/code-migration/security-ldap).

### References

For another example of creating a custom authentication provider, please refer to [PlayerAuthenticationProvider](https://github.com/Backbase/training-be-module-01/blob/code-migration/enterprise-integration-module/src/main/java/com/backbase/expert/training/security/PlayerAuthenticationProvider.java). For instructions on how to configure it, please take a look at [How to Install and Configure Enterprise Integration Module](https://github.com/Backbase/training-be-module-01/blob/code-migration/enterprise-integration-module/README.md#installation--configuration).
