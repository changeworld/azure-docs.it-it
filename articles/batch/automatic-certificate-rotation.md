---
title: Abilitare la rotazione automatica del certificato in un pool di batch
description: È possibile creare un pool di batch con un'identità gestita e un certificato che verrà rinnovato automaticamente.
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: references_regions
ms.openlocfilehash: e8bea49b2980deb8f20258ab7ea5619ece8cd2bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104962510"
---
# <a name="enable-automatic-certificate-rotation-in-a-batch-pool"></a>Abilitare la rotazione automatica del certificato in un pool di batch

 È possibile creare un pool di batch con un certificato che verrà rinnovato automaticamente. A tale scopo, è necessario creare il pool con un' [identità gestita assegnata dall'utente](managed-identity-pools.md) che avrà accesso al certificato in [Azure Key Vault](../key-vault/general/overview.md).

> [!IMPORTANT]
> Il supporto per i pool di Azure Batch con identità gestite assegnate dall'utente è attualmente disponibile in anteprima pubblica per le aree seguenti: Stati Uniti occidentali 2, Stati Uniti centro-meridionali, Stati Uniti orientali, US Gov Arizona e US Gov Virginia.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-user-assigned-identity"></a>Creare un'identità assegnata dall'utente

Per prima cosa, [creare l'identità gestita assegnata dall'utente](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) nello stesso tenant dell'account batch. Questa identità gestita non deve trovarsi nello stesso gruppo di risorse o anche nella stessa sottoscrizione.

Assicurarsi di annotare l' **ID client** dell'identità gestita assegnata dall'utente. Questo valore sarà necessario in seguito.

:::image type="content" source="media/automatic-certificate-rotation/client-id.png" alt-text="Screenshot che mostra l'ID client di un'identità gestita assegnata dall'utente nell'portale di Azure.":::

## <a name="create-your-certificate"></a>Creare il certificato

Successivamente, è necessario creare un certificato e aggiungerlo a Azure Key Vault. Se non è già stato creato un insieme di credenziali delle chiavi, è necessario eseguire prima questa operazione. Per istruzioni, vedere [Guida introduttiva: impostare e recuperare un certificato da Azure Key Vault usando il portale di Azure](../key-vault/certificates/quick-create-portal.md).

Quando si crea il certificato, assicurarsi di impostare il **tipo di azione Lifetime** per il rinnovo automatico e specificare il numero di giorni dopo i quali il certificato deve essere rinnovato.

:::image type="content" source="media/automatic-certificate-rotation/certificate.png" alt-text="Screenshot della schermata di creazione del certificato nel portale di Azure.":::

Una volta creato il certificato, prendere nota del relativo **identificatore di segreto**. Questo valore sarà necessario in seguito.

:::image type="content" source="media/automatic-certificate-rotation/secret-identifier.png" alt-text="Screenshot che mostra l'identificatore del segreto di un certificato.":::

## <a name="add-an-access-policy-in-azure-key-vault"></a>Aggiungere un criterio di accesso in Azure Key Vault

Nell'insieme di credenziali delle chiavi assegnare un criterio di accesso Key Vault che consenta all'identità gestita assegnata dall'utente di accedere a segreti e certificati. Per istruzioni dettagliate, vedere [assegnare un criterio di accesso key Vault usando il portale di Azure](../key-vault/general/assign-access-policy-portal.md).

## <a name="create-a-batch-pool-with-a-user-assigned-managed-identity"></a>Creare un pool di batch con un'identità gestita assegnata dall'utente

Creare un pool di batch con l'identità gestita usando la [libreria di gestione batch .NET](/dotnet/api/overview/azure/batch#management-library). Per altre informazioni, vedere [configurare le identità gestite nei pool di batch](managed-identity-pools.md).

Nell'esempio seguente viene usata l'API REST di gestione di batch per creare un pool. Assicurarsi di usare l' **identificatore del segreto** del certificato per `observedCertificates` e l' **ID client** dell'identità gestita per `msiClientId` , sostituendo i dati di esempio riportati di seguito.

URI DELL'API REST

```http
PUT https://management.azure.com/subscriptions/<subscriptionid>/resourceGroups/<resourcegroupName>/providers/Microsoft.Batch/batchAccounts/<batchaccountname>/pools/<poolname>?api-version=2021-01-01
```

Request Body

```json
{
    "name": "test2",
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
                        "name": "KVExtensions",
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
                                    "https://testkvwestus2s.vault.azure.net/secrets/authcertforumatesting/8f5f3f491afd48cb99286ba2aacd39af"
                                ]
                            },
                            "authenticationSettings": {
                                "msiEndpoint": "http://169.254.169.254/metadata/identity",
                                "msiClientId": "b9f6dd56-d2d6-4967-99d7-8062d56fd84c"
                            }  }, "protectedSettings":{}
                    }                ]            }
        },
        "scaleSettings": {
            "fixedScale": {
                "targetDedicatedNodes": 1,
                "resizeTimeout": "PT15M"
            }
        },
    },
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "/subscriptions/042998e4-36dc-4b7d-8ce3-a7a2c4877d33/resourceGroups/ACR/providers/Microsoft.ManagedIdentity/userAssignedIdentities/testumaforpools": {}
        }
    }
}

```

## <a name="validate-the-certificate"></a>Verificare il certificato

Per confermare che il certificato è stato distribuito correttamente, accedere al nodo di calcolo. L'output dovrebbe essere simile al seguente:

```
root@74773db5fe1b42ab9a4b6cf679d929da000000:/var/lib/waagent/Microsoft.Azure.KeyVault.KeyVaultForLinux-1.0.1363.13/status# cat 1.status
[{"status":{"code":0,"formattedMessage":{"lang":"en","message":"Successfully started Key Vault extension service. 2021-03-03T23:12:23Z"},"operation":"Service start.","status":"success"},"timestampUTC":"2021-03-03T23:12:23Z","version":"1.0"}]root@74773db5fe1b42ab9a4b6cf679d929da000000:/var/lib/waagent/Microsoft.Azure.KeyVault.KeyVaultForLinux-1.0.1363.13/status#
```

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulle [identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md).
- Informazioni su come usare [chiavi gestite dal cliente con identità gestite dall'utente](batch-customer-managed-key.md).
