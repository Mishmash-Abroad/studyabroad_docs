---
description: How to run project in a local (non production, non static ) deployment.
icon: arrows-to-circle
---

# Deployment Guide



### Prerequisites

#### Platform Requirements

* **Operating System**: Specify the Linux distribution (e.g., Ubuntu 20.04, CentOS 7, etc.).
* **Required Packages**:
  *   Install Git, Docker, and Docker Compose:

      ```bash
      sudo apt update && sudo apt install -y git docker.io
      ```
  *   Install Docker Compose (latest version):

      ```bash
      sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
      sudo chmod +x /usr/local/bin/docker-compose
      ```
  *   Verify installation:

      ```bash
      docker --version
      docker-compose --version
      ```

### Installation Steps (Local Deployment)

1.  **Clone the Repository**

    ```bash
    git clone https://github.com/Mishmash-Abroad/studyabroad.git
    cd /studyabroad
    ```
2. **Set Up Environment Variables**
   * Create a `.env` file and add the necessary environment variables.
3. **Start the Application**
   *   Build and run the application in Docker:

       ```bash
       docker compose up --build
       ```
4. **Access the Application**
   *   Open the application in your browser:

       ```
       http://localhost:8000
       ```

***

This guide ensures a smooth local deployment using Docker Compose. Let me know if you need further refinements! 🚀
