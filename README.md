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
docker build -t kwameds/mycvwebapp:1.0.0 .
```
Image is built:<p>
```
[+] Building 569.1s (18/18) FINISHED                                docker:default
 => [internal] load build definition from Dockerfile                          0.1s
 => => transferring dockerfile: 904B                                          0.0s 
 => [internal] load metadata for mcr.microsoft.com/dotnet/sdk:8.0             1.0s 
 => [internal] load metadata for mcr.microsoft.com/dotnet/aspnet:8.0          0.9s 
 => [internal] load .dockerignore                                             0.0s
 => => transferring context: 464B                                             0.0s 
 => [build 1/7] FROM mcr.microsoft.com/dotnet/sdk:8.0@sha256:1e0c55b0ae732  554.6s 
 => => resolve mcr.microsoft.com/dotnet/sdk:8.0@sha256:1e0c55b0ae732f333818f  0.0s
 => => sha256:4369f7f50afbe743842fb8431f68ba4c280217854a4 15.73MB / 15.73MB  53.8s 
 => => sha256:9cca61ea7dcde9e148fce1ff995332d2ad533b2d 183.59MB / 183.59MB  472.2s 
 => => sha256:843791b459d2398954cec1204e9d601346fcc7793fc 30.69MB / 30.69MB  83.2s 
 => => extracting sha256:843791b459d2398954cec1204e9d601346fcc7793fcbc522033  0.6s 
 => => extracting sha256:9cca61ea7dcde9e148fce1ff995332d2ad533b2d4c5927d344d  2.5s 
 => => extracting sha256:4369f7f50afbe743842fb8431f68ba4c280217854a444db13a5  0.3s 
 => [base 1/2] FROM mcr.microsoft.com/dotnet/aspnet:8.0@sha256:a22d22bcedc  105.5s 
 => => resolve mcr.microsoft.com/dotnet/aspnet:8.0@sha256:a22d22bcedc67df31b  0.0s 
 => => sha256:026220c9919398c3ac236b91cbf15c448fb5383eed7 11.03MB / 11.03MB  37.4s 
 => => sha256:fb5d00a9ff0e9f7f92e11503fbbdf16143ca63c8c357d82841 153B / 153B  0.8s 
 => => sha256:4ede38c3f6aeccac1e49f0cee1f685968f6502c801 32.23MB / 32.23MB  104.7s 
 => => sha256:cc7f450a69e27d8e92017b26b10f914c0adbde22f39dea 3.28kB / 3.28kB  1.0s 
 => => sha256:09f376ebb190216b0459f470e71bec7b5dfa611d66b 29.15MB / 29.15MB  88.8s 
 => => sha256:898e6be7b1d63a481b0a404455fe5b3c65450c37074 18.52MB / 18.52MB  78.2s 
 => => extracting sha256:09f376ebb190216b0459f470e71bec7b5dfa611d66bf008492b  0.7s 
 => => extracting sha256:898e6be7b1d63a481b0a404455fe5b3c65450c37074a0e386d3  0.3s 
 => => extracting sha256:cc7f450a69e27d8e92017b26b10f914c0adbde22f39dead838e  0.0s 
 => => extracting sha256:4ede38c3f6aeccac1e49f0cee1f685968f6502c801605dd4538  0.4s 
 => => extracting sha256:fb5d00a9ff0e9f7f92e11503fbbdf16143ca63c8c357d828412  0.0s 
 => => extracting sha256:026220c9919398c3ac236b91cbf15c448fb5383eed772379acf  0.1s 
 => [internal] load build context                                             0.8s 
 => => transferring context: 16.63MB                                          0.7s 
 => [base 2/2] WORKDIR /app                                                   0.6s 
 => [final 1/2] WORKDIR /app                                                  0.0s 
 => [build 2/7] WORKDIR /src                                                  1.1s 
 => [build 3/7] COPY [myWebCVApp/myWebCVApp.csproj, myWebCVApp/]              0.0s 
 => [build 4/7] RUN dotnet restore "./myWebCVApp/myWebCVApp.csproj"           3.9s 
 => [build 5/7] COPY . .                                                      0.1s 
 => [build 6/7] WORKDIR /src/myWebCVApp                                       0.0s 
 => [build 7/7] RUN dotnet build "./myWebCVApp.csproj" -c Release -o /app/bu  5.2s 
 => [publish 1/1] RUN dotnet publish "./myWebCVApp.csproj" -c Release -o /ap  2.1s 
 => [final 2/2] COPY --from=publish /app/publish .                            0.1s 
 => exporting to image                                                        0.5s 
 => => exporting layers                                                       0.4s 
 => => exporting manifest sha256:458dae421596fdd372d413e651b829acb1d3eeb93ae  0.0s 
 => => exporting config sha256:76ef9ba27503224cac6394aabcde92b83d56fc04a9605  0.0s 
 => => exporting attestation manifest sha256:23aa0b687d5e7b874cb8e3b9d312acc  0.0s 
 => => exporting manifest list sha256:26af15f605adebd95eae2e82c44b8919e151ef  0.0s 
 => => naming to docker.io/kwameds/mycvwebapp:1.0.0                           0.0s 
 => => unpacking to docker.io/kwameds/mycvwebapp:1.0.0                        0.1s 

View build details: docker-desktop://dashboard/build/default/default/hcmeaq9cb3mq23pliwjm1o0z9

What's Next?
  View a summary of image vulnerabilities and recommendations → docker scout quickview
```
We can verify from on Docker CLI if the image is actually built. To do this, run:
```
docker images
```
![image](https://github.com/JonesKwameOsei/myCV_WebApp/assets/81886509/abedb5d2-e476-497e-9fc9-2a32433ea236)<p>
Verification from Docker Desktop:<p>
![image](https://github.com/JonesKwameOsei/myCV_WebApp/assets/81886509/5f2a882c-293e-49f7-be4d-4e75d4f20970)<p>

Next, we will run the Docker Image Locally. This is to test the image locally to ensure it works correctly.
```
docker run -d -p -- name mycvwebapp 8080:8080 kwameds/mycvwebapp:1.0.0
```
The application access at http://localhost:8080.<p>
![image](https://github.com/JonesKwameOsei/myCV_WebApp/assets/81886509/4a6a6940-c4a4-4442-b769-3e4c6fb7b2c3)<p>
The `Home Page` is live:<p>
![image](https://github.com/JonesKwameOsei/myCV_WebApp/assets/81886509/d50e3c87-6013-4e4a-ba9c-4e94ec229905)<p>
The **CV** page can be viewed when a user clicks on the `CV` tab at the top:<p>
![image](https://github.com/JonesKwameOsei/myCV_WebApp/assets/81886509/0cedbdd5-c2df-42a3-80f8-ff4047b8640c)<p>

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
