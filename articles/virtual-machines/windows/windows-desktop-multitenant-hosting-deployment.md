---
title: Come distribuire Windows 10 in Azure con Multitenant Hosting Rights
description: Informazioni su come ottimizzare i vantaggi di Windows Software Assurance per portare licenze locali in Azure con diritti di hosting multi-tenant.
author: mimckitt
ms.service: virtual-machines
ms.collection: windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 2/2/2021
ms.author: mimckitt
ms.custom: rybaker, chmimckitt
ms.openlocfilehash: bb86ba6867ad796ef0f5eeb1357a6df9e93e9f9e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102555772"
---
# <a name="how-to-deploy-windows-10-on-azure-with-multitenant-hosting-rights"></a>Come distribuire Windows 10 in Azure con Multitenant Hosting Rights 
Per i clienti con Windows 10 Enterprise E3/E5 per utente o Windows Virtual Desktop Access per utente (licenze di sottoscrizione utente o licenze di sottoscrizione utente per i componenti aggiuntivi) Multitenant Hosting Rights per Windows 10 consente di trasferire le licenze di Windows 10 nel cloud ed eseguire macchine virtuali di Windows 10 in Azure senza sostenere i costi di un'altra licenza. I diritti di hosting multi-tenant sono disponibili solo per Windows 10 (versione 1703 o successiva).

Per altre informazioni, vedere [hosting multi-tenant per Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx).

> [!NOTE]
> - Per usare Windows 7, 8,1 e 10 immagini per lo sviluppo o il test, vedere [client Windows in Azure per scenari di sviluppo/test](client-images.md)
> - Per i vantaggi della licenza per Windows Server, fare riferimento a [Azure Hybrid use benefits for Windows Server images](hybrid-use-benefit-licensing.md) (Vantaggi Azure Hybrid Use per le immagini di Windows Server).

## <a name="subscription-licenses-that-qualify-for-multitenant-hosting-rights"></a>Licenze di sottoscrizione che sono idonee per i diritti di hosting multi-tenant

Usando l'interfaccia di [amministrazione di Microsoft](/microsoft-365/admin/admin-overview/about-the-admin-center), è possibile verificare se a un utente è stata assegnata una licenza supportata da Windows 10.

> [!IMPORTANT]
> Per usare le immagini di Windows 10 in Azure, gli utenti devono avere una delle licenze di sottoscrizione seguenti. Se non si ha una di queste licenze di sottoscrizione, è possibile acquistarle tramite il [partner del servizio cloud](https://azure.microsoft.com/overview/choosing-a-cloud-service-provider/) o direttamente tramite [Microsoft](https://www.microsoft.com/microsoft-365?rtc=1).

**Licenze di sottoscrizione idonee:**

-   Microsoft 365 E3/E5 
-   Microsoft 365 F3 
-   Microsoft 365 a3/a5 
-   Windows 10 Enterprise E3/E5
-   Windows 10 Education a3/a5 
-   Windows VDA E3/E5


## <a name="deploying-windows-10-image-from-azure-marketplace"></a>Distribuzione dell'immagine di Windows 10 da Azure Marketplace 
Per le distribuzioni di PowerShell, CLI e Azure Resource Manager modello, le immagini di Windows 10 sono reperibili usando `PublisherName: MicrosoftWindowsDesktop` e `Offer: Windows-10` . Windows 10 version Creators Update (1809) o versioni successive è supportato per i diritti di hosting multi-tenant. 

```powershell
Get-AzVmImageSku -Location '$location' -PublisherName 'MicrosoftWindowsDesktop' -Offer 'Windows-10'

Skus                        Offer      PublisherName           Location 
----                        -----      -------------           -------- 
rs4-pro                     Windows-10 MicrosoftWindowsDesktop eastus   
rs4-pron                    Windows-10 MicrosoftWindowsDesktop eastus   
rs5-enterprise              Windows-10 MicrosoftWindowsDesktop eastus   
rs5-enterprisen             Windows-10 MicrosoftWindowsDesktop eastus   
rs5-pro                     Windows-10 MicrosoftWindowsDesktop eastus   
rs5-pron                    Windows-10 MicrosoftWindowsDesktop eastus  
```

Per altre informazioni sulle immagini disponibili [, vedere trovare e usare immagini di VM di Azure Marketplace con Azure PowerShell](./cli-ps-findimage.md)

## <a name="uploading-windows-10-vhd-to-azure"></a>Caricamento di un disco rigido virtuale di Windows 10 in Azure
Se si carica un disco rigido virtuale generalizzato di Windows 10, tenere presente che per impostazione predefinita in Windows 10 non è abilitato un account predefinito Administrator. Per abilitare l'account predefinito Administrator, includere il comando seguente come parte dell'estensione Script personalizzato.

```powershell
Net user <username> /active:yes
```

Il frammento di codice di PowerShell seguente consente di contrassegnare tutti gli account amministratore come attivi, incluso l'amministratore predefinito. Questo esempio è utile se il nome utente dell'account predefinito Administrator è sconosciuto.
```powershell
$adminAccount = Get-WmiObject Win32_UserAccount -filter "LocalAccount=True" | ? {$_.SID -Like "S-1-5-21-*-500"}
if($adminAccount.Disabled)
{
    $adminAccount.Disabled = $false
    $adminAccount.Put()
}
```
Per altre informazioni: 
* [How to upload VHD to Azure](upload-generalized-managed.md) (Come caricare un disco rigido virtuale in Azure)
* [How to prepare a Windows VHD to upload to Azure](prepare-for-upload-vhd-image.md) (Come preparare un disco rigido virtuale di Windows per il caricamento in Azure)


## <a name="deploying-windows-10-with-multitenant-hosting-rights"></a>Distribuzione di Windows 10 con Multitenant Hosting Rights
Verificare di aver prima [installato e configurato l'ultima versione di Azure PowerShell](/powershell/azure/). Dopo aver preparato il disco rigido virtuale, caricarlo nell'account di archiviazione di Azure usando il cmdlet `Add-AzVhd` come segue:

```powershell
Add-AzVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```


**Distribuzione con Distribuzione di modelli di Azure Resource Manager** All'interno dei modelli di Resource Manager è possibile specificare un parametro aggiuntivo per `licenseType`. Altre informazioni sulla creazione di [modelli di Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md). Dopo aver caricato il disco rigido virtuale in Azure, modificare il modello di Resource Manager per includere il tipo di licenza come parte del provider di calcolo e distribuire il modello come di consueto:
```json
"properties": {
    "licenseType": "Windows_Client",
    "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
    }
```

**Distribuzione con PowerShell** Quando si distribuisce la macchina virtuale Windows Server con PowerShell è presente un parametro aggiuntivo per `-LicenseType`. Dopo aver caricato il disco rigido virtuale in Azure, creare una nuova VM usando `New-AzVM` e specificare il tipo di licenza come segue:
```powershell
New-AzVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Client"
```

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Verificare che la macchina virtuale usi il vantaggio della licenza
Dopo avere distribuito la VM con il metodo di distribuzione tramite PowerShell o Resource Manager, verificare il tipo di licenza con `Get-AzVM` come indicato di seguito:
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

L'output è simile all'esempio seguente per Windows 10 con il tipo di licenza corretto:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

L'output differisce da quello della seguente VM distribuita senza vantaggio Azure Hybrid Use, quale ad esempio una VM distribuita direttamente dalla raccolta di Azure:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="additional-information-about-joining-azure-ad"></a>Altre informazioni sull'aggiunta di Azure AD
Azure esegue il provisioning di tutte le macchine virtuali di Windows con l'account predefinito Administrator, che non può essere usato per l'aggiunta di AAD. Ad esempio, *Impostazioni > Account > Accedi all'azienda o all'istituto di istruzione > +Connetti* non funziona. È necessario creare e accedere come secondo account Administrator per aggiungere manualmente Azure AD. È anche possibile configurare Azure AD usando un pacchetto di provisioning. per altre informazioni, usare il collegamento nella sezione *passaggi successivi* .

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulla [configurazione di un disco rigido virtuale per Windows 10](/windows/deployment/vda-subscription-activation)
- Altre informazioni sull'[hosting multi-tenant per Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)