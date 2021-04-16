---
title: 'Esercitazione: Distribuire un soluzione Azure VMware cloud privato'
description: Informazioni su come creare e distribuire un soluzione Azure VMware cloud privato
ms.topic: tutorial
ms.date: 02/22/2021
ms.openlocfilehash: 89a44ce7e5910609068f72c321971ced2e3646b4
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374846"
---
# <a name="tutorial-deploy-an-azure-vmware-solution-private-cloud"></a>Esercitazione: Distribuire un soluzione Azure VMware cloud privato

La soluzione Azure VMware consente di distribuire un cluster vSphere in Azure. La distribuzione iniziale minima è di tre host. È possibile aggiungere altri host uno alla volta, fino a un massimo di 16 host per cluster.

Poiché la soluzione Azure VMware non consente di gestire il cloud privato con l'istanza locale di vCenter all'avvio, è necessario definire una configurazione aggiuntiva. Queste procedure e i prerequisiti correlati verranno illustrati in questa esercitazione.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un cloud privato della soluzione Azure VMware
> * Verificare la distribuzione del cloud privato

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Diritti amministrativi appropriati e l'autorizzazione per creare un cloud privato. È necessario avere almeno il livello di collaboratore nella sottoscrizione.
- Seguire le informazioni raccolte nell'articolo [sulla pianificazione per](production-ready-deployment-steps.md) distribuire soluzione Azure VMware.
- Assicurarsi di avere la rete appropriata configurata come descritto in [Esercitazione: Elenco di controllo per la rete](tutorial-network-checklist.md).
- È stato effettuato il provisioning degli host e il provider di risorse Microsoft.AVS è stato registrato come descritto in Richiedere host e abilitare il provider di risorse [Microsoft.AVS.](enable-azure-vmware-solution.md)

## <a name="create-a-private-cloud"></a>Creare un cloud privato

Per creare un cloud privato della soluzione Azure VMware, è possibile usare il [portale di Azure](#azure-portal) oppure l'[interfaccia della riga di comando di Azure](#azure-cli).

### <a name="azure-portal"></a>Portale di Azure

[!INCLUDE [create-avs-private-cloud-azure-portal](includes/create-private-cloud-azure-portal-steps.md)]

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Anziché usare il portale di Azure creare un cloud privato della soluzione Azure VMware, è possibile usare l'interfaccia della riga di comando di Azure e Azure Cloud Shell.  Per un elenco di comandi che è possibile usare con la soluzione Azure VMware, vedere [Comandi di Azure VMware](/cli/azure/ext/vmware/vmware).

#### <a name="open-azure-cloud-shell"></a>Aprire Azure Cloud Shell

Selezionare **Prova** nell'angolo superiore destro di un blocco di codice. È anche possibile avviare Cloud Shell in una scheda separata del browser visitando [https://shell.azure.com/bash](https://shell.azure.com/bash). Selezionare **Copia** per copiare i blocchi di codice, incollarli in Cloud Shell e premere **Invio** per eseguirli.

#### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il [comando "az group create".](/cli/azure/group) Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella posizione *eastus*:

```azurecli-interactive

az group create --name myResourceGroup --location eastus
```

#### <a name="create-a-private-cloud"></a>Creare un cloud privato

Specificare un nome per il gruppo di risorse e per il cloud privato, una località e le dimensioni del cluster.

| Proprietà  | Descrizione  |
| --------- | ------------ |
| **-g** (nome del gruppo di risorse)     | Nome del gruppo di risorse per le risorse del cloud privato.        |
| **-n** (nome del cloud privato)     | Nome del cloud privato della soluzione Azure VMware.        |
| **--location**     | Località per il cloud privato.         |
| **--cluster-size**     | Le dimensioni del cluster. Il valore minimo è 3.         |
| **--network-block**     | Il blocco di rete degli indirizzi IP CIDR da usare per il cloud privato. Il blocco di indirizzi non deve sovrapporsi ai blocchi di indirizzi usati in altre reti virtuali presenti nella sottoscrizione e nelle reti locali.        |
| **--sku** | Valore SKU: AV36 |

```azurecli-interactive
az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22 --sku AV36
```

## <a name="azure-vmware-commands"></a>Comandi di Azure VMware

Per un elenco di comandi che è possibile usare con la soluzione Azure VMware, vedere [Comandi di Azure VMware](/cli/azure/ext/vmware/vmware).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * Creare un cloud privato della soluzione Azure VMware
> * Verificare la distribuzione del cloud privato
> * Eliminare un cloud privato della soluzione Azure VMware

Continuare con l'esercitazione successiva per apprendere come creare una jumpbox, che consente di connettersi all'ambiente in uso per poter gestire il cloud privato in locale.


> [!div class="nextstepaction"]
> [Accedere a un cloud privato della soluzione Azure VMware](tutorial-access-private-cloud.md)
