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
name: Build and Push Docker Image CI

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v2

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v1

      - name: Login to Docker Hub
        uses: docker/login-action@v1
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}

      - name: Build and push Docker image
        uses: docker/build-push-action@v2
        with:
          context: .
          push: true
          tags: kwameds/portfolio_webapp:1.1.0
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
docker run -d -p 8080:8080 --name portfolio-webapp kwameds/portfolio-webapp:1.0.0
```
![image](https://github.com/JonesKwameOsei/Dockerise-Build-Push-Deploy-with-CI-CD/assets/81886509/1da0264b-43fd-432e-8092-0c6364669e98)<p>
![image](https://github.com/JonesKwameOsei/Dockerise-Build-Push-Deploy-with-CI-CD/assets/81886509/dbf1a754-9f8a-4b9c-9aea-2d33986ac648)<p>

The application access at http://localhost:80.<p>
![image](https://github.com/JonesKwameOsei/Dockerise-Build-Push-Deploy-with-CI-CD/assets/81886509/9fb6a408-e23a-4900-9c19-978cde31653d)<p>
![image](https://github.com/JonesKwameOsei/Dockerise-Build-Push-Deploy-with-CI-CD/assets/81886509/73cb42c5-cb18-4d37-842c-d72cb9e411f3)<p>
![image](https://github.com/JonesKwameOsei/Dockerise-Build-Push-Deploy-with-CI-CD/assets/81886509/8807b0d5-6860-4192-b98b-1660ec132e7a)<p>
![image](https://github.com/JonesKwameOsei/Dockerise-Build-Push-Deploy-with-CI-CD/assets/81886509/88e23d9b-4c7c-4594-9f63-82fccd65b89c)<p>
![image](https://github.com/JonesKwameOsei/Dockerise-Build-Push-Deploy-with-CI-CD/assets/81886509/f16372fc-1d9f-4630-b33b-3d0839a16642)<p>


Next, I will push this image to my docker hub repository so that we can compare this image to the one built with automation - GitHub actions pipeline. <p>
I will log into my Docker Hub account from the terminal.
```
docker login
```
Then push the image to Docker Hub:<p>
```
docker push kwameds/portfolio-webapp:1.0.0
```
Imaged has been pushed successfully.<p>
![image](https://github.com/JonesKwameOsei/Dockerise-Build-Push-Deploy-with-CI-CD/assets/81886509/d2c3bde6-a1d9-4ff2-b05b-99d879aecfbd)<p>

Let us get a confirmation from Docker Hub. <p>
![image](https://github.com/JonesKwameOsei/Dockerise-Build-Push-Deploy-with-CI-CD/assets/81886509/a2bfd12b-4ef1-44f4-9fb4-68c8d5db96d7)<p>
![image](https://github.com/JonesKwameOsei/Dockerise-Build-Push-Deploy-with-CI-CD/assets/81886509/b6563afc-2bee-41cc-ab4e-bc033eb04f3d)<p>

### 8. Automate the deployment process with a GitHub CI/CD Pipeline 

2. Add the changes and commit
```
git add .
git commit -m "Added configuration files"
```
3. Push the commit to trigger the actions
```
git push origin main
```
Commit and push the changes to the `main` branch of your GitHub repository. This will trigger the `actions.yml` workflow, which will automatically build and push your Docker image to the GitHub Container Registry. In the `Actions` tab of the GitHub repository, we can monitor the progress and check the status of the workflow run.<p>

The pipeline actions has been executed:<p>
![image](https://github.com/JonesKwameOsei/Dockerise-Build-Push-Deploy-with-CI-CD/assets/81886509/f7800154-04cc-47db-8199-8e32c1e436f5)<p>
![image](https://github.com/JonesKwameOsei/Dockerise-Build-Push-Deploy-with-CI-CD/assets/81886509/774cf5a0-c38b-4825-9994-794aa7756ea7)<p>

GitHub action completed. We will confirm if the the image is in the `Docker Hub` registry. 
Image in docker hub.<p>
![image](https://github.com/JonesKwameOsei/Dockerise-Build-Push-Deploy-with-CI-CD/assets/81886509/eda93fcc-321a-4b01-80c7-b4f7faf45a19)<p>
![image](https://github.com/JonesKwameOsei/Dockerise-Build-Push-Deploy-with-CI-CD/assets/81886509/dddf2fb6-5888-4877-8b8d-77965917e5be)<p>


### Test the New Image
Having automatically build and pushed the image to Docker Hub, it is important to run the application to verify if it is working. Run:
```
docker run -d -p 81:80 --name newPortfolioapp kwameds/portfolio_webapp:1.1.0
```
Docker pulled the image from my repo in the Docker Hub registry as it couldn't find the image locally. Docker goes ahead to run a container based on that image. The `-d` option runs the container in detached mode, `-p 8081:8080` maps the `host's port 81` to the `container's port 80`, and `--name newPortfolioapp` assigns the name **newCVapp** to the container.<p>
![image](https://github.com/JonesKwameOsei/Dockerise-Build-Push-Deploy-with-CI-CD/assets/81886509/6f73087a-bd34-4b33-904c-2c1ee6404c4c)<p>

Let us confirm if the image is now in our local registry by running:
```
docker images        # lists all images
docker ps            # lists containers running
```
![image](https://github.com/JonesKwameOsei/Dockerise-Build-Push-Deploy-with-CI-CD/assets/81886509/13f8cb74-a05c-4be2-a7d8-cff6c75adb04)<p>

Next, We will view the web app in the browser by typing: `http://localhost:8081`. <p>
The web page is accessible: <p>
![image](https://github.com/JonesKwameOsei/Dockerise-Build-Push-Deploy-with-CI-CD/assets/81886509/2413cf0b-334a-44e6-a96c-d148daeb90ad)<p>
![image](https://github.com/JonesKwameOsei/Dockerise-Build-Push-Deploy-with-CI-CD/assets/81886509/2e5cfc1f-4f34-418e-8bcb-9e2d45401cea)<p>
![image](https://github.com/JonesKwameOsei/Dockerise-Build-Push-Deploy-with-CI-CD/assets/81886509/0029ba03-98ae-4598-ab05-273163f495bc)<p>
![image](https://github.com/JonesKwameOsei/Dockerise-Build-Push-Deploy-with-CI-CD/assets/81886509/6078e7ef-b9eb-45f8-bb8b-968baf013578)

### Modify the codebase and Apply Continous Integration to Build and Push Updates
I will change the **professional summary** under `Intro` from: "A DevOps Engineer proficient in Linux, Git & Github, Jenkins, Python, Bash scripting, Terraform, Kubernetes, Docker, Ansible, AWS, Azure and more...", To: "An experienced DevOps Engineer proficient in Linux, Git & Github, Jenkins, Python, Bash scripting, Terraform, Kubernetes, Docker, Ansible, AWS, Azure and more...". Here, I have updated the app version to V1.2.0. 

Professional summary updated and the continuous integration pipeline executed.<p>
![image](https://github.com/JonesKwameOsei/Dockerise-Build-Push-Deploy-with-CI-CD/assets/81886509/4dc4470e-0e14-4d0d-b0b0-dc5a10ba0da6)<p>
![image](https://github.com/JonesKwameOsei/Dockerise-Build-Push-Deploy-with-CI-CD/assets/81886509/506a99a8-0e22-4824-a36f-b6352cb07045)<p>

Image pushed to my repo in Docker Hub regisry.<p>
![image](https://github.com/JonesKwameOsei/Dockerise-Build-Push-Deploy-with-CI-CD/assets/81886509/11a6626d-eb5d-4a14-94c3-55df324ac6e6)<p>

Run image and view in the browser to confirm the update. <p> 
```
docker run -d -p 82:80 --name UpdatedPortfolioapp kwameds/portfolio_webapp:1.2.0
```
Image pulled and container created:<p>
![image](https://github.com/JonesKwameOsei/Dockerise-Build-Push-Deploy-with-CI-CD/assets/81886509/78b800df-d501-43da-80bf-c720b70f2a04)<p>

The web application is accessible with `update`:<p>
![image](https://github.com/JonesKwameOsei/Dockerise-Build-Push-Deploy-with-CI-CD/assets/81886509/d4805328-2c66-480b-9c3c-1ae90664ecc1)<p>


## Conclusion
I have successfully integrated Docker into a development pipeline using `GitHub Actions`. This method enables automated build and push Docker images to a registry, ensuring that deployments are consistent and always reflect the latest code changes. This setup offers a dependable and effective way to handle Docker-based applications.

## What is Next?
Having completed the builds, I will deploy these dockerised applications (CV and Portfolio web apps) with `K8s`. 
