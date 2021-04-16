---
title: Eseguire il mapping di un dominio personalizzato a Archiviazione BLOB di Azure endpoint
titleSuffix: Azure Storage
description: Eseguire il mapping di un dominio personalizzato a un endpoint Web o di archiviazione BLOB in un account di archiviazione di Azure.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 02/12/2021
ms.author: normesta
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 45ae3d80202bfb29074461f899798d278eb0895b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538357"
---
# <a name="map-a-custom-domain-to-an-azure-blob-storage-endpoint"></a>Eseguire il mapping di un dominio personalizzato a Archiviazione BLOB di Azure endpoint

È possibile eseguire il mapping di un dominio personalizzato a un endpoint del servizio BLOB o a un endpoint [di sito Web](storage-blob-static-website.md) statico. 

> [!NOTE] 
> Questo mapping funziona solo per i sottodomini (ad esempio: `www.contoso.com` ). Se si vuole che l'endpoint Web sia disponibile nel dominio radice (ad esempio, ), è necessario usare `contoso.com` Rete CDN di Azure. Per indicazioni, vedere la sezione [Eseguire il mapping di un dominio personalizzato con HTTPS](#enable-https) abilitato in questo articolo. Poiché si sta andando in quella sezione di questo articolo per abilitare il dominio radice del dominio personalizzato, il passaggio all'interno di tale sezione per l'abilitazione di HTTPS è facoltativo. 

<a id="enable-http"></a>

## <a name="map-a-custom-domain-with-only-http-enabled"></a>Eseguire il mapping di un dominio personalizzato con solo HTTP abilitato

Questo approccio è più semplice, ma consente solo l'accesso HTTP. Se l'account di archiviazione è configurato per [richiedere il trasferimento sicuro](../common/storage-require-secure-transfer.md) tramite HTTPS, è necessario abilitare l'accesso HTTPS per il dominio personalizzato. 

Per abilitare l'accesso HTTPS, vedere la sezione Eseguire il mapping [di un dominio personalizzato](#enable-https) con HTTPS abilitato in questo articolo. 

<a id="map-a-domain"></a>

### <a name="map-a-custom-domain"></a>Eseguire il mapping di un dominio personalizzato

> [!IMPORTANT]
> Il dominio personalizzato non sarà brevemente disponibile per gli utenti durante il completamento della configurazione. Se il dominio attualmente supporta un'applicazione con un contratto di servizio che richiede zero [](#zero-down-time) tempi di inattività, seguire la procedura descritta nella sezione Eseguire il mapping di un dominio personalizzato senza tempi di inattività di questo articolo per assicurarsi che gli utenti possano accedere al dominio durante il mapping DNS.

Se non si è abiliti che il dominio non sia per breve tempo disponibile per gli utenti, seguire questa procedura.

:heavy_check_mark: Passaggio 1: Ottenere il nome host dell'endpoint di archiviazione.

:heavy_check_mark: Passaggio 2: Creare un record di nome canonico (CNAME) con il provider di dominio.

:heavy_check_mark: Passaggio 3: Registrare il dominio personalizzato con Azure. 

:heavy_check_mark: Passaggio 4: Testare il dominio personalizzato.

<a id="endpoint"></a>

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>Passaggio 1: Ottenere il nome host dell'endpoint di archiviazione 

Il nome host è l'URL dell'endpoint di archiviazione senza l'identificatore di protocollo e la barra finale. 

1. Nel [portale di Azure](https://portal.azure.com) passare all'account di archiviazione.

2. Nel riquadro dei menu, in **Impostazioni** selezionare **Proprietà.**  

3. Copiare il valore **dell'endpoint del servizio BLOB primario o** dell'endpoint del sito Web statico **primario** in un file di testo. 
  
   > [!NOTE]
   > L'endpoint di Data Lake Storage non è supportato, ad esempio `https://mystorageaccount.dfs.core.windows.net/` .

4. Rimuovere l'identificatore di protocollo (ad esempio: `HTTPS` ) e la barra finale da tale stringa. La tabella seguente contiene esempi.

   | Tipo di endpoint |  endpoint | nome host |
   |------------|-----------------|-------------------|
   |servizio BLOB  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |sito Web statico  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   Impostare questo valore da riservare per un momento successivo.

<a id="create-cname-record"></a>

#### <a name="step-2-create-a-canonical-name-cname-record-with-your-domain-provider"></a>Passaggio 2: Creare un record di nome canonico (CNAME) con il provider di dominio

Creare un record CNAME in modo che punti al nome host. Un record CNAME è un tipo di record DOMAIN NAME SYSTEM (DNS) che esegue il mapping di un nome di dominio di origine a un nome di dominio di destinazione.

1. Accedere al sito Web del registrar di dominio e quindi passare alla pagina per la gestione dell'impostazione DNS.

   che potrebbe essere contenuta in una sezione denominata **Nome di dominio**, **DNS** o **Gestione server dei nomi**.

2. Trovare la sezione per la gestione dei record CNAME. 

   Potrebbe essere necessario passare a una pagina di impostazioni avanzate e cercare le parole **CNAME**, **Alias** o **Subdomains**.

3. Creare un record CNAME. Come parte di tale record, specificare gli elementi seguenti: 

   - Alias del sottodominio, ad `www` esempio o `photos` . Il sottodominio è obbligatorio, i domini radice non sono supportati. 
      
   - Il nome host ottenuto nella sezione Ottenere il nome [host dell'endpoint di archiviazione](#endpoint) più indietro in questo articolo. 

<a id="register"></a>

#### <a name="step-3-register-your-custom-domain-with-azure"></a>Passaggio 3: Registrare il dominio personalizzato in Azure

##### <a name="portal"></a>[Portale](#tab/azure-portal)

1. Nel [portale di Azure](https://portal.azure.com) passare all'account di archiviazione.

2. Nel riquadro del menu in **Servizio BLOB** selezionare **Dominio personalizzato**.

   > [!NOTE]
   > Questa opzione non viene visualizzata negli account in cui è abilitata la funzionalità dello spazio dei nomi gerarchico. Per questi account, usare PowerShell o l'interfaccia della riga di comando di Azure per completare questo passaggio.

   ![opzione del dominio personalizzato](./media/storage-custom-domain-name/custom-domain-button.png "dominio personalizzato")

   Verrà visualizzato il riquadro **Dominio personalizzato**.

3. Nella casella **di testo Nome** di dominio immettere il nome del dominio personalizzato, incluso il sottodominio  
   
   Ad esempio, se il dominio *è* contoso.com e l'alias del sottodominio è *www*, immettere `www.contoso.com` . Se il sottodominio *è photos,* immettere `photos.contoso.com` .

4. Per registrare il dominio personalizzato, scegliere **il pulsante** Salva.

   Dopo che il record CNAME è stato propagato tramite i domain name server (DNS) e se gli utenti hanno le autorizzazioni appropriate, possono visualizzare i dati BLOB usando il dominio personalizzato.

##### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Eseguire il seguente comando di PowerShell

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group-name> -Name <storage-account-name> -CustomDomainName <custom-domain-name> -UseSubDomain $false
```

- Sostituire il `<resource-group-name>` segnaposto con il nome del gruppo di risorse.

- Sostituire il `<storage-account-name>` segnaposto con il nome dell'account di archiviazione.

- Sostituire il `<custom-domain-name>` segnaposto con il nome del dominio personalizzato, incluso il sottodominio.

  Ad esempio, se il dominio *è* contoso.com e l'alias del sottodominio è *www*, immettere `www.contoso.com` . Se il sottodominio *è photos,* immettere `photos.contoso.com` .

Dopo che il record CNAME è stato propagato tramite i domain name server (DNS) e se gli utenti hanno le autorizzazioni appropriate, possono visualizzare i dati BLOB usando il dominio personalizzato.

##### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Eseguire il seguente comando di PowerShell

```azurecli
az storage account update \
   --resource-group <resource-group-name> \ 
   --name <storage-account-name> \
   --custom-domain <custom-domain-name> \
   --use-subdomain false
  ```

- Sostituire il `<resource-group-name>` segnaposto con il nome del gruppo di risorse.

- Sostituire il `<storage-account-name>` segnaposto con il nome dell'account di archiviazione.

- Sostituire il `<custom-domain-name>` segnaposto con il nome del dominio personalizzato, incluso il sottodominio.

  Ad esempio, se il dominio *è* contoso.com e l'alias del sottodominio è *www*, immettere `www.contoso.com` . Se il sottodominio *è photos,* immettere `photos.contoso.com` .

Dopo che il record CNAME è stato propagato tramite domain name server (DNS) e se gli utenti hanno le autorizzazioni appropriate, possono visualizzare i dati BLOB usando il dominio personalizzato.

---

#### <a name="step-4-test-your-custom-domain"></a>Passaggio 4: Testare il dominio personalizzato

Per verificare che il mapping del dominio personalizzato all'endpoint del servizio BLOB sia stato eseguito, creare un BLOB in un contenitore pubblico all'interno dell'account di archiviazione. Quindi, in un Web browser, accedere al BLOB usando un URI nel formato seguente: `http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Ad esempio, per accedere a un modulo Web nel contenitore nel `myforms` *sottodominio* photos.contoso.com personalizzato, è possibile usare l'URI seguente: `http://photos.contoso.com/myforms/applicationform.htm`

<a id="zero-down-time"></a>

### <a name="map-a-custom-domain-with-zero-downtime"></a>Eseguire il mapping di un dominio personalizzato senza tempi di inattività

> [!NOTE]
> Se non si ritiene che il dominio non sia per breve tempo disponibile per gli utenti, è consigliabile seguire la procedura descritta nella sezione Eseguire il mapping di un dominio [personalizzato](#map-a-domain) di questo articolo. Si tratta di un approccio più semplice con meno passaggi.  

Se il dominio attualmente supporta un'applicazione con un contratto di servizio che richiede zero tempi di inattività, seguire questa procedura per assicurarsi che gli utenti possano accedere al dominio durante il mapping DNS. 

:heavy_check_mark: Passaggio 1: Ottenere il nome host dell'endpoint di archiviazione.

:heavy_check_mark: Passaggio 2: Creare un record di nome canonico intermedio (CNAME) con il provider di dominio.

:heavy_check_mark: Passaggio 3: Pre-registrare il dominio personalizzato con Azure.

:heavy_check_mark: Passaggio 4: Creare un record CNAME con il provider di dominio.

:heavy_check_mark: Passaggio 5: Testare il dominio personalizzato.

<a id="endpoint-2"></a>

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>Passaggio 1: Ottenere il nome host dell'endpoint di archiviazione 

Il nome host è l'URL dell'endpoint di archiviazione senza l'identificatore di protocollo e la barra finale. 

1. Nel [portale di Azure](https://portal.azure.com) passare all'account di archiviazione.

2. Nel riquadro dei menu in **Impostazioni** selezionare **Proprietà**.  

3. Copiare il valore **dell'endpoint del servizio BLOB primario o** dell'endpoint del sito Web **statico** primario in un file di testo. 

   > [!NOTE]
   > L'endpoint di Archiviazione Data Lake non è supportato (ad esempio: `https://mystorageaccount.dfs.core.windows.net/` ).

4. Rimuovere l'identificatore di protocollo (ad esempio: `HTTPS` ) e la barra finale da tale stringa. La tabella seguente contiene esempi.

   | Tipo di endpoint |  endpoint | nome host |
   |------------|-----------------|-------------------|
   |servizio BLOB  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |sito Web statico  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   Impostare questo valore da riservare per un momento successivo.

#### <a name="step-2-create-an-intermediary-canonical-name-cname-record-with-your-domain-provider"></a>Passaggio 2: Creare un record CNAME (Canonical Name) intermedio con il provider di dominio

Creare un record CNAME temporaneo in modo che punti al nome host. Un record CNAME è un tipo di record DNS che esegue il mapping di un nome di dominio di origine a uno di destinazione.

1. Accedere al sito Web del registrar di dominio e quindi passare alla pagina per la gestione dell'impostazione DNS.

   che potrebbe essere contenuta in una sezione denominata **Nome di dominio**, **DNS** o **Gestione server dei nomi**.

2. Trovare la sezione per la gestione dei record CNAME. 

   Potrebbe essere necessario passare a una pagina di impostazioni avanzate e cercare le parole **CNAME**, **Alias** o **Subdomains**.

3. Creare un record CNAME. Come parte di tale record, specificare gli elementi seguenti: 

   - Alias del sottodominio, ad `www` esempio o `photos` . Il sottodominio è obbligatorio, i domini radice non sono supportati.

     Aggiungere il `asverify` sottodominio all'alias. Ad esempio, `asverify.www` o `asverify.photos`.
       
   - Il nome host ottenuto nella sezione Ottenere il [nome host dell'endpoint di archiviazione](#endpoint) più indietro in questo articolo. 

     Aggiungere il `asverify` sottodominio al nome host. Ad esempio: `asverify.mystorageaccount.blob.core.windows.net`.

#### <a name="step-3-pre-register-your-custom-domain-with-azure"></a>Passaggio 3: Pre-registrare il dominio personalizzato in Azure

Quando si preregistra il dominio personalizzato in Azure, si consente ad Azure di riconoscere il dominio personalizzato senza dover modificare il record DNS per il dominio. In questo modo, quando si modifica il record DNS per il dominio, verrà eseguito il mapping all'endpoint BLOB senza tempi di inattività.

##### <a name="portal"></a>[Portale](#tab/azure-portal)

1. Nel [portale di Azure](https://portal.azure.com) passare all'account di archiviazione.

2. Nel riquadro del menu in **Servizio BLOB** selezionare **Dominio personalizzato**.

   > [!NOTE]
   > Questa opzione non viene visualizzata negli account in cui è abilitata la funzionalità dello spazio dei nomi gerarchico. Per questi account, usare PowerShell o l'interfaccia della riga di comando di Azure per completare questo passaggio.

   ![opzione del dominio personalizzato](./media/storage-custom-domain-name/custom-domain-button.png "dominio personalizzato")

   Verrà visualizzato il riquadro **Dominio personalizzato**.

3. Nella casella **di testo Nome** di dominio immettere il nome del dominio personalizzato, incluso il sottodominio  
   
   Ad esempio, se il dominio *è* contoso.com e l'alias del sottodominio è *www*, immettere `www.contoso.com` . Se il sottodominio *è photos,* immettere `photos.contoso.com` .

4. Selezionare la casella di controllo **Usa convalida CNAME indiretta**.

5. Per registrare il dominio personalizzato, scegliere il **pulsante** Salva.
  
   Se la registrazione ha esito positivo, una notifica nel portale informa che l'account di archiviazione è stato aggiornato correttamente. Il dominio personalizzato è stato verificato da Azure, ma il traffico verso il dominio non viene ancora instradato all'account di archiviazione fino a quando non si crea un record CNAME con il provider di dominio. Questa operazione viene illustrata nella sezione seguente.

##### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Eseguire il seguente comando di PowerShell

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group-name> -Name <storage-account-name> -CustomDomainName <custom-domain-name> -UseSubDomain $true
```

- Sostituire il `<resource-group-name>` segnaposto con il nome del gruppo di risorse.

- Sostituire il `<storage-account-name>` segnaposto con il nome dell'account di archiviazione.

- Sostituire il `<custom-domain-name>` segnaposto con il nome del dominio personalizzato, incluso il sottodominio.

  Ad esempio, se il dominio *è* contoso.com e l'alias del sottodominio è *www*, immettere `www.contoso.com` . Se il sottodominio *è photos,* immettere `photos.contoso.com` .

Il traffico verso il dominio non viene ancora instradato all'account di archiviazione fino a quando non si crea un record CNAME con il provider di dominio. Questa operazione viene illustrata nella sezione seguente.

##### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Eseguire il seguente comando di PowerShell

```azurecli
az storage account update \
   --resource-group <resource-group-name> \ 
   --name <storage-account-name> \
   --custom-domain <custom-domain-name> \
   --use-subdomain true
  ```

- Sostituire il `<resource-group-name>` segnaposto con il nome del gruppo di risorse.

- Sostituire il `<storage-account-name>` segnaposto con il nome dell'account di archiviazione.

- Sostituire il `<custom-domain-name>` segnaposto con il nome del dominio personalizzato, incluso il sottodominio.

  Ad esempio, se il dominio *è* contoso.com e l'alias del sottodominio è *www*, immettere `www.contoso.com` . Se il sottodominio *è photos,* immettere `photos.contoso.com` .

Il traffico verso il dominio non viene ancora instradato all'account di archiviazione fino a quando non si crea un record CNAME con il provider di dominio. Questa operazione viene illustrata nella sezione seguente.

---

#### <a name="step-4-create-a-cname-record-with-your-domain-provider"></a>Passaggio 4: Creare un record CNAME con il provider di dominio

Creare un record CNAME temporaneo in modo che punti al nome host.

1. Accedere al sito Web del registrar di dominio e quindi passare alla pagina per la gestione dell'impostazione DNS.

   che potrebbe essere contenuta in una sezione denominata **Nome di dominio**, **DNS** o **Gestione server dei nomi**.

2. Trovare la sezione per la gestione dei record CNAME. 

   Potrebbe essere necessario passare a una pagina di impostazioni avanzate e cercare le parole **CNAME**, **Alias** o **Subdomains**.

3. Creare un record CNAME. Come parte di tale record, specificare gli elementi seguenti: 

   - Alias del sottodominio, ad `www` esempio o `photos` . Il sottodominio è obbligatorio, i domini radice non sono supportati.
      
   - Il nome host ottenuto nella sezione Ottenere il nome [host dell'endpoint di archiviazione](#endpoint-2) più indietro in questo articolo. 

#### <a name="step-5-test-your-custom-domain"></a>Passaggio 5: Testare il dominio personalizzato

Per verificare che il mapping del dominio personalizzato all'endpoint del servizio BLOB sia stato eseguito, creare un BLOB in un contenitore pubblico all'interno dell'account di archiviazione. Quindi, in un Web browser, accedere al BLOB usando un URI nel formato seguente: `http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Ad esempio, per accedere a un modulo Web nel contenitore `myforms` nel *sottodominio photos.contoso.com* personalizzato, è possibile usare l'URI seguente: `http://photos.contoso.com/myforms/applicationform.htm`

### <a name="remove-a-custom-domain-mapping"></a>Rimuovere un mapping di dominio personalizzato

Per rimuovere un mapping di dominio personalizzato, annullare la registrazione del dominio personalizzato. Utilizzare una delle seguenti procedure.

#### <a name="portal"></a>[Portale](#tab/azure-portal)

1. Nel [portale di Azure](https://portal.azure.com) passare all'account di archiviazione.

2. Nel riquadro del menu in **Servizio BLOB** selezionare **Dominio personalizzato**.  
   Verrà visualizzato il riquadro **Dominio personalizzato**.

3. Cancellare il contenuto della casella di testo contenente il nome di dominio personalizzato.

4. Fare clic sul pulsante **Salva**.

Dopo aver rimosso correttamente il dominio personalizzato, verrà visualizzata una notifica nel portale che informa che l'account di archiviazione è stato aggiornato correttamente.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Per rimuovere la registrazione di un dominio personalizzato, usare il cmdlet di PowerShell [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) e quindi specificare una stringa vuota (`""`) per il valore dell'argomento `-CustomDomainName`.

* Formato del comando:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "<resource-group-name>" `
      -AccountName "<storage-account-name>" `
      -CustomDomainName ""
  ```

* Esempio del comando:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "myresourcegroup" `
      -AccountName "mystorageaccount" `
      -CustomDomainName ""
  ```

#### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per rimuovere la registrazione di un dominio personalizzato, usare il comando [az storage account update](/cli/azure/storage/account) dell'interfaccia della riga di comando e quindi specificare una stringa vuota (`""`) per il valore dell'argomento `--custom-domain`.

* Formato del comando:

  ```azurecli
  az storage account update \
      --name <storage-account-name> \
      --resource-group <resource-group-name> \
      --custom-domain ""
  ```

* Esempio del comando:

  ```azurecli
  az storage account update \
      --name mystorageaccount \
      --resource-group myresourcegroup \
      --custom-domain ""
  ```
---

<a id="enable-https"></a>

## <a name="map-a-custom-domain-with-https-enabled"></a>Eseguire il mapping di un dominio personalizzato con HTTPS abilitato

Questo approccio prevede più passaggi, ma consente l'accesso HTTPS. 

Se non è necessario che gli utenti accedono al BLOB o al contenuto Web tramite HTTPS, vedere la sezione Eseguire il mapping di un dominio personalizzato con solo [HTTP](#enable-http) abilitato in questo articolo. 

1. Abilitare [Rete CDN di Azure](../../cdn/cdn-overview.md) nell'endpoint BLOB o Web. 

   Per un endpoint di archiviazione BLOB, vedere [Integrare un account di archiviazione di Azure con Rete CDN di Azure](../../cdn/cdn-create-a-storage-account-with-cdn.md). 

   Per un endpoint di sito Web statico, vedere [Integrare un sito Web statico con Rete CDN di Azure](static-website-content-delivery-network.md).

2. [Eseguire il mapping del contenuto della rete CDN a un dominio personalizzato](../../cdn/cdn-map-content-to-custom-domain.md).

3. [Abilitare HTTPS in un Rete CDN di Azure personalizzato.](../../cdn/cdn-custom-ssl.md)

   > [!NOTE] 
   > Quando si aggiorna il sito Web statico, assicurarsi di cancellare il contenuto memorizzato nella cache nei server perimetrali della rete CDN ripulindo l'endpoint della rete CDN. Per altre informazioni, vedere [Ripulire un endpoint della rete CDN di Azure](../../cdn/cdn-purge-endpoint.md).

4. (Facoltativo) Esaminare le linee guida seguenti:

   * [Token di firma di accesso condiviso con Rete CDN di Azure](../../cdn/cdn-storage-custom-domain-https.md#shared-access-signatures).

   * [Reindirizzamento da HTTP a HTTPS con Rete CDN di Azure](../../cdn/cdn-storage-custom-domain-https.md#http-to-https-redirection).

   * [Prezzi e fatturazione quando si usa Archiviazione BLOB con Rete CDN di Azure](../../cdn/cdn-storage-custom-domain-https.md#pricing-and-billing).

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sull'hosting di siti Web statici in Archiviazione BLOB di Azure](storage-blob-static-website.md)