---
title: Procedura dettagliata per il peering Internet di Azure per i servizi di comunicazione
titleSuffix: Azure
description: Procedura dettagliata per il peering Internet di Azure per i servizi di comunicazione
services: internet-peering
author: gthareja
ms.service: internet-peering
ms.topic: how-to
ms.date: 03/30/2021
ms.author: gatharej
ms.openlocfilehash: ff6750883a904ff5ddbddd3ddfd1ed82e72aebbc
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106498977"
---
# <a name="azure-internet-peering-for-communications-services-walkthrough"></a>Procedura dettagliata per il peering Internet di Azure per i servizi di comunicazione

In questa sezione vengono illustrati i passaggi necessari a un provider di servizi di comunicazione per stabilire un'interconnessione diretta con Microsoft.

**Provider di servizi di comunicazione:**  I provider di servizi di comunicazione sono le organizzazioni che offrono servizi di comunicazione (comunicazioni, messaggistica, conferenze e così via) e stanno cercando di integrare l'infrastruttura dei servizi di comunicazione (SBC/SIP Gateway e così via).  con i servizi di comunicazione di Azure e i team Microsoft. 

Il peering di Azure Internet supporta i provider di servizi di comunicazione per stabilire l'interconnessione diretta con Microsoft in qualsiasi sito perimetrale (località pop). L'elenco di tutti i siti di Edge pubblici è disponibile in [PeeringDB](https://www.peeringdb.com/net/694).

Il peering di Azure Internet offre connettività altamente affidabile e QoS (Quality of Service) abilitata per i servizi di comunicazione per garantire servizi di qualità elevata e incentrati sulle prestazioni.

## <a name="technical-requirements"></a>Requisiti tecnici
I requisiti tecnici per stabilire l'interconnessione diretta per i servizi di comunicazione sono i seguenti:
-   Il peer deve fornire il proprio numero di sistema autonomo (ASN), che deve essere pubblico.
-   Il peer deve avere un'interconnessione ridondante (PNI) in ogni posizione di interconnessione per garantire la ridondanza locale.
-   Il peer deve avere una ridondanza geografica per garantire il failover in caso di errori del sito nell'area/metro.
-   Il peer deve avere le sessioni BGP come Active-Active per garantire la disponibilità elevata e la convergenza più veloce e non deve essere sottoposta a provisioning come primario e di backup.
-   Il peer deve mantenere un rapporto 1:1 per i router di peering peer ai circuiti di peering e non viene applicata alcuna limitazione della frequenza.
-   Il peer deve fornire e annunciare il proprio spazio degli indirizzi IPv4 instradabile pubblicamente usato dagli endpoint del servizio di comunicazione del peer, ad esempio SBC. 
-   Il peer deve fornire informazioni dettagliate sulla classe di traffico ed endpoint ospitati in ogni subnet annunciata. 
-   Il peer deve eseguire BGP su un rilevamento di inoltro bidirezionale (BFD) per facilitare la convergenza del sottosecondo della route.
-   Tutti i prefissi dell'infrastruttura di comunicazione sono registrati in portale di Azure e annunciati con la stringa della community 8075:8007.
-   Il peer non deve terminare il peering su un dispositivo che esegue un firewall con stato. 
-   Per impostazione predefinita, Microsoft configurerà tutti i collegamenti di interconnessione come LAG (bundle di collegamenti), quindi il peer deve supportare LACP (Link Aggregation Control Protocol) sui collegamenti di interconnessione.

## <a name="establishing-direct-interconnect-with-microsoft-for-communications-services"></a>Stabilire l'interconnessione diretta con Microsoft per i servizi di comunicazione.

Per stabilire un'interconnessione diretta usando il peering Internet di Azure, seguire questa procedura:

**1. associare un ASN pubblico peer alla sottoscrizione di Azure:**

Nel caso in cui peer sia già associato a una sottoscrizione di Azure, ignorare questo passaggio.

[Associare l'ASN peer alla sottoscrizione di Azure tramite il portale-Azure | Microsoft Docs](https://docs.microsoft.com/azure/internet-peering/howto-subscription-association-portal)

Il passaggio successivo consiste nel creare una connessione di peering diretto per il servizio di peering.

> [!NOTE]
> Una volta approvata l'associazione ASN, inviare un messaggio di posta elettronica all'indirizzo peeringservices@microsoft.com con l'ID ASN e l'ID sottoscrizione per associare la sottoscrizione a Communications Services. 

**2. creare una connessione peering diretto per il servizio di peering:**

Seguire le istruzioni per [creare o modificare un peering diretto usando il portale](https://docs.microsoft.com/azure/internet-peering/howto-direct-portal)

Verificare che soddisfi i requisiti di disponibilità elevata.

Assicurarsi di selezionare le opzioni seguenti nella pagina "creare un peering":

Tipo di peering:   **Direct**

Rete Microsoft:  **8075**

SKU:        **gratuito Premium**


In "pagina connessione peering diretto" selezionare le opzioni seguenti:

Provider di indirizzi di sessione:   **Microsoft**

Da usare per i servizi di peering:   **abilitato**

> [!NOTE] 
> Ignorare il messaggio seguente durante la selezione per l'attivazione per i servizi di peering.
> *Non abilitare, a meno che non si sia contattati peering@microsoft.com per diventare un provider di mappe.*


  **2a. utilizzare la connessione peering diretto esistente per i servizi di peering**

Se si dispone di un peering diretto esistente che si vuole usare per supportare il servizio di peering, è possibile attivare in portale di Azure.
1.  Seguire le istruzioni per [convertire un peering diretto legacy in una risorsa di Azure usando il portale](https://docs.microsoft.com/azure/internet-peering/howto-legacy-direct-portal).
Se necessario, ordinare altri circuiti per soddisfare i requisiti di disponibilità elevata.

2.  Seguire i passaggi per [abilitare il servizio di peering](https://docs.microsoft.com/azure/internet-peering/howto-peering-service-portal) in un peering diretto usando il portale.




**3. registrare i prefissi per il routing ottimizzato**

Per il routing ottimizzato per i prefissi dell'infrastruttura di servizi di comunicazione, è necessario registrare tutti i prefissi con le interconnessioni del peering.
[Registrare il servizio di peering di Azure-portale di Azure | Microsoft Docs](https://docs.microsoft.com/azure/peering-service/azure-portal)

Il prefisso Key viene popolato automaticamente per i partner di Communications Service, quindi non è necessario che il partner usi alcuna chiave di prefisso per la registrazione. 

Assicurarsi che i prefissi registrati siano annunciati tramite le interconnessioni dirette stabilite per l'area.


## <a name="faqs"></a>Domande frequenti:

**D.**  Sono presenti subnet più piccole (</24) per i servizi di comunicazione. È possibile ottenere anche le subnet più piccole indirizzate?

**R.**  Sì, Microsoft Azure servizio di peering supporta anche il routing con prefisso più piccolo. Assicurarsi di registrare i prefissi più piccoli per il routing e che lo stesso sia annunciato tramite le interconnessioni.

**D.**  Quali Route Microsoft riceveranno tramite tali interconnessioni?

**R.** Microsoft annuncia tutti i prefissi dei servizi pubblici di Microsoft su tali interconnessioni. In questo modo non solo le comunicazioni ma altri servizi cloud saranno accessibili dalla stessa interconnessione.

**D.**  È necessario impostare il limite di prefisso, il numero di route che Microsoft annuncia?

**R.** Microsoft annuncia circa 280 prefissi su Internet e può aumentare del 10-15% in futuro. Quindi, un limite sicuro di 400-500 può essere adatto per essere impostato come "numero massimo di prefisso".

**D.** Microsoft annuncia nuovamente i prefissi peer a Internet?

**R.** No.

**D.** Esiste una tariffa per il servizio?

**R.** No, tuttavia è previsto che il peer trasporti i costi della connessione incrociata del sito.

**D.** Qual è la velocità minima di collegamento per un'interconnessione?

**R.** 10Gbps.

**D.** Il peer è associato a un contratto di contratto?

**R.** Sì, quando l'utilizzo raggiunge il 40%, deve iniziare un processo di aumento del ritardo di 45 60day.

**D.** Qual è il vantaggio di questo servizio rispetto al peering diretto corrente o a Express Route?

**R.** Il percorso gratuito e l'intero percorso sono ottimizzati per il traffico vocale sulla rete WAN Microsoft e la convergenza è ottimizzata per i sottosecondi con BFD.

**D.** In che modo è necessario completare il processo di onboarding?

**R.** L'ora sarà variabile a seconda del numero e della posizione dei siti e se il peer sta migrando i peering privati esistenti o creando un nuovo cablaggio. Il gestore deve prevedere più di 3 settimane.

**D.** È possibile usare le API per l'onboarding?

**R.** Attualmente non è disponibile alcun supporto per le API e la configurazione deve essere eseguita tramite il portale Web. 
