---
title: Baseline della sicurezza di Azure per Azure Resource Graph
description: La linea di base di sicurezza di Azure Resource Graph fornisce linee guida procedurali e risorse per l'implementazione delle raccomandazioni di sicurezza specificate nel benchmark di sicurezza di Azure.
author: msmbaldwin
ms.service: resource-graph
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: e5e01c8d1ac16e5e8be405660a0726796789e645
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738974"
---
# <a name="azure-security-baseline-for-azure-resource-graph"></a>Baseline della sicurezza di Azure per Azure Resource Graph

Questa linea di base di sicurezza applica le linee guida del [benchmark di sicurezza di Azure versione 1,0](../../../security/benchmarks/overview-v1.md) ad Azure Resource Graph. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure.
Il contenuto viene raggruppato in base ai **controlli di sicurezza** definiti dal benchmark di sicurezza di Azure e alle linee guida correlate applicabili al grafico delle risorse di Azure. I **controlli** non applicabili al grafico delle risorse di Azure sono stati esclusi.

 
Per informazioni su come il grafico delle risorse di Azure è completamente mappato al benchmark di sicurezza di Azure, vedere il file di mapping della linea di base di sicurezza di Azure [Resource Graph](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: identità e controllo di accesso](../../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: controllare e riconciliare regolarmente l'accesso utente

**Linee guida**: Azure Resource Graph fornisce l'accesso ai tipi di risorse e alle proprietà in base ai controlli degli accessi in base al ruolo di Azure (RBAC di Azure). Controllare e verificare l'accesso concesso alle entità di sicurezza (utenti, gruppi e account del servizio) a intervalli regolari per assicurarsi che le query restituiscano risultati per le risorse appropriate.

- [Autorizzazioni in Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview#permissions-in-azure-resource-graph)

- [Come usare le verifiche di accesso alle identità di Azure](../../../active-directory/governance/access-reviews-overview.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Protezione dei dati](../../../security/benchmarks/security-control-data-protection.md).*

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso alle risorse 

**Linee guida**: usare il controllo degli accessi in base al ruolo di Azure Per usare il grafico delle risorse di Azure, è necessario disporre anche dell'accesso appropriato alle risorse su cui si vuole eseguire la query. Questo accesso deve avere un ambito di sola lettura ed essere concesso solo al personale richiesto.

- [Autorizzazioni in Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview#permissions-in-azure-resource-graph)

- [Come configurare RBAC di Azure](../../../role-based-access-control/role-assignments-rest.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: inventario e gestione delle risorse](../../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: usare la soluzione automazione Asset Discovery

**Linee guida**: usare Azure Resource Graph per eseguire query e individuare tutte le risorse supportate nelle sottoscrizioni, nei gruppi di gestione e nei tenant. Assicurarsi di disporre delle autorizzazioni appropriate nel tenant e di poter enumerare tutte le sottoscrizioni di Azure e le risorse all'interno delle sottoscrizioni.

- [Come creare query con Azure Resource Graph](../first-query-portal.md)

- [Informazioni sul controllo degli accessi in base al ruolo di Azure](../../../role-based-access-control/overview.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definire e gestire l'inventario delle risorse di Azure approvate

**Linee guida**: creare un inventario delle risorse di Azure approvate e del software approvato per le risorse di calcolo in base alle esigenze dell'organizzazione. Usare Azure Resource Graph per eseguire una query per le risorse di Azure approvate e la cronologia delle modifiche (anteprima) per esaminare gli snapshot e vedere cosa è cambiato.

- [Eseguire query sulle risorse di Azure con il grafico risorse di Azure](../first-query-portal.md)

- [Ottenere le modifiche delle risorse](../how-to/get-resource-changes.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare la presenza di risorse di Azure non approvate

**Linee guida**: usare Azure Resource Graph per eseguire query e individuare le risorse nelle sottoscrizioni, nei gruppi di gestione e nei tenant. Assicurarsi che tutte le risorse di Azure nell'ambiente siano approvate.

- [Eseguire query sulle risorse di Azure con il grafico risorse di Azure](../first-query-portal.md)

- [Esempi: query Starter per il grafico delle risorse di Azure](../samples/starter.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Panoramica di Azure Security Benchmark V2](/azure/security/benchmarks/overview)
- Altre informazioni su [Baseline di sicurezza di Azure](/azure/security/benchmarks/security-baselines-overview)
