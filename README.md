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
`scl enable rh-python36 bash`

`python3 -V`

`which python`


## Install azure-cli ##
`sudo rpm --import https://packages.microsoft.com/keys/microsoft.asc`

`sudo sh -c 'echo -e "[azure-cli]
name=Azure CLI
baseurl=https://packages.microsoft.com/yumrepos/azure-cli
enabled=1
gpgcheck=1
gpgkey=https://packages.microsoft.com/keys/microsoft.asc" > /etc/yum.repos.d/azure-cli.repo'`

`sudo yum install azure-cli`

`sudo reboot`


## Install OCP 4.2 on Azure ##

`az login`

`az account show`

    [
      {
        "cloudName": "AzureCloud",
        "id": "YYYYYYYY-YYYY-YYYY-YYYY-YYYYYYYYYYYY",
        "isDefault": true,
        "name": "Evaluación gratuita",
        "state": "Enabled",
        "tenantId": "XXXXXXXX-XXXX-XXXXXXXXX-XXXXXXXXXXXX",
        "user": {
          "name": "user@mail.com",
          "type": "user"
        }
      }
    ]


`az ad sp create-for-rbac --name cestay-azure-ocp-sp`


    {
      "appId": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA",
      "displayName": "cestay-azure-ocp-sp",
      "name": "http://cestay-azure-ocp-sp",
      "password": "BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB",
      "tenant": "XXXXXXXX-XXXX-XXXXXXXXX-XXXXXXXXXXXX"
    }

`az role assignment create --assignee AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA --role Contributor`

`az role assignment create --assignee AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA --role "User Access Administrator"`

`az ad app permission add --id AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA --api 00000002-0000-0000-c000-000000000000 --api-permissions 824c81eb-e3f8-4ee6-8f6d-de7f50d565b7=Role`

`az ad app permission grant --id AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA --api 00000002-0000-0000-c000-000000000000`

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


`az account list --output table`

    Name: Evaluación gratuita
    CloudName: AzureCloud
    SubscriptionId: YYYYYYYY-YYYY-YYYY-YYYY-YYYYYYYYYYYY
    State: Enable
    IsDefault: True


------------


**Crear directorio en el HOME**

`cd ~`

`mkdir ocp4`

**Descargar medios en ocp4**

**Extraer tar**

------------

`cd ~`

`ocp4/openshift-install create install-config --dir=ocp4/`

    ############################################################
    azure subscription id: YYYYYYYY-YYYY-YYYY-YYYY-YYYYYYYYYYYY
    tenantId: XXXXXXXX-XXXX-XXXXXXXXX-XXXXXXXXXXXX
    appId: AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA
    password: BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB
    ############################################################


`cat ~/ocp4/install-config.yaml`

`cat ~/.azure/osServicePrincipal.json`

`ocp4/openshift-install create cluster --dir=ocp4/`



