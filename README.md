# HivePBAC

## Table of Contents
- [Build Docker image with Extension for Containerized Deployment](#build-docker-image-with-extension-for-containerized-deployment)
- [Deployment](#deployment)   
- [Running the Image using the local Extension folder (Extension Development)](#running-the-image-using-the-local-extension-folder-extension-development)
---
## Build Docker image with Extension for Containerized Deployment
0. Remove all extensions from the local extension folder except HivePBAC (If not build allready- follow step 3 [(Running the Image using the local Extension folder (Extension Development)](#running-the-image-using-the-local-extension-folder-extension-development)))

2. Build the docker image directly with the extension included
   ```$ docker build -f Dockerfile.hivemq -t hivezodiac:latest . ```

3. (Optional) Test run the Container locally
   ```$ docker run -p 8080:8080 -p 1883:1883 --name hivezodiac-test hivezodiac:latest```

4. The steps to deploy the image to a kubernetes cluster are described below. The project keeps a Kubernetes manifest at `k8s/deployment.yaml`.

## Deployment

The deployment will be done with the Deployment file in `k8s/deployment.yaml`.
This file creates the zodiac `namespace`, a `Service` and a `Deployment` for the HiveZODIAC broker.

### Before you begin:
**Depending on the deployment method you might need to adjust the deployment manifest.**
1. If the Container is stored locally, change the image field in `k8s/deployment.yaml` to the name of the image (The docker-compose file provided in this repo calls it `hivezodiac:local`).
2. The Deployment file uses a ImagePullSecret for the deployment from a registry. 
Remove it from the deployment manifest if you are deploying locally.

### Steps:
1. Create the `zodiac` namespace(This does not need to be done if you are deploying without ImagePullSecrets):
```bash
kubectl create namespace zodiac
```
2. Create the ImagePullSecret (This does not need to be done if you are deploying without ImagePullSecrets):
```bash
kubectl create secret docker-registry regcred \
--docker-server=git.tu-berlin.de:5000 \
--docker-username='' \
--docker-password='' \
-n zodiac
```

3. Apply the deployment manifest:
```bash
kubectl apply -f k8s/deployment.yaml
```

## Running the Image using the local Extension folder (Extension Development):
### Setup
 - HivePBAC is an extension for HiveMQ, and can be deployed through docker compose
 - we will built it using docker and then map it into the deployed HiveMQ extension folder
#### 1. We first need to get the default extensions ("allow all")
1. create a folder locaclly for the extensions (eg. ./hivemq-extensions)
     ``` $ mkdir hivemq-extensions```
2. on first start, map it somewhere else (see example in yaml)
      unccoment following line in yaml file and comment other maping line 
      ```- ./hive-extensions/:/opt/hivemq/extension_TEMP```
3. start using docker compose
4. go inside the container and copy the extension from /opt/hivemq/extensions into the mapped folder

      <!--```$ docker exec -it <container_id> /bin/bash```
      ```$ cp -r /opt/hivemq-ce-2023.6/extensions/* ./<your_folder_name>```
      ```$ exit```-->
      ```docker cp hivezodiac-hivemq-1:/opt/hivemq-ce-2023.6/extensions/. ./<your_folder_name>```

      
5. stop the container and change back the mapping line in yaml file
#### 2. From now on, you can use the local folder as the actual extension folder
#### 3. Build HivePBAC through docker
1. ```$ docker run -it --rm --name my-maven-project -v "$(pwd)":/usr/src/mymaven -w /usr/src/mymaven maven:3.8.6-jdk-11 mvn package```
2. move the contents of the zip file into the folder
      ```$ unzip target/HivePBAC-1.0.0-SNAPSHOT.zip -d ./hivemq-extensions/HivePBAC```
#### 4. Start the HiveMQ container using docker compose
#### 5. The extension should now be active