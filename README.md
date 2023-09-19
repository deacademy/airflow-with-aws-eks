# airflow-with-aws-eks
This project we are going to deploy airflow dag code to aws cloud through CI/CD pipeline

# Need to run following command to AWS Cloud9 terminal IDE step by step process

Step 1: Update all existing built-in packages <br/>
`sudo yum -y update`

Step 2: Create python virtual environment called `airflow_with_eks` with python version 3.11 <br/>
`python3.11 -m venv airflow_with_eks`

Step 3: Active your python virtual environment
`source .airflow_with_eks/bin/activate`

## Now you need to execute each command at step by step process under your active python virtual environment

Step 1: Upgrade pip for installing python external package <br/>
`pip3 install --upgrade pip3`

Step 2: Download and extract the latest release of eksctl tool <br/>
`curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp`

Step 3: Move the extarcted eksctl package to linux installation folder <br/>
`sudo mv /tmp/eksctl /usr/local/bin`

Step 4: Test if eksctl installed sucessfully now. It should display version for you <br/>
`eksctl version`











