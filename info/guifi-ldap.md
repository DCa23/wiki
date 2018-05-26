Drupal sync users with LDAP every day at 01:00

Try authentication via CLI

    apt-get install ldap-utils

Run (change myuser)

    ldapsearch -W -H ldaps://ldap.guifi.net -D "uid=myuser,o=webusers,dc=guifi,dc=net"

output when succeeded

```
# extended LDIF
#
# LDAPv3
# base <> (default) with scope subtree
# filter: (objectclass=*)
# requesting: ALL
#

# search result
search: 2
result: 32 No such object

# numResponses: 1
```

output when failed

```
ldap_bind: Invalid credentials (49)
```
