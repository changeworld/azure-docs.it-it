---
title: Introduzione all'aggiornamento del dispositivo per l'hub di Azure Microsoft Docs
description: L'aggiornamento del dispositivo per l'hub Internet è un servizio che consente di distribuire gli aggiornamenti in modalità wireless (OTA) per i dispositivi Internet delle cose.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: overview
ms.service: iot-hub-device-update
ms.openlocfilehash: 60dfd448a66ca67a241f97570c91f683323a7d6d
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2021
ms.locfileid: "103232376"
---
# <a name="device-update-for-iot-hub-preview-overview"></a>Panoramica dell'aggiornamento del dispositivo per l'hub Internet (anteprima)

L'aggiornamento del dispositivo per l'hub Internet è un servizio che consente di distribuire gli aggiornamenti in modalità wireless (OTA) per i dispositivi Internet delle cose.

Poiché le organizzazioni hanno la possibilità di migliorare ulteriormente la produttività e l'efficienza operativa, le soluzioni Internet delle cose (Internet delle cose) continuano a essere adottate a tariffe crescenti. Questo rende essenziale che i dispositivi che formano queste soluzioni siano basati su una base di affidabilità e sicurezza e siano facili da connettere e gestire su larga scala. L'aggiornamento dei dispositivi per l'hub Internet è una piattaforma end-to-end che i clienti possono usare per pubblicare, distribuire e gestire gli aggiornamenti in modalità tutto il tempo, da piccoli sensori a dispositivi a livello di gateway. 

Per realizzare tutti i vantaggi della trasformazione digitale con tutto il tempo, i clienti hanno la possibilità di gestire, gestire e aggiornare i dispositivi su larga scala. Scopri i vantaggi derivanti dall'implementazione dell'aggiornamento dei dispositivi per l'hub Internet, che include la possibilità di rispondere rapidamente alle minacce alla sicurezza e di distribuire nuove funzionalità per ottenere gli obiettivi aziendali senza sostenere i costi di sviluppo e manutenzione aggiuntivi per la creazione di piattaforme di aggiornamento personalizzate.

## <a name="support-for-a-wide-range-of-iot-devices"></a>Supporto per un'ampia gamma di dispositivi Internet delle cose


L'aggiornamento dei dispositivi per l'hub Internet è progettato per offrire una distribuzione di aggiornamenti ottimizzata e operazioni semplificate tramite l'integrazione con l' [Hub Azure](https://azure.microsoft.com/en-us/services/iot-hub/). Questa integrazione consente di adottare facilmente l'aggiornamento del dispositivo in qualsiasi soluzione esistente. Fornisce una soluzione ospitata nel cloud per connettere praticamente qualsiasi dispositivo. L'aggiornamento del dispositivo supporta un'ampia gamma di sistemi operativi, tra cui Linux e [Azure RTO](https://azure.microsoft.com/en-us/services/rtos/) (sistema operativo in tempo reale), ed è estensibile tramite Open Source. Microsoft sta sviluppando un aggiornamento del dispositivo per le offerte di hub Internet con i nostri partner di semiconduttori, tra cui STMicroelectronics, NXP, Renesas e microchip. Vedere gli [esempi](https://github.com/azure-rtos/samples/tree/PublicPreview/ADU) di lavagne di valutazione dei semiconduttori chiave che includono le guide introduttive per informazioni su come configurare, compilare e distribuire gli aggiornamenti in modalità wireless (OTA) ai dispositivi di classe MCU. 

Sono disponibili le immagini Yocto del simulatore dell'agente di aggiornamento del dispositivo e il riferimento a Raspberry Pi.
L'aggiornamento del dispositivo per l'hub Internet è supportato anche per l'aggiornamento dei dispositivi Azure IoT Edge. È disponibile un agente di aggiornamento dispositivi per la piattaforma AMD64 Ubuntu server 18,04. L'aggiornamento del dispositivo per l'hub Internet fornisce anche codice open source se non si esegue una delle piattaforme precedenti. È possibile trasferire l'agente alla distribuzione in esecuzione.

L'aggiornamento del dispositivo funziona con la Plug and Play degli oggetti Internet (PnP) ed è in grado di gestire qualsiasi dispositivo che supporta le interfacce PnP richieste. Per altre informazioni, vedere l' [aggiornamento dei dispositivi per l'hub e l'plug and Play](device-update-plug-and-play.md)Internet.

## <a name="support-for-a-wide-range-of-update-artifacts"></a>Supporto per un'ampia gamma di elementi di aggiornamento

L'aggiornamento del dispositivo per l'hub Internet supporta due forme di aggiornamenti, basati su immagini e basati su pacchetti.

Gli aggiornamenti basati su pacchetti sono aggiornamenti mirati che modificano solo un componente o un'applicazione specifica nel dispositivo. In questo modo si riduce il consumo di larghezza di banda e si riduce il tempo necessario per scaricare e installare l'aggiornamento. Gli aggiornamenti dei pacchetti consentono in genere un minor tempo di inattività per i dispositivi quando si applica un aggiornamento ed evitano il sovraccarico della creazione di immagini.

Gli aggiornamenti delle immagini forniscono un livello di confidenza maggiore nello stato finale del dispositivo. È in genere più semplice replicare i risultati di un aggiornamento di immagine tra un ambiente di pre-produzione e un ambiente di produzione, poiché non comporta le stesse esigenze dei pacchetti e delle relative dipendenze.
A causa della natura atomica, è anche possibile adottare facilmente un modello di failover A/B.

Non esiste una risposta corretta ed è possibile scegliere in modo diverso in base ai casi d'uso specifici. L'aggiornamento del dispositivo per l'hub Internet delle cose supporta sia l'immagine che la forma del pacchetto di aggiornamento, consentendo di scegliere il modello di aggiornamento appropriato per l'ambiente del dispositivo.

## <a name="flexible-features-for-updating-devices"></a>Funzionalità flessibili per l'aggiornamento dei dispositivi

L'aggiornamento del dispositivo per le funzionalità dell'hub di Internet delle cose offre un'esperienza potente e flessibile, tra cui:

* Gestione aggiornamenti UX integrata con l'hub Azure
* Implementazione graduale degli aggiornamenti tramite il raggruppamento dei dispositivi e i controlli di pianificazione degli aggiornamenti
* API programmatiche per abilitare l'automazione e le esperienze personalizzate del portale
* Visualizzazione immediata degli aggiornamenti per la conformità e lo stato di tutte le flotte di dispositivi eterogeneo
* Supporto per aggiornamenti resilienti dei dispositivi (A/B) per garantire un rollback semplice
* Sottoscrizione e controlli degli accessi in base al ruolo disponibili tramite il portale di Azure.com
* Cache del contenuto locale e supporto Edge annidato per abilitare l'aggiornamento dei dispositivi disconnessi nel cloud
* Strumenti di gestione e reporting dettagliati degli aggiornamenti 

Con l'aggiornamento dei dispositivi per la gestione e la distribuzione degli hub Internet, gli utenti possono massimizzare la produttività e risparmiare tempo prezioso. L'aggiornamento del dispositivo per l'hub Internet include la possibilità di raggruppare i dispositivi e specificare in quali dispositivi deve essere distribuito un aggiornamento. Gli utenti possono anche visualizzare lo stato delle distribuzioni degli aggiornamenti e assicurarsi che ogni dispositivo applichi correttamente gli aggiornamenti.

Quando si verifica un errore di aggiornamento, l'aggiornamento del dispositivo per l'hub Internet consente anche agli utenti di identificare i dispositivi che non sono riusciti a applicare l'aggiornamento e vedere i dettagli dell'errore correlati. La possibilità di identificare i dispositivi che non sono stati aggiornati indica innumerevoli ore manuali salvate che tentano di individuare l'origine.

### <a name="best-in-class-security-at-global-scale"></a>Sicurezza ottimale su scala globale

Microsoft Azure supporta più di un miliardo di dispositivi in tutto il mondo, un numero che cresce rapidamente al giorno. L'aggiornamento dei dispositivi per l'hub Internet si basa su questa esperienza e sull'affidabilità collaudata dimostrata dalla piattaforma Windows Update, in modo che i dispositivi possano essere facilmente aggiornati su scala globale.

L'aggiornamento dei dispositivi per l'hub Internet usa una protezione completa da cloud a bordo sviluppata per la Microsoft Azure, quindi i clienti non devono dedicare tempo a capire come compilarlo da zero.


## <a name="device-update-workflows"></a>Flussi di lavoro di aggiornamento del dispositivo

La funzionalità di aggiornamento del dispositivo può essere suddivisa in tre aree: integrazione, importazione e gestione degli agenti.

### <a name="device-update-agent"></a>Agente di aggiornamento dispositivo

Quando viene ricevuto un comando Update in un dispositivo, viene eseguita la fase di aggiornamento richiesta (download, installazione e applicazione). Durante ogni fase, lo stato viene restituito all'aggiornamento del dispositivo tramite l'hub Internet, in modo da poter visualizzare lo stato corrente di una distribuzione. Se non sono in corso aggiornamenti, lo stato viene restituito come "inattivo". Una distribuzione può essere annullata in qualsiasi momento.

:::image type="content" source="media/understand-device-update/client-agent-workflow.png" alt-text="Diagramma del flusso di lavoro dell'agente di aggiornamento del dispositivo." lightbox="media/understand-device-update/client-agent-workflow.png":::

[Altre](device-update-agent-overview.md) informazioni su Device Update Agent. 

### <a name="importing"></a>Importazione

L'importazione è il modo in cui vengono inseriti gli aggiornamenti nell'aggiornamento del dispositivo, in modo che possano essere distribuiti nei dispositivi. L'aggiornamento del dispositivo supporta l'implementazione di un singolo aggiornamento per ogni dispositivo. Questa soluzione è ideale per gli aggiornamenti full-image che aggiornano una sola partizione del sistema operativo in una sola volta o un manifesto apt che descrive tutti i pacchetti che si vuole aggiornare nel dispositivo. Per importare gli aggiornamenti nell'aggiornamento del dispositivo, creare prima di tutto un manifesto di importazione che descrive l'aggiornamento, quindi caricare i file di aggiornamento e il manifesto di importazione in un percorso accessibile da Internet. Successivamente, è possibile usare l' [API REST](https://github.com/Azure/iot-hub-device-update/tree/main/docs/publish-api-reference) di portale di Azure o l'importazione dell'aggiornamento del dispositivo per avviare il processo asincrono di importazione degli aggiornamenti. L'aggiornamento del dispositivo carica i file, li elabora e li rende disponibili per la distribuzione nei dispositivi Internet.

Per contenuti sensibili, Proteggi il download usando una firma di accesso condiviso (SAS), ad esempio una firma di accesso condiviso ad hoc per l'archiviazione BLOB di Azure. [Altre informazioni su SAS](https://docs.microsoft.com/azure/storage/common/storage-sas-overview)

:::image type="content" source="media/understand-device-update/import-update.png" alt-text="Diagramma dell'aggiornamento del dispositivo per il flusso di lavoro di importazione dell'hub Internet." lightbox="media/understand-device-update/import-update.png":::

[Altre](import-concepts.md) informazioni sull'importazione di aggiornamenti. 

### <a name="grouping-and-deployment"></a>Raggruppamento e distribuzione

Dopo aver importato un aggiornamento, è possibile visualizzare gli aggiornamenti compatibili per i dispositivi e le classi del dispositivo.

L'aggiornamento del dispositivo supporta il concetto di **gruppi** tramite tag nell'hub Internet. La distribuzione di un aggiornamento in un gruppo di test è un modo efficace per ridurre il rischio di problemi durante un'implementazione di produzione.

Nell'aggiornamento del dispositivo le distribuzioni sono un modo per connettere il contenuto corretto a un set specifico di dispositivi compatibili. L'aggiornamento del dispositivo consente di orchestrare il processo di invio di comandi a ogni dispositivo, indicando loro di scaricare e installare gli aggiornamenti e ottenere lo stato.

:::image type="content" source="media/understand-device-update/manage-deploy-updates.png" alt-text="Diagramma dell'aggiornamento del dispositivo per il raggruppamento e il flusso di lavoro di distribuzione dell'hub Internet." lightbox="media/understand-device-update/manage-deploy-updates.png":::

[Altre](device-update-compliance.md) informazioni sui concetti di distribuzione

[Altre](device-update-groups.md) informazioni sui gruppi di aggiornamento del dispositivo


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Crea l'account e l'istanza di aggiornamento del dispositivo](create-device-update-account.md)
