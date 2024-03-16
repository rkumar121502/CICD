# Prerequisites
1. Jenkins server should be on Linux system.  
2. NodeJs, GO should be configured in the Manage Jenkins -> Configure  
   
Contents:

- [Requirements](#requirements)
- [Configuration](#configuration)
- [Usage](#usage)
- [Output](#output)

## Requirements

1. Make sure you have the latest versions of **Docker** and **Docker Compose** installed on your machine.  
2. Github Webhooks should be configured on the repos (https://github.com/rkumar121502/go_lang.git and https://github.com/rkumar121502/nextjs.git)  
3. Server username and password/privateKey should be stored in the Credentials in Jenkins  (`jenkinsSetupCreds`)
4. GITHub, Git and SSH plugins should be installed on the Jenkins  

## Configuration

1. Create Build Jenkins job using `Jenkins_Build` files (`go_lang_build` and `nextjs_build`) from `go_lang` and `nextjs` directory  
2. Create Deploy Jenkins job using `Jenkins_Deploy` files (`Deploy_go_lang` and `Deploy_nextjs`) from `go_lang` and `nextjs` directory  
3. while creating the Build jobs, we need to provide the repository name in the `Github Project` section  

## Usage

We can use the `docker-compose.yml` file to run the containers in the same network but to attain this, we need to clone both the nextjs and go_lang  
repos and need to place the docker-compose.yml file in the root directory where these 2 repos are cloned.

## Output

On pushing the code to the github repos, corresponding jobs will trigger and do the following tasks:  
1. Checking out the code  
2. Building the dependencies if `prebuild` step is present.  
3. Will do the Linting, Formatting (in nextjs only) and Unit testing  
4. Further it will build the code locally, building the Dockerfile to create images and pushing the images to Docker Hub.  
5. Finally archiving the artifacts means docker image into a tar file so downstream job can use it for deployment.  
6. Triggering the downstream deploy jobs  

`Deploy` Jobs will do the following tasks.  
1. Fetching the artifacts from the build job using `CopyArtifacts` build step.
2. Loading the docker image from .tar file
3. Doing ssh to the server, copying the content of worspace into the `Staging` directory and running the docker container on specific ports  
   (3000 for nextjs and 8080 for go_lang)
