---
title: Gestione risorse delegate di Azure
description: La gestione delle risorse delegate di Azure è una parte chiave della Azure Lighthouse, consentendo ai provider di servizi di gestire le risorse delegate su larga scala con agilità e precisione.
ms.date: 10/19/2020
ms.topic: conceptual
ms.openlocfilehash: 93a3de257fe88de4915eff3582ff38fc03ef380e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767764"
---
# <a name="azure-delegated-resource-management"></a>Gestione risorse delegate di Azure

La gestione delle risorse delegate di Azure è uno dei componenti chiave di [Azure Lighthouse](../overview.md). Con la gestione risorse delegate di Azure, i provider di servizi possono semplificare le esperienze di engagement e onboarding dei clienti, gestendo al contempo le risorse delegate su larga scala con agilità e precisione. I clienti mantengono il controllo su chi può accedere al tenant, a quali risorse è possibile accedere e quali azioni possono essere eseguite.

## <a name="what-is-azure-delegated-resource-management"></a>Informazioni sulla gestione risorse delegate di Azure

La gestione risorse delegate di Azure consente la proiezione logica delle risorse da un tenant a un altro. Questo consente agli utenti autorizzati di un tenant di Azure Active Directory (Azure AD) di eseguire operazioni di gestione su tenant di Azure AD diversi appartenenti ai clienti. I provider di servizi possono accedere al proprio tenant di Azure AD e avere l'autorizzazione per lavorare nelle sottoscrizioni e nei gruppi di risorse delegati dei clienti. In questo modo possono eseguire operazioni di gestione per conto dei clienti, senza dover accedere al tenant di ogni singolo cliente.

> [!TIP]
> La gestione risorse delegate di Azure può essere usata anche [all'interno di un'azienda con più tenant di Azure AD propri](enterprise.md) per semplificare la gestione tra tenant.

Con la gestione risorse delegate di Azure, gli utenti autorizzati possono lavorare direttamente nel contesto della sottoscrizione di un cliente senza avere un account nel tenant di tale cliente o essere comproprietario del tenant del cliente.

[L'esperienza di gestione tra tenant](cross-tenant-management-experience.md) consente di lavorare in modo più efficiente con i servizi di gestione di Azure come Criteri di Azure, Centro sicurezza di Azure e altro ancora. Tutte le attività del provider di servizi vengono rilevate nel log attività, che viene archiviato nel tenant del cliente e può essere visualizzato dagli utenti nel tenant di gestione. Ciò significa che gli utenti nella gestione e nel tenant gestito possono identificare facilmente l'utente associato a qualsiasi modifica.

È possibile [pubblicare il nuovo tipo di](../how-to/publish-managed-services-offers.md) offerta del servizio gestito Azure Marketplace per eseguire facilmente l'onboarder dei clienti Azure Lighthouse. In alternativa, è possibile [completare il processo di onboarding distribuendo i modelli di Azure Resource Manager](../how-to/onboard-customer.md).

## <a name="how-azure-delegated-resource-management-works"></a>Come funziona la gestione risorse delegate di Azure

Di seguito viene illustrato il funzionamento generale della gestione risorse delegate di Azure:

1. In primo luogo, identificare l'accesso (ruoli) che i gruppi, le entità servizio o gli utenti dovranno gestire le risorse di Azure del cliente. La definizione di accesso contiene l'ID tenant di gestione insieme alle identità **principalId** del tenant mappate ai valori [ **roleDefinition**](../../role-based-access-control/built-in-roles.md) predefiniti (Collaboratore, Collaboratore macchina virtuale, Lettore e così via).
2. È possibile specificare questo accesso e eseguire l'onboard del cliente per Azure Lighthouse in uno dei due modi seguenti:
   - [Pubblicare Azure Marketplace'offerta di servizio](../how-to/publish-managed-services-offers.md) gestito (privata o pubblica) che il cliente accetterà
   - [Distribuire un modello di Azure Resource Manager nel tenant del cliente](../how-to/onboard-customer.md) per una o più sottoscrizioni o gruppi di risorse specifici

3. Dopo l'onboarded del cliente, gli utenti autorizzati possono accedere al tenant di gestione ed eseguire attività nell'ambito del cliente specificato, in base all'accesso definito. I clienti possono esaminare le azioni del provider di servizi e avere la possibilità di rimuovere l'accesso, se necessario.

> [!NOTE]
> È possibile gestire le risorse delegate che si trovano in aree [diverse.](../../availability-zones/az-overview.md#regions) Tuttavia, la delega delle sottoscrizioni in un cloud nazionale e nel [cloud](../../active-directory/develop/authentication-national-cloud.md) pubblico di Azure o in due cloud nazionali separati non è supportata.

## <a name="support-for-azure-delegated-resource-management"></a>Supporto per la gestione risorse delegate di Azure

Per assistenza relativa alla gestione risorse delegate di Azure, è possibile aprire una richiesta di supporto nel portale di Azure. Per **Tipo di problema** scegliere **Tecnico**. Selezionare una sottoscrizione e quindi **Lighthouse** (in **Monitoraggio & Management**).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle [esperienze di gestione tra tenant](cross-tenant-management-experience.md).
- Informazioni sulle [offerte di servizi gestiti in Azure Marketplace](managed-services-offers.md).
