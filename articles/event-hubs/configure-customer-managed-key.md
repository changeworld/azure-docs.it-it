---
title: Configurare la propria chiave per crittografare Hub eventi di Azure dati inaltesi
description: Questo articolo fornisce informazioni su come configurare la propria chiave per crittografare le Hub eventi di Azure dati inaltere.
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: e3dd7cb1158294102d9bfe67629c80ae01ccdd17
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775188"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-event-hubs-data-at-rest-by-using-the-azure-portal"></a>Configurare le chiavi gestite dal cliente per crittografare Hub eventi di Azure dati in stato di inquieto usando il portale di Azure
Hub eventi di Azure la crittografia dei dati in stato di inalter con Archiviazione di Azure Service Encryption (Azure SSE). Il servizio Hub eventi usa Archiviazione di Azure per archiviare i dati. Tutti i dati archiviati con Archiviazione di Azure vengono crittografati usando chiavi gestite da Microsoft. Se si usa la propria chiave (detta anche chiave Bring Your Own Key (BYOK) o chiave gestita dal cliente), i dati vengono comunque crittografati usando la chiave gestita da Microsoft, ma anche la chiave gestita da Microsoft verrà crittografata usando la chiave gestita dal cliente. Questa funzionalità consente di creare, ruotare, disabilitare e revocare l'accesso alle chiavi gestite dal cliente usate per crittografare le chiavi gestite da Microsoft. L'abilitazione della funzionalità BYOK è un processo di configurazione una sola volta nello spazio dei nomi.

> [!NOTE]
> - La funzionalità BYOK è supportata dai cluster a tenant singolo dedicati di [Hub](event-hubs-dedicated-overview.md) eventi. Non può essere abilitato per gli spazi dei nomi standard di Hub eventi.
> - La crittografia può essere abilitata solo per spazi dei nomi nuovi o vuoti. Se lo spazio dei nomi contiene hub eventi, l'operazione di crittografia avrà esito negativo.

È possibile usare Azure Key Vault per gestire le chiavi e controllare l'utilizzo delle chiavi. È possibile creare chiavi personalizzate e archiviarle in un insieme di credenziali delle chiavi oppure usare le API Azure Key Vault per generare chiavi. Per altre informazioni sull'insieme di credenziali di Azure, vedere [Cos'è l'insieme di credenziali delle chiavi di Azure?](../key-vault/general/overview.md)

Questo articolo illustra come configurare un insieme di credenziali delle chiavi con chiavi gestite dal cliente usando il portale di Azure. Per informazioni su come creare un insieme di credenziali delle chiavi usando il portale di Azure, vedere Avvio [rapido:](../key-vault/general/quick-create-portal.md)Creare un Azure Key Vault usando il portale di Azure .

> [!IMPORTANT]
> L'uso di chiavi gestite dal cliente con Hub eventi di Azure richiede che l'insieme di credenziali delle chiavi abbia due proprietà obbligatorie configurate. Sono:  **Eliminazione soft e** **Non ripulire**. Queste proprietà sono abilitate per impostazione predefinita quando si crea un nuovo insieme di credenziali delle chiavi nel portale di Azure. Tuttavia, se è necessario abilitare queste proprietà in un insieme di credenziali delle chiavi esistente, è necessario usare PowerShell o l'interfaccia della riga di comando di Azure.

## <a name="enable-customer-managed-keys"></a>Abilitare chiavi gestite dal cliente
Per abilitare le chiavi gestite dal cliente nel portale di Azure, seguire questa procedura:

1. Passare al cluster Hub eventi Dedicato.
1. Selezionare lo spazio dei nomi in cui si vuole abilitare BYOK.
1. Nella pagina **Impostazioni dello** spazio dei nomi di Hub eventi selezionare **Crittografia**. 
1. Selezionare la **crittografia della chiave gestita dal cliente in stato di** inquieto, come illustrato nell'immagine seguente. 

    ![Abilitare la chiave gestita dal cliente](./media/configure-customer-managed-key/enable-customer-managed-key.png)

## <a name="set-up-a-key-vault-with-keys"></a>Configurare un insieme di credenziali delle chiavi con le chiavi
Dopo aver abilitato le chiavi gestite dal cliente, è necessario associare la chiave gestita del cliente allo spazio Hub eventi di Azure spazio dei nomi. Hub eventi supporta solo Azure Key Vault. Se si abilita **l'opzione** Crittografia con chiave gestita dal cliente nella sezione precedente, è necessario importare la chiave in Azure Key Vault. Inoltre, le chiavi devono **avere l'eliminazione soft** e **non ripulire** configurate per la chiave. Queste impostazioni possono essere configurate usando [PowerShell o l'interfaccia](../key-vault/general/key-vault-recovery.md) della [riga di comando.](../key-vault/general/key-vault-recovery.md)

1. Per creare un nuovo insieme di credenziali delle chiavi, Azure Key Vault [guida introduttiva.](../key-vault/general/overview.md) Per altre informazioni sull'importazione di chiavi esistenti, vedere [Informazioni su chiavi, segreti e certificati.](../key-vault/general/about-keys-secrets-certificates.md)
1. Per attivare la protezione da eliminazione e ripulitura quando si crea un insieme di credenziali, usare [il comando az keyvault create.](/cli/azure/keyvault#az_keyvault_create)

    ```azurecli-interactive
    az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Per aggiungere la protezione dall'eliminazione a un insieme di credenziali esistente (in cui è già abilitata l'eliminazione software), usare [il comando az keyvault update.](/cli/azure/keyvault#az_keyvault_update)

    ```azurecli-interactive
    az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Creare chiavi seguendo questa procedura:
    1. Per creare una nuova chiave, selezionare **Genera/Importa** nel menu **Chiavi** in **Impostazioni**.
        
        ![Selezionare il pulsante Genera/Importa](./media/configure-customer-managed-key/select-generate-import.png)
    1. Impostare **Opzioni** su **Genera** e assegnare un nome alla chiave.

        ![Creare una chiave](./media/configure-customer-managed-key/create-key.png) 
    1. È ora possibile selezionare questa chiave da associare allo spazio dei nomi di Hub eventi per la crittografia dall'elenco a discesa. 

        ![Selezionare la chiave dall'insieme di credenziali delle chiavi](./media/configure-customer-managed-key/select-key-from-key-vault.png)
    1. Compilare i dettagli per la chiave e fare clic su **Seleziona**. In questo modo verrà abilitata la crittografia della chiave gestita da Microsoft con la chiave (chiave gestita dal cliente). 


## <a name="rotate-your-encryption-keys"></a>Ruotare le chiavi di crittografia
È possibile ruotare la chiave nell'insieme di credenziali delle chiavi usando il meccanismo di rotazione di Azure Key Vault. È anche possibile impostare le date di attivazione e scadenza per automatizzare la rotazione delle chiavi. Il servizio Hub eventi rileverà le nuove versioni chiave e inizierà a usarle automaticamente.

## <a name="revoke-access-to-keys"></a>Revocare l'accesso alle chiavi
La revoca dell'accesso alle chiavi di crittografia non ripulirà i dati da Hub eventi. Tuttavia, non è possibile accedere ai dati dallo spazio dei nomi di Hub eventi. È possibile revocare la chiave di crittografia tramite criteri di accesso o eliminando la chiave. Per altre informazioni sui criteri di accesso e sulla protezione dell'insieme di credenziali delle chiavi, vedere [Proteggere l'accesso a un insieme di credenziali delle chiavi.](../key-vault/general/security-overview.md)

Una volta revocata la chiave di crittografia, il servizio Hub eventi nello spazio dei nomi crittografato diventerà non utilizzabile. Se l'accesso alla chiave è abilitato o la chiave di eliminazione viene ripristinata, il servizio Hub eventi sceglierà la chiave in modo da poter accedere ai dati dallo spazio dei nomi crittografato di Hub eventi.

## <a name="set-up-diagnostic-logs"></a>Configurare i log di diagnostica 
L'impostazione dei log di diagnostica per gli spazi dei nomi abilitati per BYOK fornisce le informazioni necessarie sulle operazioni. Questi log possono essere abilitati e trasmessi in un secondo momento a un hub eventi o analizzati tramite Log Analytics o trasmessi all'archiviazione per eseguire analisi personalizzate. Per altre informazioni sui log di diagnostica, vedere [Panoramica dei log di diagnostica di Azure.](../azure-monitor/essentials/platform-logs-overview.md)

## <a name="enable-user-logs"></a>Abilitare i log utente
Seguire questa procedura per abilitare i log per le chiavi gestite dal cliente.

1. Nel portale di Azure passare allo spazio dei nomi in cui è abilitata la modalità BYOK.
1. Selezionare **Impostazioni di diagnostica** in **Monitoraggio.**

    ![Selezionare le impostazioni di diagnostica](./media/configure-customer-managed-key/select-diagnostic-settings.png)
1. Selezionare **+Aggiungi impostazione di diagnostica.** 

    ![Selezionare Aggiungi impostazione di diagnostica](./media/configure-customer-managed-key/select-add-diagnostic-setting.png)
1. Specificare un **nome** e selezionare la posizione in cui trasmettere i log.
1. Selezionare **CustomerManagedKeyUserLogs e** **Salva**. Questa azione abilita i log per BYOK nello spazio dei nomi.

    ![Selezionare l'opzione dei log utente della chiave gestita dal cliente](./media/configure-customer-managed-key/select-customer-managed-key-user-logs.png)

## <a name="log-schema"></a>Schema del log 
Tutti i log vengono archiviati in formato JavaScript Object Notation (JSON). Ogni voce ha campi stringa che usano il formato descritto nella tabella seguente. 

| Nome | Descrizione |
| ---- | ----------- | 
| TaskName | Descrizione dell'attività non riuscita. |
| ActivityId | ID interno usato per il rilevamento. |
| category | Definisce la classificazione dell'attività. Ad esempio, se la chiave dell'insieme di credenziali delle chiavi viene disabilitata, si tratta di una categoria di informazioni o se non è possibile annullare il wrapping di una chiave, potrebbe verificarsi un errore. |
| resourceId | ID della risorsa Azure Resource Manager |
| keyVault | Nome completo dell'insieme di credenziali delle chiavi. |
| key | Nome della chiave usato per crittografare lo spazio dei nomi di Hub eventi. |
| version | Versione della chiave in uso. |
| operation | Operazione eseguita sulla chiave nell'insieme di credenziali delle chiavi. Ad esempio, disabilitare/abilitare la chiave, eseguire il wrapping o annullare il wrapping |
| codice | Codice associato all'operazione. Esempio: il codice di errore 404 indica che la chiave non è stata trovata. |
| message | Qualsiasi messaggio di errore associato all'operazione |

Ecco un esempio del log per una chiave gestita dal cliente:

```json
{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111-1111-1111-1111-111111111111",
   "category": "error"
   "resourceId": "/SUBSCRIPTIONS/11111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "1111111111111111111111111111111",
   "operation": "wrapKey",
   "code": "404",
   "message": "Key not found: ehbyok0/111111111111111111111111111111",
}



{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111111111-1111-1111-1111111111111",
   "category": "info"
   "resourceId": "/SUBSCRIPTIONS/111111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "111111111111111111111111111111",
   "operation": "disable" | "restore",
   "code": "",
   "message": "",
}
```

## <a name="use-resource-manager-template-to-enable-encryption"></a>Usare Resource Manager per abilitare la crittografia
In questa sezione viene illustrato come eseguire le attività seguenti usando **Azure Resource Manager modelli .** 

1. Creare uno spazio **dei nomi di Hub eventi** con un'identità del servizio gestita.
2. Creare un insieme **di credenziali delle chiavi** e concedere all'identità del servizio l'accesso all'insieme di credenziali delle chiavi. 
3. Aggiornare lo spazio dei nomi di Hub eventi con le informazioni dell'insieme di credenziali delle chiavi (chiave/valore). 


### <a name="create-an-event-hubs-cluster-and-namespace-with-managed-service-identity"></a>Creare un cluster e uno spazio dei nomi di Hub eventi con l'identità del servizio gestita
Questa sezione illustra come creare uno spazio dei nomi Hub eventi di Azure con l'identità del servizio gestita usando un modello Azure Resource Manager e PowerShell. 

1. Creare un modello Azure Resource Manager per creare uno spazio dei nomi di Hub eventi con un'identità del servizio gestita. Assegnare al file il **nomeCreateEventHubClusterAndNamespace.jsin**: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace to be created in cluster."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.EventHub/clusters",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('clusterName')]",
             "location":"[parameters('location')]",
             "sku":{
                "name":"Dedicated",
                "capacity":1
             }
          },
          {
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             },
             "dependsOn":[
                "[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             ]
          }
       ],
       "outputs":{
          "EventHubNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.EventHub/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. Creare un file di parametri di modello denominato: **CreateEventHubClusterAndNamespaceParams.jsin**. 

    > [!NOTE]
    > Sostituire i valori seguenti: 
    > - `<EventHubsClusterName>` - Nome del cluster di Hub eventi    
    > - `<EventHubsNamespaceName>` - Nome dello spazio dei nomi di Hub eventi
    > - `<Location>` - Posizione dello spazio dei nomi di Hub eventi

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    
    ```
3. Eseguire il comando di PowerShell seguente per distribuire il modello per creare uno spazio dei nomi di Hub eventi. Recuperare quindi l'ID dello spazio dei nomi di Hub eventi per usarlo in un secondo momento. Sostituire `{MyRG}` con il nome del gruppo di risorse prima di eseguire il comando.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateEventHubClusterAndNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateEventHubClusterAndNamespace.json -TemplateParameterFile ./CreateEventHubClusterAndNamespaceParams.json

    $EventHubNamespaceId = $outputs.Outputs["eventHubNamespaceId"].value
    ```
 
### <a name="grant-event-hubs-namespace-identity-access-to-key-vault"></a>Concedere all'identità dello spazio dei nomi di Hub eventi l'accesso all'insieme di credenziali delle chiavi

1. Eseguire il comando seguente per creare un insieme di credenziali delle chiavi con **la protezione dall'eliminazione** e **l'eliminazione soft abilitate.** 

    ```powershell
    New-AzureRmKeyVault -Name {keyVaultName} -ResourceGroupName {RGName}  -Location {location} -EnableSoftDelete -EnablePurgeProtection    
    ```     
    
    OPPURE    
    
    Eseguire il comando seguente per aggiornare un insieme **di credenziali delle chiavi esistente.** Specificare i valori per i nomi del gruppo di risorse e dell'insieme di credenziali delle chiavi prima di eseguire il comando. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Impostare i criteri di accesso dell'insieme di credenziali delle chiavi in modo che l'identità gestita dello spazio dei nomi di Hub eventi possa accedere al valore della chiave nell'insieme di credenziali delle chiavi. Usare l'ID dello spazio dei nomi di Hub eventi della sezione precedente. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $EventHubNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-event-hubs-namespace-with-customer-managed-key-from-key-vault"></a>Crittografare i dati nello spazio dei nomi di Hub eventi con la chiave gestita dal cliente dall'insieme di credenziali delle chiavi
Finora sono stati evasi i passaggi seguenti: 

1. Creazione di uno spazio dei nomi Premium con un'identità gestita.
2. Creare un insieme di credenziali delle chiavi e concedere all'identità gestita l'accesso all'insieme di credenziali delle chiavi. 

In questo passaggio si aggiornerà lo spazio dei nomi di Hub eventi con le informazioni sull'insieme di credenziali delle chiavi. 

1. Creare un file JSON denominato **CreateEventHubClusterAndNamespace.jscon** il contenuto seguente: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace to be created in cluster."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          },
          "keyVaultUri":{
             "type":"string",
             "metadata":{
                "description":"URI of the KeyVault."
             }
          },
          "keyName":{
             "type":"string",
             "metadata":{
                "description":"KeyName."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]",
                "encryption":{
                   "keySource":"Microsoft.KeyVault",
                   "keyVaultProperties":[
                      {
                         "keyName":"[parameters('keyName')]",
                         "keyVaultUri":"[parameters('keyVaultUri')]"
                      }
                   ]
                }
             }
          }
       ]
    }
    ``` 

2. Creare un file di parametri di **modello:UpdateEventHubClusterAndNamespaceParams.jsin**. 

    > [!NOTE]
    > Sostituire i valori seguenti: 
    > - `<EventHubsClusterName>` - Nome del cluster di Hub eventi.        
    > - `<EventHubsNamespaceName>` - Nome dello spazio dei nomi di Hub eventi
    > - `<Location>` - Posizione dello spazio dei nomi di Hub eventi
    > - `<KeyVaultName>` - Nome dell'insieme di credenziali delle chiavi
    > - `<KeyName>` - Nome della chiave nell'insieme di credenziali delle chiavi

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          },
          "keyName":{
             "value":"<KeyName>"
          },
          "keyVaultUri":{
             "value":"https://<KeyVaultName>.vault.azure.net"
          }
       }
    }
    ```             
3. Eseguire il comando di PowerShell seguente per distribuire il Resource Manager modello. Sostituire `{MyRG}` con il nome del gruppo di risorse prima di eseguire il comando . 

    ```powershell
    New-AzResourceGroupDeployment -Name UpdateEventHubNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateEventHubClusterAndNamespace.json -TemplateParameterFile ./UpdateEventHubClusterAndNamespaceParams.json 
    ```

## <a name="troubleshoot"></a>Risolvere problemi
Come procedura consigliata, abilitare sempre i log come illustrato nella sezione precedente. Consente di tenere traccia delle attività quando è abilitata la crittografia BYOK. Consente anche di identificare l'ambito dei problemi.

Di seguito sono riportati i codici di errore comuni da cercare quando è abilitata la crittografia BYOK.

| Azione | Codice di errore | Stato risultante dei dati |
| ------ | ---------- | ----------------------- | 
| Rimuovere l'autorizzazione wrap/unwrap da un insieme di credenziali delle chiavi | 403 |    Inaccessible |
| Rimuovere l'appartenenza al ruolo di AAD da un'entità di sicurezza di AAD che ha concesso l'autorizzazione wrap/unwrap | 403 |  Inaccessible |
| Eliminare una chiave di crittografia dall'insieme di credenziali delle chiavi | 404 | Inaccessible |
| Eliminare l'insieme di credenziali delle chiavi | 404 | Inaccessibile (presuppone che sia abilitata l'eliminazione soft, che è un'impostazione obbligatoria). |
| Modifica del periodo di scadenza della chiave di crittografia in modo che sia già scaduta | 403 |   Inaccessible  |
| Modifica di NBF (non prima) in modo che la chiave di crittografia della chiave non sia attiva | 403 | Inaccessible  |
| Selezione **dell'opzione Consenti servizi MSFT per** il firewall dell'insieme di credenziali delle chiavi o blocco dell'accesso di rete all'insieme di credenziali delle chiavi con la chiave di crittografia | 403 | Inaccessible |
| Spostamento dell'insieme di credenziali delle chiavi in un tenant diverso | 404 | Inaccessible |  
| Problema di rete intermittente o interruzione di DNS/AAD/MSI |  | Accessibile tramite la chiave di crittografia dei dati memorizzati nella cache |

> [!IMPORTANT]
> Per abilitare il ripristino di emergenza geografico in uno spazio dei nomi che usa la crittografia BYOK, lo spazio dei nomi secondario per l'associazione deve essere in un cluster dedicato e deve avere un'identità gestita assegnata dal sistema abilitata. Per altre informazioni, vedere [Identità gestite per le risorse di Azure.](../active-directory/managed-identities-azure-resources/overview.md)

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:
- [Panoramica di Hub eventi](event-hubs-about.md)
- [Key Vault panoramica](../key-vault/general/overview.md)