# aws-hashcat
Hashcat benchmarks on AWS instances. Files are in the format of `${INSTANCETYPE}.${HASHCATVERSION}`.

Benchmarks are run using:

* Hashcat compiled from source
* Deep Learning AMI (Amazon Linux 2) Version 26.0 **(community image)**
* In NVDIA-DOCKER
  * Docker version 18.09.9-ce, build 039a7df

## Instructions

Benchmarks in this repo are automatically created using the following user-data, you can alter this to your own use-case:

```bash
#!/bin/bash
aws ssm get-parameter --name 'gitkey' --with-decryption --region eu-west-1 | jq -r '.Parameter.Value' > ~/.ssh/id_rsa
chmod 0700 ~/.ssh/id_rsa
ssh -o StrictHostKeyChecking=no git@github.com
git clone git@github.com:javydekoning/aws-hashcat.git
cd aws-hashcat
# PULL IMAGES
nvidia-docker pull javydekoning/hashcat:cuda
nvidia-docker pull javydekoning/hashcat:opencl
# RUN CUDA
export HCVER=$(nvidia-docker run javydekoning/hashcat:cuda hashcat --version)
export FILE=$(curl http://169.254.169.254/latest/meta-data/instance-type).$HCVER.cuda.txt
nvidia-docker run javydekoning/hashcat:cuda hashcat -b > $FILE
git add $FILE
git commit -a -m "Adding $FILE"
git push
#RUN OpenCL
export HCVER=$(nvidia-docker run javydekoning/hashcat:opencl hashcat --version)
export FILE=$(curl http://169.254.169.254/latest/meta-data/instance-type).$HCVER.opencl.txt
nvidia-docker run javydekoning/hashcat:opencl hashcat -b > $FILE
git add $FILE
git commit -a -m "Adding $FILE"
git push
#TERMINATE
sudo shutdown 0
```

## Docker file

The docker files used can be found in this repository:

[![Docker Hub](http://dockeri.co/image/javydekoning/hashcat)](https://hub.docker.com/r/javydekoning/hashcat/)

Use tags `:opencl` for OpenCL, `:cuda` for CUDA.

[![](https://images.microbadger.com/badges/version/javydekoning/hashcat.svg)](https://microbadger.com/images/javydekoning/hashcat "Get your own version badge on microbadger.com")
[![](https://images.microbadger.com/badges/image/javydekoning/hashcat.svg)](https://microbadger.com/images/javydekoning/hashcat "Get your own image badge on microbadger.com")
