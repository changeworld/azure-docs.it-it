---
title: Come usare endpoint privati con servizi vocali
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare i servizi di riconoscimento vocale con endpoint privati forniti dal collegamento privato di Azure
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: alexeyo
ms.openlocfilehash: c9af0cda14261e8eab7f1ecc05c50a289d7ddfdb
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99559658"
---
# <a name="use-speech-services-through-a-private-endpoint"></a>Usare i servizi di riconoscimento vocale tramite un endpoint privato

Il [collegamento privato di Azure](../../private-link/private-link-overview.md) consente di connettersi ai servizi in Azure usando un [endpoint privato](../../private-link/private-endpoint-overview.md). Un endpoint privato è un indirizzo IP privato accessibile solo all'interno di una [rete virtuale](../../virtual-network/virtual-networks-overview.md) e una subnet specifiche.

Questo articolo illustra come configurare e usare endpoint privati e di collegamento privato con servizi di riconoscimento vocale in Servizi cognitivi di Azure.

> [!NOTE]
> Prima di procedere, vedere [come usare le reti virtuali con servizi cognitivi](../cognitive-services-virtual-networks.md).

Questo articolo descrive anche [come rimuovere gli endpoint privati in un secondo momento, ma continuare a usare la risorsa sintesi vocale](#use-a-speech-resource-with-a-custom-domain-name-and-without-private-endpoints).

## <a name="create-a-custom-domain-name"></a>Creare un nome di dominio personalizzato

Per gli endpoint privati è necessario un [nome di sottodominio personalizzato per servizi cognitivi](../cognitive-services-custom-subdomains.md). Usare le istruzioni seguenti per crearne uno per la risorsa di riconoscimento vocale.

> [!WARNING]
> Una risorsa vocale con un nome di dominio personalizzato abilitato usa un modo diverso per interagire con i servizi di riconoscimento vocale. Potrebbe essere necessario modificare il codice dell'applicazione per entrambi gli scenari: [endpoint privato abilitato](#use-a-speech-resource-with-a-custom-domain-name-and-a-private-endpoint-enabled) e [ *non* abilitato endpoint privato](#use-a-speech-resource-with-a-custom-domain-name-and-without-private-endpoints).
>
> Quando si Abilita un nome di dominio personalizzato, l'operazione [non è reversibile](../cognitive-services-custom-subdomains.md#can-i-change-a-custom-domain-name). L'unico modo per tornare al nome della [regione](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) è creare una nuova risorsa di riconoscimento vocale.
>
> Se la risorsa vocale dispone di numerosi modelli e progetti personalizzati associati creati tramite [speech studio](https://speech.microsoft.com/), è consigliabile provare la configurazione con una risorsa di test prima di modificare la risorsa usata nell'ambiente di produzione.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Per creare un nome di dominio personalizzato tramite il portale di Azure, attenersi alla procedura seguente:

1. Passare al [portale di Azure](https://portal.azure.com/) e accedere all'account Azure.
1. Selezionare la risorsa vocale richiesta.
1. Nel gruppo **Gestione risorse** nel riquadro sinistro selezionare **rete**.
1. Nella scheda **firewall e reti virtuali** selezionare **genera nome di dominio personalizzato**. Viene visualizzato un nuovo pannello a destra con le istruzioni per creare un sottodominio personalizzato univoco per la risorsa.
1. Nel pannello **genera nome dominio personalizzato** immettere un nome di dominio personalizzato. Il dominio personalizzato completo sarà simile al seguente: `https://{your custom name}.cognitiveservices.azure.com` . 
    
    Tenere presente che dopo aver creato un nome di dominio personalizzato, non è _possibile_ modificarlo.
    
    Dopo aver immesso il nome di dominio personalizzato, selezionare **Salva**.
1. Al termine dell'operazione, nel gruppo **Gestione risorse** selezionare **chiavi ed endpoint**. Verificare che il nuovo nome dell'endpoint della risorsa venga avviato in questo modo: `https://{your custom name}.cognitiveservices.azure.com` .

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per creare un nome di dominio personalizzato tramite PowerShell, verificare che il computer disponga di PowerShell 7. x o versione successiva con il modulo Azure PowerShell versione 5.1.0 o successiva. Per visualizzare le versioni di questi strumenti, attenersi alla seguente procedura:

1. In una finestra di PowerShell immettere:

    `$PSVersionTable`

    Verificare che il `PSVersion` valore sia 7. x o versione successiva. Per aggiornare PowerShell, seguire le istruzioni in [installazione di diverse versioni di PowerShell](/powershell/scripting/install/installing-powershell).

1. In una finestra di PowerShell immettere:

    `Get-Module -ListAvailable Az`

    Se non viene visualizzato alcun elemento o se tale versione del modulo Azure PowerShell è precedente a 5.1.0, seguire le istruzioni in [installare il modulo di Azure PowerShell](/powershell/azure/install-Az-ps) per eseguire l'aggiornamento.

Prima di procedere, eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="verify-that-a-custom-domain-name-is-available"></a>Verificare che sia disponibile un nome di dominio personalizzato

Controllare se il dominio personalizzato che si vuole usare è disponibile. Il codice seguente conferma che il dominio è disponibile tramite l'operazione [Controlla disponibilità dominio](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) nell'API REST di servizi cognitivi.

> [!TIP]
> Il codice seguente *non* funzionerà in Azure cloud Shell.

```azurepowershell
$subId = "Your Azure subscription Id"
$subdomainName = "custom domain name"

# Select the Azure subscription that contains the Speech resource.
# You can skip this step if your Azure account has only one active subscription.
Set-AzContext -SubscriptionId $subId

# Prepare the OAuth token to use in the request to the Cognitive Services REST API.
$Context = Get-AzContext
$AccessToken = (Get-AzAccessToken -TenantId $Context.Tenant.Id).Token
$token = ConvertTo-SecureString -String $AccessToken -AsPlainText -Force

# Prepare and send the request to the Cognitive Services REST API.
$uri = "https://management.azure.com/subscriptions/" + $subId + `
    "/providers/Microsoft.CognitiveServices/checkDomainAvailability?api-version=2017-04-18"
$body = @{
subdomainName = $subdomainName
type = "Microsoft.CognitiveServices/accounts"
}
$jsonBody = $body | ConvertTo-Json
Invoke-RestMethod -Method Post -Uri $uri -ContentType "application/json" -Authentication Bearer `
    -Token $token -Body $jsonBody | Format-List
```
Se il nome desiderato è disponibile, verrà visualizzata una risposta simile alla seguente:
```azurepowershell
isSubdomainAvailable : True
reason               :
type                 :
subdomainName        : my-custom-name
```
Se il nome è già stato effettuato, verrà visualizzata la risposta seguente:
```azurepowershell
isSubdomainAvailable : False
reason               : Sub domain name 'my-custom-name' is already used. Please pick a different name.
type                 :
subdomainName        : my-custom-name
```
## <a name="create-your-custom-domain-name"></a>Creare il nome di dominio personalizzato

Per abilitare un nome di dominio personalizzato per la risorsa vocale selezionata, usare il cmdlet [set-AzCognitiveServicesAccount](/powershell/module/az.cognitiveservices/set-azcognitiveservicesaccount) .

> [!WARNING]
> Quando il codice seguente viene eseguito correttamente, si creerà un nome di dominio personalizzato per la risorsa di riconoscimento vocale. Tenere presente che questo nome *non può* essere modificato.

```azurepowershell
$resourceGroup = "Resource group name where Speech resource is located"
$speechResourceName = "Your Speech resource name"
$subdomainName = "custom domain name"

# Select the Azure subscription that contains the Speech resource.
# You can skip this step if your Azure account has only one active subscription.
$subId = "Your Azure subscription Id"
Set-AzContext -SubscriptionId $subId

# Set the custom domain name to the selected resource.
# WARNING: THIS CANNOT BE CHANGED OR UNDONE!
Set-AzCognitiveServicesAccount -ResourceGroupName $resourceGroup `
    -Name $speechResourceName -CustomSubdomainName $subdomainName
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

Questa sezione richiede la versione più recente dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, è già installata la versione più recente.

## <a name="verify-that-the-custom-domain-name-is-available"></a>Verificare che il nome di dominio personalizzato sia disponibile

Controllare se il dominio personalizzato che si vuole usare è gratuito. Usare il metodo di [Verifica della disponibilità del dominio](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) dall'API REST di servizi cognitivi.

Copiare il blocco di codice seguente, inserire il nome di dominio personalizzato preferito e salvarlo nel file `subdomain.json` .

```json
{
    "subdomainName": "custom domain name",
    "type": "Microsoft.CognitiveServices/accounts"
}
```

Copiare il file nella cartella corrente o caricarlo Azure Cloud Shell ed eseguire il comando seguente. Sostituire `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` con l'ID della sottoscrizione di Azure.

```azurecli-interactive
az rest --method post --url "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.CognitiveServices/checkDomainAvailability?api-version=2017-04-18" --body @subdomain.json
```
Se il nome desiderato è disponibile, verrà visualizzata una risposta simile alla seguente:
```azurecli
{
  "isSubdomainAvailable": true,
  "reason": null,
  "subdomainName": "my-custom-name",
  "type": null
}
```

Se il nome è già stato effettuato, verrà visualizzata la risposta seguente:
```azurecli
{
  "isSubdomainAvailable": false,
  "reason": "Sub domain name 'my-custom-name' is already used. Please pick a different name.",
  "subdomainName": "my-custom-name",
  "type": null
}
```
## <a name="enable-a-custom-domain-name"></a>Abilitare un nome di dominio personalizzato

Per abilitare un nome di dominio personalizzato per la risorsa vocale selezionata, usare il comando [AZ cognitiveservices account Update](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_update) .

Selezionare la sottoscrizione di Azure che contiene la risorsa di sintesi vocale. Se l'account Azure ha solo una sottoscrizione attiva, è possibile ignorare questo passaggio. Sostituire `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` con l'ID della sottoscrizione di Azure.
```azurecli-interactive
az account set --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
Consente di impostare il nome di dominio personalizzato sulla risorsa selezionata. Sostituire i valori dei parametri di esempio con quelli effettivi ed eseguire il comando seguente.

> [!WARNING]
> Al termine dell'esecuzione del comando seguente, verrà creato un nome di dominio personalizzato per la risorsa di riconoscimento vocale. Tenere presente che questo nome *non può* essere modificato.

```azurecli
az cognitiveservices account update --name my-speech-resource-name --resource-group my-resource-group-name --custom-domain my-custom-name
```

***

## <a name="enable-private-endpoints"></a>Abilita endpoint privati

È consigliabile usare la [zona DNS privata](../../dns/private-dns-overview.md) collegata alla rete virtuale con gli aggiornamenti necessari per gli endpoint privati. Per impostazione predefinita, si crea una zona DNS privata durante il processo di provisioning. Se si usa un server DNS personalizzato, potrebbe essere necessario modificare anche la configurazione DNS. 

Scegliere una strategia DNS *prima* di effettuare il provisioning di endpoint privati per una risorsa vocale di produzione. E testare le modifiche DNS, soprattutto se si usa il proprio server DNS.

Per creare endpoint privati, usare uno degli articoli seguenti. Questi articoli usano un'app Web come risorsa di esempio per abilitare con endpoint privati.

- [Creare un endpoint privato con il portale di Azure](../../private-link/create-private-endpoint-portal.md)
- [Creare un endpoint privato con Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)
- [Creare un endpoint privato con l'interfaccia della riga di comando di Azure](../../private-link/create-private-endpoint-cli.md)

Usare questi parametri anziché i parametri nell'articolo scelto:

| Impostazione             | Valore                                    |
|---------------------|------------------------------------------|
| Tipo di risorsa       | **Microsoft. CognitiveServices/accounts** |
| Risorsa            | **\<your-speech-resource-name>**         |
| Sottorisorsa di destinazione | **account**                              |

**DNS per endpoint privati:** Esaminare i principi generali del [DNS per gli endpoint privati nelle risorse di servizi cognitivi](../cognitive-services-virtual-networks.md#dns-changes-for-private-endpoints). Quindi verificare che la configurazione DNS funzioni correttamente eseguendo i controlli descritti nelle sezioni riportate di seguito.

### <a name="resolve-dns-from-the-virtual-network"></a>Risolvere il DNS dalla rete virtuale

Questo controllo è *obbligatorio*.

Per testare la voce DNS personalizzata dalla rete virtuale, seguire questa procedura:

1. Accedere a una macchina virtuale che si trova nella rete virtuale a cui è stato collegato l'endpoint privato. 
1. Aprire un prompt dei comandi di Windows o una shell bash, eseguire `nslookup` e verificare che venga risolto correttamente il nome di dominio personalizzato della risorsa.

   ```dos
   C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
   Server:  UnKnown
   Address:  168.63.129.16

   Non-authoritative answer:
   Name:    my-private-link-speech.privatelink.cognitiveservices.azure.com
   Address:  172.28.0.10
   Aliases:  my-private-link-speech.cognitiveservices.azure.com
   ```

1. Verificare che l'indirizzo IP corrisponda all'indirizzo IP dell'endpoint privato.

### <a name="resolve-dns-from-other-networks"></a>Risolvere il DNS da altre reti

Eseguire questa verifica solo se è stata abilitata l'opzione **tutte le reti** o l'opzione di accesso **reti selezionate e endpoint privati** nella sezione **rete** della risorsa. 

Se si prevede di accedere alla risorsa usando solo un endpoint privato, è possibile ignorare questa sezione.

1. Accedere a un computer collegato a una rete a cui è consentito accedere alla risorsa.
2. Aprire un prompt dei comandi di Windows o una shell bash, eseguire `nslookup` e verificare che venga risolto correttamente il nome di dominio personalizzato della risorsa.

   ```dos
   C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
   Server:  UnKnown
   Address:  fe80::1

   Non-authoritative answer:
   Name:    vnetproxyv1-weu-prod.westeurope.cloudapp.azure.com
   Address:  13.69.67.71
   Aliases:  my-private-link-speech.cognitiveservices.azure.com
             my-private-link-speech.privatelink.cognitiveservices.azure.com
             westeurope.prod.vnet.cog.trafficmanager.net
   ```

> [!NOTE]
> L'indirizzo IP risolto punta a un endpoint proxy della rete virtuale, che invia il traffico di rete all'endpoint privato per la risorsa Servizi cognitivi. Il comportamento sarà diverso per una risorsa con un nome di dominio personalizzato ma *senza* endpoint privati. Per informazioni dettagliate, vedere [questa sezione](#dns-configuration) .

## <a name="adjust-existing-applications-and-solutions"></a>Modificare le applicazioni e le soluzioni esistenti

Una risorsa vocale con un dominio personalizzato abilitato usa un modo diverso per interagire con i servizi di riconoscimento vocale. Questo vale per una risorsa di sintesi vocale abilitata per il dominio personalizzato con e senza endpoint privati. Le informazioni contenute in questa sezione si applicano a entrambi gli scenari.

### <a name="use-a-speech-resource-with-a-custom-domain-name-and-a-private-endpoint-enabled"></a>Usare una risorsa vocale con un nome di dominio personalizzato e un endpoint privato abilitato

Una risorsa vocale con un nome di dominio personalizzato e un endpoint privato abilitato usa un modo diverso per interagire con i servizi di riconoscimento vocale. Questa sezione illustra come usare tale risorsa con le API REST di servizi vocali e l' [SDK di riconoscimento vocale](speech-sdk.md).

> [!NOTE]
> Una risorsa vocale senza endpoint privati ma con un nome di dominio personalizzato abilitato dispone anche di un metodo speciale per interagire con i servizi di riconoscimento vocale. Questo comportamento differisce dallo scenario di una risorsa di riconoscimento vocale abilitata per l'endpoint privato. Se si dispone di una risorsa di questo tipo, ad esempio se si dispone di una risorsa con endpoint privati, ma si è deciso di rimuoverli, vedere la sezione [usare una risorsa vocale con un nome di dominio personalizzato e senza endpoint privati](#use-a-speech-resource-with-a-custom-domain-name-and-without-private-endpoints).

#### <a name="speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-rest-apis"></a>Risorsa vocale con un nome di dominio personalizzato e un endpoint privato: utilizzo con le API REST

`my-private-link-speech.cognitiveservices.azure.com`Per questa sezione verrà usato come nome DNS della risorsa vocale di esempio (dominio personalizzato).

Servizi vocali ha API REST per [sintesi vocale](rest-speech-to-text.md) [e sintesi vocale.](rest-text-to-speech.md) Prendere in considerazione le informazioni seguenti per lo scenario abilitato per gli endpoint privati.

Il riconoscimento vocale ha due API REST. Ogni API serve uno scopo diverso, USA endpoint diversi e richiede un approccio diverso quando lo si usa nello scenario abilitato per gli endpoint privati.

Le API REST per sintesi vocale sono:
- [API REST di sintesi vocale v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30), che viene usata per la [trascrizione](batch-transcription.md) e la [riconoscimento vocale personalizzato](custom-speech-overview.md)di batch. v 3.0 è un [successore della versione 2.0](./migrate-v2-to-v3.md)
- [API REST per sintesi vocale per brevi audio](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio), che viene usata per la trascrizione online 

L'uso dell'API REST di sintesi vocale per l'audio breve e l'API REST di sintesi vocale nello scenario di endpoint privato è la stessa. Equivale al [caso dell'SDK di riconoscimento vocale](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk) descritto più avanti in questo articolo. 

L'API REST di sintesi vocale v 3.0 usa un set di endpoint diverso, quindi richiede un approccio diverso per lo scenario abilitato per gli endpoint privati.

Le sottosezioni successive descrivono entrambi i casi.

##### <a name="speech-to-text-rest-api-v30"></a>API REST per sintesi vocale v 3.0

In genere, le risorse vocali usano [endpoint regionali di servizi cognitivi](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) per la comunicazione con l' [API REST di sintesi vocale v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30). Queste risorse hanno il formato di denominazione seguente: <p/>`{region}.api.cognitive.microsoft.com`.

Si tratta di un URL di richiesta di esempio:

```http
https://westeurope.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions
```

> [!NOTE]
> Vedere [questo articolo](sovereign-clouds.md) per gli endpoint di Azure per enti pubblici e Azure Cina.

Dopo aver abilitato un dominio personalizzato per una risorsa vocale (necessaria per gli endpoint privati), tale risorsa userà il modello di nome DNS seguente per l'endpoint API REST di base: <p/>`{your custom name}.cognitiveservices.azure.com`.

Ciò significa che, in questo esempio, il nome dell'endpoint dell'API REST sarà: <p/>`my-private-link-speech.cognitiveservices.azure.com`.

E l'URL della richiesta di esempio deve essere convertito in:
```http
https://my-private-link-speech.cognitiveservices.azure.com/speechtotext/v3.0/transcriptions
```
Questo URL deve essere raggiungibile dalla rete virtuale con l'endpoint privato collegato (purché la [risoluzione DNS sia corretta](#resolve-dns-from-the-virtual-network)).

Dopo aver abilitato un nome di dominio personalizzato per una risorsa vocale, in genere si sostituisce il nome host in tutti gli URL delle richieste con il nuovo nome host del dominio personalizzato. Tutte le altre parti della richiesta, come il percorso `/speechtotext/v3.0/transcriptions` nell'esempio precedente, rimangono invariate.

> [!TIP]
> Alcuni clienti sviluppano applicazioni che usano la parte Region del nome DNS dell'endpoint di area (ad esempio, per inviare la richiesta alla risorsa di sintesi vocale distribuita in una determinata area di Azure).
>
> Un dominio personalizzato per una risorsa vocale *non* contiene informazioni sull'area in cui è distribuita la risorsa. Quindi, la logica dell'applicazione descritta in precedenza *non* funzionerà e deve essere modificata.

##### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>API REST per sintesi vocale per le API REST brevi audio e sintesi vocale

L' [API REST per sintesi vocale per l'audio breve](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) e l' [API REST](rest-text-to-speech.md) di sintesi vocale usano due tipi di endpoint:
- [Endpoint regionali di servizi cognitivi](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) per la comunicazione con l'API REST di servizi cognitivi per ottenere un token di autorizzazione
- Endpoint speciali per tutte le altre operazioni

> [!NOTE]
> Vedere [questo articolo](sovereign-clouds.md) per gli endpoint di Azure per enti pubblici e Azure Cina.

In [questa sottosezione](#construct-endpoint-url) viene fornita una descrizione dettagliata degli endpoint speciali e del modo in cui l'URL deve essere trasformato per una risorsa di riconoscimento vocale abilitata per l'endpoint privato in merito all'utilizzo con l'SDK di riconoscimento vocale. Lo stesso principio descritto per l'SDK si applica all'API REST di sintesi vocale per l'audio breve e l'API REST di sintesi vocale.

Acquisire familiarità con il materiale nella sottosezione indicata nel paragrafo precedente e vedere l'esempio seguente. Nell'esempio viene descritta l'API REST di sintesi vocale. L'uso dell'API REST di sintesi vocale per l'audio breve è completamente equivalente.

> [!NOTE]
> Quando si usa l'API REST di riconoscimento vocale per l'API REST per audio e sintesi vocale brevi negli scenari di endpoint privati, usare una chiave di sottoscrizione passata attraverso l' `Ocp-Apim-Subscription-Key` intestazione. (Vedere i dettagli per l' [API REST di sintesi vocale per](rest-speech-to-text.md#request-headers) l' [API REST](rest-text-to-speech.md#request-headers)per audio breve e sintesi vocale)
>
> Usare un token di autorizzazione e passarlo all'endpoint speciale tramite l' `Authorization` intestazione funzionerà *solo* se è stata abilitata l'opzione accesso a **tutte le reti** nella sezione **rete** della risorsa di riconoscimento vocale. In altri casi, si otterrà `Forbidden` `BadRequest` un errore o quando si tenta di ottenere un token di autorizzazione.

**Esempio di utilizzo dell'API REST di sintesi vocale**

L'Europa occidentale verrà usata come area di Azure di esempio e `my-private-link-speech.cognitiveservices.azure.com` come nome DNS della risorsa vocale di esempio (dominio personalizzato). Il nome di dominio personalizzato `my-private-link-speech.cognitiveservices.azure.com` nell'esempio appartiene alla risorsa vocale creata nell'area Europa occidentale.

Per ottenere l'elenco delle voci supportate nell'area, eseguire la richiesta seguente:

```http
https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list
```
Per altri dettagli, vedere la [documentazione dell'API REST di sintesi vocale](rest-text-to-speech.md).

Per la risorsa di riconoscimento vocale abilitata per l'endpoint privato, è necessario modificare l'URL dell'endpoint per la stessa operazione. La stessa richiesta sarà simile alla seguente:

```http
https://my-private-link-speech.cognitiveservices.azure.com/tts/cognitiveservices/voices/list
```
Vedere una spiegazione dettagliata nella sottosezione dell' [URL di costruzione dell'endpoint](#construct-endpoint-url) per l'SDK di riconoscimento vocale.

#### <a name="speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk"></a>Risorsa vocale con un nome di dominio personalizzato e un endpoint privato: utilizzo con l'SDK di riconoscimento vocale

L'uso dell'SDK di riconoscimento vocale con un nome di dominio personalizzato e risorse vocali abilitate per endpoint privati richiede di rivedere e probabilmente modificare il codice dell'applicazione.

`my-private-link-speech.cognitiveservices.azure.com`Per questa sezione verrà usato come nome DNS della risorsa vocale di esempio (dominio personalizzato).

##### <a name="construct-endpoint-url"></a>Costruisci URL endpoint

In genere negli scenari SDK (nonché nell'API REST di sintesi vocale per gli scenari di API REST per audio e sintesi vocale brevi), le risorse vocali usano gli endpoint regionali dedicati per le diverse offerte di servizio. Il formato del nome DNS per questi endpoint è:

`{region}.{speech service offering}.speech.microsoft.com`

Un nome DNS di esempio è:

`westeurope.stt.speech.microsoft.com`

Tutti i valori possibili per l'area (primo elemento del nome DNS) sono elencati nelle [aree supportate del servizio voce](regions.md). (Vedere [questo articolo](sovereign-clouds.md) per gli endpoint di Azure per enti pubblici e Azure Cina). La tabella seguente presenta i valori possibili per l'offerta di servizi vocali (secondo elemento del nome DNS):

| Valore nome DNS | Offerta di servizi vocali                                    |
|----------------|-------------------------------------------------------------|
| `commands`     | [Comandi personalizzati](custom-commands.md)                       |
| `convai`       | [Trascrizione conversazione](conversation-transcription.md) |
| `s2s`          | [Traduzione vocale](speech-translation.md)                 |
| `stt`          | [Riconoscimento vocale](speech-to-text.md)                         |
| `tts`          | [Sintesi vocale](text-to-speech.md)                         |
| `voice`        | [Voce personalizzata](how-to-custom-voice.md)                      |

Quindi, l'esempio precedente ( `westeurope.stt.speech.microsoft.com` ) sta per un endpoint di riconoscimento vocale nell'Europa occidentale.

Gli endpoint abilitati per gli endpoint privati comunicano con i servizi vocali tramite un proxy speciale. Per questo motivo, *è necessario modificare gli URL di connessione dell'endpoint*. 

Un URL di endpoint "standard" ha un aspetto simile al seguente: <p/>`{region}.{speech service offering}.speech.microsoft.com/{URL path}`

Un URL di endpoint privato ha un aspetto simile al seguente: <p/>`{your custom name}.cognitiveservices.azure.com/{speech service offering}/{URL path}`

**Esempio 1.** Un'applicazione sta comunicando usando l'URL seguente (riconoscimento vocale usando il modello di base per l'inglese Stati Uniti nell'Europa occidentale):

```
wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Per usarlo nello scenario abilitato per l'endpoint privato quando il nome di dominio personalizzato della risorsa vocale è `my-private-link-speech.cognitiveservices.azure.com` , è necessario modificare l'URL in questo modo:

```
wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Si notino i dettagli:

- Il nome host `westeurope.stt.speech.microsoft.com` viene sostituito dal nome host del dominio personalizzato `my-private-link-speech.cognitiveservices.azure.com` .
- Il secondo elemento del nome DNS originale ( `stt` ) diventa il primo elemento del percorso URL e precede il percorso originale. L'URL originale `/speech/recognition/conversation/cognitiveservices/v1?language=en-US` diventa quindi `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` .

**Esempio 2.** Un'applicazione usa l'URL seguente per sintetizzare la sintesi vocale nell'Europa occidentale usando un modello Voice personalizzato:
```http
https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

L'URL equivalente seguente usa un endpoint privato abilitato, in cui il nome di dominio personalizzato della risorsa vocale è `my-private-link-speech.cognitiveservices.azure.com` :

```http
https://my-private-link-speech.cognitiveservices.azure.com/voice/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

Viene applicato lo stesso principio nell'esempio 1, ma questa volta è l'elemento chiave `voice` .

##### <a name="modifying-applications"></a>Modifica di applicazioni

Per modificare il codice, attenersi alla procedura seguente:

1. Determinare l'URL dell'endpoint dell'applicazione:

   - [Abilitare la registrazione per l'applicazione](how-to-use-logging.md) ed eseguirla per registrare l'attività.
   - Nel file di log cercare `SPEECH-ConnectionUrl` . Nelle righe corrispondenti il `value` parametro contiene l'URL completo usato dall'applicazione per raggiungere i servizi di riconoscimento vocale.

   Esempio:

   ```
   (114917): 41ms SPX_DBG_TRACE_VERBOSE:  property_bag_impl.cpp:138 ISpxPropertyBagImpl::LogPropertyAndValue: this=0x0000028FE4809D78; name='SPEECH-ConnectionUrl'; value='wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?traffictype=spx&language=en-US'
   ```

   Quindi, l'URL usato dall'applicazione in questo esempio è:

   ```
   wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
   ```

2. Creare un' `SpeechConfig` istanza di utilizzando un URL completo dell'endpoint:

   1. Modificare l'endpoint appena determinato, come descritto nella sezione precedente costruire l' [URL dell'endpoint](#construct-endpoint-url) .

   1. Modificare la modalità di creazione dell'istanza di `SpeechConfig` . È probabile che l'applicazione stia usando un codice simile al seguente:
      ```csharp
      var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
      ```
      Questa operazione non funzionerà per una risorsa vocale abilitata per l'endpoint privato a causa delle modifiche apportate al nome host e all'URL descritte nelle sezioni precedenti. Se si prova a eseguire l'applicazione esistente senza apportare alcuna modifica usando la chiave di una risorsa abilitata per gli endpoint privati, viene ricevuto un errore di autenticazione (401).

      Per renderlo funzionante, modificare la modalità di creazione dell'istanza della `SpeechConfig` classe e utilizzare l'inizializzazione "from endpoint"/"with endpoint". Si supponga che siano state definite le due variabili seguenti:
      - `subscriptionKey` contiene la chiave della risorsa vocale abilitata per l'endpoint privato.
      - `endPoint` contiene l'URL completo dell'endpoint *modificato* (usando il tipo richiesto dal linguaggio di programmazione corrispondente). In questo esempio, questa variabile deve contenere:
        ```
        wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
        ```

      Creare un'istanza di `SpeechConfig`:
      ```csharp
      var config = SpeechConfig.FromEndpoint(endPoint, subscriptionKey);
      ```
      ```cpp
      auto config = SpeechConfig::FromEndpoint(endPoint, subscriptionKey);
      ```
      ```java
      SpeechConfig config = SpeechConfig.fromEndpoint(endPoint, subscriptionKey);
      ```
      ```python
      import azure.cognitiveservices.speech as speechsdk
      speech_config = speechsdk.SpeechConfig(endpoint=endPoint, subscription=subscriptionKey)
      ```
      ```objectivec
      SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithEndpoint:endPoint subscription:subscriptionKey];
      ```

> [!TIP]
> I parametri di query specificati nell'URI dell'endpoint non vengono modificati, anche se sono impostati da altre API. Se, ad esempio, la lingua di riconoscimento viene definita nell'URI come parametro di query `language=en-US` e viene anche impostata su `ru-RU` tramite la proprietà corrispondente, viene utilizzata l'impostazione della lingua nell'URI. Il linguaggio effettivo è quindi `en-US` .
>
> I parametri impostati nell'URI dell'endpoint hanno sempre la precedenza. Altre API possono eseguire l'override solo di parametri non specificati nell'URI dell'endpoint.

Dopo questa modifica, l'applicazione dovrebbe funzionare con le risorse vocali abilitate per l'endpoint privato. Stiamo lavorando a un supporto più trasparente per gli scenari di endpoint privati.

### <a name="use-a-speech-resource-with-a-custom-domain-name-and-without-private-endpoints"></a>Usare una risorsa vocale con un nome di dominio personalizzato e senza endpoint privati

In questo articolo sono state evidenziate diverse volte che l'abilitazione di un dominio personalizzato per una risorsa vocale è *irreversibile*. Una risorsa di questo tipo utilizzerà un modo diverso per comunicare con i servizi vocali, rispetto a quelli che usano [nomi di endpoint internazionali](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints).

Questa sezione illustra come usare una risorsa di sintesi vocale con un nome di dominio personalizzato abilitato, ma *senza* endpoint privati con le API REST di servizi vocali e l'SDK per la [sintesi vocale](speech-sdk.md). Potrebbe trattarsi di una risorsa che è stata usata una volta in uno scenario di endpoint privato, ma di cui sono stati eliminati gli endpoint privati.

#### <a name="dns-configuration"></a>Configurazione del DNS

Ricordare come un nome DNS di dominio personalizzato della risorsa di riconoscimento vocale abilitata per l'endpoint privato venga [risolto dalle reti pubbliche](#resolve-dns-from-other-networks). In questo caso, l'indirizzo IP risolto punta a un endpoint proxy per una rete virtuale. Tale endpoint viene usato per l'invio del traffico di rete alla risorsa Servizi cognitivi abilitati per gli endpoint privati.

Tuttavia, quando *tutti* gli endpoint privati delle risorse vengono rimossi (o subito dopo l'abilitazione del nome di dominio personalizzato), viene effettuato il nuovo provisioning del record CNAME della risorsa di riconoscimento vocale. Ora punta all'indirizzo IP dell' [endpoint di servizi cognitivi](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)corrispondente.

Quindi, l'output del `nslookup` comando sarà simile al seguente:
```dos
C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
Server:  UnKnown
Address:  fe80::1

Non-authoritative answer:
Name:    apimgmthskquihpkz6d90kmhvnabrx3ms3pdubscpdfk1tsx3a.cloudapp.net
Address:  13.93.122.1
Aliases:  my-private-link-speech.cognitiveservices.azure.com
          westeurope.api.cognitive.microsoft.com
          cognitiveweprod.trafficmanager.net
          cognitiveweprod.azure-api.net
          apimgmttmdjylckcx6clmh2isu2wr38uqzm63s8n4ub2y3e6xs.trafficmanager.net
          cognitiveweprod-westeurope-01.regional.azure-api.net
```
Confrontarlo con l'output di [questa sezione](#resolve-dns-from-other-networks).

#### <a name="speech-resource-with-a-custom-domain-name-and-without-private-endpoints-usage-with-the-rest-apis"></a>Risorsa vocale con un nome di dominio personalizzato e senza endpoint privati: utilizzo con le API REST

##### <a name="speech-to-text-rest-api-v30"></a>API REST per sintesi vocale v 3.0

L'utilizzo dell'API REST di sintesi vocale v 3.0 è completamente equivalente al caso di [risorse vocali abilitate per gli endpoint privati](#speech-to-text-rest-api-v30).

##### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>API REST per sintesi vocale per le API REST brevi audio e sintesi vocale

In questo caso, l'utilizzo dell'API REST di sintesi vocale per l'audio breve e l'utilizzo dell'API REST di sintesi vocale non presenta alcuna differenza rispetto al caso generale, con un'eccezione. Vedere la nota seguente. È necessario usare entrambe le API come descritto nell' [API REST di riconoscimento vocale per la documentazione di breve audio](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) e dell' [API REST](rest-text-to-speech.md) di sintesi vocale.

> [!NOTE]
> Quando si usa l'API REST di riconoscimento vocale per l'API REST per audio e sintesi vocale brevi negli scenari di dominio personalizzati, usare una chiave di sottoscrizione passata attraverso l' `Ocp-Apim-Subscription-Key` intestazione. (Vedere i dettagli per l' [API REST di sintesi vocale per](rest-speech-to-text.md#request-headers) l' [API REST](rest-text-to-speech.md#request-headers)per audio breve e sintesi vocale)
>
> Usare un token di autorizzazione e passarlo all'endpoint speciale tramite l' `Authorization` intestazione funzionerà *solo* se è stata abilitata l'opzione accesso a **tutte le reti** nella sezione **rete** della risorsa di riconoscimento vocale. In altri casi, si otterrà `Forbidden` `BadRequest` un errore o quando si tenta di ottenere un token di autorizzazione.

#### <a name="speech-resource-with-a-custom-domain-name-and-without-private-endpoints-usage-with-the-speech-sdk"></a>Risorsa vocale con un nome di dominio personalizzato e senza endpoint privati: utilizzo con l'SDK di riconoscimento vocale

L'uso dell'SDK di riconoscimento vocale con risorse vocali abilitate per il dominio personalizzato *senza* endpoint privati è equivalente al caso generale, come descritto nella [documentazione di Speech SDK](speech-sdk.md).

Nel caso in cui sia stato modificato il codice per l'uso di con una [risorsa di riconoscimento vocale abilitata per endpoint privato](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk), tenere presente quanto segue.

Nella sezione sulle [risorse vocali abilitate per gli endpoint privati](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk)è stato illustrato come determinare l'URL dell'endpoint, modificarlo e fare in modo che funzioni con l'inizializzazione "from endpoint"/"with endpoint" dell' `SpeechConfig` istanza della classe.

Tuttavia, se si tenta di eseguire la stessa applicazione dopo la rimozione di tutti gli endpoint privati (per un certo periodo di tempo per il nuovo provisioning dei record DNS), si otterrà un errore interno del servizio (404). Il motivo è che il [record DNS](#dns-configuration) punta ora all'endpoint di servizi cognitivi regionali anziché al proxy della rete virtuale e i percorsi URL come `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` non verranno trovati.

È necessario eseguire il rollback dell'applicazione alla creazione di un'istanza standard di `SpeechConfig` nello stile del codice seguente:

```csharp
var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
```

## <a name="pricing"></a>Prezzi

Per informazioni dettagliate sui prezzi, vedere [Prezzi di Collegamento privato di Azure](https://azure.microsoft.com/pricing/details/private-link).

## <a name="learn-more"></a>Altre informazioni

* [Collegamento privato di Azure](../../private-link/private-link-overview.md)
* [Speech SDK](speech-sdk.md)
* [API REST di riconoscimento vocale](rest-speech-to-text.md)
* [API REST di sintesi vocale](rest-text-to-speech.md)