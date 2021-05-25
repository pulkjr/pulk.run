---
title: "Enable SAML Authentication to GitLab"
last_modified_at: 2021-05-24T22:38:42-05:00
categories:
  - recipes
tags:
  - GitLab
  - SAML
  - ADFS
---
### Process
1. Open the Gitlab server configuration (sudo emacs -nw /etc/gitlab/gitlab.rb)
2. Scroll to the omniauth section of the configuration and update to be similar to below:

```ruby
gitlab_rails['omniauth_enabled'] = true
gitlab_rails['omniauth_allow_single_sign_on'] = ['saml']
gitlab_rails['omniauth_block_auto_created_users'] = false
gitlab_rails['omniauth_auto_link_ldap_user'] = true
gitlab_rails['omniauth_auto_link_saml_user'] = true
gitlab_rails['omniauth_providers'] = [
  {
    name: 'saml',
    args: {
      assertion_consumer_service_url: 'https://<gitlab server external url>/users/auth/saml/callback',
      idp_cert: "-----BEGIN CERTIFICATE-----
<encryption cert from adfs server>
-----END CERTIFICATE-----",
      idp_sso_target_url: 'https://<sts adfs domainname>/adfs/ls/',
      idp_slo_target_url: 'https://<sts adfs domainname>/adfs/ls/',
      issuer: 'https://<gitlab server external url>',
      name_identifier_format: 'urn:oasis:names:tc:SAML:2.0:nameid-format:persistent', # Ensure to provide the NameId within ADFS transform
      #authn_context: 'urn:oasis:names:tc:SAML:2.0:ac:classes:X509', # Adjusts the authentication to require X509 certificates from ADFS
      attribute_statements: {
        username: ['http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn'],
        email: ['http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress'],
        name: ['http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name'],
        first_name: ['http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname'],
        last_name: ['http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname']
      }
    },
    label: 'SAML' # optional label for SAML login button, defaults to "Saml"
  }
]
```