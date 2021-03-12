---
title: Domande comuni sulla macchina virtuale in Azure Marketplace
description: Domande comuni rilevate durante la creazione di una macchina virtuale in Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/15/2020
ms.openlocfilehash: 09644747c36b5406960097e52bbeeef1fa157e89
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "102629994"
---
# <a name="common-questions-about-vm-in-azure-marketplace"></a>Domande comuni sulla macchina virtuale in Azure Marketplace

Queste domande frequenti riguardano i problemi comuni che possono verificarsi durante la creazione di un'offerta di macchina virtuale (VM) in Azure Marketplace.

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Ricerca per categorie: configurare una rete VPN da usare con le macchine virtuali

Se si usa il modello di distribuzione Azure Resource Manager, sono disponibili tre opzioni:

- [Creare un gateway VPN basato su route usando il portale di Azure](../vpn-gateway/tutorial-create-gateway-portal.md)
- [Creare un gateway VPN basato su route usando Azure PowerShell](../vpn-gateway/create-routebased-vpn-gateway-powershell.md)
- [Creare un gateway VPN basato su route usando l'interfaccia della riga di comando](../vpn-gateway/create-routebased-vpn-gateway-cli.md)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Quali sono i criteri di supporto Microsoft per l'esecuzione di software server Microsoft in macchine virtuali basate su Azure?

Maggiori dettagli sono disponibili in [Supporto software di server Microsoft per macchine virtuali di Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>In una macchina virtuale, com'è possibile gestire l'estensione di script personalizzati nell'attività di avvio?

Per informazioni dettagliate su come usare l'estensione di script personalizzato usando il modulo Azure PowerShell e i modelli di Azure Resource Manager, nonché i passaggi di risoluzione dei problemi in Windows, vedere [Estensione di script personalizzati per Windows](../virtual-machines/extensions/custom-script-windows.md).

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>Le applicazioni o i servizi a 32 bit sono supportati in Azure Marketplace?

No. I sistemi operativi supportati e i servizi standard per le macchine virtuali di Azure sono tutti a 64 bit. Sebbene la maggior parte dei sistemi operativi a 64 bit supporti le versioni a 32 bit delle applicazioni per la compatibilità con le versioni precedenti, l'uso di applicazioni a 32 bit come parte della soluzione di macchina virtuale non è supportato ed è altamente sconsigliato. Ricreare l'applicazione come un progetto a 64 bit.

Per altre informazioni, vedere questi articoli:

- [Running 32-bit applications](/windows/desktop/WinProg64/running-32-bit-applications) (Applicazioni a 32 bit in esecuzione)
- [Support for 32-bit operating systems in Azure virtual machines](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines) (Supporto per sistemi operativi a 32 bit nelle macchine virtuali di Azure)
- [Microsoft server software support for Microsoft Azure virtual machines](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) (Supporto software di server Microsoft per macchine virtuali di Microsoft Azure)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>Errore: Il disco rigido virtuale è già registrato con l'archivio immagini come risorsa

Ogni volta che si tenta di creare un'immagine dai dischi rigidi virtuali, in Azure PowerShell viene visualizzato l'errore "Il disco rigido virtuale è già registrato con l'archivio immagini come risorsa". Non è stato creato alcun tipo di risorsa in precedenza, né è stata trovata alcuna immagine con questo nome in Azure. Come posso risolvere il problema?

Questo problema si verifica in genere se l'utente ha creato una macchina virtuale da un disco rigido virtuale con un blocco. Accertarsi che da questo disco rigido virtuale non sia allocata alcuna macchina virtuale, quindi ripetere l'operazione. Se il problema persiste, aprire un ticket di supporto. Vedere [Supporto per il Centro per i partner](support.md).

## <a name="how-do-i-create-a-vm-from-a-generalized-vhd"></a>Ricerca per categorie creare una macchina virtuale da un disco rigido virtuale generalizzato?

### <a name="prepare-an-azure-resource-manager-template"></a>Preparare un modello di Azure Resource Manager

Questa sezione descrive come creare e distribuire un'immagine di macchina virtuale (VM) fornita dall'utente. A tale scopo, è possibile fornire le immagini del sistema operativo e del disco rigido virtuale del disco dati da un disco rigido virtuale distribuito da Azure. Questa procedura distribuisce la macchina virtuale usando il disco rigido virtuale generalizzato.

1. Accedere al portale di Azure.
2. Caricare il VHD del sistema operativo generalizzato e i dischi rigidi virtuali del disco dati nell'account di archiviazione di Azure.
3. Nella home page selezionare Crea una risorsa, cercare "distribuzione modello" e selezionare Crea.
4. Scegliere Creare un modello personalizzato nell'editor.

   :::image type="content" source="media/vm/template-deployment.png" alt-text="Mostra la selezione di un modello":::

5. Incollare il modello JSON seguente nell'editor e selezionare Salva.
 ```json
  {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
           "userStorageAccountName": {
               "type": "String"
           },
           "userStorageContainerName": {
               "defaultValue": "vhds",
               "type": "String"
           },
           "dnsNameForPublicIP": {
               "type": "String"
           },
           "adminUserName": {
               "defaultValue": "isv",
               "type": "String"
           },
           "adminPassword": {
               "defaultValue": "",
               "type": "SecureString"
           },
           "osType": {
               "defaultValue": "windows",
               "allowedValues": [
                   "windows",
                   "linux"
               ],
               "type": "String"
           },
           "subscriptionId": {
               "type": "String"
           },
           "location": {
               "type": "String"
           },
           "vmSize": {
               "type": "String"
           },
           "publicIPAddressName": {
               "type": "String"
           },
           "vmName": {
               "type": "String"
           },
           "virtualNetworkName": {
               "type": "String"
           },
           "nicName": {
               "type": "String"
           },
           "vhdUrl": {
               "type": "String",
               "metadata": {
                   "description": "VHD Url..."
               }
           }
       },
       "variables": {
           "addressPrefix": "10.0.0.0/16",
           "subnet1Name": "Subnet-1",
           "subnet2Name": "Subnet-2",
           "subnet1Prefix": "10.0.0.0/24",
           "subnet2Prefix": "10.0.1.0/24",
           "publicIPAddressType": "Dynamic",
           "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
           "subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
           "hostDNSNameScriptArgument": "[concat(parameters('dnsNameForPublicIP'),'.',parameters('location'),'.cloudapp.azure.com')]",
           "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
       },
       "resources": [
           {
               "type": "Microsoft.Network/publicIPAddresses",
               "apiVersion": "2015-06-15",
               "name": "[parameters('publicIPAddressName')]",
               "location": "[parameters('location')]",
               "properties": {
                   "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                   "dnsSettings": {
                       "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                   }
               }
           },
           {
               "type": "Microsoft.Network/virtualNetworks",
               "apiVersion": "2015-06-15",
               "name": "[parameters('virtualNetworkName')]",
               "location": "[parameters('location')]",
               "properties": {
                   "addressSpace": {
                       "addressPrefixes": [
                           "[variables('addressPrefix')]"
                       ]
                   },
                   "subnets": [
                       {
                           "name": "[variables('subnet1Name')]",
                           "properties": {
                               "addressPrefix": "[variables('subnet1Prefix')]"
                           }
                       },
                       {
                           "name": "[variables('subnet2Name')]",
                           "properties": {
                               "addressPrefix": "[variables('subnet2Prefix')]"
                           }
                       }
                   ]
               }
           },
           {
               "type": "Microsoft.Network/networkInterfaces",
               "apiVersion": "2015-06-15",
               "name": "[parameters('nicName')]",
               "location": "[parameters('location')]",
               "dependsOn": [
                   "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
                   "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
               ],
               "properties": {
                   "ipConfigurations": [
                       {
                           "name": "ipconfig1",
                           "properties": {
                               "privateIPAllocationMethod": "Dynamic",
                               "publicIPAddress": {
                                   "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                               },
                               "subnet": {
                                   "id": "[variables('subnet1Ref')]"
                               }
                           }
                       }
                   ]
               }
           },
           {
               "type": "Microsoft.Compute/virtualMachines",
               "apiVersion": "2015-06-15",
               "name": "[parameters('vmName')]",
               "location": "[parameters('location')]",
               "dependsOn": [
                   "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
               ],
               "properties": {
                   "hardwareProfile": {
                       "vmSize": "[parameters('vmSize')]"
                   },
                   "osProfile": {
                       "computername": "[parameters('vmName')]",
                       "adminUsername": "[parameters('adminUsername')]",
                       "adminPassword": "[parameters('adminPassword')]"
                   },
                   "storageProfile": {
                       "osDisk": {
                           "name": "[concat(parameters('vmName'),'-osDisk')]",
                           "osType": "[parameters('osType')]",
                           "caching": "ReadWrite",
                           "image": {
                               "uri": "[parameters('vhdUrl')]"
                           },
                           "vhd": {
                               "uri": "[variables('osDiskVhdName')]"
                           },
                           "createOption": "FromImage"
                       }
                   },
                   "networkProfile": {
                       "networkInterfaces": [
                           {
                               "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                           }
                       ]
                   }
               }
           }
       ]
   }
   ```


6. Specificare i valori dei parametri per le pagine delle proprietà di Distribuzione personalizzata.

 **TABELLA 1**

| **ResourceGroupName** | **Nome del gruppo di risorse di Azure esistente. In genere, usare lo stesso RG dell'insieme di credenziali delle chiavi.** |
| --- | --- |
| TemplateFile | Percorso completo del file VHDtoImage.json. |
| userStorageAccountName | Nome dell'account di archiviazione. |
| dnsNameForPublicIP | Nome DNS per l'indirizzo IP pubblico; deve essere minuscolo. |
| subscriptionId | Identificatore della sottoscrizione di Azure. |
| Location | Posizione geografica standard di Azure del gruppo di risorse. |
| vmName | Nome della macchina virtuale. |
| vhdUrl | Indirizzo Web del disco rigido virtuale. |
| vmSize | Dimensione dell'istanza di macchina virtuale. |
| publicIPAddressName | Nome dell'indirizzo IP pubblico. |
| virtualNetworkName | Nome della rete virtuale. |
| nicName | Nome dell'interfaccia di rete per la rete virtuale. |
| adminUserName | Nome utente dell'account amministratore. |
| adminPassword | Password amministratore. |
|

7. Dopo aver immesso questi valori, selezionare Acquisto.

Azure inizierà la distribuzione. Crea una nuova macchina virtuale con il disco rigido virtuale non gestito specificato nel percorso di account di archiviazione indicato. È possibile monitorare lo stato nel portale di Azure selezionando Macchine virtuali nel lato sinistro del portale. Dopo che la macchina virtuale è stata creata, lo stato verrà modificato da Avvio in corso a In esecuzione.

Per la distribuzione di macchine virtuali di seconda generazione, usare questo modello:
 ```json
 {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "userStorageAccountName": {
              "type": "String"
          },
          "userStorageContainerName": {
              "defaultValue": "vhds",
              "type": "String"
          },
          "dnsNameForPublicIP": {
              "type": "String"
          },
          "adminUserName": {
              "defaultValue": "isv",
              "type": "String"
          },
          "adminPassword": {
              "defaultValue": "",
              "type": "SecureString"
          },
          "osType": {
              "defaultValue": "windows",
              "allowedValues": [
                  "windows",
                  "linux"
              ],
              "type": "String"
          },
          "subscriptionId": {
              "type": "String"
          },
          "location": {
              "type": "String"
          },
          "vmSize": {
              "type": "String"
          },
          "publicIPAddressName": {
              "type": "String"
          },
          "vmName": {
              "type": "String"
          },
          "virtualNetworkName": {
              "type": "String"
          },
          "nicName": {
              "type": "String"
          },
          "vhdUrl": {
              "type": "String",
              "metadata": {
                  "description": "VHD Url..."
              }
          }
      },
      "variables": {
          "addressPrefix": "10.0.0.0/16",
          "subnet1Name": "Subnet-1",
          "subnet2Name": "Subnet-2",
          "subnet1Prefix": "10.0.0.0/24",
          "subnet2Prefix": "10.0.1.0/24",
          "publicIPAddressType": "Dynamic",
          "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
          "subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
          "hostDNSNameScriptArgument": "[concat(parameters('dnsNameForPublicIP'),'.',parameters('location'),'.cloudapp.azure.com')]",
          "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
      },
      "resources": [
          {
              "type": "Microsoft.Network/publicIPAddresses",
              "apiVersion": "2015-06-15",
              "name": "[parameters('publicIPAddressName')]",
              "location": "[parameters('location')]",
              "properties": {
                  "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                  "dnsSettings": {
                      "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                  }
              }
          },
          {
              "type": "Microsoft.Network/virtualNetworks",
              "apiVersion": "2015-06-15",
              "name": "[parameters('virtualNetworkName')]",
              "location": "[parameters('location')]",
              "properties": {
                  "addressSpace": {
                      "addressPrefixes": [
                          "[variables('addressPrefix')]"
                      ]
                  },
                  "subnets": [
                      {
                          "name": "[variables('subnet1Name')]",
                          "properties": {
                              "addressPrefix": "[variables('subnet1Prefix')]"
                          }
                      },
                      {
                          "name": "[variables('subnet2Name')]",
                          "properties": {
                              "addressPrefix": "[variables('subnet2Prefix')]"
                          }
                      }
                  ]
              }
          },
          {
              "type": "Microsoft.Network/networkInterfaces",
              "apiVersion": "2015-06-15",
              "name": "[parameters('nicName')]",
              "location": "[parameters('location')]",
              "dependsOn": [
                  "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
                  "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
              ],
              "properties": {
                  "ipConfigurations": [
                      {
                          "name": "ipconfig1",
                          "properties": {
                              "privateIPAllocationMethod": "Dynamic",
                              "publicIPAddress": {
                                  "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                              },
                              "subnet": {
                                  "id": "[variables('subnet1Ref')]"
                              }
                          }
                      }
                  ]
              }
          },
          {
              "type": "Microsoft.Compute/virtualMachines",
              "apiVersion": "2015-06-15",
              "name": "[parameters('vmName')]",
              "location": "[parameters('location')]",
              "dependsOn": [
                  "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
              ],
              "properties": {
                  "hardwareProfile": {
                      "vmSize": "[parameters('vmSize')]"
                  },
                  "osProfile": {
                      "computername": "[parameters('vmName')]",
                      "adminUsername": "[parameters('adminUsername')]",
                      "adminPassword": "[parameters('adminPassword')]"
                  },
                  "storageProfile": {
                      "osDisk": {
                          "name": "[concat(parameters('vmName'),'-osDisk')]",
                          "osType": "[parameters('osType')]",
                          "caching": "ReadWrite",
                          "image": {
                              "uri": "[parameters('vhdUrl')]"
                          },
                          "vhd": {
                              "uri": "[variables('osDiskVhdName')]"
                          },
                          "createOption": "FromImage"
                      }
                  },
                  "networkProfile": {
                      "networkInterfaces": [
                          {
                              "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                          }
                      ]
                  }
              }
          }
      ]
  }
  ```


### <a name="deploy-an-azure-vm-using-powershell"></a>Distribuire una macchina virtuale di Azure con PowerShell

Copiare e modificare lo script seguente per specificare i valori per le variabili `$storageaccount` e `$vhdUrl`. Eseguirlo per creare una risorsa di macchina virtuale di Azure dal disco rigido virtuale generalizzato esistente.

```powershell
# storage account of existing generalized VHD
$storageaccount = "testwinrm11815"
# generalized VHD URL
$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl
$objAzureKeyVaultSecret.Id -vhdUrl "$vhdUrl" -vmSize "Standard\_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

# deploying VM with existing VHD
New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName"
```


## <a name="next-steps"></a>Passaggi successivi

- [Risoluzione dei problemi di certificazione delle VM](azure-vm-create-certification-faq.md)
