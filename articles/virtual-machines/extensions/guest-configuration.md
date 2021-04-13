---
title: Criteri di Azure'estensione Configurazione guest
description: Informazioni sull'estensione usata per controllare/configurare le impostazioni all'interno delle macchine virtuali
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: mgreenegit
ms.author: migreene
ms.date: 04/15/2021
ms.openlocfilehash: 2fda3cc2cf9adc3a734780209a0c9cc06a04e7cf
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368665"
---
# <a name="overview-of-the-azure-policy-guest-configuration-extension"></a>Panoramica dell'estensione Criteri di Azure configurazione guest

L'estensione Configurazione guest è un componente che Criteri di Azure le operazioni di controllo e configurazione all'interno delle macchine virtuali.
Criteri come le definizioni delle baseline di sicurezza [per Linux](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc9b3da7-8347-4380-8e70-0a0361d8dedd) e [Windows](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72650e9f-97bc-4b2a-ab5f-9781a9fcecbc) non possono controllare le impostazioni all'interno dei computer fino a quando non viene installata l'estensione.

## <a name="prerequisites"></a>Prerequisiti

Per l'autenticazione del computer nel servizio Configurazione guest, il computer deve avere [un'identità gestita assegnata dal sistema.](../../active-directory/managed-identities-azure-resources/overview.md)
Il requisito di identità in una macchina virtuale viene soddisfatto se è impostata la proprietà seguente.

  ```json
  "identity": {
    "type": "SystemAssigned"
  }
  ```

### <a name="operating-systems"></a>Sistemi operativi

Il supporto per l'estensione Configurazione guest è uguale al supporto del sistema operativo [documentato per la soluzione end-to-end](../../governance/policy/concepts/guest-configuration.md#supported-client-types).

### <a name="internet-connectivity"></a>Connettività Internet

L'agente installato dall'estensione Configurazione guest deve essere in grado di raggiungere i pacchetti di contenuto elencati dalle assegnazioni di Configurazione guest e segnalare lo stato al servizio Configurazione guest.
Il computer può connettersi tramite HTTPS in uscita sulla porta TCP 443 o se viene fornita una connessione tramite rete privata.
Per altre informazioni sulla rete privata, vedere gli articoli seguenti:

- [Configurazione guest, comunicare tramite collegamento privato in Azure](../../governance/policy/concepts/guest-configuration.md#communicate-over-private-link-in-azure)
- [Usare endpoint privati per Archiviazione di Azure](../../storage/common/storage-private-endpoints.md)

## <a name="how-can-i-install-the-extension"></a>Come è possibile installare l'estensione?

Per distribuire la versione più recente dell'estensione su larga scala, inclusi i requisiti di identità, assegnare il Criteri di Azure distribuisci i prerequisiti per abilitare i criteri di Configurazione [guest nelle macchine virtuali.](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_Prerequisites.json)
Per i singoli computer, l'estensione può essere distribuita usando l'interfaccia della riga di comando di Azure, PowerShell, Resource Manager modelli o strumenti di terze parti.

Il nome dell'istanza dell'estensione deve essere impostato su "AzurePolicyforWindows" o "AzurePolicyforLinux", perché i criteri a cui si fa riferimento in precedenza richiedono queste stringhe specifiche.

Per impostazione predefinita, tutte le distribuzioni vengono aggiornate alla versione più recente. Il valore predefinito della _proprietà autoUpgradeMinorVersion_ è "true" a meno che non venga specificato diversamente. Non è necessario preoccuparsi di aggiornare il codice quando vengono rilasciate nuove versioni dell'estensione.

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per distribuire l'estensione per Linux:


```azurecli
az vm extension set  --publisher Microsoft.GuestConfiguration --name ConfigurationforLinux --extension-instance-name AzurePolicyforLinux --resource-group myResourceGroup --vm-name myVM
```

Per distribuire l'estensione per Windows:

```azurecli
az vm extension set  --publisher Microsoft.GuestConfiguration --name ConfigurationforWindows --extension-instance-name AzurePolicyforWindows --resource-group myResourceGroup --vm-name myVM
```

### <a name="powershell"></a>PowerShell

Per distribuire l'estensione per Linux:

```powershell
Set-AzVMExtension -Publisher 'Microsoft.GuestConfiguration' -Type 'ConfigurationforLinux' -Name 'AzurePolicyforLinux' -TypeHandlerVersion 1.0 -ResourceGroupName 'myResourceGroup' -Location 'myLocation' -VMName 'myVM'
```

Per distribuire l'estensione per Windows:

```powershell
Set-AzVMExtension -Publisher 'Microsoft.GuestConfiguration' -Type 'ConfigurationforWindows' -Name 'AzurePolicyforWindows' -TypeHandlerVersion 1.0 -ResourceGroupName 'myResourceGroup' -Location 'myLocation' -VMName 'myVM'
```

### <a name="resource-manager-template"></a>Modello di Resource Manager

Per distribuire l'estensione per Linux:

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(parameters('VMName'), '/AzurePolicyforLinux')]",
  "apiVersion": "2019-07-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.GuestConfiguration",
    "type": "ConfigurationforLinux",
    "typeHandlerVersion": "1.0"
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

Per distribuire l'estensione per Windows:

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(parameters('VMName'), '/AzurePolicyforWindows')]",
  "apiVersion": "2019-07-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.GuestConfiguration",
    "type": "ConfigurationforWindows",
    "typeHandlerVersion": "1.0"
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

### <a name="terraform"></a>Terraform

Per distribuire l'estensione per Linux:

```terraform
resource "azurerm_virtual_machine_extension" "gc" {
  name                  = "AzurePolicyforLinux"
  virtual_machine_id    = "myVMID"
  publisher             = "Microsoft.GuestConfiguration"
  type                  = "ConfigurationforLinux"
  type_handler_version  = "1.0"
}
```

Per distribuire l'estensione per Windows:

```terraform
resource "azurerm_virtual_machine_extension" "gc" {
  name                  = "AzurePolicyforWindows"
  virtual_machine_id    = "myVMID"
  publisher             = "Microsoft.GuestConfiguration"
  type                  = "ConfigurationforWindows"
  type_handler_version  = "1.0"
}
```

## <a name="settings"></a>Impostazioni

Non è necessario includere impostazioni o proprietà delle impostazioni protette nell'estensione.
Tutte queste informazioni vengono recuperate dall'agente dalle [risorse di assegnazione della configurazione](/rest/api/guestconfiguration/guestconfigurationassignments) guest. Ad esempio, le [proprietà ConfigurationUri](/rest/api/guestconfiguration/guestconfigurationassignments/createorupdate#guestconfigurationnavigation), [Mode](/rest/api/guestconfiguration/guestconfigurationassignments/createorupdate#configurationmode)e [ConfigurationSetting](/rest/api/guestconfiguration/guestconfigurationassignments/createorupdate#configurationsetting) sono gestite per ogni configurazione anziché nell'estensione vm.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla Criteri di Azure guest, vedere Informazioni sulla configurazione guest Criteri di Azure [di Criteri di Azure](../../governance/policy/concepts/guest-configuration.md)
* Per altre informazioni sul funzionamento dell'agente Linux e delle estensioni, vedere Estensioni e funzionalità delle macchine virtuali [di Azure per Linux.](features-linux.md)
* Per altre informazioni sul funzionamento dell'agente guest Di Windows e delle estensioni, vedere Estensioni e funzionalità delle macchine virtuali [di Azure per Windows.](features-windows.md)  
* Per installare l'agente guest Windows, vedere Panoramica [dell'agente macchina virtuale Windows di Azure.](agent-windows.md)  
* Per installare l'agente Linux, vedere [Panoramica dell'agente macchina virtuale Linux di Azure.](agent-linux.md)  
