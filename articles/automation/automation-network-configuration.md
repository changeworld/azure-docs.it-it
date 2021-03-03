---
title: Dettagli della configurazione di rete di automazione di Azure
description: Questo articolo fornisce informazioni dettagliate sulle informazioni di rete necessarie per la configurazione dello stato di automazione di Azure, il ruolo di lavoro ibrido per Runbook di automazione di Azure, Gestione aggiornamenti e Rilevamento modifiche
ms.author: magoedte
ms.topic: conceptual
ms.date: 01/26/2021
ms.openlocfilehash: 36331e9c07926d4d3ffff136aefa2f9a77d47cb4
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101708884"
---
# <a name="azure-automation-network-configuration-details"></a>Dettagli della configurazione di rete di automazione di Azure

Questa pagina fornisce i dettagli di rete necessari per il ruolo di [lavoro ibrido per Runbook e la configurazione dello stato](#hybrid-runbook-worker-and-state-configuration)e per [Gestione aggiornamenti e rilevamento modifiche e](#update-management-and-change-tracking-and-inventory)per l'inventario.

## <a name="hybrid-runbook-worker-and-state-configuration"></a>Ruolo di lavoro ibrido per Runbook e configurazione dello stato

La porta e gli URL seguenti sono necessari per il ruolo di lavoro ibrido per Runbook e per la [configurazione dello stato di automazione](automation-dsc-overview.md) per la comunicazione con automazione di Azure.

* Porta: solo 443 necessari per l'accesso a Internet in uscita
* URL globale: `*.azure-automation.net`
* URL globale di US Gov Virginia: `*.azure-automation.us`
* Servizio Agent: `https://<workspaceId>.agentsvc.azure-automation.net`

### <a name="network-planning-for-hybrid-runbook-worker"></a>Pianificazione di rete per il ruolo di lavoro ibrido per Runbook

Per la connessione e la registrazione di un ruolo di lavoro ibrido per Runbook di sistema o utente in automazione di Azure, deve avere accesso al numero di porta e agli URL descritti in questa sezione. Il ruolo di lavoro deve inoltre avere accesso alle [porte e agli URL necessari per la connessione dell'agente di log Analytics](../azure-monitor/agents/agent-windows.md) all'area di lavoro log Analytics di monitoraggio di Azure.

Se di dispone di un account di Automazione di Azure definito per un'area specifica, è possibile limitare la comunicazione del ruolo di lavoro ibrido per runbook a tale data center regionale. Esaminare i [record DNS usati da automazione di Azure](how-to/automation-region-dns-records.md) per i record DNS necessari.

### <a name="configuration-of-private-networks-for-state-configuration"></a>Configurazione delle reti private per la configurazione dello stato

Se i nodi si trovano in una rete privata, è necessario specificare la porta e gli URL definiti in precedenza. Queste risorse forniscono la connettività di rete per il nodo gestito e consentono a DSC di comunicare con Automazione di Azure.

Se si usano risorse DSC che comunicano tra i nodi, ad esempio le [risorse WaitFor*](/powershell/scripting/dsc/reference/resources/windows/waitForAllResource), è anche necessario consentire il traffico tra i nodi. Per informazioni su questi requisiti di rete, vedere la documentazione relativa a ogni risorsa DSC.

Per informazioni sui requisiti dei client per TLS 1,2, vedere [tls 1,2 Enforcement for Azure Automation](automation-managing-data.md#tls-12-enforcement-for-azure-automation).

## <a name="update-management-and-change-tracking-and-inventory"></a>Gestione aggiornamenti e Rilevamento modifiche e inventario

Gli indirizzi in questa tabella sono necessari sia per Gestione aggiornamenti che per Rilevamento modifiche e per l'inventario. Anche il paragrafo che segue la tabella si applica a entrambi.

Per la comunicazione con questi indirizzi viene utilizzata la **porta 443**.

|Azure Public  |Azure Government  |
|---------|---------|
|\*.ods.opinsights.azure.com    | \*.ods.opinsights.azure.us         |
|\*.oms.opinsights.azure.com     | \*.oms.opinsights.azure.us        |
|\*.blob.core.windows.net | \*.blob.core.usgovcloudapi.net|
|\*.azure-automation.net | \*. azure-automation.us|

Quando si creano regole di sicurezza del gruppo di rete o si configura il firewall di Azure per consentire il traffico verso il servizio di automazione e l'area di lavoro Log Analytics, usare i [tag di servizio](../virtual-network/service-tags-overview.md#available-service-tags) **GuestAndHybridManagement** e **AzureMonitor**. Ciò semplifica la gestione continuativa delle regole di sicurezza di rete. Per connettersi al servizio di automazione dalle macchine virtuali di Azure in modo sicuro e privato, vedere [usare il collegamento privato di Azure](./how-to/private-link-security.md). Per ottenere il tag di servizio e le informazioni sull'intervallo correnti da includere come parte delle configurazioni del firewall locali, vedere [file JSON scaricabili](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulla [Panoramica di automazione gestione aggiornamenti](update-management\overview.md).
* Informazioni sul ruolo di [lavoro ibrido per Runbook](automation-hybrid-runbook-worker.md).
* Informazioni sulle [rilevamento modifiche e l'inventario](change-tracking\overview.md).
* Informazioni sulla [configurazione dello stato di automazione](automation-dsc-overview.md).