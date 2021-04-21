---
title: Configurare la propria chiave per crittografare bus di servizio di Azure dati inaltesi
description: Questo articolo fornisce informazioni su come configurare la propria chiave per crittografare le bus di servizio di Azure dati inaltere.
ms.topic: conceptual
ms.date: 02/10/2021
ms.openlocfilehash: 88de4ccc2c6997622540664dc15b21f052df622a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788588"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-service-bus-data-at-rest-by-using-the-azure-portal"></a>Configurare le chiavi gestite dal cliente per crittografare bus di servizio di Azure dati in stato di inquieto usando il portale di Azure
bus di servizio di Azure Premium fornisce la crittografia dei dati in pausa con Archiviazione di Azure Service Encryption (Azure SSE). Il bus di servizio Premium Archiviazione di Azure per archiviare i dati. Tutti i dati archiviati con Archiviazione di Azure vengono crittografati usando chiavi gestite da Microsoft. Se si usa la propria chiave (detta anche chiave Bring Your Own Key (BYOK) o chiave gestita dal cliente), i dati vengono comunque crittografati usando la chiave gestita da Microsoft, ma anche la chiave gestita da Microsoft verrà crittografata usando la chiave gestita dal cliente. Questa funzionalità consente di creare, ruotare, disabilitare e revocare l'accesso alle chiavi gestite dal cliente usate per crittografare le chiavi gestite da Microsoft. L'abilitazione della funzionalità BYOK è un processo di configurazione una sola volta nello spazio dei nomi.

Esistono alcune avvertenze per la chiave gestita dal cliente per la crittografia lato servizio. 
- Questa funzionalità è supportata dal [bus di servizio di Azure Premium.](service-bus-premium-messaging.md) Non può essere abilitato per gli spazi dei nomi del bus di servizio di livello Standard.
- La crittografia può essere abilitata solo per spazi dei nomi nuovi o vuoti. Se lo spazio dei nomi contiene code o argomenti, l'operazione di crittografia avrà esito negativo.

È possibile usare Azure Key Vault per gestire le chiavi e controllare l'utilizzo delle chiavi. È possibile creare chiavi personalizzate e archiviarle in un insieme di credenziali delle chiavi oppure usare le API Azure Key Vault per generare chiavi. Per altre informazioni sull'insieme di credenziali di Azure, vedere [Cos'è l'insieme di credenziali delle chiavi di Azure?](../key-vault/general/overview.md)

Questo articolo illustra come configurare un insieme di credenziali delle chiavi con chiavi gestite dal cliente usando il portale di Azure. Per informazioni su come creare un insieme di credenziali delle chiavi usando il portale di Azure, vedere Avvio [rapido:](../key-vault/general/quick-create-portal.md)Creare un Azure Key Vault usando il portale di Azure .

> [!IMPORTANT]
> L'uso di chiavi gestite dal cliente con bus di servizio di Azure richiede che l'insieme di credenziali delle chiavi abbia due proprietà obbligatorie configurate. Sono:  **Eliminazione soft e** **Non ripulire**. Queste proprietà sono abilitate per impostazione predefinita quando si crea un nuovo insieme di credenziali delle chiavi nel portale di Azure. Tuttavia, se è necessario abilitare queste proprietà in un insieme di credenziali delle chiavi esistente, è necessario usare PowerShell o l'interfaccia della riga di comando di Azure.

## <a name="enable-customer-managed-keys"></a>Abilitare chiavi gestite dal cliente
Per abilitare le chiavi gestite dal cliente nel portale di Azure, seguire questa procedura:

1. Passare allo spazio dei nomi Premium del bus di servizio.
2. Nella pagina **Impostazioni dello** spazio dei nomi del bus di servizio selezionare **Crittografia**.
3. Selezionare la **crittografia della chiave gestita dal cliente in stato di** inquieto, come illustrato nell'immagine seguente.

    ![Abilitare la chiave gestita dal cliente](./media/configure-customer-managed-key/enable-customer-managed-key.png)


## <a name="set-up-a-key-vault-with-keys"></a>Configurare un insieme di credenziali delle chiavi con le chiavi

Dopo aver abilitato le chiavi gestite dal cliente, è necessario associare la chiave gestita del cliente allo spazio bus di servizio di Azure spazio dei nomi. Il bus di servizio supporta solo Azure Key Vault. Se si abilita **l'opzione** Crittografia con chiave gestita dal cliente nella sezione precedente, è necessario importare la chiave in Azure Key Vault. Inoltre, le chiavi devono **avere l'eliminazione soft** e **non ripulire** configurate per la chiave. Queste impostazioni possono essere configurate tramite [PowerShell o l'interfaccia](../key-vault/general/key-vault-recovery.md) della [riga di comando.](../key-vault/general/key-vault-recovery.md)

1. Per creare un nuovo insieme di credenziali delle chiavi, Azure Key Vault [guida introduttiva.](../key-vault/general/overview.md) Per altre informazioni sull'importazione di chiavi esistenti, vedere [Informazioni su chiavi, segreti e certificati.](../key-vault/general/about-keys-secrets-certificates.md)
1. Per attivare la protezione da eliminazione e ripulitura quando si crea un insieme di credenziali, usare [il comando az keyvault create.](/cli/azure/keyvault#az_keyvault_create)

    ```azurecli-interactive
    az keyvault create --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Per aggiungere la protezione dall'eliminazione a un insieme di credenziali esistente (in cui è già abilitata l'eliminazione software), usare [il comando az keyvault update.](/cli/azure/keyvault#az_keyvault_update)

    ```azurecli-interactive
    az keyvault update --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Creare chiavi seguendo questa procedura:
    1. Per creare una nuova chiave, selezionare **Genera/Importa** nel menu **Chiavi** in **Impostazioni**.
        
        ![Selezionare il pulsante Genera/Importa](./media/configure-customer-managed-key/select-generate-import.png)

    1. Impostare **Opzioni** su **Genera** e assegnare un nome alla chiave.

        ![Creare una chiave](./media/configure-customer-managed-key/create-key.png) 

    1. È ora possibile selezionare questa chiave da associare allo spazio dei nomi del bus di servizio per la crittografia dall'elenco a discesa. 

        ![Selezionare la chiave dall'insieme di credenziali delle chiavi](./media/configure-customer-managed-key/select-key-from-key-vault.png)
        > [!NOTE]
        > Per la ridondanza, è possibile aggiungere fino a 3 chiavi. Nel caso in cui una delle chiavi sia scaduta o non sia accessibile, le altre chiavi verranno usate per la crittografia.
        
    1. Immettere i dettagli per la chiave e fare clic su **Seleziona.** In questo modo verrà abilitata la crittografia della chiave gestita da Microsoft con la chiave (chiave gestita dal cliente). 


    > [!IMPORTANT]
    > Se si sta cercando di usare la chiave gestita dal cliente insieme al ripristino di emergenza geografico, vedere questa sezione. 
    >
    > Per abilitare la crittografia della chiave gestita da [](../key-vault/general/security-overview.md) Microsoft con una chiave gestita dal cliente, vengono impostati criteri di accesso per l'identità gestita del bus di servizio nell'insieme di credenziali delle chiavi di Azure specificato. In questo modo si garantisce l'accesso controllato all'istanza di Azure KeyVault dallo spazio bus di servizio di Azure spazio dei nomi.
    >
    > Per questo:
    > 
    >   * Se [il ripristino di emergenza geografico](service-bus-geo-dr.md) è già abilitato per lo spazio dei nomi del bus di servizio e si desidera abilitare la chiave gestita dal cliente, 
    >     * Interrompere l'associazione
    >     * [Configurare i criteri di accesso per l'identità](../key-vault/general/assign-access-policy-portal.md) gestita per gli spazi dei nomi primario e secondario nell'insieme di credenziali delle chiavi.
    >     * Configurare la crittografia nello spazio dei nomi primario.
    >     * Riassciare gli spazi dei nomi primario e secondario.
    > 
    >   * Se si sta cercando di abilitare il ripristino di emergenza geografico in uno spazio dei nomi del bus di servizio in cui la chiave gestita dal cliente è già impostata, -
    >     * [Configurare i criteri di accesso per l'identità](../key-vault/general/assign-access-policy-portal.md) gestita per lo spazio dei nomi secondario nell'insieme di credenziali delle chiavi.
    >     * Associare gli spazi dei nomi primario e secondario.


## <a name="rotate-your-encryption-keys"></a>Ruotare le chiavi di crittografia

È possibile ruotare la chiave nell'insieme di credenziali delle chiavi usando il meccanismo di rotazione di Azure Key Vault. È anche possibile impostare le date di attivazione e scadenza per automatizzare la rotazione delle chiavi. Il servizio bus di servizio rileverà le nuove versioni chiave e inizierà a usarle automaticamente.

## <a name="revoke-access-to-keys"></a>Revocare l'accesso alle chiavi

La revoca dell'accesso alle chiavi di crittografia non ripulirà i dati dal bus di servizio. Tuttavia, non è possibile accedere ai dati dallo spazio dei nomi del bus di servizio. È possibile revocare la chiave di crittografia tramite i criteri di accesso o eliminando la chiave. Per altre informazioni sui criteri di accesso e sulla protezione dell'insieme di credenziali delle chiavi, vedere [Proteggere l'accesso a un insieme di credenziali delle chiavi.](../key-vault/general/security-overview.md)

Una volta revocata la chiave di crittografia, il servizio del bus di servizio nello spazio dei nomi crittografato diventerà inoperabile. Se l'accesso alla chiave è abilitato o la chiave eliminata viene ripristinata, il servizio del bus di servizio sceglierà la chiave in modo da poter accedere ai dati dallo spazio dei nomi del bus di servizio crittografato.

## <a name="caching-of-keys"></a>Memorizzazione nella cache delle chiavi
L'istanza del bus di servizio esegue il polling delle chiavi di crittografia elencate ogni 5 minuti. Vengono memorizzati nella cache e utilizzati fino al polling successivo, ovvero dopo 5 minuti. Se è disponibile almeno una chiave, le code e gli argomenti sono accessibili. Se tutte le chiavi elencate non sono accessibili durante il polling, tutte le code e gli argomenti non saranno più disponibili. 

Di seguito sono riportate informazioni dettagliate: 

- Ogni 5 minuti, il servizio del bus di servizio esegue il polling di tutte le chiavi gestite dal cliente elencate nel record dello spazio dei nomi:
    - Se una chiave è stata ruotata, il record viene aggiornato con la nuova chiave.
    - Se una chiave è stata revocata, la chiave viene rimossa dal record.
    - Se tutte le chiavi sono state revocate, lo stato di crittografia dello spazio dei nomi è impostato su **Revoked**. Non è possibile accedere ai dati dallo spazio dei nomi del bus di servizio. 
    

## <a name="use-resource-manager-template-to-enable-encryption"></a>Usare Resource Manager modello per abilitare la crittografia
In questa sezione viene illustrato come eseguire le attività seguenti **usando Azure Resource Manager modelli .** 

1. Creare uno **spazio dei nomi del** bus di servizio Premium con **un'identità del servizio gestita.**
2. Creare un insieme **di credenziali delle** chiavi e concedere all'identità del servizio l'accesso all'insieme di credenziali delle chiavi. 
3. Aggiornare lo spazio dei nomi del bus di servizio con le informazioni dell'insieme di credenziali delle chiavi (chiave/valore). 


### <a name="create-a-premium-service-bus-namespace-with-managed-service-identity"></a>Creare uno spazio dei nomi del bus di servizio Premium con l'identità del servizio gestita
Questa sezione illustra come creare uno spazio dei nomi bus di servizio di Azure con l'identità del servizio gestita usando un modello Azure Resource Manager e PowerShell. 

1. Creare un modello Azure Resource Manager per creare uno spazio dei nomi del livello Premium del bus di servizio con un'identità del servizio gestita. Assegnare al file il **nomeCreateServiceBusPremiumNamespace.jsin**: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace."
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
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Premium",
                "tier":"Premium",
                "capacity":1
             },
             "properties":{
    
             }
          }
       ],
       "outputs":{
          "ServiceBusNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.ServiceBus/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. Creare un file di parametri di modello denominato: **CreateServiceBusPremiumNamespaceParams.jsin**. 

    > [!NOTE]
    > Sostituire i valori seguenti: 
    > - `<ServiceBusNamespaceName>` - Nome dello spazio dei nomi del bus di servizio
    > - `<Location>` - Posizione dello spazio dei nomi del bus di servizio

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    ```
3. Eseguire il comando di PowerShell seguente per distribuire il modello per creare uno spazio dei nomi premium del bus di servizio. Recuperare quindi l'ID dello spazio dei nomi del bus di servizio per usarlo in un secondo momento. Sostituire `{MyRG}` con il nome del gruppo di risorse prima di eseguire il comando.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateServiceBusPremiumNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateServiceBusPremiumNamespace.json -TemplateParameterFile ./CreateServiceBusPremiumNamespaceParams.json
    
    $ServiceBusNamespaceId = $outputs.Outputs["serviceBusNamespaceId"].value
    ```
 
### <a name="grant-service-bus-namespace-identity-access-to-key-vault"></a>Concedere all'identità dello spazio dei nomi del bus di servizio l'accesso all'insieme di credenziali delle chiavi

1. Eseguire il comando seguente per creare un insieme di credenziali delle chiavi con **la protezione dall'eliminazione e** **l'eliminazione soft abilitate.** 

    ```powershell
    New-AzureRmKeyVault -Name "{keyVaultName}" -ResourceGroupName {RGName}  -Location "{location}" -EnableSoftDelete -EnablePurgeProtection    
    ```
    
    OPPURE
    
    Eseguire il comando seguente per aggiornare un insieme **di credenziali delle chiavi esistente.** Specificare i valori per i nomi del gruppo di risorse e dell'insieme di credenziali delle chiavi prima di eseguire il comando. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Impostare i criteri di accesso dell'insieme di credenziali delle chiavi in modo che l'identità gestita dello spazio dei nomi del bus di servizio possa accedere al valore della chiave nell'insieme di credenziali delle chiavi. Usare l'ID dello spazio dei nomi del bus di servizio della sezione precedente. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $ServiceBusNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-service-bus-namespace-with-customer-managed-key-from-key-vault"></a>Crittografare i dati nello spazio dei nomi del bus di servizio con la chiave gestita dal cliente dall'insieme di credenziali delle chiavi
Fino a questo punto sono stati evasi i passaggi seguenti: 

1. Creazione di uno spazio dei nomi Premium con un'identità gestita.
2. Creare un insieme di credenziali delle chiavi e concedere all'identità gestita l'accesso all'insieme di credenziali delle chiavi. 

In questo passaggio si aggiornerà lo spazio dei nomi del bus di servizio con le informazioni sull'insieme di credenziali delle chiavi. 

1. Creare un file JSON denominato **UpdateServiceBusNamespaceWithEncryption.jscon** il contenuto seguente: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
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
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Premium",
                "tier":"Premium",
                "capacity":1
             },
             "properties":{
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

2. Creare un file di parametri di **modello:UpdateServiceBusNamespaceWithEncryptionParams.jsin**.

    > [!NOTE]
    > Sostituire i valori seguenti: 
    > - `<ServiceBusNamespaceName>` - Nome dello spazio dei nomi del bus di servizio
    > - `<Location>` - Posizione dello spazio dei nomi del bus di servizio
    > - `<KeyVaultName>` - Nome dell'insieme di credenziali delle chiavi
    > - `<KeyName>` - Nome della chiave nell'insieme di credenziali delle chiavi  

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
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
    New-AzResourceGroupDeployment -Name UpdateServiceBusNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateServiceBusNamespaceWithEncryption.json -TemplateParameterFile ./UpdateServiceBusNamespaceWithEncryptionParams.json
    ```
    

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:
- [Panoramica del bus di servizio](service-bus-messaging-overview.md)
- [Key Vault panoramica](../key-vault/general/overview.md)