---
title: Connettere i dati di Microsoft Defender for Identity (in precedenza Azure ATP) a Azure Sentinel| Microsoft Docs
description: Informazioni su come trasmettere i log da Microsoft Defender for Identity (in precedenza Azure Advanced Threat Protection) (ATP) a Azure Sentinel con un solo clic.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 7a26091d4985b6fdb17120c6fd70476a750c94a9
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714128"
---
# <a name="connect-data-from-microsoft-defender-for-identity-formerly-azure-advanced-threat-protection"></a>Connettere i dati da Microsoft Defender per l'identità (in precedenza Azure Advanced Threat Protection)

> [!IMPORTANT]
> Microsoft Defender for Identity Data Connector in Azure Sentinel è attualmente in anteprima pubblica.
> Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questo articolo descrive come trasmettere gli avvisi di sicurezza da [Microsoft Defender per l'identità](/azure-advanced-threat-protection/what-is-atp) Azure Sentinel. 

Per inoltrare gli avvisi di integrità oltre agli avvisi di sicurezza, integrare Microsoft Defender per l'identità con un server Syslog. Per altre informazioni, vedere la [documentazione di Microsoft Defender per l'identità.](/defender-for-identity/setting-syslog) 

## <a name="prerequisites"></a>Prerequisiti

- Utente con autorizzazioni di amministratore globale o amministratore della sicurezza
- È necessario essere un cliente di anteprima di Microsoft Defender per l'identità e abilitare l'integrazione tra Microsoft Defender per l'identità e Microsoft Cloud App Security. Per altre informazioni, vedere [Microsoft Defender for Identity Integration.](/cloud-app-security/mdi-integration)

## <a name="connect-to-microsoft-defender-for-identity"></a>Connettersi a Microsoft Defender per l'identità

Assicurarsi che la versione di anteprima di Microsoft Defender per l'identità sia [abilitata nella rete.](/azure-advanced-threat-protection/install-atp-step1)
Se Microsoft Defender for Identity viene distribuito e i dati vengono inseriti, gli avvisi sospetti possono essere trasmessi facilmente Azure Sentinel. L'avvio dello streaming degli avvisi in Azure Sentinel può richiedere fino a 24 ore.


1. Per connettere Microsoft Defender for Identity a Azure Sentinel, è prima necessario abilitare l'integrazione tra Microsoft Defender per l'identità e Microsoft Cloud App Security. Per informazioni su come eseguire questa operazione, vedere [Integrazione di Microsoft Defender per l'identità.](/cloud-app-security/mdi-integration)

1. In Azure Sentinel selezionare **Connettori dati e** quindi fare clic sul riquadro **Microsoft Defender for Identity (anteprima).**

1. È possibile scegliere se si vuole che gli avvisi di Microsoft Defender per l'identità generino automaticamente eventi imprevisti Azure Sentinel automaticamente. In **Create incidents** (Crea eventi imprevisti) selezionare **Enable** (Abilita) per abilitare la regola di analisi predefinita che crea automaticamente gli eventi imprevisti dagli avvisi generati nel servizio di sicurezza connesso. È quindi possibile modificare questa regola in **Analisi** e quindi **Active rules** (Regole attive).

1. Fare clic su **Connetti**.

1. Per usare lo schema pertinente in Log Analytics per gli avvisi di Microsoft Defender per le identità, cercare **SecurityAlert.**

> [!NOTE]
> Se le dimensioni degli avvisi sono superiori a 30 KB, Azure Sentinel non visualizza più il campo Entità negli avvisi.

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere Microsoft Defender for Identity a Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce.](quickstart-get-visibility.md)
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
