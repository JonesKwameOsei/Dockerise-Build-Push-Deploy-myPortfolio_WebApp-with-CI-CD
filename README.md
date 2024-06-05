# Dockerise, Build and Push My Protfolio Web Application 
## Automatiing the Deployment of Web Application with Continuous Integration

## Overview
This project demonstrates how to automate the building and deployment of my wep application using GitHub Actions Pipeline. Incorporating **Continuous Integration (CI)** in the deployment of my web application for a DevOps portfolio is crucial for ensuring consistent, reliable, and automated builds and deployments. CI allows for early detection of integration issues, streamlines the deployment process, and enables faster feedback loops, ultimately leading to more efficient and robust application delivery.

## Step-by-Step Process

### 1. Create a GitHub Repository
Begin by creating a new GitHub repository for your project. This will be the central location for your code, configurations, and the GitHub Actions workflow.<p>

### 2. Create the Dockerfile and Package File
In the repository:
- Create a file called Dockerfile.
```
vi Dockerfile
```
- Add these lines of codes:
```
# Use the official Nginx image from the Docker Hub
FROM nginx:alpine

# Copy the website files into the container
COPY . /usr/share/nginx/html

# Expose port 80 to the outside world
EXPOSE 80

# Run Nginx in the foreground
CMD ["nginx", "-g", "daemon off;"]
```
### 3. Set up the Docker Build Workflow
In the GitHub repository, create a new folder named `.github/workflows`. This is where the GitHub Actions workflow is defined.

Inside the `workflows` folder, create a new file named `dockerhub-push.yml`. This file will contain the configuration for the Docker build workflow.

```
name: Build and Push Docker image to Docker Hub

on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

jobs:
  push_to_registry:
    name: Push Docker image to Docker Hub
    runs-on: ubuntu-latest
    steps:
      - name: Check out the repo
        uses: actions/checkout@v4
    
    - name: Set up Docker Buildx
      uses: docker/setup-buildx-action@v1
      
    - name: Login to Docker Hub
        uses: docker/login-action@v2
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}
    
    - name: Build and push Docker image
        uses: docker/build-push-action@v4
        with:
          context: DockerFileFolder/
          push: true
          tags: rekhugopal/testrepo:latest
```

This workflow will be triggered on push and pull request events to the `main` branch. It performs the following steps:

1. Checks out the repository code.
2. Sets up the Docker Buildx environment for multi-architecture builds.
3. Logs in to the GitHub Container Registry (ghcr.io) using the `GITHUB_TOKEN` secret.
4. Builds the Docker image and pushes it to the GitHub Container Registry, tagging it with the current commit SHA.

### 4. Generate Docker Hub Access Token
By providing the Docker Hub access token as a secret in the GitHub Actions workflow, we can securely authenticate your workflow and grant it the required permissions to push the Docker image to the desired registry location on Docker Hub.

This approach is a best practice for managing credentials in the CI/CD pipelines, as it separates the sensitive information from the workflow code and ensures better security and maintainability of the build and deployment processes.
- In `Docker Hub`, click on the `Docker Hub Profile` on the top right corner.<p>
![image](https://github.com/JonesKwameOsei/Build-Docker-Images-with-GitHub-Pipeline-Actions/assets/81886509/6eaa67b7-f464-497b-8985-ec6e3fe50367)<p>
- Select `My Account `.
- Select `Security`.<p>
![image](https://github.com/JonesKwameOsei/Build-Docker-Images-with-GitHub-Pipeline-Actions/assets/81886509/9c1a3c03-0f51-4537-8159-ef9668278e26)<p>
- Click the `New Access Token` button.<p>
![image](https://github.com/JonesKwameOsei/Build-Docker-Images-with-GitHub-Pipeline-Actions/assets/81886509/e16438c4-eba3-4167-a1b1-fe6ae4d67b2d)<p>
- In the `Access Token Description` field, enter a description `GithubAction` for the token.<p>
![image](https://github.com/JonesKwameOsei/Build-Docker-Images-with-GitHub-Pipeline-Actions/assets/81886509/c6b17bd1-11ff-495e-a392-64f4f0e61774)<p>
- Leave the next field as default and click `Generate Token`.
- Copy the token generated and close the pane. <p>
![image](https://github.com/JonesKwameOsei/Build-Docker-Images-with-GitHub-Pipeline-Actions/assets/81886509/d3207ccd-ca38-476c-9949-c0ee064c94ae)<p>

### 5. Store Secrets in Github Actions
1. In the Github repo, click on settings.
2. On the left silde, find `Secrets and Variables`, click the dropdown and select `Actions`.
![image](https://github.com/JonesKwameOsei/Build-Docker-Images-with-GitHub-Pipeline-Actions/assets/81886509/9ec60cc6-8df0-4905-a16d-76f78d180c87)<p>
3. Under the `Repository secrets`, click on the green button, `New repository Secret`.
4. For `Name` and `Secret`, enter details of the following:<p>

|Name|Secret|
|----|------|
|DOKERHUB_USERNAME|Your docker username|
|DOKERHUB_TOKEN|The access token generated|<p>

The configuration should look like this:<p>
![image](https://github.com/JonesKwameOsei/Build-Docker-Images-with-GitHub-Pipeline-Actions/assets/81886509/f7e19914-b23c-4671-9fa9-9b5461222a2c)<p>

### 7. Manually build and Push the Docker Image Locally
Open a terminal, navigate to the directory containing the Dockerfile, and build the Docker image.
```
docker build -t kwameds/portfolio-webapp:1.0.0 . 
```
Image is built:<p>
![image](https://github.com/JonesKwameOsei/Dockerise-Build-Push-Deploy-with-CI-CD/assets/81886509/61f750af-fc3a-4d57-8b67-f307f9bee88b)<p>

We can verify from on Docker CLI if the image is actually built. To do this, run:
```
docker images
```
![image](https://github.com/JonesKwameOsei/Dockerise-Build-Push-Deploy-with-CI-CD/assets/81886509/1cd840e4-a661-41d2-941d-48ff244cfb44)<p>

Verification from Docker Desktop:<p>
![image](https://github.com/JonesKwameOsei/Dockerise-Build-Push-Deploy-with-CI-CD/assets/81886509/6ffef5ee-d6c0-4234-8510-31cbc6296a60)<p>

Next, we will run the Docker Image Locally. This is to test the image locally to ensure it works correctly.
```
docker run -d -p 8080:8080 --name mycvwebapp kwameds/mycvwebapp:1.0.0
```
![image](https://github.com/JonesKwameOsei/Dockerise-Build-Push-Deploy-with-CI-CD/assets/81886509/1da0264b-43fd-432e-8092-0c6364669e98)<p>

The application access at http://localhost:80.<p>
![image](https://github.com/JonesKwameOsei/Dockerise-Build-Push-Deploy-with-CI-CD/assets/81886509/9fb6a408-e23a-4900-9c19-978cde31653d)<p>
![image](https://github.com/JonesKwameOsei/Dockerise-Build-Push-Deploy-with-CI-CD/assets/81886509/73cb42c5-cb18-4d37-842c-d72cb9e411f3)<p>
![image](https://github.com/JonesKwameOsei/Dockerise-Build-Push-Deploy-with-CI-CD/assets/81886509/8807b0d5-6860-4192-b98b-1660ec132e7a)<p>
![image](https://github.com/JonesKwameOsei/Dockerise-Build-Push-Deploy-with-CI-CD/assets/81886509/88e23d9b-4c7c-4594-9f63-82fccd65b89c)<p>
![image](https://github.com/JonesKwameOsei/Dockerise-Build-Push-Deploy-with-CI-CD/assets/81886509/f16372fc-1d9f-4630-b33b-3d0839a16642)<p>


It will be good to push this image to my docker hub repository so that we can compare this image to the one built with automation - GitHub actions pipeline. <p>
I will log into my Docker Hub account from the terminal.
```
docker login
```
Then push the image to Docker Hub:<p>
```
docker push kwameds/mycvwebapp:1.0.0
```
![image](https://github.com/JonesKwameOsei/myCV_WebApp/assets/81886509/4fd4e6f8-69b0-4601-9035-c3666c6c0c7f)<p>
Imaged has been pushed successfully.<p>
![image](https://github.com/JonesKwameOsei/myCV_WebApp/assets/81886509/72e672ca-5841-4736-8927-11445b3d9702)<p>
Let us get a confirmation from Docker Hub. <p>
![image](https://github.com/JonesKwameOsei/myCV_WebApp/assets/81886509/39e8d72b-31a3-4f31-8909-e00a6764dea9)<p>
![image](https://github.com/JonesKwameOsei/myCV_WebApp/assets/81886509/8b55a231-7903-4357-bb6b-32dc18c0206c)<p>
![image](https://github.com/JonesKwameOsei/myCV_WebApp/assets/81886509/9b8f606a-c9ad-42dc-9d5c-7775dcc1b8a5)<p>


### 7. Run the GitHub Actions Workflow

2. Add the changes and commit
```
git add .
git commit -m "Added configuration files"
```
3. Push the commit to trigger the actions
```
git push origin main
```
Commit and push the changes to the `main` branch of your GitHub repository. This will trigger the `docker-build.yml` workflow, which will automatically build and push your Docker image to the GitHub Container Registry.In the "Actions" tab of the GitHub repository, we can monitor the progress and check the status of the workflow run.<p>

The pipeline actions has been executed:<p>
![workflow1](https://github.com/JonesKwameOsei/myCV_WebApp/assets/81886509/17403ab6-559a-44f7-9c19-3cb3b811fa2e)<p>
![workflow2](https://github.com/JonesKwameOsei/myCV_WebApp/assets/81886509/34de5e66-5437-496c-a152-8d43a3a8f7ac)<p>
![workflow3](https://github.com/JonesKwameOsei/myCV_WebApp/assets/81886509/c6e5832e-7bd7-4f28-8f9c-68e69f7d0839)<p>
![workflow4](https://github.com/JonesKwameOsei/myCV_WebApp/assets/81886509/e619c1ed-babf-406e-ba62-6361563817ad)<p>

GitHub action completed. We will confirm if the the image is in the `Docker Hub` registry. 
![image](https://github.com/JonesKwameOsei/myCV_WebApp/assets/81886509/0e101d3d-c3e3-4929-81b2-146142fb0108)<p>
Image in docker hub.<p>
![image](https://github.com/JonesKwameOsei/myCV_WebApp/assets/81886509/24ab17e0-045b-43de-a8a6-e85a8f123690)<p>
![image](https://github.com/JonesKwameOsei/myCV_WebApp/assets/81886509/9cac5376-ae6d-4454-9a9e-78a9b6f2689d)<p>

### Test the New Image
Having automatically build and pushed the image to Docker Hub, it is important to run the application to verify if it is working. Run:
```
docker run -d -p 8081:8080 --name newCVapp kwameds/mycv_webapp:2.0.0
```
Docker pulled the image from my repo in the Docker Hub registry as it couldn't find the image locally. Docker goes ahead to run a container based on that image. The `-d` option runs the container in detached mode, `-p 8081:8080` maps the `host's port 8081` to the `container's port 8080`, and `--name newCVapp` assigns the name **newCVapp** to the container.<p>
![image](https://github.com/JonesKwameOsei/myCV_WebApp/assets/81886509/6c4c691c-ba28-4537-8762-a3a93288a4f9)<p>
Let us confirm if the image is now in our local registry by running:
```
docker images        # lists all images
docker ps            # lists containers running
```
![image](https://github.com/JonesKwameOsei/myCV_WebApp/assets/81886509/8167e1df-968e-4061-ab5d-28df771662ae)<p>
Next, We will view the web app in the browser by typing: `http://localhost:8081`. <p>
The web page is accessible: <p>
![image](https://github.com/JonesKwameOsei/myCV_WebApp/assets/81886509/47d9669b-6966-44b3-a3ce-d10e15f1267a)<p>
![image](https://github.com/JonesKwameOsei/myCV_WebApp/assets/81886509/b12bf5b8-014e-4427-8c97-5cd0fb8f11a4)<p>
![image](https://github.com/JonesKwameOsei/myCV_WebApp/assets/81886509/8c838419-cf0d-4efe-ad53-94175172cb28)<p>
![image](https://github.com/JonesKwameOsei/myCV_WebApp/assets/81886509/77142a96-8f4f-4f29-9399-f59889ffe369)<p>

### Modify the codebase and Apply Continous Integration to Build and Push Updates
The web app has been tested and it is working as expected. However, there there are some updates that needs to be done. In the privacy page, it can be observed that the point no.6 states the website may have external links. This needs to be updated because there are no external links. <p>
![image](https://github.com/JonesKwameOsei/myCV_WebApp/assets/81886509/59bae05c-d927-467b-b6d3-a8ceaa73bded)<p>
Privacy policy updated and the continuous integration pipeline executed.<p>
![image](https://github.com/JonesKwameOsei/myCV_WebApp/assets/81886509/c75148d0-8a15-413d-8c7f-30ef3daf13ac)<p>
Image pushed to my repo in Docker Hub regisry.<p>
![image](https://github.com/JonesKwameOsei/myCV_WebApp/assets/81886509/b7fc0072-f5f7-4d41-8ed9-f8c7e1ab2809)<p>
Run image and view in the browser to confirm the update. <p> 
```
docker run -d -p 8082:8080 --name UpdatedCVapp kwameds/mycv_webapp:2.1.0
```
Image pulled and container created:<p>
![image](https://github.com/JonesKwameOsei/myCV_WebApp/assets/81886509/765b4ff1-bca7-46c8-bc51-f220087486e6)<p>
![image](https://github.com/JonesKwameOsei/myCV_WebApp/assets/81886509/ea675287-6af5-4282-8765-1dcf10f6742b)<p>

The web application is accessible:<p>
![image](https://github.com/JonesKwameOsei/myCV_WebApp/assets/81886509/24d90a1d-d589-404e-9540-6916e7097c61)<p>
Privacy policy is updated:<p>
![image](https://github.com/JonesKwameOsei/myCV_WebApp/assets/81886509/6f55f56c-e9f1-430f-9030-f24d354c97ed)<p>

## Conclusion
By leveraging GitHub Actions, I have seamlessly integrated Docker into a development pipeline. This approach allow us to automatically build and push Docker images to a registry, ensuring that the deployments are consistent and up-to-date with the latest changes to the codebase. This setup provides a reliable and efficient way to manage the Docker-based applications.
