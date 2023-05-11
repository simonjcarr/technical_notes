## Prerequisites
[[Install K3S]]
[[Install Kubectl]]

## AWX Installation

AWX Installation notes taken from https://awstip.com/deploy-ansible-awx-into-a-k3s-single-node-cluster-794c023c514b

```bash
sudo apt update
sudo apt upgrade -y
sudo apt install curl git build-essential jq -y
export NAMESPACE=awx
kubectl create ns ${NAMESPACE}
kubectl config set-context --current --namespace=$NAMESPACE
git clone https://github.com/ansible/awx-operator.git
cd awx-operator
RELEASE_TAG=`curl -s https://api.github.com/repos/ansible/awx-operator/releases/latest | grep tag_name | cut -d '"' -f 4`  
echo $RELEASE_TAG  
git checkout $RELEASE_TAG
make deploy
```