---
title: Usare i riferimenti di Key Vault
description: Informazioni su come configurare Servizio app di Azure e Funzioni di Azure usare Azure Key Vault riferimenti. Rendere Key Vault segreti disponibili per il codice dell'applicazione.
author: mattchenderson
ms.topic: article
ms.date: 02/05/2021
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: b87001f9b283c774096fe669d58a9b487174625d
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750771"
---
# <a name="use-key-vault-references-for-app-service-and-azure-functions"></a>Usare Key Vault per il servizio app e le Funzioni di Azure

Questo argomento illustra come usare i segreti da Azure Key Vault all'interno dell'applicazione di Servizio app o Funzioni di Azure senza che siano richieste modifiche al codice. [Azure Key Vault](../key-vault/general/overview.md) è un servizio che supporta la gestione centralizzata dei segreti con controllo completo sui criteri di accesso e sulla cronologia di controllo.

## <a name="granting-your-app-access-to-key-vault"></a>Concedere all'app l'accesso a Key Vault

Per leggere i segreti da Key Vault, è necessario avere creato un insieme di credenziali e concedere all'app l'autorizzazione per accedervi.

1. Creare un insieme di credenziali seguendo l'[avvio rapido per Key Vault](../key-vault/secrets/quick-create-cli.md).

1. Creare un'[identità gestita assegnata dal sistema](overview-managed-identity.md) per l'applicazione.

   > [!NOTE] 
   > I riferimenti a Key Vault supportano attualmente solo identità gestite assegnate dal sistema. Non è possibile usare identità assegnate dall'utente.

1. Creare [criteri di accesso in Key Vault](../key-vault/general/security-overview.md#privileged-access) per l'identità di applicazione creata in precedenza. Abilitare l'autorizzazione per il segreto "Get" in questi criteri. Non configurare l'"applicazione autorizzata" o le impostazioni di `applicationId`, poiché questa operazione non è compatibile con un'identità gestita.

### <a name="access-network-restricted-vaults"></a>Accedere agli insiemi di credenziali con restrizioni di rete

> [!NOTE]
> Le applicazioni basate su Linux non sono attualmente in grado di risolvere i segreti da un insieme di credenziali delle chiavi con restrizioni di rete, a meno che l'app non sia ospitata in un [ambiente del servizio app](./environment/intro.md).

Se l'insieme di credenziali è configurato [con restrizioni di rete,](../key-vault/general/overview-vnet-service-endpoints.md)è anche necessario assicurarsi che l'applicazione abbia accesso alla rete.

1. Assicurarsi che l'applicazione abbia funzionalità di rete in uscita configurate, come descritto in Funzionalità di rete del servizio [app](./networking-features.md) e Funzioni di Azure [di rete.](../azure-functions/functions-networking-options.md)

2. Assicurarsi che gli account di configurazione dell'insieme di credenziali per la rete o la subnet tramite cui l'app accederà.

> [!IMPORTANT]
> L'accesso a un insieme di credenziali tramite l'integrazione della rete virtuale non è attualmente compatibile con gli aggiornamenti [automatici per i segreti senza una versione specificata.](#rotation)

## <a name="reference-syntax"></a>Sintassi del riferimento

Un riferimento a Key Vault viene espresso nel formato `@Microsoft.KeyVault({referenceString})`, in cui sostituire `{referenceString}` con una delle opzioni seguenti:

> [!div class="mx-tdBreakAll"]
> | Stringa di riferimento                                                            | Descrizione                                                                                                                                                                                 |
> |-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | SecretUri=_secretUri_                                                       | **SecretUri** deve essere l'URI completo del piano dati di un segreto in Key Vault, includendo facoltativamente una versione, ad esempio `https://myvault.vault.azure.net/secrets/mysecret/` o`https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931`  |
> | VaultName=_vaultName_;SecretName=_secretName_;SecretVersion=_secretVersion_ | VaultName **è** obbligatorio e deve essere il nome della Key Vault risorsa. SecretName **è** obbligatorio e deve essere il nome del segreto di destinazione. SecretVersion **è** facoltativo, ma se presente indica la versione del segreto da usare. |

Un riferimento completo, ad esempio, avrebbe un aspetto simile al seguente:

```
@Microsoft.KeyVault(SecretUri=https://myvault.vault.azure.net/secrets/mysecret/)
```

In alternativa:

```
@Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret)
```

## <a name="rotation"></a>Rotazione

> [!IMPORTANT]
> [L'accesso a un insieme di credenziali tramite l'integrazione](#access-network-restricted-vaults) della rete virtuale non è attualmente compatibile con gli aggiornamenti automatici per i segreti senza una versione specificata.

Se non viene specificata una versione nel riferimento, l'app userà la versione più recente presente in Key Vault. Quando diventano disponibili versioni più recenti, ad esempio con un evento di rotazione, l'app verrà aggiornata automaticamente e inizierà a usare la versione più recente entro un giorno. Eventuali modifiche alla configurazione apportate all'app causeranno un aggiornamento immediato alle versioni più recenti di tutti i segreti a cui si fa riferimento.

## <a name="source-application-settings-from-key-vault"></a>Impostazioni dell'applicazione di origine da Key Vault

Key Vault riferimenti possono essere usati come valori per Impostazioni applicazione [,](configure-common.md#configure-app-settings)consentendo di mantenere i segreti Key Vault anziché la configurazione del sito. Le impostazioni dell'applicazione vengono crittografate in modo sicuro in caso di inquieto, ma se sono necessarie funzionalità di gestione dei segreti, dovrebbero essere Key Vault.

Per usare un riferimento a Key Vault per un'impostazione di applicazione, impostare il riferimento come valore dell'impostazione. L'app può fare riferimento al segreto tramite la relativa chiave come di consueto. Non sono necessarie modifiche al codice.

> [!TIP]
> La maggior parte delle impostazioni di applicazione che usano riferimenti a Key Vault deve essere contrassegnata come impostazioni slot, perché è consigliabile usare insiemi di credenziali separati per ogni ambiente.

### <a name="azure-resource-manager-deployment"></a>Distribuzione Azure Resource Manager

Durante l'automazione delle distribuzioni di risorse tramite modelli di Azure Resource Manager, potrebbe essere necessario disporre in sequenza le dipendenze in un determinato ordine per usufruire di questa funzionalità. Si noti che sarà necessario definire le impostazioni di applicazione come risorsa propria, invece di usare una proprietà `siteConfig` nella definizione del sito. Questo avviene perché il sito deve essere definito per primo, in modo che l'identità assegnata dal sistema venga creata insieme al sito e possa essere usata nei criteri di accesso.

Un esempio di pseudo-modello per un'app per le funzioni potrebbe essere simile al seguente:

```json
{
    //...
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            //...
        },
        {
            "type": "Microsoft.Insights/components",
            "name": "[variables('appInsightsName')]",
            //...
        },
        {
            "type": "Microsoft.Web/sites",
            "name": "[variables('functionAppName')]",
            "identity": {
                "type": "SystemAssigned"
            },
            //...
            "resources": [
                {
                    "type": "config",
                    "name": "appsettings",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('storageConnectionStringName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('appInsightsKeyName'))]"
                    ],
                    "properties": {
                        "AzureWebJobsStorage": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "APPINSIGHTS_INSTRUMENTATIONKEY": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('appInsightsKeyResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_ENABLE_SYNC_UPDATE_SITE": "true"
                        //...
                    }
                },
                {
                    "type": "sourcecontrols",
                    "name": "web",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.Web/sites/config', variables('functionAppName'), 'appsettings')]"
                    ],
                }
            ]
        },
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[variables('keyVaultName')]",
            //...
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]"
            ],
            "properties": {
                //...
                "accessPolicies": [
                    {
                        "tenantId": "[reference(concat('Microsoft.Web/sites/',  variables('functionAppName'), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').tenantId]",
                        "objectId": "[reference(concat('Microsoft.Web/sites/',  variables('functionAppName'), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').principalId]",
                        "permissions": {
                            "secrets": [ "get" ]
                        }
                    }
                ]
            },
            "resources": [
                {
                    "type": "secrets",
                    "name": "[variables('storageConnectionStringName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
                    ],
                    "properties": {
                        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountResourceId'),'2015-05-01-preview').key1)]"
                    }
                },
                {
                    "type": "secrets",
                    "name": "[variables('appInsightsKeyName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
                    ],
                    "properties": {
                        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
                    }
                }
            ]
        }
    ]
}
```

> [!NOTE] 
> In questo esempio, la distribuzione del controllo del codice sorgente dipende dalle impostazioni dell'applicazione. Si tratta in genere di un comportamento non sicuro, dato che l'aggiornamento delle impostazioni dell'applicazione ha un comportamento asincrono. Tuttavia, dato che è stata inclusa l'impostazione dell'applicazione `WEBSITE_ENABLE_SYNC_UPDATE_SITE`, l'aggiornamento è sincrono. Questo significa che la distribuzione del controllo del codice sorgente inizierà solo dopo l'aggiornamento completo delle impostazioni dell'applicazione.

## <a name="troubleshooting-key-vault-references"></a>Risoluzione dei Key Vault riferimenti

Se un riferimento non viene risolto correttamente, verrà usato il valore di riferimento. Ciò significa che per le impostazioni dell'applicazione viene creata una variabile di ambiente il cui valore ha la `@Microsoft.KeyVault(...)` sintassi . Ciò potrebbe causare la generazione di errori da parte dell'applicazione, perché prevedeva un segreto di una determinata struttura.

In genere, ciò è dovuto a una configurazione errata dei criteri Key Vault [di accesso.](#granting-your-app-access-to-key-vault) Tuttavia, potrebbe anche essere dovuto a un segreto non più esistente o a un errore di sintassi nel riferimento stesso.

Se la sintassi è corretta, è possibile visualizzare altre cause di errore controllando lo stato di risoluzione corrente nel portale. Passare a Impostazioni applicazione e selezionare "Modifica" per il riferimento in questione. Sotto la configurazione dell'impostazione dovrebbero essere visualizzate informazioni sullo stato, inclusi gli eventuali errori. L'assenza di questi elementi implica che la sintassi di riferimento non è valida.

È anche possibile usare uno dei rilevatori predefiniti per ottenere informazioni aggiuntive.

### <a name="using-the-detector-for-app-service"></a>Uso del rilevatore per il servizio app

1. Nel portale passare all'app.
2. Selezionare **Diagnostica e risoluzione dei problemi**.
3. Scegliere **Disponibilità e prestazioni e** selezionare App Web in **basso.**
4. Trovare **Key Vault diagnostica delle impostazioni dell'applicazione e** fare clic su Altre **informazioni.**


### <a name="using-the-detector-for-azure-functions"></a>Uso del rilevatore per Funzioni di Azure

1. Nel portale passare all'app.
2. Passare a **Funzionalità della piattaforma.**
3. Selezionare **Diagnostica e risoluzione dei problemi**.
4. Scegliere **Disponibilità e prestazioni e** selezionare App per le funzioni in basso o **segnalazione di errori.**
5. Fare clic su **Key Vault diagnostica delle impostazioni dell'applicazione.**
