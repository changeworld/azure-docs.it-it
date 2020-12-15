---
title: Introduzione all'analisi spaziale Visione artificiale
titleSuffix: Azure Cognitive Services
description: In questo documento vengono illustrati i concetti e le funzionalità di base di un contenitore di analisi spaziale Visione artificiale.
services: cognitive-services
author: tchristiani
manager: nitinme
ms.author: terrychr
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: e017fac551e3122cc6586b32423ff166462ccad8
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97513334"
---
# <a name="introduction-to-computer-vision-spatial-analysis"></a>Introduzione all'analisi spaziale Visione artificiale

Visione artificiale analisi spaziale è una nuova funzionalità dei servizi cognitivi di Azure Visione artificiale che aiuta le organizzazioni a massimizzare il valore dei propri spazi fisici comprendendo i movimenti e la presenza di persone all'interno di una determinata area. Consente di inserire video da telecamere TVCC o di sorveglianza, eseguire le competenze per intelligenza artificiale per estrarre informazioni dai flussi video e generare eventi che verranno usati da altri sistemi. Con l'input da un flusso della fotocamera, un'abilità di intelligenza artificiale può eseguire operazioni come il conteggio del numero di persone che entrano in uno spazio o la misurazione della conformità con le linee guida di dissociazione

## <a name="the-basics-of-spatial-analysis"></a>Nozioni di base sull'analisi spaziale

Oggi le competenze principali dell'analisi spaziale sono tutte basate su una pipeline che inserisce video, rileva le persone nel video, tiene traccia delle persone mentre si spostano nel tempo e genera eventi quando gli utenti interagiscono con le aree di interesse.

## <a name="spatial-analysis-terms"></a>Termini di analisi spaziale

| Termine | Definizione |
|------|------------|
| Rilevamento persone | Questo componente risponde alla domanda "dove sono le persone in questa immagine"? Trova gli utenti in un'immagine e passa un rettangolo di delimitazione che indica la posizione di ogni persona al componente di rilevamento persone. |
| Rilevamento persone | Questo componente connette i rilevamenti degli utenti nel tempo mentre gli utenti si spostano davanti a una fotocamera. Usa la logica temporale sul modo in cui le persone si spostano in genere e le informazioni di base sull'aspetto generale delle persone a tale scopo. Non è in grado di rilevare persone tra più fotocamere o di riidentificare un utente scomparso per più di circa un minuto. Il rilevamento di persone non usa marcatori biometrici come il riconoscimento facciale o il rilevamento dell'andamento. |
| Area di interesse | Si tratta di una zona o di una linea definita nel video di input come parte della configurazione. Quando una persona interagisce con l'area del video, il sistema genera un evento. Per l'abilità PersonCrossingLine, ad esempio, viene definita una riga nel video. Quando un utente incrocia la riga, viene generato un evento. |
| Evento | Un evento è l'output primario dell'analisi spaziale. Ogni Skill emette un evento specifico periodicamente (ad esempio una volta al minuto) o quando si verifica un trigger specifico. L'evento include informazioni su ciò che si è verificato nel video di input, ma non include immagini o video. Ad esempio, l'abilità PeopleCount può emettere un evento contenente il conteggio aggiornato ogni volta che il conteggio delle persone cambia (trigger) o ogni minuto (periodicamente). |

## <a name="example-use-cases-for-spatial-analysis"></a>Casi d'uso di esempio per l'analisi spaziale

Di seguito sono riportati alcuni casi d'uso di esempio che abbiamo preso in considerazione come abbiamo progettato e testato l'analisi spaziale.

**Conformità** a distanza sociale: uno spazio ufficio dispone di diverse fotocamere che usano l'analisi spaziale per monitorare la conformità delle distanze sociali misurando la distanza tra le persone. Il gestore di strutture può usare Heatmaps con statistiche aggregate sulla conformità delle distanze sociali nel tempo per regolare l'area di lavoro e semplificare le operazioni di allontanamento sociale.

**Analisi del cliente** : un negozio di alimentari usa le fotocamere a cui puntano i prodotti per misurare l'effetto delle modifiche di merchandising nel traffico di archiviazione. Il sistema consente al gestore del negozio di identificare i nuovi prodotti che comportano la maggior parte delle modifiche apportate a Engagement.

**Gestione delle code** : le fotocamere che puntano alle code di checkout forniscono avvisi ai responsabili quando il tempo di attesa diventa troppo lungo, consentendo loro di aprire più righe. I dati cronologici sull'abbandono della coda forniscono informazioni dettagliate sul comportamento degli utenti.

**Compilazione** di un & di lavoro: un edificio di Office USA le fotocamere incentrate sugli accessi agli spazi chiave per misurare calpestio e il modo in cui gli utenti usano l'area di lavoro. Le informazioni dettagliate consentono al responsabile della creazione di adattare il servizio e il layout per servire meglio gli occupanti.

**Rilevamento personale minimo** : In un Data Center, le fotocamere monitorano l'attività nei server. Quando i dipendenti stanno correggendo fisicamente apparecchiature sensibili, è sempre necessario che siano presenti due persone durante il ripristino per motivi di sicurezza. Le fotocamere vengono usate per verificare che le linee guida siano seguite.

**Ottimizzazione dell'area di lavoro** : in un ristorante informale veloce, le fotocamere della cucina vengono usate per generare informazioni aggregate sul flusso di lavoro dei dipendenti. Viene usato dai responsabili per migliorare i processi e il training del team.

## <a name="considerations-when-choosing-a-use-case"></a>Considerazioni sulla scelta di un caso d'uso

**Evitare avvisi critici** per la sicurezza: l'analisi spaziale non è stata progettata per gli avvisi in tempo reale di sicurezza critici. Non si deve fare affidamento su per gli scenari in cui gli avvisi in tempo reale sono necessari per attivare l'intervento per evitare infortuni, ad esempio la disattivazione di una parte di macchinari pesanti quando è presente una persona. Può essere usato per la riduzione dei rischi usando le statistiche e l'intervento per ridurre il comportamento rischioso, ad esempio per gli utenti che accedono a un'area limitata o proibita.

**Evitare l'uso di decisioni relative all'impiego** : l'analisi spaziale fornisce metriche probabilistiche sulla posizione e lo spostamento delle persone all'interno di uno spazio. Sebbene questi dati risultino utili per il miglioramento del processo di aggregazione, i dati non sono un indicatore valido delle singole prestazioni del ruolo di lavoro e non devono essere usati per prendere decisioni relative all'utilizzo.

**Evitare l'uso di decisioni relative all'assistenza sanitaria** : l'analisi spaziale fornisce dati probabilistici e parziali relativi ai movimenti degli utenti. I dati non sono adatti per prendere decisioni relative allo stato di integrità.

**Evitare di usare gli spazi protetti** per proteggere la privacy dei singoli utenti valutando posizioni e posizioni della fotocamera, regolando gli angoli e l'area di interesse, in modo che non trascurano aree protette, ad esempio Servizi igienici.

**Valutare con attenzione l'uso nelle scuole o nelle strutture di assistenza anziane** : l'analisi spaziale non è stata testata molto con dati contenenti minori di età inferiore ai 18 anni o adulti rispetto all'età 65. Si consiglia ai clienti di valutare accuratamente le frequenze degli errori per il proprio scenario negli ambienti in cui prevalgono questi tempi.

Valutare **con attenzione l'uso negli spazi pubblici** : valutare le posizioni e le posizioni della fotocamera, modificare gli angoli e l'area di interesse per ridurre al minimo la raccolta dagli spazi pubblici. L'illuminazione e il meteo negli spazi pubblici, ad esempio strade e parchi, influiscono in modo significativo sulle prestazioni del sistema di analisi spaziale ed è estremamente difficile fornire una divulgazione efficace negli spazi pubblici.

## <a name="spatial-analysis-gating-for-public-preview"></a>Controllo di analisi spaziale per l'anteprima pubblica

Per garantire che l'analisi spaziale venga utilizzata per gli scenari per cui è stata progettata, questa tecnologia viene messa a disposizione dei clienti tramite un processo dell'applicazione. Per ottenere l'accesso all'analisi spaziale, sarà necessario iniziare compilando il modulo di assunzione online. [Avviare l'applicazione qui](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u).

L'accesso alla versione di anteprima pubblica dell'analisi spaziale è soggetto alla esclusiva discrezione di Microsoft, in base ai criteri di idoneità, al processo di vagliatura e alla disponibilità per supportare un numero limitato di clienti durante questa anteprima gestita. Nella versione di anteprima pubblica, i clienti che hanno una relazione significativa con Microsoft sono interessati a collaborare con Microsoft nei casi d'uso consigliati e a scenari aggiuntivi che sono in grado di rispettare gli impegni di intelligenza artificiale responsabili.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Caratteristiche e limitazioni per l'analisi spaziale](https://docs.microsoft.com/legal/cognitive-services/computer-vision/accuracy-and-limitations?context=%2fazure%2fcognitive-services%2fComputer-vision%2fcontext%2fcontext)
