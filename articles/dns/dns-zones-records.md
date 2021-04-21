---
title: Panoramica di zone e record DNS - DNS di Azure
description: Panoramica del supporto per l'hosting di zone e record DNS in DNS di Microsoft Azure.
author: rohinkoul
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.topic: conceptual
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 04/20/2021
ms.author: rohink
ms.openlocfilehash: 26eefe5cbcef417dee1a400b492ee847394ca6a9
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816964"
---
# <a name="overview-of-dns-zones-and-records"></a>Panoramica delle zone e dei record DNS

Questo articolo illustra i concetti chiave di domini, zone DNS, record DNS e set di record. Si apprenderà come è supportato in DNS di Azure.

## <a name="domain-names"></a>Nomi di dominio

Domain Name System è una gerarchia di domini. La gerarchia inizia dal dominio 'root', il cui nome è semplicemente **'.**'.  seguito dai domini di primo livello, come "com", "net", "org", "uk" o "jp",  Sotto i domini di primo livello sono presenti domini di secondo livello, ad esempio "org.uk" o "co.jp". I domini nella gerarchia DNS vengono distribuiti a livello globale, ospitati dai server dei nomi DNS in tutto il mondo.

Un registrar è un'organizzazione che consente di acquistare un nome di dominio, ad esempio `contoso.com` .  L'acquisto di un nome di dominio offre il diritto di controllare la gerarchia DNS con tale nome, ad esempio consentendo di indirizzare il nome `www.contoso.com` al sito Web aziendale. Il registrar può ospitare il dominio nei propri server dei nomi per conto dell'utente o consentire all'utente di specificare server dei nomi alternativi.

DNS di Azure un'infrastruttura di server dei nomi a disponibilità elevata e distribuita a livello globale che è possibile usare per ospitare il dominio. Ospitando i domini in DNS di Azure, è possibile gestire i record DNS usando gli stessi strumenti, credenziali, API, fatturazione e supporto degli altri servizi di Azure.

DNS di Azure attualmente non supporta l'acquisto di nomi di dominio. Se si vuole acquistare un nome di dominio, è necessario ricorrere a un registrar di terze parti. che addebita in genere una tariffa annuale contenuta. I domini possono quindi essere ospitati in DNS di Azure per la gestione dei record DNS. Per informazioni dettagliate, vedere [Delegare un dominio a DNS di Azure](dns-domain-delegation.md) .

## <a name="dns-zones"></a>Zone DNS

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="dns-records"></a>Record DNS

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

### <a name="time-to-live"></a>Time-To-Live

La durata Time-to-Live o TTL specifica per quanto tempo ogni record viene memorizzato nella cache da parte dei client, prima che venga eseguita nuovamente la query. Nell'esempio precedente, il valore TTL è 3600 secondi o 1 ora.

In DNS di Azure, la durata (TTL) viene specificata per il set di record, non per ogni record, quindi lo stesso valore viene usato per tutti i record all'interno di tale set di record.  È possibile specificare qualsiasi valore TTL compreso tra 1 e 2.147.483.647 secondi.

### <a name="wildcard-records"></a>Record con caratteri jolly

DNS di Azure supporta [record con caratteri jolly](https://en.wikipedia.org/wiki/Wildcard_DNS_record). I record con caratteri jolly vengono restituiti in risposta a qualsiasi query con un nome corrispondente, a meno che non vi sia una corrispondenza più vicina da un set di record non con caratteri jolly. DNS di Azure supporta set di record con caratteri jolly per tutti i tipi di record tranne NS e SOA.

Per creare un set di record con caratteri jolly, usare il nome del set di record "\*". È anche possibile usare un nome con '' come etichetta più a \* sinistra, ad esempio ' \* .foo'.

### <a name="caa-records"></a>Record CAA

Un record CAA consente ai proprietari di domini di specificare quali autorità di certificazione (CA) sono autorizzate a emettere certificati per i loro domini. Questo record consente alle AUTORITÀ di certificazione di evitare il rilascio ergasto di certificati in alcune circostanze. I record CAA hanno tre proprietà:
* **Flag:** questo campo è un numero intero compreso tra 0 e 255, usato per rappresentare il flag critico con un significato speciale in base alla [RFC](https://tools.ietf.org/html/rfc6844#section-3)
* **Tag**: una stringa ASCII che può avere uno dei valori seguenti:
    * **issue**: se si desidera specificare le AUTORITÀ di certificazione autorizzate a rilasciare certificati (tutti i tipi)
    * **issuewild:** se si desidera specificare le CA autorizzate a rilasciare certificati (solo certificati con caratteri jolly)
    * **iodef**: specificare un indirizzo di posta elettronica o nome host a cui le CA possono inviare notifiche per le richieste di emissione di certificati non autorizzate
* **Valore**: il valore del tag specifico scelto

### <a name="cname-records"></a>Record CNAME

I set di record CNAME non possono coesistere con altri set di record con lo stesso nome. Ad esempio, non è possibile creare contemporaneamente un set di record CNAME con il nome relativo "www" e un record A con il nome relativo "www".

Poiché il dominio principale della zona (name = ' ') conterrà sempre i set di record NS e SOA durante la creazione della zona, non è possibile creare un set di record CNAME al dominio principale della \@ zona.

Questi vincoli sono causati dagli standard DNS e non sono limitazioni di DNS di Azure.

### <a name="ns-records"></a>Record NS

Il set di record NS nell'apice della zona (nome ' ') viene creato automaticamente con ogni zona DNS e viene eliminato automaticamente quando la \@ zona viene eliminata. Non può essere eliminato separatamente.

Questo set contiene i nomi dei server dei nomi DNS di Azure assegnati alla zona. È possibile aggiungere più server dei nomi a questo set di record NS per supportare il cohosting di domini con più di un provider DNS. È anche possibile modificare il valore TTL e i metadati per questo set di record. Tuttavia, la rimozione o la modifica dei server dei nomi DNS di Azure non è consentita. 

Questa restrizione si applica solo al set di record NS nel apice della zona. Gli altri set di record NS nella zona, usati per delegare le zone figlio, possono essere creati, modificati ed eliminati senza vincoli.

### <a name="soa-records"></a>Record SOA

Un set di record SOA viene creato automaticamente all'apice di ogni zona (nome = ' ') e viene eliminato automaticamente quando la \@ zona viene eliminata.  I record SOA non possono essere creati o eliminati separatamente.

È possibile modificare tutte le proprietà del record SOA, ad eccezione della proprietà 'host'. Questa proprietà viene preconfigurato per fare riferimento al nome del server dei nomi primario fornito da DNS di Azure.

Il numero di serie della zona nel record SOA non viene aggiornato automaticamente quando vengono apportate modifiche ai record nella zona. Può essere aggiornato manualmente modificando il record SOA, se necessario.

### <a name="spf-records"></a>Record SPF

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="srv-records"></a>Record SRV

I [record SRV](https://en.wikipedia.org/wiki/SRV_record) vengono usati da svariati servizi per specificare i percorsi dei server. Quando si specifica un record SRV in DNS di Azure:

* Il *servizio* e il *protocollo* devono essere specificati come parte del nome del set di record, preceduti da caratteri di sottolineatura.  Ad esempio, "\_sip.\_tcp.name".  Per un record nell'apice della zona, non è necessario specificare ' ' nel nome del record, ma è sufficiente usare il servizio e il protocollo, ad esempio \@ \_ ' sip. \_ tcp'.
* La *priorità*, il *peso*, la *porta* e la *destinazione* vengono specificati come parametri di ogni record nel set di record.

### <a name="txt-records"></a>Record TXT

I record TXT vengono usati per eseguire il mapping di nomi di dominio a stringhe di testo arbitrarie. Vengono usati in più applicazioni, in particolare correlate alla configurazione della posta elettronica, ad esempio [Sender Policy Framework (SPF)](https://en.wikipedia.org/wiki/Sender_Policy_Framework) e [DomainKeys Identified Mail (DKIM).](https://en.wikipedia.org/wiki/DomainKeys_Identified_Mail)

Gli standard DNS consentono a un singolo record TXT di contenere più stringhe, ognuna con una lunghezza massima di 254 caratteri. Se sono usate più stringhe, vengono concatenate dai client e considerate come stringa singola.

Quando si chiama l'API REST del servizio DNS di Azure, è necessario specificare ogni stringa TXT separatamente.  Quando si usano le interfacce del portale di Azure, di PowerShell o l'interfaccia della riga di comando, è consigliabile specificare una sola stringa per record, che viene automaticamente divisa in segmenti di 254 caratteri, se necessario.

Le più stringhe in un record DNS non devono essere confuse con più record TXT in un set di record TXT.  Un set di record TXT può contenere più record, *ognuno dei quali* può contenere più stringhe.  Il servizio DNS di Azure supporta una lunghezza massima totale delle stringhe pari a 1024 caratteri in ogni set di record TXT (in tutti i record combinati).

## <a name="tags-and-metadata"></a>Tag e metadati

### <a name="tags"></a>Tag

I tag sono un elenco di coppie nome-valore usate da Azure Resource Manager per etichettare le risorse. Azure Resource Manager i tag per abilitare le visualizzazioni filtrate della fattura di Azure e consente anche di impostare criteri per determinati tag. Per altre informazioni sui tag, vedere [Uso dei tag per organizzare le risorse di Azure](../azure-resource-manager/management/tag-resources.md).

DNS di Azure supporta l'uso di tag di Azure Resource Manager in risorse di zona DNS.  Non supporta i tag nei set di record DNS, anche se come 'metadati' alternativi è supportato nei set di record DNS, come illustrato di seguito.

### <a name="metadata"></a>Metadati

In alternativa ai tag dei set di record, DNS di Azure supporta l'annotazione dei set di report tramite "metadati".  Analogamente ai tag, i metadati permettono di associare coppie nome-valore a ogni set di record.  Questa funzionalità può essere utile, ad esempio per registrare lo scopo di ogni set di record.  Diversamente dai tag, i metadati non possono essere usati per fornire una visualizzazione filtrata della fattura di Azure e non possono essere specificati in criteri di Azure Resource Manager.

## <a name="etags"></a>ETag

Si supponga che due persone o due processi provino a modificare un record DNS nello stesso momento. Quale prevale? E quello che prevale sa di avere sovrascritto le modifiche create da qualcun altro?

DNS di Azure usa gli Etag per gestire in modo sicuro le modifiche simultanee alla stessa risorsa. Gli ETag sono separati dai ["tag" di Azure Resource Manager](#tags). Ogni risorsa DNS (zona o set di record) ah un Etag associato. Quando viene recuperata una risorsa, viene recuperato anche il relativo valore Etag. Quando si aggiorna una risorsa, è possibile scegliere di passare nuovamente l'Etag DNS di Azure verificare le corrispondenze dell'Etag nel server. Dal momento che ogni aggiornamento di una risorsa comporta la rigenerazione dell'Etag, una mancata corrispondenza del valore Etag indica che si è verificata una modifica simultanea. Gli Etag possono essere usati anche quando si crea una nuova risorsa per assicurarsi che la risorsa non esista già.

Per impostazione predefinita, PowerShell in DNS di Azure usa gli Etag per bloccare le modifiche simultanee a zone e a set di record. L'opzione facoltativa *-Overwrite* può essere usata per disattivare i controlli di ETag e in questo caso le eventuali modifiche simultanee vengono sovrascritte.

A livello dell'API REST di DNS di Azure, gli ETag vengono specificati usando le intestazioni HTTP.  Il relativo comportamento è illustrato nella tabella seguente:

| Intestazione | Comportamento |
| --- | --- |
| nessuno |PUT riesce sempre (nessun controllo di Etag) |
| If-Match \<etag> |PUT riesce solo se la risorsa esiste e l'Etag corrisponde |
| If-match * |PUT riesce solo se la risorsa esiste |
| If-none-match * |PUT ha esito positivo solo se la risorsa non esiste |


## <a name="limits"></a>Limiti

Quando si usa DNS di Azure, si applicano i limiti predefiniti seguenti:

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

## <a name="next-steps"></a>Passaggi successivi

* Per iniziare a usare DNS di Azure, è necessario sapere come [creare una zona DNS](./dns-getstarted-portal.md) e come [creare record DNS](./dns-getstarted-portal.md).
* Per eseguire la migrazione di una zona DNS esistente, è necessario saper [importare ed esportare un file di zona DNS](dns-import-export.md).