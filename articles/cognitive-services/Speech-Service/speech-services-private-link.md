---
title: Come usare endpoint privati con il servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare il servizio di riconoscimento vocale con endpoint privati forniti dal collegamento privato di Azure
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: alexeyo
ms.openlocfilehash: d5822b6eeecfc61a5092519618ddfcaf88a625ae
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98018531"
---
# <a name="use-speech-service-through-a-private-endpoint"></a>Usare il servizio riconoscimento vocale tramite un endpoint privato

Il [collegamento privato di Azure](../../private-link/private-link-overview.md) consente di connettersi ai servizi in Azure usando un [endpoint privato](../../private-link/private-endpoint-overview.md).
Un endpoint privato è un indirizzo IP privato accessibile solo all'interno di una [rete virtuale](../../virtual-network/virtual-networks-overview.md) e una subnet specifiche.

Questo articolo illustra come configurare e usare endpoint privati e di collegamento privato con servizi di riconoscimento vocale cognitivi di Azure.

> [!NOTE]
> Questo articolo illustra le specifiche relative alla configurazione e all'uso del collegamento privato con i servizi di riconoscimento vocale cognitivo di Azure. Prima di procedere, vedere come [usare le reti virtuali con servizi cognitivi](../cognitive-services-virtual-networks.md).

Per usare un servizio di riconoscimento vocale tramite un endpoint privato, eseguire le attività seguenti:

1. [Crea nome di dominio personalizzato per la risorsa vocale](#create-a-custom-domain-name)
2. [Creare e configurare endpoint privati](#enable-private-endpoints)
3. [Modificare le applicazioni e le soluzioni esistenti](#use-speech-resource-with-custom-domain-name-and-private-endpoint-enabled)

Per rimuovere gli endpoint privati in un secondo momento, ma continuare a usare la risorsa vocale, si eseguiranno le attività disponibili in [questa sezione](#use-speech-resource-with-custom-domain-name-without-private-endpoints).

## <a name="create-a-custom-domain-name"></a>Creare un nome di dominio personalizzato

Per gli endpoint privati è necessario un [nome di sottodominio personalizzato di servizi cognitivi](../cognitive-services-custom-subdomains.md). Seguire le istruzioni riportate di seguito per crearne uno per la risorsa vocale.

> [!WARNING]
> Una risorsa vocale con nome di dominio personalizzato abilitato usa un modo diverso per interagire con il servizio di riconoscimento vocale.
> Probabilmente è necessario modificare il codice dell'applicazione sia per gli scenari di [endpoint privato abilitato](#use-speech-resource-with-custom-domain-name-and-private-endpoint-enabled) che per quelli [ **non** abilitati per gli endpoint privati](#use-speech-resource-with-custom-domain-name-without-private-endpoints) .
>
> Quando si Abilita un nome di dominio personalizzato, l'operazione [**non è reversibile**](../cognitive-services-custom-subdomains.md#can-i-change-a-custom-domain-name). L'unico modo per tornare al nome della [regione](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) è creare una nuova risorsa di riconoscimento vocale.
>
> Se la risorsa vocale dispone di numerosi modelli e progetti personalizzati associati creati tramite [speech studio](https://speech.microsoft.com/) , è **consigliabile provare** la configurazione con una risorsa di test prima di modificare la risorsa usata nell'ambiente di produzione.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Per creare un nome di dominio personalizzato tramite portale di Azure, attenersi alla procedura seguente:

1. Passare a [portale di Azure](https://portal.azure.com/) e accedere al proprio account Azure.
1. Selezionare la risorsa vocale richiesta.
1. Nel gruppo **Gestione risorse** nel riquadro di spostamento a sinistra fare clic su **rete**.
1. Nella scheda **firewall e reti virtuali** fare clic su **genera nome di dominio personalizzato**. Viene visualizzato un nuovo pannello a destra con le istruzioni per creare un sottodominio personalizzato univoco per la risorsa.
1. Nel pannello genera nome dominio personalizzato immettere una parte del nome di dominio personalizzato. Il dominio personalizzato completo sarà simile al seguente: `https://{your custom name}.cognitiveservices.azure.com` . 
    **Dopo aver creato un nome di dominio personalizzato, non è _possibile_ modificarlo. Leggere di nuovo l'avviso sopra riportato.** Dopo aver immesso il nome di dominio personalizzato, fare clic su **Salva**.
1. Al termine dell'operazione, nel gruppo **Gestione risorse** fare clic su **chiavi ed endpoint**. Verificare che il nuovo nome dell'endpoint della risorsa venga avviato in questo modo:

    `https://{your custom name}.cognitiveservices.azure.com`

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per creare un nome di dominio personalizzato tramite PowerShell, verificare che il computer disponga di PowerShell 7. x o versione successiva con il modulo Azure PowerShell versione 5.1.0 o successiva. per visualizzare le versioni di questi strumenti, attenersi alla seguente procedura:

1. In una finestra di PowerShell digitare:

    `$PSVersionTable`

    Verificare che il valore di PSVersion sia maggiore di 7. x. Per aggiornare PowerShell, seguire le istruzioni in [installazione di varie versioni di PowerShell per l'](/powershell/scripting/install/installing-powershell) aggiornamento.

1. In una finestra di PowerShell digitare:

    `Get-Module -ListAvailable Az`

    Se non viene visualizzato nulla o se Azure PowerShell versione del modulo è inferiore a 5.1.0, seguire le istruzioni disponibili in [Install Azure PowerShell Module](/powershell/azure/install-Az-ps) to upgrade.

Prima di procedere, eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="verify-custom-domain-name-is-available"></a>Verificare che il nome di dominio personalizzato sia disponibile

Controllare se il dominio personalizzato che si vuole usare è disponibile. Seguire questa procedura per verificare che il dominio sia disponibile usando l'operazione [Controlla disponibilità dominio](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) nell'API REST di servizi cognitivi.

> [!TIP]
> Il codice riportato di seguito **non** funzionerà in Azure cloud Shell.

```azurepowershell
$subId = "Your Azure subscription Id"
$subdomainName = "custom domain name"

# Select the Azure subscription that contains Speech resource.
# You can skip this step if your Azure account has only one active subscription.
Set-AzContext -SubscriptionId $subId

# Prepare OAuth token to use in request to Cognitive Services REST API.
$Context = Get-AzContext
$AccessToken = (Get-AzAccessToken -TenantId $Context.Tenant.Id).Token
$token = ConvertTo-SecureString -String $AccessToken -AsPlainText -Force

# Prepare and send the request to Cognitive Services REST API.
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

Per abilitare il nome di dominio personalizzato per la risorsa vocale selezionata, viene usato il cmdlet [set-AzCognitiveServicesAccount](/powershell/module/az.cognitiveservices/set-azcognitiveservicesaccount) .

> [!WARNING]
> Quando il codice seguente viene eseguito correttamente, si creerà un nome di dominio personalizzato per la risorsa di riconoscimento vocale.
> Questo nome **non può** essere modificato. Per ulteriori informazioni, vedere **l'avviso** sopra riportato.

```azurepowershell
$resourceGroup = "Resource group name where Speech resource is located"
$speechResourceName = "Your Speech resource name"
$subdomainName = "custom domain name"

# Select the Azure subscription that contains Speech resource.
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

- Questa sezione richiede la versione più recente dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="verify-the-custom-domain-name-is-available"></a>Verificare che il nome di dominio personalizzato sia disponibile

Controllare se il dominio personalizzato che si vuole usare è gratuito. Si userà il metodo di [Verifica della disponibilità del dominio](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) dall'API REST di servizi cognitivi.

Copiare il blocco di codice riportato di seguito, inserire il nome di dominio personalizzato preferito e salvarlo nel file `subdomain.json` .

```json
{
    "subdomainName": "custom domain name",
    "type": "Microsoft.CognitiveServices/accounts"
}
```

Copiare il file nella cartella corrente o caricarlo Azure Cloud Shell ed eseguire il comando seguente. Sostituire `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` con l'ID sottoscrizione di Azure.

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
## <a name="enable-custom-domain-name"></a>Abilita nome di dominio personalizzato

Per abilitare il nome di dominio personalizzato per la risorsa vocale selezionata, viene usato il comando [AZ cognitiveservices account Update](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_update) .

Selezionare la sottoscrizione di Azure contenente la risorsa di riconoscimento vocale. Se l'account Azure ha solo una sottoscrizione attiva, è possibile ignorare questo passaggio. Sostituire `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` con l'ID sottoscrizione di Azure.
```azurecli-interactive
az account set --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
Consente di impostare il nome di dominio personalizzato sulla risorsa selezionata. Sostituire i valori dei parametri di esempio con quelli effettivi ed eseguire il comando seguente.

> [!WARNING]
> Al termine dell'esecuzione del comando riportato di seguito, si creerà un nome di dominio personalizzato per la risorsa di riconoscimento vocale. Questo nome **non può** essere modificato. Per altre informazioni, vedere l'avviso di attenzione sopra.

```azurecli
az cognitiveservices account update --name my-speech-resource-name --resource-group my-resource-group-name --custom-domain my-custom-name
```

**_

## <a name="enable-private-endpoints"></a>Abilita endpoint privati

È consigliabile usare la [zona DNS privata](../../dns/private-dns-overview.md) collegata alla rete virtuale con gli aggiornamenti necessari per gli endpoint privati, che vengono creati per impostazione predefinita durante il processo di provisioning. Tuttavia, se si usa il proprio server DNS, potrebbe essere necessario modificare la configurazione DNS, come illustrato in _DNS per gli endpoint privati_, di seguito. Decidere la strategia DNS _ *prima* di * eseguire il provisioning di endpoint privati per una risorsa vocale di produzione e testare le modifiche DNS, soprattutto se si usa il proprio server DNS.

Per creare endpoint privati, usare uno degli articoli seguenti. Gli articoli usano un'app Web come risorsa di esempio per abilitare con endpoint privati. Questi parametri verranno usati anziché quelli dell'articolo:

| Impostazione             | Valore                                    |
|---------------------|------------------------------------------|
| Tipo di risorsa       | **Microsoft. CognitiveServices/accounts** |
| Resource            | **\<your-speech-resource-name>**         |
| Sottorisorsa di destinazione | **account**                              |

- [Creare un endpoint privato con il portale di Azure](../../private-link/create-private-endpoint-portal.md)
- [Creare un endpoint privato usando Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)
- [Creare un endpoint privato usando l'interfaccia della riga di comando](../../private-link/create-private-endpoint-cli.md)

**DNS per endpoint privati:** Esaminare i principi generali del [DNS per gli endpoint privati nelle risorse di servizi cognitivi](../cognitive-services-virtual-networks.md#dns-changes-for-private-endpoints). Verificare quindi che la configurazione DNS funzioni correttamente eseguendo questi controlli:

### <a name="resolve-dns-from-the-virtual-network"></a>Risolvere il DNS dalla rete virtuale

Questo controllo è **obbligatorio**.

Seguire questa procedura per testare la voce DNS personalizzata dalla rete virtuale.

1. Accedere a una macchina virtuale che si trova nella rete virtuale a cui è stato collegato l'endpoint privato. 
1. Aprire il prompt dei comandi di Windows o la shell bash, eseguire `nslookup` e verificare che il nome di dominio personalizzato della risorsa venga risolto correttamente.

```dos
C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
Server:  UnKnown
Address:  168.63.129.16

Non-authoritative answer:
Name:    my-private-link-speech.privatelink.cognitiveservices.azure.com
Address:  172.28.0.10
Aliases:  my-private-link-speech.cognitiveservices.azure.com
```

3. Verificare che l'indirizzo IP corrisponda all'indirizzo IP dell'endpoint privato.

### <a name="resolve-dns-from-other-networks"></a>Risolvere il DNS da altre reti

Eseguire questa verifica solo se si prevede di usare la risorsa di riconoscimento vocale endpoint privato abilitato in modalità "ibrido", in cui è stata abilitata l'opzione di accesso a **tutte le reti** o **reti selezionate ed endpoint privati** nella sezione **rete** della risorsa. Se si prevede di accedere alla risorsa usando solo un endpoint privato, è possibile ignorare questa sezione.

1. Accedere a un computer collegato a una rete autorizzata ad accedere alla risorsa.
2. Aprire il prompt dei comandi di Windows o la shell bash, eseguire `nslookup` e verificare che il nome di dominio personalizzato della risorsa venga risolto correttamente.

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

3. Verificare che l'indirizzo IP corrisponda all'indirizzo IP dell'endpoint privato.

> [!NOTE]
> L'indirizzo IP risolto punta a un endpoint proxy della rete virtuale, che invia il traffico di rete all'endpoint privato per la risorsa Servizi cognitivi. Il comportamento sarà diverso per una risorsa con un nome di dominio personalizzato ma *senza* endpoint privati. Per informazioni dettagliate, vedere [questa sezione](#dns-configuration) .

## <a name="adjust-existing-applications-and-solutions"></a>Modificare le applicazioni e le soluzioni esistenti

Una risorsa vocale con un dominio personalizzato abilitato usa un modo diverso per interagire con i servizi di riconoscimento vocale. Questo vale per una risorsa vocale abilitata per il dominio personalizzato con e senza endpoint privati. Le informazioni contenute in questa sezione si applicano a entrambi gli scenari.

### <a name="use-speech-resource-with-custom-domain-name-and-private-endpoint-enabled"></a>Usare una risorsa vocale con un nome di dominio personalizzato e un endpoint privato abilitato

Una risorsa vocale con nome di dominio personalizzato e endpoint privato abilitato usa un modo diverso per interagire con i servizi di riconoscimento vocale. Questa sezione illustra come usare tale risorsa con l'API REST di servizi vocali e l'SDK per la [sintesi vocale](speech-sdk.md).

> [!NOTE]
> Si noti che una risorsa di sintesi vocale senza endpoint privati, ma con un **nome di dominio personalizzato** abilitato dispone anche di un metodo speciale per interagire con i servizi vocali, ma in questo modo si differenzia dallo scenario di una risorsa di sintesi vocale abilitata per un endpoint privato. Se si dispone di una risorsa di questo tipo, ad esempio se si dispone di una risorsa con endpoint privati, ma si è deciso di rimuoverli, assicurarsi di acquisire familiarità con la [sezione corrispondente](#use-speech-resource-with-custom-domain-name-without-private-endpoints).

#### <a name="speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-rest-api"></a>Risorsa vocale con nome di dominio personalizzato e endpoint privato. Uso con l'API REST

`my-private-link-speech.cognitiveservices.azure.com`Per questa sezione verrà usato come nome DNS della risorsa vocale di esempio (dominio personalizzato).

##### <a name="note-on-speech-services-rest-api"></a>Nota sull'API REST di servizi vocali

Servizi di riconoscimento vocale ha un'API REST per [sintesi vocale](rest-speech-to-text.md) e [sintesi vocale.](rest-text-to-speech.md) Per lo scenario di endpoint privato abilitato è necessario considerare quanto segue.

Il riconoscimento vocale ha due diverse API REST. Ogni API serve uno scopo diverso, USA endpoint diversi e richiede un approccio diverso quando viene usato sing in uno scenario di endpoint privato abilitato.

Le API REST per sintesi vocale sono:
- L' [API REST di riconoscimento vocale v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) viene usata per la [trascrizione](batch-transcription.md) e la [riconoscimento vocale personalizzato](custom-speech-overview.md)di batch. v 3.0 è un [successore della versione 2.0](/azure/cognitive-services/speech-service/migrate-v2-to-v3).
- Per la trascrizione OnLine viene usata l' [API REST per sintesi vocale per l'audio breve](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) . 

L'uso dell'API REST di riconoscimento vocale per l'API REST brevi per audio e sintesi vocale nello scenario di endpoint privato è lo stesso e equivalente al caso dell' [SDK di riconoscimento vocale](#speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk) descritto più avanti in questo articolo. 

L'API REST di sintesi vocale v 3.0 usa un set di endpoint diverso e quindi richiede un approccio diverso per lo scenario abilitato per gli endpoint privati.

Entrambi i casi sono descritti nelle sottosezioni successive.


##### <a name="speech-to-text-rest-api-v30"></a>API REST per sintesi vocale v 3.0

In genere le risorse vocali usano [endpoint regionali di servizi cognitivi](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) per la comunicazione con l' [API REST di sintesi vocale v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30). Queste risorse hanno il formato di denominazione seguente: <p/>`{region}.api.cognitive.microsoft.com`

Si tratta di un URL di richiesta di esempio:

```http
https://westeurope.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions
```
Dopo aver abilitato il dominio personalizzato per una risorsa vocale (necessaria per gli endpoint privati), tale risorsa userà il modello di nome DNS seguente per l'endpoint API REST di base: <p/>`{your custom name}.cognitiveservices.azure.com`

Questo significa che, in questo esempio, il nome dell'endpoint dell'API REST sarà: <p/>`my-private-link-speech.cognitiveservices.azure.com`

E l'URL della richiesta di esempio precedente devono essere convertiti in:
```http
https://my-private-link-speech.cognitiveservices.azure.com/speechtotext/v3.0/transcriptions
```
Questo URL deve essere raggiungibile dalla rete virtuale con l'endpoint privato collegato (purché la [risoluzione DNS sia corretta](#resolve-dns-from-the virtual-network)).

In genere, dopo aver abilitato il nome di dominio personalizzato per una risorsa vocale, sostituire il nome host in tutti gli URL di richiesta con il nuovo nome host del dominio personalizzato. Tutte le altre parti della richiesta, come il percorso `/speechtotext/v3.0/transcriptions` nell'esempio precedente, rimangono invariate.

> [!TIP]
> Alcuni clienti hanno sviluppato applicazioni che usano la parte Region del nome DNS dell'endpoint regionale, ad esempio per inviare la richiesta alla risorsa vocale distribuita in una determinata area di Azure.
>
> Il nome di dominio personalizzato della risorsa vocale **non** contiene informazioni sull'area in cui è distribuita la risorsa. Quindi, la logica dell'applicazione descritta in precedenza **non** funzionerà e deve essere modificata.

##### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>API REST per sintesi vocale per le API REST brevi audio e sintesi vocale

[API REST di sintesi vocale per](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) le [API REST](rest-text-to-speech.md) brevi audio e sintesi vocale usare due tipi di endpoint:
- [Endpoint regionali di servizi cognitivi](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) per la comunicazione con l'API REST di servizi cognitivi per ottenere un token di autorizzazione
- Endpoint speciali per tutte le altre operazioni

La descrizione dettagliata degli endpoint speciali e il modo in cui deve essere trasformato l'URL per una risorsa di riconoscimento vocale con endpoint privato sono disponibili in [questa](#general-principle) sottosezione della sezione "utilizzo con l'SDK di riconoscimento vocale" riportata di seguito. Lo stesso principio descritto per l'SDK è valido per le API REST di sintesi vocale v 1.0 e sintesi vocale.

Acquisire familiarità con il materiale nella sottosezione indicata nel paragrafo precedente e vedere l'esempio seguente. Nell'esempio viene descritta l'API REST di sintesi vocale; l'utilizzo dell'API REST di riconoscimento vocale per l'audio breve è completamente equivalente

> [!NOTE]
> Quando si usa l' **API REST di sintesi vocale per l'audio breve** negli scenari di endpoint privati, usare un token di autorizzazione [passato](rest-speech-to-text.md#request-headers) all' `Authorization` [intestazione](rest-speech-to-text.md#request-headers). Il passaggio della chiave di sottoscrizione vocale all'endpoint speciale tramite `Ocp-Apim-Subscription-Key` intestazione **non** funzionerà e genererà l'errore 401.

**Esempio di utilizzo dell'API REST di sintesi vocale.**

L'Europa occidentale viene usata come area di Azure di esempio e `my-private-link-speech.cognitiveservices.azure.com` come nome DNS della risorsa vocale di esempio (dominio personalizzato). Il nome di dominio personalizzato `my-private-link-speech.cognitiveservices.azure.com` nell'esempio appartiene alla risorsa vocale creata nell'area Europa occidentale.

Per ottenere l'elenco delle voci supportate nell'area, è necessario eseguire le due operazioni seguenti:

- Ottenere il token di autorizzazione:
```http
https://westeurope.api.cognitive.microsoft.com/sts/v1.0/issuetoken
```
- Utilizzando il token, ottenere l'elenco di voci:
```http
https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list
```
(Per altre informazioni sui passaggi precedenti, vedere la [documentazione dell'API REST di sintesi vocale](rest-text-to-speech.md))

Per la risorsa di riconoscimento vocale endpoint privato abilitato è necessario modificare gli URL dell'endpoint per la stessa sequenza di operazioni. La stessa sequenza sarà simile alla seguente:
- Ottenere il token di autorizzazione tramite
```http
https://my-private-link-speech.cognitiveservices.azure.com/v1.0/issuetoken
```
(per informazioni dettagliate, vedere la sezione precedente relativa all' [API REST di sintesi vocale v 3.0](#speech-to-text-rest-api-v30) )
- Utilizzando il token ottenuto ottenere l'elenco di voci tramite
```http
https://my-private-link-speech.cognitiveservices.azure.com/tts/cognitiveservices/voices/list
```
(vedere la spiegazione dettagliata nella sottosezione [generale](#general-principle) di "uso con l'SDK di riconoscimento vocale" riportata di seguito)

#### <a name="speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk"></a>Risorsa vocale con nome di dominio personalizzato e endpoint privato. Utilizzo con l'SDK di riconoscimento vocale

L'uso dell'SDK di riconoscimento vocale con il nome di dominio personalizzato e le risorse vocali abilitate per endpoint privato richiede la revisione e le modifiche probabilmente del codice dell'applicazione.

`my-private-link-speech.cognitiveservices.azure.com`Per questa sezione verrà usato come nome DNS della risorsa vocale di esempio (dominio personalizzato).

##### <a name="general-principle"></a>Principio generale

In genere negli scenari SDK (oltre che negli scenari dell'API REST di sintesi vocale), le risorse vocali usano gli endpoint regionali dedicati per le diverse offerte di servizio. Il formato del nome DNS per questi endpoint è: </p>`{region}.{speech service offering}.speech.microsoft.com`

Esempio: </p>`westeurope.stt.speech.microsoft.com`

Tutti i valori possibili per l'area (primo elemento del nome DNS) sono elencati [qui](regions.md) la tabella seguente presenta il valore possibile per l'offerta di servizi vocali (secondo elemento del nome DNS):

| Valore nome DNS | Offerta di servizi vocali                                    |
|----------------|-------------------------------------------------------------|
| `commands`     | [Comandi personalizzati](custom-commands.md)                       |
| `convai`       | [Trascrizione conversazione](conversation-transcription.md) |
| `s2s`          | [Traduzione vocale](speech-translation.md)                 |
| `stt`          | [Riconoscimento vocale](speech-to-text.md)                         |
| `tts`          | [Sintesi vocale](text-to-speech.md)                         |
| `voice`        | [Voce personalizzata](how-to-custom-voice.md)                      |

Quindi, l'esempio precedente ( `westeurope.stt.speech.microsoft.com` ) sta per l'endpoint di riconoscimento vocale nell'Europa occidentale.

Gli endpoint privati abilitati per l'endpoint comunicano con servizi vocali tramite un proxy speciale e per questo motivo **è necessario modificare gli URL di connessione dell'endpoint**. 

Un URL di endpoint "standard" ha un aspetto simile al seguente: <p/>`{region}.{speech service offering}.speech.microsoft.com/{URL path}`

Un URL di endpoint privato ha un aspetto simile al seguente: <p/>`{your custom name}.cognitiveservices.azure.com/{speech service offering}/{URL path}`

**Esempio 1.** L'applicazione sta comunicando usando l'URL seguente (riconoscimento vocale usando il modello di base per l'inglese Stati Uniti nell'Europa occidentale):

```
wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Per usarlo nello scenario di endpoint privato abilitato quando il nome di dominio personalizzato della risorsa vocale è è `my-private-link-speech.cognitiveservices.azure.com` necessario modificare l'URL in questo modo:

```
wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Si notino i dettagli:

- Hostname `westeurope.stt.speech.microsoft.com` viene sostituito dal nome host del dominio personalizzato `my-private-link-speech.cognitiveservices.azure.com` .
- Il secondo elemento del nome DNS originale ( `stt` ) diventa il primo elemento del percorso URL e precede il percorso originale. L'URL originale `/speech/recognition/conversation/cognitiveservices/v1?language=en-US` diventa quindi `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` .

**Esempio 2.** L'applicazione usa l'URL seguente per sintetizzare la sintesi vocale nell'Europa occidentale usando un modello Voice personalizzato:
```http
https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

Di seguito è riportato un URL equivalente che usa un endpoint privato abilitato dove il nome di dominio personalizzato della risorsa vocale è `my-private-link-speech.cognitiveservices.azure.com` :

```http
https://my-private-link-speech.cognitiveservices.azure.com/voice/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

Viene applicato lo stesso principio dell'esempio 1, ma questa volta è l'elemento chiave `voice` .

##### <a name="modify-applications"></a>Modificare le applicazioni

Per modificare il codice, attenersi alla procedura seguente:

**1. determinare l'URL dell'endpoint dell'applicazione**

- [Abilitare la registrazione per l'applicazione](how-to-use-logging.md) ed eseguirla per registrare l'attività.
- Nel file di log cercare `SPEECH-ConnectionUrl` . Nelle righe corrispondenti il `value` parametro contiene l'URL completo usato dall'applicazione per raggiungere il servizio di riconoscimento vocale.

Esempio:

```
(114917): 41ms SPX_DBG_TRACE_VERBOSE:  property_bag_impl.cpp:138 ISpxPropertyBagImpl::LogPropertyAndValue: this=0x0000028FE4809D78; name='SPEECH-ConnectionUrl'; value='wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?traffictype=spx&language=en-US'
```

Quindi, l'URL usato dall'applicazione in questo esempio è:

```
wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

**2. creare un' `SpeechConfig` istanza usando l'URL completo dell'endpoint**

Modificare l'endpoint specificato nella sezione precedente, come descritto in [principio generale](#general-principle) sopra.

Modificare ora la modalità di creazione dell'istanza di `SpeechConfig` . È molto probabile che l'applicazione odierna stia usando un codice simile al seguente:
```csharp
var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
```
Questa operazione non funzionerà per la risorsa di sintesi vocale abilitata per endpoint privati a causa delle modifiche del nome host e dell'URL descritte nelle sezioni precedenti. Se si prova a eseguire l'applicazione esistente senza alcuna modifica usando la chiave di una risorsa abilitata per l'endpoint privato, si otterrà un errore di autenticazione (401).

Per renderlo funzionante, modificare la modalità di creazione `SpeechConfig` dell'istanza della classe e utilizzare l'inizializzazione "da endpoint"/"con endpoint". Si supponga che siano state definite le due variabili seguenti:
- `subscriptionKey` che contiene la chiave della risorsa di riconoscimento vocale endpoint privato abilitato
- `endPoint` contenente l'URL completo dell'endpoint **modificato** (usando il tipo richiesto dal linguaggio di programmazione corrispondente). Nell'esempio questa variabile deve contenere
```
wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Successivamente, creare un' `SpeechConfig` istanza:
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
> I parametri di query specificati nell'URI dell'endpoint non vengono modificati, anche se sono impostati da qualsiasi altra API. Se, ad esempio, la lingua di riconoscimento viene definita nell'URI come parametro di query "Language = en-US" e viene anche impostata su "ur-ur" tramite la proprietà corrispondente, viene utilizzata l'impostazione della lingua nell'URI e la lingua valida è "en-US". I parametri impostati nell'URI dell'endpoint accettano sempre precidence. Solo i parametri non specificati nell'URI dell'endpoint possono essere sostituiti da altre API.

Dopo questa modifica, l'applicazione dovrebbe funzionare con le risorse di riconoscimento vocale private Enabled. Stiamo lavorando a un supporto più trasparente dello scenario di endpoint privato.

### <a name="use-speech-resource-with-custom-domain-name-without-private-endpoints"></a>Usare la risorsa vocale con un nome di dominio personalizzato senza endpoint privati

In questo articolo sono state illustrate diverse volte che l'abilitazione di un dominio personalizzato per una risorsa vocale è **irreversibile** e tale risorsa utilizzerà un modo diverso per comunicare con i servizi di riconoscimento vocale che si confrontano con quelli "normali", ovvero quelli che usano [nomi di endpoint internazionali](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints).

Questa sezione illustra come usare una risorsa di riconoscimento vocale con un nome di dominio personalizzato abilitato, ma **senza** endpoint privati con l'API REST di servizi vocali e l'SDK per la [sintesi vocale](speech-sdk.md). Potrebbe trattarsi di una risorsa che è stata usata una volta in uno scenario di endpoint privato, ma ne ha eliminato gli endpoint privati.

#### <a name="dns-configuration"></a>Configurazione del DNS

Ricordare come un nome DNS di dominio personalizzato della risorsa di riconoscimento vocale dell'endpoint privato venga [risolto dalle reti pubbliche](#resolve-dns-from-other-networks). In questo caso l'indirizzo IP risolto punta a un endpoint proxy VNet, che viene usato per l'invio del traffico di rete alla risorsa di servizi cognitivi abilitata per l'endpoint privato.

Tuttavia, quando si riesegue il provisioning di **tutti** gli endpoint privati delle risorse (o subito dopo l'abilitazione del nome di dominio personalizzato), viene eseguito il provisioning dell'indirizzo IP dell' [endpoint di servizi cognitivi](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)corrispondente.

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

#### <a name="speech-resource-with-custom-domain-name-without-private-endpoints-usage-with-rest-api"></a>Risorsa vocale con nome di dominio personalizzato senza endpoint privati. Uso con l'API REST

##### <a name="speech-to-text-rest-api-v30"></a>API REST per sintesi vocale v 3.0

L'utilizzo dell'API REST di sintesi vocale v 3.0 è completamente equivalente al caso di [risorse vocali abilitate per gli endpoint privati](#speech-to-text-rest-api-v30).

##### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>API REST per sintesi vocale per le API REST brevi audio e sintesi vocale

In questo caso l'API REST di riconoscimento vocale per l'utilizzo di brevi audio e l'API REST di sintesi vocale non presenta alcuna differenza rispetto al caso generale con un'eccezione per l'API REST di riconoscimento vocale per l'audio breve (vedere la nota di seguito). Entrambe le API devono essere usate come descritto in [API REST di sintesi vocale per la documentazione dell'API REST per audio breve](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) e sintesi [vocale](rest-text-to-speech.md) .

> [!NOTE]
> Quando si usa l' **API REST di sintesi vocale per l'audio breve** negli scenari di dominio personalizzato, usare un token di autorizzazione [passato](rest-speech-to-text.md#request-headers) all' `Authorization` [intestazione](rest-speech-to-text.md#request-headers). Il passaggio della chiave di sottoscrizione vocale all'endpoint speciale tramite `Ocp-Apim-Subscription-Key` intestazione **non** funzionerà e genererà l'errore 401.

#### <a name="speech-resource-with-custom-domain-name-without-private-endpoints-usage-with-speech-sdk"></a>Risorsa vocale con nome di dominio personalizzato senza endpoint privati. Utilizzo con l'SDK di riconoscimento vocale

L'uso dell'SDK vocale con il nome di dominio personalizzato abilitato per le risorse vocali **senza** endpoint privati richiede la revisione e probabilmente modifiche del codice dell'applicazione. Si noti che queste modifiche sono **diverse** rispetto al caso di una [risorsa di riconoscimento vocale abilitata per endpoint privato](#speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk). Stiamo lavorando a un supporto più trasparente dello scenario privato di endpoint/domini personalizzati.

`my-private-link-speech.cognitiveservices.azure.com`Per questa sezione verrà usato come nome DNS della risorsa vocale di esempio (dominio personalizzato).

Nella sezione relativa alla [risorsa di riconoscimento vocale per endpoint privato](#speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk) è stato illustrato come determinare l'URL dell'endpoint usato, modificarlo e fare in modo che funzioni con l'inizializzazione "from endpoint"/"with endpoint" dell' `SpeechConfig` istanza della classe.

Tuttavia, se si tenta di eseguire la stessa applicazione dopo la rimozione di tutti gli endpoint privati (per un certo periodo di tempo per il nuovo provisioning dei record DNS), si otterrà un errore interno del servizio (404). Il motivo è il [record DNS](#dns-configuration) che ora punta all'endpoint di servizi cognitivi regionali anziché al proxy VNet e i percorsi URL come `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` non verranno trovati, di conseguenza l'errore "non trovato" (404).

Se si esegue il rollback dell'applicazione nella creazione di un'istanza "standard" di `SpeechConfig` nello stile di
```csharp
var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
```
l'applicazione verrà terminata con l'errore di autenticazione (401).

##### <a name="modifying-applications"></a>Modifica di applicazioni

Per consentire all'applicazione di usare una risorsa vocale con un nome di dominio personalizzato e senza endpoint privati, attenersi alla procedura seguente:

**1. richiedere il token di autorizzazione dall'API REST di servizi cognitivi**

[Questo articolo](../authentication.md#authenticate-with-an-authentication-token) illustra come ottenere il token usando l'API REST di servizi cognitivi.

Usare il nome di dominio personalizzato nell'URL dell'endpoint, in questo esempio l'URL è:
```http
https://my-private-link-speech.cognitiveservices.azure.com/sts/v1.0/issueToken
```
> [!TIP]
> Questo URL è reperibile in portale di Azure. Nella pagina delle risorse vocali, sotto il gruppo **Gestione risorse** selezionare **chiavi ed endpoint**.

**2. creare un' `SpeechConfig` istanza usando il metodo "from token di autorizzazione"/"con token di autorizzazione".**

Creare un' `SpeechConfig` istanza usando il token di autorizzazione ottenuto nella sezione precedente. Si supponga che siano state definite le variabili seguenti:

- `token`: token di autorizzazione ottenuto nella sezione precedente
- `azureRegion`: nome dell' [area](regions.md) della risorsa vocale (esempio: `westeurope` )
- `outError`: (solo per caso [Objective C](/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithauthorizationtokenregionerror) )

Successivamente, creare un' `SpeechConfig` istanza:

```csharp
var config = SpeechConfig.FromAuthorizationToken(token, azureRegion);
```
```cpp
auto config = SpeechConfig::FromAuthorizationToken(token, azureRegion);
```
```java
SpeechConfig config = SpeechConfig.fromAuthorizationToken(token, azureRegion);
```
```python
import azure.cognitiveservices.speech as speechsdk
speech_config = speechsdk.SpeechConfig(auth_token=token, region=azureRegion)
```
```objectivec
SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithAuthorizationToken:token region:azureRegion error:outError];
```
> [!NOTE]
> Il chiamante deve verificare che il token di autorizzazione sia valido.
> Prima della scadenza del token di autorizzazione, il chiamante deve aggiornarlo chiamando questo Setter con un nuovo token valido.
> Quando vengono copiati i valori di configurazione durante la creazione di un nuovo sistema di riconoscimento o di un sintetizzatore, il nuovo valore del token non verrà applicato ai sistemi di riconoscimento o ai sintetizzatori già creati.
> Per questi elementi, impostare il token di autorizzazione del riconoscimento o del sintetizzatore corrispondente per aggiornare il token.
> Se non si aggiorna il token, il riconoscitore o il sintetizzatore rileverà errori durante il funzionamento.

Dopo questa modifica, l'applicazione dovrebbe funzionare con le risorse vocali che usano un nome di dominio personalizzato senza endpoint privati.

## <a name="pricing"></a>Prezzi

Per informazioni dettagliate sui prezzi, vedere [Prezzi di Collegamento privato di Azure](https://azure.microsoft.com/pricing/details/private-link).

## <a name="learn-more"></a>Altre informazioni

* [Collegamento privato di Azure](../../private-link/private-link-overview.md)
* [Speech SDK](speech-sdk.md)
* [API REST di riconoscimento vocale](rest-speech-to-text.md)
* [API REST di sintesi vocale](rest-text-to-speech.md)
