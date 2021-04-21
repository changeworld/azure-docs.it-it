---
title: Integrare Azure NetApp Files con servizio Azure Kubernetes
description: Informazioni su come integrare Azure NetApp Files con servizio Azure Kubernetes
services: container-service
ms.topic: article
ms.date: 10/23/2020
ms.openlocfilehash: 28c5b77f06bc48bf06575e45194adfaed068b30f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776052"
---
# <a name="integrate-azure-netapp-files-with-azure-kubernetes-service"></a>Integrare Azure NetApp Files con servizio Azure Kubernetes

[Azure NetApp Files][anf] è un servizio di archiviazione file a consumo di livello aziendale, ad alte prestazioni, in esecuzione in Azure. Questo articolo illustra come integrare i servizi Azure NetApp Files con servizio Azure Kubernetes (AKS).

## <a name="before-you-begin"></a>Prima di iniziare
Questo articolo presuppone che si disponga di un cluster del servizio Azure Kubernetes esistente. Se è necessario un cluster del servizio Azure Kubernetes, vedere la guida di avvio rapido sul servizio Azure Kubernetes [Uso dell'interfaccia della riga di comando di Azure][aks-quickstart-cli] oppure [Uso del portale di Azure][aks-quickstart-portal].

> [!IMPORTANT]
> Il cluster del servizio Web Diaks deve anche [essere in un'area che supporta Azure NetApp Files][anf-regions].

È anche necessario che sia installata e configurata l'interfaccia della riga di comando di Azure 2.0.59 o versione successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

### <a name="limitations"></a>Limitazioni

Quando si usa Azure NetApp Files, si applicano le limitazioni Azure NetApp Files:

* Azure NetApp Files è disponibile solo [nelle aree di Azure selezionate.][anf-regions]
* Prima di poter usare Azure NetApp Files, è necessario disporre dell'accesso al Azure NetApp Files servizio. Per richiedere l'accesso, è possibile usare il modulo Azure NetApp Files di invio della lista [di attesa o][anf-waitlist] passare a https://azure.microsoft.com/services/netapp/#getting-started . Non è possibile accedere al servizio Azure NetApp Files finché non si riceve il messaggio di posta elettronica di conferma ufficiale dal team Azure NetApp Files.
* Dopo la distribuzione iniziale di un cluster del servizio AKS, è supportato solo il provisioning statico Azure NetApp Files servizio.
* Per usare il provisioning dinamico con Azure NetApp Files, installare e configurare [NetApp Trident](https://netapp-trident.readthedocs.io/) versione 19.07 o successiva.

## <a name="configure-azure-netapp-files"></a>Configurare Azure NetApp Files

> [!IMPORTANT]
> Prima di registrare il provider  *di risorse Microsoft.NetApp,* è necessario completare il modulo Azure NetApp Files di invio della lista di [attesa][anf-waitlist] o passare a per https://azure.microsoft.com/services/netapp/#getting-started la sottoscrizione. Non è possibile registrare la risorsa specificata fino a quando non si riceve il messaggio di posta elettronica di conferma ufficiale dal team Azure NetApp Files risorse.

Registrare il provider *di risorse Microsoft.NetApp:*

```azurecli
az provider register --namespace Microsoft.NetApp --wait
```

> [!NOTE]
> Questa operazione può richiedere del tempo.

Quando si crea un account Azure NetApp per l'uso con il servizio AzureKs, è necessario creare l'account nel gruppo **di risorse** del nodo. Per prima cosa, ottenere il nome del gruppo di risorse con il comando [az servizio Azure Kubernetes show][az-aks-show] e aggiungere il parametro di query `--query nodeResourceGroup`. L'esempio seguente ottiene il gruppo di risorse del nodo per il cluster del servizio Web AKS *denominato myAKSCluster* nel nome del gruppo di risorse *myResourceGroup*:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

```output
MC_myResourceGroup_myAKSCluster_eastus
```

Creare un account Azure NetApp Files nel  gruppo di risorse del nodo e nella stessa area del cluster del servizio Web Diaz [usando az netappfiles account create][az-netappfiles-account-create]. L'esempio seguente crea un account  denominato *myaccount1* nel gruppo MC_myResourceGroup_myAKSCluster_eastus e *nell'area eastus:*

```azurecli
az netappfiles account create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1
```

Creare un nuovo pool di capacità usando [az netappfiles pool create][az-netappfiles-pool-create]. L'esempio seguente crea un nuovo pool di capacità denominato *mypool1* con dimensioni di 4 TB e *livello di* servizio Premium:

```azurecli
az netappfiles pool create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1 \
    --pool-name mypool1 \
    --size 4 \
    --service-level Premium
```

Creare una subnet da [delegare a Azure NetApp Files][anf-delegate-subnet] usando [az network vnet subnet create][az-network-vnet-subnet-create]. *Questa subnet deve essere nella stessa rete virtuale del cluster del servizio Web Disatteso.*

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
az network vnet subnet create \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $VNET_NAME \
    --name $SUBNET_NAME \
    --delegations "Microsoft.NetApp/volumes" \
    --address-prefixes 10.0.0.0/28
```

Creare un volume usando [az netappfiles volume create][az-netappfiles-volume-create].

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
LOCATION=eastus
ANF_ACCOUNT_NAME=myaccount1
POOL_NAME=mypool1
SERVICE_LEVEL=Premium
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $SUBNET_NAME --query "id" -o tsv)
VOLUME_SIZE_GiB=100 # 100 GiB
UNIQUE_FILE_PATH="myfilepath2" # Please note that file path needs to be unique within all ANF Accounts

az netappfiles volume create \
    --resource-group $RESOURCE_GROUP \
    --location $LOCATION \
    --account-name $ANF_ACCOUNT_NAME \
    --pool-name $POOL_NAME \
    --name "myvol1" \
    --service-level $SERVICE_LEVEL \
    --vnet $VNET_ID \
    --subnet $SUBNET_ID \
    --usage-threshold $VOLUME_SIZE_GiB \
    --file-path $UNIQUE_FILE_PATH \
    --protocol-types "NFSv3"
```

## <a name="create-the-persistentvolume"></a>Creare persistentVolume

Elencare i dettagli del volume usando [az netappfiles volume show][az-netappfiles-volume-show]

```azurecli
az netappfiles volume show --resource-group $RESOURCE_GROUP --account-name $ANF_ACCOUNT_NAME --pool-name $POOL_NAME --volume-name "myvol1"
```

```output
{
  ...
  "creationToken": "myfilepath2",
  ...
  "mountTargets": [
    {
      ...
      "ipAddress": "10.0.0.4",
      ...
    }
  ],
  ...
}
```

Creare una `pv-nfs.yaml` definizione di PersistentVolume. Sostituire `path` con *creationToken* e con `server` *ipAddress* del comando precedente. Ad esempio:

```yaml
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-nfs
spec:
  capacity:
    storage: 100Gi
  accessModes:
    - ReadWriteMany
  mountOptions:
    - vers=3
  nfs:
    server: 10.0.0.4
    path: /myfilepath2
```

Aggiornare il *server* *e il* percorso dei valori del volume NFS (Network File System) creato nel passaggio precedente. Creare PersistentVolume con il [comando kubectl apply:][kubectl-apply]

```console
kubectl apply -f pv-nfs.yaml
```

Verificare che *lo stato* di PersistentVolume sia *Disponibile* usando il [comando kubectl describe:][kubectl-describe]

```console
kubectl describe pv pv-nfs
```

## <a name="create-the-persistentvolumeclaim"></a>Creare persistentVolumeClaim

Creare una `pvc-nfs.yaml` definizione di PersistentVolume. Ad esempio:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-nfs
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 1Gi
```

Creare PersistentVolumeClaim con il [comando kubectl apply:][kubectl-apply]

```console
kubectl apply -f pvc-nfs.yaml
```

Verificare che *lo stato* di PersistentVolumeClaim sia *Associato* usando il [comando kubectl describe:][kubectl-describe]

```console
kubectl describe pvc pvc-nfs
```

## <a name="mount-with-a-pod"></a>Montare con un pod

Creare un `nginx-nfs.yaml` oggetto che definisce un pod che usa PersistentVolumeClaim. Ad esempio:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-nfs
spec:
  containers:
  - image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    name: nginx-nfs
    command:
    - "/bin/sh"
    - "-c"
    - while true; do echo $(date) >> /mnt/azure/outfile; sleep 1; done
    volumeMounts:
    - name: disk01
      mountPath: /mnt/azure
  volumes:
  - name: disk01
    persistentVolumeClaim:
      claimName: pvc-nfs
```

Creare il pod con il [comando kubectl apply:][kubectl-apply]

```console
kubectl apply -f nginx-nfs.yaml
```

Verificare che il pod sia *In* esecuzione usando [il comando kubectl describe:][kubectl-describe]

```console
kubectl describe pod nginx-nfs
```

Verificare che il volume sia stato montato nel pod usando [kubectl exec][kubectl-exec] per connettersi al pod e quindi verificare `df -h` se il volume è montato.

```console
$ kubectl exec -it nginx-nfs -- sh
```

```output
/ # df -h
Filesystem             Size  Used Avail Use% Mounted on
...
10.0.0.4:/myfilepath2  100T  384K  100T   1% /mnt/azure
...
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui Azure NetApp Files, vedere [Informazioni Azure NetApp Files][anf]. Per altre informazioni sull'uso di NFS con il servizio AKS, vedere Creare e usare manualmente un volume del [server Linux NFS (Network File System) con servizio Azure Kubernetes (AKS).][aks-nfs]


[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-nfs]: azure-nfs-volume.md
[anf]: ../azure-netapp-files/azure-netapp-files-introduction.md
[anf-delegate-subnet]: ../azure-netapp-files/azure-netapp-files-delegate-subnet.md
[anf-quickstart]: ../azure-netapp-files/
[anf-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all
[anf-waitlist]: https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-netappfiles-account-create]: /cli/azure/netappfiles/account#az_netappfiles_account_create
[az-netappfiles-pool-create]: /cli/azure/netappfiles/pool#az_netappfiles_pool_create
[az-netappfiles-volume-create]: /cli/azure/netappfiles/volume#az_netappfiles_volume_create
[az-netappfiles-volume-show]: /cli/azure/netappfiles/volume#az_netappfiles_volume_show
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_create
[install-azure-cli]: /cli/azure/install-azure-cli
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
