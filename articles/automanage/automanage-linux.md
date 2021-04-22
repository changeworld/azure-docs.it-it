---
title: Gestione automatica di Azure per Linux
description: Informazioni sulle procedure consigliate Gestione automatica di Azure per le macchine virtuali per i servizi che vengono automaticamente onboarded e configurati per le macchine Linux.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.collection: linux
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: deanwe
ms.openlocfilehash: 0d35963d96ead68c35ca44467119b3c03d0b16c8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107863066"
---
# <a name="azure-automanage-for-virtual-machines-best-practices---linux"></a>Gestione automatica di Azure procedure consigliate per le macchine virtuali - Linux

Questi servizi di Azure vengono automaticamente onboarded quando si usa Gestione automatica per le macchine virtuali (VM) in una macchina virtuale Linux. Sono essenziali per le procedure consigliate white paper, disponibili nell'Cloud Adoption Framework [.](/azure/cloud-adoption-framework/manage/azure-server-management)

Per tutti questi servizi, si eseguirà l'onboardmento automatico, la configurazione automatica, il monitoraggio della deriva e la correzione se viene rilevata la deriva. Per altre informazioni su questo processo, vedere Gestione automatica di Azure [per le macchine virtuali.](automanage-virtual-machines.md)

## <a name="supported-linux-distributions-and-versions"></a>Distribuzioni e versioni di Linux supportate

La gestione automatica supporta le distribuzioni e le versioni di Linux seguenti:

- CentOS 7.3+
- RHEL 7.4+
- Ubuntu 16.04 e 18.04
- SLES 12 (solo SP3-SP5)

## <a name="participating-services"></a>Servizi partecipanti

>[!NOTE]
> Microsoft Antimalware non è attualmente supportato nelle macchine virtuali Linux.

|Servizio    |Descrizione    |Ambienti supportati<sup>1</sup>    |Preferenze supportate<sup>1</sup>    |
|-----------|---------------|----------------------|-------------------------|
|[Monitoraggio di Informazioni dettagliate macchina virtuale](https://docs.microsoft.com/azure/azure-monitor/vm/vminsights-overview)    |Monitoraggio di Azure per le macchine virtuali monitora le prestazioni e l'integrità delle macchine virtuali, inclusi i processi in esecuzione e le dipendenze da altre risorse. [Altre informazioni](../azure-monitor/vm/vminsights-overview.md).    |Produzione    |No    |
|[Backup](https://docs.microsoft.com/azure/backup/backup-overview)   |Backup di Azure offre backup indipendenti e isolati per prevenire la distruzione accidentale dei dati nelle macchine virtuali. [Altre informazioni](../backup/backup-azure-vms-introduction.md). Gli addebiti sono basati sul numero e sulle dimensioni delle macchine virtuali da proteggere. [Altre informazioni](https://azure.microsoft.com/pricing/details/backup/).    |Produzione    |Sì    |
|[Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-introduction)    |Centro sicurezza di Azure è un sistema unificato di gestione della sicurezza dell'infrastruttura che potenzia il livello di sicurezza dei data center e offre protezione avanzata dalle minacce nei carichi di lavoro ibridi nel cloud. [Altre informazioni](../security-center/security-center-introduction.md).  La gestione automatica configurerà la sottoscrizione in cui risiede la macchina virtuale nell'offerta di livello gratuito di Centro sicurezza di Azure. Se la sottoscrizione è già stata Centro sicurezza di Azure, gestione automatica non la riconfigurerà.    |Produzione, sviluppo/test    |No    |
|[Gestione degli aggiornamenti](https://docs.microsoft.com/azure/automation/update-management/overview)    |È possibile usare Gestione aggiornamenti in Automazione di Azure per gestire gli aggiornamenti del sistema operativo per le macchine virtuali. È possibile valutare rapidamente lo stato degli aggiornamenti disponibili in tutti i computer agente e gestire il processo di installazione degli aggiornamenti necessari per i server. [Altre informazioni](../automation/update-management/overview.md).    |Produzione, sviluppo/test    |No    |
|[Rilevamento modifiche & inventario](https://docs.microsoft.com/azure/automation/change-tracking/overview) |Rilevamento modifiche e inventario combina le funzioni di rilevamento delle modifiche e di inventario per consentire di tenere traccia delle modifiche apportate all'infrastruttura di macchine virtuali e server. Il servizio supporta il rilevamento delle modifiche tra servizi, software daemon, Registro di sistema e file nell'ambiente per diagnosticare le modifiche indesiderate e generare avvisi. Il supporto dell'inventario consente di eseguire query sulle risorse nel guest per ottenere la visibilità delle applicazioni installate e di altri elementi di configurazione.  [Altre informazioni](../automation/change-tracking/overview.md).    |Produzione, sviluppo/test    |No    |
|[Configurazione guest di Azure](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration)  | I criteri di configurazione guest vengono usati per monitorare la configurazione e segnalare la conformità del computer. Il servizio Gestione automatica installerà la baseline linux di Azure usando l'estensione Configurazione guest. Per i computer Linux, il servizio di configurazione guest installerà la baseline in modalità solo controllo. Sarà possibile vedere dove la macchina virtuale non è conforme alla baseline, ma la non conformità non verrà corretti automaticamente. [Altre informazioni](../governance/policy/concepts/guest-configuration.md).    |Produzione, sviluppo/test    |No    |
|[Diagnostica di avvio](https://docs.microsoft.com/azure/virtual-machines/boot-diagnostics)  | La diagnostica di avvio è una funzionalità di debug per le macchine virtuali di Azure che consente la diagnosi degli errori di avvio della macchina virtuale. La diagnostica di avvio consente a un utente di osservare lo stato della macchina virtuale durante l'avvio raccogliendo le informazioni di log seriali e gli screenshot. Questa opzione verrà abilitata solo per i computer che usano dischi gestiti. |Produzione, sviluppo/test    |No    |
|[Automazione di Azure account](https://docs.microsoft.com/azure/automation/automation-create-standalone-account)    |Automazione di Azure supporta la gestione per tutto il ciclo di vita dell'infrastruttura e delle applicazioni. [Altre informazioni](../automation/automation-intro.md).    |Produzione, sviluppo/test    |No    |
|[Area di lavoro Log Analytics](https://docs.microsoft.com/azure/azure-monitor/logs/log-analytics-overview) |Monitoraggio di Azure i dati di log in un'area di lavoro Log Analytics, che è una risorsa di Azure e un contenitore in cui i dati vengono raccolti, aggregati e vengono serviti come limite amministrativo. [Altre informazioni](../azure-monitor/logs/design-logs-deployment.md).    |Produzione, sviluppo/test    |No    |


<sup>1</sup> La selezione dell'ambiente è disponibile quando si abilita La funzionalità Disarticola automaticamente. [Altre informazioni](automanage-virtual-machines.md#environment-configuration). È anche possibile modificare le impostazioni predefinite dell'ambiente e impostare preferenze personalizzate entro i vincoli delle procedure consigliate.


## <a name="next-steps"></a>Passaggi successivi

Provare ad abilitare Gestione automatica per le macchine virtuali nel portale di Azure.

> [!div class="nextstepaction"]
> [Abilitare La gestione automatica per le macchine virtuali nel portale di Azure](quick-create-virtual-machines-portal.md)