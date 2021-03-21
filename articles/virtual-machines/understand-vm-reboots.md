---
title: Informazioni sui riavvii delle VM
description: Informazioni sui riavvii delle VM-manutenzione e tempi di inattività
author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: mimckitt
ms.reviewer: cynthn
ms.openlocfilehash: af371a8f7da5ef32e95d4096b69c5d52ce3e3700
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102510530"
---
# <a name="understand-vm-reboots---maintenance-vs-downtime"></a>Informazioni sui riavvii delle VM-manutenzione e tempi di inattività
Esistono tre scenari che possono comportare l'insorgere di macchine virtuali in Azure: manutenzione dell'hardware non pianificata, tempo di inattività imprevisto e manutenzione pianificata.

## <a name="unplanned-hardware-maintenance-event"></a>Evento di manutenzione hardware non pianificato
La manutenzione non pianificata dell'hardware si verifica quando la piattaforma Azure prevede che l'hardware o qualsiasi componente della piattaforma associato a un computer fisico stia per verificarsi un errore. Quando la piattaforma prevede un errore, genera un evento di manutenzione hardware non pianificata per ridurre l'impatto sulle macchine virtuali ospitate in tale hardware. Azure usa la tecnologia [Live Migration](./maintenance-and-updates.md) per eseguire la migrazione delle macchine virtuali dall'hardware non funzionante a un computer fisico integro. Live Migration è un'operazione di mantenimento della VM che sospende la macchina virtuale solo per un breve periodo. Memoria, file aperti e connessioni di rete vengono conservati, ma le prestazioni potrebbero subire un rallentamento prima e/o dopo l'evento. Nei casi in cui non è possibile usare Live Migration, si verificheranno problemi di tempi di inattività imprevisti nella VM, come descritto sotto.


## <a name="unexpected-downtime"></a>Tempo di inattività imprevisto
Un tempo di inattività imprevisto si verifica quando l'hardware o l'infrastruttura fisica della macchina virtuale non riesce in modo imprevisto. Può trattarsi, ad esempio, di errori della rete locale, guasti di un disco locale o altri errori a livello di rack. Quando viene rilevata, la piattaforma Azure esegue automaticamente la migrazione della macchina virtuale a un computer fisico integro nella stessa data center. Durante la procedura di riparazione, nelle macchine virtuali si verificano tempi di inattività (riavvio) e in alcuni casi la perdita dell'unità temporanea. Il sistema operativo e i dischi dati collegati vengono sempre conservati.

Le macchine virtuali possono anche riscontrare tempi di inattività nell'eventualità di un'interruzione o di un'emergenza che interessa un'intera data center o anche un'intera area. Per questi scenari, Azure offre opzioni di protezione che includono [zone di disponibilità](../availability-zones/az-overview.md) e [aree abbinate](regions.md#region-pairs).

## <a name="planned-maintenance-events"></a>Eventi di manutenzione pianificata
Gli eventi di manutenzione pianificata sono aggiornamenti periodici effettuati da Microsoft sulla piattaforma Azure sottostante per migliorare l'affidabilità, la sicurezza e le prestazioni complessive dell'infrastruttura della piattaforma su cui sono in esecuzione le macchine virtuali. La maggior parte di questi aggiornamenti viene eseguita senza alcun effetto sulle macchine virtuali o sui servizi cloud (vedere [manutenzione che non richiede un riavvio](maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)). Anche se la piattaforma Azure cerca di usare la manutenzione con mantenimento delle VM in tutte le occasioni possibili, in alcuni rari casi questi aggiornamenti richiedono un riavvio della macchina virtuale per applicare gli aggiornamenti necessari all'infrastruttura sottostante. In tal caso, è possibile eseguire la manutenzione pianificata di Azure con l'operazione di ridistribuzione della manutenzione avviando la manutenzione per le VM nell'intervallo di tempo adatto. Per altre informazioni, vedere [Manutenzione pianificata per le macchine virtuali](maintenance-and-updates.md).

## <a name="reduce-downtime"></a>Ridurre i tempi di inattività
Per ridurre le conseguenze dei tempi di inattività causati da uno o più di questi eventi, è possibile attuare le seguenti procedure consigliate per aumentare la disponibilità delle macchine virtuali:

* Usare [zone di disponibilità](../availability-zones/az-overview.md) per la protezione da errori Data Center
* Configurare più macchine virtuali in un [set di disponibilità](availability-set-overview.md) per la ridondanza
* Usare [gli eventi pianificati per Linux](/linux/scheduled-events.md) o [gli eventi pianificati per Windows](/windows/scheduled-events.md) per rispondere in modo proattivo agli eventi che INFLUISCAno sulle macchine virtuali
* Configurare ogni livello dell'applicazione in set di disponibilità separati
* Combinare un servizio di [bilanciamento del carico](../load-balancer/load-balancer-overview.md) con le zone o i set di disponibilità

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle opzioni di disponibilità in Azure, vedere [Panoramica della disponibilità](availability.md).
