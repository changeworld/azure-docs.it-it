---
title: Crittografia in stato di inquieto con chiavi gestite dal cliente
titleSuffix: Azure Cognitive Search
description: Integrare la crittografia lato server su indici e mappe sinonimi Ricerca cognitiva di Azure usando le chiavi create e gestite in Azure Key Vault.
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/02/2020
ms.custom: references_regions
ms.openlocfilehash: a4c06cf13b3988891f3c4b45c96f7153b3014ce0
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752157"
---
# <a name="configure-customer-managed-keys-for-data-encryption-in-azure-cognitive-search"></a>Configurare le chiavi gestite dal cliente per la crittografia dei dati in Ricerca cognitiva di Azure

Ricerca cognitiva di Azure crittografa automaticamente il contenuto indicizzato in pausa con [chiavi gestite dal servizio.](../security/fundamentals/encryption-atrest.md#azure-encryption-at-rest-components) Se è necessaria una maggiore protezione, è possibile integrare la crittografia predefinita con un livello di crittografia aggiuntivo usando le chiavi create e gestite in Azure Key Vault. Questo articolo illustra i passaggi per configurare la crittografia delle chiavi gestita dal cliente.

La crittografia della chiave gestita dal cliente dipende da [Azure Key Vault](../key-vault/general/overview.md). È possibile creare chiavi di crittografia personalizzate e archiviarle in un insieme di credenziali delle chiavi oppure usare le API di Azure Key Vault per generare chiavi di crittografia. Con Azure Key Vault, è anche possibile controllare l'utilizzo delle chiavi se si [abilita la registrazione di](../key-vault/general/logging.md).  

La crittografia con chiavi gestite dal cliente viene applicata a singoli indici o mappe sinonimi quando vengono creati tali oggetti e non viene specificata a livello di servizio di ricerca. È possibile crittografare solo nuovi oggetti. Non è possibile crittografare il contenuto già esistente.

Le chiavi non devono essere tutte nello stesso insieme di credenziali delle chiavi. Un singolo servizio di ricerca può ospitare più indici crittografati o mappe sinonimi, ognuna crittografata con le proprie chiavi di crittografia gestite dal cliente, archiviate in insiemi di credenziali delle chiavi diversi. È anche possibile avere indici e mappe sinonimi nello stesso servizio che non vengono crittografati usando chiavi gestite dal cliente.

>[!Important]
> Se si implementano chiavi gestite dal cliente, assicurarsi di seguire procedure rigorose durante la rotazione di routine delle chiavi dell'insieme di credenziali delle chiavi e dei segreti dell'applicazione Active Directory e della registrazione. Aggiornare sempre tutto il contenuto crittografato in modo da usare nuovi segreti e chiavi prima di eliminare quelli precedenti. Se si perde questo passaggio, il contenuto non può essere decrittografato.

## <a name="double-encryption"></a>Crittografia doppia

Per i servizi creati dopo il 1° agosto 2020 e in aree specifiche, l'ambito della crittografia delle chiavi gestite dal cliente include dischi temporanei, ottenendo la doppia crittografia [completa,](search-security-overview.md#double-encryption)attualmente disponibile in queste aree: 

+ West US 2
+ Stati Uniti orientali
+ Stati Uniti centro-meridionali
+ US Gov Virginia
+ US Gov Arizona

Se si usa un'area diversa o un servizio creato prima del 1° agosto, la crittografia della chiave gestita è limitata solo al disco dati, esclusi i dischi temporanei usati dal servizio.

## <a name="prerequisites"></a>Prerequisiti

In questo scenario vengono usati gli strumenti e i servizi seguenti.

+ [Ricerca cognitiva di Azure](search-create-service-portal.md) in un [livello fatturabile](search-sku-tier.md#tier-descriptions) (Basic o superiore, in qualsiasi area).
+ [Azure Key Vault](../key-vault/general/overview.md), è possibile creare un insieme di credenziali delle chiavi [usando portale di Azure](../key-vault//general/quick-create-portal.md), l'interfaccia della riga [di comando](../key-vault//general/quick-create-cli.md)di Azure [o Azure PowerShell](../key-vault//general/quick-create-powershell.md). nella stessa sottoscrizione di Ricerca cognitiva di Azure. Nell'insieme di credenziali delle chiavi **deve essere abilitata l'eliminazione** e **la protezione dall'eliminazione.**
+ [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md). Se non se ne ha uno, [configurare un nuovo tenant](../active-directory/develop/quickstart-create-new-tenant.md).

È necessario disporre di un'applicazione di ricerca in grado di creare l'oggetto crittografato. In questo codice si fa riferimento a una chiave dell'insieme di credenziali delle chiavi e alle informazioni di registrazione di Active Directory. Questo codice può essere un'app funzionante o un prototipo di codice, ad esempio l'esempio di codice [C# DotNetHowToEncryptionUsingCMK.](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)

> [!TIP]
> È possibile usare [Postman](search-get-started-rest.md), [Visual Studio Code](search-get-started-vs-code.md)o [Azure PowerShell](./search-get-started-powershell.md)per chiamare le API REST che creano indici e mappe sinonimi che includono un parametro della chiave di crittografia. Al momento non è disponibile il supporto del portale per l'aggiunta di una chiave agli indici o alle mappe sinonimi.

## <a name="1---enable-key-recovery"></a>1 - Abilitare il ripristino delle chiavi

A causa della natura della crittografia con chiavi gestite dal cliente, nessuno può recuperare i dati se la chiave dell'insieme di credenziali delle chiavi di Azure viene eliminata. Per evitare la perdita di dati causata da eliminazioni accidentali Key Vault chiave, è necessario che nell'insieme di credenziali delle chiavi siano abilitate l'eliminazione e la protezione dall'eliminazione. L'eliminazione temporaneamente è abilitata per impostazione predefinita, pertanto si verificano problemi solo se è stata disabilitata appositamente. La protezione dall'eliminazione non è abilitata per impostazione predefinita, ma è necessaria per la crittografia della chiave gestita dal cliente in Ricerca cognitiva. Per altre informazioni, vedere [Panoramica della protezione dall'eliminazione](../key-vault/general/soft-delete-overview.md) e dall'eliminazione. [](../key-vault/general/soft-delete-overview.md#purge-protection)

È possibile impostare entrambe le proprietà usando il portale, PowerShell o i comandi dell'interfaccia della riga di comando di Azure.

### <a name="using-azure-portal"></a>Uso del portale di Azure

1. [Accedere a portale di Azure](https://portal.azure.com) aprire la pagina di panoramica dell'insieme di credenziali delle chiavi.

1. Nella pagina **Panoramica** in **Informazioni di base** abilitare la protezione eliminazione **e** **ripulitura.**

### <a name="using-powershell"></a>Uso di PowerShell

1. Eseguire `Connect-AzAccount` per configurare le credenziali di Azure.

1. Eseguire il comando seguente per connettersi all'insieme di credenziali delle chiavi, sostituendo `<vault_name>` con un nome valido:

   ```powershell
   $resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId
   ```

1. Azure Key Vault viene creato con l'eliminazione soft abilitata. Se è disabilitato nell'insieme di credenziali, eseguire il comando seguente:

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'
   ```

1. Abilitare la protezione da ripulitura:

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'
   ```

1. Salvare gli aggiornamenti:

   ```powershell
   Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
   ```

### <a name="using-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure

+ Se si dispone di [un'installazione dell'interfaccia della](/cli/azure/install-azure-cli)riga di comando di Azure, è possibile eseguire il comando seguente per abilitare le proprietà necessarie.

   ```azurecli-interactive
   az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
   ```

## <a name="2---create-a-key-in-key-vault"></a>2 - Creare una chiave in Key Vault

Ignorare questo passaggio se è già presente una chiave Azure Key Vault.

1. [Accedere a portale di Azure](https://portal.azure.com) aprire la pagina di panoramica dell'insieme di credenziali delle chiavi.

1. Selezionare **Chiavi** a sinistra e quindi **selezionare + Genera/Importa**.

1. **Nell'elenco Opzioni del** riquadro Crea una chiave scegliere il metodo da usare per creare una chiave. È possibile **generare** una nuova chiave, **caricare** una chiave esistente o usare Ripristina **backup** per selezionare un backup di una chiave.

1. Immettere un **Nome** per la chiave e, facoltativamente, selezionare altre proprietà della chiave.

1. Selezionare **Crea** per avviare la distribuzione.

1. Prendere nota dell'identificatore di chiave: è costituito dal valore della chiave **URI**, dal nome della chiave e dalla **versione della chiave**. Sarà necessario l'identificatore per definire un indice crittografato in Ricerca cognitiva di Azure.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-key-identifier.png" alt-text="Creare una nuova chiave dell'insieme di credenziali delle chiavi":::

## <a name="3---register-an-app-in-active-directory"></a>3 - Registrare un'app in Active Directory

1. In [portale di Azure](https://portal.azure.com)trovare la risorsa Azure Active Directory per la sottoscrizione.

1. A sinistra, in **Gestisci,** **selezionare** Registrazioni app e quindi selezionare **Nuova registrazione.**

1. Assegnare un nome alla registrazione, ad esempio un nome simile al nome dell'applicazione di ricerca. Selezionare **Registra**.

1. Dopo aver creato la registrazione dell'app, copiare l'ID applicazione. È necessario fornire questa stringa all'applicazione. 

   Se si sta esplorando [DotNetHowToEncryptionUsingCMK,](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)incollare questo valore nelappsettings.js **nel** file .

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-id.png" alt-text="ID applicazione nella sezione Informazioni di base":::

1. Selezionare quindi **Certificati per & segreti** a sinistra.

1. Selezionare **Nuovo segreto client**. Assegnare un nome visualizzato al segreto e selezionare **Aggiungi.**

1. Copiare il segreto dell'applicazione. Se si sta esplorando l'esempio, incollare questo valore nelappsettings.js **nel** file .

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-secret.png" alt-text="Segreto dell'applicazione":::

## <a name="4---grant-key-access-permissions"></a>4 - Concedere le autorizzazioni di accesso alla chiave

In questo passaggio verranno creati criteri di accesso in Key Vault. Questo criterio concede all'applicazione registrata con Active Directory l'autorizzazione per usare la chiave gestita dal cliente.

Le autorizzazioni di accesso possono essere revocate in qualsiasi momento. Una volta revocato, qualsiasi indice del servizio di ricerca o mappa di sinonimi che usa tale insieme di credenziali delle chiavi diventerà inutilizzabile. Il ripristino delle autorizzazioni di accesso dell'insieme di credenziali delle chiavi in un secondo momento consente di ripristinare l'accesso alla mappa indice/sinonimo. Per altre informazioni, vedere Proteggere [l'accesso a un insieme di credenziali delle chiavi.](../key-vault/general/security-overview.md)

1. Sempre nel portale di Azure aprire la pagina Panoramica dell'insieme **di credenziali delle** chiavi. 

1. Selezionare i **criteri di accesso** a sinistra e selezionare + Aggiungi criteri di **accesso**.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-add-access-policy.png" alt-text="Aggiungere nuovi criteri di accesso dell'insieme di credenziali delle chiavi":::

1. Scegliere **Seleziona entità** e selezionare l'applicazione registrata in Active Directory. È possibile cercarlo in base al nome.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-access-policy-permissions.png" alt-text="Selezionare l'entità criteri di accesso dell'insieme di credenziali delle chiavi":::

1. In **Autorizzazioni chiave** scegliere *Ottieni*, Annulla il wrapping *della chiave* e Wrap *Key*.

1. In **Autorizzazioni segrete** selezionare *Ottieni*.

1. In **Autorizzazioni certificato** selezionare *Ottieni*.

1. Selezionare **Aggiungi** e quindi **Salva**.

> [!Important]
> Il contenuto crittografato Ricerca cognitiva di Azure configurato per l'uso di una chiave Azure Key Vault con una versione **specifica.** Se si modifica la chiave o la versione, è necessario aggiornare l'indice  o la mappa sinonimi per usare la nuova chiave\versione prima di eliminare la chiave o la versione precedente. In caso contrario, l'indice o la mappa dei sinonimi non sarà utilizzabile, in non sarà possibile decrittografare il contenuto dopo la perdita dell'accesso alla chiave.

<a name="encrypt-content"></a>

## <a name="5---encrypt-content"></a>5 - Crittografare il contenuto

Per aggiungere una chiave gestita dal cliente su un indice, un'origine dati, un set di competenze, un indicizzatore o una mappa sinonimi, è necessario usare [l'API REST](/rest/api/searchservice/) di ricerca o un SDK. Il portale non espone mappe sinonimi o proprietà di crittografia. Quando si usa un indice API valido, le origini dati, i set di competenze, gli indicizzatori e le mappe sinonimi supportano una proprietà **encryptionKey di primo livello.**

Questo esempio usa l'API REST, con i valori per Azure Key Vault e Azure Active Directory:

```json
{
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

> [!Note]
> Nessuno di questi dettagli dell'insieme di credenziali delle chiavi viene considerato segreto e può essere facilmente recuperato esplorando la pagina Azure Key Vault chiave pertinente in portale di Azure.

## <a name="example-index-encryption"></a>Esempio: Crittografia dell'indice

Creare un indice crittografato usando [l'API REST Create Index Ricerca cognitiva di Azure .](/rest/api/searchservice/create-index) Usare la `encryptionKey` proprietà per specificare la chiave di crittografia da usare.
> [!Note]
> Nessuno di questi dettagli dell'insieme di credenziali delle chiavi viene considerato segreto e può essere facilmente recuperato selezionando la pagina Azure Key Vault chiave pertinente in portale di Azure.

## <a name="rest-examples"></a>Esempi REST

Questa sezione illustra il codice JSON completo per un indice crittografato e una mappa sinonimi

### <a name="index-encryption"></a>Crittografia degli indici

I dettagli della creazione di un nuovo indice tramite l'API REST sono disponibili in Creare un indice [(API REST),](/rest/api/searchservice/create-index)dove l'unica differenza consiste nello specificare i dettagli della chiave di crittografia come parte della definizione dell'indice:

```json
{
 "name": "hotels",
 "fields": [
  {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true},
  {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false},
  {"name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene"},
  {"name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
  {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
  {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true},
  {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true},
  {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true},
 ],
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

È ora possibile inviare la richiesta di creazione dell'indice e quindi iniziare a usare l'indice normalmente.

### <a name="synonym-map-encryption"></a>Crittografia delle mappe sinonimi

Creare una mappa di sinonimi crittografata usando [l'API REST Create Synonym Map Ricerca cognitiva di Azure REST](/rest/api/searchservice/create-synonym-map). Usare la `encryptionKey` proprietà per specificare la chiave di crittografia da usare.

```json
{
  "name" : "synonymmap1",
  "format" : "solr",
  "synonyms" : "United States, United States of America, USA\n
  Washington, Wash. => WA",
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

È ora possibile inviare la richiesta di creazione della mappa sinonima e quindi iniziare a usarla normalmente.

## <a name="example-data-source-encryption"></a>Esempio: Crittografia dell'origine dati

Creare un'origine dati crittografata usando [l'API REST create data (Ricerca cognitiva di Azure data source).](/rest/api/searchservice/create-data-source) Usare la `encryptionKey` proprietà per specificare la chiave di crittografia da usare.

```json
{
  "name" : "datasource1",
  "type" : "azureblob",
  "credentials" :
  { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=datasource;AccountKey=accountkey;EndpointSuffix=core.windows.net"
  },
  "container" : { "name" : "containername" },
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

È ora possibile inviare la richiesta di creazione dell'origine dati e quindi iniziare a usarla normalmente.

## <a name="example-skillset-encryption"></a>Esempio: Crittografia del set di competenze

Creare un set di competenze crittografato usando [l'API REST Create Skillset Ricerca cognitiva di Azure .](/rest/api/searchservice/create-skillset) Usare la `encryptionKey` proprietà per specificare la chiave di crittografia da usare.

```json
{
  "name" : "datasource1",
  "type" : "azureblob",
  "credentials" :
  { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=datasource;AccountKey=accountkey;EndpointSuffix=core.windows.net"
  },
  "container" : { "name" : "containername" },
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

È ora possibile inviare la richiesta di creazione del set di competenze e quindi iniziare a usarla normalmente.

## <a name="example-indexer-encryption"></a>Esempio: Crittografia dell'indicizzatore

Creare un indicizzatore crittografato usando [l'API REST Create Indexer Ricerca cognitiva di Azure .](/rest/api/searchservice/create-indexer) Utilizzare la `encryptionKey` proprietà per specificare la chiave di crittografia da usare.

```json
{
  "name": "indexer1",
  "dataSourceName": "datasource1",
  "skillsetName": "skillset1",
  "parameters": {
      "configuration": {
          "imageAction": "generateNormalizedImages"
      }
  },
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

È ora possibile inviare la richiesta di creazione dell'indicizzatore e quindi iniziare a usarla normalmente.

>[!Important]
> Anche se non può essere aggiunto a indici di ricerca o mappe sinonimi esistenti, può essere aggiornato specificando valori diversi per uno dei tre dettagli dell'insieme di credenziali delle chiavi, ad esempio aggiornando la versione della `encryptionKey` chiave. Quando si modifica una nuova chiave Key Vault o una nuova versione della chiave, qualsiasi indice di ricerca o mappa sinonima che usa la chiave deve essere prima aggiornato per usare la nuova chiave\versione prima di eliminare la chiave o la versione precedente.  In caso contrario, l'indice o la mappa dei sinonimi non sarà utilizzabile, in quanto non sarà in grado di decrittografare il contenuto dopo la perdita dell'accesso alla chiave. Sebbene il ripristino delle autorizzazioni di accesso dell'insieme di credenziali delle chiavi in un secondo momento ripristini l'accesso al contenuto.

## <a name="simpler-alternative-trusted-service"></a>Alternativa più semplice: Servizio attendibile

A seconda della configurazione del tenant e dei requisiti di autenticazione, è possibile implementare un approccio più semplice per l'accesso a una chiave dell'insieme di credenziali delle chiavi. Invece di creare e usare un'applicazione Active Directory, è possibile impostare un servizio di ricerca come servizio attendibile abilitando un'identità gestita dal sistema. Si userebbe quindi il servizio di ricerca attendibile come principio di sicurezza, anziché un'applicazione registrata in Active Directory, per accedere alla chiave dell'insieme di credenziali delle chiavi.

Questo approccio consente di omettere i passaggi per la registrazione dell'applicazione e i segreti dell'applicazione e semplifica la definizione di una chiave di crittografia solo per i componenti dell'insieme di credenziali delle chiavi (URI, nome dell'insieme di credenziali, versione della chiave).

In generale, un'identità gestita consente al servizio di ricerca di eseguire l'autenticazione Azure Key Vault senza archiviare le credenziali (ApplicationID o ApplicationSecret) nel codice. Il ciclo di vita di questo tipo di identità gestita è associato al ciclo di vita del servizio di ricerca, che può avere una sola identità gestita. Per altre informazioni sul funzionamento delle identità gestite, vedere Informazioni sulle [identità gestite per le risorse di Azure.](../active-directory/managed-identities-azure-resources/overview.md)

1. Rendere il servizio di ricerca un servizio attendibile.
   ![Attivare l'identità gestita assegnata dal sistema](./media/search-managed-identities/turn-on-system-assigned-identity.png "Attivare l'identità gestita assegnata dal sistema")

1. Quando si configurano criteri di accesso in Azure Key Vault, scegliere il servizio di ricerca attendibile come principio (anziché l'applicazione registrata in Active Directory). Assegnare le stesse autorizzazioni (più GET, WRAP, UNWRAP) come indicato nel passaggio Concedere le autorizzazioni per la chiave di accesso.

1. Usare una costruzione semplificata di `encryptionKey` che omette le proprietà di Active Directory.

    ```json
    {
      "encryptionKey": {
        "keyVaultUri": "https://demokeyvault.vault.azure.net",
        "keyVaultKeyName": "myEncryptionKey",
        "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
      }
    }
    ```

Le condizioni che impediscono l'adozione di questo approccio semplificato includono:

+ Non è possibile concedere direttamente al servizio di ricerca le autorizzazioni di accesso all'insieme di credenziali delle chiavi, ad esempio se il servizio di ricerca si trova in un tenant di Active Directory diverso da quello Azure Key Vault.

+ È necessario un singolo servizio di ricerca per ospitare più indici crittografati\mappe sinonimi, ognuno dei  quali usa una chiave diversa da un insieme di credenziali delle chiavi diverso, in cui ogni insieme di credenziali delle chiavi deve usare un'identità diversa per l'autenticazione. Poiché un servizio di ricerca può avere una sola identità gestita, i requisiti per più identità non qualificano l'approccio semplificato per lo scenario.  

## <a name="work-with-encrypted-content"></a>Usare il contenuto crittografato

Con la crittografia della chiave gestita dal cliente, si noterà la latenza sia per l'indicizzazione che per le query a causa del lavoro aggiuntivo di crittografia/decrittografia. Ricerca cognitiva di Azure non registra l'attività di crittografia, ma è possibile monitorare l'accesso alle chiavi tramite la registrazione dell'insieme di credenziali delle chiavi. È consigliabile abilitare la [registrazione come parte della](../key-vault/general/logging.md) configurazione dell'insieme di credenziali delle chiavi.

Si prevede che la rotazione delle chiavi si verifichi nel tempo. Ogni volta che si ruotano le chiavi, è importante seguire questa sequenza:

1. [Determinare la chiave utilizzata da una mappa](search-security-get-encryption-keys.md)di indice o sinonimo.
1. [Creare una nuova chiave nell'insieme di credenziali delle](../key-vault/keys/quick-create-portal.md)chiavi, ma lasciare disponibile la chiave originale.
1. [Aggiornare le proprietà encryptionKey](/rest/api/searchservice/update-index) in una mappa di indice o sinonimo per usare i nuovi valori. Solo gli oggetti creati in origine con questa proprietà possono essere aggiornati in modo da usare un valore diverso.
1. Disabilitare o eliminare la chiave precedente nell'insieme di credenziali delle chiavi. Monitorare l'accesso alla chiave per verificare che venga usata la nuova chiave.

Per motivi di prestazioni, il servizio di ricerca memorizza nella cache la chiave per un massimo di diverse ore. Se si disabilita o si elimina la chiave senza specificarne una nuova, le query continueranno a funzionare temporaneamente fino alla scadenza della cache. Tuttavia, quando il servizio di ricerca non è in grado di decrittografare il contenuto, verrà visualizzato il messaggio"Accesso negato. La chiave di query usata potrebbe essere stata revocata. Riprovare." 

## <a name="next-steps"></a>Passaggi successivi

Se non si ha familiarità con l'architettura di sicurezza di Azure, vedere la documentazione sulla sicurezza di [Azure](../security/index.yml)e, in particolare, questo articolo:

> [!div class="nextstepaction"]
> [Crittografia dei dati inattivi](../security/fundamentals/encryption-atrest.md)