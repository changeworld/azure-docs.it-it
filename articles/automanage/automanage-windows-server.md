---
title: Gestione automatica di Azure per Windows Server
description: Informazioni sulle procedure consigliate Gestione automatica di Azure per le macchine virtuali per i servizi che vengono automaticamente onboarded e configurati per i computer Windows Server.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: deanwe
ms.openlocfilehash: 98d91356b55be015b2c1b73787dad0bb7d8fd424
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107863012"
---
# <a name="azure-automanage-for-virtual-machines-best-practices---windows-server"></a>Gestione automatica di Azure per le procedure consigliate per le macchine virtuali - Windows Server

Questi servizi di Azure vengono automaticamente onboarded quando si usa Gestione automatica per macchine virtuali (VM) in una macchina virtuale Windows Server. Sono essenziali per le procedure consigliate white paper, che è possibile trovare nel Cloud Adoption Framework [.](/azure/cloud-adoption-framework/manage/azure-server-management)

Per tutti questi servizi, si eseguirà l'onboardment automatico, la configurazione automatica, il monitoraggio della deriva e la correzione se viene rilevata la deriva. Per altre informazioni su questo processo, vedere Gestione automatica di Azure [per le macchine virtuali.](automanage-virtual-machines.md)

## <a name="supported-windows-server-versions"></a>Versioni supportate di Windows Server

La gestione automatica supporta le versioni di Windows Server seguenti:

- Windows Server 2012/R2
- Windows Server 2016
- Windows Server 2019
- Windows Server 2019 Azure Edition

## <a name="participating-services"></a>Servizi partecipanti

|Servizio    |Descrizione    |Ambienti supportati<sup>1</sup>    |Preferenze supportate<sup>1</sup>    |
|-----------|---------------|----------------------|-------------------------|
|[Monitoraggio di Informazioni dettagliate sulle macchine virtuali](https://docs.microsoft.com/azure/azure-monitor/vm/vminsights-overview)    |Monitoraggio di Azure per le macchine virtuali le prestazioni e l'integrità delle macchine virtuali, inclusi i processi in esecuzione e le dipendenze da altre risorse. [Altre informazioni](../azure-monitor/vm/vminsights-overview.md).    |Produzione    |No    |
|[Backup](https://docs.microsoft.com/azure/backup/backup-overview)    |Backup di Azure offre backup indipendenti e isolati per prevenire la distruzione accidentale dei dati nelle macchine virtuali. [Altre informazioni](../backup/backup-azure-vms-introduction.md). Gli addebiti si basano sul numero e sulle dimensioni delle macchine virtuali protette. [Altre informazioni](https://azure.microsoft.com/pricing/details/backup/).    |Produzione    |Sì    |
|[Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-introduction)    |Centro sicurezza di Azure è un sistema di gestione della sicurezza dell'infrastruttura unificato che rafforza la sicurezza dei data center e offre una protezione avanzata dalle minacce nei carichi di lavoro ibridi nel cloud. [Altre informazioni](../security-center/security-center-introduction.md).  La gestione automatica configurerà la sottoscrizione in cui risiede la macchina virtuale nell'offerta di livello gratuito di Centro sicurezza di Azure. Se la sottoscrizione è già stata Centro sicurezza di Azure, gestione automatica non la riconfigurerà.    |Produzione, sviluppo/test    |No    |
|[Microsoft Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware)    |Microsoft Antimalware per Azure è una funzionalità di protezione in tempo reale gratuita che aiuta a identificare e rimuovere virus, spyware e altro software dannoso e genera avvisi quando un software dannoso o indesiderato tenta l'installazione o l'esecuzione nei sistemi di Azure. [Altre informazioni](../security/fundamentals/antimalware.md). |Produzione, sviluppo/test    |Sì    |
|[Gestione degli aggiornamenti](https://docs.microsoft.com/azure/automation/update-management/overview)    |È possibile usare Gestione aggiornamenti in Automazione di Azure per gestire gli aggiornamenti del sistema operativo per le macchine virtuali. È possibile valutare rapidamente lo stato degli aggiornamenti disponibili in tutti i computer agente e gestire il processo di installazione degli aggiornamenti necessari per i server. [Altre informazioni](../automation/update-management/overview.md).    |Produzione, sviluppo/test    |No    |
|[Rilevamento modifiche & inventario](https://docs.microsoft.com/azure/automation/change-tracking/overview) |Rilevamento modifiche e inventario combina le funzioni di rilevamento delle modifiche e di inventario per consentire di tenere traccia delle modifiche apportate all'infrastruttura di macchine virtuali e server. Il servizio supporta il rilevamento delle modifiche tra servizi, software daemon, Registro di sistema e file nell'ambiente in uso per facilitare la diagnosi delle modifiche indesiderate e generare avvisi. Il supporto dell'inventario consente di eseguire query sulle risorse nel guest per ottenere la visibilità delle applicazioni installate e di altri elementi di configurazione.  [Altre informazioni](../automation/change-tracking/overview.md).    |Produzione, sviluppo/test    |No    |
|[Configurazione guest di Azure](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration) | I criteri di configurazione guest vengono usati per monitorare la configurazione e creare report sulla conformità del computer. Il servizio Gestione automatica installerà le baseline di [sicurezza di Windows](/windows/security/threat-protection/windows-security-baselines) usando l'estensione Configurazione guest. Per i computer Windows, il servizio di configurazione guest riapplica automaticamente le impostazioni di base se non sono conformi. [Altre informazioni](../governance/policy/concepts/guest-configuration.md).    |Produzione, sviluppo/test    |No    |
|[Diagnostica di avvio](https://docs.microsoft.com/azure/virtual-machines/boot-diagnostics)    | La diagnostica di avvio è una funzionalità di debug per le macchine virtuali di Azure che consente la diagnosi degli errori di avvio delle macchine virtuali. La diagnostica di avvio consente a un utente di osservare lo stato della macchina virtuale durante l'avvio raccogliendo le informazioni sui log seriali e gli screenshot. Questa opzione verrà abilitata solo per i computer che usano dischi gestiti. |Produzione, sviluppo/test    |No    |
|[Automazione di Azure account](https://docs.microsoft.com/azure/automation/automation-create-standalone-account)    |Automazione di Azure supporta la gestione per tutto il ciclo di vita dell'infrastruttura e delle applicazioni. [Altre informazioni](../automation/automation-intro.md).    |Produzione, sviluppo/test    |No    |
|[Area di lavoro Log Analytics](https://docs.microsoft.com/azure/azure-monitor/logs/log-analytics-overview) |Monitoraggio di Azure i dati di log in un'area di lavoro Log Analytics, che è una risorsa di Azure e un contenitore in cui i dati vengono raccolti, aggregati e vengono serviti come limite amministrativo. [Altre informazioni](../azure-monitor/logs/design-logs-deployment.md).    |Produzione, sviluppo/test    |No    |


<sup>1</sup> La selezione dell'ambiente è disponibile quando si abilita l'opzione Automanage. [Altre informazioni](automanage-virtual-machines.md#environment-configuration). È anche possibile modificare le impostazioni predefinite dell'ambiente e impostare preferenze personalizzate entro i vincoli delle procedure consigliate.


## <a name="next-steps"></a>Passaggi successivi

Provare ad abilitare la gestione automatica per le macchine virtuali nel portale di Azure.

> [!div class="nextstepaction"]
> [Abilitare la gestione automatica per le macchine virtuali nel portale di Azure](quick-create-virtual-machines-portal.md)