---
title: Baseline della sicurezza di Azure per criteri di Azure
description: La linea di base di sicurezza dei criteri di Azure fornisce linee guida procedurali e risorse per l'implementazione delle raccomandazioni di sicurezza specificate nel benchmark di sicurezza di Azure.
author: msmbaldwin
ms.service: azure-policy
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 6e8bb4cf715c6cb8d0729399c1985376de18687b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561288"
---
# <a name="azure-security-baseline-for-azure-policy"></a>Baseline della sicurezza di Azure per criteri di Azure

Questa linea di base di sicurezza applica le linee guida del [benchmark di sicurezza di Azure](../../../security/benchmarks/overview.md) ai criteri di Azure. Azure Security Benchmark offre consigli sulla protezione delle soluzioni cloud in Azure. Il contenuto viene raggruppato in base ai **domini di conformità** e ai controlli di **sicurezza** definiti dal benchmark di sicurezza di Azure e alle linee guida correlate applicabili ai criteri di Azure. I **controlli** non applicabili ai criteri di Azure sono stati esclusi. Per informazioni sul mapping completo di criteri di Azure al benchmark di sicurezza di Azure, vedere il [file di mapping di base di sicurezza dei criteri](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)di Azure.

Per un mapping dei controlli benchmark di sicurezza di Azure alle definizioni di criteri predefinite tramite l'iniziativa predefinita, vedere [conformità normativa: benchmark di sicurezza di Azure](../samples/azure-security-benchmark.md).

I criteri di Azure usano il termine _proprietario_ al posto della _responsabilità_. Per informazioni dettagliate sulla _Proprietà_, vedere [definizioni dei criteri di criteri di Azure](./definition-structure.md#type) e [responsabilità condivisa nel cloud](../../../security/fundamentals/shared-responsibility.md).

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: registrazione e monitoraggio](../../../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: abilitare la registrazione di controllo per le risorse di Azure

**Linee guida**: i criteri di Azure usano i log attività, abilitati automaticamente, per includere origine evento, data, utente, timestamp, indirizzi di origine, indirizzi di destinazione e altri elementi utili.

- [Come raccogliere i log e le metriche della piattaforma con monitoraggio di Azure](../../../azure-monitor/essentials/diagnostic-settings.md)

- [Informazioni sulla registrazione e sui diversi tipi di log in Azure](../../../azure-monitor/essentials/platform-logs-overview.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="identity-and-access-control"></a>Gestione delle identità e controllo di accesso

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: identità e controllo di accesso](../../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: usare account amministrativi dedicati

**Indicazioni**: creare procedure operative standard per l'utilizzo di account amministrativi dedicati. Usare la gestione delle identità e degli accessi del Centro sicurezza di Azure per monitorare il numero di account amministrativi. È anche possibile abilitare una soluzione di accesso just-in-time/just-enough usando [Azure Active Directory (Azure ad) Privileged Identity Management](../../../active-directory/privileged-identity-management/pim-configure.md) ruoli con privilegi o [Azure Resource Manager](../../../azure-resource-manager/management/overview.md).

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: il [benchmark di sicurezza di Azure](/azure/governance/policy/samples/azure-security-benchmark) è l'iniziativa di criteri predefinita per il Centro sicurezza ed è la base per le [raccomandazioni del Centro sicurezza](/azure/security-center/security-center-recommendations). Le definizioni di criteri di Azure correlate a questo controllo sono abilitate automaticamente dal centro sicurezza. Gli avvisi correlati a questo controllo possono richiedere un piano di [Azure Defender](/azure/security-center/azure-defender) per i servizi correlati.

**Definizioni predefinite di criteri di Azure-Microsoft. GuestConfiguration**:

[!INCLUDE [Resource Policy for Microsoft.GuestConfiguration 3.3](../../../../includes/policy/standards/asb/rp-controls/microsoft.guestconfiguration-3-3.md)]

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: usare workstation sicure gestite da Azure per le attività amministrative

**Linee guida**: usare workstation con accesso con privilegi (Paw) con l'autenticazione a più fattori configurata per l'accesso e la configurazione delle risorse di Azure.

- [Informazioni sulle workstation con accesso con privilegi](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Come abilitare l'autenticazione a più fattori in Azure](../../../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="data-protection"></a>Protezione dei dati

*Per altre informazioni, vedere [Azure Security Benchmark: Protezione dei dati](../../../security/benchmarks/security-control-data-protection.md).*

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: usare il controllo degli accessi in base al ruolo di Azure per controllare l'accesso alle risorse 

**Linee guida**: usare il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per controllare l'accesso ai criteri di Azure.

- [Autorizzazioni del controllo degli accessi in base al ruolo di Azure in Criteri di Azure](../overview.md#azure-rbac-permissions-in-azure-policy)

- [Come configurare RBAC di Azure](../../../role-based-access-control/role-assignments-portal.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: registrare e inviare avvisi per le modifiche alle risorse di Azure critiche

**Linee guida**: usare monitoraggio di Azure con i log attività per creare avvisi per le modifiche apportate ai criteri di Azure.

- [Come creare avvisi per gli eventi del log attività di Azure](../../../azure-monitor/alerts/alerts-activity-log.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="inventory-and-asset-management"></a>Gestione asset e inventario

*Per altre informazioni, vedere [benchmark di sicurezza di Azure: inventario e gestione delle risorse](../../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="62-maintain-asset-metadata"></a>6.2: gestire i metadati degli asset

**Indicazioni**: applicare i tag alle risorse di Azure che contengono metadati per organizzarle in modo logico in categorie in una tassonomia. Usare l'effetto _modifica_ criteri di Azure per creare report e applicare la conformità e la governance dei tag coerenti.

- [Esercitazione: creare e gestire criteri](../tutorials/create-and-manage.md)

- [Esercitazione: Gestire la governance dei tag](../tutorials/govern-tags.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: definire e gestire l'inventario delle risorse di Azure approvate

**Linee guida**: creare un inventario delle definizioni dei criteri approvate e delle assegnazioni di criteri in base alle esigenze dell'organizzazione.

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: monitorare la presenza di risorse di Azure non approvate

**Linee guida**: usare i criteri di Azure per applicare restrizioni al tipo di risorse che è possibile creare nelle sottoscrizioni.

- [Come configurare e gestire Criteri di Azure](../tutorials/create-and-manage.md)

**Responsabilità**: Customer

**Monitoraggio del Centro sicurezza di Azure**: nessuno

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Panoramica di Azure Security Benchmark V2](../../../security/benchmarks/overview.md)
- Altre informazioni su [Baseline di sicurezza di Azure](../../../security/benchmarks/security-baselines-overview.md)