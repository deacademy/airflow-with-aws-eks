# airflow-with-aws-eks
This project we are going to deploy airflow dag code to aws cloud through CI/CD pipeline

## Need to run following command to AWS Cloud9 terminal IDE step by step process

Step 1: Update all existing built-in packages<br/>
`sudo yum -y update`

Step 2: Create python virtual environment name with `airflow_with_aws_eks_venv` with python version 3.7<br/>
`python3 -m venv airflow_with_aws_eks_venv`

Step 3: Active your python virtual environment<br/>
`source ./airflow_with_aws_eks_venv/bin/activate`

## Now you need to execute each command at step by step process under your active python virtual environment

# Generate new ssh key for authenticating to Git code from terminal
Step 1: Run the following command and press enter for all questions to skip and keep default values.<br />
`ssh-keygen -t ed25519`

Step 2: Copy public key content by running the following command need to add in ssh key under your GitHub account.<br/>
`cat  /home/ec2-user/.ssh/id_ed25519.pub`

# Confguring SSH to EC2 instance for acessing it. 
Step 1: Add the ssh key to EC2 instances. You should able to see key name airflow-with-aws-eks-github-ssh in key pairs list in EC2 service page under Network & Security section.<br />
`aws ec2 import-key-pair --key-name "airflow-with-aws-eks-github-ssh" --public-key-material fileb:///home/ec2-user/.ssh/id_ed25519.pub`

# Add GitHUB pernsonal access token to GitHUB repository. Token should be different in your GitHUB account.
Step 1: Terminal command to add GITHUB_TOKEN environment variable in cloud9 environment<br/>
`export GITHUB_TOKEN=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`<br/>

# Clone this repository to cloud9 environment throough terminal
`git clone git@github.com:deacademy/airflow-with-aws-eks.git`

# Upgrading latest version of pip package (Stay upto data with latest python packages)
Step 1: Upgrade pip for installing python external package<br/>
`pip3 install --upgrade pip`

# eksctl (For creating kubernetes cluster through terminal)

Step 1: Download and extract the latest release of eksctl tool<br/>
`curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp`

Step 2: Move the extarcted eksctl package to linux installation folder <br/>
`sudo mv /tmp/eksctl /usr/local/bin`

Step 3: Test if eksctl installed sucessfully now. It should display latest version for you <br/>
`eksctl version`

# Kubectl(For interacting with kubernetes cluster through terminal)
Step 1: Download the latest version of Kubectl package <br/>
`curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.24.8/bin/linux/amd64/kubectl`

Step 2: provide executable permission to Kubectl package <br/>
`chmod +x ./kubectl`

Step 3: Move the extarcted kubectl package to linux installation folder <br/>
`sudo mv ./kubectl /usr/local/bin/kubectl`

Step 4: Test if kubectl installed sucessfully now. It should display latest version for you <br/>
`kubectl version --client`

# Helm3(Helps to install/mamange applications on Kubernetes cluster)
Step 1: Download and install Helm3 package <br />
`curl -sSL https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash`

Step 2: Test if Helm3 installed sucessfully now. It should display latest version for you <br/>
`helm version --short`

# Add Git stable repo(Repo where pre-configured your applications run on your kubernetes cluster)
Step 1: Download the git stable repo to interact latest changes with EkS cluster container <br/>
`helm repo add stable https://charts.helm.sh/stable`

# Confiure git username 
Step 1: Changing git config name only but keeping the email as it is. <br/>
`git config --global user.name "airflow-with-aws-eks"`

# Now setting up aws configure under python virtual environment  
Step 1: Upgrade aws cli package installation <br/>
`pip install --upgrade awscli && hash -r`

Step 2: Install some dependancy utilities packages <br/>
`sudo yum -y install jq gettext bash-completion moreutils`

Step 3: Remove temporary credentials <br/>
`rm -vf ${HOME}/.aws/credentials`

Step 4: Make sure your aws managed temporary credentials is turned off. Configure aws credentials, you copy/paste your aws access key and secret access key that stored earlier and provide region depends on your iam user created zone that can get right before profile icon at right hand top corner. My case region is 'us-east-1' and other option just click enter to skip. <br />
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

# Configuring AWS IAM authenticator 
Step 1: Installing aws-iam-authenticator otherwise kubectl connection will fial due to token. Run one by one commands.<br />
`curl -Lo aws-iam-authenticator https://github.com/kubernetes-sigs/aws-iam-authenticator/releases/download/v0.5.9/aws-iam-authenticator_0.5.9_linux_amd64` <br/>
`chmod +x ./aws-iam-authenticator` <br/>
`mkdir -p $HOME/bin && cp ./aws-iam-authenticator $HOME/bin/aws-iam-authenticator && export PATH=$PATH:$HOME/bin` <br/>
`echo 'export PATH=$PATH:$HOME/bin' >> ~/.bashrc` <br/>

Step 2: For validate the installation <br />
`aws-iam-authenticator help`

Step 3: Install AWS V2. Run commands one by one <br />
`curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"`<br/>
`unzip awscliv2.zip`<br/>
`sudo ./aws/install --update`

# Create AWS EKS Clsuter through yaml configuration file
Step 1: Go inside folder called airflow-with-aws-eks <br />
`cd airflow-with-aws-eks`

Step 2: Create aws eks cluster throuhg infrasturucture as a code yaml file <br/>
`eksctl create cluster -f cluster.yml`

Step 3: Check if the cluster is healthy<br/>
 You should see three while run the below command<br/>
`kubectl get nodes`<br/>
 Check the cluster health<br/>
`kubectl get pods --all-namespaces`

# Next run other terminal commands
Step 1: Install Flux library <br/>
`curl -s https://fluxcd.io/install.sh | sudo bash`<br/>

Step 2: Detect changes between Git repo and EKS code container<br/>
`flux bootstrap github \
  --owner=deacademy \
  --repository=airflow-with-aws-eks \
  --branch=main \
  --interval=15s \
  --personal`

# Structure bit structure your git project folder(Make sure you are in  airflow-with-aws-eks folder)
Step 1: Create three different folders called workloads,releases,namespaces in your root folder<br/>
`mkdir airflow-with-aws-eks/{workloads,releases,namespaces}`<br/>

Step 2: Create .find file all your folder <br/>
`find airflow-with-aws-eks/ -type d -exec touch {}/.keep \;`<br/>

Step 3: Transfer your code changes to git remote repository.<br/>
`git add .`<br/>
`git commit -am "add basic structure"`<br/>
`git push`<br/>

Command to get namespaces under eks cluster <br/>
`kubectl get namespaces`<br/>

You can see those pods those correspnding to system applications <br/>
`kubectl get pods -n flux-system`

Check logs for source-controller<br/>
`kubectl logs -f source-controller-* -n flux-system`<br/>

# Install AWS Elastic Block store(EBS) CSI Driver
Step 1: Add the aws-ebs-csi-driver Helm repository.<br />
`helm repo add aws-ebs-csi-driver https://kubernetes-sigs.github.io/aws-ebs-csi-driver`<br />

Step 2: Update the aws-ebs-csi-driver Helm repository.<br />
`helm repo update`<br />

Step 3:  Install the aws-ebs-csi-driver Helm repository.<br />
`helm upgrade --install aws-ebs-csi-driver \
    --namespace kube-system \
    aws-ebs-csi-driver/aws-ebs-csi-driver`<br />

Step 4: Once the driver has been deployed, verify the pods are running.<br />
`kubectl get pods -n kube-system -l app.kubernetes.io/name=aws-ebs-csi-driver`<br />

# Setting up Helm repository for accessing airflow Helm chart
Step 1:



