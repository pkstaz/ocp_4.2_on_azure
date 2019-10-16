# OCP 4.2 on Azure with RHEL 7.6
Instalación desde una maquina RHEL 7.6 hacia una cuenta AZURE. 
Importante modificar limites de la cuenta Azure para poder crear la maquinas.

## Install python3 local machine ##
`su -`
`subscription-manager repos --enable rhel-7-server-optional-rpms --enable rhel-server-rhscl-7-rpms`
`yum -y install @development`
`yum -y install rh-python36`
`yum -y install rh-python36-numpy rh-python36-scipy rh-python36-python-tools rh-python36-python-six`


#### Enable python3 on rhel
scl enable rh-python36 bash
python3 -V
which python


## Install azure-cli ##
sudo rpm --import https://packages.microsoft.com/keys/microsoft.asc

sudo sh -c 'echo -e "[azure-cli]
name=Azure CLI
baseurl=https://packages.microsoft.com/yumrepos/azure-cli
enabled=1
gpgcheck=1
gpgkey=https://packages.microsoft.com/keys/microsoft.asc" > /etc/yum.repos.d/azure-cli.repo'

sudo yum install azure-cli
sudo reboot


## Install OCP 4.2 on Azure ##

az login 

az account show

######
[
  {
    "cloudName": "AzureCloud",
    "id": "25acef01-162a-481e-a09a-b2562f685eff",
    "isDefault": true,
    "name": "Evaluaci?n gratuita",
    "state": "Enabled",
    "tenantId": "96bb3ea4-0743-4e9f-8421-18f7d351caa0",
    "user": {
      "name": "carlos@pksmaster.cl",
      "type": "user"
    }
  }
]
######


az ad sp create-for-rbac --name cestay-azure-ocp-sp

######
{
  "appId": "b68e7135-1215-464b-bbca-8b50898b376e",
  "displayName": "cestay-azure-ocp-sp",
  "name": "http://cestay-azure-ocp-sp",
  "password": "10b302f1-e123-42d3-bb45-a620fa117ea0",
  "tenant": "96bb3ea4-0743-4e9f-8421-18f7d351caa0"
}
######

az role assignment create --assignee \
b68e7135-1215-464b-bbca-8b50898b376e --role Contributor

az role assignment create --assignee \
b68e7135-1215-464b-bbca-8b50898b376e --role "User Access Administrator"

az ad app permission add --id b68e7135-1215-464b-bbca-8b50898b376e \
--api 00000002-0000-0000-c000-000000000000 \
--api-permissions 824c81eb-e3f8-4ee6-8f6d-de7f50d565b7=Role

az ad app permission grant --id \
b68e7135-1215-464b-bbca-8b50898b376e \
--api 00000002-0000-0000-c000-000000000000

######
{
  "clientId": "e761a5d4-32c6-4b48-bb39-a8fb79a2442a", 
  "consentType": "AllPrincipals",
  "expiryTime": "2020-10-08T18:33:56.341574",
  "objectId": "1KVh58YySEu7Oaj7eaJEKqsvD0SJmz1Gue_DyA1abM4",
  "odata.metadata": "https://graph.windows.net/96bb3ea4-0743-4e9f-8421-18f7d351caa0/$metadata#oauth2PermissionGrants/@Element",
  "odatatype": null,
  "principalId": null,
  "resourceId": "440f2fab-9b89-463d-b9ef-c3c80d5a6cce",
  "scope": "user_impersonation",
  "startTime": "2019-10-08T18:33:56.341574"
}
######


az account list --output table

######
Name: Evaluación gratuita
CloudName: AzureCloud
SubscriptionId: 25acef01-162a-481e-a09a-b2562f685eff  
State: Enable    
IsDefault: True
######


mkdir ~/ocp4

######
Descargar medios 
Extraer tar
######


ocp4/openshift-install create install-config --dir=ocp4/

azure subscription id: 25acef01-162a-481e-a09a-b2562f685eff
tenantId: 96bb3ea4-0743-4e9f-8421-18f7d351caa0

azure service principal client id
appId: b68e7135-1215-464b-bbca-8b50898b376e
password: 10b302f1-e123-42d3-bb45-a620fa117ea0


cat ~/ocp4/install-config.yaml
cat ~/.azure/osServicePrincipal.json

ocp4/openshift-install create cluster --dir=ocp4/

