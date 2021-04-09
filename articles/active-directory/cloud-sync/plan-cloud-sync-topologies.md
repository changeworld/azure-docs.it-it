---
title: Topologie e scenari supportati per la sincronizzazione Cloud Azure AD Connect
description: Informazioni sulle diverse topologie locali e Azure Active Directory (Azure AD) che usano Azure AD Connect la sincronizzazione cloud.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fd14ed8d149cdc5296229c52ceb74afb2ce7b23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98613542"
---
# <a name="azure-ad-connect-cloud-sync-supported-topologies-and-scenarios"></a>Topologie e scenari supportati per la sincronizzazione Cloud Azure AD Connect
Questo articolo descrive diverse topologie locali e Azure Active Directory (Azure AD) che usano Azure AD Connect la sincronizzazione cloud. Questo articolo include solo le configurazioni e gli scenari supportati.

> [!IMPORTANT]
> Microsoft non supporta la modifica o la gestione di Azure AD Connect sincronizzazione cloud al di fuori delle configurazioni o delle azioni che sono formalmente documentate. Una di queste configurazioni o azioni può causare uno stato incoerente o non supportato della sincronizzazione Azure AD Connect Cloud. Di conseguenza, Microsoft non è in grado di fornire supporto tecnico per tali distribuzioni.

## <a name="things-to-remember-about-all-scenarios-and-topologies"></a>Aspetti da ricordare per tutti gli scenari e le topologie
Di seguito è riportato un elenco di informazioni da tenere presenti quando si seleziona una soluzione.

- Utenti e gruppi devono essere identificati in modo univoco in tutte le foreste
- La corrispondenza tra foreste non si verifica con la sincronizzazione cloud
- Un utente o un gruppo deve essere rappresentato solo una volta in tutte le foreste
- L'ancoraggio di origine per gli oggetti viene scelto automaticamente.  USA ms-DS-ConsistencyGuid se presente; in caso contrario, viene usato ObjectGUID.
- Non è possibile modificare l'attributo usato per l'ancoraggio di origine.

## <a name="single-forest-single-azure-ad-tenant"></a>Foresta singola, tenant singolo di Azure AD
![Diagramma che mostra la topologia per una singola foresta e un singolo tenant.](media/tutorial-single-forest/diagram-2.png)

La topologia più semplice è una singola foresta locale, con uno o più domini, e un singolo tenant Azure AD.  Per un esempio di questo scenario [, vedere Esercitazione: una singola foresta con un singolo tenant di Azure ad](tutorial-single-forest.md)


## <a name="multi-forest-single-azure-ad-tenant"></a>Tenant a più foreste, Single Azure AD
![Topologia per un multi-foresta e un tenant singolo](media/plan-cloud-provisioning-topologies/multi-forest-2.png)

Una topologia comune è rappresentata da più insiemi di strutture di Active Directory, con uno o più domini, e un singolo tenant Azure AD.  

## <a name="existing-forest-with-azure-ad-connect-new-forest-with-cloud-provisioning"></a>Foresta esistente con Azure AD Connect, nuova foresta con provisioning nel cloud
![Diagramma che mostra la topologia di una foresta esistente e una nuova foresta.](media/tutorial-existing-forest/existing-forest-new-forest-2.png)

Questo scenario è simile allo scenario a più foreste, tuttavia questo implica un ambiente di Azure AD Connect esistente e quindi l'introduzione di una nuova foresta con Azure AD Connect la sincronizzazione cloud.  Per un esempio di questo scenario [, vedere Esercitazione: una foresta esistente con un singolo tenant di Azure ad](tutorial-existing-forest.md)

## <a name="piloting-azure-ad-connect-cloud-sync-in-an-existing-hybrid-ad-forest"></a>Pilota di Azure AD Connect la sincronizzazione cloud in una foresta di Active Directory ibrida esistente
![Topologia per una singola foresta e un singolo tenant ](media/tutorial-migrate-aadc-aadccp/diagram-2.png) lo scenario pilota prevede l'esistenza di Azure ad Connect e Azure ad Connect la sincronizzazione cloud nella stessa foresta e l'ambito degli utenti e dei gruppi di conseguenza. Nota: un oggetto deve essere nell'ambito solo in uno degli strumenti. 

Per un esempio di questo scenario [, vedere Esercitazione: pilota Azure ad Connect Cloud Sync in una foresta di Active Directory sincronizzata esistente](tutorial-pilot-aadc-aadccp.md)



## <a name="next-steps"></a>Passaggi successivi 

- [Cos'è il provisioning?](what-is-provisioning.md)
- [Che cos'è Azure AD Connect sincronizzazione cloud?](what-is-cloud-sync.md)

