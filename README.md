
# TaxiRL Training

## Overview of code structure:
- **dltrainer**
Contains Dockerfile and kubernetes deoployment file for PyTorch trainer.

- **cache-server**
Contains Dockerfile and kubernetes deployment file for NGINX cache-server.

- **ku** script:
Contains commands to setup kubernetes cluster on Google Cloud

- **kuber-cluster-config.sh** script:
Contains parameters that can be configured to customize the kubernetes setup

## Initial setup
Before proceeding, please ensure you have following packages installed locally; follow instructions available online.
1. Docker
1. Google Cloud SDK: After installing Google Cloud SDK, run `gcloud init`


## Kubernetes cluster setup:
1. Set parameters in `kube-cluster-config.sh`
2. Call `./ku init`. Please don't kill the execution inbetween. The command does the following:
    - Creates a kubernetes clusters
    - Creates a GPU node-pool with each node containing requested number of GPUs
    - Install cache-server and deploys it into the cluster

## Deploying PyTorch trainer on Kubernetes cluster:
Change to dltrainer folder.
1. Three important files are
    - train.py: PyTorch trainer
    - model.py: Neural Network model
    - dataset.py: Dataset loader. This file also serves as an example to fetch data using dlinputs library.
2. Build the docker image   
`docker build -t name_of_your_docker_image .`
3. Tag docker image   
`docker tag name_of_your_docker_image gcr.io/$GCLOUD_PROJECT_NAME/name_of_your_docker_image:v1` 
4. Upload docker image to a cloud repository.     
`gcloud docker -- push gcr.io/$GCLOUD_PROJECT_NAME/name_of_your_docker_image:v1`
5. Configure location of your image in trainer-job.yml
6. Configure other parameters required for your training job in `trainer-deploy.yml` - type of GPUs, number of GPUs.

7. Deploy your job   
`kubectl create -f trainer-job.yml`
