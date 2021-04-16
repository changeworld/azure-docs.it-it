---
title: Creare un backup Azure Red Hat OpenShift 4 dell'applicazione cluster con Velero
description: Informazioni su come creare un backup delle applicazioni Azure Red Hat OpenShift cluster con Velero
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 06/22/2020
author: troy0820
ms.author: b-trconn
keywords: aro, openshift, az aro, red hat, cli
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: c8bf722bd77372cd89e7c64757347b5fd07eb1ed
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481362"
---
# <a name="create-an-azure-red-hat-openshift-4-cluster-application-backup"></a>Creare un backup Azure Red Hat OpenShift 4 cluster dell'applicazione

In questo articolo si preparerà l'ambiente per creare un backup dell'applicazione Azure Red Hat OpenShift 4 cluster. Si apprenderà come:

> [!div class="checklist"]
> * Configurare i prerequisiti e installare gli strumenti necessari
> * Creare un backup Azure Red Hat OpenShift 4 applicazioni

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure 2.6.0 o versione successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Prima di iniziare

### <a name="install-velero"></a>Installare Velero

Per [installare](https://velero.io/docs/main/basic-install/) Velero nel sistema, seguire la procedura consigliata per il sistema operativo.

### <a name="set-up-azure-storage-account-and-blob-container"></a>Configurare l'account di archiviazione di Azure e il contenitore BLOB

Questo passaggio creerà un gruppo di risorse all'esterno del gruppo di risorse del cluster ARO.  Questo gruppo di risorse consentirà di rendere persistenti i backup e di ripristinare le applicazioni in nuovi cluster.

```bash
AZURE_BACKUP_RESOURCE_GROUP=Velero_Backups
az group create -n $AZURE_BACKUP_RESOURCE_GROUP --location eastus

AZURE_STORAGE_ACCOUNT_ID="velero$(uuidgen | cut -d '-' -f5 | tr '[A-Z]' '[a-z]')"
az storage account create \
    --name $AZURE_STORAGE_ACCOUNT_ID \
    --resource-group $AZURE_BACKUP_RESOURCE_GROUP \
    --sku Standard_GRS \
    --encryption-services blob \
    --https-only true \
    --kind BlobStorage \
    --access-tier Hot

BLOB_CONTAINER=velero
az storage container create -n $BLOB_CONTAINER --public-access off --account-name $AZURE_STORAGE_ACCOUNT_ID
```

## <a name="set-permissions-for-velero"></a>Impostare le autorizzazioni per Velero

### <a name="create-service-principal"></a>Creare un'entità servizio

Velero necessita delle autorizzazioni per eseguire backup e ripristini. Quando si crea un'entità servizio, si dà a Velero l'autorizzazione per accedere al gruppo di risorse definito nel passaggio precedente. Questo passaggio otterrà il gruppo di risorse del cluster:

```bash
export AZURE_RESOURCE_GROUP=$(az aro show --name <name of cluster> --resource-group <name of resource group> | jq -r .clusterProfile.resourceGroupId | cut -d '/' -f 5,5)
```


```bash
AZURE_SUBSCRIPTION_ID=$(az account list --query '[?isDefault].id' -o tsv)

AZURE_TENANT_ID=$(az account list --query '[?isDefault].tenantId' -o tsv)
```

```bash
AZURE_CLIENT_SECRET=$(az ad sp create-for-rbac --name "velero" --role "Contributor" --query 'password' -o tsv \
--scopes  /subscriptions/$AZURE_SUBSCRIPTION_ID)
AZURE_CLIENT_ID=$(az ad sp list --display-name "velero" --query '[0].appId' -o tsv)

```

```bash
cat << EOF  > ./credentials-velero.yaml
AZURE_SUBSCRIPTION_ID=${AZURE_SUBSCRIPTION_ID}
AZURE_TENANT_ID=${AZURE_TENANT_ID}
AZURE_CLIENT_ID=${AZURE_CLIENT_ID}
AZURE_CLIENT_SECRET=${AZURE_CLIENT_SECRET}
AZURE_RESOURCE_GROUP=${AZURE_RESOURCE_GROUP}
AZURE_CLOUD_NAME=AzurePublicCloud
EOF
```

## <a name="install-velero-on-azure-red-hat-openshift-4-cluster"></a>Installare Velero nel cluster Azure Red Hat OpenShift 4

Questo passaggio installerà Velero nel proprio progetto e nelle definizioni di risorse [personalizzate](https://kubernetes.io/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/) necessarie per eseguire backup e ripristini con Velero. Assicurarsi di aver eseguito correttamente l'accesso a un cluster Azure Red Hat OpenShift v4.


```bash
velero install \
--provider azure \
--plugins velero/velero-plugin-for-microsoft-azure:v1.1.0 \
--bucket $BLOB_CONTAINER \
--secret-file ~/path/to/credentials-velero.yaml \
--backup-location-config resourceGroup=$AZURE_BACKUP_RESOURCE_GROUP,storageAccount=$AZURE_STORAGE_ACCOUNT_ID \
--snapshot-location-config apiTimeout=15m \
--velero-pod-cpu-limit="0" --velero-pod-mem-limit="0" \
--velero-pod-mem-request="0" --velero-pod-cpu-request="0"
```

## <a name="create-a-backup-with-velero"></a>Creare un backup con Velero

Per creare un backup dell'applicazione con Velero, è necessario includere lo spazio dei nomi in cui si trova l'applicazione.  Se si dispone di uno spazio dei nomi e si vogliono includere tutte le risorse in tale spazio dei nomi nel `nginx-example` backup, eseguire il comando seguente nel terminale:

```bash
velero create backup <name of backup> --include-namespaces=nginx-example
```
È possibile controllare lo stato del backup eseguendo:

```bash
oc get backups -n velero <name of backup> -o yaml
```

Un backup riuscito verrà `phase:Completed` restituito e gli oggetti saranno presenti nel contenitore nell'account di archiviazione.

## <a name="create-a-backup-with-velero-to-include-snapshots"></a>Creare un backup con Velero per includere gli snapshot

Per creare un backup dell'applicazione con Velero in modo da includere i volumi permanenti dell'applicazione, è necessario includere lo spazio dei nomi in cui si trova l'applicazione e il flag durante la creazione del `snapshot-volumes=true` backup

```bash
velero backup create <name of backup> --include-namespaces=nginx-example --snapshot-volumes=true --include-cluster-resources=true
```

È possibile controllare lo stato del backup eseguendo:

```bash
oc get backups -n velero <name of backup> -o yaml
```

Un backup riuscito con output `phase:Completed` e gli oggetti saranno presenti nel contenitore nell'account di archiviazione.

Per altre informazioni su come creare backup e ripristini usando Velero, vedere [Eseguire il backup delle risorse OpenShift in modo nativo](https://www.openshift.com/blog/backup-openshift-resources-the-native-way)

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato eseguito il backup Azure Red Hat OpenShift'applicazione cluster Azure Red Hat OpenShift 4. Si è appreso come:

> [!div class="checklist"]
> * Creare un backup dell'applicazione cluster OpenShift v4 con Velero
> * Creare un backup dell'applicazione cluster OpenShift v4 con snapshot usando Velero


Passare all'articolo successivo per informazioni su come creare un'applicazione Azure Red Hat OpenShift 4 cluster.

* [Creare un ripristino Azure Red Hat OpenShift 4 cluster dell'applicazione](howto-create-a-restore.md)
* [Creare un ripristino Azure Red Hat OpenShift 4 cluster, inclusi gli snapshot](howto-create-a-restore.md)
