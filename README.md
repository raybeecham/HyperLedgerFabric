# Guide: Setting up Hyperledger Fabric Test Network and Hyperledger Explorer

This guide presents a detailed, step-by-step instruction on setting up the Hyperledger Fabric Test Network and Hyperledger Explorer. As these instructions may vary depending on several factors, it's recommended to also refer to the official installation instructions found at [Hyperledger Fabric Installation](https://hyperledger-fabric.readthedocs.io/en/latest/install.html).

## 1. Initialize the Droplet

- Start by creating a new droplet. Set the desired locale (use `en_US.UTF-8` if unsure).
- Next, update and upgrade the system by running these commands:

```bash
sudo dpkg-reconfigure locales
sudo apt-get update && sudo apt-get upgrade
```

## 2. Create a New User
Installing Hyperledger Explorer as the root user can lead to errors. To circumvent this, create a new user (we use "ray" as an example).

- Run the following commands to create a new user and set up a password:
```bash
sudo adduser ray
sudo usermod -aG sudo ray
```
- These commands create the new user "ray" and grant "ray" sudo access. Switch to the new user using: su - ray.

## 3. Install Prerequisites
Fabric and Explorer require Docker to run. Make sure you have the necessary prerequisites installed:

- Install the necessary packages:

```bash
sudo apt-get install git curl docker-compose -y
```
- Start the Docker daemon:

```bash
sudo systemctl start docker
sudo systemctl enable docker
``````

- If needed, install additional dependencies (e.g., nodejs, npm, python)
- Install golang:

```bash
wget https://dl.google.com/go/go1.13.6.linux-amd64.tar.gz
tar -xzvf go1.13.6.linux-amd64.tar.gz
sudo mv go/ /usr/local
```
- Edit the 'GOPATH' in '.bashrc':
```bash
pico ~/.bashrc
```
- Add these lines to the end of '.bashrc' file:
```bash
export GOPATH=/usr/local/go
export PATH=$PATH:$GOPATH/bin
```
- Exit and log back in as 'ray'
```bash
exit
su - ray
```

## 4. Set Up Hyperledger Fabric Test Network
Now that the dependencies are installed, you are ready to set up Hyperledger Fabric:
- To get the install script run this script:

```bash
curl -sSLO https://raw.githubusercontent.com/hyperledger/fabric/main/scripts/install-fabric.sh && chmod +x install-fabric.sh
```
- Use Docker to download the Fabric Container Images, download binaries and samples with either command:

```bash
./install-fabric.sh docker samples binary
```
or
```bash
./install-fabric.sh d s b
```

- Navigate to the test-network with this command:
```bash
cd fabric-samples/test-network
```
- Bring up the network, create a channel between Org1 and Org2, and join their peers to the channel. Run this command to create the channel with the default name of mychannel:
```bash
./network.sh up createChannel
```

## 5. Download Files Required for Hyperledger Explorer
- Create a new folder for Explorer:

```bash
cd ~/
mkdir explorer
cd explorer/
```

- Download the required files:
```bash
wget https://raw.githubusercontent.com/hyperledger/blockchain-explorer/master/examples/net1/config.json
wget https://raw.githubusercontent.com/hyperledger/blockchain-explorer/master/examples/net1/connection-profile/test-network.json -P connection-profile
wget https://raw.githubusercontent.com/hyperledger/blockchain-explorer/master/docker-compose.yaml
```
## 6. Copy the Test Network Crypto Artifact Directory to Explorer Folder
```bash
cp -r ~/fabric-samples/test-network/organizations/   ~/explorer/
```
## 7. Edit docker-compose.yaml
- Open the file:
```bash
pico docker-compose.yaml
```

-Ensure the volumes section looks like below:
```bash
- ./config.json:/opt/explorer/app/platform/fabric/config.json
- ./connection-profile:/opt/explorer/app/platform/fabric/connection-profile
- ./organizations:/tmp/crypto
- walletstore:/opt/wallet
```

- Optionally, you can edit the test-network.json file if you want to change the password and id:
```bash
pico connection-profile/test-network.json
```

- Default username and password:
```bash
"id": "exploreradmin",
"password": "exploreradminpw"
```

## 8. Start Hyperledger Explorer
- Start Hyperledger Explorer:
```bash
docker-compose up -d
```

- If you made any changes, clear them out with this command and start again:
```bash
docker-compose down -v
```



- Access the Hyperledger Explorer by navigating to http://localhost:8080 in your local browser. You will be prompted to enter your credentials. Enter "exploreradmin" and "exploreradminpw".

Always consult the official documentation for more detailed installation instructions and updates.

![specialization-certificate](https://github.com/raybeecham/HyperLedgerFabric/blob/main/assets/Hyperledger%20Explorer.png)
