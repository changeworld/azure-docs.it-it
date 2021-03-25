---
title: Trovare e usare le informazioni sul piano di acquisto del Marketplace con PowerShell
description: Usare Azure PowerShell per trovare i parametri delle immagini e i parametri del piano di acquisto, ad esempio Editore, offerta, SKU e versione, per le immagini di macchine virtuali del Marketplace.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 03/17/2021
ms.author: cynthn
ms.custom: contperf-fy21q3
ms.openlocfilehash: 34fd6720b93a1462836b51856d73573a86809367
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105022824"
---
# <a name="find-and-use-azure-marketplace-vm-images-with-azure-powershell"></a>Trovare e usare immagini di VM di Azure Marketplace con Azure PowerShell

Questo articolo descrive come usare Azure PowerShell per trovare immagini di VM in Microsoft Azure Marketplace. È quindi possibile specificare un'immagine del Marketplace e pianificare le informazioni quando si crea una macchina virtuale.

È anche possibile esplorare immagini e offerte disponibili usando [Azure Marketplace](https://azuremarketplace.microsoft.com/) o l'interfaccia della riga di comando di [Azure](../linux/cli-ps-findimage.md). 

## <a name="terminology"></a>Terminologia

Un'immagine del Marketplace in Azure presenta gli attributi seguenti:

* **Editore**: organizzazione che ha creato l'immagine. Esempi: Canonical, MicrosoftWindowsServer
* **Offerta**: nome di un gruppo di immagini correlate create da un editore. Esempi: UbuntuServer, WindowsServer
* **SKU**: istanza di un'offerta, ad esempio una versione principale di una distribuzione. Esempi: 18,04-LTS, 2019-Datacenter
* **Versione**: numero di versione di uno SKU immagine. 

Questi valori possono essere passati singolarmente o come *urn* immagine, combinando i valori separati dai due punti (:). Ad esempio: *server di pubblicazione*:*offerta*:*SKU*:*versione*. È possibile sostituire il numero di versione nell'URN con `latest` per usare la versione più recente dell'immagine. 

Se l'autore dell'immagine fornisce condizioni di licenza e di acquisto aggiuntive, è necessario accettarle prima di poter usare l'immagine.  Per altre informazioni, vedere [accettare i termini del piano di acquisto](#accept-purchase-plan-terms).

## <a name="list-images"></a>Elencare le immagini

È possibile usare PowerShell per circoscrivere un elenco di immagini. Sostituire i valori delle variabili per soddisfare le proprie esigenze.

1. Elencare gli editori di immagini con [Get-AzVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher).
    
    ```powershell
    $locName="<location>"
    Get-AzVMImagePublisher -Location $locName | Select PublisherName
    ```
1. Per un determinato editore, elencare le offerte usando [Get-AzVMImageOffer](/powershell/module/az.compute/get-azvmimageoffer).
    
    ```powershell
    $pubName="<publisher>"
    Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
    ```
1. Per un server di pubblicazione e un'offerta specificati, elencare gli SKU disponibili usando [Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku).
    
    ```powershell
    $offerName="<offer>"
    Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
    ```
1. Per uno SKU, elencare le versioni dell'immagine usando [Get-AzVMImage](/powershell/module/az.compute/get-azvmimage).

    ```powershell
    $skuName="<SKU>"
    Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
    ```
    È anche possibile usare `latest` se si vuole usare l'immagine più recente e non una versione precedente specifica.


Ora è possibile combinare l'editore, l'offerta, lo SKU e la versione selezionati in un URN (valori separati da :). Passare questo URN con il parametro `--image` quando si crea una macchina virtuale con il cmdlet [New-AzVM](/powershell/module/az.compute/new-azvm). È anche possibile sostituire il numero di versione nell'URN con `latest` per ottenere la versione più recente dell'immagine.

Se si distribuisce una macchina virtuale con un modello di Resource Manager, impostare i parametri dell'immagine singolarmente nelle proprietà `imageReference`. Vedere le [informazioni di riferimento sul modello](/azure/templates/microsoft.compute/virtualmachines).


## <a name="view-purchase-plan-properties"></a>Visualizzare le proprietà del piano di acquisto

Alcune immagini di macchina virtuale in Azure Marketplace hanno condizioni di licenza e di acquisto aggiuntive che è necessario accettare prima di poter distribuirle a livello di codice. È necessario accettare i termini dell'immagine una volta per ogni sottoscrizione.

Per visualizzare informazioni sul piano di acquisto di un'immagine, eseguire il cmdlet `Get-AzVMImage`. Se la proprietà `PurchasePlan` nell'output non è `null`, l'immagine presenta condizioni che è necessario accettare prima della distribuzione a livello di codice.  

Ad esempio, l'immagine di *Windows Server 2016 Datacenter* non ha condizioni aggiuntive, quindi l'informazione relativa a `PurchasePlan` è `null`:

```powershell
$version = "2016.127.20170406"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName -Version $version
```

Il risultato sarà simile al seguente:

```output
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2019.0.20190115
Location         : westus
PublisherName    : MicrosoftWindowsServer
Offer            : WindowsServer
Skus             : 2019-Datacenter
Version          : 2019.0.20190115
FilterExpression :
Name             : 2019.0.20190115
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : null
DataDiskImages   : []

```

Nell'esempio seguente viene illustrato un comando simile per l'immagine *Data Science Virtual Machine-Windows 2016* , che presenta le `PurchasePlan` proprietà seguenti: `name` , `product` e `publisher` . Alcune immagini hanno anche una proprietà `promotion code`. Per distribuire questa immagine, vedere le sezioni seguenti per accettare le condizioni e abilitare la distribuzione a livello di codice.

```powershell
Get-AzVMImage -Location "westus" -PublisherName "microsoft-ads" -Offer "windows-data-science-vm" -Skus "windows2016" -Version "0.2.02"
```

Il risultato sarà simile al seguente:

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/microsoft-ads/ArtifactTypes/VMImage/Offers/windows-data-science-vm/Skus/windows2016/Versions/19.01.14
Location         : westus
PublisherName    : microsoft-ads
Offer            : windows-data-science-vm
Skus             : windows2016
Version          : 19.01.14
FilterExpression :
Name             : 19.01.14
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : {
                     "publisher": "microsoft-ads",
                     "name": "windows2016",
                     "product": "windows-data-science-vm"
                   }
DataDiskImages   : []

```

Per visualizzare le condizioni di licenza, usare il cmdlet [Get-AzMarketplaceterms](/powershell/module/az.marketplaceordering/get-azmarketplaceterms) e passare i parametri del piano di acquisto. L'output include un collegamento alle condizioni di licenza per l'immagine del Marketplace e mostra se le condizioni sono già state accettate in precedenza. Assicurarsi di usare tutte lettere minuscole nei valori dei parametri.

```powershell
Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"
```

Il risultato sarà simile al seguente:

```output
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DVM%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : 2UMWH6PHSAIM4U22HXPXW25AL2NHUJ7Y7GRV27EBL6SUIDURGMYG6IIDO3P47FFIBBDFHZHSQTR7PNK6VIIRYJRQ3WXSE6BTNUNENXA
Accepted          : False
Signdate          : 1/25/2019 7:43:00 PM
```

## <a name="accept-purchase-plan-terms"></a>Accetta condizioni del piano di acquisto

Usare il cmdlet [Set-AzMarketplaceterms](/powershell/module/az.marketplaceordering/set-azmarketplaceterms) per accettare o rifiutare le condizioni. È sufficiente accettare le condizioni per l'immagine una sola volta per ogni sottoscrizione. Assicurarsi di usare tutte lettere minuscole nei valori dei parametri. 

```powershell
$agreementTerms=Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"

Set-AzMarketplaceTerms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016" -Terms $agreementTerms -Accept
```



```output
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DV
                    M%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : XXXXXXK3MNJ5SROEG2BYDA2YGECU33GXTD3UFPLPC4BAVKAUL3PDYL3KBKBLG4ZCDJZVNSA7KJWTGMDSYDD6KRLV3LV274DLBXXXXXX
Accepted          : True
Signdate          : 2/23/2018 7:49:31 PM
```


## <a name="create-a-new-vm-from-a-marketplace-image"></a>Creare una nuova macchina virtuale da un'immagine del Marketplace

Se si dispone già delle informazioni sull'immagine che si vuole usare, è possibile passare le informazioni nel cmdlet [set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage) per aggiungere informazioni sull'immagine alla configurazione della macchina virtuale. Vedere le sezioni successive per la ricerca e l'elenco delle immagini disponibili nel Marketplace.

Per alcune immagini a pagamento è anche necessario fornire le informazioni sul piano di acquisto usando [set-AzVMPlan](/powershell/module/az.compute/set-azvmplan). 

```powershell
...

$vmConfig = New-AzVMConfig -VMName "myVM" -VMSize Standard_D1

# Set the Marketplace image
$offerName = "windows-data-science-vm"
$skuName = "windows2016"
$version = "19.01.14"
$vmConfig = Set-AzVMSourceImage -VM $vmConfig -PublisherName $publisherName -Offer $offerName -Skus $skuName -Version $version

# Set the Marketplace plan information, if needed
$publisherName = "microsoft-ads"
$productName = "windows-data-science-vm"
$planName = "windows2016"
$vmConfig = Set-AzVMPlan -VM $vmConfig -Publisher $publisherName -Product $productName -Name $planName

...
```

Si passerà quindi la configurazione della macchina virtuale insieme agli altri oggetti di configurazione al `New-AzVM` cmdlet. Per un esempio dettagliato dell'uso di una configurazione di macchina virtuale con PowerShell, vedere questo [script](https://github.com/Azure/azure-docs-powershell-samples/blob/master/virtual-machine/create-vm-detailed/create-windows-vm-detailed.ps1).

Se viene visualizzato un messaggio relativo all'accettazione delle condizioni dell'immagine, vedere la sezione precedente [accettare](#accept-purchase-plan-terms)i termini del piano di acquisto.

## <a name="create-a-new-vm-from-a-vhd-with-purchase-plan-information"></a>Creare una nuova macchina virtuale da un disco rigido virtuale con informazioni sul piano di acquisto

Se si dispone di un disco rigido virtuale esistente creato con un'immagine di Azure Marketplace, potrebbe essere necessario fornire le informazioni sul piano di acquisto quando si crea una nuova macchina virtuale da tale disco rigido virtuale.

Se è ancora presente la macchina virtuale originale o un'altra macchina virtuale creata dalla stessa immagine, è possibile ottenere il nome del piano, il server di pubblicazione e le informazioni sul prodotto utilizzando Get-AzVM. Questo esempio Mostra come ottenere una macchina virtuale denominata *myVM* nel gruppo di risorse *myResourceGroup* e quindi visualizzare le informazioni sul piano di acquisto.

```azurepowershell-interactive
$vm = Get-azvm `
   -ResourceGroupName myResourceGroup `
   -Name myVM
$vm.Plan
```

Se non sono state rilevate informazioni sul piano prima dell'eliminazione della macchina virtuale originale, è possibile archiviare una [richiesta di supporto](https://ms.portal.azure.com/#create/Microsoft.Support). Saranno necessari il nome della macchina virtuale, l'ID sottoscrizione e il timestamp dell'operazione di eliminazione.

Per creare una VM usando un disco rigido virtuale, fare riferimento a questo articolo [creare una macchina virtuale da un disco rigido virtuale specializzato](create-vm-specialized.md) e aggiungere una riga per aggiungere le informazioni sul piano alla configurazione della macchina virtuale usando [set-AzVMPlan](/powershell/module/az.compute/set-azvmplan) simile al seguente:

```azurepowershell-interactive
$vmConfig = Set-AzVMPlan `
   -VM $vmConfig `
   -Publisher "publisherName" `
   -Product "productName" `
   -Name "planName"
```


## <a name="next-steps"></a>Passaggi successivi

Per creare rapidamente una macchina virtuale con il cmdlet `New-AzVM` usando le informazioni di base sull'immagine, vedere [Creare una macchina virtuale Windows con PowerShell](quick-create-powershell.md).

Per altre informazioni sull'uso delle immagini di Azure Marketplace per creare immagini personalizzate in una raccolta di immagini condivise, vedere [fornire informazioni sul piano di acquisto di Azure Marketplace durante la creazione di immagini](../marketplace-images.md).
