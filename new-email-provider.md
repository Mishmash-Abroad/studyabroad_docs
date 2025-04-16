---
description: Changing the Email Provider Account
---

# New Email Provider

1\. Overview

By default, our system uses **SendGrid** for sending emails. The SendGrid credentials (API key, default sender email, etc.) are stored as **GitHub secrets** and referenced as environment variables in our application code. To switch to another email provider (e.g., Mailgun, AWS SES, SMTP), follow the steps below.

***

#### 2. Update Credentials in GitHub Secrets

1. Go to the GitHub repository settings and navigate to **Secrets and variables** → **Actions** (or simply **Settings** → **Secrets** → **Actions** in the repo).
2. Locate the existing secrets, for example:
   * `SENDGRID_API_KEY`
   * `SENDGRID_DEFAULT_FROM`
3. **Remove or replace** them with the **new provider**’s credentials:
   * For instance, if using Mailgun, you might create secrets like `MAILGUN_API_KEY` and `MAILGUN_DOMAIN`.
4.  Ensure you also document these changes in your deployment or secrets management guides.

    > **Tip**: Check the [add-secrets.md](add-secrets.md "mention") page in your GitBook for any environment-specific instructions on naming or scoping secrets.

***

#### 3. Adapt `email_utils.py` for the New Provider

Inside **`email_utils.py`**, we currently import and configure **SendGrid**:

```python
from sendgrid import SendGridAPIClient
from sendgrid.helpers.mail import Mail
SENDGRID_API_KEY = os.getenv("SENDGRID_API_KEY")
DEFAULT_FROM_EMAIL = os.getenv("SENDGRID_DEFAULT_FROM")
```

To swap providers:

1. **Remove** or **comment out** the `SendGridAPIClient` import and any SendGrid-specific logic.
2. **Import** your new provider’s library (e.g., `import mailgun` or configure an SMTP client).
3.  Adjust the code in `send_email(...)` to build and send an email using the new library’s methods, referencing new environment variables:

    ```python
    NEW_PROVIDER_API_KEY = os.getenv("MAILGUN_API_KEY", "")
    ...
    # Use the library's send logic, e.g.,
    # mailgun_client.send_message(domain, from=..., to=..., subject=..., etc.)
    ```
4. Replace the logging and error handling with whichever approach is appropriate for your new library.

***

#### 4. Environment Variables and Default Email Sender

Just like we currently do with:

```python
DEFAULT_FROM_EMAIL = os.getenv("SENDGRID_DEFAULT_FROM", "hccabroad@gmail.com")
```

you’ll need to define:

* **New** environment variables for the default “from” address or domain name.
* A fallback default sender address to avoid code-breaking if the environment variable isn’t set.

**Example** (if using Mailgun):

```python
DEFAULT_FROM_EMAIL = os.getenv("MAILGUN_DEFAULT_FROM", "hccabroad@example.com")
MAILGUN_API_KEY = os.getenv("MAILGUN_API_KEY", "")
MAILGUN_DOMAIN = os.getenv("MAILGUN_DOMAIN", "example.mailgun.org")
```

***

#### 5. Test Your New Email Flow

1. **Update** and **redeploy** your application with the new environment variables.
2. Try sending a test email (e.g., an application welcome email or a letter of recommendation request).
3. Check your logs or console for a success status code.
4. If an error occurs, verify:
   * The **API keys** and **sender domain** are correct.
   * You have replaced all references to SendGrid with your new provider’s library.

***

#### 6. Conclusion

Switching email providers primarily involves:

1. **Storing new credentials** in GitHub secrets.
2. **Modifying** `email_utils.py` to use the new provider’s library, environment variables, and sending logic.
3. **Testing** thoroughly to ensure all emails (e.g., recommendations, enrollment notifications) still work.

If you have any questions on provider-specific integration steps, refer to the new provider’s documentation. For environment variable management, see our [add-secrets.md](add-secrets.md "mention") in the GitBook.

