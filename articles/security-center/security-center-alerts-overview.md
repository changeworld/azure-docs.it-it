---
title: Avvisi ed eventi imprevisti di sicurezza nel centro sicurezza di Azure
description: Informazioni sul modo in cui il Centro sicurezza di Azure genera avvisi di sicurezza e li correla in eventi imprevisti.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 02/25/2021
ms.author: memildin
ms.openlocfilehash: 9d917b3682d3b503743e706e8c952854e86fbc5e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727043"
---
# <a name="security-alerts-and-incidents-in-azure-security-center"></a>Avvisi ed eventi imprevisti di sicurezza nel centro sicurezza di Azure

Il Centro sicurezza genera avvisi per le risorse distribuite in ambienti Azure, locali e cloud ibridi.

Gli avvisi di sicurezza vengono attivati da rilevamenti avanzati e sono disponibili solo con Azure Defender. È possibile eseguire l'aggiornamento dalla pagina dei **prezzi & impostazioni** , come descritto in [Guida introduttiva: abilitare Azure Defender](enable-azure-defender.md). È disponibile una versione di valutazione gratuita valida 30 giorni. Per informazioni dettagliate sui prezzi nella valuta scelta e in base alla propria area, vedere [prezzi del Centro sicurezza](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="what-are-security-alerts-and-security-incidents"></a>Che cosa sono gli avvisi di sicurezza e gli eventi imprevisti della sicurezza? 

Gli **avvisi** sono le notifiche generate dal Centro sicurezza quando rileva le minacce nelle risorse. Il Centro sicurezza assegna le priorità ed elenca gli avvisi, insieme alle informazioni necessarie per analizzare rapidamente il problema. Il Centro sicurezza offre anche indicazioni per rimediare ai danni causati da un attacco.

Un **evento imprevisto di sicurezza** è una raccolta di avvisi correlati, anziché un elenco di avvisi singoli. Il Centro sicurezza usa la [correlazione](#cloud-smart-alert-correlation-in-azure-security-center-incidents) degli avvisi cloud per correlare avvisi diversi e segnali di fedeltà bassa in eventi imprevisti della sicurezza.

Con gli eventi imprevisti il Centro sicurezza offre un'unica visualizzazione di una campagna di attacco e di tutti gli avvisi correlati. Questa visualizzazione consente di comprendere rapidamente le azioni intraprese dall'utente malintenzionato e le risorse interessate.

## <a name="respond-to-todays-threats"></a>Rispondi alle minacce attuali

Negli ultimi 20 anni sono state introdotte modifiche significative nel panorama delle minacce . In passato, le aziende dovevano in genere preoccuparsi solo del danneggiamento del sito Web da parte di singoli utenti malintenzionati, interessati soprattutto a dar prova di sé. Oggi gli utenti malintenzionati sono molto più sofisticati e organizzati. Hanno spesso obiettivi finanziari e strategici specifici, Hanno inoltre a disposizione più risorse, in quanto potrebbero essere finanziate in base a stati di nazione o crimini organizzati.

Queste nuove realtà hanno portato a un livello di professionalità senza precedenti nelle schiere di utenti malintenzionati. Non sono più interessati al danneggiamento del Web. Ora sono interessati al furto di informazioni, conti finanziari e dati privati, che possono usare per generare liquidità nel mercato aperto o per sfruttare una particolare posizione aziendale, politica o militare. Ancora più preoccupanti di quelli con un obiettivo finanziario sono gli utenti malintenzionati che violano le reti per danneggiare l'infrastruttura e le persone.

Per rispondere a questa situazione, le organizzazioni distribuiscono spesso varie soluzioni specifiche, concentrandosi sulla difesa del perimetro o degli endpoint dell'organizzazione attraverso la ricerca delle firme di attacchi noti. Queste soluzioni tendono a generare un numero elevato di avvisi con un basso livello di affidabilità, che richiedono l'intervento di un analista di sicurezza per la valutazione e l'analisi. La maggior parte delle organizzazioni non ha il tempo e le competenze che servono per rispondere a questi avvisi, quindi molti rimangono senza risposta.  

Inoltre, gli utenti malintenzionati hanno cambiato i metodi di attacco, per compromettere molte difese basate sulle firme e [adattarsi agli ambienti cloud](https://azure.microsoft.com/blog/detecting-threats-with-azure-security-center/). Per identificare le minacce emergenti più rapidamente e accelerare le operazioni di rilevamento e risposta, sono quindi necessari nuovi approcci.


## <a name="continuous-monitoring-and-assessments"></a>Monitoraggio e valutazioni continui

Il Centro sicurezza di Azure si avvale di team dedicati alle ricerche sulla sicurezza e all'analisi scientifica dei dati in Microsoft che monitorano costantemente le modifiche che avvengono nel panorama delle minacce. Sono incluse le iniziative seguenti:

* **Monitoraggio dell'intelligence per le minacce**: l'intelligence per le minacce include meccanismi, indicatori, implicazioni e consigli utili sulle minacce attuali o emergenti. Queste informazioni sono condivise nella community sulla sicurezza e Microsoft monitora costantemente i feed di intelligence per le minacce da origini interne ed esterne.
* **Condivisione dei segnali**: le informazioni dettagliate provenienti dai team della sicurezza nell'ampio portfolio di servizi, server e dispositivi endpoint client locali e cloud di Microsoft vengono condivise e analizzate.
* **Specialisti della sicurezza Microsoft**: collaborazione continua con i team Microsoft che operano in ambiti di sicurezza specializzati, ad esempio analisi forense e rilevamento di attacchi Web.
* **Ottimizzazione del rilevamento**: gli algoritmi vengono eseguiti su set di dati reali dei clienti e ricercatori che si occupano di sicurezza collaborano con i clienti per convalidare i risultati. Per perfezionare gli algoritmi di Machine Learning vengono usati veri e falsi positivi.

Questi sforzi combinati convergono in rilevamenti nuovi e migliorati, da cui è possibile trarre vantaggio immediatamente, senza che sia richiesta alcuna azione.

## <a name="how-does-security-center-detect-threats"></a>Metodo di rilevamento delle minacce del Centro sicurezza <a name="detect-threats"> </a>

I ricercatori Microsoft nell'ambito della sicurezza sono costantemente impegnati nella ricerca delle minacce. A causa della nostra presenza globale nel cloud e in locale, abbiamo accesso a un ampio set di dati di telemetria. La vasta gamma di set di set di impostazioni consente di individuare nuovi modelli di attacco e tendenze nei prodotti aziendali e consumer locali, oltre ai Servizi online. Di conseguenza, il Centro sicurezza può aggiornare rapidamente gli algoritmi di rilevamento a fronte del rilascio di exploit nuovi e sofisticati da parte di utenti malintenzionati. Questo approccio consente di tenere il passo con un ambiente caratterizzato da minacce in rapida evoluzione.

Per rilevare minacce reali e ridurre i falsi positivi, il Centro sicurezza raccoglie, analizza e integra i dati di log generati dalle risorse di Azure e dalla rete. Funziona anche con soluzioni partner connesse, ad esempio soluzioni di protezione endpoint e firewall. Per identificare le minacce, il Centro sicurezza analizza queste informazioni, spesso correlando quelle raccolte da più origini.

![Raccolta dati e presentazione del Centro sicurezza](./media/security-center-alerts-overview/security-center-detection-capabilities.png)

Il Centro sicurezza si avvale di analisi della sicurezza avanzate, che vanno ben oltre gli approcci basati sulle firme. I progressi tecnologici in ambito Big Data e [Machine Learning](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) vengono sfruttati per valutare gli eventi nell'intera l'infrastruttura cloud, rilevando minacce che sarebbe impossibile identificare con approcci manuali e stimando l'evoluzione degli attacchi. Queste analisi della sicurezza includono:

- **Intelligence per le minacce integrata**: Microsoft vanta un'enorme quantità di dati di intelligence per le minacce globali. I dati di telemetria passano da più origini, ad esempio Azure, Microsoft 365, Microsoft CRM Online, Microsoft Dynamics AX, outlook.com, MSN.com, Microsoft Digital Crimes Unit (DC) e Microsoft Security Response Center (MSRC). I ricercatori ricevono anche informazioni di intelligence per le minacce condivise tra i principali provider di servizi cloud e i feed di terze parti. Il Centro sicurezza di Azure usa queste informazioni per avvisare gli utenti nel caso di minacce provenienti da attori dannosi noti.

- **Analisi del comportamento**: L'analisi del comportamento è una tecnica che analizza e confronta i dati con una raccolta di modelli noti. Tuttavia, questi modelli non sono semplici firme. Sono determinati usando algoritmi di Machine Learning complessi applicati a set di dati di grandi dimensioni. Sono anche definiti tramite l'attento esame di comportamenti dannosi da parte di analisti esperti. Il Centro sicurezza di Azure può usare le analisi del comportamento per identificare le risorse compromesse in base all'analisi dei log delle macchine virtuali, dei dispositivi di rete virtuale, dell'infrastruttura e di altre origini.

- **Rilevamento anomalie**: Il Centro sicurezza di Azure usa inoltre il rilevamento anomalie per identificare le minacce. A differenza dell'analisi del comportamento, che dipende da modelli noti derivati da set di dati di grandi dimensioni, il rilevamento anomalie è più "personalizzato" e incentrato sulle baseline specifiche delle distribuzioni. Le tecniche di apprendimento automatico vengono applicate per determinare la normale attività per le distribuzioni dei clienti e quindi vengono generate regole per definire le condizioni degli outlier che possono rappresentare un evento di sicurezza.

## <a name="how-are-alerts-classified"></a>Classificazione degli avvisi

Il Centro sicurezza assegna una gravità agli avvisi, in modo da assegnare una priorità in base alla quale rispondere a ogni avviso, in modo che quando una risorsa viene compromessa, è possibile accedervi immediatamente. Il livello di gravità è basato sul grado di attendibilità riscontrato dal Centro sicurezza nell'individuazione o nell'analisi usata per emettere l'avviso, nonché sul grado di fiducia con cui si ritiene che vi sia un intento dannoso dietro l'attività che ha portato all'avviso.

> [!NOTE]
> La gravità degli avvisi viene visualizzata in modo diverso nel portale e nelle versioni dell'API REST precedenti a 01-01-2019. Se si usa una versione precedente dell'API, eseguire l'aggiornamento per un'esperienza coerente, come descritto di seguito.

| Gravità          | Risposta consigliata      |
|---|---|
| **Alta**          | esiste una forte probabilità che la risorsa sia compromessa. È consigliabile controllarla immediatamente. Il Centro sicurezza ha una certezza elevata sia delle finalità dannose che delle conclusioni usate per inviare l'avviso, ad esempio un avviso che rileva l'esecuzione di uno strumento dannoso noto, come Mimikatz, uno strumento comune usato per il furto di credenziali.                                                                                                               |
| **Media**        | Si tratta probabilmente di un'attività sospetta che può indicare la compromissione di una risorsa. La fiducia del Centro sicurezza nell'analisi o nella conclusione è media e la certezza delle finalità dannose è da media a elevata. Si tratta in genere di rilevamenti basati su anomalie o sull'apprendimento automatico, ad esempio un tentativo di accesso da una posizione anomala.                                                                                                                |
| **Bassa**           | potrebbe trattarsi di un falso positivo o di un attacco bloccato. Il Centro sicurezza non è sufficientemente sicuro che lo scopo sia dannoso e l'attività potrebbe essere innocente. Ad esempio, log Clear è un'azione che può verificarsi quando un utente malintenzionato tenta di nascondere le proprie tracce, ma in molti casi è un'operazione di routine eseguita dagli amministratori. Il Centro sicurezza non segnala in genere quando sono stati bloccati attacchi, a meno che non si tratti di un caso interessante che è consigliabile esaminare. |
| **Informativo** | gli avvisi informativi verranno visualizzati solo quando si esegue il drill-down di un evento imprevisto relativo alla sicurezza oppure se si usa l'API REST con un ID avviso specifico. Un evento imprevisto è in genere costituito da un certo numero di avvisi, alcuni dei quali potrebbero apparire in modo autonomo come solo informativo, ma nel contesto degli altri avvisi può essere degno di un aspetto più vicino.                                                                                                                         |

## <a name="export-alerts"></a>Esportare gli avvisi

Sono disponibili diverse opzioni per la visualizzazione degli avvisi all'esterno del Centro sicurezza, tra cui:

- **Scaricare il report CSV** nel dashboard degli avvisi fornisce un'esportazione monouso nel volume condiviso cluster.
- L' **esportazione continua** da impostazioni & prezzi consente di configurare flussi di avvisi di sicurezza e raccomandazioni per log Analytics aree di lavoro e hub eventi. [Altre informazioni sull'esportazione continua](continuous-export.md)
- Il **connettore Azure Sentinel** trasmette gli avvisi di sicurezza dal centro sicurezza di Azure in Sentinel di Azure. [Altre informazioni sulla connessione del Centro sicurezza di Azure con Sentinel di Azure](../sentinel/connect-azure-security-center.md)

## <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Correlazione tra Smart Alert cloud nel centro sicurezza di Azure (eventi imprevisti)

Il Centro sicurezza di Azure analizza continuamente i carichi di lavoro del cloud ibrido usando analisi avanzate e Intelligence per le minacce per segnalare attività dannose.

La maggior parte della copertura delle minacce sta crescendo. La necessità di rilevare anche il minimo compromesso è importante e può essere difficile per gli analisti della sicurezza valutare i diversi avvisi e identificare un attacco effettivo. Il Centro sicurezza consente agli analisti di affrontare la fatica degli avvisi. Consente di diagnosticare gli attacchi non appena si verificano, correlando avvisi diversi e segnali di fedeltà bassa negli eventi imprevisti della sicurezza.

Fusion Analytics è la tecnologia e il back-end analitico che alimenta gli eventi imprevisti del Centro sicurezza, consentendo al reparto IT di correlare diversi avvisi e segnali contestuali insieme. Fusion esamina i diversi segnali segnalati in una sottoscrizione tra le risorse. Fusion trova modelli che rivelano la progressione degli attacchi o segnali con informazioni contestuali condivise, a indicare che è necessario usare una procedura di risposta unificata.

Fusion Analytics combina le informazioni sul dominio di sicurezza con intelligenza artificiale per analizzare gli avvisi, scoprendo nuovi modelli di attacco man mano che si verificano. 

Il Centro sicurezza usa la matrice di attacco MITRE per associare gli avvisi alla finalità percepita, contribuendo a formalizzare la conoscenza del dominio di sicurezza. Inoltre, usando le informazioni raccolte per ogni passaggio di un attacco, il Centro sicurezza può escludere l'attività che sembra essere una procedura di attacco, ma in realtà non lo è.

Poiché gli attacchi si verificano spesso in tenant diversi, il Centro sicurezza può combinare gli algoritmi di intelligenza artificiale per analizzare le sequenze di attacco segnalate in ogni sottoscrizione. Questa tecnica identifica le sequenze di attacco come modelli di avviso prevalenti, anziché essere associato a vicenda.

Durante un'indagine di un evento imprevisto, gli analisti spesso necessitano di un contesto aggiuntivo per raggiungere un verdetto sulla natura della minaccia e su come mitigarlo. Ad esempio, anche quando viene rilevata un'anomalia di rete, senza comprendere quanto altro accade sulla rete o per quanto riguarda la risorsa di destinazione, è difficile capire quali azioni eseguire successivamente. Per semplificare, un evento imprevisto di sicurezza può includere elementi, eventi correlati e informazioni. Le informazioni aggiuntive disponibili per gli eventi imprevisti di sicurezza variano a seconda del tipo di minaccia rilevato e della configurazione dell'ambiente. 

> [!TIP]
> Per un elenco degli avvisi relativi agli eventi imprevisti della sicurezza che possono essere generati da Fusion Analytics, vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-fusion).

:::image type="content" source="./media/security-center-alerts-cloud-smart/security-incident.png" alt-text="Screenshot del report rilevato evento imprevisto della sicurezza":::

Per gestire gli eventi imprevisti della sicurezza, vedere [come gestire gli eventi imprevisti della sicurezza nel centro sicurezza di Azure](security-center-incident.md).

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono stati descritti i diversi tipi di avvisi disponibili nel Centro sicurezza. Per altre informazioni, vedere:

- [Avvisi di sicurezza nel log attività di Azure](https://go.microsoft.com/fwlink/?linkid=2114113) : oltre a essere disponibili nel portale di Azure o a livello di codice, gli avvisi di sicurezza e gli eventi imprevisti vengono controllati come eventi nel log attività di Azure
- [Tabella di riferimento degli avvisi di Azure Defender](alerts-reference.md)
- [Rispondere agli avvisi di sicurezza](security-center-managing-and-responding-alerts.md#respond-to-security-alerts)