---
title: Linee guida per la distribuzione responsabile della tecnologia Voice sintetica
titleSuffix: Azure Cognitive Services
description: Linee guida generali per la progettazione di Microsoft per l'utilizzo della tecnologia Voice sintetica. Queste sono state sviluppate in studi che Microsoft ha condotto con il talento vocale, i consumatori, nonché singoli utenti con problemi di sintesi vocale per guidare lo sviluppo responsabile della voce sintetica.
services: cognitive-services
author: benoah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: benoah
ms.openlocfilehash: 9a7a8868497433ea0de8f2f8b32f8e8fbaa497eb
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537185"
---
# <a name="guidelines-for-responsible-deployment-of-synthetic-voice-technology"></a>Linee guida per la distribuzione responsabile della tecnologia Voice sintetica

In questo articolo vengono fornite informazioni sulle linee guida di progettazione generali di Microsoft per l'utilizzo della tecnologia Voice sintetica. Queste linee guida sono state sviluppate in studi che Microsoft ha condotto con il talento vocale, i consumatori e gli utenti con problemi di sintesi vocale per guidare lo sviluppo responsabile delle voci sintetiche.

Per la distribuzione della tecnologia sintesi vocale sintetica, le linee guida seguenti si applicano alla maggior parte degli scenari.

### <a name="disclose-when-the-voice-is-synthetic"></a>Divulga quando la voce è sintetica
La divulgazione di una voce in un computer generato non solo riduce al minimo il rischio di risultati dannosi da un inganno, ma aumenta anche il trust nell'organizzazione che fornisce la voce. Altre informazioni su [come divulgare](concepts-disclosure-guidelines.md).

Microsoft richiede ai clienti di divulgare la natura sintetica della voce neurale personalizzata ai propri utenti. 
* Assicurarsi di fornire una divulgazione adeguata ai destinatari, in particolare quando si usa la voce di una persona nota, le persone fanno giudizio sulle informazioni in base alla persona che la consegna, a prescindere dal fatto che vengano eseguite in modo cosciente o inconscio.  Ad esempio, la divulgazione potrebbe essere condivisa verbalmente all'inizio di una trasmissione. Per ulteriori informazioni, vedere la pagina relativa ai [modelli di divulgazione](concepts-disclosure-patterns.md).   
* Prendere in considerazione la divulgazione corretta a elementi padre o ad altre parti con casi di utilizzo progettati per i minorenni e gli elementi figlio. se il caso d'uso è destinato a minorenni o bambini, è necessario assicurarsi che i genitori o i tutori legali siano in grado di comprendere la divulgazione sull'utilizzo di supporti sintetici e prendere la decisione giusta per i minorenni o i figli per l'utilizzo 

### <a name="select-appropriate-voice-types-for-your-scenario"></a>Selezionare i tipi vocali appropriati per lo scenario
Valutare con attenzione il contesto di utilizzo e i potenziali danni associati all'uso della voce sintetica. Ad esempio, le voci sintetiche ad alta fedeltà potrebbero non essere appropriate negli scenari ad alto rischio, ad esempio per la messaggistica personale, le transazioni finanziarie o situazioni complesse che richiedono adattabilità umana o empatia. 

Gli utenti possono anche avere aspettative diverse per i tipi di voce. Ad esempio, quando si è in ascolto di notizie riservate lette da una voce sintetica, alcuni utenti preferiscono un tono più empatico e simile a quello umano, mentre altri preferiscono una voce non distorta. Provare a testare l'applicazione per comprendere meglio le preferenze dell'utente.

### <a name="be-transparent-about-capabilities-and-limitations"></a>Essere trasparente sulle funzionalità e le limitazioni
È più probabile che gli utenti abbiano aspettative più elevate durante l'interazione con agenti vocali sintetici ad alta fedeltà. Quando le funzionalità di sistema non soddisfano tali aspettative, l'attendibilità può soffrire e può comportare esperienze spiacevoli o persino dannose.

### <a name="provide-optional-human-support"></a>Fornire supporto umano facoltativo
In scenari ambigui e transazionali, ad esempio un Call Support Center, gli utenti non considerano sempre attendibile un agente computer per rispondere in modo appropriato alle richieste. In queste situazioni può essere necessario il supporto umano, indipendentemente dalla qualità realistica della voce o della capacità del sistema.

## <a name="considerations-for-voice-talent"></a>Considerazioni per il talento vocale
Quando si lavora con il talento vocale, ad esempio gli attori vocali, per creare voci sintetiche, si applicano le linee guida riportate di seguito.

### <a name="obtain-meaningful-consent-from-voice-talent"></a>Ottenere un consenso significativo dal talento vocale
Il talento vocale deve avere il controllo sul modello vocale (come e dove verrà usato) e compensarlo per l'uso. Microsoft richiede che i clienti vocali personalizzati ottengano esplicitamente l'autorizzazione scritta dal proprio talento vocale per creare una voce sintetica e il suo accordo con la voce Talents contemplate la durata, l'utilizzo e le eventuali limitazioni del contenuto.  Se si crea una voce sintetica di una persona nota, è necessario fornire un modo per la persona dietro la voce per modificare o approvare il contenuto.

Un talento vocale non è a conoscenza dei potenziali usi dannosi della tecnologia e deve essere istruito dai proprietari del sistema sulle funzionalità della tecnologia. Microsoft richiede ai clienti di condividere la [divulgazione di Microsoft per il talento vocale](/legal/cognitive-services/speech-service/disclosure-voice-talent) , direttamente o tramite il rappresentante autorizzato del talento vocale, che descrive il modo in cui le voci sintetiche vengono sviluppate e operate insieme ai servizi di riconoscimento vocale.

## <a name="considerations-for-those-with-speech-disorders"></a>Considerazioni per gli utenti con problemi di sintesi vocale
Quando si lavora con utenti con problemi di sintesi vocale, per creare o distribuire la tecnologia vocale sintetica, si applicano le linee guida seguenti.

### <a name="provide-guidelines-to-establish-contracts"></a>Fornire linee guida per stabilire i contratti
Fornire linee guida per la definizione di contratti con singoli utenti che utilizzano la voce sintetica per assistenza nella comunicazione. Il contratto deve considerare la possibilità di specificare le entità che possiedono la voce, la durata dell'utilizzo, i criteri di trasferimento della proprietà, le procedure per eliminare il carattere vocale e come impedire l'accesso non autorizzato. Inoltre, è possibile abilitare il trasferimento della proprietà del tipo di carattere voce dopo la morte ai membri della famiglia, se è stata assegnata l'autorizzazione.

### <a name="account-for-inconsistencies-in-speech-patterns"></a>Account per le incoerenze nei modelli di riconoscimento vocale
Per i singoli utenti con problemi di sintesi vocale che registrano i propri tipi di carattere vocali, le incoerenze nel modello di riconoscimento vocale (i residui o l'impossibilità di pronunciare determinate parole) potrebbero complicare il processo di registrazione. In questi casi, le sessioni di registrazione e tecnologia della voce sintetica dovrebbero adattarsi (ovvero fornire interruzioni e un numero aggiuntivo di sessioni di registrazione).

### <a name="allow-modification-over-time"></a>Consenti modifiche nel tempo
Gli utenti con problemi di riconoscimento vocale desiderano apportare aggiornamenti alla voce sintetica per riflettere la durata (ad esempio, un bambino che raggiunge la pubertà). Le persone possono anche avere preferenze stilistiche che cambiano nel tempo e possono apportare modifiche a pitch, Accent o altre caratteristiche vocali.


## <a name="see-also"></a>Vedi anche

* [Divulgazione per il talento vocale](https://docs.microsoft.com/legal/cognitive-services/speech-service/disclosure-voice-talent?context=/azure/cognitive-services/speech-service/context/context)
* [Come divulgare](concepts-disclosure-guidelines.md)
* [Schemi di progettazione della divulgazione](concepts-disclosure-patterns.md)