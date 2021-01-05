---
title: Soluzione VMware di Azure di CloudSimple-escalation dei privilegi CloudSimple
description: Viene descritto come eseguire l'escalation delle autorizzazioni CloudSimple per eseguire funzioni amministrative nel cloud privato vCenter
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d481717a79856583d23f61107678d2ecd1af68d6
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2021
ms.locfileid: "97895728"
---
# <a name="escalate-cloudsimple-privileges-to-perform-administrative-functions-in-private-cloud-vcenter"></a>Escalation dei privilegi CloudSimple per eseguire funzioni amministrative nel cloud privato vCenter

L'approccio con privilegi CloudSimple è progettato per fornire agli utenti di vCenter i privilegi necessari per eseguire operazioni normali. In alcuni casi, un utente può richiedere privilegi aggiuntivi per eseguire un'attività specifica.  È possibile inoltrare i privilegi di un utente SSO vCenter per un periodo di tempo limitato.

I motivi per i privilegi di escalation possono includere quanto segue:

* Configurazione delle origini identità
* Gestione degli utenti
* Eliminazione del gruppo di porte distribuite
* Installazione di soluzioni vCenter (ad esempio, app di backup)
* Creazione degli account di servizio

> [!WARNING]
> Le azioni eseguite nello stato con privilegi escalated possono influire negativamente sul sistema e causare la mancata disponibilità del sistema. Eseguire solo le azioni necessarie durante il periodo di escalation.

Dal portale di CloudSimple, [inoltrare i privilegi](escalate-private-cloud-privileges.md) per l'utente locale di CloudOwner in vCenter SSO.  È possibile inoltrare il privilegio dell'utente remoto solo se è stato configurato un provider di identità aggiuntivo in vCenter.  L'escalation dei privilegi comporta l'aggiunta dell'utente selezionato al gruppo di amministratori predefiniti di vSphere.  Un solo utente può disporre di privilegi escalation.  Se è necessario inoltrare i privilegi di un altro utente, prima di tutto annullare l'escalation dei privilegi degli utenti correnti.

Gli utenti di altre origini di identità devono essere aggiunti come membri del gruppo CloudOwner.

> [!CAUTION]
> I nuovi utenti devono essere aggiunti solo a *cloud-Owner-Group*, *cloud-Global-cluster-admin-* Group, *cloud-Global-Storage-admin-Group*, *cloud-Global-Network-Admin-Group* o, *cloud-Global-VM-admin-Group*.  Gli utenti aggiunti al gruppo *Administrators* verranno rimossi automaticamente.  Solo gli account di servizio devono essere aggiunti al gruppo *Administrators* e gli account del servizio non devono essere usati per accedere all'interfaccia utente Web di vSphere.

Durante il periodo di escalation, CloudSimple usa il monitoraggio automatizzato con le notifiche di avviso associate per identificare eventuali modifiche accidentali all'ambiente.
