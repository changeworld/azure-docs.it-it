---
title: Da RDP ai nodi di Windows Server del servizio Web Del servizio Web AKS
titleSuffix: Azure Kubernetes Service
description: Informazioni su come creare una connessione RDP con i nodi di Windows Server del cluster servizio Azure Kubernetes (AKS) per la risoluzione dei problemi e le attività di manutenzione.
services: container-service
ms.topic: article
ms.date: 06/04/2019
ms.openlocfilehash: 62f29c0550b858e34d888da61f1bd7fbd358f82d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782928"
---
# <a name="connect-with-rdp-to-azure-kubernetes-service-aks-cluster-windows-server-nodes-for-maintenance-or-troubleshooting"></a>Connettersi con RDP ai nodi di Windows Server del cluster servizio Azure Kubernetes (AKS) per la manutenzione o la risoluzione dei problemi

Per tutto il ciclo di vita del cluster servizio Azure Kubernetes (AKS), potrebbe essere necessario accedere a un nodo di Windows Server del servizio Diaks. Questo accesso potrebbe servire per la manutenzione, la raccolta dei registri o altre operazioni di risoluzione dei problemi. È possibile accedere ai nodi di Windows Server del servizio Web Del servizio Web Diapasto tramite RDP. In alternativa, se si vuole usare SSH per accedere ai nodi di Windows Server del servizio Web AKS e si ha accesso alla stessa coppia di chiavi usata durante la creazione del cluster, è possibile seguire la procedura descritta in SSH nei nodi del [cluster di servizio Azure Kubernetes (AKS).][ssh-steps] Per motivi di sicurezza, i nodi di servizio Azure Kubernetes non sono esposti in Internet.

Questo articolo illustra come creare una connessione RDP con un nodo del servizio Web Diapasto usando i relativi indirizzi IP privati.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che l'utente abbia un cluster del servizio Web Disassociato di Microsoft Con un nodo di Windows Server. Se è necessario un cluster del servizio Azure Container, vedere l'articolo sulla creazione di un cluster del servizio Azure Container con un contenitore Windows tramite l'interfaccia [della riga di comando di Azure.][aks-windows-cli] Sono necessari il nome utente e la password dell'amministratore di Windows per il nodo di Windows Server di cui si vuole risolvere i problemi. Se non li si conosce, è possibile reimpostarli seguendo le istruzioni riportate in Reset Servizi Desktop remoto or its administrator password in a Windows VM (Reimposta Servizi Desktop remoto password [amministratore in una macchina virtuale Windows). ](/troubleshoot/azure/virtual-machines/reset-rdp) È necessario anche un client RDP, ad esempio [Desktop remoto Microsoft][rdp-mac].

È anche necessario che sia installata e configurata l'interfaccia della riga di comando di Azure versione 2.0.61 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

## <a name="deploy-a-virtual-machine-to-the-same-subnet-as-your-cluster"></a>Distribuire una macchina virtuale nella stessa subnet del cluster

I nodi di Windows Server del cluster del servizio Web Diaks non hanno indirizzi IP accessibili esternamente. Per stabilire una connessione RDP, è possibile distribuire una macchina virtuale con un indirizzo IP accessibile pubblicamente nella stessa subnet dei nodi di Windows Server.

L'esempio seguente crea una macchina virtuale denominata *myVM* nel *gruppo di risorse myResourceGroup.*

Ottenere prima di tutto la subnet usata dal pool di nodi di Windows Server. Per ottenere l'ID subnet, è necessario il nome della subnet. Per ottenere il nome della subnet, è necessario il nome della rete virtuale. Per ottenere il nome della rete virtuale, eseguire una query sul cluster per trovare l'elenco delle reti. Per eseguire query sul cluster, è necessario il nome. È possibile ottenere tutti questi elementi eseguendo il comando seguente nel Azure Cloud Shell:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
VNET_NAME=$(az network vnet list -g $CLUSTER_RG --query [0].name -o tsv)
SUBNET_NAME=$(az network vnet subnet list -g $CLUSTER_RG --vnet-name $VNET_NAME --query [0].name -o tsv)
SUBNET_ID=$(az network vnet subnet show -g $CLUSTER_RG --vnet-name $VNET_NAME --name $SUBNET_NAME --query id -o tsv)
```

Dopo aver creato il SUBNET_ID, eseguire il comando seguente nella stessa finestra Azure Cloud Shell per creare la macchina virtuale:

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2019datacenter \
    --admin-username azureuser \
    --admin-password myP@ssw0rd12 \
    --subnet $SUBNET_ID \
    --query publicIpAddress -o tsv
```

L'output di esempio seguente mostra che la macchina virtuale è stata creata correttamente e visualizza l'indirizzo IP pubblico della macchina virtuale.

```console
13.62.204.18
```

Registrare l'indirizzo IP pubblico della macchina virtuale. Questo indirizzo verrà utilizzato in un passaggio successivo.

## <a name="allow-access-to-the-virtual-machine"></a>Consentire l'accesso alla macchina virtuale

Le subnet del pool di nodi del servizio Gateway Gateway sono protette con gruppi di sicurezza di rete (gruppi di sicurezza di rete) per impostazione predefinita. Per ottenere l'accesso alla macchina virtuale, è necessario abilitato l'accesso nel gruppo di protezione di rete.

> [!NOTE]
> I NSG sono controllati dal servizio Servizio Web Disatteso. Qualsiasi modifica apportata al gruppo di controllo di rete verrà sovrascritta in qualsiasi momento dal piano di controllo.
>

Per prima cosa, ottenere il gruppo di risorse e il nome nsg del gruppo di sicurezza di rete a cui aggiungere la regola:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NSG_NAME=$(az network nsg list -g $CLUSTER_RG --query [].name -o tsv)
```

Creare quindi la regola NSG:

```azurecli-interactive
az network nsg rule create --name tempRDPAccess --resource-group $CLUSTER_RG --nsg-name $NSG_NAME --priority 100 --destination-port-range 3389 --protocol Tcp --description "Temporary RDP access to Windows nodes"
```

## <a name="get-the-node-address"></a>Ottenere l'indirizzo del nodo

Per gestire un cluster Kubernetes, usare [kubectl][kubectl], il client da riga di comando di Kubernetes. Se si usa Azure Cloud Shell, `kubectl` è già installato. Per installare `kubectl` in locale, usare il comando [az aks install-cli][az-aks-install-cli]:
    
```azurecli-interactive
az aks install-cli
```

Per configurare `kubectl` per la connessione al cluster Kubernetes, usare il comando [az aks get-credentials][az-aks-get-credentials]. Questo comando scarica le credenziali e configura l'interfaccia della riga di comando di Kubernetes per usarli.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Elencare l'indirizzo IP interno dei nodi di Windows Server usando il [comando kubectl get:][kubectl-get]

```console
kubectl get nodes -o wide
```

L'output di esempio seguente mostra gli indirizzi IP interni di tutti i nodi del cluster, inclusi i nodi di Windows Server.

```console
$ kubectl get nodes -o wide
NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Registrare l'indirizzo IP interno del nodo di Windows Server di cui si vuole risolvere i problemi. Questo indirizzo verrà utilizzato in un passaggio successivo.

## <a name="connect-to-the-virtual-machine-and-node"></a>Connettersi alla macchina virtuale e al nodo

Connettersi all'indirizzo IP pubblico della macchina virtuale creata in precedenza usando un client [RDP, ad][rdp-mac]esempio Desktop remoto Microsoft .

![Immagine della connessione alla macchina virtuale tramite un client RDP](media/rdp/vm-rdp.png)

Dopo aver connesso la macchina virtuale, connettersi all'indirizzo *IP* interno del nodo di Windows Server di cui si vuole risolvere i problemi usando un client RDP dall'interno della macchina virtuale.

![Immagine della connessione al nodo Windows Server tramite un client RDP](media/rdp/node-rdp.png)

A questo punto si è connessi al nodo Windows Server.

![Immagine della finestra di comando nel nodo Windows Server](media/rdp/node-session.png)

È ora possibile eseguire qualsiasi comando di risoluzione dei problemi nella *finestra di* comando. Poiché i nodi di Windows Server usano Windows Server Core, non è disponibile un'interfaccia utente grafica completa o altri strumenti gui quando ci si connette a un nodo di Windows Server tramite RDP.

## <a name="remove-rdp-access"></a>Rimuovere l'accesso RDP

Al termine, chiudere la connessione RDP al nodo Windows Server e quindi chiudere la sessione RDP alla macchina virtuale. Dopo aver chiuso entrambe le sessioni RDP, eliminare la macchina virtuale con [il comando az vm delete:][az-vm-delete]

```azurecli-interactive
az vm delete --resource-group myResourceGroup --name myVM
```

E la regola NSG:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NSG_NAME=$(az network nsg list -g $CLUSTER_RG --query [].name -o tsv)
```

```azurecli-interactive
az network nsg rule delete --resource-group $CLUSTER_RG --nsg-name $NSG_NAME --name tempRDPAccess
```

## <a name="next-steps"></a>Passaggi successivi

Se sono necessari dati aggiuntivi per la risoluzione dei problemi, è possibile visualizzare i log del nodo [master Kubernetes][view-master-logs] [o Monitoraggio di Azure][azure-monitor-containers].

<!-- EXTERNAL LINKS -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[rdp-mac]: https://aka.ms/rdmac

<!-- INTERNAL LINKS -->
[aks-windows-cli]: windows-container-cli.md
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-vm-delete]: /cli/azure/vm#az_vm_delete
[azure-monitor-containers]: ../azure-monitor/containers/container-insights-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ssh-steps]: ssh.md
[view-master-logs]: view-master-logs.md