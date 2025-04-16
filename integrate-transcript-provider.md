# Adding a New Transcript Provider

This guide explains how to add a new transcript provider to the system using the existing `TranscriptProvider` interface.

## 1. Create a New Provider Class

Create a new file in the `transcript_providers/` directory: transcript_providers/myprovider.py

Then implement the required methods:

```python
from transcript_providers.base import TranscriptProvider
from django.core.exceptions import ValidationError

class MyProvider(TranscriptProvider):
    def is_account_required(self):
        # Return True if users must link an account, otherwise False
        return True

    def validate_account(self, credentials):
        # Optional: validate credentials provided in the request
        return True

    def connect_account(self, user, credentials):
        # Perform any necessary linking logic and store values on the user
        user.transcript_username = credentials.get("myprovider_username")
        user.save()

    def fetch_transcript(self, user):
        # Fetch and return a dictionary of course code -> grade
        return {
            "CS 101": "A",
            "MATH 201": "B+"
        }
```

## 2. Use It in views.py
Import your provider and use it inside any relevant view actions:
```python
from transcript_providers.myprovider import MyProvider

@action(detail=True, methods=["post"], permission_classes=[IsAdminOrSelf])
def connect_transcript_provider(self, request, pk=None):
    user = self.get_object()
    provider = MyProvider()

    try:
        provider.connect_account(user, request.data)
        return Response({"message": "Account connected."})
    except ValidationError as e:
        return Response({"error": str(e)}, status=400)

@action(detail=True, methods=["post"], permission_classes=[IsAdminOrSelf])
def refresh_transcript(self, request, pk=None):
    user = self.get_object()
    provider = MyProvider()

    try:
        transcript_data = provider.fetch_transcript(user)
        user.transcript_data = transcript_data
        user.save()
        return Response({"transcript": transcript_data})
    except ValidationError as e:
        return Response({"error": str(e)}, status=400)

```

## 3. Update the User Model if Needed

In models.py, the default field is likely named transcript_username (used for Ulink). If your provider stores different data (e.g., email, token, API ID), update or add the necessary fields to the User model.

```python
transcript_username = models.CharField(max_length=100, blank=True, null=True)
```

Update migrations accordingly.

## Summary
- Implement a class that inherits from TranscriptProvider
- Use it in views.py for account linking and transcript fetching
- Update models.py if your provider needs to store different fields
