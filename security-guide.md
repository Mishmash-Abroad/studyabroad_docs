# Security Guide

Create a Security Guide documenting HIPAA/FERPA-level security measures, including configurations for AES-256/TLS, Qualys SSL and Mozilla Observatory reports, and advanced security features.HIPAA/FERPA-level security measures

## HIPAA/FERPA-level security measures

### AES-256 / TLS configurations

Threat Model We Handle
- The Asset we are trying to protect
  - The information being shared between the server serving the website and the clients (i.e. the students and admins)
- The Attacker Capabilities
  - The attacker can intercept packets between client and server. 
- The Attacker Knowledge
  - The attacker knows when to intercept packets between client and server

AES/TLS encryption addresses this security concern because we can prevent attackers eavesdropping on communications between the client and server from gaining any information. 

### MFA

Threat Model We Handle
- The Asset we are trying to protect
  - Access to a users account and actions that they can take
- The Attacker Capabilities
  - The attacker can attempt to login to the system using the known credentials
- The Attacker Knowledge
  - The attacker knows the user's username and password

MFA addresses this security concern because although an attacker can know the user's credentials, if the attacker doesn't have the user's device, they won't be able to login to the system. The attacker must be able to breach both factors of authentication.

### Session Timeout

Threat Model We Handle
- The Asset we are trying to protect
  - Access to a users account and actions that they can take
- The Attacker Capabilities
  - The attacker can access a user's device but only after the session timer has passed
- The Attacker Knowledge
  - The attacker does not know the user's credentials

Session Timeout addresses this security concern because although the attacker can access the user's device, because it is after the session timeout, then they won't be able to log back in and take advantage of the user's resources/account.

### Audit Logging

Audit logging was achieved by leveraging Django's ![established audit logging](https://django-auditlog.readthedocs.io/en/latest/). 

![image](https://github.com/user-attachments/assets/d693fd20-1157-4da6-92f9-943bdc5984b9)


Threat Model We Handle
- The Asset we are trying to protect
  - The data associated with users and the website and the integrity of that data
- The Attacker Capabilities
  - The attacker can login with the user's credentials and perform malicious actions
- The Attacker Knowledge
  - The attacker has access to a user's credentials

Audit logging addresses this security concern because the attacker can be caught in the act of performing malicious actions using a user's credentials, and subsequently they can be stopped or potentially identified. Once malicious actions have been identified, we can look through the audit logs and see who performed such actions and when, which will help us prevent future attacks.

## Qualys SSL Report

<figure><img src=".gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

No changes were needed in order to achieve this qualification.

{% file src=".gitbook/assets/SSL Server Test_ mishmash.colab.duke.edu (Powered by Qualys SSL Labs).pdf" %}

## Mozilla Observatory Report

{% file src=".gitbook/assets/Scan results for mishmash.colab.duke.edu _ HTTP Observatory _ MDN.pdf" %}





