---
title: Piattaforme supportate dal Centro sicurezza di Azure | Microsoft Docs
description: Questo documento include un elenco di piattaforme supportate dal Centro sicurezza di Azure.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 03/31/2020
ms.author: memildin
ms.openlocfilehash: 88dc0760f320a99b0cbc99b7637dc34dd11dfecc
ms.sourcegitcommit: 33ac5cd254c33659f668a76a2e295fddcd5d194d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/14/2021
ms.locfileid: "103465447"
---
# <a name="supported-platforms"></a>Piattaforme supportate 

Questa pagina illustra le piattaforme e gli ambienti supportati dal Centro sicurezza di Azure.

## <a name="combinations-of-environments"></a>Combinazioni di ambienti <a name="vm-server"></a>

Il Centro sicurezza di Azure supporta macchine virtuali e server in diversi tipi di ambienti ibridi:

* Solo Azure
* Azure e in locale
* Azure e altri cloud
* Azure, altri cloud e in locale

Per un ambiente di Azure attivato in una sottoscrizione di Azure, il Centro sicurezza di Azure rileva automaticamente le risorse IaaS distribuite nella sottoscrizione.

## <a name="supported-operating-systems"></a>Sistemi operativi supportati

Il Centro sicurezza dipende dall' [agente log Analytics](../azure-monitor/agents/agents-overview.md#log-analytics-agent). Verificare che nei computer sia in esecuzione uno dei sistemi operativi supportati per questo agente, come descritto nelle pagine seguenti:

* [Agente di Log Analytics per i sistemi operativi Windows supportati](../azure-monitor/agents/agents-overview.md#supported-operating-systems)
* [Agente di Log Analytics per i sistemi operativi Linux supportati](../azure-monitor/agents/agents-overview.md#supported-operating-systems)

Assicurarsi inoltre che l'agente di Log Analytics sia [correttamente configurato per l'invio di dati al Centro sicurezza](security-center-enable-data-collection.md#manual-agent)

Per altre informazioni sulle specifiche funzionalità del Centro sicurezza disponibili in Windows e in Linux, vedere [Copertura delle funzionalità per i computer](security-center-services.md).

> [!NOTE]
> Anche se Azure Defender è progettato per proteggere i server, la maggior parte delle funzionalità di **Azure Defender per i server** sono supportate per i computer Windows 10. Una delle funzionalità attualmente non supportate è la [soluzione EDR integrata del Centro sicurezza: Microsoft Defender per endpoint](security-center-wdatp.md).

## <a name="managed-virtual-machine-services"></a>Servizi gestiti delle macchine virtuali <a name="virtual-machine"></a>

Nelle sottoscrizioni dei clienti vengono create anche macchine virtuali come parte di alcuni servizi gestiti di Azure, come Azure Kubernetes, Azure Databricks e altri ancora. Il Centro sicurezza individua anche queste macchine virtuali e, se è disponibile un sistema operativo supportato, è possibile installare e configurare l'agente di Log Analytics.

## <a name="cloud-services"></a>Servizi cloud <a name="cloud-services"></a>

Sono supportate anche le macchine virtuali in esecuzione in un servizio cloud. Vengono monitorati solo i ruoli di lavoro e Web dei servizi cloud in esecuzione in slot di produzione. Per altre informazioni sui servizi cloud, vedere [Panoramica di Servizi Cloud di Azure](../cloud-services/cloud-services-choose-me.md).

È supportata anche la protezione per le macchine virtuali che risiedono nell'hub Azure Stack. Per altre informazioni sull'integrazione del Centro sicurezza con Azure Stack Hub, vedere [onboarding the Azure stack Hub Virtual Machines to Security Center](quickstart-onboard-machines.md?pivots=azure-portal#onboard-your-azure-stack-hub-vms). 

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul modo in cui il [Centro sicurezza raccoglie i dati tramite l'agente di Log Analytics](security-center-enable-data-collection.md).
- Informazioni sul modo in cui il [Centro sicurezza gestisce e salvaguarda i dati](security-center-data-security.md).
- Leggere le informazioni su come [pianificare e progettare l'adozione del Centro sicurezza di Azure](security-center-planning-and-operations-guide.md).