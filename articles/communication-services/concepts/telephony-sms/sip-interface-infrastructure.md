---
title: Requisiti dell'infrastruttura dell'interfaccia SIP-servizi di comunicazione di Azure
description: Acquisire familiarità con i requisiti dell'infrastruttura per la configurazione dell'interfaccia SIP dei servizi di comunicazione di Azure
author: boris-bazilevskiy
manager: nmurav
services: azure-communication-services
ms.author: bobazile
ms.date: 02/09/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: b258f2ef82e74073e3e4f1aa61b036d423c30300
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100422465"
---
# <a name="sip-interface-infrastructure-requirements"></a>Requisiti dell'infrastruttura dell'interfaccia SIP 

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

 
Questo articolo descrive i dettagli di connettività di infrastruttura, licenze e controller border controller (SBC) che è opportuno tenere presente quando si pianifica la distribuzione dell'interfaccia SIP.


## <a name="infrastructure-requirements"></a>Requisiti dell'infrastruttura
Nella tabella seguente sono elencati i requisiti dell'infrastruttura per i SBCs supportati, i domini e altri requisiti di connettività di rete per distribuire l'interfaccia SIP.  

|Requisito dell'infrastruttura|Sono necessari gli elementi seguenti:|
|:--- |:--- |
|Controller bordo sessione (SBC)|SBC supportato. Per altre informazioni, vedere [SBCS supportati](#supported-session-border-controllers-sbcs).|
|Trunk di telefonia connessi a SBC|Uno o più trunk di telefonia connessi a SBC. A un lato, SBC si connette al servizio di comunicazione di Azure tramite l'interfaccia SIP. Il SBC può inoltre connettersi a entità di telefonia di terze parti, ad esempio PBX, adattatori telefonici analoghi e così via. Tutte le opzioni di connettività PSTN connesse al SBC funzioneranno. Per la configurazione dei trunk PSTN per SBC, fare riferimento ai fornitori SBC o ai provider trunk.|
|Sottoscrizione di Azure|Una sottoscrizione di Azure usata per creare la risorsa ACS e la configurazione e la connessione a SBC.|
|Token di accesso di servizi di comunicazione|Per effettuare chiamate, è necessario un token di accesso valido con `voip` ambito. Vedere [token di accesso](https://docs.microsoft.com/azure/communication-services/concepts/identity-model#access-tokens)|
|Indirizzo IP pubblico per SBC|Un indirizzo IP pubblico che può essere usato per la connessione a SBC. In base al tipo di SBC, SBC può utilizzare NAT.|
|Nome di dominio completo (FQDN) per SBC|FQDN per SBC, in cui la parte del dominio FQDN non corrisponde ai domini registrati nell'organizzazione Microsoft 365 o Office 365. Per ulteriori informazioni, vedere la pagina relativa ai [nomi di dominio SBC](#sbc-domain-names).|
|Voce DNS pubblica per SBC |Una voce DNS pubblica che mappa il nome di dominio completo SBC all'indirizzo IP pubblico. |
|Certificato pubblico attendibile per SBC |Certificato per il SBC da utilizzare per tutte le comunicazioni con l'interfaccia SIP. Per ulteriori informazioni, vedere [certificato attendibile pubblico per SBC](#public-trusted-certificate-for-the-sbc).|
|Indirizzi IP e porte del firewall per la segnalazione e il supporto SIP |SBC comunica con i servizi seguenti nel cloud:<br/><br/>Proxy SIP, che gestisce la segnalazione<br/>Processore di contenuti multimediali, che gestisce i supporti<br/><br/>Questi due servizi hanno indirizzi IP distinti in Microsoft Cloud, descritti più avanti in questo documento.


## <a name="sbc-domain-names"></a>Nomi di dominio SBC

I clienti senza Office 365 possono usare qualsiasi nome di dominio per il quale possano ottenere un certificato pubblico.

Nella tabella seguente vengono illustrati esempi di nomi DNS registrati per il tenant, se il nome può essere utilizzato come nome di dominio completo (FQDN) per SBC ed esempi di nomi FQDN validi:

|Nome DNS|Può essere usato per il nome di dominio completo SBC|Esempi di nomi FQDN|
|:--- |:--- |:--- |
contoso.com|Sì|**Nomi validi:**<br/>sbc1.contoso.com<br/>ssbcs15.contoso.com<br/>europe.contoso.com|
|contoso.onmicrosoft.com|No|L'utilizzo di domini *. onmicrosoft.com non è supportato per i nomi SBC

Se si è un cliente di Office 365, il nome di dominio SBC non deve corrispondere registrato nei domini del tenant di Office 365. Di seguito è riportato l'esempio di coesistenza di Office 365 e del servizio di comunicazione di Azure:

|Dominio registrato in Office 365|Esempi di FQDN SBC nei team|Esempi di nomi FQDN SBC in ACS|
|:--- |:--- |:--- |
**contoso.com** (dominio di secondo livello)|**SBC.contoso.com** (nome nel dominio di secondo livello)|**SBC.ACS.contoso.com** (nome nel dominio di terzo livello)<br/>**SBC.fabrikam.com** (qualsiasi nome all'interno di un dominio diverso)|
|**O365.contoso.com** (dominio di terzo livello)|**SBC.O365.contoso.com** (nome nel dominio di terzo livello)|**SBC.contoso.com** (nome nel dominio di secondo livello)<br/>**SBC.ACS.O365.contoso.com** (nome nel dominio di quarto livello)<br/>**SBC.fabrikam.com** (qualsiasi nome all'interno di un dominio diverso)

L'associazione SBC funziona a livello di risorsa dei servizi di comunicazione, vale a dire che è possibile associare molti SBCs a una singola risorsa di servizi di comunicazione, ma non è possibile associare un singolo SBC a più di una risorsa di servizi di comunicazione. Per l'associazione a risorse diverse sono necessari FQDN SBC univoci.

## <a name="public-trusted-certificate-for-the-sbc"></a>Certificato pubblico attendibile per SBC

Microsoft consiglia di richiedere il certificato per SBC generando una richiesta di firma del certificato (CSR). Per istruzioni specifiche sulla generazione di un CSR per un SBC, vedere le istruzioni di interconnessione o la documentazione fornita dai fornitori di SBC. 

  > [!NOTE]
  > La maggior parte delle autorità di certificazione (CAs) richiede che la dimensione della chiave privata sia almeno 2048. Tenere presente questo aspetto durante la generazione del CSR.

Il certificato deve avere l'FQDN SBC come nome comune (CN) o il campo nome alternativo oggetto (SAN). Il certificato deve essere emesso direttamente da un'autorità di certificazione, non da un provider intermedio.

In alternativa, l'interfaccia SIP di servizi di comunicazione supporta un carattere jolly in CN e/o SAN e il carattere jolly deve essere conforme allo standard [RFC http su TLS](https://tools.ietf.org/html/rfc2818#section-3.1). 

Un esempio potrebbe essere `\*.contoso.com` l'uso di, che corrisponderebbe al nome di dominio completo SBC `sbc.contoso.com` , ma non corrisponderà a `sbc.test.contoso.com` .

Il certificato deve essere generato da una delle autorità di certificazione radice seguenti:

- AffirmTrust
- AddTrust External CA Root
- Radice CyberTrust Baltimore *
- Buypass
- Cybertrust
- Class 3 Public Primary Certification Authority
- CA radice sicura di comodo
- Deutsche Telekom 
- DigiCert Global Root CA
- DigiCert High Assurance EV Root CA
- Affidare
- GlobalSign
- Go Daddy
- GeoTrust
- Verisign, Inc. 
- SSL.com
- Starfield
- Symantec Enterprise Mobile Root for Microsoft 
- SwissSign
- Thawte Timestamping CA
- Trustwave
- TeliaSonera 
- T-Systems International GmbH (Deutsche Telekom)
- QuoVadis

Microsoft sta lavorando per aggiungere altre autorità di certificazione in base alle richieste dei clienti. 

## <a name="sip-signaling-fqdns"></a>Segnalazione SIP: FQDN 

I punti di connessione per l'interfaccia SIP di servizi di comunicazione sono i tre FQDN seguenti:

- **SIP.pstnhub.Microsoft.com** – FQDN globale: è necessario provare prima. Quando SBC invia una richiesta di risoluzione del nome, i server DNS Microsoft Azure restituiscono un indirizzo IP che punta al data center primario di Azure assegnato a SBC. L'assegnazione è basata sulle metriche delle prestazioni dei data center e sulla prossimità geografica al SBC. L'indirizzo IP restituito corrisponde al nome di dominio completo primario.
- **SIP2.pstnhub.Microsoft.com** -FQDN secondario: mappa geografica alla seconda area di priorità.
- **SIP3.pstnhub.Microsoft.com** – FQDN terziario: Mappa geograficamente alla terza area prioritaria.

Il posizionamento di questi tre FQDN in ordine è necessario per:

- Offrire un'esperienza ottimale (meno caricato e più vicino al Data Center SBC assegnato eseguendo una query sul primo FQDN).
- Consente di eseguire il failover quando la connessione da un SBC viene stabilita a un Data Center in cui si verifica un problema temporaneo. Per altre informazioni, vedere [meccanismo di failover](#failover-mechanism-for-sip-signaling) di seguito.  

I nomi di dominio completi, ovvero sip.pstnhub.microsoft.com, sip2.pstnhub.microsoft.com e sip3.pstnhub.microsoft.com, verranno risolti in uno dei seguenti indirizzi IP:

- `52.114.148.0`
- `52.114.132.46`
- `52.114.75.24` 
- `52.114.76.76` 
- `52.114.7.24` 
- `52.114.14.70`
- `52.114.16.74`
- `52.114.20.29`

Aprire le porte del firewall per questi indirizzi IP per consentire il traffico in ingresso e in uscita da e verso gli indirizzi per la segnalazione. Se il firewall supporta i nomi DNS, il nome di dominio completo viene `sip-all.pstnhub.microsoft.com` risolto in tutti questi indirizzi IP. 

## <a name="sip-signaling-ports"></a>Segnalazione SIP: porte

Utilizzare le seguenti porte per l'interfaccia SIP di servizi di comunicazione:

|Traffico|Da|A|Porta di origine|Porta di destinazione|
|:--- |:--- |:--- |:--- |:--- |
|SIP/TLS|Proxy SIP|SBC|1024 – 65535|Definito in SBC (per Office 365 GCC High/DoD solo la porta 5061 deve essere utilizzata)|
SIP/TLS|SBC|Proxy SIP|Definito in SBC|5061|

### <a name="failover-mechanism-for-sip-signaling"></a>Meccanismo di failover per la segnalazione SIP

SBC esegue una query DNS per risolvere sip.pstnhub.microsoft.com. In base alla posizione SBC e alle metriche delle prestazioni del Data Center, viene selezionato il data center primario. Se si verifica un problema nel data center principale, SBC tenterà il sip2.pstnhub.microsoft.com, che si risolve nel secondo Data Center assegnato e, nel caso raro in cui i Data Center in due aree non sono disponibili, SBC ritenta l'ultimo FQDN (sip3.pstnhub.microsoft.com), che fornisce l'indirizzo IP del Data Center terziario.

## <a name="media-traffic-ip-and-port-ranges"></a>Traffico multimediale: IP e intervalli di porte

Il traffico multimediale passa da e verso un servizio separato denominato processore di contenuti multimediali. Al momento della pubblicazione di un processore di contenuti multimediali per ACS, è possibile usare qualsiasi indirizzo IP di Azure. Scaricare [l'elenco completo degli indirizzi](https://www.microsoft.com/download/details.aspx?id=56519).

### <a name="port-range"></a>Intervallo di porte
L'intervallo di porte dei processori di contenuti multimediali è illustrato nella tabella seguente: 

|Traffico|Da|A|Porta di origine|Porta di destinazione|
|:--- |:--- |:--- |:--- |:--- |
|UDP/SRTP|Processore di contenuti multimediali|SBC|3478-3481 e 49152 – 53247|Definito in SBC|
|UDP/SRTP|SBC|Processore di contenuti multimediali|Definito in SBC|3478-3481 e 49152 – 53247|

  > [!NOTE]
  > Microsoft consiglia almeno due porte per ogni chiamata simultanea a SBC.


## <a name="media-traffic-media-processors-geography"></a>Traffico multimediale: media processor geography

Il traffico multimediale viene trasmesso tramite componenti denominati processori di contenuti multimediali. I processori di contenuti multimediali sono posizionati negli stessi data center dei proxy SIP. Sono inoltre disponibili processori di contenuti multimediali aggiuntivi per ottimizzare il flusso multimediale. Ad esempio, non è disponibile un componente proxy SIP in Australia (flussi SIP con Singapore o Hong Kong), ma il processore di contenuti multimediali è disponibile localmente in Australia. La necessità di processori di contenuti multimediali in locale è determinata dalla latenza che si verifica inviando il traffico a distanza prolungata, ad esempio da Australia a Singapore o Hong Kong. Anche se la latenza nell'esempio di traffico proveniente dall'Australia alla Hong Kong o a Singapore è accettabile per mantenere una valida qualità delle chiamate per il traffico SIP, per il traffico multimediale in tempo reale non lo è.

Percorsi in cui vengono distribuiti sia il proxy SIP che i componenti del processore multimediale:
- US (due negli Stati Uniti occidentali e nei data center degli Stati Uniti orientali)
- Europa (Data Center di Amsterdam e Dublino)
- Asia (Singapore e Data Center di Hong Kong)
- Australia (Data Center dell'Australia orientale e sudorientale)

Percorsi in cui vengono distribuiti solo i processori di contenuti multimediali (flussi SIP tramite il data center più vicino elencato sopra):
- Giappone (JP est e Data Center occidentali)


## <a name="media-traffic-codecs"></a>Traffico multimediale: codec

### <a name="leg-between-sbc-and-cloud-media-processor-or-microsoft-teams-client"></a>Gamba tra SBC e il processore di contenuti multimediali cloud o Microsoft teams client.
Si applica sia ai casi di bypass multimediale che ai casi non di bypass.

L'interfaccia di routing diretto sul piede tra il controller del bordo della sessione e il processore di contenuti multimediali cloud può usare i codec seguenti:

- SILK, G. 711, G. 722, G. 729

È possibile forzare l'uso del codec specifico sul controller del bordo della sessione escludendo i codec indesiderati dall'offerta.

### <a name="leg-between-acs-sdk-app-and-cloud-media-processor"></a>Piedi tra l'app ACS SDK e il processore di contenuti multimediali cloud

Quando si usa il processore di contenuti multimediali cloud e l'app ACS SDK, viene usato SILK o G. 722. La scelta del codec in questa fase è basata sugli algoritmi Microsoft, che prendono in considerazione più parametri. 

## <a name="supported-session-border-controllers-sbcs"></a>Controller Border Session supportati (SBCs)

La certificazione è in corso. Nel frattempo, i clienti possono usare i [controller dei bordi della sessione certificati](https://docs.microsoft.com/MicrosoftTeams/direct-routing-border-controllers). 

## <a name="next-steps"></a>Passaggi successivi

### <a name="conceptual-documentation"></a>Documentazione concettuale

- [Concetto di telefonia](./telephony-concept.md)
- [Tipi di numeri di telefono in Servizi di comunicazione di Azure](./plan-solution.md)
- [Prezzi](../pricing.md)

### <a name="quickstarts"></a>Avvi rapidi

- [Chiamata al telefono](../../quickstarts/voice-video-calling/pstn-call.md)
