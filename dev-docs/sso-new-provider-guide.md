---
description: >-
  This documentation describes how to update the application to use a different
  OpenID Connect (OIDC) provider instead of Duke NetID SSO.
---

# SSO New Provider Guide

## Integrating with a Different SSO Provider



### Overview

The application currently uses Duke University's OIDC provider for Single Sign-On authentication. This guide explains how to reconfigure the system to work with a different OIDC provider while maintaining the same functionality.

### Configuration Components

The SSO integration consists of several key components:

1. **Environment Variables** - Secure credentials storage
2. **Django Settings** - Provider configuration
3. **Frontend Integration** - Login button and redirect URLs
4. **Custom Adapter** - Maps provider data to user fields

### Step 1: Update Environment Variables

The application uses environment variables to store sensitive SSO credentials:

```bash
# In .env file or environment configuration
OIDC_CLIENT_SECRET=your_new_client_secret
```

This variable must be updated with the client secret from your new OIDC provider. The environment variable is referenced in:

* Development environment: `docker-compose.yml`
* Testing environment: `docker-compose.test.yml`
* Production environment: `docker-compose.prod.yml`
* CI/CD workflows in `.github/workflows/`

### Step 2: Modify Django Settings

Update the OIDC provider configuration in `mishmash/mishmash/settings.py`:

```python
SOCIALACCOUNT_PROVIDERS = {
    "openid_connect": {
        "OAUTH_PKCE_ENABLED": True,  # Enable or disable based on provider requirements
        "APPS": [
            {
                "provider_id": "new-oidc-provider",  # Choose a recognizable identifier
                "name": "New SSO Provider Name",     # Display name shown to users
                "client_id": "your_client_id",       # Client ID from your new provider
                "secret": OIDC_CLIENT_SECRET,        # Secret from environment variable
                "settings": {
                    "server_url": "https://your-provider.example.com/oidc", # Provider's OIDC endpoint
                    "token_auth_method": "client_secret_basic",  # Or other supported method
                    "oauth_pkce_enabled": True,  # Based on provider capabilities
                },
            }
        ]
    }
}
```

### Step 3: Update Custom Social Account Adapter

The adapter in `api/adapters.py` maps SSO provider data to user fields. Modify it to handle your new provider's data structure:

```python
class CustomSocialAccountAdapter(DefaultSocialAccountAdapter):
    def populate_user(self, request, sociallogin, data):
        user = super().populate_user(request, sociallogin, data)
        
        extra_data = sociallogin.account.extra_data
        
        # Update these field names based on your provider's response
        user_id = extra_data.get("user_id_field_name")
        full_name = extra_data.get("name_field_name")
        
        if not user_id:
            raise ValidationError("SSO response does not contain a valid user ID field.")
            
        user.username = user_id
        user.display_name = full_name if full_name else "New User"
        
        return user
```

Key considerations:

* Different providers use different field names for user data
* Review your provider's documentation for the exact JSON structure
* Ensure critical fields like user ID are properly mapped

### Step 4: Update Frontend Integration

Modify the SSO login button in `frontend/src/components/LoginModal.js`:

```javascript
const handleSSOLogin = () => {
  // Update this path to match your new provider ID
  window.location.href = "/api/accounts/oidc/new-oidc-provider/login/";
};
```

Also update the button text to reflect the new provider:

```javascript
<FormButton type="button" onClick={handleSSOLogin}>
  Login with New SSO Provider
</FormButton>
```

### Step 5: Update Automatic Ulink Integration (If Applicable)

If your application needs similar username-based integrations like the current Ulink connection:

```python
# In api/models.py, User.save() method
def save(self, *args, **kwargs):
    if self.is_sso and not self.ulink_username:
        # Update this to match your provider's user ID field
        conflict = User.objects.filter(ulink_username=self.username).exclude(id=self.id).exists()
        if not conflict:
            self.ulink_username = self.username
    super().save(*args, **kwargs)
```

### Step 6: Testing the Integration

1. Configure your provider account:
   * Register your application with the new OIDC provider
   * Set allowed callback URLs: `https://your-domain.example.com/api/accounts/oidc/new-oidc-provider/login/callback/`
   * Obtain client ID and client secret
2. Update environment variables and deploy
3. Test the integration by:
   * Clicking the SSO login button
   * Verifying successful authentication
   * Confirming user data is correctly mapped
   * Testing integration with other systems (like Ulink)

### Troubleshooting

Common issues and solutions:

1. **Incorrect field mapping**:
   * Use your browser's developer tools to inspect the provider's response data
   * Update the custom adapter to match the actual field names
2. **Redirect issues**:
   * Verify the callback URL is correctly registered with your provider
   * Check for URL encoding problems in the login redirect
3. **Authentication failures**:
   * Verify client ID and secret are correct
   * Ensure PKCE settings match provider requirements
4. **Missing user data**:
   * Request additional scopes from your provider if needed fields are missing
   * Add scope configuration to provider settings

### Security Considerations

1. Always store secrets in environment variables, never in code.
2. Use HTTPS for all SSO communication.
3. Review the security requirements of your new provider.
4. Update CSRF settings if needed for the new provider.
5. Consider token/session timeout settings based on your security requirements.

### Additional Configuration Options

The Django Allauth OpenID Connect provider supports additional settings:

```python
"settings": {
    # Required settings
    "server_url": "https://your-provider.example.com/oidc",
    
    # Optional settings
    "identity_claim": "sub",  # Field used as unique identifier
    "oauth_pkce_enabled": True,
    "user_info_enabled": True,  # Whether to fetch userinfo endpoint
    "scope": ["openid", "profile", "email"],  # Requested scopes
    "token_auth_method": "client_secret_basic",
}
```



