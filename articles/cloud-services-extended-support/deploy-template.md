---
title: Distribuire un servizio cloud di Azure (supporto esteso)-modelli
description: Distribuire un servizio cloud di Azure (supporto esteso) usando i modelli ARM
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 6cb4abd536cc0d4177df424ac6a774e4e2e328d7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105564756"
---
# <a name="deploy-a-cloud-service-extended-support-using-arm-templates"></a>Distribuire un servizio cloud (supporto esteso) usando i modelli ARM

Questa esercitazione illustra come creare una distribuzione del servizio cloud (supporto esteso) usando i [modelli ARM](../azure-resource-manager/templates/overview.md). 

> [!IMPORTANT]
> Servizi cloud (supporto esteso) è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="before-you-begin"></a>Prima di iniziare

1. Esaminare i [prerequisiti di distribuzione](deploy-prerequisite.md) per i servizi cloud (supporto esteso) e creare le risorse associate.

2. Creare un nuovo gruppo di risorse usando il [portale di Azure](../azure-resource-manager/management/manage-resource-groups-portal.md) o [PowerShell](../azure-resource-manager/management/manage-resource-groups-powershell.md). Questo passaggio è facoltativo se si usa un gruppo di risorse esistente.
 
3. Creare un nuovo account di archiviazione usando il [portale di Azure](../storage/common/storage-account-create.md?tabs=azure-portal) o [PowerShell](../storage/common/storage-account-create.md?tabs=azure-powershell). Questo passaggio è facoltativo se si usa un account di archiviazione esistente.

4. Caricare i file di definizione del servizio (. csdef) e di configurazione del servizio (. cscfg) nell'account di archiviazione usando il [portale di Azure](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob), [AzCopy](../storage/common/storage-use-azcopy-blobs-upload.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) o [PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md#upload-blobs-to-the-container). Ottenere gli URI SAS di entrambi i file da aggiungere al modello ARM più avanti in questa esercitazione.

5. Opzionale Creare un insieme di credenziali delle chiavi e caricare i certificati.

    -  I certificati possono essere collegati ai servizi cloud per consentire la comunicazione sicura da e verso il servizio. Per usare i certificati, le relative identificazioni personali devono essere specificate nel file di configurazione del servizio (con estensione cscfg) e caricate in un insieme di credenziali delle chiavi. È possibile creare un insieme di credenziali delle chiavi tramite il [portale di Azure](../key-vault/general/quick-create-portal.md) o [PowerShell](../key-vault/general/quick-create-powershell.md).
    - L'insieme di credenziali delle chiavi associato deve trovarsi nella stessa area e nella stessa sottoscrizione del servizio cloud.
    - L'insieme di credenziali delle chiavi associato per deve essere abilitato per le autorizzazioni appropriate, in modo che la risorsa servizi cloud (supporto esteso) possa recuperare i certificati da Key Vault. Per ulteriori informazioni, vedere [certificati e Key Vault](certificates-and-key-vault.md)
    - È necessario fare riferimento all'insieme di credenziali delle chiavi nella sezione OsProfile del modello ARM illustrato nei passaggi seguenti.

## <a name="deploy-a-cloud-service-extended-support"></a>Distribuire un servizio cloud (supporto esteso)

> [!NOTE]
> Un modo più semplice e rapido per generare il modello ARM e il file dei parametri è tramite il [portale di Azure](https://portal.azure.com). È possibile [scaricare il modello ARM generato](generate-template-portal.md) tramite il portale per creare il servizio cloud tramite PowerShell
 
1. Creare una rete virtuale. Il nome della rete virtuale deve corrispondere ai riferimenti nel file di configurazione del servizio (con estensione cscfg). Se si usa una rete virtuale esistente, omettere questa sezione dal modello ARM.

    ```json
    "resources": [ 
        { 
          "apiVersion": "2019-08-01", 
          "type": "Microsoft.Network/virtualNetworks", 
          "name": "[parameters('vnetName')]", 
          "location": "[parameters('location')]", 
          "properties": { 
            "addressSpace": { 
              "addressPrefixes": [ 
                "10.0.0.0/16" 
              ] 
            }, 
            "subnets": [ 
              { 
                "name": "WebTier", 
                "properties": { 
                  "addressPrefix": "10.0.0.0/24" 
                } 
              } 
            ] 
          } 
        } 
    ] 
    ```
    
     Se si crea una nuova rete virtuale, aggiungere quanto segue alla `dependsOn` sezione per assicurarsi che la piattaforma crei la rete virtuale prima di creare il servizio cloud.

    ```json
    "dependsOn": [ 
            "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]" 
     ] 
    ```
 
2. Creare un indirizzo IP pubblico e, facoltativamente, impostare la proprietà etichetta DNS dell'indirizzo IP pubblico. Se si usa un indirizzo IP statico, è necessario farvi riferimento come IP riservato nel file di configurazione del servizio (con estensione cscfg). Se si usa un indirizzo IP esistente, ignorare questo passaggio e aggiungere le informazioni sull'indirizzo IP direttamente nelle impostazioni di configurazione del servizio di bilanciamento del carico del modello ARM.
 
    ```json
    "resources": [ 
        { 
          "apiVersion": "2019-08-01", 
          "type": "Microsoft.Network/publicIPAddresses", 
          "name": "[parameters('publicIPName')]", 
          "location": "[parameters('location')]", 
          "properties": { 
            "publicIPAllocationMethod": "Dynamic", 
            "idleTimeoutInMinutes": 10, 
            "publicIPAddressVersion": "IPv4", 
            "dnsSettings": { 
              "domainNameLabel": "[variables('dnsName')]" 
            } 
          }, 
          "sku": { 
            "name": "Basic" 
          } 
        } 
    ] 
    ```
     
     Se si crea un nuovo indirizzo IP, aggiungere quanto segue alla `dependsOn` sezione per assicurarsi che la piattaforma crei l'indirizzo IP prima di creare il servizio cloud.
    
    ```json
    "dependsOn": [ 
            "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]" 
          ] 
    ```
 
3. Creare un oggetto profilo di rete e associare l'indirizzo IP pubblico al front-end del servizio di bilanciamento del carico. La piattaforma crea automaticamente un servizio di bilanciamento del carico.

    ```json
    "networkProfile": { 
        "loadBalancerConfigurations": [ 
          { 
            "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/loadBalancers/', variables('lbName'))]", 
            "name": "[variables('lbName')]", 
            "properties": { 
              "frontendIPConfigurations": [ 
                { 
                  "name": "[variables('lbFEName')]", 
                  "properties": { 
                    "publicIPAddress": { 
                      "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]" 
                    } 
                  } 
                } 
              ] 
            } 
          } 
        ] 
      } 
    ```
 

4. Aggiungere il riferimento all'insieme di credenziali delle chiavi nella  `OsProfile`   sezione del modello ARM. Key Vault viene utilizzato per archiviare i certificati associati ai servizi cloud (supporto esteso). Aggiungere i certificati a Key Vault, quindi fare riferimento alle identificazioni personali del certificato nel file di configurazione del servizio (con estensione cscfg). È anche necessario abilitare Key Vault "criteri di accesso" per "macchine virtuali di Azure per la distribuzione" (nel portale), in modo che la risorsa servizi cloud (supporto esteso) possa recuperare il certificato archiviato come segreto da Key Vault. L'insieme di credenziali delle chiavi deve trovarsi nella stessa area e nella stessa sottoscrizione del servizio cloud e avere un nome univoco. Per ulteriori informazioni, vedere [utilizzo di certificati con servizi cloud (supporto esteso)](certificates-and-key-vault.md).
     
    ```json
    "osProfile": { 
          "secrets": [ 
            { 
              "sourceVault": { 
                "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.KeyVault/vaults/{keyvault-name}" 
              }, 
              "vaultCertificates": [ 
                { 
                  "certificateUrl": "https://{keyvault-name}.vault.azure.net:443/secrets/ContosoCertificate/{secret-id}" 
                } 
              ] 
            } 
          ] 
        } 
    ```
  
    > [!NOTE]
    > SourceVault è l'ID della risorsa ARM per l'insieme di credenziali delle chiavi. È possibile trovare queste informazioni individuando l'ID risorsa nella sezione proprietà dell'insieme di credenziali delle chiavi.
    > - è possibile trovare certificateUrl passando al certificato nell'insieme di credenziali delle chiavi denominato **identificatore del segreto**.  
   >  - il formato di certificateUrl deve essere https:///{Vault-endpoint}/Secrets/{secretname}/{Secret-ID}

5. Creare un profilo del ruolo. Verificare che il numero di ruoli, i nomi di ruolo, il numero di istanze in ogni ruolo e dimensioni siano gli stessi nella sezione configurazione del servizio (. cscfg), definizione del servizio (. csdef) e profilo ruolo nel modello ARM.
    
    ```json
    "roleProfile": {
      "roles": {
        "value": [
          {
            "name": "WebRole1",
            "sku": {
              "name": "Standard_D1_v2",
              "capacity": "1"
            }
          },
          {
            "name": "WorkerRole1",
            "sku": {
              "name": "Standard_D1_v2",
              "capacity": "1"
            } 
          } 
        ]
      }
    }   
    ```

6. Opzionale Creare un profilo di estensione per aggiungere estensioni al servizio cloud. Per questo esempio viene aggiunta l'estensione di diagnostica desktop remoto e Windows Azure.
   > [!Note] 
   > La password per desktop remoto deve avere una lunghezza compresa tra 8-123 caratteri e deve soddisfare almeno 3 dei requisiti di complessità delle password seguenti: 1) contiene un carattere maiuscolo 2) contiene un carattere minuscolo 3) contiene una cifra numerica 4) che contiene un carattere speciale 5) non sono consentiti caratteri di controllo

    ```json
        "extensionProfile": {
          "extensions": [
            {
              "name": "RDPExtension",
              "properties": {
                "autoUpgradeMinorVersion": true,
                "publisher": "Microsoft.Windows.Azure.Extensions",
                "type": "RDP",
                "typeHandlerVersion": "1.2.1",
                "settings": "<PublicConfig>\r\n <UserName>[Insert Username]</UserName>\r\n <Expiration>1/21/2022 12:00:00 AM</Expiration>\r\n</PublicConfig>",
                "protectedSettings": "<PrivateConfig>\r\n <Password>[Insert Password]</Password>\r\n</PrivateConfig>"
              }
            },
            {
              "name": "Microsoft.Insights.VMDiagnosticsSettings_WebRole1",
              "properties": {
                "autoUpgradeMinorVersion": true,
                "publisher": "Microsoft.Azure.Diagnostics",
                "type": "PaaSDiagnostics",
                "typeHandlerVersion": "1.5",
                "settings": "[parameters('wadPublicConfig_WebRole1')]",
                "protectedSettings": "[parameters('wadPrivateConfig_WebRole1')]",
                "rolesAppliedTo": [
                  "WebRole1"
                ]
              }
            }
          ]
        }
    ```

7. Esaminare il modello completo.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "cloudServiceName": {
          "type": "string",
          "metadata": {
            "description": "Name of the cloud service"
          }
        },
        "location": {
          "type": "string",
          "metadata": {
            "description": "Location of the cloud service"
          }
        },
        "deploymentLabel": {
          "type": "string",
          "metadata": {
            "description": "Label of the deployment"
          }
        },
        "packageSasUri": {
          "type": "securestring",
          "metadata": {
            "description": "SAS Uri of the CSPKG file to deploy"
          }
        },
        "configurationSasUri": {
          "type": "securestring",
          "metadata": {
            "description": "SAS Uri of the service configuration (.cscfg)"
          }
        },
        "roles": {
          "type": "array",
          "metadata": {
            "description": "Roles created in the cloud service application"
          }
        },
        "wadPublicConfig_WebRole1": {
          "type": "string",
          "metadata": {
             "description": "Public configuration of Windows Azure Diagnostics extension"
          }
        },
        "wadPrivateConfig_WebRole1": {
          "type": "securestring",
          "metadata": {
            "description": "Private configuration of Windows Azure Diagnostics extension"
          }
        },
        "vnetName": {
          "type": "string",
          "defaultValue": "[concat(parameters('cloudServiceName'), 'VNet')]",
          "metadata": {
            "description": "Name of vitual network"
          }
        },
        "publicIPName": {
          "type": "string",
          "defaultValue": "contosocsIP",
          "metadata": {
            "description": "Name of public IP address"
          }
        },
        "upgradeMode": {
          "type": "string",
          "defaultValue": "Auto",
          "metadata": {
            "UpgradeMode": "UpgradeMode of the CloudService"
          }
        }
      },
      "variables": {
        "cloudServiceName": "[parameters('cloudServiceName')]",
        "subscriptionID": "[subscription().subscriptionId]",
        "dnsName": "[variables('cloudServiceName')]",
        "lbName": "[concat(variables('cloudServiceName'), 'LB')]",
        "lbFEName": "[concat(variables('cloudServiceName'), 'LBFE')]",
        "resourcePrefix": "[concat('/subscriptions/', variables('subscriptionID'), '/resourceGroups/', resourceGroup().name, '/providers/')]"
      },
      "resources": [
        {
          "apiVersion": "2019-08-01",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "[parameters('vnetName')]",
          "location": "[parameters('location')]",
          "properties": {
            "addressSpace": {
              "addressPrefixes": [
                "10.0.0.0/16"
              ]
            },
            "subnets": [
              {
                "name": "WebTier",
                "properties": {
                  "addressPrefix": "10.0.0.0/24"
                }
              }
            ]
          }
        },
        {
          "apiVersion": "2019-08-01",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "[parameters('publicIPName')]",
          "location": "[parameters('location')]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "idleTimeoutInMinutes": 10,
            "publicIPAddressVersion": "IPv4",
            "dnsSettings": {
              "domainNameLabel": "[variables('dnsName')]"
            }
          },
          "sku": {
            "name": "Basic"
          }
        },
        {
          "apiVersion": "2020-10-01-preview",
          "type": "Microsoft.Compute/cloudServices",
          "name": "[variables('cloudServiceName')]",
          "location": "[parameters('location')]",
          "tags": {
            "DeploymentLabel": "[parameters('deploymentLabel')]",
            "DeployFromVisualStudio": "true"
          },
          "dependsOn": [
            "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]",
            "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]"
          ],
          "properties": {
            "packageUrl": "[parameters('packageSasUri')]",
            "configurationUrl": "[parameters('configurationSasUri')]",
            "upgradeMode": "[parameters('upgradeMode')]",
            "roleProfile": {
              "roles": [
                {
                  "name": "WebRole1",
                  "sku": {
                    "name": "Standard_D1_v2",
                    "capacity": "1"
                  }
                },
                {
                  "name": "WorkerRole1",
                  "sku": {
                    "name": "Standard_D1_v2",
                    "capacity": "1"
                  }
                }
              ]
            },
            "networkProfile": {
              "loadBalancerConfigurations": [
                {
                  "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/loadBalancers/', variables('lbName'))]",
                  "name": "[variables('lbName')]",
                  "properties": {
                    "frontendIPConfigurations": [
                      {
                        "name": "[variables('lbFEName')]",
                        "properties": {
                          "publicIPAddress": {
                            "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]"
                          }
                        }
                      }
                    ]
                  }
                }
              ]
            },
            "osProfile": {
              "secrets": [
                {
                  "sourceVault": {
                    "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.KeyVault/vaults/{keyvault-name}"
                  },
                  "vaultCertificates": [
                    {
                      "certificateUrl": "https://{keyvault-name}.vault.azure.net:443/secrets/ContosoCertificate/{secret-id}"
                    }
                  ]
                }
              ]
            },
            "extensionProfile": {
              "extensions": [
                {
                  "name": "RDPExtension",
                  "properties": {
                    "autoUpgradeMinorVersion": true,
                    "publisher": "Microsoft.Windows.Azure.Extensions",
                    "type": "RDP",
                    "typeHandlerVersion": "1.2.1",
                    "settings": "<PublicConfig>\r\n <UserName>[Insert Username]</UserName>\r\n <Expiration>1/21/2022 12:00:00 AM</Expiration>\r\n</PublicConfig>",
                    "protectedSettings": "<PrivateConfig>\r\n <Password>[Insert Password]</Password>\r\n</PrivateConfig>"
                  }
                },
                {
                  "name": "Microsoft.Insights.VMDiagnosticsSettings_WebRole1",
                  "properties": {
                    "autoUpgradeMinorVersion": true,
                    "publisher": "Microsoft.Azure.Diagnostics",
                    "type": "PaaSDiagnostics",
                    "typeHandlerVersion": "1.5",
                    "settings": "[parameters('wadPublicConfig_WebRole1')]",
                    "protectedSettings": "[parameters('wadPrivateConfig_WebRole1')]",
                    "rolesAppliedTo": [
                      "WebRole1"
                  ]
                }
              }
            ]
          }
        }
       }
      ]
    }
    ```

8. Distribuire il modello e il file dei parametri (definendo i parametri nel file di modello) per creare la distribuzione del servizio cloud (supporto esteso). Fare riferimento a questi [modelli di esempio](https://github.com/Azure-Samples/cloud-services-extended-support) come richiesto.

    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName "ContosOrg" -TemplateFile "file path to your template file" -TemplateParameterFile "file path to your parameter file"
    ```

## <a name="next-steps"></a>Passaggi successivi 

- Esaminare le [domande frequenti](faq.md) per i servizi cloud (supporto esteso).
- Distribuire un servizio cloud (supporto esteso) usando il [portale di Azure](deploy-portal.md), [PowerShell](deploy-powershell.md), il [modello](deploy-template.md) o [Visual Studio](deploy-visual-studio.md).
- Visitare il [repository di esempi di servizi cloud (supporto esteso)](https://github.com/Azure-Samples/cloud-services-extended-support)