---
title: Introduzione all'agente desktop virtuale di Windows
description: Panoramica dell'agente desktop virtuale di Windows e dei processi di aggiornamento.
author: Sefriend
ms.topic: conceptual
ms.date: 12/16/2020
ms.author: sefriend
manager: clarkn
ms.openlocfilehash: 325502255e84e38a39ca5b90ee4126354c0d425b
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/20/2021
ms.locfileid: "98601241"
---
# <a name="get-started-with-the-windows-virtual-desktop-agent"></a>Introduzione all'agente desktop virtuale di Windows

Nel Framework del servizio desktop virtuale di Windows sono disponibili tre componenti principali: il client di Desktop remoto, il servizio e le macchine virtuali. Queste macchine virtuali risiedono nella sottoscrizione del cliente in cui sono installati l'agente desktop virtuale di Windows e il bootloader agente. L'agente funge da Communicator intermedio tra il servizio e le macchine virtuali, abilitando la connettività. Se pertanto si verificano problemi con l'installazione, l'aggiornamento o la configurazione dell'agente, le macchine virtuali non saranno in grado di connettersi al servizio. Il bootloader dell'agente è il file eseguibile che carica l'agente. 

In questo articolo viene illustrata una breve panoramica dei processi di installazione e aggiornamento dell'agente.

>[!NOTE]
>Questa documentazione non è relativa all'agente FSLogix o Desktop remoto agente client.


## <a name="initial-installation-process"></a>Processo di installazione iniziale

L'agente desktop virtuale di Windows viene inizialmente installato in uno dei due modi seguenti. Se si effettua il provisioning di macchine virtuali (VM) nell'portale di Azure e in Azure Marketplace, verranno installati automaticamente il bootloader dell'agente e dell'agente. Se si effettua il provisioning di macchine virtuali tramite PowerShell, è necessario scaricare manualmente i file dell'agente e dell'agente bootloader. msi quando si [Crea un pool host di desktop virtuali Windows con PowerShell](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool). Quando l'agente viene installato, lo stack side-by-side di desktop virtuale di Windows e l'agente di monitoraggio di Ginevra vengono installati contemporaneamente. Il componente stack affiancato è necessario per consentire agli utenti di stabilire in modo sicuro connessioni inverse da server a client. L'agente di monitoraggio di Ginevra monitora lo stato dell'agente. Tutti e tre questi componenti sono essenziali per un corretto funzionamento della connettività utente end-to-end.

>[!IMPORTANT]
>Per installare correttamente l'agente desktop virtuale di Windows, lo stack affiancato e l'agente di monitoraggio di Ginevra, è necessario sbloccare tutti gli URL elencati nell' [elenco degli URL richiesti](safe-url-list.md#virtual-machines). Lo sblocco di questi URL è necessario per utilizzare il servizio desktop virtuale di Windows.

## <a name="agent-update-process"></a>Processo di aggiornamento dell'agente

Il servizio desktop virtuale di Windows aggiorna automaticamente l'agente ogni volta che un aggiornamento diventa disponibile. Gli aggiornamenti dell'agente possono includere nuove funzionalità o risolvere i problemi precedenti. Una volta installata la versione iniziale di Windows Virtual Desktop Agent, l'agente esegue periodicamente query sul servizio desktop virtuale Windows per determinare se è disponibile una versione più recente dell'agente e dei relativi componenti. Se è presente una nuova versione, il bootloader dell'agente Scarica automaticamente la versione più recente dell'agente, lo stack affiancato e l'agente di monitoraggio di Ginevra.

>[!NOTE]
>- Quando l'agente di monitoraggio di Ginevra esegue l'aggiornamento alla versione più recente, l'attività GenevaTask precedente viene individuata e disabilitata prima di creare una nuova attività per il nuovo agente di monitoraggio. La versione precedente dell'agente di monitoraggio non viene eliminata se la versione più recente dell'agente di monitoraggio presenta un problema che richiede il ripristino della versione precedente per la correzione. Se la versione più recente presenta un problema, l'agente di monitoraggio precedente verrà riabilitato per continuare a consegnare i dati di monitoraggio. Tutte le versioni del monitoraggio precedenti all'ultima installata prima dell'aggiornamento verranno eliminate dalla macchina virtuale.
>- La VM conserva tre versioni dello stack affiancato alla volta. Questo consente un ripristino rapido se si verifica un errore durante l'aggiornamento. La versione meno recente dello stack viene rimossa dalla VM ogni volta che lo stack viene aggiornato.

Questa installazione degli aggiornamenti dura normalmente 2-3 minuti su una nuova macchina virtuale e non dovrebbe causare la perdita della connessione o l'arresto della macchina virtuale. Questo processo di aggiornamento si applica sia al desktop virtuale Windows (classico) che alla versione più recente del desktop virtuale di Windows con Azure Resource Manager.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è in grado di comprendere meglio l'agente desktop virtuale di Windows, di seguito sono riportate alcune risorse che possono essere utili:

- Se si verificano problemi relativi all'agente o alla connettività, consultare la [Guida alla risoluzione dei problemi dell'agente desktop virtuale di Windows](troubleshoot-agent.md).