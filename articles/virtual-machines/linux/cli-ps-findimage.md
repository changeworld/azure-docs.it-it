---
title: Trovare e usare informazioni sul piano di acquisto del Marketplace usando l'interfaccia della riga di comando
description: Informazioni su come usare l'interfaccia della riga di comando di Azure per trovare gli URN di immagini e i parametri del piano di acquisto, ad esempio Editore, offerta, SKU e versione, per le immagini di macchine virtuali del Marketplace.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cynthn
ms.collection: linux
ms.custom: contperf-fy21q3-portal
ms.openlocfilehash: 70cb4cc54c6f9a376d3bd38dc8bb6cd3a059a20c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105022844"
---
# <a name="find-azure-marketplace-image-information-using-the-azure-cli"></a>Trovare informazioni sulle immagini di Azure Marketplace usando l'interfaccia della riga di comando di Azure

Questo argomento descrive come usare l'interfaccia della riga di comando di Azure per trovare immagini di macchine virtuali in Azure Marketplace. Usare queste informazioni per specificare un'immagine del Marketplace quando si crea una macchina virtuale a livello di codice mediante l'interfaccia della riga di comando, modelli di Resource Manager o altri strumenti.

È anche possibile esplorare immagini e offerte disponibili usando [Azure Marketplace](https://azuremarketplace.microsoft.com/) o  [Azure PowerShell](../windows/cli-ps-findimage.md). 

## <a name="terminology"></a>Terminologia

Un'immagine del Marketplace in Azure presenta gli attributi seguenti:

* **Editore**: organizzazione che ha creato l'immagine. Esempi: Canonical, MicrosoftWindowsServer
* **Offerta**: nome di un gruppo di immagini correlate create da un editore. Esempi: UbuntuServer, WindowsServer
* **SKU**: istanza di un'offerta, ad esempio una versione principale di una distribuzione. Esempi: 18,04-LTS, 2019-Datacenter
* **Versione**: numero di versione di uno SKU immagine. 

Questi valori possono essere passati singolarmente o come *urn* immagine, combinando i valori separati dai due punti (:). Ad esempio: *server di pubblicazione*:*offerta*:*SKU*:*versione*. È possibile sostituire il numero di versione nell'URN con `latest` per usare la versione più recente dell'immagine. 

Se l'autore dell'immagine fornisce condizioni di licenza e di acquisto aggiuntive, è necessario accettarle prima di poter usare l'immagine.  Per ulteriori informazioni, vedere [controllare le informazioni sul piano di acquisto](#check-the-purchase-plan-information).



## <a name="list-popular-images"></a>Elencare immagini popolari

Eseguire il comando [az vm image list](/cli/azure/vm/image), senza l'opzione `--all`, per visualizzare un elenco di immagini di VM popolari in Azure Marketplace. Ad esempio, eseguire il comando seguente per visualizzare un elenco memorizzato nella cache di immagini popolari in formato tabella:

```azurecli
az vm image list --output table
```

L'output include l'URN dell'immagine. È anche possibile usare *UrnAlias* , una versione abbreviata creata per le immagini più diffuse, ad esempio *UbuntuLTS*.

```output
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.5                 OpenLogic:CentOS:7.5:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
debian-10      Debian                  10                  Debian:debian-10:10:latest                                      Debian               latest
openSUSE-Leap  SUSE                    42.3                SUSE:openSUSE-Leap:42.3:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7-LVM               RedHat:RHEL:7-LVM:latest                                        RHEL                 latest
SLES           SUSE                    15                  SUSE:SLES:15:latest                                             SLES                 latest
UbuntuServer   Canonical               18.04-LTS           Canonical:UbuntuServer:18.04-LTS:latest                         UbuntuLTS            latest
WindowsServer  MicrosoftWindowsServer  2019-Datacenter     MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest     Win2019Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2016-Datacenter     MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest     Win2016Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
```

## <a name="find-specific-images"></a>Trovare immagini specifiche

Per trovare un'immagine di VM specifica in Marketplace, usare il comando `az vm image list` con l'opzione `--all`. Questa versione del comando richiede del tempo per essere completata e può restituire un output lungo, pertanto l'elenco si filtra in genere in base a `--publisher` o a un altro parametro. 

Ad esempio, il comando che segue visualizza tutte le offerte Debian. Tenere presente che senza l'opzione `--all`, la ricerca viene eseguita solo nella cache locale delle immagini comuni:

```azurecli
az vm image list --offer Debian --all --output table 
```

Output parziale: 

```output
Offer                                    Publisher                         Sku                                      Urn                                                                                                   Version
---------------------------------------  --------------------------------  ---------------------------------------  ----------------------------------------------------------------------------------------------------  --------------
apache-solr-on-debian                    apps-4-rent                       apache-solr-on-debian                    apps-4-rent:apache-solr-on-debian:apache-solr-on-debian:1.0.0                                         1.0.0
atomized-h-debian10-v1                   atomizedinc1587939464368          hdebian10plan                            atomizedinc1587939464368:atomized-h-debian10-v1:hdebian10plan:1.0.0                                   1.0.0
atomized-h-debian9-v1                    atomizedinc1587939464368          hdebian9plan                             atomizedinc1587939464368:atomized-h-debian9-v1:hdebian9plan:1.0.0                                     1.0.0
atomized-r-debian10-v1                   atomizedinc1587939464368          rdebian10plan                            atomizedinc1587939464368:atomized-r-debian10-v1:rdebian10plan:1.0.0                                   1.0.0
atomized-r-debian9-v1                    atomizedinc1587939464368          rdebian9plan                             atomizedinc1587939464368:atomized-r-debian9-v1:rdebian9plan:1.0.0                                     1.0.0
cis-debian-linux-10-l1                   center-for-internet-security-inc  cis-debian10-l1                          center-for-internet-security-inc:cis-debian-linux-10-l1:cis-debian10-l1:1.0.7                         1.0.7
cis-debian-linux-10-l1                   center-for-internet-security-inc  cis-debian10-l1                          center-for-internet-security-inc:cis-debian-linux-10-l1:cis-debian10-l1:1.0.8                         1.0.8
cis-debian-linux-10-l1                   center-for-internet-security-inc  cis-debian10-l1                          center-for-internet-security-inc:cis-debian-linux-10-l1:cis-debian10-l1:1.0.9                         1.0.9
cis-debian-linux-9-l1                    center-for-internet-security-inc  cis-debian9-l1                           center-for-internet-security-inc:cis-debian-linux-9-l1:cis-debian9-l1:1.0.18                          1.0.18
cis-debian-linux-9-l1                    center-for-internet-security-inc  cis-debian9-l1                           center-for-internet-security-inc:cis-debian-linux-9-l1:cis-debian9-l1:1.0.19                          1.0.19
cis-debian-linux-9-l1                    center-for-internet-security-inc  cis-debian9-l1                           center-for-internet-security-inc:cis-debian-linux-9-l1:cis-debian9-l1:1.0.20                          1.0.20
apache-web-server-with-debian-10         cognosys                          apache-web-server-with-debian-10         cognosys:apache-web-server-with-debian-10:apache-web-server-with-debian-10:1.2019.1008                1.2019.1008
docker-ce-with-debian-10                 cognosys                          docker-ce-with-debian-10                 cognosys:docker-ce-with-debian-10:docker-ce-with-debian-10:1.2019.0710                                1.2019.0710
Debian                                   credativ                          8                                        credativ:Debian:8:8.0.201602010                                                                       8.0.201602010
Debian                                   credativ                          8                                        credativ:Debian:8:8.0.201603020                                                                       8.0.201603020
Debian                                   credativ                          8                                        credativ:Debian:8:8.0.201604050                                                                       8.0.201604050
...
```


## <a name="look-at-all-available-images"></a>Esaminare tutte le immagini disponibili
 
Un altro modo per trovare un'immagine in una posizione è l'esecuzione in sequenza dei comandi [az vm image list-publishers](/cli/azure/vm/image), [az vm image list-offers](/cli/azure/vm/image) e [az vm image list-skus](/cli/azure/vm/image) . Con questi comandi si determinano questi valori:

1. Elencare gli editori di immagini per un percorso. In questo esempio si esamina l'area *Stati Uniti occidentali* .
    
    ```azurecli
    az vm image list-publishers --location westus --output table
    ```

1. Elencando le offerte di un determinato editore. In questo esempio viene aggiunto *Canonical* come server di pubblicazione.
    
    ```azurecli
    az vm image list-offers --location westus --publisher Canonical --output table
    ```

1. Elencando le SKU di una determinata offerta. In questo esempio viene aggiunto *UbuntuServer* come offerta.
    ```azurecli
    az vm image list-skus --location westus --publisher Canonical --offer UbuntuServer --output table
    ```

1. Per i server di pubblicazione, l'offerta e lo SKU specificati, visualizzare tutte le versioni dell'immagine. In questo esempio si aggiunge *18,04-LTS* come SKU.

    ```azurecli
    az vm image list \
        --location westus \
        --publisher Canonical \  
        --offer UbuntuServer \    
        --sku 18.04-LTS \
        --all --output table
    ```

Passare questo valore della colonna URN con il `--image` parametro quando si crea una macchina virtuale con il comando [AZ VM create](/cli/azure/vm) . È anche possibile sostituire il numero di versione nell'URN con "Latest" per usare semplicemente la versione più recente dell'immagine. 

Se si distribuisce una macchina virtuale con un modello di Resource Manager, impostare singolarmente i parametri dell'immagine nelle proprietà `imageReference`. Vedere le [informazioni di riferimento sul modello](/azure/templates/microsoft.compute/virtualmachines).


## <a name="check-the-purchase-plan-information"></a>Controllare le informazioni sul piano di acquisto

Alcune immagini di macchina virtuale in Azure Marketplace hanno condizioni di licenza e di acquisto aggiuntive che è necessario accettare prima di poter distribuirle a livello di codice.  

Per distribuire una macchina virtuale da tale immagine, è necessario accettare i termini dell'immagine la prima volta che si usa, una volta per ogni sottoscrizione. È anche necessario specificare i parametri del *piano di acquisto* per distribuire una macchina virtuale da tale immagine

Per visualizzare le informazioni sul piano di acquisto di un'immagine, eseguire il comando [AZ VM Image Show](/cli/azure/image) con l'URN dell'immagine. Se la proprietà `plan` nell'output non è `null`, l'immagine presenta condizioni che è necessario accettare prima della distribuzione a livello di codice.

L'immagine Canonical Ubuntu Server 18.04 LTS, ad esempio, non ha condizioni aggiuntive, perché l'informazione `plan` è `null`:

```azurecli
az vm image show --location westus --urn Canonical:UbuntuServer:18.04-LTS:latest
```

Output:

```output
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/Canonical/ArtifactTypes/VMImage/Offers/UbuntuServer/Skus/18.04-LTS/Versions/18.04.201901220",
  "location": "westus",
  "name": "18.04.201901220",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": null,
  "tags": null
}
```

Eseguendo un comando analogo per l'immagine RabbitMQ certificata da Bitnami vengono mostrate le proprietà `plan` seguenti: `name`, `product` e `publisher`. (Alcune immagini hanno anche una proprietà `promotion code`.) 

```azurecli
az vm image show --location westus --urn bitnami:rabbitmq:rabbitmq:latest
```
Output:

```output
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/bitnami/ArtifactTypes/VMImage/Offers/rabbitmq/Skus/rabbitmq/Versions/3.7.1901151016",
  "location": "westus",
  "name": "3.7.1901151016",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": {
    "name": "rabbitmq",
    "product": "rabbitmq",
    "publisher": "bitnami"
  },
  "tags": null
}
```

Per distribuire questa immagine, è necessario accettare i termini e fornire i parametri del piano di acquisto quando si distribuisce una macchina virtuale usando l'immagine.

## <a name="accept-the-terms"></a>Accettare le condizioni

Per visualizzare e accettare le condizioni di licenza, usare il comando [az vm image accept-terms](/cli/azure/vm/image/terms). Quando si accettano le condizioni, si abilita la distribuzione a livello di codice nella sottoscrizione. È sufficiente accettare le condizioni per l'immagine una sola volta per ogni sottoscrizione. Ad esempio:

```azurecli
az vm image terms show --urn bitnami:rabbitmq:rabbitmq:latest
``` 

L'output include un `licenseTextLink` alle condizioni di licenza e indica che il valore di `accepted` è `true`:

```output
{
  "accepted": true,
  "additionalProperties": {},
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.MarketplaceOrdering/offertypes/bitnami/offers/rabbitmq/plans/rabbitmq",
  "licenseTextLink": "https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_BITNAMI%253a24RABBITMQ%253a24RABBITMQ%253a24IGRT7HHPIFOBV3IQYJHEN2O2FGUVXXZ3WUYIMEIVF3KCUNJ7GTVXNNM23I567GBMNDWRFOY4WXJPN5PUYXNKB2QLAKCHP4IE5GO3B2I.txt",
  "name": "rabbitmq",
  "plan": "rabbitmq",
  "privacyPolicyLink": "https://bitnami.com/privacy",
  "product": "rabbitmq",
  "publisher": "bitnami",
  "retrieveDatetime": "2019-01-25T20:37:49.937096Z",
  "signature": "XXXXXXLAZIK7ZL2YRV5JYQXONPV76NQJW3FKMKDZYCRGXZYVDGX6BVY45JO3BXVMNA2COBOEYG2NO76ONORU7ITTRHGZDYNJNXXXXXX",
  "type": "Microsoft.MarketplaceOrdering/offertypes"
}
```

Per accettare i termini, digitare:

```azurecli
az vm image terms accept --urn bitnami:rabbitmq:rabbitmq:latest
``` 

## <a name="deploy-a-new-vm-using-the-image-parameters"></a>Distribuire una nuova VM usando i parametri dell'immagine

Con le informazioni sull'immagine, è possibile distribuirla usando il `az vm create` comando. 

Per distribuire un'immagine che non dispone di informazioni sul piano, ad esempio l'immagine più recente di Ubuntu server 18,04 da Canonical, passare l'URN per `--image` :

```azurecli-interactive
az group create --name myURNVM --location westus
az vm create \
   --resource-group myURNVM \
   --name myVM \
   --admin-username azureuser \
   --generate-ssh-keys \
   --image Canonical:UbuntuServer:18.04-LTS:latest 
```


Per un'immagine con i parametri del piano di acquisto, ad esempio l'immagine RabbitMQ Certified by BitNami, è possibile passare l'URN per `--image` e fornire anche i parametri del piano di acquisto:

```azurecli
az group create --name myPurchasePlanRG --location westus

az vm create \
   --resource-group myPurchasePlanRG \
   --name myVM \
   --admin-username azureuser \
   --generate-ssh-keys \
   --image bitnami:rabbitmq:rabbitmq:latest \
   --plan-name rabbitmq \
   --plan-product rabbitmq \
   --plan-publisher bitnami
```

Se viene ricevuto un messaggio per l'accettazione delle condizioni dell'immagine, vedere [la sezione accettare le condizioni](#accept-the-terms). Verificare che l'output di `az vm image accept-terms` restituisca il valore `"accepted": true,` che indica che sono state accettate le condizioni dell'immagine.


## <a name="using-an-existing-vhd-with-purchase-plan-information"></a>Uso di un VHD esistente con informazioni sul piano di acquisto

Se si dispone di un disco rigido virtuale esistente da una macchina virtuale creata usando un'immagine di Azure Marketplace a pagamento, potrebbe essere necessario fornire le informazioni sul piano di acquisto quando si crea una nuova macchina virtuale da tale disco rigido virtuale. 

Se è ancora presente la macchina virtuale originale o un'altra macchina virtuale creata usando la stessa immagine del Marketplace, è possibile ottenere il nome del piano, l'editore e le informazioni sul prodotto usando il comando [AZ VM Get-instance-View](/cli/azure/vm#az_vm_get_instance_view). Questo esempio Mostra come ottenere una macchina virtuale denominata *myVM* nel gruppo di risorse *myResourceGroup* e quindi visualizzare le informazioni sul piano di acquisto.

```azurepowershell-interactive
az vm get-instance-view -g myResourceGroup -n myVM --query plan
```

Se non sono state rilevate informazioni sul piano prima dell'eliminazione della macchina virtuale originale, è possibile archiviare una [richiesta di supporto](https://ms.portal.azure.com/#create/Microsoft.Support). Saranno necessari il nome della macchina virtuale, l'ID sottoscrizione e il timestamp dell'operazione di eliminazione.

Dopo aver ottenuto le informazioni sul piano, è possibile creare la nuova macchina virtuale usando il `--attach-os-disk` parametro per specificare il disco rigido virtuale.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myNewVM \
  --nics myNic \
  --size Standard_DS1_v2 --os-type Linux \
  --attach-os-disk myVHD \
  --plan-name planName \
  --plan-publisher planPublisher \
  --plan-product planProduct 
```


## <a name="next-steps"></a>Passaggi successivi
Per creare rapidamente una macchina virtuale usando le informazioni relative all'immagine, vedere [Creare e gestire VM Linux con l'interfaccia della riga di comando di Azure](tutorial-manage-vm.md).
