---
title: Azure Lighthouse in scenari ISV
description: Le funzionalità di Azure Lighthouse possono essere usate dagli ISV per una maggiore flessibilità con le offerte dei clienti.
ms.date: 12/18/2020
ms.topic: conceptual
ms.openlocfilehash: d6a12a51d360ad236563b871dbd94cc442ade434
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2020
ms.locfileid: "97696355"
---
# <a name="azure-lighthouse-in-isv-scenarios"></a>Azure Lighthouse in scenari ISV

Uno scenario comune per [Azure Lighthouse](../overview.md) è un provider di servizi che gestisce le risorse nei tenant Azure Active Directory (Azure ad) dei clienti. Le funzionalità di Azure Lighthouse possono anche essere usate da fornitori di software indipendenti (ISV) usando offerte basate su SaaS con i clienti. Azure Lighthouse può essere particolarmente utile per gli ISV che offrono servizi gestiti o supporto che richiedono l'accesso all'ambito della sottoscrizione.

## <a name="managed-service-offers-in-azure-marketplace"></a>Offerte di servizi gestiti in Azure Marketplace

In qualità di ISV, è possibile che siano già state pubblicate soluzioni in Azure Marketplace. Se si offrono servizi gestiti ai clienti, è possibile farlo pubblicando un'offerta di servizio gestito. Queste offerte semplificano il processo di onboarding e rendono i servizi più scalabili per il numero necessario di clienti. Azure Lighthouse supporta un'ampia gamma di [attività di gestione e scenari](cross-tenant-management-experience.md#enhanced-services-and-scenarios) che possono essere usati per fornire valore ai clienti.

Per altre informazioni, vedere [pubblicare un'offerta di servizio gestito in Azure Marketplace](../how-to/publish-managed-services-offers.md).

## <a name="using-azure-lighthouse-with-azure-managed-applications"></a>Uso di Azure Lighthouse con le applicazioni gestite di Azure

[Le applicazioni gestite di Azure](../../azure-resource-manager/managed-applications/overview.md) rappresentano un altro modo in cui gli ISV possono fornire servizi ai clienti. È possibile usare Azure Lighthouse insieme alle applicazioni gestite di Azure per abilitare scenari avanzati.

Per altre informazioni, vedere [Azure Lighthouse e applicazioni gestite di Azure](managed-applications.md).

## <a name="saas-based-multi-tenant-offerings"></a>Offerte multi-tenant basate su SaaS

Uno scenario aggiuntivo è il punto in cui ISV ospita le risorse in una sottoscrizione nel proprio tenant, quindi usa Azure Lighthouse per consentire ai clienti di accedere a tali risorse. Il cliente può quindi accedere al proprio tenant e accedere a queste risorse in base alle esigenze. Gli ISV mantengono il proprio indirizzo IP nel proprio tenant e possono usare i propri piani di supporto per generare ticket correlati alla soluzione ospitata nel tenant, anziché usare il piano del cliente. Poiché le risorse si trovano nel tenant dell'ISV, tutte le azioni possono essere eseguite direttamente dall'ISV, ad esempio l'accesso alle macchine virtuali, l'installazione di app e l'esecuzione di attività di manutenzione.

In questo scenario, agli utenti del tenant del cliente viene essenzialmente concesso l'accesso come tenant di gestione, anche se il cliente non gestisce le risorse dell'ISV. Poiché accedono direttamente al tenant dell'ISV, è importante concedere solo le autorizzazioni minime necessarie, in modo che i clienti non possano apportare modifiche inavvertitamente alla soluzione o ad altre risorse ISV.

Per abilitare questa architettura, l'ISV deve ottenere l'ID oggetto per un gruppo di utenti nel tenant Azure AD del cliente, insieme all'ID tenant. Il ISV compila quindi un modello ARM che concede a questo gruppo di utenti le autorizzazioni appropriate e lo [distribuisce nella sottoscrizione dell'ISV](../how-to/onboard-customer.md) che contiene le risorse a cui il cliente accederà.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle [esperienze di gestione tra tenant](cross-tenant-management-experience.md).
- Informazioni sulla [gestione risorse delegate di Azure](azure-delegated-resource-management.md).