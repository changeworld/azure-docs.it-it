---
title: Configurare chiavi gestite dal cliente per l'account Azure Batch con Azure Key Vault e identità gestite
description: Informazioni su come crittografare i dati batch usando chiavi gestite dal cliente.
author: pkshultz
ms.topic: how-to
ms.date: 02/11/2021
ms.author: peshultz
ms.openlocfilehash: d3f10436b95aaeb5eb35a873c2a3862c1492bd47
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100385065"
---
# <a name="configure-customer-managed-keys-for-your-azure-batch-account-with-azure-key-vault-and-managed-identity"></a>Configurare chiavi gestite dal cliente per l'account Azure Batch con Azure Key Vault e identità gestite

Per impostazione predefinita Azure Batch USA chiavi gestite dalla piattaforma per crittografare tutti i dati dei clienti archiviati nel servizio Azure Batch, ad esempio i certificati, i metadati di processi o attività. Facoltativamente, è possibile usare chiavi personalizzate, ovvero chiavi gestite dal cliente, per crittografare i dati archiviati in Azure Batch.

Le chiavi fornite devono essere generate in [Azure Key Vault](../key-vault/general/basic-concepts.md)ed è necessario accedervi con [identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md).

Esistono due tipi di identità gestite: assegnato dal [ *sistema* e *assegnato dall'utente*](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types).

È possibile creare l'account batch con un'identità gestita assegnata dal sistema oppure creare un'identità gestita assegnata dall'utente separata che avrà accesso alle chiavi gestite dal cliente. Esaminare la [tabella di confronto](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) per comprendere le differenze e valutare quale opzione funziona meglio per la soluzione. Se ad esempio si vuole usare la stessa identità gestita per accedere a più risorse di Azure, sarà necessaria un'identità gestita assegnata dall'utente. In caso contrario, potrebbe essere sufficiente un'identità gestita assegnata dal sistema associata all'account batch. L'uso di un'identità gestita assegnata dall'utente offre inoltre la possibilità di applicare chiavi gestite dal cliente durante la creazione dell'account batch, come illustrato [nell'esempio riportato di seguito](#create-a-batch-account-with-user-assigned-managed-identity-and-customer-managed-keys).

## <a name="create-a-batch-account-with-system-assigned-managed-identity"></a>Creare un account batch con identità gestita assegnata dal sistema

Se non è necessaria un'identità gestita separata assegnata dall'utente, è possibile abilitare l'identità gestita assegnata dal sistema quando si crea l'account batch.

### <a name="azure-portal"></a>Portale di Azure

Nel [portale di Azure](https://portal.azure.com/), quando si creano gli account batch, scegliere **sistema assegnato** nel tipo di identità nella scheda **Avanzate** .

![Screenshot di un nuovo account batch con tipo di identità assegnato dal sistema.](./media/batch-customer-managed-key/create-batch-account.png)

Dopo aver creato l'account, è possibile trovare un GUID univoco nel campo **ID entità identità** nella sezione **Proprietà** . Viene visualizzato il **tipo di identità** `System assigned` .

![Screenshot che mostra un GUID univoco nel campo ID entità identità.](./media/batch-customer-managed-key/linked-batch-principal.png)

Questo valore sarà necessario per concedere a questo account batch l'accesso al Key Vault.

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Quando si crea un nuovo account batch, specificare `SystemAssigned` per il `--identity` parametro.

```azurecli
resourceGroupName='myResourceGroup'
accountName='mybatchaccount'

az batch account create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' \
    --identity 'SystemAssigned'
```

Dopo aver creato l'account, è possibile verificare che l'identità gestita assegnata dal sistema sia stata abilitata in questo account. Tenere presente che `PrincipalId` , poiché questo valore sarà necessario per concedere a questo account batch l'accesso al Key Vault.

```azurecli
az batch account show \
    -n $accountName \
    -g $resourceGroupName \
    --query identity
```

> [!NOTE]
> L'identità gestita assegnata dal sistema creata in un account batch viene utilizzata solo per il recupero delle chiavi gestite dal cliente dal Key Vault. Questa identità non è disponibile nei pool di batch. Per usare un'identità gestita assegnata dall'utente in un pool, vedere [configurare le identità gestite nei pool di batch](managed-identity-pools.md).

## <a name="create-a-user-assigned-managed-identity"></a>Creare un'identità gestita assegnata dall'utente

Se si preferisce, è possibile [creare un'identità gestita assegnata dall'utente](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) che può essere usata per accedere alle chiavi gestite dal cliente.

Per poter accedere al Key Vault, sarà necessario il valore dell' **ID client** dell'identità.

## <a name="configure-your-azure-key-vault-instance"></a>Configurare l'istanza di Azure Key Vault

Il Azure Key Vault in cui verranno generate le chiavi deve essere creato nello stesso tenant dell'account batch. Non deve trovarsi nello stesso gruppo di risorse o anche nella stessa sottoscrizione.

### <a name="create-an-azure-key-vault"></a>Creare un Azure Key Vault

Quando si [Crea un'istanza di Azure Key Vault](../key-vault/general/quick-create-portal.md) con chiavi gestite dal cliente per Azure batch, assicurarsi che sia abilitata la protezione **eliminazione** temporanea e **ripulitura** .

![Screenshot della schermata di creazione del Key Vault.](./media/batch-customer-managed-key/create-key-vault.png)

### <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>Aggiungere un criterio di accesso all'istanza di Azure Key Vault

Nel portale di Azure, dopo la creazione del Key Vault, nell' **impostazione** **criteri di accesso** aggiungere l'accesso all'account batch usando identità gestita. In **autorizzazioni chiave** selezionare **Get**, **Wrap Key** e **Unwrap Key**.

![Screenshot che mostra la schermata Aggiungi criteri di accesso.](./media/batch-customer-managed-key/key-permissions.png)

Nel campo **Seleziona** in **principale**, compilare uno dei seguenti elementi:

- Per identità gestita assegnata dal sistema: immettere il `principalId` precedentemente recuperato o il nome dell'account batch.
- Per identità gestita assegnata dall'utente: immettere l' **ID client** recuperato in precedenza o il nome dell'identità gestita assegnata dall'utente.

![Screenshot della schermata principale.](./media/batch-customer-managed-key/principal-id.png)

### <a name="generate-a-key-in-azure-key-vault"></a>Generare una chiave in Azure Key Vault

Nel portale di Azure passare all'istanza di Key Vault nella sezione **chiave** , selezionare **genera/importa**. Selezionare il **tipo di chiave** `RSA` e la **dimensione della chiave RSA** come almeno `2048` bit. `EC` i tipi di chiave non sono attualmente supportati come chiave gestita dal cliente in un account batch.

![Creare una chiave](./media/batch-customer-managed-key/create-key.png)

Dopo aver creato la chiave, fare clic sulla chiave appena creata e sulla versione corrente, copiare l' **identificatore di chiave** nella sezione **Proprietà** .  Assicurarsi che nelle **operazioni consentite**, la chiave a **capo** e la **chiave Unwrap** siano entrambe selezionate.

## <a name="enable-customer-managed-keys-on-a-batch-account"></a>Abilitare le chiavi gestite dal cliente in un account batch

Dopo aver completato i passaggi precedenti, è possibile abilitare le chiavi gestite dal cliente nell'account batch.

### <a name="azure-portal"></a>Portale di Azure

Nella [portale di Azure](https://portal.azure.com/)passare alla pagina account batch. Nella sezione **crittografia** abilitare **chiave gestita dal cliente**. È possibile usare direttamente l'identificatore di chiave oppure è possibile selezionare l'insieme di credenziali delle chiavi e quindi fare clic su selezionare un insieme di credenziali delle **chiavi e una chiave**.

![Screenshot che mostra la sezione e l'opzione di crittografia per abilitare la chiave gestita dal cliente](./media/batch-customer-managed-key/encryption-page.png)

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Dopo che l'account batch è stato creato con l'identità gestita assegnata dal sistema e l'accesso a Key Vault è stato concesso, aggiornare l'account batch con l' `{Key Identifier}` URL in `keyVaultProperties` parametro. Impostare anche **encryption_key_source** come `Microsoft.KeyVault` .

```azurecli
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_source Microsoft.KeyVault \
    --encryption_key_identifier {YourKeyIdentifier} 
```

## <a name="create-a-batch-account-with-user-assigned-managed-identity-and-customer-managed-keys"></a>Creare un account batch con identità gestite assegnate dall'utente e chiavi gestite dal cliente

Con il client batch Management .NET è possibile creare un account batch che avrà un'identità gestita assegnata dall'utente e chiavi gestite dal cliente.

```c#
EncryptionProperties encryptionProperties = new EncryptionProperties()
{
    KeySource = KeySource.MicrosoftKeyVault,
    KeyVaultProperties = new KeyVaultProperties()
    {
        KeyIdentifier = "Your Key Azure Resource Manager Resource ID"
    }
};

BatchAccountIdentity identity = new BatchAccountIdentity()
{
    Type = ResourceIdentityType.UserAssigned,
    UserAssignedIdentities = new Dictionary<string, BatchAccountIdentityUserAssignedIdentitiesValue>
    {
            ["Your Identity Azure Resource Manager ResourceId"] = new BatchAccountIdentityUserAssignedIdentitiesValue()
    }
};
var parameters = new BatchAccountCreateParameters(TestConfiguration.ManagementRegion, encryption:encryptionProperties, identity: identity);

var account = await batchManagementClient.Account.CreateAsync("MyResourceGroup",
    "mynewaccount", parameters); 
```

## <a name="update-the-customer-managed-key-version"></a>Aggiornare la versione della chiave gestita dal cliente

Quando si crea una nuova versione di una chiave, aggiornare l'account batch per usare la nuova versione. A tale scopo, seguire questa procedura:

1. Passare all'account batch in portale di Azure e visualizzare le impostazioni di crittografia.
2. Immettere l'URI per la nuova versione della chiave. In alternativa, è possibile selezionare di nuovo il Key Vault e la chiave per aggiornare la versione.
3. Salvare le modifiche.

È anche possibile usare l'interfaccia della riga di comando di Azure per aggiornare la versione.

```azurecli
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_identifier {YourKeyIdentifierWithNewVersion} 
```

## <a name="use-a-different-key-for-batch-encryption"></a>Usare una chiave diversa per la crittografia batch

Per modificare la chiave usata per la crittografia batch, attenersi alla procedura seguente:

1. Passare all'account batch e visualizzare le impostazioni di crittografia.
2. Immettere l'URI della nuova chiave. In alternativa, è possibile selezionare il Key Vault e scegliere una nuova chiave.
3. Salvare le modifiche.

È anche possibile usare l'interfaccia della riga di comando di Azure per usare una chiave diversa.

```azurecli
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_identifier {YourNewKeyIdentifier} 
```

## <a name="frequently-asked-questions"></a>Domande frequenti

- **Le chiavi gestite dal cliente sono supportate per gli account batch esistenti?** No. Le chiavi gestite dal cliente sono supportate solo per i nuovi account batch.
- **È possibile selezionare dimensioni della chiave RSA maggiori di 2048 bit?** Sì, sono supportate anche le dimensioni delle chiavi RSA di `3072` e `4096` BITS.
- **Quali operazioni sono disponibili dopo la revoca di una chiave gestita dal cliente?** L'unica operazione consentita è l'eliminazione dell'account se il batch perde l'accesso alla chiave gestita dal cliente.
- **Come si ripristina l'accesso all'account batch se si elimina accidentalmente la chiave di Key Vault?** Poiché sono abilitate la protezione dall'eliminazione e l'eliminazione temporanea, è possibile ripristinare le chiavi esistenti. Per ulteriori informazioni, vedere la pagina relativa al [ripristino di un Azure Key Vault](../key-vault/general/key-vault-recovery.md).
- **È possibile disabilitare le chiavi gestite dal cliente?** È possibile impostare di nuovo il tipo di crittografia dell'account batch su "Microsoft Managed Key" in qualsiasi momento. Successivamente, è possibile eliminare o modificare la chiave.
- **Come è possibile ruotare le chiavi?** Le chiavi gestite dal cliente non vengono ruotate automaticamente. Per ruotare la chiave, aggiornare l'identificatore di chiave a cui è associato l'account.
- **Dopo il ripristino dell'accesso, quanto tempo sarà necessario per il corretto funzionamento dell'account batch?** Possono essere necessari fino a 10 minuti prima che l'account sia nuovamente accessibile dopo il ripristino dell'accesso.
- **Mentre l'account batch non è disponibile, cosa accade alle risorse?** Tutti i pool in esecuzione quando l'accesso batch alle chiavi gestite dal cliente viene perso continuerà a essere eseguito. Tuttavia, i nodi passeranno a uno stato non disponibile e le attività smetteranno di funzionare e verranno riaccodate. Una volta ripristinato l'accesso, i nodi diventeranno nuovamente disponibili e le attività verranno riavviate.
- **Questo meccanismo di crittografia si applica ai dischi delle macchine virtuali in un pool di batch?** No. Per i pool di configurazione dei servizi cloud, non viene applicata alcuna crittografia per il sistema operativo e il disco temporaneo. Per i pool di configurazione delle macchine virtuali, il sistema operativo e i dischi dati specificati verranno crittografati con una chiave gestita della piattaforma Microsoft per impostazione predefinita. Attualmente, non è possibile specificare la propria chiave per questi dischi. Per crittografare il disco temporaneo delle macchine virtuali per un pool di batch con una chiave gestita dalla piattaforma Microsoft, è necessario abilitare la proprietà [diskEncryptionConfiguration](/rest/api/batchservice/pool/add#diskencryptionconfiguration) nel pool di [configurazione della macchina virtuale](/rest/api/batchservice/pool/add#virtualmachineconfiguration) . Per gli ambienti altamente sensibili, è consigliabile abilitare la crittografia del disco temporaneo ed evitare l'archiviazione di dati sensibili nei dischi del sistema operativo e dei dati. Per altre informazioni, vedere [creare un pool con crittografia del disco abilitata](./disk-encryption.md)
- **L'identità gestita assegnata dal sistema per l'account batch è disponibile nei nodi di calcolo?** No. L'identità gestita assegnata dal sistema viene attualmente utilizzata solo per accedere alla Azure Key Vault per la chiave gestita dal cliente. Per usare un'identità gestita assegnata dall'utente nei nodi di calcolo, vedere [configurare le identità gestite nei pool di batch](managed-identity-pools.md).

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulle procedure consigliate per la sicurezza sono disponibili [in Azure batch](security-best-practices.md).
- Altre informazioni su[Azure Key Vault](../key-vault/general/basic-concepts.md).
