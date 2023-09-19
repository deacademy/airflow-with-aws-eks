# airflow-with-aws-eks
This project we are going to deploy airflow dag code to aws cloud through CI/CD pipeline

## Need to run following command to AWS Cloud9 terminal IDE step by step process

Step 1: Update all existing built-in packages <br/>
`sudo yum -y update`

Step 2: Create python virtual environment called `airflow_with_eks` with python version 3.11 <br/>
`python3.11 -m venv airflow_with_eks`

Step 3: Active your python virtual environment
`source .airflow_with_eks/bin/activate`

## Now you need to execute each command at step by step process under your active python virtual environment

# Installing following packages 
1. Upgrading latest version of pip package
2. eksctl
3. Kubectl
4. Helm3
5. git stable repo
6. git username configuration
   
Step 1: Upgrade pip for installing python external package <br/>
`pip3 install --upgrade pip3`

Step 2: Download and extract the latest release of eksctl tool <br/>
`curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp`

Step 3: Move the extarcted eksctl package to linux installation folder <br/>
`sudo mv /tmp/eksctl /usr/local/bin`

Step 4: Test if eksctl installed sucessfully now. It should display latest version for you <br/>
`eksctl version`

Step5: Download the latest version of Kubectl package <br/>
`curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/arm64/kubectl"`

Step 6: provide executable permission to Kubectl package <br/>
`chmod +x ./kubectl`

Step7: Move the extarcted kubectl package to linux installation folder <br/>
`sudo mv ./kubectl /usr/local/bin/kubectl`

Step 8: Test if kubectl installed sucessfully now. It should display latest version for you <br/>
`kubectl version --client`

Step 9: Download and install Helm3 package
`curl -sSL https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash`

Step 10: Test if Helm3 installed sucessfully now. It should display latest version for you <br/>
`helm version --short`

Step 11: Download the git stable repo to interact latest changes with EkS cluster container <br/>
`helm repo add stable https://charts.helm.sh/stable`

Step 12: Changing git config name only but keeping the email as it is. <br/>
`git config --global user.name "airflow-with-eks"`

# Now setting up aws configure under python virtual environment  

Step 1: Upgrade aws cli package installation <br/>
`upgrade aws cli`

Step 2: Install some dependancy utilities packages <br/>
`sudo yum -y install jq gettext bash-completion moreutils`

Step 3: Remove temporary credentials <br/>
`rm -vf ${HOME}/.aws/credentials`

Step 4:  Configure aws credentials, you copy/paste your aws access key and secret access key that stored earlier and provide region depends on your iam user created zone that can get right before profile icon at right hand top corner. My case region is 'us-east-1' and other option just click enter to skip. <br />
`aws configure`

Step 5: Run the following command to display the account id for  your iam user. <br />
`aws sts get-caller-identity`

Step 6: Run following command to set environment variable. <br />
`export ACCOUNT_ID=<Take value from previous command>`<br/>
`export AWS_REGION=<Your IAM user Availability>`

Step 7: Run the following commands one by one to update the file bash_profile and configure aws <br />
`echo "export ACCOUNT_ID=${ACCOUNT_ID}" | tee -a ~/.bash_profile` <br/>
`echo "export AWS_REGION=${AWS_REGION}" | tee -a ~/.bash_profile` <br/>
`aws configure set default.region ${AWS_REGION}` <br/>
`aws configure get default.region`

# Generate new ssh key for authenticating to Git code from terminal
Step 1: Run the following command and press enter for all questions to skip and keep default values. <br />
`ssh-keygen -t ed25519`

# Configuring ssh key and AWS IAM authenticator
Step 1: Go inside folder called airflow-with-aws-eks <br />
`cd airflow-with-aws-eks`

Step 2: Install the ssh key <br />
`aws ec2 import-key-pair --key-name "airflow-with-eks" --public-key-material fileb:///home/ec2-user/.ssh/id_rsa.pub`

Step 3: Installing aws-iam-authenticator otherwise kubectl connection will fial due to token. Run one by one commands.<br />
`curl -Lo aws-iam-authenticator https://github.com/kubernetes-sigs/aws-iam-authenticator/releases/download/v0.5.9/aws-iam-authenticator_0.5.9_linux_amd64` <br/>
`chmod +x ./aws-iam-authenticator` <br/>
`mkdir -p $HOME/bin && cp ./aws-iam-authenticator $HOME/bin/aws-iam-authenticator && export PATH=$PATH:$HOME/bin` <br/>
`echo 'export PATH=$PATH:$HOME/bin' >> ~/.bashrc` <br/>

Step 4: For validate the installation <br />
`aws-iam-authenticator help`

Step 5: Install AWS V2. Run commands one by one <br />
`curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"`<br/>
`unzip awscliv2.zip` <br/>
`sudo ./aws/install --update`

Step 6: Create aws eks cluster throuhg infrasturucture as a code yaml file <br/>
`eksctl create cluster -f cluster.yml`

Step 7: Check if the cluster is healthy <br/>
`kubectl get nodes`<br/>
`kubectl get pods --all-namespaces`


# Next run other terminal commands
curl -s https://fluxcd.io/install.sh | sudo bash

flux bootstrap github \
  --owner=marclamberti \
  --repository=airflow-eks-config \
  --branch=main \
  --interval=15s \
  --personal

mkdir airflow-eks-config/{workloads,releases,namespaces}
find airflow-eks-config/ -type d -exec touch {}/.keep \;
cd airflow-eks-config
git add .




