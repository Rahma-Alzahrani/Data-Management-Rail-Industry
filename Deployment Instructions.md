# Deployment Instructions

***Note:*** The following instructions are for the local deployment of the network within *Docker Containers*. The entire network will be hosted on one computer.

***Note:*** The following instructions are for the local deployment of the network on a *Linux* system (Ubuntu 20.04).

## Requirements

The following prerequisites are required for the deployment of the network:
- Git
- cURL: Latest Package
- Golang: Version *1.13.x*
- Node: Version *8.9* or *10.15.3* or higher
    - ***Note:*** Version *9* is not supported
- npm: Version *5.x or higher*
- Python: Version *2.7.x*
- Docker Engine: Version *17.06.2-ce* or higher
- Docker Compose: Version *1.14* or higher
- Fabric Hyperledger 2.2 Binaries, Samples and Docker Images
- Angular CLI
- MongoDB

In addition to these, there are a numerous of path configuration that are required. These are outlined within the "***`prereq.sh`***" script.

To check which of the prerequisites are installed, and their version, you can run the following script: "***`prereq_version_check.sh`***". With the top level of the repository set as the working directory, the script can be executed with the following terminal command: 
``` 
bash prereq_version_check.sh
``` 

***Note:*** You may be prompted to enter the password for the current user.

To install all of the prerequisuites (apart from MongoDB) and to set up the required paths, run the following script: "***`preqreq.sh`***". With the top level of the repository set as the working directory, the script can be executed with the following terminal command: 
```
bash prereq.sh
```
***Note:*** The "***`prereq.sh`***" script can still be executed if some/all of the prerequisites are already installed.

***Note:*** You may be prompted to enter the password for the current user.

***Note:*** You may be prompted to continue the installation of each package. Enter "Y" then press enter to accpet.

***Note:*** The commands within the "***`prereq.sh`***" script can be executed manually within a terminal.

Upon completion of the script, restart the terminal. Then type the following command into the terminal: "***`peer`***". The following output indicates that the prerequistes and paths have been configured correctly:

```
Usage:
  peer [command]

Available Commands:
  chaincode   Operate a chaincode: install|instantiate|invoke|package|query|signpackage|upgrade|list.
  channel     Operate a channel: create|fetch|join|joinbysnapshot|joinbysnapshotstatus|list|update|signconfigtx|getinfo.
  help        Help about any command
  lifecycle   Perform _lifecycle operations
  node        Operate a peer node: start|reset|rollback|pause|resume|rebuild-dbs|upgrade-dbs.
  snapshot    Manage snapshot requests: submitrequest|cancelrequest|listpending
  version     Print fabric peer version.

Flags:
  -h, --help   help for peer

Use "peer [command] --help" for more information about a command.
```

### Installation of MongoDB
For the installation of MongoDB, please see the following instructions: https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/#install-mongodb-community-edition

***Note:*** Please ensure you select the version of Ubuntu you are currently running.

## Deployment of the Network

The folllowing section will outline how to deploy the network locally within *Docker Containers*. The deployment process has been split into the required tasks.

### Creation of Certificates
The first stage requires the creation of certificates from the certificate authority. To do this, run the following commands within the terminal. ***Note:*** *The following commands assume that the intial working directory is set to the top level of the repository*.

**1.** Altering the current working directory (Data-Management-Rail-Industury/network/channel/create-certificate-with-ca):
```
cd network/channel/create-certificate-with-ca
```
**2.** Intialising the *Docker Containers* required for certificate creation:
```
docker-compose up -d
```
***Note:*** When running the 2nd and 7th command, you may get the following output:
```
ERROR: Couldn't connect to Docker daemon at http+docker://localhost - is it running?
```
If this is the case, run the following command:
```
sudo chmod 666 /var/run/docker.sock
```
**3.** Executing the shell script to create the certificates:
```
./create-certificate-with-ca.sh
```
***Note:*** When running the 3rd, 5th, 8th, 9th, 10th and 16th command, you may get the following output:
```
bash: ./'filename'.sh: Permission denied
```
If this if the case, run the following command: 
```
sudo chmod u+rwx 'filename'.sh
```

### Creation of Artifacts
The next stage requires the creation of the network artifacts (genesis.block). To do this, run the following commands within the terminal. ***Note:*** *The following commands assume that the intial working directory is set to the directory outlined in command one.*

**4.** Altering the current working directory (Data-Management-Rail-Industury/network/channel):
```
cd ..
```
**5.** Executing the shell script to create the artifacts:
```
./create-artifacts.sh
```

### Creation of Network
With the artifacts created, the network can be intialised within *Docker Containers*. To do this, run the following commands within the terminal. ***Note:*** *The following commands assume that the intial working directory is set to the directory outlined in command four.*

**6.** Altering the current working directory (Data-Management-Rail-Industury/network):
```
cd ..
```
**7.** Creating the network within *Docker Containers*:
```
docker-compose up -d
```

### Creation of Channel
With the network intialised, the peers need to be connected via a channel. To do this, run the following commands within the terminal. ***Note:*** *The following commands assume that the intial working directory is set to the directory outlined in command six.*

**8.** Executing the shell script to create the channel:
```
./createChannel.sh
```

### Deployment of Chaincode
The final stage in the network setup is the deployment of the chaincode onto the channel. To do this, run the following commands within the terminal. ***Note:*** *The following commands assume that the intial working directory is set to the directory outlined in command six.*

**9.** Executing the shell script to deploy the chaincode:
```
./deployChaincode.sh
```
**10.** Executing the script to upgrade the chaincode:
```
./upgradeChaincode.sh
```

### Accessing CouchDB
With the network now set up, admins can use CouchDB to monitor the network. To access CouchDB, type the following into a browser:

```
localhost:5984/_utils
```
This will take you to the log in screen. For first time access, the log in credentials are:
```
Username: admin
Password: adminpw
```
The password should be changed via the 'Change Password' option on the toolbar.

## Deployment of API Server

The following section will outline how to deploy the API server required to acess the front-end of the network. To do this, run the following commands within the terminal, ***Note:*** *The following commands assume that the intial working directory is set to the top level of the repository.*

**11.** Starting MongoDB:
```
sudo systemctl start mongod
```
**12.** Altering the current working directory (Data-Management-Rail-Industry/api-server):
```
cd api-server
```
**13.** Installing the Build-Essential package:
```
sudo apt-get install build-essentials
```
**14.** Running npm install:
```
npm i
```
**15.** Altering the current working directory (Data-Management-Rail-Industry/api-server)/src/config:
```
cd src/config
```
**16.** Executing the shell script to generater ccp:
```
./generate-ccp.sh
```
**17.** Altering the current working directory (Data-Management-Rail-Industry/api-server/src)):
```
cd ..
```
**18.** Building the API server:
```
npm run build
```
**19.** Starting the API server:
```
npm run start
```
Once these commands have been executed, the API server should be running. ***Note:*** *Leave this terminal open and running. To end the server, use "ctrl + c."*

## Deployment of the Front End
The following section will outline how to deploy the Front End of the network.  To do this, run the following commands within the a **new terminal**. ***Note:*** *The following commands assume that the intial working directory is set to the top level of the repository.*

**20.** Altering the current working directory (Data-Management-Rail-Industry/Front-end/data-management):
```
cd Front-end/data-management
```
**21.** Running npm install:
```
npm i
```
**22.** Altering the current working directory (Data-Management-Rail-Industry/Front-end/data-management/src/app):
```
cd src/app
```
**23.** Building and serving of angular app:
``` 
ng s
```
Once these commands have been executed, the front-end should be running. ***Note:*** *Leave this terminal open and running. To end the server, use "ctrl + c."*

To access the front end, go to a browser and type the following:
```
localhost:4200
```
This will take you to log in/sign up page of the front end.
