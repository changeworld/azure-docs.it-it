---
title: Novità di Automazione di Azure
description: Aggiornamenti significativi delle Automazione di Azure aggiornati ogni mese.
ms.subservice: ''
ms.topic: overview
author: mgoedtel
ms.author: magoedte
ms.date: 04/09/2021
ms.custom: references_regions
ms.openlocfilehash: f8b4d6965a8a1f046fd2459ce9fe5cce8ea45443
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531083"
---
# <a name="whats-new-in-azure-automation"></a>Novità di Automazione di Azure?

Automazione di Azure riceve miglioramenti su base continuativa. Per stare al passo con gli sviluppi più recenti, questo articolo fornisce informazioni sugli argomenti seguenti:

- Versioni più recenti
- Problemi noti
- Correzioni di bug

Questa pagina viene aggiornata ogni mese, si consiglia pertanto di consultarla regolarmente.

## <a name="march-2021"></a>Marzo 2021

### <a name="new-azure-automation-built-in-policies"></a>Nuovi Automazione di Azure criteri predefiniti

**Tipo:** Nuova funzionalità

Automazione di Azure sono stati aggiunti 5 nuovi criteri predefiniti:

- Gli account di automazione devono disabilitare l'accesso alla rete pubblica,
- Automazione di Azure account devono usare chiavi gestite dal cliente per crittografare i dati in pausa
- Configurare gli Automazione di Azure per disabilitare l'accesso alla rete pubblica
- Configurare le connessioni degli endpoint privati Automazione di Azure account
- Le connessioni endpoint private sugli account di Automazione devono essere abilitate.

Per altri [dettagli, vedere](./policy-reference.md) l'articolo di riferimento sui criteri.

### <a name="support-for-automation-and-state-configuration-declared-ga-in-south-india"></a>Supporto per Automazione e State Configuration ga ga dichiarato in India meridionale

**Tipo:** Nuova funzionalità

Usare l'automazione dei processi e le funzionalità di configurazione dello stato nell'India meridionale. Per altre [informazioni,](https://azure.microsoft.com/updates/azure-automation-in-south-india-region/) leggere l'annuncio.

### <a name="support-for-automation-and-state-configuration-declared-ga-in-uk-west"></a>Supporto per Automazione e State Configuration a livello di codice a livello di Regno Unito occidentale

**Tipo:** Nuova funzionalità

Usare le funzionalità di automazione dei processi e di configurazione dello stato Regno Unito occidentale. Per altre informazioni, vedere [annuncio](https://azure.microsoft.com/updates/azure-automation-in-uk-west-region/).

### <a name="support-for-automation-and-state-configuration-declared-ga-in-uae-central"></a>Supporto per Automazione e State Configuration a livello di codice a livello di Emirati Arabi Uniti centrali

**Tipo:** Nuova funzionalità

Usare le funzionalità di automazione dei processi e di configurazione dello stato Emirati Arabi Uniti centrali. Per altre [informazioni,](https://azure.microsoft.com/updates/azure-automation-in-uae-central-region/) leggere l'annuncio.

### <a name="support-for-automation-and-state-configuration-available-in-australia-central-2--norway-west-and-france-south"></a>Supporto per Automazione e State Configuration disponibili in Australia centrale 2, Norvegia occidentale e Francia meridionale

**Tipo:** Nuova funzionalità

Per altre informazioni, vedere [la pagina Residenza dei dati](https://azure.microsoft.com/global-infrastructure/data-residency/) selezionando l'area geografica per ogni area.

### <a name="new-scripts-added-for-installing-hybrid-worker-on-windows-and-linux"></a>Nuovi script aggiunti per l'installazione del ruolo di lavoro ibrido in Windows e Linux

**Tipo:** Nuova funzionalità

Sono stati aggiunti due nuovi script al [repository GitHub](https://github.com/azureautomation) Automazione di Azure che si indirizzano a uno degli scenari chiave di Automazione di Azure di configurazione di un ruolo di lavoro ibrido per runbook in un computer Windows o Linux. Lo script crea una nuova macchina virtuale o ne usa una esistente, crea un'area di lavoro Log Analytics se necessario, installa l'agente di Log Analytics per Windows o l'agente di Log Analytics per Linux e registra il computer nell'area di lavoro Log Analytics. Lo script di Windows è denominato **Create Automation Windows HybridWorker** e lo script Linux **è Create Automation Linux HybridWorker**.

### <a name="invoke-runbook-through-an-azure-resource-manager-template-webhook"></a>Richiamare il runbook tramite un webhook Azure Resource Manager modello

**Tipo:** Nuova funzionalità

Per altri dettagli, vedere Usare [un webhook](./automation-webhooks.md#use-a-webhook-from-an-arm-template) da un modello di Arm.

### <a name="azure-update-management-now-supports-centos-8x-red-hat-enterprise-linux-server-8x-and-suse-linux-enterprise-server-15"></a>Azure Gestione aggiornamenti ora supporta Centos 8.x, Red Hat Enterprise Linux Server 8.x e SUSE Linux Enterprise Server 15

**Tipo:** Nuova funzionalità

Per altri [dettagli, vedere l'elenco](./update-management/overview.md#supported-operating-systems) completo dei sistemi operativi Linux supportati.

### <a name="in-region-data-residency-support-for-brazil-south-and-south-east-asia"></a>Supporto della residenza dei dati nell'area geografica per brasile meridionale e sud Asia orientale 

**Tipo:** Nuova funzionalità

In tutte le aree, ad eccezione dell'Asia sud-orientale e del Brasile meridionale, i dati Automazione di Azure vengono archiviati in un'area diversa (area abbinata ad Azure) per fornire continuità aziendale e ripristino di emergenza (BCDR). Solo per le aree Brasile e Asia sud-orientale, vengono ora archiviati Automazione di Azure dati nella stessa area per soddisfare i requisiti di residenza dei dati per queste aree. Per [altri dettagli,](./automation-managing-data.md#geo-replication-in-azure-automation) vedere Replica geografica Automazione di Azure in microsoft Automazione di Azure.

## <a name="february-2021"></a>Febbraio 2021

### <a name="support-for-automation-and-state-configuration-declared-ga-in-japan-west"></a>Supporto per Automazione e State Configuration ga ga dichiarato in Giappone occidentale

**Tipo:** Nuova funzionalità

Account di automazione e State Configuration disponibilità nell'area Giappone occidentale. Per altre informazioni, vedere [annuncio](https://azure.microsoft.com/updates/azure-automation-in-japan-west-region/).

### <a name="introduced-custom-azure-policy-compliance-to-enforce-runbook-execution-on-hybrid-worker"></a>Introduzione della conformità Criteri di Azure per applicare l'esecuzione di runbook nel ruolo di lavoro ibrido

**Digitare :** Nuova funzionalità

È possibile usare la nuova regola Criteri di Azure di conformità per consentire la creazione di processi, webhook e pianificazioni dei processi solo nei gruppi di ruoli di lavoro ibridi.

### <a name="update-management-availability-in-east-us-france-central-and-north-europe-regions"></a>Gestione aggiornamenti disponibilità nelle aree Stati Uniti orientali, Francia centrale ed Europa settentrionale

**Tipo:** Nuova funzionalità

La Gestione aggiornamenti di automazione è disponibile nelle aree Stati Uniti orientali, Francia centrale ed Europa settentrionale. Vedere [Mapping di aree supportate](how-to/region-mappings.md) per gli aggiornamenti alla documentazione che riflettono questa modifica.

## <a name="january-2021"></a>Gennaio 2021

### <a name="support-for-automation-and-state-configuration-declared-ga-in-switzerland-west"></a>Supporto per Automazione e State Configuration dichiarato a livello di codice a livello di codice a livello di codice in Svizzera occidentale

**Tipo:** Nuova funzionalità

Account di automazione State Configuration disponibilità nell'area Svizzera occidentale. Per altre informazioni, leggere [l'annuncio](https://azure.microsoft.com/updates/azure-automation-in-switzerland-west-region/).

### <a name="added-python-3-script-to-import-module-with-multiple-dependencies"></a>Aggiunta dello script Python 3 per importare il modulo con più dipendenze

**Tipo:** Nuova funzionalità

Lo script è disponibile per il download dal [repository GitHub.](https://github.com/azureautomation/runbooks/blob/master/Utility/Python/import_py3package_from_pypi.py) 
 
### <a name="hybrid-runbook-worker-role-support-for-centos-8xrhel-8xsles-15"></a>Supporto del ruolo di lavoro ibrido per runbook per Centos 8.x/RHEL 8.x/SLES 15

**digitare.** Nuova funzionalità

La funzionalità ruolo di lavoro ibrido per runbook supporta le distribuzioni CentOS 8.x, REHL 8.x e SLES 15 solo per l'automazione dei processi nei thread di lavoro ibridi per runbook. Vedere [Sistemi operativi supportati per](automation-linux-hrw-install.md#supported-linux-operating-systems) gli aggiornamenti alla documentazione per riflettere queste modifiche.

### <a name="update-management-and-change-tracking-availability-in-australia-east-east-asia-west-us-and-central-us-regions"></a>Gestione aggiornamenti e Rilevamento modifiche disponibili in Australia orientale, Asia orientale, Stati Uniti occidentali e Stati Uniti centrali

**Tipo:** Nuova funzionalità

Account di automazione, Rilevamento modifiche e inventario e Gestione aggiornamenti sono disponibili in Australia orientale, Asia orientale, Stati Uniti occidentali e Stati Uniti centrali. 

### <a name="introduced-public-preview-of-python-3-runbooks-in-us-government-cloud"></a>Introduzione dell'anteprima pubblica dei runbook Python 3 nel cloud del governo degli Stati Uniti

**Digitare:** La nuova funzionalità Automazione di Azure il supporto dell'anteprima pubblica dell'esecuzione di runbook cloud e ibridi Python 3 nelle aree cloud del governo degli Stati Uniti. Per altre informazioni, vedere l'[annuncio](https://azure.microsoft.com/updates/azure-automation-python-3-public-preview/).

### <a name="azure-automation-runbooks-moved-from-technet-script-center-to-github"></a>Automazione di Azure runbook spostati da TechNet Script Center a GitHub

**Tipo:** Modifica pianificata

TechNet Script Center è in fase di ritiro e tutti i runbook ospitati nella raccolta di runbook sono stati spostati nell'organizzazione [GitHub di Automazione.](https://github.com/azureautomation) Per altre informazioni, vedere [Automazione di Azure runbook che si spostano in GitHub.](https://techcommunity.microsoft.com/t5/azure-governance-and-management/azure-automation-runbooks-moving-to-github/ba-p/2039337)

## <a name="december-2020"></a>Dicembre 2020

### <a name="azure-automation-and-update-management-private-link-ga"></a>Automazione di Azure e Gestione aggiornamenti collegamento privato ga

**Tipo:** Nuova funzionalità

Automazione di Azure e Gestione aggiornamenti è stato annunciato come GA per i cloud globali e per enti pubblici di Azure. Automazione di Azure abilitato il supporto collegamento privato per proteggere l'esecuzione di un runbook in un ruolo di lavoro ibrido, l'uso di Gestione aggiornamenti per applicare patch ai computer, richiamare un runbook tramite un webhook e usare il servizio State Configuration per evitare che i computer reclamino. Per altre informazioni, vedere Supporto [Automazione di Azure collegamento privato](https://azure.microsoft.com/updates/azure-automation-private-link)

### <a name="azure-automation-classified-as-grade-c-certified-on-accessibility"></a>Automazione di Azure classificata come di livello C certificata per l'accessibilità

**Tipo:** Nuova funzionalità

Le funzionalità di accessibilità dei prodotti Microsoft consentono alle agenzie di soddisfare i requisiti di accessibilità globali. Nella pagina [dell'annuncio](https://cloudblogs.microsoft.com/industry-blog/government/2018/09/11/accessibility-conformance-reports/) del blog cercare **Automazione di Azure** per leggere il report conformità accessibilità per il servizio Automazione.

### <a name="support-for-automation-and-state-configuration-ga-in-uae-north"></a>Supporto per Automazione e State Configuration ga in Emirati Arabi Uniti settentrionali

**Tipo:** Nuova funzionalità

Account di Automazione State Configuration disponibilità nell'Emirati Arabi Uniti settentrionali locale. Per altre informazioni, vedere [l'annuncio](https://azure.microsoft.com/updates/azure-automation-in-uae-north-region/).

### <a name="support-for-automation-and-state-configuration-ga-in-germany-west-central"></a>Supporto per Automazione e State Configuration ga in Germania centro-occidentale

**Tipo:** Nuova funzionalità

L'account di Automazione State Configuration disponibilità nell'area Germania occidentale. Per altre informazioni, vedere [l'annuncio](https://azure.microsoft.com/updates/azure-automation-in-germany-west-central-region/).

### <a name="dsc-support-for-oracle-6-and-7"></a>Supporto DSC per Oracle 6 e 7

**Tipo:** Nuova funzionalità

Gestire Oracle Linux 6 e 7 computer con Automazione State Configuration. Vedere [Distribuzioni Linux supportate per](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions) gli aggiornamenti alla documentazione per riflettere queste modifiche.

### <a name="public-preview-for-python3-runbooks-in-automation"></a>Anteprima pubblica per runbook Python3 in Automazione

**Tipo:** Nuova funzionalità

Automazione di Azure ora supporta l'esecuzione di runbook ibridi e cloud Python 3 in anteprima pubblica in tutte le aree del cloud globale di Azure. Per altri dettagli, vedere [annuncio](( https://azure.microsoft.com/updates/azure-automation-python-3-public-preview/) ).

## <a name="november-2020"></a>Novembre 2020

### <a name="dsc-support-for-ubuntu-1804"></a>Supporto DSC per Ubuntu 18.04

**Tipo:** Nuova funzionalità

Vedere [Distribuzioni Linux supportate](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions) per gli aggiornamenti alla documentazione che riflettono queste modifiche.

## <a name="october-2020"></a>Ottobre 2020

### <a name="support-for-automation-and-state-configuration-ga-in-switzerland-north"></a>Supporto per Automazione e State Configuration ga in Svizzera settentrionale

**Tipo:** Nuova funzionalità

Account di automazione e State Configuration disponibilità in Svizzera settentrionale. Per altre informazioni, vedere [annuncio](https://azure.microsoft.com/updates/azure-automation-in-switzerland-north-region/).

### <a name="support-for-automation-and-state-configuration-ga-in-brazil-south-east"></a>Supporto per Automazione e State Configuration ga in Brasile sud-orientale

**Tipo:** Nuova funzionalità

Account di automazione e State Configuration disponibilità in Brasile sud-orientale. Per altre informazioni, vedere [annuncio](https://azure.microsoft.com/updates/azure-automation-in-brazil-southeast-region/).

### <a name="update-management-availability-in-south-central-us"></a>Gestione aggiornamenti disponibilità negli Stati Uniti centro-meridionali

**Tipo:** Nuova funzionalità

Automazione di Azure mapping dell'area è stato aggiornato per supportare Gestione aggiornamenti funzionalità nell'area Stati Uniti centro-meridionali. Vedere [Mapping di aree supportate](how-to/region-mappings.md#supported-mappings) per gli aggiornamenti alla documentazione per riflettere questa modifica.

## <a name="september-2020"></a>Settembre 2020

### <a name="startstop-vms-during-off-hours-runbooks-updated-to-use-azure-az-modules"></a>Avvio/Arresto di macchine virtuali durante gli orari di minore attività runbook aggiornati per l'uso dei moduli Az di Azure

**Tipo:** Nuova funzionalità

I runbook di avvio/arresto della macchina virtuale sono stati aggiornati per usare i moduli Az al posto Azure Resource Manager moduli. Vedere [Avvio/Arresto di macchine virtuali durante gli orari di minore attività](automation-solution-vm-management.md) panoramica degli aggiornamenti alla documentazione per riflettere queste modifiche.

## <a name="august-2020"></a>Agosto 2020

### <a name="published-the-dsc-extension-to-support-azure-arc"></a>È stata pubblicata l'estensione DSC per supportare Azure Arc

**Tipo:** Nuova funzionalità

Usare Automazione di Azure State Configuration per archiviare in modo centralizzato le configurazioni e mantenere lo stato desiderato dei computer connessi ibridi abilitati Azure Arc server abilitati per l'estensione macchina virtuale DSC. Per altre informazioni, vedere Panoramica [delle estensioni macchina virtuale dei server abilitati per Arc.](../azure-arc/servers/manage-vm-extensions.md)

### <a name="july-2020"></a>Luglio 2020

### <a name="introduced-public-preview-of-private-link-support-in-automation"></a>Introduzione dell'anteprima pubblica del supporto dei collegamenti privati in Automazione

**Tipo:** Nuova funzionalità

Usare collegamento privato di Azure per connettere in modo sicuro le reti virtuali Automazione di Azure usando endpoint privati. Per altre informazioni, leggere [l'annuncio](https://azure.microsoft.com/updates/public-preview-private-link-azure-automation-is-now-available/).

### <a name="hybrid-runbook-worker-support-for-windows-server-2008-r2"></a>Supporto del ruolo di lavoro ibrido per runbook per Windows Server 2008 R2

**Tipo:** Nuova funzionalità

Automation Hybrid Runbook Worker supporta il sistema operativo Windows Server 2008 R2. Vedere [Sistemi operativi supportati per](automation-windows-hrw-install.md#supported-windows-operating-system) gli aggiornamenti alla documentazione per riflettere queste modifiche.

### <a name="update-management-support-for-windows-server-2008-r2"></a>Gestione aggiornamenti per Windows Server 2008 R2

**Tipo:** Nuova funzionalità

Gestione aggiornamenti supporta la valutazione e l'applicazione di patch al sistema operativo Windows Server 2008 R2. Vedere [Sistemi operativi supportati per](update-management/overview.md#clients) gli aggiornamenti alla documentazione per riflettere queste modifiche.

### <a name="automation-diagnostic-logs-schema-update"></a>Aggiornamento dello schema dei log di diagnostica di Automazione

**Tipo:** Nuova funzionalità

Modifica dello schema dei Automazione di Azure log nel servizio Log Analytics. Per altre informazioni, vedere [Inoltrare i Automazione di Azure processo a Monitoraggio di Azure log.](automation-manage-send-joblogs-log-analytics.md#filter-job-status-output-converted-into-a-json-object)

### <a name="azure-lighthouse-supports-automation-update-management"></a>Azure Lighthouse supporta l'automazione Gestione aggiornamenti

**Tipo:** Nuova funzionalità

Azure Lighthouse la gestione delle risorse delegata con Gestione aggiornamenti per i provider di servizi e i clienti. Altre informazioni sono disponibili [qui](https://azure.microsoft.com/blog/how-azure-lighthouse-enables-management-at-scale-for-service-providers/).

## <a name="june-2020"></a>Giugno 2020

### <a name="automation-and-update-management-availability-in-the-us-gov-arizona-region"></a>Automazione e Gestione aggiornamenti disponibilità nell'area US Gov Arizona dati

**Tipo:** Nuova funzionalità

Gli account di automazione Gestione aggiornamenti sono disponibili in US Gov Arizona. Per altre informazioni, vedere [annuncio](https://azure.microsoft.com/updates/azure-automation-generally-available-in-usgov-arizona-region/).

### <a name="hybrid-runbook-worker-onboarding-script-updated-to-use-az-modules"></a>Script di onboarding del ruolo di lavoro ibrido per runbook aggiornato per l'uso dei moduli Az

**Tipo:** Nuova funzionalità

Il New-OnPremiseHybridWorker runbook è stato aggiornato per supportare i moduli Az. Per altre informazioni, vedere il pacchetto nella [PowerShell Gallery](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/1.7).

### <a name="update-management-availability-in-china-east-2"></a>Gestione aggiornamenti disponibilità in Cina orientale 2

**Tipo:** Nuova funzionalità

Automazione di Azure mapping dell'area è stato aggiornato per supportare Gestione aggiornamenti funzionalità nell Cina orientale 2 area. Vedere [Mapping di aree supportate](how-to/region-mappings.md#supported-mappings) per gli aggiornamenti alla documentazione per riflettere questa modifica.

## <a name="may-2020"></a>Maggio 2020

### <a name="updated-automation-service-dns-records-from-region-specific-to-automation-account-specific-urls"></a>Aggiornamento dei record DNS del servizio Di automazione da URL specifici dell'area a URL specifici dell'account di Automazione

**Tipo:** Nuova funzionalità

Automazione di Azure i record DNS sono stati aggiornati per supportare i collegamenti privati. Per altre informazioni, leggere [l'annuncio](https://azure.microsoft.com/updates/azure-automation-updateddns-records/).

### <a name="added-capability-to-keep-automation-runbooks-and-dsc-scripts-encrypted-by-default"></a>Aggiunta della funzionalità per mantenere crittografati i runbook di Automazione e gli script DSC per impostazione predefinita

**Tipo:** Nuova funzionalità

Oltre a migliorare la sicurezza degli asset, i runbook e gli script DSC vengono crittografati per migliorare Automazione di Azure sicurezza.

## <a name="april-2020"></a>Aprile 2020

### <a name="retirement-of-the-automation-watcher-task"></a>Ritiro dell'attività Watcher di Automazione

**Tipo:** Modifica pianificata

App per la logica di Azure è ora il modo consigliato e supportato per monitorare gli eventi, pianificare attività ricorrenti e azioni trigger. Non saranno necessari altri investimenti nella funzionalità dell'attività Watcher. Per altre informazioni, vedere [Pianificare ed eseguire attività automatiche ricorrenti con App per la logica.](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)

## <a name="march-2020"></a>Marzo 2020

### <a name="support-for-impact-level-5-il5-compute-isolation-in-azure-commercial-and-government-cloud"></a>Supporto per l'isolamento del calcolo del livello di impatto 5 (IL5) nel cloud commerciale e per enti pubblici di Azure

**Tipo:**

Automazione di Azure ruolo di lavoro ibrido per runbook può essere usato in Azure per enti pubblici per supportare carichi di lavoro di livello 5 di impatto. Per altre informazioni, vedere la [documentazione](automation-hybrid-runbook-worker.md#support-for-impact-level-5-il5)di .

## <a name="february-2020"></a>Febbraio 2020

### <a name="introduced-support-for-azure-virtual-network-service-tags"></a>È stato introdotto il supporto per i tag del servizio di rete virtuale di Azure

**Tipo:** Nuova funzionalità

Il supporto dell'automazione dei tag di servizio consente o nega il traffico per il servizio di Automazione, per un subset di scenari. Per altre informazioni, vedere la [documentazione](automation-hybrid-runbook-worker.md#service-tags).

### <a name="enable-tls-12-support-for-azure-automation-service"></a>Abilitare il supporto tls 1.2 per Automazione di Azure servizio

**Tipo:** Modifica pianificata

Automazione di Azure supporta completamente TLS 1.2 e tutte le chiamate client (tramite webhook, nodi DSC e ruolo di lavoro ibrido). TLS 1.1 e TLS 1.0 sono ancora supportati per la compatibilità con le versioni precedenti dei client fino a quando i clienti non standardizzano ed esere completamente la migrazione a TLS 1.2.

## <a name="january-2020"></a>Gennaio 2020

### <a name="introduced-public-preview-of-customer-managed-keys-for-azure-automation"></a>Introduzione dell'anteprima pubblica delle chiavi gestite dal cliente per Automazione di Azure

**Tipo:** Nuova funzionalità

I clienti possono gestire e proteggere la crittografia Automazione di Azure asset usando le proprie chiavi gestite. Per altre informazioni, vedere [Uso delle chiavi gestite dal cliente.](automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

### <a name="retirement-of-azure-service-management-asm-rest-apis-for-azure-automation"></a>Ritiro delle API REST di Gestione dei servizi di Azure (ASM) per Automazione di Azure

**Digitare:** Ritirarsi

Le API REST di Gestione dei servizi di Azure (ASM) per Automazione di Azure verranno ritirate e non saranno più supportate dopo il 30 gennaio 2020. Per altre informazioni, vedere [l'annuncio](https://azure.microsoft.com/updates/azure-automation-service-management-rest-apis-are-being-retired-april-30-2019/).

## <a name="next-steps"></a>Passaggi successivi

Per contribuire alla documentazione di Automazione di Azure, vedere la Guida per i collaboratori [di Docs.](/contribute/)
