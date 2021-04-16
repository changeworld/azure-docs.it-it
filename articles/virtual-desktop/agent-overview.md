---
title: Introduzione all'agente di Desktop virtuale Windows
description: Panoramica dell'agente di Desktop virtuale Windows e dei processi di aggiornamento.
author: Sefriend
ms.topic: conceptual
ms.date: 12/16/2020
ms.author: sefriend
manager: clarkn
ms.openlocfilehash: 529a86712994aae91a554589d383cc748f79d07f
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520106"
---
# <a name="get-started-with-the-windows-virtual-desktop-agent"></a>Introduzione all'agente di Desktop virtuale Windows

Nel framework servizio Desktop virtuale Windows sono presenti tre componenti principali: il client Desktop remoto, il servizio e le macchine virtuali. Queste macchine virtuali si trova nella sottoscrizione del cliente in cui sono installati l'agente di Desktop virtuale Windows e il bootloader dell'agente. L'agente funge da intermediario intermedio tra il servizio e le macchine virtuali, abilitando la connettività. Pertanto, se si verificano problemi con l'installazione, l'aggiornamento o la configurazione dell'agente, le macchine virtuali non saranno in grado di connettersi al servizio. Il bootloader dell'agente è l'eseguibile che carica l'agente. 

Questo articolo fornisce una breve panoramica dei processi di installazione e aggiornamento dell'agente.

>[!NOTE]
>Questa documentazione non è relativa all'agente FSLogix o all'Desktop remoto client.


## <a name="initial-installation-process"></a>Processo di installazione iniziale

L'agente di Desktop virtuale Windows viene inizialmente installato in uno dei due modi seguenti. Se si effettua il provisioning di macchine virtuali (VM) nel portale di Azure e Azure Marketplace, l'agente e il bootloader dell'agente vengono installati automaticamente. Se si effettua il provisioning di macchine virtuali tramite PowerShell, è necessario scaricare manualmente i file msi dell'agente e del bootloader dell'agente durante la creazione di un pool di host di Desktop virtuale [Windows con PowerShell.](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool) Dopo l'installazione, l'agente installa lo stack side-by-side di Desktop virtuale Windows e l'agente di monitoraggio Disamandati. Il componente stack side-by-side è necessario per consentire agli utenti di stabilire in modo sicuro connessioni da server a client inversi. L'agente di monitoraggio di Svizzera monitora l'integrità dell'agente. Tutti e tre questi componenti sono essenziali per il corretto funzionamento della connettività degli utenti end-to-end.

>[!IMPORTANT]
>Per installare correttamente l'agente di Desktop virtuale Windows, lo stack side-by-side e l'agente di monitoraggio Disartiemand, è necessario sbloccare tutti gli URL elencati nell'elenco [URL necessari](safe-url-list.md#virtual-machines). Per usare il servizio Desktop virtuale Windows, è necessario sbloccare questi URL.

## <a name="agent-update-process"></a>Processo di aggiornamento dell'agente

Il servizio Desktop virtuale Windows aggiorna l'agente ogni volta che diventa disponibile un aggiornamento. Gli aggiornamenti dell'agente possono includere nuove funzionalità o correzioni per i problemi precedenti. È sempre necessario che sia installata la versione stabile più recente dell'agente in modo che le macchine virtuali non perdano connettività o sicurezza. Dopo aver installato la versione iniziale dell'agente di Desktop virtuale Windows, l'agente esegue regolarmente una query sul servizio Desktop virtuale Windows per determinare se è disponibile una versione più recente dell'agente, dello stack o del componente di monitoraggio. Se è già stata distribuita una versione più recente di uno dei componenti, il componente aggiornato viene installato automaticamente dal sistema di versioni di versioni di versioni.

Le nuove versioni dell'agente vengono distribuite a intervalli regolari nei periodi di una settimana a tutte le sottoscrizioni di Azure. Questi periodi di aggiornamento sono denominati "voli". Quando si verifica un volo, è possibile che le macchine virtuali nel pool di host ricevano l'aggiornamento dell'agente in momenti diversi. Tutti gli agenti di macchine virtuali in tutte le sottoscrizioni verranno aggiornati entro la fine del periodo di distribuzione. Il sistema di distribuzione dei servizi di distribuzione di Desktop virtuale Windows migliora l'affidabilità del servizio garantendo la stabilità e la qualità dell'aggiornamento dell'agente.


Altri aspetti importanti da tenere presenti:

- Poiché le macchine virtuali nel pool di host possono ricevere gli aggiornamenti dell'agente in momenti diversi, sarà necessario essere in grado di distinguere tra i problemi di gestione dei voli e gli aggiornamenti dell'agente non riusciti. Se si accede ai log eventi per la macchina virtuale **in Visualizzatore eventi** Windows Logs Application e viene visualizzato un evento con  >    >   etichetta "ID 3277", significa che l'aggiornamento dell'agente non funziona. Se l'evento non viene visualizzato, la macchina virtuale è in un volo diverso e verrà aggiornata in un secondo momento.
- Quando l'agente di monitoraggio di Svizzera viene aggiornato alla versione più recente, l'attività Diamanetask precedente viene individuata e disabilitata prima di creare una nuova attività per il nuovo agente di monitoraggio. La versione precedente dell'agente di monitoraggio non viene eliminata nel caso in cui la versione più recente dell'agente di monitoraggio presenti un problema che richiede il ripristino della versione precedente per la correzione. Se la versione più recente presenta un problema, l'agente di monitoraggio precedente verrà ri-abilitato per continuare a recapitare i dati di monitoraggio. Tutte le versioni del monitoraggio precedenti all'ultima installata prima dell'aggiornamento verranno eliminate dalla macchina virtuale.
- La macchina virtuale mantiene tre versioni dello stack side-by-side alla volta. Ciò consente un ripristino rapido in caso di problemi con l'aggiornamento. La versione meno recente dello stack viene rimossa dalla macchina virtuale ogni volta che lo stack viene aggiornato.

L'aggiornamento dell'agente dura in genere 2-3 minuti in una nuova macchina virtuale e non deve causare la perdita della connessione o l'arresto della macchina virtuale. Questo processo di aggiornamento si applica sia a Desktop virtuale Windows (versione classica) che alla versione più recente di Desktop virtuale Windows con Azure Resource Manager.

## <a name="next-steps"></a>Passaggi successivi

Ora che si ha una migliore conoscenza dell'agente di Desktop virtuale Windows, ecco alcune risorse che possono essere utili:

- Se si verificano problemi relativi all'agente o alla connettività, vedere la guida alla risoluzione dei problemi dell'agente Desktop virtuale [Windows](troubleshoot-agent.md).
