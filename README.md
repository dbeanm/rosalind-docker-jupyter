# rosalind-docker-jupyter
Quick setup for jupyter in docker on KCL rosalind

# Openstack
* Create an ubuntu 20.04 instance and attach an empty volume
* Assign a floating IP and open ports 22 and 8888

# Instance config
SSH into the instance

Install docker (details https://docs.docker.com/engine/install/ubuntu/)

```
sudo apt-get update

```

```
sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
```
```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```
```
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
```
sudo apt-get update
```
```
sudo apt-get install docker-ce docker-ce-cli containerd.io
```

Set up the mounted volume
```
sudo mkfs.ext4 /dev/vdb
sudo mkdir /mnt/datastore
sudo mount /dev/vdb /mnt/datastore/
```

Start the docker container
```
sudo docker run -i -t -p 8888:8888 --name=jupyter \
--volume=/mnt/datastore/:/opt/notebooks \
 continuumio/anaconda3 /bin/bash -c "\
     conda install jupyter -y --quiet && \
     jupyter notebook \
     --notebook-dir=/opt/notebooks --ip='*' --port=8888 \
     --no-browser --allow-root"
```
Copy/paste the authorisation token from the terminal and go to
```
http://{your instance IP}:8888/{your auth token}
```

# Notes
You can just leave the terminal session or detach e.g. with ctrl+p,ctrl+q but don't ctrl+c (i.e. don't stop the juputer container)

If the container crashes or dies for whatever reason, it can be restarted with 
```
sudo docker start jupyter
```

Optionally verify docker install with
```
sudo docker run hello-world
```
