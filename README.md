# HivePBAC

## Setup

 - HivePBAC is an extension for HiveMQ, and can be deployed through docker compose
 - we will built it using docker and then map it into the deployed HiveMQ extension folder
 - we first need to get the default extension ("allow all")
   - create the extension folder locally
   - on first start, map it somewhere else (see example in yaml)
   - start using docker compose
   - go inside the container and copy the extension from /opt/hivemq/extensions into the mapped folder
 - from now on, you can use the local folder as the actual extension folder
 - build HivePBAC through docker
   - `docker run -it --rm --name my-maven-project -v "$(pwd)":/usr/src/mymaven -w /usr/src/mymaven maven:3.3-jdk-8 mvn package`
   - move the contents of the zip file into the folder
 - it should™️ work now