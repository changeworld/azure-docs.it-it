---
title: Azure automanage per Linux
description: Informazioni sulle procedure consigliate per la gestione automatica di Azure per le macchine virtuali per i servizi caricati automaticamente e configurati per i computer Linux.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: deanwe
ms.openlocfilehash: e36d170e1a7f918ff40c32f3225ce3a0ddbe260f
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662734"
---
# <a name="azure-automanage-for-virtual-machines-best-practices---linux"></a>Procedure consigliate per la gestione delle macchine virtuali di Azure-Linux

Questi servizi di Azure vengono automaticamente caricati quando si usa la gestione automatica per le macchine virtuali (VM) in una VM Linux. Sono essenziali per le procedure consigliate white paper, che è possibile trovare nel [Framework di adozione del cloud](/azure/cloud-adoption-framework/manage/azure-server-management).

Per tutti questi servizi, verrà eseguito automaticamente il caricamento, la configurazione automatica, il monitoraggio della deviazione e la correzione se viene rilevata la deriva. Per ulteriori informazioni su questo processo, vedere [gestione delle macchine virtuali di Azure](automanage-virtual-machines.md).

## <a name="supported-linux-distributions-and-versions"></a>Distribuzioni e versioni di Linux supportate

Automanage supporta le seguenti distribuzioni e versioni di Linux:

- CentOS 7.3 +
- RHEL 7.4-7,8
- Ubuntu 16.04 e 18.04
- SLES 12 (tutti i Service Pack)

## <a name="participating-services"></a>Servizi partecipanti

>[!NOTE]
> Microsoft antimalware non è attualmente supportato nelle macchine virtuali Linux.

|Servizio    |Descrizione    |Ambienti supportati<sup>1</sup>    |Preferenze supportate<sup>1</sup>    |
|-----------|---------------|----------------------|-------------------------|
|Monitoraggio di VM Insights    |Monitoraggio di Azure per le macchine virtuali monitora le prestazioni e l'integrità delle macchine virtuali, inclusi i processi in esecuzione e le dipendenze da altre risorse. [Altre](../azure-monitor/vm/vminsights-overview.md)informazioni.    |Produzione    |No    |
|Backup    |Backup di Azure offre backup indipendenti e isolati per prevenire la distruzione accidentale dei dati nelle macchine virtuali. [Altre](../backup/backup-azure-vms-introduction.md)informazioni. Gli addebiti si basano sul numero e sulle dimensioni delle macchine virtuali protette. [Altre](https://azure.microsoft.com/pricing/details/backup/)informazioni.    |Produzione    |Sì    |
|Centro sicurezza di Azure    |Il Centro sicurezza di Azure è un sistema di gestione della sicurezza dell'infrastruttura unificata che rafforza il comportamento di sicurezza dei data center e offre protezione avanzata dalle minacce nei carichi di lavoro ibridi nel cloud. [Altre](../security-center/security-center-introduction.md)informazioni.  La gestione autonoma configurerà la sottoscrizione in cui si trova la macchina virtuale nell'offerta del livello gratuito del Centro sicurezza di Azure. Se la sottoscrizione è già stata caricata nel centro sicurezza di Azure, non verrà riconfigurata automaticamente da gestione automaticamente.    |Produzione, sviluppo/test    |No    |
|Gestione degli aggiornamenti    |È possibile usare Gestione aggiornamenti in automazione di Azure per gestire gli aggiornamenti del sistema operativo per le macchine virtuali. È possibile valutare rapidamente lo stato degli aggiornamenti disponibili in tutti i computer agente e gestire il processo di installazione degli aggiornamenti necessari per i server. [Altre](../automation/update-management/overview.md)informazioni.    |Produzione, sviluppo/test    |No    |
|Inventario Rilevamento modifiche &    |Rilevamento modifiche e inventario combina le funzioni di rilevamento delle modifiche e di inventario per consentire di tenere traccia delle modifiche apportate all'infrastruttura di macchine virtuali e server. Il servizio supporta il rilevamento delle modifiche tra i servizi, il software DAEMONS, il registro di sistema e i file nell'ambiente per facilitare la diagnosi delle modifiche indesiderate e la generazione di avvisi. Il supporto dell'inventario consente di eseguire query sulle risorse nel guest per ottenere la visibilità delle applicazioni installate e di altri elementi di configurazione.  [Altre](../automation/change-tracking/overview.md)informazioni.    |Produzione, sviluppo/test    |No    |
|Configurazione Guest di Azure    | I criteri di configurazione Guest vengono usati per monitorare la configurazione e creare report sulla conformità del computer. Il servizio di gestione automatica installerà la baseline Linux di Azure usando l'estensione di configurazione Guest. Per i computer Linux, il servizio di configurazione Guest installerà la linea di base in modalità solo controllo. Sarà possibile vedere il punto in cui la macchina virtuale non è conforme alla linea di base, ma la mancata conformità non verrà risolta automaticamente. [Altre](../governance/policy/concepts/guest-configuration.md)informazioni.    |Produzione, sviluppo/test    |No    |
|Account di automazione di Azure    |Automazione di Azure supporta la gestione per tutto il ciclo di vita dell'infrastruttura e delle applicazioni. [Altre](../automation/automation-intro.md)informazioni.    |Produzione, sviluppo/test    |No    |
|Area di lavoro Log Analytics    |Monitoraggio di Azure archivia i dati di log in un'area di lavoro Log Analytics, ovvero una risorsa di Azure e un contenitore in cui i dati vengono raccolti, aggregati e utilizzati come limite amministrativo. [Altre](../azure-monitor/logs/design-logs-deployment.md)informazioni.    |Produzione, sviluppo/test    |No    |


<sup>1</sup> la selezione dell'ambiente è disponibile quando si Abilita automanage. [Altre](automanage-virtual-machines.md#environment-configuration)informazioni. È anche possibile modificare le impostazioni predefinite dell'ambiente e impostare le proprie preferenze nei vincoli delle procedure consigliate.


## <a name="next-steps"></a>Passaggi successivi

Provare ad abilitare la gestione automatici per le macchine virtuali nella portale di Azure.

> [!div class="nextstepaction"]
> [Abilitare la gestione automatici per le macchine virtuali nella portale di Azure](quick-create-virtual-machines-portal.md)