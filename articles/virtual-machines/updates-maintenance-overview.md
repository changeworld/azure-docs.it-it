---
title: Panoramica degli aggiornamenti e della manutenzione
description: Informazioni sugli aggiornamenti e sulle opzioni di manutenzione disponibili con le macchine virtuali in Azure
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: overview
ms.date: 03/08/2021
ms.reviewer: cynthn
ms.openlocfilehash: 81c6fb2e7f25abc9a236c80d1412b84fd6761872
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103022296"
---
# <a name="updates-and-maintenance-overview"></a>Panoramica degli aggiornamenti e della manutenzione
Questo articolo fornisce una panoramica delle varie opzioni di aggiornamento e manutenzione per le macchine virtuali di Azure.

## <a name="automatic-os-image-upgrade"></a>Aggiornamento automatico dell'immagine del sistema operativo

L'abilitazione degli [aggiornamenti automatici delle immagini del sistema operativo](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md?context=/azure/virtual-machines/context/context) nel set di scalabilità consente di semplificare la gestione degli aggiornamenti eseguendo l'aggiornamento automatico del disco del sistema operativo per tutte le istanze nel set di scalabilità

L' [aggiornamento automatico del sistema operativo](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md?context=/azure/virtual-machines/context/context) presenta le caratteristiche seguenti:

- Una volta configurato, l'immagine del sistema operativo più recente pubblicata dagli editori di immagini viene applicata automaticamente al set di scalabilità senza l'intervento dell'utente.
- Aggiorna batch di istanze in modo sequenziale ogni volta che una nuova immagine viene pubblicata dal server di pubblicazione.
- Si integra con i probe di integrità dell'applicazione e con l'[estensione Integrità applicazione](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md?context=/azure/virtual-machines/context/context).
- Funziona per tutte le dimensioni delle macchine virtuali e per le immagini Windows e Linux.
- È possibile rifiutare esplicitamente gli aggiornamenti automatici in qualsiasi momento (gli aggiornamenti del sistema operativo possono anche essere avviati manualmente).
- Il disco del sistema operativo di una macchina virtuale viene sostituito con il nuovo disco del sistema operativo creato con una versione più recente dell'immagine. Le estensioni configurate e gli script di dati personalizzati vengono eseguiti, mentre i dischi dati persistenti vengono mantenuti.
- È supportata la [sequenziazione delle estensioni](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md?context=/azure/virtual-machines/context/context).
- L'aggiornamento automatico delle immagini del sistema operativo può essere abilitato in un set di scalabilità di qualsiasi dimensione.


## <a name="automatic-vm-guest-patching-preview"></a>Applicazione automatica delle patch Guest per macchine virtuali (anteprima)

L'abilitazione dell'applicazione [automatica delle patch Guest](automatic-vm-guest-patching.md) per le VM di Azure consente di semplificare la gestione degli aggiornamenti con l'applicazione di patch alle macchine virtuali in modo sicuro e automatico per garantire la conformità

L'applicazione [automatica di patch per guest VM](automatic-vm-guest-patching.md) presenta le caratteristiche seguenti:
- Le patch classificate come *critiche* o la *sicurezza* vengono scaricate e applicate automaticamente nella macchina virtuale.
- Le patch vengono applicate durante gli orari di minore traffico nel fuso orario della macchina virtuale.
- Patch Orchestration è gestito da Azure e le patch vengono applicate in base ai [principi di disponibilità](automatic-vm-guest-patching.md#availability-first-patching).
- L'integrità della macchina virtuale, come determinato dai segnali di integrità della piattaforma, viene monitorata per rilevare errori di applicazione delle patch.
- È adatto per le macchine virtuali di qualsiasi dimensione.


## <a name="automatic-extension-upgrade-preview"></a>Aggiornamento dell'estensione automatica (anteprima)

L' [aggiornamento automatico dell'estensione](automatic-extension-upgrade.md) è disponibile in anteprima per le VM di Azure e i set di scalabilità di macchine virtuali di Azure. Quando l'aggiornamento automatico dell'estensione è abilitato in una macchina virtuale o in un set di scalabilità, l'estensione viene aggiornata automaticamente ogni volta che il server di pubblicazione dell'estensione rilascia una nuova versione per l'estensione.

 L'aggiornamento automatico dell'estensione presenta le funzionalità seguenti:
- Supportato per le VM di Azure e i set di scalabilità di macchine virtuali di Azure. Service Fabric set di scalabilità di macchine virtuali non sono attualmente supportati.
- Gli aggiornamenti vengono applicati in un modello di distribuzione First di disponibilità.
- Per un set di scalabilità di macchine virtuali, non più del 20% delle macchine virtuali del set di scalabilità verrà aggiornato in un singolo batch. La dimensione minima del batch è una macchina virtuale.
- Funziona per tutte le dimensioni delle macchine virtuali e per le estensioni Windows e Linux.
- È possibile rifiutare esplicitamente l'aggiornamento automatico in qualsiasi momento.
- L'aggiornamento automatico dell'estensione può essere abilitato in un set di scalabilità di macchine virtuali di qualsiasi dimensione.
- Ogni estensione supportata è registrata singolarmente ed è possibile scegliere le estensioni da aggiornare automaticamente.
- Supportato in tutte le aree del cloud pubblico.

## <a name="hotpatch"></a>Danneggiata 

[Patch a caldo](../automanage/automanage-hotpatch.md?context=/azure/virtual-machines/context/context) è un nuovo modo per installare gli aggiornamenti nelle nuove macchine virtuali (VM) di Windows Server Azure Edition che non richiedono un riavvio dopo l'installazione. Danneggiata per le VM di Windows Server Azure Edition offre i vantaggi seguenti:

- Minore effetto sul carico di lavoro con meno riavvii
- Distribuzione più rapida degli aggiornamenti poiché i pacchetti sono più piccoli, installare più velocemente e avere più semplice orchestrazione patch con Azure Update Manager
- Migliore protezione, poiché i pacchetti di aggiornamento danneggiata hanno come ambito gli aggiornamenti della sicurezza di Windows che vengono installati più velocemente senza riavviare


## <a name="azure-update-management"></a>Gestione degli aggiornamenti di Azure

È possibile usare [Gestione aggiornamenti in automazione di Azure](../automation/update-management/overview.md?context=/azure/virtual-machines/context/context) per gestire gli aggiornamenti del sistema operativo per le macchine virtuali Windows e Linux in Azure, negli ambienti locali e in altri ambienti cloud. È possibile valutare rapidamente lo stato degli aggiornamenti disponibili in tutti i computer agente e gestire il processo di installazione degli aggiornamenti necessari per i server.

## <a name="maintenance-control"></a>Controllo della manutenzione

Gestire gli aggiornamenti della piattaforma, che non richiedono un riavvio, usando il [controllo di manutenzione](maintenance-control.md). Azure aggiorna spesso l'infrastruttura per migliorare l'affidabilità, le prestazioni, la sicurezza o avviare nuove funzionalità. La maggior parte degli aggiornamenti è trasparente per gli utenti. Alcuni carichi di lavoro sensibili, ad esempio giochi, flussi multimediali e transazioni finanziarie, non possono tollerare persino pochi secondi di blocco o disconnessione di una macchina virtuale per la manutenzione. Il controllo di manutenzione offre la possibilità di attendere gli aggiornamenti della piattaforma e di applicarli in una finestra in sequenza di 35 giorni. 

Il controllo della manutenzione consente di decidere quando applicare gli aggiornamenti alle VM isolate e agli host dedicati di Azure.

Con il [controllo di manutenzione](maintenance-control.md), è possibile:
- Aggiornamenti batch in un unico pacchetto di aggiornamento.
- Attendere fino a 35 giorni per l'applicazione degli aggiornamenti. 
- Automatizzare gli aggiornamenti della piattaforma configurando una pianificazione di manutenzione o usando [funzioni di Azure](https://github.com/Azure/azure-docs-powershell-samples/tree/master/maintenance-auto-scheduler).
- Le configurazioni di manutenzione funzionano tra le sottoscrizioni e i gruppi di risorse. 


## <a name="scheduled-events"></a>Eventi pianificati

Gli eventi pianificati sono un servizio metadati di Azure che concede all'applicazione il tempo di prepararsi alla manutenzione delle macchine virtuali. Questo servizio secondario offre informazioni sugli eventi di manutenzione imminenti (ad esempio il riavvio), in modo che l'applicazione possa prepararsi di conseguenza e limitare le interruzioni. È disponibile per tutti i tipi di macchine virtuali di Azure, incluse quelle di tipo PaaS e IaaS sia Windows che Linux. 

Per informazioni su Eventi pianificati, vedere [eventi pianificati per le macchine virtuali Windows](./windows/scheduled-events.md) e [eventi pianificati per Linux](./linux/scheduled-events.md)

## <a name="next-steps"></a>Passaggi successivi

Esaminare la documentazione relativa a [disponibilità e scalabilità](availability.md) per altri modi per aumentare il tempo di esecuzione delle applicazioni e dei servizi. 