---
title: Criteri di messaggistica
titleSuffix: An Azure Communication Services concept document
description: Informazioni sui criteri di messaggistica SMS.
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 03/19/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: bb9765c2620f45d67bf888f8bfe8a4dee450cfd6
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2021
ms.locfileid: "105646761"
---
# <a name="azure-communication-services-messaging-policy"></a>Criteri di messaggistica di servizi di comunicazione di Azure

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Servizi di comunicazione di Azure sta trasformando il modo in cui i clienti si impegnano con i propri clienti creando esperienze di comunicazione avanzate e personalizzate che sfruttano i vantaggi offerti dagli stessi servizi di livello aziendale che fanno parte di Microsoft teams e Skype. Integra la funzionalità di messaggistica SMS nelle tue soluzioni di comunicazione per raggiungere i clienti in qualsiasi momento e in qualsiasi luogo che necessitano di supporto. Per iniziare, è sufficiente tenere presente alcuni requisiti di messaggistica.

Sappiamo che i requisiti di messaggistica possono sembrare scoraggianti da apprendere, ma sono semplici quanto ricordare "OCM":

- C-consenso
- O-Opt-Out
- M-contenuto del messaggio
- Spoofing S

Questo criterio di messaggistica è stato sviluppato per aiutare a soddisfare i requisiti normativi e ad allinearsi alle procedure consigliate. 

[!INCLUDE [Notice](../../includes/messaging-policy-include.md)]

## <a name="consent"></a>Consenso 

### <a name="what-is-consent"></a>Che cos'è il consenso?

Il consenso è un contratto tra l'utente e il destinatario del messaggio che consente di inviare messaggi automatizzati. È necessario ottenere il consenso prima di inviare il primo messaggio ed è necessario chiarire al destinatario che si sta accettando di ricevere messaggi dall'utente. Questa procedura è nota come ricezione del consenso esplicito precedente dall'utente che si intende assegnare al messaggio.

I messaggi inviati devono corrispondere allo stesso tipo di messaggi che il destinatario ha accettato di ricevere e deve essere inviato solo al numero fornito dal destinatario. Se si intende inviare messaggi informativi, ad esempio gli avvisi o i promemoria di appuntamento, il consenso può essere scritto o orale. Se si intende inviare messaggi promozionali, ad esempio messaggi di vendita o di marketing che promuovono un prodotto o un servizio, è necessario scrivere il consenso.

### <a name="how-do-you-obtain-consent"></a>Come ottenere il consenso?

Il consenso può essere ottenuto in diversi modi, ad esempio:

- Quando un utente immette il proprio numero di telefono in un sito Web, 
- Quando un utente avvia uno scambio di messaggi di testo o
- Quando un utente invia una parola chiave di iscrizione al numero di telefono. 
 
Indipendentemente dal modo in cui viene ottenuto il consenso, l'utente e i clienti devono assicurarsi che il consenso non sia ambiguo. L'ambito del consenso deve essere chiaro al destinatario.


### <a name="consent-requirements"></a>Requisiti di consenso:

- Fornire una "chiamata all'azione" prima di ottenere il consenso. L'utente e i clienti devono fornire i potenziali destinatari dei messaggi con una "chiamata all'azione" che li invita a acconsentire esplicitamente al programma di messaggistica. La chiamata all'azione deve includere, come minimo: (1) l'identità del mittente del messaggio, (2) cancellare le istruzioni per il consenso esplicito, (3) istruzioni per il rifiuto esplicito e (4) qualsiasi costo di messaggistica associato.
- Il consenso non è trasferibile o assegnabile. Qualsiasi consenso fornito da un utente non potrà essere trasferito o venduto a una terza parte non affiliata. Se si raccoglie il consenso di un utente per una terza parte, è necessario identificare chiaramente la terza parte per la persona. È inoltre necessario indicare che il consenso ottenuto si applica solo alle comunicazioni di terze parti.
- Il consenso è limitato allo scopo. Un utente che fornisce il proprio numero per uno scopo specifico acconsente a ricevere le comunicazioni solo per lo scopo specifico e per il mittente del messaggio specifico. Prima di ottenere il consenso, è necessario notificare chiaramente al destinatario del messaggio previsto se si invieranno messaggi o messaggi ricorrenti da un affiliato.

### <a name="consent-best-practices"></a>Procedure consigliate per il consenso:

Oltre ai requisiti di messaggistica descritti in precedenza, è consigliabile implementare diverse procedure consigliate comuni, tra cui: 

- Informazioni dettagliate sulla chiamata all'azione. Per assicurarsi di ottenere il consenso appropriato, fornire
  - nome o descrizione del programma di messaggistica o del prodotto
  - numero o i numeri dai quali i destinatari riceveranno i messaggi e 
  - eventuali termini e condizioni applicabili prima che un singolo utente acconsente alla ricezione di messaggi.
- Record accurati del consenso. È necessario conservare i record di qualsiasi consenso fornito da un utente per almeno quattro anni. I record di consenso possono includere:
  - timestamp
  - supporto per il quale è stato ottenuto il consenso
  - campagna specifica per cui è stato ottenuto il consenso
  - Acquisizioni schermate
  - ID di sessione o indirizzo IP dell'utente che ha acconsentito.
- Criteri di privacy e sicurezza. Gli sviluppatori sono invitati a fornire semplici criteri di privacy che i destinatari dei messaggi possono esaminare prima di ottenere il consenso. Si consiglia inoltre di mantenere i controlli di sicurezza proattivi per salvaguardare le informazioni private dei singoli utenti.


## <a name="double-opt-in-consent"></a>Consenso al doppio consenso esplicito:

Servizi di comunicazione di Azure consiglia di usare il doppio consenso esplicito per tutte le campagne di messaggistica. Il doppio consenso esplicito è un processo in due fasi in cui un singolo utente fornisce un primo consenso per ricevere determinati tipi di messaggi dall'utente. Viene quindi inviato un messaggio di consenso esplicito per confermare il proprio consenso. È necessario inviare più messaggi solo dopo che il destinatario del messaggio ne ha confermato il consenso.

Il messaggio di conferma iniziale inviato deve includere la propria identità, opzione per rifiutare esplicitamente i messaggi futuri, ad esempio l'utilizzo di un comando "STOP", un numero a pedaggio o un comando "HELP" per ulteriori informazioni, notificare che l'utente è registrato in un programma di messaggi ricorrente, una breve descrizione del programma, la frequenza con cui si intende inviare messaggi ricorrenti ed eventuali tariffe associate. 

### <a name="does-azure-communication-services-ever-require-double-opt-in-consent"></a>I servizi di comunicazione di Azure richiedono un consenso esplicito doppio?
Sì, mentre il doppio consenso esplicito è sempre consigliato, per i servizi di comunicazione di Azure è necessario usare il doppio consenso esplicito per alcuni tipi di campagne di messaggistica a causa dell'uso frequente negli schemi di phishing o la loro tendenza a produrre reclami da parte degli utenti. Queste campagne includono:
- Messaggi di garanzia automatica
- Piani assicurativi sull'integrità a breve termine
- Rifinanziamento del debito o messaggi di riduzione del tasso di interesse se non effettuati da un istituto finanziario
- Messaggi di generazione lead
- Lotterie, concorsi e Giveaway
- Offerte da lavoro a casa

Le campagne per le quali è richiesto il doppio consenso esplicito sono soggette a modifiche a discrezione dei servizi di comunicazione di Azure.

### <a name="exceptions-to-traditional-consent-rules"></a>Eccezioni alle regole di consenso tradizionali:
Anche se prima dell'invio di un messaggio è necessario il consenso esplicito precedente, esistono due situazioni in cui il consenso al messaggio di un individuo è implicito.

- Il destinatario avvia una comunicazione. Se un utente avvia una comunicazione inviando un messaggio all'utente, è possibile fornire informazioni rilevanti in risposta a una richiesta o a una richiesta specifica contenuta nel messaggio. Tuttavia, il consenso implicito fornito dall'utente è limitato alla conversazione avviata dall'utente, a meno che non si ottenga il consenso per altre comunicazioni.
- Esenzioni per servizi specifici. Sono disponibili diversi servizi specifici per i quali si potrebbe avere il consenso implicito per l'avvio di un messaggio. I più comuni sono: 
- messaggi di recapito del pacchetto
- messaggi dell'istituto finanziario che riguardano argomenti sensibili al tempo, ad esempio transazioni potenzialmente fraudolente o violazioni dei dati
- messaggi del provider sanitario che includono informazioni sensibili al tempo e uno scopo di trattamento, ad esempio promemoria per appuntamento o esami, risultati del Lab e notifiche di prescrizione. 
 
Nessuno di questi messaggi può includere richieste o annunci.


## <a name="opt-out"></a>Rifiutare esplicitamente

I destinatari dei messaggi possono revocare il consenso e rifiutare esplicitamente la ricezione di messaggi futuri attraverso qualsiasi mezzo ragionevole. Non è possibile designare un metodo esclusivo per i destinatari dei messaggi per revocare il consenso. 

### <a name="opt-out-requirements"></a>Requisiti per il rifiuto esplicito:

Assicurarsi che i destinatari dei messaggi possano rifiutare esplicitamente i messaggi futuri in qualsiasi momento. È anche necessario offrire più opzioni di rifiuto esplicito. Dopo che il destinatario di un messaggio è stato escluso, è consigliabile non inviare altri messaggi a meno che l'utente non fornisca il consenso rinnovato.

Uno dei meccanismi di rifiuto espliciti più comuni consiste nell'includere una parola chiave "STOP" nel messaggio iniziale di ogni nuova conversazione. Essere pronti a rimuovere i clienti che rispondono con una parola chiave "Stop" minuscola o con altre parole chiave comuni, ad esempio "Annulla sottoscrizione" o "Annulla". Dopo che un utente revoca il consenso, è necessario rimuoverlo da tutte le campagne di messaggistica ricorrenti, a meno che non si scelga espressamente di continuare a ricevere i messaggi da un particolare programma.

### <a name="opt-out-best-practices"></a>Procedure consigliate per il rifiuto esplicito:

Oltre alle parole chiave, altri meccanismi comuni di rifiuto esplicito includono la fornitura di un indirizzo di posta elettronica esplicito, il numero di telefono del personale del supporto tecnico o un collegamento per annullare la sottoscrizione alla pagina Web. 


### <a name="how-we-handle-opt-out-requests"></a>Modalità di gestione delle richieste di rifiuto esplicito:

Se una singola richiesta di rifiutare esplicitamente i messaggi futuri in un numero senza pedaggio di servizi di comunicazione di Azure, tutto il traffico ulteriore da tale numero verrà automaticamente arrestato. Tuttavia, è comunque necessario assicurarsi di non inviare altri messaggi per tale campagna di messaggistica da numeri nuovi o diversi. Se si è ottenuto separatamente il consenso esplicito per una campagna di messaggistica diversa, è possibile continuare a inviare messaggi da un numero diverso per tale campagna. Vedere la sezione Domande frequenti per ulteriori informazioni sulla [gestione del rifiuto esplicito](https://github.com/Prakulka/azure-docs-pr/blob/master/articles/communication-services/concepts/telephony-sms/sms-faq.md#how-can-i-receive-messages-using-azure-communication-services)

## <a name="message-content"></a>Contenuto del messaggio

### <a name="adult-content"></a>Contenuti per adulti:

Il contenuto del messaggio che include elementi di sesso, avversione, alcool, armi, tabacco, gioco d'azzardo, lotteria e concorsi può attivare requisiti aggiuntivi. Questo contenuto è espressamente vietato in alcune giurisdizioni. Se si invia un messaggio che include questo contenuto, è necessario rispettare tutte le leggi applicabili delle giurisdizioni in cui vengono ricevute le comunicazioni. Presso la richiesta di autorità giudiziaria o servizi di comunicazione di Azure, è necessario essere pronti a fornire la prova del consenso con leggi locali che regolano il contenuto per adulti.

Anche se tale contenuto non è lecito, è necessario includere un meccanismo di verifica dell'età al consenso esplicito per il destinatario del messaggio previsto dal contenuto per adulti. Nel Stati Uniti, i requisiti legali aggiuntivi si applicano alle comunicazioni di marketing indirizzate agli elementi figlio di età inferiore a 13. 

### <a name="prohibited-content"></a>Contenuto non consentito:

I servizi di comunicazione di Azure proibiscono il contenuto di alcuni messaggi indipendentemente dal consenso. Il contenuto vietato include:
- Contenuto che promuove attività illecite (ad esempio, evasione fiscale o crudelità degli animali nella Stati Uniti)
- Parole non definitive, sintesi vocale, molestia o altro discorso determinato come offensivo
- Contenuti pornografici
- Contenuto osceno o volgare
- Intimidazione e minacce
- Contenuto che intende frodare, ingannare, causare danni o ottenere in modo illecito qualsiasi valore 
- Contenuti che incitano danno, discriminazione o violenza
- Contenuto che diffonde malware
- Contenuto che intende eludere i requisiti di controllo dell'età

Si riserva il diritto di modificare l'elenco di contenuto del messaggio non consentito in qualsiasi momento.

## <a name="spoofing"></a>Spoofing

Lo spoofing è la causa di un numero di origine fuorviante o non accurato da visualizzare sul dispositivo di un destinatario del messaggio. Si sconsiglia vivamente a tutti i provider di servizi utilizzati dall'invio di messaggi contraffatti. Lo spoofing protegge l'identità del mittente del messaggio e impedisce ai destinatari dei messaggi di rifiutare facilmente le comunicazioni indesiderate. È anche necessario rispettare tutte le leggi di spoofing applicabili.

## <a name="final-thoughts"></a>Considerazioni finali

### <a name="legal-responsibility"></a>Responsabilità giuridica:

Questo criterio di messaggistica non costituisce consigli legali e si riserva il diritto di modificare i criteri in qualsiasi momento. Servizi di comunicazione di Azure non è responsabile di garantire che il contenuto, la tempistica o i destinatari dei messaggi dei clienti soddisfino tutti i requisiti legali applicabili. 

I clienti sono responsabili di tutti i requisiti di messaggistica. Se si è un provider di piattaforma o software che usa i servizi di comunicazione di Azure ai fini della messaggistica, è necessario richiedere che i clienti rispettino anche tutti i requisiti descritti in questo criterio di messaggistica. Per ulteriori linee guida, CTIA fornisce [principi di messaggistica e procedure consigliate](https://api.ctia.org/wp-content/uploads/2019/07/190719-CTIA-Messaging-Principles-and-Best-Practices-FINAL.pdf)utili.

### <a name="penalties"></a>Sanzioni

Invitiamo i nostri clienti a sviluppare e implementare criteri e procedure progettati per garantire la conformità a tutti i requisiti di messaggistica. Le violazioni dei requisiti di messaggistica possono compromettere le ammende che possono generare rapidamente un fumetto. Si tratta di un interesse migliore per apprendere e rispettare tutti i requisiti di messaggistica applicabili e sviluppare misure di sicurezza efficaci per contenere ed eliminare le violazioni prima di distribuirle. Se si violano i criteri di messaggistica o altri requisiti legali, si collaborerà con l'utente per garantire la conformità futura. Tuttavia, Microsoft si riserva il diritto di rimuovere qualsiasi cliente dalla piattaforma dei servizi di comunicazione di Azure che illustra un modello di non conformità con i criteri di messaggistica o i requisiti legali.
