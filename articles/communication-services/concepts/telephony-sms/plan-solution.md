---
title: Tipi di numeri di telefono in Servizi di comunicazione di Azure
titleSuffix: An Azure Communication Services concept document
description: Informazioni su come usare in modo efficace tipi diversi di numeri di telefono per SMS e telefonia.
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 10/05/2020
ms.topic: overview
ms.custom: references_regions
ms.service: azure-communication-services
ms.openlocfilehash: 8a811a033a2d6e2229c83899e237f996ccddd7b3
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102430412"
---
# <a name="phone-number-types-in-azure-communication-services"></a>Tipi di numeri di telefono in Servizi di comunicazione di Azure



[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

> [!IMPORTANT]
> La disponibilità dei numeri di telefono è attualmente limitata alle sottoscrizioni di Azure a pagamento che dispongono di un indirizzo di fatturazione nelle risorse Stati Uniti e servizi di comunicazione che hanno un percorso dati negli Stati Uniti. Non è possibile acquisire i numeri di telefono per gli account di valutazione o usare i crediti gratuiti di Azure. Per ulteriori informazioni, visitare la sezione [idoneità](#azure-subscriptions-eligibility) per la sottoscrizione di questo documento.


Servizi di comunicazione di Azure consente di usare i numeri di telefono per effettuare chiamate vocali e inviare messaggi SMS con la rete PSTN (Public Switched Telephone Network). In questo documento verranno esaminati i tipi di numero di telefono, le opzioni di configurazione e la disponibilità a livello di area per pianificare la soluzione di telefonia e SMS usando Servizi di comunicazione.

## <a name="azure-subscriptions-eligibility"></a>Idoneità delle sottoscrizioni di Azure

Per acquisire un numero di telefono, è necessario avere una sottoscrizione di Azure a pagamento. Non è possibile acquistare i numeri di telefono con account di valutazione o usando i crediti gratuiti di Azure. 

La disponibilità dei numeri di telefono è attualmente limitata alle sottoscrizioni di Azure con un indirizzo di fatturazione nelle risorse Stati Uniti e servizi di comunicazione che hanno un percorso dati negli Stati Uniti.


## <a name="number-types-and-features"></a>Tipi di numeri e funzionalità
Servizi di comunicazione offre due tipi di numero di telefono, ossia **numero locale** e **numero verde**. 

### <a name="local-numbers"></a>Numeri locali
I numeri locali (geografici) sono numeri di telefono di 10 cifre costituiti da prefissi locali degli Stati Uniti. Ad esempio, `+1 (206) XXX-XXXX` è un numero locale con prefisso `206`. Questo prefisso viene assegnato alla città di Seattle. Questi numeri di telefono vengono in genere usati da privati e aziende locali. Servizi di comunicazione di Azure offre numeri locali negli Stati Uniti. Questi numeri possono essere usati per effettuare telefonate, ma non per inviare messaggi SMS. 

### <a name="toll-free-numbers"></a>Numeri verdi
I numeri verdi sono numeri telefonici di 10 cifre con prefissi distinti che è possibile chiamare gratuitamente da qualsiasi numero di telefono. Ad esempio, `+1 (800) XXX-XXXX` è un numero verde nell'area America del Nord. Questi numeri di telefono vengono in genere usati per servizi di assistenza clienti. Servizi di comunicazione di Azure offre numeri verdi negli Stati Uniti. Questi numeri possono essere usati per effettuare telefonate e per inviare messaggi SMS. I numeri verdi non possono essere usati da persone e possono essere assegnati solo ad applicazioni.

#### <a name="choosing-a-phone-number-type"></a>Scelta di un numero di telefono

Se il numero di telefono verrà usato da un'applicazione, ad esempio per effettuare chiamate o inviare messaggi per conto del servizio, è possibile selezionare un numero verde o locale (geografico). È possibile selezionare un numero verde se l'applicazione invia messaggi SMS e/o effettua chiamate.

Se il numero di telefono viene usato da una persona, ad esempio un'utente dell'applicazione di chiamate, è necessario usare un numero di telefono locale (geografico). 

La tabella seguente riepiloga i tipi di numeri di telefono disponibili: 

| Tipo di numero di telefono | Esempio                              | Paesi di disponibilità    | Funzionalità del numero di telefono |Caso d'uso comune                                                                                                     |
| ----------------- | ------------------------------------ | ----------------------- | ------------------------|------------------------------------------------------------------------------------------------------------------- |
| Locale (geografico)        | +1 (prefisso locale) XXX XX XX  | US                      | Chiamate (in uscita) | Assegnazione di numeri di telefono agli utenti delle applicazioni  |
| Numero verde         | \+ 1 (*codice* area n. verde) XXX XX XX | US                      | Chiamate (in uscita), SMS (in ingresso/in uscita)| Assegnazione di numeri di telefono tramite sistemi IRV (Interactive Voice Response), bot o applicazioni di SMS                                        |


### <a name="phone-number-features-in-azure-communication-services"></a>Funzionalità dei numeri di telefono in Servizi di comunicazione di Azure 

[!INCLUDE [Emergency Calling Notice](../../includes/emergency-calling-notice-include.md)]

Per la maggior parte dei numeri di telefono, è possibile configurare un set di funzionalità "à la carte". Queste funzionalità possono essere selezionate al momento del lease dei numeri di telefono all'interno di Servizi di comunicazione di Azure.

Le funzionalità disponibili variano in base al paese in cui si opera, al caso d'uso e al tipo di numero di telefono selezionato. La variazione in base al paese è dovuta a requisiti normativi. Servizi di comunicazione di Azure offre le funzionalità seguenti per i numeri di telefono:

- **SMS in uscita unidirezionali** Questa opzione consente di inviare messaggi SMS agli utenti. Può essere utile negli scenari di notifica e dell'autenticazione a due fattori. 
- **SMS in ingresso e in uscita bidirezionali** Questa opzione consente di inviare e ricevere messaggi dagli utenti usando i numeri di telefono. Può essere utile negli scenari di assistenza clienti.
- **Chiamate telefoniche in uscita unidirezionali** Questa opzione consente di effettuare chiamate agli utenti e di configurare l'ID chiamante per le chiamate in uscita effettuate dal servizio. Può essere utile negli scenari di assistenza clienti e notifica vocale.

## <a name="countryregion-availability"></a>Disponibilità per Paese/area geografica

La tabella seguente illustra le località in cui è possibile acquisire i diversi numeri di telefono, oltre alle funzionalità per chiamate e SMS in ingresso e in uscita associate a questi tipi di numero.

|Tipo di numero| Località di acquisizione | Per effettuare chiamate a                                        | Per ricevere chiamate da                                    |Per inviare messaggi a       | Per ricevere messaggi da |
|-----------| ------------------ | ---------------------------------------------------  |-------------------------------------------------------|-----------------------|--------|
| Locale (geografico)  | US                 | Stati Uniti, Canada, Regno Unito, Germania, Francia e altri ancora*| Stati Uniti, Canada, Regno Unito, Germania, Francia e altri ancora* |Non disponibile| Non disponibile |
| Numero verde | US                 | US                                                   | US                                                    |US                | US |

*Per altre informazioni sulle destinazioni e sui prezzi delle chiamate, vedere la [pagina dei prezzi](../pricing.md).


## <a name="next-steps"></a>Passaggi successivi

### <a name="quickstarts"></a>Guide rapide introduttive

- [Ottenere un numero di telefono](../../quickstarts/telephony-sms/get-phone-number.md)
- [Effettuare una chiamata](../../quickstarts/voice-video-calling/calling-client-samples.md)
- [Inviare un SMS](../../quickstarts/telephony-sms/send.md)

### <a name="conceptual-documentation"></a>Documentazione concettuale

- [Concetti relativi alle chiamate vocali e video](../voice-video-calling/about-call-types.md)
- [Concetti relativi alla telefonia](./telephony-concept.md)
- [Flussi delle chiamate](../call-flows.md)
- [Prezzi](../pricing.md)
