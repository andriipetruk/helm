Helm:

Get azure credentials
az login
az acs kubernetes get-credentials --resource-group=<rg> --name=<acsn> --ssh-key-file=<key_rsa> --debug

Upgrade acs Tiller
helm init --upgrade

helm install --values=values_dev.yaml . --name <name>
helm upgrade <name> --values=values_dev.yaml .

====================
Azure Containers Registry:

Create kubernetes secret for private registry
kubectl create secret docker-registry registry-auth --docker-server=<your-registry-server> --docker-username=<your-name> --docker-password=<your-pword> --docker-email=<your-email>

====================
External DNS:

Find master FQDN in Azure portal and ssh to master using created key
ssh acs_dev@edp-devmgmt.westus2.cloudapp.azure.com -i acs_dev
Get azure.json
sudo cat /etc/kubernetes/azure.json

Using this file, create kuberntes secret with azure auth credentials
kubectl create secret generic azure-auth --from-file=./azure.json

====================
nginx: 

Upload certs 
kubectl create secret generic ssl-cert --from-file=tls.crt --from-file=tls.key

====================
Applications: 
kubectl create secret generic akv-secret --from-literal=client_id='akv_client_id' --from-literal=client_secret='akv_client_secret' --from-literal=url='akv_url'

====================
Issues: 
1. Kubernetes does not support Azure Key Vault for secrets storage. PR is targeted for 1.9 release https://github.com/kubernetes/features/issues/370
2. Changing ConfigMap/Secrets won't trigger rolling update https://github.com/kubernetes/kubernetes/issues/22368
3. (CRITICAL) Sometimes ACS is not releasing PVC. Proposed solution: stop failing node's VM, detach all volumes except system, start node. https://github.com/Azure/ACS/issues/12