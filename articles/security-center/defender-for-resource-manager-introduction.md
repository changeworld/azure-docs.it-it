---
title: Azure Defender per Resource Manager - Vantaggi e funzionalità
description: Informazioni sui vantaggi e sulle funzionalità di Azure Defender per Resource Manager
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 922189909da21aac65d1e2e898df58598868b929
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100590463"
---
# <a name="introduction-to-azure-defender-for-resource-manager"></a>Introduzione ad Azure Defender per Resource Manager

[Azure Resource Manager](../azure-resource-manager/management/overview.md) è il servizio di distribuzione e gestione di Azure. Fornisce un livello di gestione che consente di creare, aggiornate ed eliminare risorse nell'account Azure. È possibile usare funzionalità di gestione, come il controllo di accesso, i blocchi e i tag, per proteggere e organizzare le risorse dopo la distribuzione.

Il livello di gestione cloud è un servizio cruciale connesso a tutte le risorse cloud. Per questo motivo, è spesso un potenziale bersaglio degli utenti malintenzionati. Di conseguenza, si consiglia ai team addetti alle operazioni di sicurezza di monitorare attentamente il livello di gestione delle risorse. 

Azure Defender per Resource Manager monitora automaticamente le operazioni di gestione risorse nell'organizzazione, indipendentemente dal fatto che vengano eseguite tramite il portale di Azure, le API REST di Azure, l'interfaccia della riga di comando di Azure o altri client di programmazione di Azure. Azure Defender esegue analisi di sicurezza avanzate per rilevare le minacce e inviare avvisi sulle attività sospette.

## <a name="availability"></a>Disponibilità

|Aspetto|Dettagli|
|----|:----|
|Stato della versione:|Anteprima<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|Prezzi:|**Azure Defender per Resource Manager** è soggetto alle tariffe visualizzate nella [pagina dei prezzi](security-center-pricing.md)|
|Cloud:|![Sì](./media/icons/yes-icon.png) Cloud commerciali<br>![No](./media/icons/no-icon.png) Cloud nazionali/sovrani (US Gov, governo cinese, altri governi)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-resource-manager"></a>Quali sono i vantaggi di Azure Defender per Resource Manager?

Azure Defender per Resource Manager protegge da problemi quali:

- **Operazioni di gestione delle risorse sospette**, ad esempio operazioni da indirizzi IP sospetti, disabilitazione dell'antimalware e script sospetti in esecuzione nelle estensioni di VM
- **Uso di toolkit di sfruttamento** come Microburst o PowerZure
- **Spostamento laterale** dal livello di gestione di Azure al piano dati delle risorse di Azure

:::image type="content" source="media/defender-for-resource-manager-introduction/consistent-management-layer-with-defender.png" alt-text="Diagramma di panoramica di Azure Resource Manager":::

Per un elenco completo degli avvisi forniti da Azure Defender per Resource Manager, vedere [Avvisi per Resource Manager](alerts-reference.md#alerts-resourcemanager).


 ## <a name="how-to-investigate-alerts-from-azure-defender-for-resource-manager"></a>Come esaminare gli avvisi di Azure Defender per Resource Manager

Gli avvisi di sicurezza di Azure Defender per Resource Manager si basano sulle minacce rilevate tramite il monitoraggio delle operazioni di Azure Resource Manager. Azure Defender usa origini di log interne di Azure Resource Manager e il log attività di Azure, un log di piattaforma di Azure che fornisce informazioni dettagliate sugli eventi a livello di sottoscrizione.

Altre informazioni sul [log attività di Azure](../azure-monitor/essentials/activity-log.md).

Per esaminare gli avvisi di sicurezza di Azure Defender per Resource Manager:

1. Aprire il log attività di Azure.

    :::image type="content" source="media/defender-for-resource-manager-introduction/opening-azure-activity-log.png" alt-text="Come aprire il log attività di Azure":::

1. Filtrare gli eventi in base a:
    - La sottoscrizione indicata nell'avviso
    - L'intervallo di tempo dell'attività rilevata
    - L'account utente correlato (se pertinente)

1. Cercare attività sospette.

> [!TIP]
> Per un'esperienza di analisi più efficace e dettagliata, trasmettere i log attività di Azure ad Azure Sentinel come descritto in [Connettere dati dal log attività di Azure](../sentinel/connect-azure-activity.md).



## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono state fornite informazioni su Azure Defender per Resource Manager. Per i materiali correlati, vedere l'articolo seguente: 

- Gli avvisi di sicurezza potrebbero essere generati dal Centro sicurezza o essere ricevuti dal Centro sicurezza da prodotti di sicurezza diversi. Per esportare tutti questi avvisi in Azure Sentinel, in qualsiasi soluzione SIEM di terze parti o in qualsiasi altro strumento esterno, seguire le istruzioni riportate in [Esportazione degli avvisi in un sistema di informazioni di sicurezza e gestione degli eventi](continuous-export.md).

- > [!div class="nextstepaction"]
    > [Abilitare Azure Defender](security-center-pricing.md#enable-azure-defender)