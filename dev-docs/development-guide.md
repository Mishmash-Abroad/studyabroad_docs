---
icon: arrows-to-circle
description: How to run project in a local (non production, non static ) deployment.
---

# Development Guide



### Prerequisites

#### Platform Requirements

* **Operating System**: Specify the Linux distribution (e.g., Ubuntu 20.04, CentOS 7, etc.).
* **Required Packages**:
  *   Install Git, Docker, and Docker Compose:

      ```bash
      sudo apt update && sudo apt install -y git docker.io
      ```
  * Install Docker compose: Guide [HERE](https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository)
  *   Verify installation:

      ```bash
      docker --version
      docker compose --version
      ```

### Installation Steps (Local Deployment)

1.  **Clone the Repository**

    ```bash
    git clone https://github.com/Mishmash-Abroad/studyabroad.git
    cd /studyabroad
    ```



1. **Start the Application**
   *   Build and run the application in Docker:

       ```bash
       docker compose up --build
       ```
2. **Access the Application**
   *   Open the frontend in your browser:

       ```
       http://localhost:3000
       ```

***

## Project Structure Overview

mishmash/ 
│── mishmash/                     # Django project root \
│   ├── settings.py               # Global Django settings \
│   ├── urls.py                   # Main URL router for Django \
│── api/                           # Main API application \
│   ├── admin.py                   # Django admin settings \
│   ├── models.py                  # Database models \
│   ├── serializers.py             # API serializers for DRF \
│   ├── urls.py                    # API-specific URL routes \
│   ├── views.py                   # Django REST Framework (DRF) API views \
│   ├── management/commands/        # Custom Django management commands \
│── frontend/                       # React frontend \
│   ├── src/                        # Source code \
│   │   ├── components/             # Reusable React components \
│   │   ├── pages/                  # Page-level React components \
│   │   ├── utils/axios.js          # API request helper \
│   │   ├── App.js                  # Main React App entry point \
│   │   ├── theme/index.js          # MUI theme configuration \
│── Dockerfile                      # Docker setup \
│── .env                            # Environment variables (ignored by Git) \
│── requirements.txt                 # Python dependencies \
│── package.json                     # Frontend dependencies \
│── manage.py                        # Django command-line utility \



## Creating New Pages (Frontend)

### 1. Create a new file in frontend/src/pages

For example, `frontend/src/pages/NewPage.js`

### 2. Define a functional component in the new file

For example,
```
import React from "react";

const NewPage = () => {
  return (
    <div>
      <h1>Welcome to the New Page!</h1>
    </div>
  );
};

export default NewPage;
```

### 3. Add a route to frontend/src/App.js

For example,
```
import { BrowserRouter as Router, Route, Routes } from "react-router-dom";
import NewPage from "./pages/NewPage";

function App() {
  return (
    <Router>
      <Routes>
        <Route path="/new-page" element={<NewPage />} />
      </Routes>
    </Router>
  );
}

export default App;
```

## Adding New API Endpoints (Backend)

### If the new endpoint requires a new table in the database, define a new model in api/models.py
```
from django.db import models

class ExampleModel(models.Model):
    name = models.CharField(max_length=100)
    description = models.TextField()
```

You will then need to serialize the model:
```
from rest_framework import serializers
from .models import ExampleModel

class ExampleModelSerializer(serializers.ModelSerializer):
    class Meta:
        model = ExampleModel
        fields = '__all__'
```

Then create a ViewSet for the model in api/views.py. This will handle basic API functionality for the new model.

```
from rest_framework import viewsets
from .models import ExampleModel
from .serializers import ExampleModelSerializer

class ExampleModelViewSet(viewsets.ModelViewSet):
    queryset = ExampleModel.objects.all()
    serializer_class = ExampleModelSerializer
```

Consider who should have access to this endpoint, and use the custom permission classes to define how the endpoint can be used. For example, in the ApplicationViewSet, only application owners and the admin should be able to edit the application, so the following line was added 
`permission_classes = [permissions.IsAuthenticated, IsOwnerOrAdmin]`

### If you are adding a new endpoint involving an already existing endpoint, then modify the existing ModelViewSet in views.py

Add a new action on the viewset. This will add a new endpoint called {model-name}/{action-name}, and will return the response defined by the action, and will expect the defined inputs. Note that these actions can have their own permission classes. For example:
```
@action(detail=False, methods=["post"], permission_classes=[permissions.AllowAny])
    def login(self, request):
        """Custom login endpoint."""
        username = request.data.get("username")
        password = request.data.get("password")
        user = authenticate(request, username=username, password=password)

        if user:
            token, _ = Token.objects.get_or_create(user=user)
            return Response({"token": token.key, "user": UserSerializer(user).data})
        return Response(
            {"detail": "Invalid credentials."}, status=status.HTTP_401_UNAUTHORIZED
        )
```
Creates an endpoint /login, that accepts any user, and returns an authentication token when a valid username and password are included as arguments. After creating a new endpoint, be sure to add that endpoint to the testing procedure specified below.

## Testing, Superusers, and Test Data

### Testing

All of the files in api/management/commands can be run using `python manage.py {command name}`. Command names are derived from the filename. These files perform the following tasks, and can be used as needed: creating test or production data, testing the functionaility of the exising API endpoints, and other miscellaneous tasks. There is a command called test_api_endpoints, which runs through all endpoints defined in the project with both valid and invalid inputs, and verifies functionaility. Any new endpoints defines should be added to this test file in the same format as in the file. 

Test data can be generated with the commands add_test_users, add_test_programs, add_test_applications, and add_test_announcements. This will generate test data for running the project locally. Using the `-prod` flag with these commands will generate the specific data required for production.




