---
title: Introduzione a Azure Active Directory credenziali verificabili (anteprima)
description: Panoramica delle credenziali verificabili di Azure.
services: active-directory
author: barclayn
manager: daveba
editor: ''
ms.service: identity
ms.subservice: verifiable-credentials
ms.topic: overview
ms.date: 04/01/2021
ms.author: barclayn
ms.reviewer: ''
ms.openlocfilehash: 04b36b9b32e78016f693e61d40246776492be0e3
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222861"
---
# <a name="introduction-to-azure-active-directory-verifiable-credentials-preview"></a>Introduzione a Azure Active Directory credenziali verificabili (anteprima)

> [!IMPORTANT]
> Azure Active Directory credenziali verificabili è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Le nostre vite digitali e fisiche sono sempre più collegate a app, servizi e dispositivi usati per accedere a una vasta gamma di esperienze. Questa trasformazione digitale ci permette di interagire con centinaia di aziende e migliaia di altri utenti in modi che in precedenza erano inimmaginabili.

Ma i dati di identità sono troppo spesso esposti in violazioni della protezione. Queste violazioni hanno un impatto sulla vita degli utenti che interessano le nostre vite sociali, professionali e finanziarie. Microsoft ritiene che esista un metodo migliore. Ogni persona ha diritto a un'identità che possiede e controlla, una che archivia in modo sicuro gli elementi della propria identità digitale e mantiene la privacy. Questo nozioni di base illustra il modo in cui ci uniamo a una community diversificata per creare una soluzione di identità (DID), affidabile, interoperabile e basata su standard per gli utenti e le organizzazioni.

## <a name="why-we-need-decentralized-identity"></a>Perché è necessaria un'identità decentralizzata

Oggi usiamo la nostra identità digitale al lavoro, a casa e in ogni app, servizio e dispositivo che utilizziamo. È costituito da tutto ciò che diciamo, facciamo ed esperienza nella nostra vita, acquistando ticket per un evento, archiviando un hotel o persino ordinando il pranzo. Attualmente, l'identità e tutte le interazioni digitali sono di proprietà e controllate da altre parti, alcune delle quali non sono ancora a conoscenza.

In genere, gli utenti concedono il consenso a diverse app e dispositivi. Questo approccio richiede un elevato livello di vigilanza sulla parte dell'utente per tenere traccia degli utenti che hanno accesso alle informazioni. Per quanto riguarda l'organizzazione, la collaborazione con consumer e partner richiede un'orchestrazione ad alta touch per lo scambio sicuro di dati in modo da mantenere la privacy e la sicurezza per tutti gli utenti.

Si ritiene che un sistema di identità decentralizzato basato su standard possa sbloccare una nuova serie di esperienze che consentono a utenti e organizzazioni di avere maggiore controllo sui propri dati e di offrire un livello di attendibilità e sicurezza superiore per le app, i dispositivi e i provider di servizi.

## <a name="lead-with-open-standards"></a>Lead con standard aperti

Ci impegniamo a collaborare con i clienti, i partner e la community per sbloccare la nuova generazione di esperienze basate su identità decentralizzate e siamo lieti di collaborare con le persone e le organizzazioni che stanno apportando contributi straordinari in questo spazio. Se l'ecosistema è stato ampliato, gli standard, i componenti tecnici e i risultati finali del codice devono essere open source e accessibili a tutti.

Microsoft collabora attivamente con i membri del gruppo decentralizzato Identity Foundation (DIF), W3C Credentials community e la più ampia community di identità. Microsoft ha collaborato con questi gruppi per identificare e sviluppare standard critici e gli standard seguenti sono stati implementati nei servizi.

* [Identificatori decentralizzati W3C](https://www.w3.org/TR/did-core/)
* [Credenziali verificabili W3C](https://www.w3.org/TR/vc-data-model/)
* [SideTree DIF](https://identity.foundation/sidetree/spec/)
* [Configurazione molto nota di DIF](https://identity.foundation/specs/did-configuration/)
* [DIF SIOP](https://identity.foundation/did-siop/)
* [Scambio di presentazione DIF](https://identity.foundation/presentation-exchange/)


## <a name="what-are-dids"></a>Che cosa sono DIDs?

Prima di poter comprendere DIDs, è utile confrontarli con i sistemi di identità correnti. Gli indirizzi di posta elettronica e gli ID di social networking sono alias semplici da usare per la collaborazione, ma sono ora sottocaricati per fungere da punti di controllo per l'accesso ai dati in molti scenari oltre la collaborazione. Questo consente di creare un potenziale problema perché l'accesso a questi ID può essere rimosso in qualsiasi momento da parti esterne.

Gli identificatori decentralizzati (DIDs) sono diversi. DIDs sono identificatori univoci a livello globale generati dall'utente, che hanno una radice in sistemi decentralizzati come ION. Hanno caratteristiche univoche, come una maggiore sicurezza dell'immutabilità, la resistenza alla censura e la evasività delle manomissioni. Questi attributi sono fondamentali per qualsiasi sistema ID progettato per fornire la proprietà autonoma e il controllo utente. 

La soluzione di credenziale verificabile di Microsoft usa credenziali decentralizzate (DIDs) per firmare a livello di crittografia come prova che un relying party (Verifier) attesta le informazioni che dimostrano che sono proprietari di una credenziale verificabile. Per chiunque crei una soluzione di credenziale verificabile basata sull'offerta Microsoft, è quindi consigliabile una conoscenza di base degli identificatori decentralizzati.
## <a name="what-are-verifiable-credentials"></a>Che cosa sono le credenziali verificabili?

 Gli ID vengono usati nella nostra vita quotidiana. Sono disponibili licenze per i driver usati come prova della nostra capacità di usare un'auto. Le Università emettono diplomi che dimostrano che è stato raggiunto un livello di istruzione. Utilizziamo i passaporti per dimostrare a chi siamo responsabili dell'arrivo in altri paesi. Il modello di dati descrive il modo in cui è possibile gestire questi tipi di scenari quando si lavora su Internet, ma in modo sicuro rispetta la privacy degli utenti. È possibile ottenere informazioni aggiuntive nel [modello di dati delle credenziali verificabili 1,0](https://www.w3.org/TR/vc-data-model/)

In breve, le credenziali verificabili sono oggetti dati costituiti da attestazioni eseguite dall'emittente che attestano le informazioni su un oggetto. Queste attestazioni sono identificate dallo schema e includono l'emittente e il soggetto. L'emittente ha creato una firma digitale come prova che attestano queste informazioni.


## <a name="how-does-decentralized-identity-work"></a>Come funziona l'identità decentralizzata?

È necessario un nuovo tipo di identità. È necessaria un'identità che riunisce tecnologie e standard per fornire attributi di identità chiave come la proprietà autonoma e la resistenza alla censura. Queste funzionalità sono difficili da realizzare usando i sistemi esistenti.

Per offrire queste promesse, abbiamo bisogno di un fondamento tecnico costituito da sette innovazioni chiave. Una delle principali novità è rappresentata dagli identificatori di proprietà dell'utente, da un agente utente per la gestione delle chiavi associate a tali identificatori e da archivi dati crittografati controllati dall'utente.

![Panoramica dell'ambiente delle credenziali verificabili di Microsoft](media/decentralized-identifier-overview/microsoft-did-system.png)

**1. gli ID DIDs (decentralizzated Identifier) W3C** generano, possiedono e controllano in modo indipendente da qualsiasi organizzazione o governo. DIDs sono identificatori univoci a livello globale collegati a metadati di infrastruttura a chiave pubblica (DPKI) decentralizzati costituiti da documenti JSON che contengono materiale della chiave pubblica, descrittori di autenticazione ed endpoint di servizio.

**2. sistema decentralizzato: Ion (Identity overlay network)** Ion è una rete aperta e senza autorizzazioni di livello 2 basata sul protocollo SideTree puramente deterministico, che non richiede token speciali, validator attendibili o altri meccanismi di consenso. la progressione lineare della catena temporale di Bitcoin è tutto ciò che è necessario per il funzionamento. È disponibile [un pacchetto NPM Open Source](https://www.npmjs.com/package/@decentralized-identity/ion-tools) per semplificare l'integrazione con la rete Ion nelle app e nei servizi. Le librerie includono la creazione di una nuova, la generazione di chiavi e l'ancoraggio del fatto in Bitcoin Blockchain. 

**3. agente utente/portafoglio: Microsoft Authenticator app** consente alle persone reali di usare identità decentralizzate e credenziali verificabili. L'autenticatore crea DIDs, semplifica le richieste di rilascio e presentazione per le credenziali verificabili e gestisce il backup del valore di inizializzazione eseguito tramite un file Wallet crittografato.

**4. Microsoft resolver** un'API che si connette al nodo ion per cercare e risolvere DIDs usando il ```did:ion``` metodo e restituire l'oggetto documento did (DDO). DDO include i metadati DPKI associati all'oggetto, ad esempio le chiavi pubbliche e gli endpoint di servizio. 

**5. Azure Active Directory credenziali verificate servizio** un'API di rilascio e verifica e un SDK open source per le [credenziali verificabili W3C](https://www.w3.org/TR/vc-data-model/) firmate con il ```did:ion``` metodo. Consentono ai proprietari di identità di generare, presentare e verificare le attestazioni. Questo costituisce la base della relazione di trust tra gli utenti dei sistemi.

## <a name="a-sample-scenario"></a>Uno scenario di esempio

Lo scenario usato per spiegare il funzionamento di VCs prevede quanto segue:

- Società di Woodgrove Inc.
- Proseware, una società che offre sconti per i dipendenti Woodgrove.
- Alice, un dipendente presso Woodgrove, Inc. chi desidera ottenere uno sconto da proseware



Attualmente, Alice fornisce un nome utente e una password per accedere all'ambiente di rete di Woodgrove. Woodgrove distribuisce una soluzione VC per offrire un modo più gestibile per dimostrare di essere un dipendente di Woodgrove. Proseware usa una soluzione VC compatibile con la soluzione VC di Woodgrove e accetta le credenziali emesse da Woodgrove come prova di impiego.

L'emittente della credenziale, Woodgrove Inc., crea una chiave pubblica e una chiave privata. La chiave pubblica viene archiviata su ION. Quando la chiave viene aggiunta all'infrastruttura, la voce viene registrata in un Ledger decentralizzato basato su blockchain. L'emittente fornisce ad Alice la chiave privata archiviata in un'applicazione Wallet. Ogni volta che Alice utilizza correttamente la chiave privata, la transazione viene registrata nell'applicazione del portafoglio.

![Panoramica di Microsoft](media/decentralized-identifier-overview/did-overview.png)

## <a name="roles-in-a-verifiable-credential-solution"></a>Ruoli in una soluzione di credenziale verificabile 

Esistono tre attori primari nella soluzione di credenziale verificabile. Nel diagramma seguente:

- **Passaggio 1**, l' **utente** richiede una credenziale verificabile da un'autorità emittente.
- **Passaggio 2**, l' **emittente** della credenziale attesta che la prova fornita dall'utente è accurata e crea una credenziale verificabile firmata con il fatto che l'utente ha fatto l'oggetto.
- **Nel passaggio 3**, l'utente firma una presentazione verificabile (VP) con il fatto e invia al **Verifier.** Il verificatore convalida quindi le credenziali eseguendo la corrispondenza con la chiave pubblica posizionata in DPKI.

I ruoli in questo scenario sono:

![ruoli in un ambiente di credenziali verificabile](media/decentralized-identifier-overview/issuer-user-verifier.png)

**autorità di certificazione** : l'emittente è un'organizzazione che crea una soluzione di rilascio che richiede informazioni a un utente. Le informazioni vengono usate per verificare l'identità dell'utente. Ad esempio, Woodgrove, Inc. dispone di una soluzione di rilascio che consente loro di creare e distribuire le credenziali verificabili (VCs) a tutti i dipendenti. Il dipendente usa l'app Authenticator per accedere con il nome utente e la password, che passa un token ID al servizio emittente. Quando Woodgrove, Inc. convalida il token ID inviato, la soluzione di rilascio crea un VC che include attestazioni sul dipendente ed è firmato con Woodgrove, Inc. DID. Il dipendente dispone ora di una credenziale verificabile firmata dal proprio datore di lavoro, che include i dipendenti utilizzati come soggetto.  

**utente** : l'utente è la persona o l'entità che richiede un VC. Alice, ad esempio, è un nuovo dipendente di Woodgrove, Inc. e in precedenza ha emesso una credenziale verificabile per l'impiego. Quando Alice deve fornire una prova di impiego per ottenere uno sconto in Proseware, può concedere l'accesso alla credenziale nell'app Authenticator firmando una presentazione verificabile che dimostra che Alice è il proprietario di. Proseware è in grado di convalidare le credenziali emesse da Woodgrove, Inc. e Alice è il proprietario della credenziale. 

**verificatore** : il verificatore è una società o un'entità che deve verificare le attestazioni da una o più autorità emittenti attendibili. Ad esempio, Proseware considera attendibile Woodgrove, Inc. esegue un processo adeguato per la verifica dell'identità dei dipendenti e l'emissione di un VCs autentico e valido. Quando Alice tenta di ordinare le attrezzature necessarie per il proprio lavoro, Proseware utilizzerà standard aperti, ad esempio SIOP e scambio di presentazione, per richiedere le credenziali dell'utente dimostrando che si tratta di un dipendente di Woodgrove, Inc. Ad esempio, Proseware potrebbe fornire ad Alice un collegamento a un sito Web con un codice a matrice che analizza con la fotocamera del telefono. Viene avviata la richiesta per un VC specifico, che l'autenticatore analizzerà e fornirà ad Alice la possibilità di approvare la richiesta per dimostrare il suo impiego a proseware. Proseware può usare l'API o l'SDK del servizio di credenziali verificabili per verificare l'autenticità della presentazione verificabile. In base alle informazioni fornite da Alice, lo sconto viene concesso a Alice. Se altre aziende e organizzazioni sanno che Woodgrove, Inc. rilascia VCs ai propri dipendenti, possono anche creare una soluzione di verifica e utilizzare le credenziali di Woodgrove, Inc. verificabili per fornire offerte speciali riservate ai dipendenti Woodgrove, Inc..

## <a name="next-steps"></a>Passaggi successivi

Ora che si conoscono le credenziali di DIDs e verificabili, provare a eseguire l'esercitazione seguendo l'articolo introduttivo o uno degli articoli che forniscono informazioni più dettagliate sui concetti relativi alle credenziali verificabili.

- [Introduzione alle credenziali verificabili](get-started-verifiable-credentials.md)
- [Come personalizzare le credenziali](credential-design.md)
- [Domande frequenti sulle credenziali verificabili](verifiable-credentials-faq.md)