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
# Installing following packages- <br/>
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
`git config --global user.name "airflow-ws-with-eks"`







