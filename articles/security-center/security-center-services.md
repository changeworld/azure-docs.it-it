---
title: Funzionalità del Centro sicurezza di Azure in base a sistema operativo, tipo di computer e cloud
description: Informazioni sulle funzionalità del Centro sicurezza di Azure disponibili in base al sistema operativo, al tipo e alla distribuzione cloud.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 02/16/2021
ms.author: memildin
ms.openlocfilehash: 070f35dc3efea4a3f05ec36cc3860fa589aa87e3
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102437911"
---
# <a name="feature-coverage-for-machines"></a>Copertura delle funzionalità per le macchine virtuali

Le due schede seguenti illustrano le funzionalità del Centro sicurezza di Azure disponibili per server e macchine virtuali Windows e Linux.

## <a name="supported-features-for-virtual-machines-and-servers"></a>Funzionalità supportate per server e macchine virtuali <a name="vm-server-features"></a>

### <a name="windows-machines"></a>[**Computer Windows**](#tab/features-windows)

|**Funzionalità**|**Macchine virtuali di Azure**|**Set di scalabilità di macchine virtuali di Azure**|**Computer con abilitazione di Azure Arc**|**Azure Defender necessario**
|----|:----:|:----:|:----:|:----:|
|[Integrazione di Microsoft Defender per endpoint](security-center-wdatp.md)|✔</br>(nelle versioni supportate)|✔</br>(nelle versioni supportate)|✔|Sì|
|[Analisi comportamentale delle macchine virtuali (e avvisi di sicurezza)](alerts-reference.md)|✔|✔|✔|Sì|
|[Avvisi di sicurezza senza file](alerts-reference.md#alerts-windows)|✔|✔|✔|Sì|
|[Avvisi di sicurezza basati sulla rete](other-threat-protections.md#network-layer)|✔|✔|-|Sì|
|[Accesso JIT alle macchine virtuali](security-center-just-in-time.md)|✔|-|-|Sì|
|[Valutazione della vulnerabilità nativa](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|✔|Sì|
|[Monitoraggio dell'integrità dei file](security-center-file-integrity-monitoring.md)|✔|✔|✔|Sì|
|[Controlli delle applicazioni adattivi](security-center-adaptive-application.md)|✔|-|✔|Sì|
|[Mappa di rete](security-center-network-recommendations.md#network-map)|✔|✔|-|Sì|
|[Protezione adattiva della rete](security-center-adaptive-network-hardening.md)|✔|-|-|Sì|
|[Dashboard e report di conformità alle normative](security-center-compliance-dashboard.md)|✔|✔|✔|Sì|
|Raccomandazioni e protezione dalle minacce nei contenitori IaaS ospitati da Docker|-|-|-|Sì|
|Valutazione delle patch del sistema operativo mancanti|✔|✔|✔|Azure: No<br><br>Abilitazione di Arc: Sì|
|Valutazione degli errori di configurazione della sicurezza|✔|✔|✔|Azure: No<br><br>Abilitazione di Arc: Sì|
|[Valutazione della protezione endpoint](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Azure: No<br><br>Abilitazione di Arc: Sì|
|Valutazione della crittografia dischi|✔</br>(per gli [scenari supportati](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios))|✔|-|No|
|Valutazione della vulnerabilità di terze parti|✔|-|✔|No|
|[Valutazione della sicurezza di rete](security-center-network-recommendations.md)|✔|✔|-|No|


### <a name="linux-machines"></a>[**Computer Linux**](#tab/features-linux)

|**Funzionalità**|**Macchine virtuali di Azure**|**Set di scalabilità di macchine virtuali di Azure**|**Computer con abilitazione di Azure Arc**|**Azure Defender necessario**
|----|:----:|:----:|:----:|:----:|
|[Integrazione di Microsoft Defender per endpoint](security-center-wdatp.md)|-|-|-|-|
|[Analisi comportamentale delle macchine virtuali (e avvisi di sicurezza)](./azure-defender.md)|✔</br>(nelle versioni supportate)|✔</br>(nelle versioni supportate)|✔|Sì|
|[Avvisi di sicurezza senza file](alerts-reference.md#alerts-windows)|-|-|-|Sì|
|[Avvisi di sicurezza basati sulla rete](other-threat-protections.md#network-layer)|✔|✔|-|Sì|
|[Accesso JIT alle macchine virtuali](security-center-just-in-time.md)|✔|-|-|Sì|
|[Valutazione della vulnerabilità nativa](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|✔|Sì|
|[Monitoraggio dell'integrità dei file](security-center-file-integrity-monitoring.md)|✔|✔|✔|Sì|
|[Controlli delle applicazioni adattivi](security-center-adaptive-application.md)|✔|-|✔|Sì|
|[Mappa di rete](security-center-network-recommendations.md#network-map)|✔|✔|-|Sì|
|[Protezione adattiva della rete](security-center-adaptive-network-hardening.md)|✔|-|-|Sì|
|[Dashboard e report di conformità alle normative](security-center-compliance-dashboard.md)|✔|✔|✔|Sì|
|Raccomandazioni e protezione dalle minacce nei contenitori IaaS ospitati da Docker|✔|✔|✔|Sì|
|Valutazione delle patch del sistema operativo mancanti|✔|✔|✔|Azure: No<br><br>Abilitazione di Arc: Sì|
|Valutazione degli errori di configurazione della sicurezza|✔|✔|✔|Azure: No<br><br>Abilitazione di Arc: Sì|
|[Valutazione della protezione endpoint](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|No|
|Valutazione della crittografia dischi|✔</br>(per gli [scenari supportati](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios))|✔|-|No|
|Valutazione della vulnerabilità di terze parti|✔|-|✔|No|
|[Valutazione della sicurezza di rete](security-center-network-recommendations.md)|✔|✔|-|No|

--- 


> [!TIP]
>Per sperimentare le funzionalità disponibili solo con Azure Defender, è possibile registrarsi per ottenere una versione di valutazione valida 30 giorni. Per altre informazioni vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/security-center/).


## <a name="supported-endpoint-protection-solutions"></a>Soluzioni di protezione endpoint supportate <a name="endpoint-supported"></a>

La tabella seguente contiene una matrice che indica:

 - Se è possibile usare Centro sicurezza di Azure per l'installazione ogni soluzione.
 - Quali soluzioni di protezione endpoint può fornire Centro sicurezza. Se viene individuata una delle soluzioni di protezione endpoint di questo elenco, il Centro sicurezza non consiglierà di installarne una.

Per informazioni sui casi in cui vengono generate raccomandazioni per ognuna di queste protezioni, vedere [Valutazione e raccomandazioni per la protezione endpoint](security-center-endpoint-protection.md).

| Endpoint Protection| Piattaforme | Installazione del Centro sicurezza | Individuazione del Centro sicurezza |
|------|------|-----|-----|
| Microsoft Defender Antivirus| Windows Server 2016 o versione successiva| No, predefinito nel sistema operativo| Sì |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2, 2012, 2008 R2 (vedere la nota seguente) | Tramite estensione | Sì |
| Trend Micro Deep Security | Famiglia Windows Server  | No | Sì |
| Symantec v12.1.1100+| Famiglia Windows Server  | No | Sì |
| McAfee v10+ | Famiglia Windows Server  | No | Sì |
| McAfee v10+ | Famiglia di server Linux  | No | Sì |
| Sophos V9+| Famiglia di server Linux  | No | Sì |

> [!NOTE]
> Per poter rilevare System Center Endpoint Protection (SCEP) in una macchina virtuale Windows Server 2008 R2, SCEP deve essere installato dopo PowerShell (versione 3.0 o successiva).



## <a name="feature-support-in-government-clouds"></a>Supporto delle funzionalità nei cloud per enti pubblici

| Servizio/Funzionalità | US Gov | Governo cinese |
|------|:----:|:----:|
|[Accesso JIT alla macchina virtuale](security-center-just-in-time.md) (1)|✔|✔|
|[Monitoraggio dell'integrità dei file](security-center-file-integrity-monitoring.md) (1)|✔|✔|
|[Controlli applicazioni adattivi](security-center-adaptive-application.md) (1)|✔|✔|
|[Protezione avanzata adattiva per la rete](security-center-adaptive-network-hardening.md) (1)|-|-|
|[Protezione avanzata dell'host Docker](harden-docker-hosts.md) (1)|✔|✔|
|[Valutazione della vulnerabilità integrata per le macchine virtuali](deploy-vulnerability-assessment-vm.md) (1)|-|-|
|[Microsoft Defender per endpoint](harden-docker-hosts.md) (1)|✔|-|
|[Connessione account AWS](quickstart-onboard-aws.md) (1)|-|-|
|[Connessione account GCP](quickstart-onboard-gcp.md) (1)|-|-|
|[Esportazione continua](continuous-export.md)|✔|✔|
|[Automazione del flusso di lavoro](workflow-automation.md)|✔|✔|
|[Regole di esenzione delle raccomandazioni](exempt-resource.md)|-|-|
|[Regole di eliminazione avvisi](alerts-suppression-rules.md)|✔|✔|
|[Notifiche tramite posta elettronica per gli avvisi di sicurezza](security-center-provide-security-contact-details.md)|✔|✔|
|[Inventario degli asset](asset-inventory.md)|✔|✔|
|[Azure Defender per il servizio app](defender-for-app-service-introduction.md)|-|-|
|[Azure Defender per Archiviazione](defender-for-storage-introduction.md)|✔|-|
|[Azure Defender per SQL](defender-for-sql-introduction.md)|✔|✔ (2)|
|[Azure Defender per Key Vault](defender-for-key-vault-introduction.md)|-|-|
|[Azure Defender per Resource Manager](defender-for-resource-manager-introduction.md)|-|-|
|[Azure Defender per DNS](defender-for-dns-introduction.md)|-|-|
|[Azure Defender per registri contenitori](defender-for-container-registries-introduction.md)|✔ (2)|✔ (2)|
|[Azure Defender per Kubernetes](defender-for-kubernetes-introduction.md)|✔|✔|
|[Protezione del carico di lavoro Kubernetes](kubernetes-workload-protections.md)|✔|✔|
|||

(1) richiede **Azure Defender per i server**

(2) Parziale


## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul modo in cui il [Centro sicurezza raccoglie i dati tramite l'agente di Log Analytics](security-center-enable-data-collection.md).
- Informazioni su come il [Centro sicurezza gestisce e protegge i dati](security-center-data-security.md).
- Informazioni sulle [piattaforme che supportano il Centro sicurezza](security-center-os-coverage.md).