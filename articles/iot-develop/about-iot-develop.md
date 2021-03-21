---
title: Introduzione allo sviluppo di applicazioni e dispositivi Azure
description: Informazioni su come usare Azure Internet per lo sviluppo di dispositivi embedded e la creazione di applicazioni cloud abilitate per i dispositivi.
author: ryanwinter
ms.author: rywinter
ms.service: iot-develop
ms.topic: overview
ms.date: 01/11/2021
ms.openlocfilehash: dd4e53eebe6593db457798f009d3d05ddcbd77b8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "100654950"
---
# <a name="what-is-azure-iot-device-and-application-development"></a>Che cos'è lo sviluppo di applicazioni e dispositivi Azure?

Azure è una raccolta di servizi gestiti e di piattaforma che connettono, monitorano e controllano i dispositivi. Azure Internet delle cose offre agli sviluppatori una serie completa di opzioni. Le opzioni includono le piattaforme per dispositivi, i servizi cloud, gli SDK e gli strumenti per la creazione di applicazioni cloud abilitate per i dispositivi.

Questo articolo illustra alcune considerazioni chiave per gli sviluppatori che iniziano a usare Azure Internet. Questi concetti orientano l'utente, come sviluppatore di dispositivi per le cose, alle opzioni di Azure e come iniziare. In particolare, l'articolo descrive i concetti seguenti:
- [Informazioni sui ruoli di sviluppo del dispositivo](#device-development-roles)
- [Scelta dell'hardware](#choosing-your-hardware)
- [Scelta di un SDK](#choosing-an-sdk)
- [Selezione delle opzioni di connessione](#selecting-connection-options)

## <a name="device-development-roles"></a>Ruoli di sviluppo del dispositivo
Questo articolo illustra due ruoli comuni che è possibile osservare tra gli sviluppatori di dispositivi. Come usato in questo caso, un ruolo è una raccolta di attività di sviluppo correlate. È utile comprendere il tipo di ruolo di sviluppo che si sta attualmente lavorando. Il ruolo influisca sulle numerose scelte di sviluppo effettuate.

* **Sviluppo di applicazioni per dispositivi:** L'allineamento con le procedure di sviluppo moderne è destinato a molti linguaggi di ordine superiore e viene eseguito in un sistema operativo di uso generico, ad esempio Windows o Linux.

* **Sviluppo di dispositivi incorporati:** Descrive lo sviluppo per i dispositivi con vincoli di risorse. Un dispositivo con vincoli di risorse verrà usato spesso per ridurre i costi per unità, il consumo di energia o le dimensioni del dispositivo. Questi dispositivi hanno il controllo diretto sulla piattaforma hardware in cui vengono eseguiti.

### <a name="device-application-development"></a>Sviluppo di applicazioni per dispositivi
Gli sviluppatori di applicazioni per dispositivi stanno adattando i dispositivi esistenti per connettersi al cloud e integrarsi nelle soluzioni Internet delle cose. Questi dispositivi possono supportare linguaggi di ordine superiore, ad esempio C# o Python, e spesso supportano un solido sistema operativo per utilizzo generico, ad esempio Windows o Linux. I dispositivi di destinazione comuni includono PC, contenitori, Raspberry Pis e dispositivi mobili. 

Invece di sviluppare dispositivi vincolati su larga scala, questi sviluppatori si concentrano sull'abilitazione di uno scenario di Internet delle cose specifico richiesto dalla soluzione cloud. Alcuni di questi sviluppatori funzioneranno anche su dispositivi vincolati per la soluzione cloud. Per gli sviluppatori che usano dispositivi vincolati, vedere il percorso di [sviluppo del dispositivo embedded](#embedded-device-development) di seguito.

> [!TIP]
> Per iniziare, vedere gli [SDK per dispositivi non vincolati](about-iot-sdks.md#unconstrained-device-sdks) .

### <a name="embedded-device-development"></a>Sviluppo di dispositivi incorporati
Lo sviluppo incorporato è destinato a dispositivi vincolati con memoria e elaborazione limitate. I dispositivi vincolati limitano ciò che è possibile ottenere rispetto a una piattaforma di sviluppo tradizionale.

I dispositivi incorporati usano in genere un sistema operativo in tempo reale (RTO) o nessun sistema operativo. I dispositivi incorporati hanno il controllo completo sui propri componenti hardware, a causa della mancanza di un sistema operativo per utilizzo generico. Questo fatto rende i dispositivi incorporati una scelta ottimale per i sistemi in tempo reale.

Gli SDK incorporati correnti sono destinati al linguaggio **C** . Gli SDK incorporati non forniscono alcun sistema operativo o il supporto di Azure RTO. Sono progettate tenendo presenti le destinazioni incorporate. Le considerazioni di progettazione includono la necessità di un'impronta minima e una progettazione di allocazione di memoria non.

Se il dispositivo è in grado di eseguire un sistema operativo di uso generico, è consigliabile seguire il percorso di [sviluppo dell'applicazione del dispositivo](#device-application-development) . Offre un set di opzioni di sviluppo più completo.

> [!TIP]
> Per iniziare, vedere gli [SDK per dispositivi vincolati](about-iot-sdks.md#constrained-device-sdks) .

## <a name="choosing-your-hardware"></a>Scelta dell'hardware
I dispositivi Azure Internet sono i blocchi predefiniti di base di una soluzione Internet e sono responsabili dell'osservazione e dell'interazione con l'ambiente. Sono disponibili molti tipi diversi di dispositivi Internet e è utile comprendere i tipi di dispositivi esistenti e il modo in cui possono influito sul processo di sviluppo.

Per altre informazioni sulla differenza tra i tipi di dispositivi trattati in questo articolo, vedere [informazioni sui tipi](concepts-iot-device-types.md)di dispositivi.

## <a name="choosing-an-sdk"></a>Scelta di un SDK
Per gli sviluppatori di dispositivi Azure Internet, è disponibile un set diversificato di SDK per dispositivi e SDK dei servizi di Azure, che consentono di creare applicazioni cloud abilitate per i dispositivi. Gli SDK semplificano il lavoro di sviluppo e semplificano la maggior parte della complessità della connessione e della gestione dei dispositivi. 

Come indicato nella sezione [ruoli di sviluppo dei dispositivi](#device-development-roles) , sono disponibili tre tipi di SDK per lo sviluppo di dispositivi:
- SDK per dispositivi incorporati (per i dispositivi vincolati)
- SDK per dispositivi (per l'uso di linguaggi di ordine superiore per connettere i dispositivi esistenti alle applicazioni Internet delle cose)
- SDK dei servizi (per la creazione di soluzioni Azure per la connessione di dispositivi ai servizi)

Per altre informazioni sulla scelta di un dispositivo o di un SDK di servizi Azure, vedere [Panoramica degli SDK per dispositivi Azure](about-iot-sdks.md).

## <a name="selecting-connection-options"></a>Selezione delle opzioni di connessione
Un passaggio importante del processo di sviluppo è la scelta del set di opzioni che verrà usato per connettere e gestire i dispositivi. È necessario considerare due aspetti importanti:
- Scelta di una piattaforma di applicazioni Internet per ospitare i dispositivi. Per quanto riguarda Azure, ciò significa scegliere l'hub o IoT Central.
- Scelta degli strumenti di sviluppo che consentono di connettere, gestire e monitorare i dispositivi.

Per altre informazioni sulla selezione di una piattaforma applicativa e degli strumenti, vedere [Panoramica: opzioni di connessione per gli sviluppatori di dispositivi Azure](concepts-overview-connection-options.md)Internet.

## <a name="next-steps"></a>Passaggi successivi
Selezionare una delle seguenti serie di guide introduttive più rilevanti per il proprio ruolo di sviluppo. Questi articoli illustrano le nozioni di base per la creazione di un'applicazione Azure Internet per ospitare i dispositivi, usando un SDK, connettendo un dispositivo e inviando dati di telemetria.  
- Per lo sviluppo di applicazioni per dispositivi:  [Guida introduttiva: inviare dati di telemetria da un dispositivo ad Azure IOT Central](quickstart-send-telemetry-python.md)
- Per lo sviluppo di dispositivi embedded: [Introduzione allo sviluppo di dispositivi embedded di Azure](quickstart-device-development.md)
