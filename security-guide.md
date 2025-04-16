# Security Guide

Create a Security Guide documenting HIPAA/FERPA-level security measures, including configurations for AES-256/TLS, Qualys SSL and Mozilla Observatory reports, and advanced security features.HIPAA/FERPA-level security measures

## HIPAA/FERPA-level security measures

To ensure that PDFs are viewed only by the correct logged-in user, the application implements the following security measures:

* **Custom Permission Class**: **IsDocumentOwnerOrAdmin** ensures that only the document owner or an admin can access the document. Admins have read-only access.
* **Document ViewSet**: The **DocumentViewSet** uses the **IsDocumentOwnerOrAdmin** permission class to enforce access control.
* **PDFUploadForm Component**: Fetches and displays documents based on the logged-in user's application ID, ensuring only authorized access.
* **DocumentStatusDisplay Component**: Displays the status of required documents and allows viewing if the user has the correct permissions.

### AES-256 / TLS Encryptions

* **TLS Enforcement:** All HTTP traffic is redirected to HTTPS using TLS v1.2 & v1.3 for secure data transmission.
* **SSL Certificates:** Managed by **Let's Encrypt**, ensuring automatic renewal and strong encryption.
* **AES-256 Encryption:** TLS cipher suites include **AES-256-GCM** and **ChaCha20-Poly1305** for high security and performance.
* **Secure Headers:** Prevents attacks with `X-Frame-Options: DENY`, `X-Content-Type-Options: nosniff`, and `X-XSS-Protection: block`.
* **Proxy Security:** NGINX forwards **client IPs and protocols** securely to Django, ensuring accurate logging and authentication.
* **Session Protection:** Cookies are set with `HttpOnly; Secure` flags to prevent **XSS and session hijacking**.
* **Cache Control:** Static files have **long expiration (30 days)**, while dynamic content is set to **no-store** to prevent outdated pages.
* **Gzip Compression:** Enabled for static/media files to **improve performance** while maintaining security.
* **Admin Panel Security:** CSRF protection enforced via secure **proxy cookie settings** and domain

### Threat models we handle

* The Asset we are trying to protect
  * The information being shared between the server serving the website and the clients (i.e. the students and admins)
* The Attacker Capabilities
  * The attacker can intercept packets between client and server.
* The Attacker Knowledge
  * The attacker knows when to intercept packets between client and server

AES/TLS encryption addresses this security concern because we can prevent attackers eavesdropping on communications between the client and server from gaining any information.

### MFA

MFA was done through the [Django AllAuth library](https://docs.allauth.org/en/dev/mfa/index.html) which we also used for SSO. It was implemented with authentication apps in mind, with options to use a QR code or a TOTP to activate a device. A viewset in Django was created for interacting with MFA and adjusting the MFA settings for users.

Threat Model We Handle

* The Asset we are trying to protect
  * Access to a users account and actions that they can take
* The Attacker Capabilities
  * The attacker can attempt to login to the system using the known credentials
* The Attacker Knowledge
  * The attacker knows the user's username and password

MFA addresses this security concern because although an attacker can know the user's credentials, if the attacker doesn't have the user's device, they won't be able to login to the system. The attacker must be able to breach both factors of authentication.

### Session Timeout

Session timeout was done within our AuthContext.js file and it gathers information on whether a user has been moving their mouse or using their keyboard while using the website.

Threat Model We Handle

* The Asset we are trying to protect
  * Access to a users account and actions that they can take
* The Attacker Capabilities
  * The attacker can access a user's device but only after the session timer has passed
* The Attacker Knowledge
  * The attacker does not know the user's credentials

Session Timeout addresses this security concern because although the attacker can access the user's device, because it is after the session timeout, then they won't be able to log back in and take advantage of the user's resources/account.

### Audit Logging

Audit logging was achieved by leveraging Django's [established audit logging](https://django-auditlog.readthedocs.io/en/latest/).

![image](https://github.com/user-attachments/assets/d693fd20-1157-4da6-92f9-943bdc5984b9)

Threat Model We Handle

* The Asset we are trying to protect
  * The data associated with users and the website and the integrity of that data
* The Attacker Capabilities
  * The attacker can login with the user's credentials and perform malicious actions
* The Attacker Knowledge
  * The attacker has access to a user's credentials

Audit logging addresses this security concern because the attacker can be caught in the act of performing malicious actions using a user's credentials, and subsequently they can be stopped or potentially identified. Once malicious actions have been identified, we can look through the audit logs and see who performed such actions and when, which will help us prevent future attacks.

## Qualys SSL Report

<figure><img src=".gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>

No changes were needed in order to achieve this qualification.

{% file src=".gitbook/assets/SSL Server Test_ mishmash.colab.duke.edu (Powered by Qualys SSL Labs).pdf" %}

## Mozilla Observatory Report

{% file src=".gitbook/assets/Scan results for mishmash.colab.duke.edu _ HTTP Observatory _ MDN.pdf" %}

## System Monitoring

Threat Model

* The Asset we are trying to protect
  * Attackers changing files in the code base and/or adding malicious files
* The Attacker Capabilities
  * attackers can access the codebase files and make changes
* The Attacker Knowledge
  * attacker knows where the codebase is

File monitoring addresses this threat because if the attacker changes anything in the code base then it will get picked up and we will be alerted if something goes wrong. This way we can go in and fix it and hopefully, paired with our audit logging, we can identify the culprit.

## Security Hardened Backups

* The Asset we are trying to protect
  * Backups of the system
* The Attacker Capabilities
  * attacker has full access to the production server
* The Attacker Knowledge
  * Attacker knows the location of every file they are interested in as well as the hostname of the backupserver

Security hardened backups protects us from this threat model because even if an attacker has full control over the production server, they have no way of accessing the backup server because they have no credentials accessible to access it. Also they would need the encryption key in order to do anything with it



***

## Vulnerabilty monitoring



We use dependabot for chekcing for vulnerabilites in our dependencies and CodeQL for scanning our codebase for vulnerabilities. \




Dependabot demonstration:

### Dependabot Demonstration



### vulnerability detected:

<figure><img src=".gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

Fix implemented:'

<figure><img src=".gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

***

## SSO Provider&#x20;



### Overview

The application implements Single Sign-On (SSO) authentication using Duke University's OpenID Connect (OIDC) provider. This integration allows Duke users to seamlessly log into the application using their Duke credentials.

### Technical Implementation

#### Backend Configuration

**Settings**

The SSO integration is configured in settings.py with the following key components:

```python
SOCIALACCOUNT_PROVIDERS = {    "openid_connect": {        "OAUTH_PKCE_ENABLED": True,        "APPS": [            {                "provider_id": "duke-oidc",                "name": "Duke University Login",                "client_id": "ece-spring-2025-sc814",                "secret": OIDC_CLIENT_SECRET,                "settings": {                    "server_url": "https://oauth.oit.duke.edu/oidc",                    "token_auth_method": "client_secret_basic",                    "oauth_pkce_enabled": True,                }            }        ]    }}Key settings:
PKCE (Proof Key for Code Exchange) is enabled for enhanced security
Uses Duke's OIDC server URL: https://oauth.oit.duke.edu/oidc
Custom social account adapter is configured: SOCIALACCOUNT_ADAPTER = "api.adapters.CustomSocialAccountAdapter"
```



#### User Model

The User model includes SSO-specific functionality



```python

class User(AbstractUser):
    @property    def is_sso(self):
            """Check if user logged in via SSO."""        
            if not self.pk:
                return False
                return SocialAccount.objects.filter(user=self).exists()
```

#### Custom Social Account Adapter

The application uses a custom adapter (CustomSocialAccountAdapter) to properly map SSO data to user fields







&#x20;`CustomSocialAccountAdapter(DefaultSocialAccountAdapter):    def populate_user(self, request, sociallogin, data):        user = super().populate_user(request, sociallogin, data)                extra_data = sociallogin.account.extra_data        duke_net_id = extra_data.get("dukeNetID")        full_name = extra_data.get("name")                user.username = duke_net_id        user.display_name = full_name if full_name else "New User"                return user`

### Frontend Implementation

#### Authentication Flow

1. Login Button: The SSO login option is presented to users in the LoginModal component `<FormButton type="button" onClick={handleDukeSSOLogin}>    Login with Duke SSO</FormButton>`
2. SSO Redirect: When users click the Duke SSO button, they are redirected to Duke's OIDC login endpoint `const handleDukeSSOLogin = () => {    window.location.href = "/api/accounts/oidc/duke-oidc/login/";};`

#### Session Management

The AuthContext provides session management for SSO users:

* Automatic token verification on app startup
* Session persistence across page refreshes
* Automatic user data refresh when needed

#### User Interface Considerations

SSO users have certain restrictions in the UI:

* Cannot change their password
* Cannot manually connect Ulink accounts
* Have automatic Ulink integration based on their Duke NetID

### Security Features

1. Session Security:

* Secure session cookies enabled: SESSION\_COOKIE\_SECURE = True
* CSRF protection with trusted origins configured
* Session timeout settings for both inactivity and absolute timeouts

1. API Security:

* Token-based authentication for API requests
* Session authentication for browsable API
* CORS configuration for frontend communication

### Special Considerations

1. SSO User Restrictions:

* SSO users cannot manually change their passwords
* SSO users cannot be deleted through the normal user deletion process
* SSO users have automatic Ulink account integration

1. Ulink Integration:

* SSO users' Ulink usernames are automatically set to their Duke NetID
* The system prevents duplicate Ulink username assignments

### Error Handling

The system includes proper error handling for various SSO-related scenarios:

1. Missing Duke NetID `if not duke_net_id:       raise ValidationError("SSO response does not contain a valid 'dukeNetID' field.")`
2. SSO Session Detection: `catch (err) {       console.error("No SSO session detected.", err);   }`

### Best Practices

1. Security:

* Use HTTPS for all SSO-related communications
* Implement PKCE for enhanced security
* Properly handle session timeouts and token expiration

1. User Experience:

* Provide clear login options for SSO users
* Handle SSO-specific restrictions gracefully in the UI
* Implement automatic Ulink integration for seamless user experience

1. Error Handling:

* Implement proper error handling for SSO-related operations
* Provide clear error messages to users
* Log SSO-related errors for debugging

