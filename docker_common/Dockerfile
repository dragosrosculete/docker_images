FROM ubuntu:20.04
ARG TERRAFORM_VERSION=1.2.6
ARG KUBECTL_VERSION=v1.21.0
ARG ANSIBLE_VERSION=4.8.0

RUN echo 'debconf debconf/frontend select Noninteractive' | debconf-set-selections

COPY set-client-passwords /usr/local/bin/set-client-passwords
RUN chmod +x /usr/local/bin/set-client-passwords

RUN apt-get update && \
    apt-get install -yq --no-install-recommends \
curl \
wget \
unzip \
vim \
telnet \
tree \
build-essential \
software-properties-common \
git \
checkinstall \
libreadline-gplv2-dev \
libncursesw5-dev \
libssl-dev \
libsqlite3-dev \
tk-dev \
libgdbm-dev \
libc6-dev \
libbz2-dev \
apt-transport-https \
openssh-client \
python3-pip \
python3-setuptools \
python3-wheel \
node-ajv \
nodejs \
npm \
rsync \
jq \
sshpass

RUN npm install -g ajv-cli
RUN npm install ajv
RUN apt-get clean
RUN apt-get -yq upgrade

# Install Boto
RUN pip3 install wheel botocore boto boto3 ansible==${ANSIBLE_VERSION} awscli sceptre===2.5.0
#RUN ansible-galaxy collection install kubernetes.core
RUN pip3 install markupsafe==2.0.1 --force-reinstall
RUN pip3 install jinja2==3.1.2 --force-reinstall

# Install docker
RUN apt-get install -y docker.io

#Install helm
RUN curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash

#Install Openshift ( for k8s ansible module )
RUN pip3 install openshift==0.11 kubernetes-validate

#Install kubectl
RUN curl -LO https://storage.googleapis.com/kubernetes-release/release/${KUBECTL_VERSION}/bin/linux/amd64/kubectl
RUN chmod +x ./kubectl
RUN mv ./kubectl /usr/local/bin/kubectl

#Install mvn
RUN apt-get install -y maven

# Install Packer
RUN curl -sL https://releases.hashicorp.com/packer/1.4.5/packer_1.4.5_linux_amd64.zip > packer_1.4.5_linux_amd64.zip && \
    unzip -d /usr/local/packer packer_1.4.5_linux_amd64.zip && \
    rm -f packer_1.4.5_linux_amd64.zip && \
    ln -s /usr/local/packer/packer /usr/local/bin/packer

#AWS IAM AUTH
ARG AWS_IAM_AUTHENTICATOR_URL=https://amazon-eks.s3-us-west-2.amazonaws.com/1.14.6/2019-08-22/bin/linux/amd64/aws-iam-authenticator
ADD ${AWS_IAM_AUTHENTICATOR_URL} /usr/local/bin/aws-iam-authenticator
RUN chmod +x /usr/local/bin/aws-iam-authenticator

#Terraform
RUN wget https://releases.hashicorp.com/terraform/${TERRAFORM_VERSION}/terraform_${TERRAFORM_VERSION}_linux_amd64.zip && \
    unzip terraform_${TERRAFORM_VERSION}_linux_amd64.zip && \
    mv terraform /usr/local/bin/

#install GPG
RUN apt-get install gnupg -y

RUN apt-get autoremove

RUN mkdir -p /root/.aws
COPY config /root/.aws/config

# Set environment variables
ENV LC_ALL=C.UTF-8
ENV LANG=C.UTF-8
