---
title: Uso di servizi di riconoscimento vocale con endpoint privati
titleSuffix: Azure Cognitive Services
description: HowTo sull'uso di servizi di riconoscimento vocale con endpoint privati forniti dal collegamento privato di Azure
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: alexeyo
ms.openlocfilehash: c88a7820518d0a73bfb0e93d3b364190207b8f90
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97051221"
---
# <a name="using-speech-services-with-private-endpoints-provided-by-azure-private-link"></a>Uso di servizi di riconoscimento vocale con endpoint privati forniti dal collegamento privato di Azure

Il [collegamento privato di Azure](../../private-link/private-link-overview.md) consente di connettersi a diversi servizi PaaS in Azure tramite un [endpoint privato](../../private-link/private-endpoint-overview.md). Un endpoint privato è un indirizzo IP privato all'interno di una [rete virtuale](../../virtual-network/virtual-networks-overview.md) e una subnet specifiche.

Questo articolo illustra come configurare e usare endpoint privati e di collegamento privato con servizi di riconoscimento vocale cognitivi di Azure. 

> [!NOTE]
> Questo articolo illustra le specifiche relative alla configurazione e all'uso del collegamento privato con i servizi di riconoscimento vocale cognitivo di Azure. Prima di continuare, acquisire familiarità con l'articolo generale sull' [uso delle reti virtuali con servizi cognitivi](../cognitive-services-virtual-networks.md).

Per abilitare una risorsa vocale per gli scenari di endpoint privato, è necessario eseguire le attività seguenti:
- [Crea nome di dominio personalizzato per la risorsa vocale](#create-custom-domain-name)
- [Creare e configurare endpoint privati](#enabling-private-endpoints)
- [Modificare le applicazioni e le soluzioni esistenti](#using-speech-resource-with-custom-domain-name-and-private-endpoint-enabled)

Se successivamente si decide di rimuovere tutti gli endpoint privati, ma si continua a usare la risorsa, le azioni necessarie sono descritte in [questa sezione](#using-speech-resource-with-custom-domain-name-without-private-endpoints).

## <a name="create-custom-domain-name"></a>Crea nome di dominio personalizzato

Gli endpoint privati richiedono l'utilizzo di [nomi di sottodominio personalizzati di servizi cognitivi](../cognitive-services-custom-subdomains.md). Usare le istruzioni seguenti per crearne uno per la risorsa vocale.

> [!WARNING]
> Una risorsa vocale con nome di dominio personalizzato abilitato usa un modo diverso per interagire con i servizi di riconoscimento vocale. Probabilmente sarà necessario modificare il codice dell'applicazione sia per gli scenari di [endpoint privato abilitato](#using-speech-resource-with-custom-domain-name-and-private-endpoint-enabled) che per quelli [ **non** abilitati per gli endpoint privati](#using-speech-resource-with-custom-domain-name-without-private-endpoints) .
>
> Il funzionamento dell'abilitazione del nome di dominio personalizzato [**non è reversibile**](../cognitive-services-custom-subdomains.md#can-i-change-a-custom-domain-name). L'unico modo per tornare al nome della [regione](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) è creare una nuova risorsa di riconoscimento vocale. 
>
> In particolare nei casi in cui la risorsa vocale dispone di numerosi modelli e progetti personalizzati associati creati tramite [speech studio](https://speech.microsoft.com/) , si consiglia **vivamente** di provare la configurazione con una risorsa di test e solo di modificare quella usata nell'ambiente di produzione.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

- Passare a [portale di Azure](https://portal.azure.com/) e accedere al proprio account Azure
- Selezionare la risorsa vocale obbligatoria
- Selezione *rete* (gruppo di *Gestione risorse* ) 
- Nella scheda *firewall e reti virtuali* (impostazione predefinita) fare clic sul pulsante **genera nome di dominio personalizzato**
- Verrà visualizzato un nuovo pannello con le istruzioni per creare un sottodominio personalizzato univoco per la risorsa
> [!WARNING]
> Dopo aver creato un nome di dominio personalizzato, non è **possibile** modificarlo. Per ulteriori informazioni, vedere l'avviso sopra riportato.
- Al termine dell'operazione, potrebbe essere necessario selezionare *chiavi ed endpoint* (gruppo di *Gestione risorse* ) e verificare il nome del nuovo endpoint della risorsa nel formato `{your custom name}.cognitiveservices.azure.com`

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Questa sezione richiede l'esecuzione locale di PowerShell 7. x o versione successiva con il modulo Azure PowerShell versione 5.1.0 o successiva. Eseguire `Get-Module -ListAvailable Az` per trovare la versione installata. Se è necessario installare o aggiornare, vedere [install Azure PowerShell Module](/powershell/azure/install-Az-ps) .

Prima di continuare l'esecuzione `Connect-AzAccount` per creare una connessione con Azure.

## <a name="verify-custom-domain-name-availability"></a>Verificare la disponibilità del nome di dominio personalizzato

È necessario verificare se il dominio personalizzato che si vuole usare è gratuito. Si userà il metodo di [Verifica della disponibilità del dominio](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) dall'API REST di servizi cognitivi. Vedere i commenti nel blocco di codice riportato di seguito per illustrare i passaggi.

> [!TIP]
> Il codice riportato di seguito **non** funzionerà in Azure cloud Shell.

```azurepowershell
$subId = "Your Azure subscription Id"
$subdomainName = "custom domain name"

# Select the Azure subscription containing Speech resource
# If your Azure account has only one active subscription
# you can skip this step
Set-AzContext -SubscriptionId $subId

# Preparing OAuth token which is used in request
# to Cognitive Services REST API
$Context = Get-AzContext
$AccessToken = (Get-AzAccessToken -TenantId $Context.Tenant.Id).Token
$token = ConvertTo-SecureString -String $AccessToken -AsPlainText -Force

# Preparing and executing the request to Cognitive Services REST API
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
Se il nome desiderato è disponibile, verrà restituita una risposta simile alla seguente:
```azurepowershell
isSubdomainAvailable : True
reason               :
type                 :
subdomainName        : my-custom-name
```
Se il nome è già stato preso, si otterrà la risposta seguente:
```azurepowershell
isSubdomainAvailable : False
reason               : Sub domain name 'my-custom-name' is already used. Please pick a different name.
type                 :
subdomainName        : my-custom-name
```
## <a name="enabling-custom-domain-name"></a>Abilitazione del nome di dominio personalizzato

Per abilitare il nome di dominio personalizzato per la risorsa vocale selezionata, viene usato il cmdlet [set-AzCognitiveServicesAccount](/powershell/module/az.cognitiveservices/set-azcognitiveservicesaccount) . Vedere i commenti nel blocco di codice riportato di seguito per illustrare i passaggi.

> [!WARNING]
> Al termine dell'esecuzione del codice riportato di seguito, si creerà un nome di dominio personalizzato per la risorsa di riconoscimento vocale. Questo nome **non può** essere modificato. Per ulteriori informazioni, vedere l'avviso sopra riportato.

```azurepowershell
$resourceGroup = "Resource group name where Speech resource is located"
$speechResourceName = "Your Speech resource name"
$subdomainName = "custom domain name"

# Select the Azure subscription containing Speech resource
# If your Azure account has only one active subscription
# you can skip this step
$subId = "Your Azure subscription Id"
Set-AzContext -SubscriptionId $subId

# Set the custom domain name to the selected resource
# WARNING! THIS IS NOT REVERSIBLE!
Set-AzCognitiveServicesAccount -ResourceGroupName $resourceGroup `
    -Name $speechResourceName -CustomSubdomainName $subdomainName
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Questa sezione richiede la versione più recente dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="verify-custom-domain-name-availability"></a>Verificare la disponibilità del nome di dominio personalizzato

È necessario verificare se il dominio personalizzato che si vuole usare è gratuito. Si userà il metodo di [Verifica della disponibilità del dominio](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) dall'API REST di servizi cognitivi. 

Copiare il blocco di codice seguente, inserire il nome di dominio personalizzato e salvarlo nel file `subdomain.json` .

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
Se il nome desiderato è disponibile, verrà restituita una risposta simile alla seguente:
```azurecli
{
  "isSubdomainAvailable": true,
  "reason": null,
  "subdomainName": "my-custom-name",
  "type": null
}
```

Se il nome è già stato preso, si otterrà la risposta seguente:
```azurecli
{
  "isSubdomainAvailable": false,
  "reason": "Sub domain name 'my-custom-name' is already used. Please pick a different name.",
  "subdomainName": "my-custom-name",
  "type": null
}
```
## <a name="enabling-custom-domain-name"></a>Abilitazione del nome di dominio personalizzato

Per abilitare il nome di dominio personalizzato per la risorsa vocale selezionata, viene usato il comando [AZ cognitiveservices account Update](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_update) .

Selezionare la sottoscrizione di Azure contenente la risorsa di riconoscimento vocale. Se l'account Azure ha solo una sottoscrizione attiva, è possibile ignorare questo passaggio. Sostituire `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` con l'ID sottoscrizione di Azure.
```azurecli-interactive
az account set --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
Consente di impostare il nome di dominio personalizzato sulla risorsa selezionata. Sostituire i valori dei parametri di esempio con quelli effettivi ed eseguire il comando seguente.
> [!WARNING]
> Al termine dell'esecuzione del comando riportato di seguito, si creerà un nome di dominio personalizzato per la risorsa di riconoscimento vocale. Questo nome **non può** essere modificato. Per ulteriori informazioni, vedere l'avviso sopra riportato.
```azurecli
az cognitiveservices account update --name my-speech-resource-name --resource-group my-resource-group-name --custom-domain my-custom-name
```

**_

## <a name="enabling-private-endpoints"></a>Abilitazione di endpoint privati

Abilitare l'endpoint privato con portale di Azure, Azure PowerShell o l'interfaccia della riga di comando di Azure.

È consigliabile usare la [zona DNS privata](../../dns/private-dns-overview.md) collegata alla rete virtuale con gli aggiornamenti necessari per gli endpoint privati, che vengono creati per impostazione predefinita durante il processo di provisioning. Tuttavia, se si usa il proprio server DNS, potrebbe essere necessario apportare altre modifiche alla configurazione DNS. Vedere [la sezione DNS per endpoint privati](#dns-for-private-endpoints) . È consigliabile scegliere la strategia DNS _ *prima* di * eseguire il provisioning di endpoint privati per una risorsa vocale di produzione. Si consiglia anche di eseguire test preliminari, specialmente se si usa il proprio server DNS.

Usare gli articoli seguenti per creare endpoint privati. Gli articoli usano un'app Web come risorsa di esempio per abilitare con endpoint privati. Usare invece i parametri seguenti:

| Impostazione             | Valore                                    |
|---------------------|------------------------------------------|
| Tipo di risorsa       | **Microsoft. CognitiveServices/accounts** |
| Risorsa            | **\<your-speech-resource-name>**         |
| Sottorisorsa di destinazione | **account**                              |

- [Creare un endpoint privato con il portale di Azure](../../private-link/create-private-endpoint-portal.md)
- [Creare un endpoint privato usando Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)
- [Creare un endpoint privato usando l'interfaccia della riga di comando](../../private-link/create-private-endpoint-cli.md)

### <a name="dns-for-private-endpoints"></a>DNS per endpoint privati

Acquisire familiarità con i principi generali del [DNS per gli endpoint privati nelle risorse di servizi cognitivi](../cognitive-services-virtual-networks.md#dns-changes-for-private-endpoints). Verificare quindi che la configurazione DNS funzioni correttamente (vedere le sottosezioni successive).

#### <a name="mandatory-check-dns-resolution-from-the-virtual-network"></a>(Controllo obbligatorio). Risoluzione DNS dalla rete virtuale

`my-private-link-speech.cognitiveservices.azure.com`Per questa sezione verrà usato come nome DNS della risorsa vocale di esempio.

Accedere a una macchina virtuale che si trova nella rete virtuale a cui è stato collegato l'endpoint privato. Aprire il prompt dei comandi di Windows o la shell bash, eseguire il comando ' nslookup ' e assicurarsi che il nome di dominio personalizzato della risorsa venga risolto correttamente:
```dos
C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
Server:  UnKnown
Address:  168.63.129.16

Non-authoritative answer:
Name:    my-private-link-speech.privatelink.cognitiveservices.azure.com
Address:  172.28.0.10
Aliases:  my-private-link-speech.cognitiveservices.azure.com
```
Verificare che l'indirizzo IP risolto corrisponda all'indirizzo dell'endpoint privato.

#### <a name="optional-check-dns-resolution-from-other-networks"></a>(Controllo facoltativo). Risoluzione DNS da altre reti

Questo controllo è necessario se si prevede di usare la risorsa di riconoscimento vocale dell'endpoint privato abilitata in modalità "ibrido", ovvero è stata abilitata l'opzione per l'accesso a *tutte le reti* o *reti selezionate e a endpoint privati* nella sezione *rete* della risorsa. Se si prevede di accedere alla risorsa usando solo un endpoint privato, è possibile ignorare questa sezione.

`my-private-link-speech.cognitiveservices.azure.com`Per questa sezione verrà usato come nome DNS della risorsa vocale di esempio.

In qualsiasi computer collegato a una rete da cui si consente l'accesso alla risorsa aprire il prompt dei comandi di Windows o la shell bash, eseguire il comando ' nslookup ' e assicurarsi che il nome di dominio personalizzato della risorsa venga risolto correttamente:
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

Si noti che l'indirizzo IP risolto punta a un endpoint proxy VNet, che viene usato per l'invio del traffico di rete alla risorsa di servizi cognitivi abilitata per l'endpoint privato. Questo comportamento sarà diverso per una risorsa con un nome di dominio personalizzato abilitato, ma *senza* endpoint privati configurati. Vedere [questa sezione](#dns-configuration).

## <a name="adjusting-existing-applications-and-solutions"></a>Regolazione delle applicazioni e delle soluzioni esistenti 

Una risorsa vocale con un dominio personalizzato abilitato usa un modo diverso per interagire con i servizi di riconoscimento vocale. Questo vale per una risorsa vocale abilitata per il dominio personalizzato [con](#using-speech-resource-with-custom-domain-name-and-private-endpoint-enabled) e [senza](#using-speech-resource-with-custom-domain-name-without-private-endpoints) endpoint privati. Nella sezione corrente vengono fornite le informazioni necessarie per entrambi i casi.

### <a name="using-speech-resource-with-custom-domain-name-and-private-endpoint-enabled"></a>Uso della risorsa vocale con un nome di dominio personalizzato e un endpoint privato abilitato

Una risorsa vocale con nome di dominio personalizzato e endpoint privato abilitato usa un modo diverso per interagire con i servizi di riconoscimento vocale. Questa sezione illustra come usare tale risorsa con l'API REST di servizi vocali e l'SDK per la [sintesi vocale](speech-sdk.md).

> [!NOTE]
> Si noti che una risorsa di sintesi vocale senza endpoint privati, ma con un **nome di dominio personalizzato** abilitato dispone anche di un metodo speciale per interagire con i servizi vocali, ma in questo modo si differenzia dallo scenario di una risorsa di sintesi vocale abilitata per un endpoint privato. Se si dispone di una risorsa di questo tipo, ad esempio se si dispone di una risorsa con endpoint privati, ma si è deciso di rimuoverli, assicurarsi di acquisire familiarità con la [sezione corrispondente](#using-speech-resource-with-custom-domain-name-without-private-endpoints).

#### <a name="speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-rest-api"></a>Risorsa vocale con nome di dominio personalizzato e endpoint privato. Uso con l'API REST

`my-private-link-speech.cognitiveservices.azure.com`Per questa sezione verrà usato come nome DNS della risorsa vocale di esempio (dominio personalizzato).

##### <a name="note-on-speech-services-rest-api"></a>Nota sull'API REST di servizi vocali

Servizi di riconoscimento vocale ha un'API REST per [sintesi vocale](rest-speech-to-text.md) e [sintesi vocale.](rest-text-to-speech.md) Per lo scenario di endpoint privato abilitato è necessario considerare quanto segue.

Per riconoscimento vocale sono disponibili due diverse API REST. Ogni API serve uno scopo diverso, USA endpoint diversi e richiede un approccio diverso quando viene usato sing in uno scenario di endpoint privato abilitato.

Le API REST per sintesi vocale sono:
- [v 1.0](rest-speech-to-text.md) viene usato per la trascrizione in linea
- v 3.0 viene usato per la [trascrizione](batch-transcription.md) e la [riconoscimento vocale personalizzato](custom-speech-overview.md)di batch. (Vedere il [riferimento completo](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0))

L'utilizzo di riconoscimento vocale v 1.0 e dell'API REST di sintesi vocale nello scenario di endpoint privato è lo stesso e l'equivalente al caso dell' [SDK di riconoscimento vocale](#speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk) descritto più avanti in questo articolo. 

L'API REST di sintesi vocale v 3.0 usa un set di endpoint diverso e quindi richiede un approccio diverso per lo scenario abilitato per gli endpoint privati.

Entrambi i casi sono descritti nelle sottosezioni successive.


##### <a name="speech-to-text-rest-api-v30"></a>API REST per sintesi vocale v 3.0

In genere le risorse vocali usano [endpoint regionali di servizi cognitivi](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) per la comunicazione con l' [API REST di sintesi vocale v 3.0](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0). Queste risorse hanno il formato di denominazione seguente: <p/>`{region}.api.cognitive.microsoft.com`

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
Questo URL deve essere raggiungibile dalla rete virtuale con l'endpoint privato collegato (purché la [risoluzione DNS sia corretta](#mandatory-check-dns-resolution-from-the-virtual-network)).

Quindi, in genere, dopo aver abilitato il nome di dominio personalizzato per una risorsa vocale, è necessario sostituire il nome host in tutti gli URL di richiesta con il nuovo nome host del dominio personalizzato. Tutte le altre parti della richiesta, come il percorso `/speechtotext/v3.0/transcriptions` nell'esempio precedente, rimangono invariate.

> [!TIP]
> Alcuni clienti hanno sviluppato applicazioni che usano la parte Region del nome DNS dell'endpoint regionale, ad esempio per inviare la richiesta alla risorsa vocale distribuita in una determinata area di Azure.
>
> Il nome di dominio personalizzato della risorsa vocale **non** contiene informazioni sull'area in cui è distribuita la risorsa. Quindi, la logica dell'applicazione descritta in precedenza **non** funzionerà e deve essere modificata.

##### <a name="speech-to-text-rest-api-v10-and-text-to-speech-rest-api"></a>API REST per sintesi vocale v 1.0 e API REST di sintesi vocale

L'API [Rest di sintesi vocale v 1.0](rest-speech-to-text.md) e l' [API REST](rest-text-to-speech.md) di sintesi vocale usano due tipi di endpoint:
- [Endpoint regionali di servizi cognitivi](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) per la comunicazione con l'API REST di servizi cognitivi per ottenere un token di autorizzazione
- Endpoint speciali per tutte le altre operazioni

La descrizione dettagliata degli endpoint speciali e il modo in cui deve essere trasformato l'URL per una risorsa di riconoscimento vocale con endpoint privato sono disponibili in [questa](#general-principle) sottosezione della sezione "utilizzo con l'SDK di riconoscimento vocale" riportata di seguito. Lo stesso principio descritto per l'SDK è valido per le API REST di sintesi vocale v 1.0 e sintesi vocale.

Acquisire familiarità con il materiale nella sottosezione indicata nel paragrafo precedente e vedere l'esempio seguente. Nell'esempio viene descritta l'API REST di sintesi vocale; l'utilizzo dell'API REST di riconoscimento vocale v 1.0 è completamente equivalente

**Esempio di utilizzo dell'API REST di sintesi vocale.**

L'Europa occidentale viene usata come area di Azure di esempio e `my-private-link-speech.cognitiveservices.azure.com` come nome DNS della risorsa vocale di esempio (dominio personalizzato). Il nome di dominio personalizzato `my-private-link-speech.cognitiveservices.azure.com` nell'esempio appartiene alla risorsa vocale creata nell'area Europa occidentale.

Per ottenere l'elenco delle voci supportate nell'area, è necessario eseguire le due operazioni seguenti:

- Ottenere il token di autorizzazione tramite
```http
https://westeurope.api.cognitive.microsoft.com/sts/v1.0/issuetoken
```
- Utilizzando il token ottenuto ottenere l'elenco di voci tramite
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

L'uso dell'SDK di riconoscimento vocale con il nome di dominio personalizzato e le risorse vocali abilitate per endpoint privato richiede la revisione e le modifiche probabilmente del codice dell'applicazione. Stiamo lavorando a un supporto più trasparente dello scenario di endpoint privato.

`my-private-link-speech.cognitiveservices.azure.com`Per questa sezione verrà usato come nome DNS della risorsa vocale di esempio (dominio personalizzato).

##### <a name="general-principle"></a>Principio generale

In genere negli scenari SDK (oltre che negli scenari dell'API REST di sintesi vocale) vengono usati gli endpoint internazionali speciali per le diverse offerte di servizio. Il formato del nome DNS per questi endpoint è: </p>`{region}.{speech service offering}.speech.microsoft.com`

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

Gli endpoint privati abilitati per l'endpoint comunicano con servizi vocali tramite un proxy speciale e per il fatto che **gli URL di connessione dell'endpoint devono essere modificati**. Viene applicato il principio seguente: un URL dell'endpoint "standard" segue il modello di <p/>`{region}.{speech service offering}.speech.microsoft.com/{URL path}`

Dovrà essere modificata in: <p/>`{your custom name}.cognitiveservices.azure.com/{speech service offering}/{URL path}`

**Esempio 1.** L'applicazione sta comunicando usando l'URL seguente (riconoscimento vocale usando il modello di base per l'inglese Stati Uniti nell'Europa occidentale): 
```
wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

Per usarlo nello scenario di endpoint privato abilitato quando il nome di dominio personalizzato della risorsa vocale è l' `my-private-link-speech.cognitiveservices.azure.com` URL deve essere modificato come segue:
```
wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

È ora più vicino:
- Il nome host `westeurope.stt.speech.microsoft.com` viene sostituito dal nome host del dominio personalizzato `my-private-link-speech.cognitiveservices.azure.com`
- Il secondo elemento del nome DNS originale ( `stt` ) diventa il primo elemento del percorso URL e precede il percorso originale, ovvero l'URL originale `/speech/recognition/conversation/cognitiveservices/v1?language=en-US` diventa `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US`
 
**Esempio 2.** L'applicazione sta comunicando usando l'URL seguente (sintesi vocale usando il modello Voice personalizzato nell'Europa occidentale): 
```http
https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```
Per usarlo nello scenario di endpoint privato abilitato quando il nome di dominio personalizzato della risorsa vocale è l' `my-private-link-speech.cognitiveservices.azure.com` URL deve essere modificato come segue: 
```http
https://my-private-link-speech.cognitiveservices.azure.com/voice/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

Viene applicato lo stesso principio dell'esempio 1, ma questa volta è l'elemento chiave `voice` .

##### <a name="modifying-applications"></a>Modifica di applicazioni

Per applicare il principio descritto nella sezione precedente al codice dell'applicazione, è necessario eseguire due operazioni principali:

- Determinare l'URL dell'endpoint usato dall'applicazione
- Modificare l'URL dell'endpoint come descritto nella sezione precedente e creare l' `SpeechConfig` istanza della classe usando questo URL modificato in modo esplicito

###### <a name="determining-application-endpoint-url"></a>Determinazione dell'URL dell'endpoint dell'applicazione

- [Abilitare la registrazione per l'applicazione](how-to-use-logging.md) ed eseguirla per generare il log
- Nel file di log cercare `SPEECH-ConnectionUrl` . La stringa conterrà il `value` parametro, che a sua volta conterrà l'URL completo usato dall'applicazione

Esempio di riga di un file di log con l'URL dell'endpoint:
```
(114917): 41ms SPX_DBG_TRACE_VERBOSE:  property_bag_impl.cpp:138 ISpxPropertyBagImpl::LogPropertyAndValue: this=0x0000028FE4809D78; name='SPEECH-ConnectionUrl'; value='wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?traffictype=spx&language=en-US'
```
L'URL usato dall'applicazione in questo esempio è il seguente:
```
wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```
###### <a name="creating-speechconfig-instance-using-full-endpoint-url"></a>Creazione dell' `SpeechConfig` istanza tramite l'URL completo dell'endpoint

Modificare l'endpoint specificato nella sezione precedente, come descritto in [principio generale](#general-principle) sopra.

A questo punto è necessario modificare la modalità di creazione dell'istanza di `SpeechConfig` . È molto probabile che l'applicazione odierna stia usando un codice simile al seguente:
```csharp
var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
```
Questa operazione non funzionerà per la risorsa di sintesi vocale abilitata per endpoint privati a causa delle modifiche del nome host e dell'URL descritte nelle sezioni precedenti. Se si prova a eseguire l'applicazione esistente senza alcuna modifica usando la chiave di una risorsa abilitata per l'endpoint privato, si otterrà un errore di autenticazione (401).

Per renderlo funzionante, è necessario modificare la modalità di creazione di un'istanza `SpeechConfig` della classe e utilizzare l'inizializzazione "da endpoint"/"con endpoint". Si supponga che siano state definite le due variabili seguenti:
- `subscriptionKey` che contiene la chiave della risorsa di riconoscimento vocale endpoint privato abilitato
- `endPoint` contenente l'URL completo dell'endpoint **modificato** (usando il tipo richiesto dal linguaggio di programmazione corrispondente). Nell'esempio questa variabile deve contenere
```
wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```
Quindi è necessario creare un'istanza della `SpeechConfig` classe come la seguente:
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
> I parametri di query specificati nell'URI dell'endpoint non vengono modificati, anche se sono impostati da qualsiasi altra API. Se, ad esempio, la lingua di riconoscimento viene definita nell'URI come parametro di query "Language = en-US" e viene anche impostata su "ur-ur" tramite la proprietà corrispondente, l'impostazione della lingua nell'URI ha la precedenza e la lingua valida è "en-US". Solo i parametri non specificati nell'URI dell'endpoint possono essere impostati da altre API.

Dopo questa modifica, l'applicazione dovrebbe funzionare con le risorse di riconoscimento vocale private Enabled. Stiamo lavorando a un supporto più trasparente dello scenario di endpoint privato.

### <a name="using-speech-resource-with-custom-domain-name-without-private-endpoints"></a>Uso di una risorsa vocale con un nome di dominio personalizzato senza endpoint privati

In questo articolo sono state illustrate diverse volte che l'abilitazione di un dominio personalizzato per una risorsa vocale è **irreversibile** e tale risorsa utilizzerà un modo diverso per comunicare con i servizi di riconoscimento vocale che si confrontano con quelli "normali", ovvero quelli che usano [nomi di endpoint internazionali](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints).

Questa sezione illustra come usare una risorsa di riconoscimento vocale con un nome di dominio personalizzato abilitato, ma **senza** endpoint privati con l'API REST di servizi vocali e l'SDK per la [sintesi vocale](speech-sdk.md). Potrebbe trattarsi di una risorsa che è stata usata una volta in uno scenario di endpoint privato, ma ne ha eliminato gli endpoint privati.

#### <a name="dns-configuration"></a>Configurazione del DNS

Ricordare come un nome DNS di dominio personalizzato della risorsa di riconoscimento vocale dell'endpoint privato venga [risolto dalle reti pubbliche](#optional-check-dns-resolution-from-other-networks). In questo caso l'indirizzo IP risolto punta a un endpoint proxy VNet, che viene usato per l'invio del traffico di rete alla risorsa di servizi cognitivi abilitata per l'endpoint privato.

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
Confrontarlo con l'output di [questa sezione](#optional-check-dns-resolution-from-other-networks).

#### <a name="speech-resource-with-custom-domain-name-without-private-endpoints-usage-with-rest-api"></a>Risorsa vocale con nome di dominio personalizzato senza endpoint privati. Uso con l'API REST

##### <a name="speech-to-text-rest-api-v30"></a>API REST per sintesi vocale v 3.0

L'utilizzo dell'API REST di sintesi vocale v 3.0 è completamente equivalente al caso di [risorse vocali abilitate per gli endpoint privati](#speech-to-text-rest-api-v30).

##### <a name="speech-to-text-rest-api-v10-and-text-to-speech-rest-api"></a>API REST per sintesi vocale v 1.0 e API REST di sintesi vocale

In questo caso, l'API REST di riconoscimento vocale v 1.0 e l'utilizzo dell'API REST di sintesi vocale non hanno differenze rispetto al caso generale e devono essere usate come descritto in [API REST di sintesi vocale v 1.0](rest-speech-to-text.md) e documentazione sull' [API REST](rest-text-to-speech.md) per la sintesi vocale.


#### <a name="speech-resource-with-custom-domain-name-without-private-endpoints-usage-with-speech-sdk"></a>Risorsa vocale con nome di dominio personalizzato senza endpoint privati. Utilizzo con l'SDK di riconoscimento vocale

L'uso dell'SDK vocale con il nome di dominio personalizzato abilitato per le risorse vocali **senza** endpoint privati richiede la revisione e probabilmente modifiche del codice dell'applicazione. Si noti che queste modifiche sono **diverse** rispetto al caso di una [risorsa di riconoscimento vocale abilitata per endpoint privato](#speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk). Stiamo lavorando a un supporto più trasparente per l'endpoint privato o il dominio personalizzato.

`my-private-link-speech.cognitiveservices.azure.com`Per questa sezione verrà usato come nome DNS della risorsa vocale di esempio (dominio personalizzato).

Nella sezione relativa alla [risorsa di riconoscimento vocale per endpoint privato](#speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk) è stato illustrato come determinare l'URL dell'endpoint usato, modificarlo e fare in modo che funzioni con l'inizializzazione "from endpoint"/"with endpoint" dell' `SpeechConfig` istanza della classe.

Tuttavia, se si tenta di eseguire la stessa applicazione dopo la rimozione di tutti gli endpoint privati (per un certo periodo di tempo per il nuovo provisioning dei record DNS), si otterrà un errore interno del servizio (404). Il motivo è il [record DNS](#dns-configuration) che ora punta all'endpoint di servizi cognitivi regionali anziché al proxy VNet e i percorsi URL come `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` non verranno trovati, di conseguenza l'errore "non trovato" (404).

Se si esegue il rollback dell'applicazione nella creazione di un'istanza "standard" di `SpeechConfig` nello stile di
```csharp
var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
```
l'applicazione verrà terminata con l'errore di autenticazione (401).

##### <a name="modifying-applications"></a>Modifica di applicazioni

Per abilitare l'applicazione per lo scenario della risorsa vocale con un nome di dominio personalizzato senza endpoint privati, è necessario eseguire le operazioni seguenti:
- Richiedere un token di autorizzazione tramite l'API REST di servizi cognitivi
- Creare un'istanza `SpeechConfig` della classe usando il metodo "from token di autorizzazione"/"con il token di autorizzazione" 

###### <a name="requesting-authorization-token"></a>Richiesta del token di autorizzazione

Vedere [questo articolo](../authentication.md#authenticate-with-an-authentication-token) su come ottenere il token tramite l'API REST di servizi cognitivi. 

Usare il nome di dominio personalizzato nell'URL dell'endpoint, in questo esempio l'URL è:
```http
https://my-private-link-speech.cognitiveservices.azure.com/sts/v1.0/issueToken
```
> [!TIP]
> È possibile trovare questo URL nella sezione *chiavi e endpoint* (gruppo di *Gestione risorse* ) della risorsa di riconoscimento vocale in portale di Azure.

###### <a name="creating-speechconfig-instance-using-authorization-token"></a>Creazione di un' `SpeechConfig` istanza con il token di autorizzazione

È necessario creare un'istanza della `SpeechConfig` classe utilizzando il token di autorizzazione ottenuto nella sezione precedente. Si supponga che siano state definite le variabili seguenti:

- `token` contenente il token di autorizzazione ottenuto nella sezione precedente
- `azureRegion` contenente il nome dell' [area](regions.md) della risorsa vocale (esempio: `westeurope` )
- `outError` (solo per i casi [Objective C](/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithauthorizationtokenregionerror) )

Quindi è necessario creare un'istanza della `SpeechConfig` classe come la seguente:
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
> Il chiamante deve verificare che il token di autorizzazione sia valido. Prima della scadenza del token di autorizzazione, il chiamante deve aggiornarlo chiamando questo Setter con un nuovo token valido. Poiché i valori di configurazione vengono copiati durante la creazione di un nuovo riconoscimento/sintetizzatore, il nuovo valore del token non verrà applicato ai sistemi di riconoscimento che sono già stati creati. Per i riconoscitori o i sintetizzatori creati in precedenza, è necessario impostare il token di autorizzazione del riconoscimento/sintetizzatore corrispondente per aggiornare il token. In caso contrario, i riconoscitori/sintetizzatori rileveranno errori durante il riconoscimento o la sintesi.

Dopo questa modifica, l'applicazione dovrebbe funzionare con le risorse vocali abilitate per il nome di dominio personalizzato senza endpoint privati. Stiamo lavorando a un supporto più trasparente dello scenario di dominio personalizzato/endpoint privato.

## <a name="pricing"></a>Prezzi

Per informazioni dettagliate sui prezzi, vedere [Prezzi di Collegamento privato di Azure](https://azure.microsoft.com/pricing/details/private-link).

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su [Collegamento privato di Azure](../../private-link/private-link-overview.md)
* Altre informazioni sull' [SDK vocale](speech-sdk.md)
* Altre informazioni sull' [API REST per sintesi vocale](rest-speech-to-text.md)
* Altre informazioni sull' [API REST di sintesi vocale](rest-text-to-speech.md)
