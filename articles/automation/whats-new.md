---
title: Novità di automazione di Azure
description: Aggiornamenti significativi di automazione di Azure aggiornati ogni mese.
ms.subservice: ''
ms.topic: overview
author: mgoedtel
ms.author: magoedte
ms.date: 02/23/2021
ms.custom: references_regions
ms.openlocfilehash: 899249c98c3ce0fdf061b1e689182f71c120aa13
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101729396"
---
# <a name="whats-new-in-azure-automation"></a>Novità di automazione di Azure

Automazione di Azure riceve i miglioramenti su base continuativa. Per stare al passo con gli sviluppi più recenti, questo articolo fornisce informazioni sugli argomenti seguenti:

- Versioni più recenti
- Problemi noti
- Correzioni di bug

Questa pagina viene aggiornata ogni mese, si consiglia pertanto di consultarla regolarmente.

## <a name="february-2021"></a>2021 febbraio

### <a name="support-for-automation-and-state-configuration-declared-ga-in-japan-west"></a>Supporto per l'automazione e la configurazione dello stato dichiarate GA in Giappone occidentale

**Tipo:** Nuova funzionalità

Disponibilità dell'account di automazione e della configurazione dello stato nell'area Giappone occidentale. Per ulteriori informazioni, leggere l' [annuncio](https://azure.microsoft.com/updates/azure-automation-in-japan-west-region/).

### <a name="introduced-custom-azure-policy-compliance-to-enforce-runbook-execution-on-hybrid-worker"></a>È stata introdotta la conformità dei criteri di Azure personalizzata per applicare l'esecuzione di Runbook nel ruolo

**Tipo:** Nuova funzionalità

È possibile usare la nuova regola di conformità dei criteri di Azure per consentire la creazione di processi, webhook e pianificazioni di processi da eseguire solo nei gruppi di lavoro ibridi.

### <a name="update-management-availability-in-east-us-france-central-and-north-europe-regions"></a>Gestione aggiornamenti disponibilità nelle aree Stati Uniti orientali, Francia centrale ed Europa settentrionale

**Tipo:** Nuova funzionalità

La funzionalità Gestione aggiornamenti di automazione è disponibile nelle aree Stati Uniti orientali, Francia centrale ed Europa settentrionale. Vedere [mapping delle aree supportate](how-to/region-mappings.md) per gli aggiornamenti alla documentazione che riflettono questa modifica.

## <a name="january-2021"></a>Gennaio 2021

### <a name="support-for-automation-and-state-configuration-declared-ga-in-switzerland-west"></a>Supporto per l'automazione e la configurazione dello stato dichiarata GA in Svizzera occidentale

**Tipo:** Nuova funzionalità

Disponibilità dell'account di automazione e della configurazione dello stato nell'area Svizzera occidentale. Per ulteriori informazioni, leggere l' [annuncio](https://azure.microsoft.com/updates/azure-automation-in-switzerland-west-region/).

### <a name="added-python-3-script-to-import-module-with-multiple-dependencies"></a>Aggiunta dello script Python 3 per importare il modulo con più dipendenze

**Tipo:** Nuova funzionalità

Lo script è disponibile per il download dal [repository GitHub](https://github.com/azureautomation/runbooks/blob/master/Utility/Python/import_py3package_from_pypi.py). 
 
### <a name="hybrid-runbook-worker-role-support-for-centos-8xrhel-8xsles-15"></a>Supporto del ruolo di lavoro ibrido per Runbook per CentOS 8. x/RHEL 8. x/SLES 15

**Tipo.** Nuova funzionalità

La funzionalità Hybrid Runbook Worker supporta le distribuzioni CentOS 8. x, REHL 8. x e SLES 15 per l'automazione dei processi solo nei ruoli di lavoro ibridi per Runbook.  Per riflettere queste modifiche, vedere [sistemi operativi supportati](automation-linux-hrw-install.md#supported-linux-operating-systems) per gli aggiornamenti della documentazione.

### <a name="update-management--change-tracking-availability-in-australia-east-east-asia-west-us--central-us-regions"></a>Gestione aggiornamenti & disponibilità Rilevamento modifiche in Australia orientale, Asia orientale, Stati Uniti occidentali & aree Stati Uniti centrali

**Tipo:** Nuova funzionalità

Gli account di automazione, Rilevamento modifiche e inventario e i Gestione aggiornamenti sono disponibili nelle aree Australia orientale, Asia orientale, Stati Uniti occidentali & Stati Uniti centrali. 

### <a name="introduced-public-preview-of-python-3-runbooks-in-us-government-cloud"></a>È stata introdotta l'anteprima pubblica di Python 3 manuali operativi nel cloud degli Stati Uniti

**Tipo:** La nuova funzionalità automazione di Azure introduce il supporto per l'anteprima pubblica del cloud Python 3 e l'esecuzione di Runbook ibride nelle aree del cloud degli Stati Uniti.  Per altre informazioni, vedere l'[annuncio](https://azure.microsoft.com/updates/azure-automation-python-3-public-preview/).

### <a name="azure-automation-runbooks-moved-from-technet-script-center-to-github"></a>Manuali operativi di automazione di Azure spostati da TechNet Script Center a GitHub

**Tipo:** Modifica pianificata

TechNet Script Center è in ritiro e tutti i manuali operativi ospitati nella raccolta Runbook sono stati spostati nell' [organizzazione di GitHub di automazione](https://github.com/azureautomation). Per altre informazioni, vedere [manuali operativi di automazione di Azure che passano a GitHub](https://techcommunity.microsoft.com/t5/azure-governance-and-management/azure-automation-runbooks-moving-to-github/ba-p/2039337).

## <a name="december-2020"></a>Dicembre 2020

### <a name="azure-automation-and-update-management-private-link-ga"></a>Automazione di Azure e collegamento privato Gestione aggiornamenti GA

**Tipo:** Nuova funzionalità

Automazione di Azure e supporto Gestione aggiornamenti annunciato come GA per cloud globali e per enti pubblici di Azure. Supporto del collegamento privato abilitato per automazione di Azure per proteggere l'esecuzione di un Runbook in un ruolo di lavoro ibrido, usando Gestione aggiornamenti per applicare patch ai computer, richiamare un Runbook tramite un webhook e usare il servizio di configurazione dello stato per mantenere la denuncia dei computer. Per altre informazioni, leggere [supporto del collegamento privato di automazione di Azure](https://azure.microsoft.com/updates/azure-automation-private-link)

### <a name="azure-automation-classified-as-grade-c-certified-on-accessibility"></a>Automazione di Azure classificata come Grade-C certificato per l'accessibilità

**Tipo:** Nuova funzionalità

Le funzionalità di accessibilità dei prodotti Microsoft consentono di soddisfare i requisiti di accessibilità globali. Nella pagina dell' [annuncio del Blog](https://cloudblogs.microsoft.com/industry-blog/government/2018/09/11/accessibility-conformance-reports/) cercare automazione di **Azure** per leggere il report di conformità dell'accessibilità per il servizio di automazione.

### <a name="support-for-automation-and-state-configuration-ga-in-uae-north"></a>Supporto per l'automazione e la configurazione dello stato GA in Emirati Arabi Uniti settentrionali

**Tipo:** Nuova funzionalità

Disponibilità dell'account di automazione e della configurazione dello stato nell'area Emirati Arabi Uniti settentrionali. Per ulteriori informazioni, leggere l' [annuncio](https://azure.microsoft.com/updates/azure-automation-in-uae-north-region/).

### <a name="support-for-automation-and-state-configuration-ga-in-germany-west-central"></a>Supporto per l'automazione e la configurazione dello stato GA in Germania centro-occidentale

**Tipo:** Nuova funzionalità

Disponibilità dell'account di automazione e della configurazione dello stato nell'area Germania occidentale. Per ulteriori informazioni, leggere l' [annuncio](https://azure.microsoft.com/updates/azure-automation-in-germany-west-central-region/).

### <a name="dsc-support-for-oracle-6-and-7"></a>Supporto DSC per Oracle 6 e 7

**Tipo:** Nuova funzionalità

Gestire Oracle Linux computer 6 e 7 con la configurazione dello stato di automazione. Per riflettere queste modifiche, vedere [distribuzioni Linux supportate](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions) per gli aggiornamenti della documentazione.

### <a name="public-preview-for-python3-runbooks-in-automation"></a>Anteprima pubblica per Python3 manuali operativi in automazione

**Tipo:** Nuova funzionalità

Automazione di Azure ora supporta l'esecuzione Runbook ibrida di Python 3 cloud & in versione di anteprima pubblica in tutte le aree del cloud globale di Azure. Per ulteriori informazioni, vedere [annuncio] (() https://azure.microsoft.com/updates/azure-automation-python-3-public-preview/) .

## <a name="november-2020"></a>Novembre 2020

### <a name="dsc-support-for-ubuntu-1804"></a>Supporto DSC per Ubuntu 18,04

**Tipo:** Nuova funzionalità

Vedere [distribuzioni Linux supportate](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions) per gli aggiornamenti della documentazione che riflettono tali modifiche.

## <a name="october-2020"></a>Ottobre 2020

### <a name="support-for-automation-and-state-configuration-ga-in-switzerland-north"></a>Supporto per l'automazione e la configurazione dello stato GA in Svizzera settentrionale

**Tipo:** Nuova funzionalità

Disponibilità dell'account di automazione e della configurazione dello stato in Svizzera settentrionale. Per ulteriori informazioni, leggere l' [annuncio](https://azure.microsoft.com/updates/azure-automation-in-switzerland-north-region/).

### <a name="support-for-automation-and-state-configuration-ga-in-brazil-south-east"></a>Supporto per l'automazione e la configurazione dello stato GA in Brasile sud-orientale

**Tipo:** Nuova funzionalità

Disponibilità dell'account di automazione e della configurazione dello stato in Brasile sud-orientale. Per ulteriori informazioni, leggere l' [annuncio](https://azure.microsoft.com/updates/azure-automation-in-brazil-southeast-region/).

### <a name="update-management-availability-in-south-central-us"></a>Disponibilità Gestione aggiornamenti negli Stati Uniti centro-meridionali

**Tipo:** Nuova funzionalità

Mapping dell'area di automazione di Azure aggiornato per supportare Gestione aggiornamenti funzionalità nell'area Stati Uniti centro-meridionali. Per informazioni su questa modifica, vedere [mapping delle aree supportate](how-to/region-mappings.md#supported-mappings) per gli aggiornamenti della documentazione.

## <a name="september-2020"></a>Settembre 2020

### <a name="startstop-vms-during-off-hours-runbooks-updated-to-use-azure-az-modules"></a>Avvio/Arresto di macchine virtuali durante gli orari di minore attività manuali operativi aggiornato per l'uso di Azure AZ Modules

**Tipo:** Nuova funzionalità

Le manuali operativi di avvio/arresto delle macchine virtuali sono state aggiornate in modo da usare AZ Modules al posto dei moduli Azure Resource Manager. Per riflettere queste modifiche, vedere [avvio/arresto di macchine virtuali durante gli orari di minore attività](automation-solution-vm-management.md) Panoramica degli aggiornamenti della documentazione.

## <a name="august-2020"></a>Agosto 2020

### <a name="published-the-dsc-extension-to-support-azure-arc"></a>Pubblicazione dell'estensione DSC per supportare Azure Arc

**Tipo:** Nuova funzionalità

Usare la configurazione dello stato di automazione di Azure per archiviare centralmente le configurazioni e mantenere lo stato desiderato dei computer connessi ibridi abilitati tramite l'estensione VM DSC per i server abilitati per Azure Arc. Per altre informazioni, vedere [Panoramica delle estensioni VM dei server abilitati per Arc](../azure-arc/servers/manage-vm-extensions.md).

### <a name="july-2020"></a>Luglio 2020

### <a name="introduced-public-preview-of-private-link-support-in-automation"></a>È stata introdotta l'anteprima pubblica del supporto del collegamento privato in automazione

**Tipo:** Nuova funzionalità

Usare il collegamento privato di Azure per connettere in modo sicuro le reti virtuali ad automazione di Azure usando endpoint privati. Per ulteriori informazioni, leggere l' [annuncio](https://azure.microsoft.com/updates/public-preview-private-link-azure-automation-is-now-available/).

### <a name="hybrid-runbook-worker-support-for-windows-server-2008-r2"></a>Supporto del ruolo di lavoro ibrido per Runbook per Windows Server 2008 R2

**Tipo:** Nuova funzionalità

Il ruolo di lavoro ibrido per Runbook di automazione supporta il sistema operativo Windows Server 2008 R2. Per riflettere queste modifiche, vedere [sistemi operativi supportati](automation-windows-hrw-install.md#supported-windows-operating-system) per gli aggiornamenti della documentazione.

### <a name="update-management-support-for-windows-server-2008-r2"></a>Supporto Gestione aggiornamenti per Windows Server 2008 R2

**Tipo:** Nuova funzionalità

Gestione aggiornamenti supporta la valutazione e l'applicazione di patch al sistema operativo Windows Server 2008 R2. Per riflettere queste modifiche, vedere [sistemi operativi supportati](update-management/overview.md#clients) per gli aggiornamenti della documentazione.

### <a name="automation-diagnostic-logs-schema-update"></a>Aggiornamento dello schema dei log di diagnostica di automazione

**Tipo:** Nuova funzionalità

Modifica dello schema dei dati del log di automazione di Azure nel servizio Log Analytics. Per altre informazioni, vedere [trasmettere i dati dei processi di automazione di Azure ai log di monitoraggio di Azure](automation-manage-send-joblogs-log-analytics.md#filter-job-status-output-converted-into-a-json-object).

### <a name="azure-lighthouse-supports-automation-update-management"></a>Azure Lighthouse supporta Gestione aggiornamenti di automazione

**Tipo:** Nuova funzionalità

Azure Lighthouse consente la gestione delegata delle risorse con Gestione aggiornamenti per i provider di servizi e i clienti. Altre informazioni sono disponibili [qui](https://azure.microsoft.com/blog/how-azure-lighthouse-enables-management-at-scale-for-service-providers/).

## <a name="june-2020"></a>Giugno 2020

### <a name="automation-and-update-management-availability-in-the-us-gov-arizona-region"></a>Automazione e disponibilità Gestione aggiornamenti nell'area US Gov Arizona

**Tipo:** Nuova funzionalità

L'account di automazione e Gestione aggiornamenti sono disponibili in US Gov Arizona. Per ulteriori informazioni, vedere l' [annuncio](https://azure.microsoft.com/updates/azure-automation-generally-available-in-usgov-arizona-region/).

### <a name="hybrid-runbook-worker-onboarding-script-updated-to-use-az-modules"></a>Script di onboarding di Runbook Worker ibrido aggiornato per l'uso di AZ Modules

**Tipo:** Nuova funzionalità

Il New-OnPremiseHybridWorker Runbook è stato aggiornato per supportare AZ modules. Per ulteriori informazioni, vedere il pacchetto nel [PowerShell Gallery](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/1.7).

### <a name="update-management-availability-in-china-east-2"></a>Disponibilità Gestione aggiornamenti in Cina orientale 2

**Tipo:** Nuova funzionalità

Mapping dell'area di automazione di Azure aggiornato per supportare la funzionalità Gestione aggiornamenti in Cina orientale 2. Per informazioni su questa modifica, vedere [mapping delle aree supportate](how-to/region-mappings.md#supported-mappings) per gli aggiornamenti della documentazione.

## <a name="may-2020"></a>Maggio 2020

### <a name="updated-automation-service-dns-records-from-region-specific-to-automation-account-specific-urls"></a>Record DNS del servizio di automazione aggiornati dall'area specifica agli URL specifici dell'account di automazione

**Tipo:** Nuova funzionalità

I record DNS di automazione di Azure sono stati aggiornati per supportare i collegamenti privati. Per ulteriori informazioni, leggere l' [annuncio](https://azure.microsoft.com/updates/azure-automation-updateddns-records/).

### <a name="added-capability-to-keep-automation-runbooks--dsc-scripts-encrypted-by-default"></a>È stata aggiunta la funzionalità per proteggere manuali operativi di automazione & gli script DSC crittografati per impostazione predefinita

**Tipo:** Nuova funzionalità

Oltre a migliorare la sicurezza degli asset, vengono crittografati anche gli script manuali operativi & DSC per migliorare la sicurezza di automazione di Azure.

## <a name="april-2020"></a>Aprile 2020

### <a name="retirement-of-the-automation-watcher-task"></a>Ritiro dell'attività Watcher di automazione

**Tipo:** Modifica pianificata

App per la logica di Azure è ora la modalità consigliata e supportata per monitorare gli eventi, pianificare le attività ricorrenti e attivare le azioni. Non vi saranno ulteriori investimenti nella funzionalità Watcher Task. Per altre informazioni, vedere [pianificare ed eseguire attività automatiche ricorrenti con le app per la logica](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

## <a name="march-2020"></a>Marzo 2020

### <a name="support-for-impact-level-5-il5-compute-isolation-in-azure-commercial-and-government-cloud"></a>Supporto per l'isolamento di calcolo IL5 (Impact Level 5) nel cloud commerciale e per enti pubblici di Azure

**Tipo:**

Il ruolo di lavoro ibrido per Runbook di automazione di Azure può essere usato in Azure per enti pubblici per supportare carichi di lavoro di livello 5 Per altre informazioni, vedere la [documentazione](automation-hybrid-runbook-worker.md#support-for-impact-level-5-il5).

## <a name="february-2020"></a>Febbraio 2020

### <a name="introduced-support-for-azure-virtual-network-service-tags"></a>Introduzione del supporto per i tag del servizio rete virtuale di Azure

**Tipo:** Nuova funzionalità

Il supporto di automazione dei tag del servizio consente o nega il traffico per il servizio di automazione, per un subset di scenari. Per altre informazioni, vedere la [documentazione](automation-hybrid-runbook-worker.md#service-tags).

### <a name="enable-tls-12-support-for-azure-automation-service"></a>Abilitare il supporto TLS 1,2 per il servizio automazione di Azure

**Tipo:** Modifica pianificata

Automazione di Azure supporta completamente TLS 1,2 e tutte le chiamate client (tramite webhook, nodi DSC e ruolo di lavoro ibrido). TLS 1,1 e TLS 1,0 sono ancora supportati per la compatibilità con le versioni precedenti dei client, fino a quando i clienti eseguono la standardizzazione e la migrazione completa a TLS 1,2.

## <a name="january-2020"></a>Gennaio 2020

### <a name="introduced-public-preview-of-customer-managed-keys-for-azure-automation"></a>È stata introdotta l'anteprima pubblica delle chiavi gestite dal cliente per automazione di Azure

**Tipo:** Nuova funzionalità

I clienti possono gestire e proteggere la crittografia degli asset di automazione di Azure usando le proprie chiavi gestite. Per ulteriori informazioni, vedere [utilizzo delle chiavi gestite dal cliente](automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account).

### <a name="retirement-of-azure-service-management-asm-rest-apis-for-azure-automation"></a>Ritiro delle API REST di gestione dei servizi di Azure (ASM) per automazione di Azure

**Tipo:** Ritirare

Le API REST di Azure Service Management (ASM) per automazione di Azure verranno ritirate e non saranno più supportate dopo il 30 gennaio 2020. Per altre informazioni, vedere l' [annuncio](https://azure.microsoft.com/updates/azure-automation-service-management-rest-apis-are-being-retired-april-30-2019/).

## <a name="next-steps"></a>Passaggi successivi

Se si vuole contribuire alla documentazione di automazione di Azure, vedere la [Guida](/contribute/)per i collaboratori di docs.
