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

## Project Structure



