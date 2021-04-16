---
title: Risolvere i problemi di connettività di Synapse Studio
description: Risolvere i Azure Synapse di connettività di Azure Synapse Studio con PowerShell
author: saveenr
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: conceptual
ms.date: 10/30/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 2bbdaef9268239005cdf5ea7fbee6734dadc8113
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566251"
---
# <a name="troubleshoot-synapse-studio-connectivity-with-powershell"></a>Risolvere i Synapse Studio connettività con PowerShell

Azure Synapse Studio dipende da un set di endpoint API Web per funzionare correttamente. Questa guida consente di identificare le cause dei problemi di connettività quando si è:
- configurazione della rete locale (ad esempio, una rete dietro un firewall aziendale) per l'accesso Azure Synapse Studio.
- problemi di connettività con Azure Synapse Studio.

## <a name="prerequisite"></a>Prerequisito

* PowerShell 5.0 o versione successiva in Windows oppure
* PowerShell Core 6.0 o versione successiva in Windows o Linux.

## <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi

Fare clic con il pulsante destro del mouse sul collegamento seguente e scegliere "Salva destinazione con nome":

- [Test-AzureSynapse.ps1](https://go.microsoft.com/fwlink/?linkid=2119734)

In alternativa, è possibile aprire direttamente il collegamento e salvare il file di script aperto. Non salvare l'indirizzo del collegamento precedente, perché potrebbe cambiare in futuro.

In Esplora file fare clic con il pulsante destro del mouse sul file di script scaricato e scegliere "Esegui con PowerShell".

![Eseguire il file di script scaricato con PowerShell](media/troubleshooting-synapse-studio-powershell/run-with-powershell.png)

Quando richiesto, immettere il nome Azure Synapse'area di lavoro in cui si è verificato un problema o che si vuole testare per la connettività e premere INVIO.

![Immettere il nome dell'area di lavoro](media/troubleshooting-synapse-studio-powershell/enter-workspace-name.png)

Verrà avviata la sessione di diagnostica. Attendere il completamento.

![Attendere il completamento della diagnosi](media/troubleshooting-synapse-studio-powershell/wait-for-diagnosis.png)

Alla fine, verrà visualizzato un riepilogo della diagnosi. Se il PC non riesce a connettersi a uno o più endpoint, verranno visualizzati alcuni suggerimenti nella sezione "Riepilogo".

![Esaminare il riepilogo di diagnostica](media/troubleshooting-synapse-studio-powershell/diagnosis-summary.png)

Inoltre, verrà generato un file di log di diagnostica per questa sessione nella stessa cartella dello script di risoluzione dei problemi. La posizione è illustrata nella sezione "Suggerimenti generali" ( `D:\TestAzureSynapse_2020....log` ). Se necessario, è possibile inviare questo file al supporto tecnico.

Gli amministratori di rete e l'ottimizzazione della configurazione del firewall per Azure Synapse Studio possono essere utili nei dettagli tecnici illustrati sopra la sezione "Riepilogo".

* Tutti gli elementi di test (richieste) contrassegnati con "Superato" significano che sono stati superati i test di connettività, indipendentemente dal codice di stato HTTP.
 Per le richieste non riuscite, il motivo è indicato in giallo, ad esempio `NamedResolutionFailure` o `ConnectFailure` . Questi motivi possono aiutare a capire se sono presenti errori di configurazione con l'ambiente di rete.


## <a name="next-steps"></a>Passaggi successivi
Se i passaggi precedenti non consentono di risolvere il problema, [creare un ticket di supporto](../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md).