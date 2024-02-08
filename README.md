# Deploy React Application on Cloud using Jenkins - DevOps

### **Phase 1: Initial Setup and Deployment**

**Step 1: Launch EC2 (Ubuntu 22.04):**

- Launch an EC2 instance (Recommended: T2 Large) on AWS with Ubuntu 22.04.
- Connect to the instance using SSH.

**Step 2: Clone the Code:**

- Update all the packages and then clone the code.
- Clone your React application's code repository onto the EC2 instance:
    
    ```bash
    git clone https://github.com/yourusername/your-react-app.git
    ```

**Step 3: Install Docker and Run the App Using a Container:**

- Set up Docker on the EC2 instance:
    
    ```bash
    sudo apt-get update
    sudo apt-get install docker.io -y
    sudo usermod -aG docker $USER  # Replace with your system's username, e.g., 'ubuntu'
    newgrp docker
    sudo chmod 777 /var/run/docker.sock
    ```
    
- Build and run your application using Docker containers:
    
    ```bash
    docker build -t react-app .
    docker run -d --name react-app-container -p 8080:80 react-app:latest
    
    # to delete
    docker stop react-app-container
    docker rm react-app-container
    docker rmi -f react-app
    ```

**Step 4: Obtain Necessary API Keys:**

- If your React application relies on any external APIs, such as authentication or data retrieval, obtain the required API keys following the respective service's documentation.

**Step 5: Integrate API Keys into Docker Build:**

- Once you've obtained the necessary API keys, integrate them into your Docker build process.
- For example, if your application requires an API key, you can pass it as a build argument during Docker image creation:
    
    ```bash
    docker build --build-arg API_KEY=<your-api-key> -t react-app .
    ```

By following these steps, you can set up and deploy your React application on AWS EC2 using Docker, ensuring smooth integration with necessary API keys for seamless functionality.

### **Phase 2: Security**

**Step 1: Install SonarQube and Trivy:**

- Begin by installing SonarQube and Trivy on the EC2 instance to perform vulnerability scans.
        
    Install SonarQube using Docker:
    
    ```bash
    docker run -d --name sonarqube -p 9000:9000 sonarqube:lts-community
    ```
        
  Access SonarQube via: `publicIP:9000` (Default username & password: admin)
        
  **Trivy:**
        
    Install Trivy for vulnerability scanning:
    
    ```bash
    sudo apt-get install wget apt-transport-https gnupg lsb-release
    wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo apt-key add -
    echo deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main | sudo tee -a /etc/apt/sources.list.d/trivy.list
    sudo apt-get update
    sudo apt-get install trivy
    ```
        
    To scan an image using Trivy:
        
    ```bash
    trivy image <imageid>
    ```

**Step 2: Integrate SonarQube and Configure:**

  - Integrate SonarQube into your CI/CD pipeline.
  - Configure SonarQube to analyze code for quality and security issues.
