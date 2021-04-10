---
title: Usare le estensioni con i pool di batch
description: Le estensioni sono piccole applicazioni che facilitano la configurazione e la configurazione post-provisioning nei nodi di calcolo di batch.
ms.topic: how-to
ms.date: 02/10/2021
ms.openlocfilehash: 1bf9847af57347c143ee3d790d89988ba7cd48e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100417448"
---
# <a name="use-extensions-with-batch-pools"></a>Usare le estensioni con i pool di batch

Le estensioni sono piccole applicazioni che facilitano la configurazione e la configurazione post-provisioning nei nodi di calcolo di batch. È possibile selezionare tutte le estensioni consentite da Azure Batch e installarle nei nodi di calcolo quando ne viene eseguito il provisioning. Successivamente, l'estensione può eseguire l'operazione desiderata.

È possibile controllare lo stato in tempo reale delle estensioni usate e recuperare le informazioni che restituiscono per poter eseguire le funzionalità di rilevamento, correzione o diagnostica.

## <a name="prerequisites"></a>Prerequisiti

- I pool con estensioni devono usare la [configurazione della macchina virtuale](nodes-and-pools.md#virtual-machine-configuration).
- Il tipo di estensione CustomScript è riservato per il servizio Azure Batch e non può essere sottoposto a override.

### <a name="supported-extensions"></a>Estensioni supportate

Al momento della creazione di un pool di batch, è possibile installare le estensioni seguenti. 

- Estensione Azure Key Vault per [Linux](../virtual-machines/extensions/key-vault-linux.md) e [Windows](../virtual-machines/extensions/key-vault-windows.md)
- Estensione di monitoraggio e log Analytics per [Linux](../virtual-machines/extensions/oms-linux.md) e [Windows](../virtual-machines/extensions/oms-windows.md)
- Pacchetto di sicurezza di Azure

È possibile richiedere supporto per altri editori e/o tipi di estensione aprendo una richiesta di supporto.

## <a name="create-a-pool-with-extensions"></a>Creare un pool con le estensioni

Nell'esempio seguente viene creato un pool di batch di nodi Linux che usa l'estensione Azure Key Vault.

URI DELL'API REST

```http
 PUT https://management.azure.com/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Batch/batchAccounts/<batchaccountName>/pools/<batchpoolName>?api-version=2021-01-01
```

Request Body

```json
{
    "name": "test1",
    "type": "Microsoft.Batch/batchAccounts/pools",
    "properties": {
        "vmSize": "STANDARD_DS2_V2",
        "taskSchedulingPolicy": {
            "nodeFillType": "Pack"
        },
        "deploymentConfiguration": {
            "virtualMachineConfiguration": {
                "imageReference": {
                    "publisher": "canonical",
                    "offer": "ubuntuserver",
                    "sku": "18.04-lts",
                    "version": "latest"
                },
                "nodeAgentSkuId": "batch.node.ubuntu 18.04",
                "extensions": [
                    {
                        "name": "secretext",
                        "type": "KeyVaultForLinux",
                        "publisher": "Microsoft.Azure.KeyVault",
                        "typeHandlerVersion": "1.0",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "secretsManagementSettings": {
                                "pollingIntervalInS": "300",
                                "certificateStoreLocation": "/var/lib/waagent/Microsoft.Azure.KeyVault",
                                "requireInitialSync": true,
                                "observedCertificates": [
                                    "https://testkvwestus2.vault.azure.net/secrets/authsecreat"
                                ]
                            },
                            "authenticationSettings": {
                                "msiEndpoint": "http://169.254.169.254/metadata/identity",
                                "msiClientId": "885b1a3d-f13c-4030-afcf-9f05044d78dc"
                            }
                        },
                        "protectedSettings":{}
                    }
                ]
            }
        },
        "scaleSettings": {
            "fixedScale": {
                "targetDedicatedNodes": 1,
                "targetLowPriorityNodes": 0,
                "resizeTimeout": "PT15M"
            }
        }
```

## <a name="get-extension-data-from-a-pool"></a>Ottenere i dati dell'estensione da un pool

Nell'esempio seguente vengono recuperati i dati dall'estensione Azure Key Vault.

URI DELL'API REST

```http
 GET https://<accountname>.<region>.batch.azure.com/pools/test3/nodes/tvmps_a3ce79db285d6c124399c5bd3f3cf308d652c89675d9f1f14bfc184476525278_d/extensions/secretext?api-version=2010-01-01
```

Corpo della risposta

```json
{
  "odata.metadata":"https://testwestus2batch.westus2.batch.azure.com/$metadata#extensions/@Element","instanceView":{
    "name":"secretext","statuses":[
      {
        "code":"ProvisioningState/succeeded","level":0,"displayStatus":"Provisioning succeeded","message":"Successfully started Key Vault extension service. 2021-02-08T19:49:39Z"
      }
    ]
  },"vmExtension":{
    "name":"KVExtensions","publisher":"Microsoft.Azure.KeyVault","type":"KeyVaultForLinux","typeHandlerVersion":"1.0","autoUpgradeMinorVersion":true,"settings":"{\r\n  \"secretsManagementSettings\": {\r\n    \"pollingIntervalInS\": \"300\",\r\n    \"certificateStoreLocation\": \"/var/lib/waagent/Microsoft.Azure.KeyVault\",\r\n    \"requireInitialSync\": true,\r\n    \"observedCertificates\": [\r\n      \"https://testkvwestus2.vault.azure.net/secrets/testumi\"\r\n    ]\r\n  },\r\n  \"authenticationSettings\": {\r\n    \"msiEndpoint\": \"http://169.254.169.254/metadata/identity\",\r\n    \"msiClientId\": \"885b1a3d-f13c-4030-afcf-922f05044d78dc\"\r\n  }\r\n}"
  }
}

```

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sui vari modi per [copiare le applicazioni e i dati nei nodi del pool](batch-applications-to-pool-nodes.md).
- Altre informazioni sull'uso di [nodi e pool](nodes-and-pools.md).
