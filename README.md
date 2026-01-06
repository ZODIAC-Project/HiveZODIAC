# HivePBAC
## Running the Image using the local Extension folder:
### Setup
 - HivePBAC is an extension for HiveMQ, and can be deployed through docker compose
 - we will built it using docker and then map it into the deployed HiveMQ extension folder
#### We first need to get the default extensions ("allow all")
1. create a folder locaclly for the extensions (eg. ./hivemq-extensions)
     ``` $ mkdir hivemq-extensions```
2. on first start, map it somewhere else (see example in yaml)
      unccoment following line in yaml file and comment other maping line 
      ```- ./hive-extensions/:/opt/hivemq/extension_TEMP```
3. start using docker compose
4. go inside the container and copy the extension from /opt/hivemq/extensions into the mapped folder

      ```$ docker exec -it <container_id> /bin/bash```
      ```$ cp -r /opt/hivemq/extensions/* ./<your_folder_name>```
      ```$ exit```
      
5. stop the container and change back the mapping line in yaml file
#### 2. From now on, you can use the local folder as the actual extension folder
#### 3. Build HivePBAC through docker
1. ```$ docker run -it --rm --name my-maven-project -v "$(pwd)":/usr/src/mymaven -w /usr/src/mymaven maven:3.8.6-jdk-11 mvn package```
2. move the contents of the zip file into the folder
      ```$ unzip target/HivePBAC-1.0.0-SNAPSHOT.zip -d ./hivemq-extensions/HivePBAC```
#### 4. Start the HiveMQ container using docker compose
#### 5. The extension should now be active
---
## Build Docker image with Extension for Kubernetes deployment
0. Remove all extensions from the local extension folder except HivePBAC (If not build - follow step 3 (Build HivePBAC through docker) from above)

2. Build the docker image directly with the extension included
   ```$ docker build -f Dockerfile.hivemq -t hivezodiac:latest . ```

3. (Optional) Test run the Container locally
   ```$ docker run -p 8080:8080 -p 1883:1883 --name hivezodiac-test hivezodiac:latest```

4. The steps to deploy the image to a kubernetes cluster are described below. The project keeps a rendered Kubernetes manifest at `k8s/deployment.yaml`.

## Deployment

- **Apply (recommended simple flow):**

      ```bash
      kubectl apply -f k8s/deployment.yaml
      ```

      This creates the `Service` and `Deployment` included in `k8s/deployment.yaml`. The manifest uses the image `hivezodiac:latest` by default — update the `image:` field if you push the image to a registry (for example `your-registry/hivezodiac:tag`) and add `imagePullSecrets` as needed.

- **Notes:**
      - The manifest contains a `ClusterIP` Service exposing ports `8080` (HTTP) and `1883` (MQTT).
      - The `Deployment` sets `replicas: 1`.

- **Local testing:**

      ```bash
      # build image
      docker build -f Dockerfile.hivemq -t hivezodiac:latest .

      # run locally
      docker run -p 8080:8080 -p 1883:1883 --name hivezodiac-test hivezodiac:latest
      ```