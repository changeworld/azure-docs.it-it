---
title: Residenza dei dati
description: Residenza dei dati e informazioni sui server abilitati per Azure Arc.
ms.topic: reference
ms.date: 02/16/2021
ms.custom: references_regions
ms.openlocfilehash: d4764772473bbf2e5aafe2607a9462c9a6a15203
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100559499"
---
# <a name="azure-arc-enabled-servers-data-residency"></a>Server abilitati per Azure Arc: residenza dei dati

Questo articolo illustra il concetto di residenza dei dati e il modo in cui si applica ai server abilitati per Azure Arc.

I server abilitati per Azure Arc sono **[disponibili](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)** in **Stati Uniti, Europa, Regno Unito, Australia e Asia Pacifico**.

## <a name="data-residency"></a>Residenza dei dati

I server abilitati per Azure Arc archiviano le impostazioni di configurazione dell' [estensione delle macchine virtuali di Azure](manage-vm-extensions.md) , ovvero i valori delle proprietà, che l'estensione richiede specificare prima di provare a abilitare nel computer connesso. Ad esempio, quando si Abilita l'estensione Log Analytics VM, viene richiesto l'ID dell' **area di lavoro** log Analytics e la **chiave primaria**.

Vengono raccolte anche le informazioni sui metadati sul computer connesso. In particolare:

* Nome, tipo e versione del sistema operativo
* Nome computer
* Nome di dominio completo (FQDN) del computer
* Versione dell'agente Connected Machine
* Nome di dominio completo (FQDN) di Active Directory e DNS
* UUID (ID BIOS)
* Heartbeat agente computer connesso
* Versione dell'agente Connected Machine
* Chiave pubblica per identità gestita
* Stato di conformità dei criteri e dettagli (se si usano i criteri di configurazione Guest di criteri di Azure)

I server abilitati per Arc consentono di specificare l'area in cui verranno archiviati i dati. Microsoft può eseguire la replica in altre aree per la resilienza dei dati, ma Microsoft non replica né sposta i dati all'esterno dell'area geografica. Questi dati vengono archiviati nell'area geografica in cui è configurata la risorsa del computer Azure Arc. Ad esempio, se il computer è registrato con Arc nell'area Stati Uniti orientali, questi dati vengono archiviati nell'area Stati Uniti.

Per altre informazioni sul supporto per la resilienza e la conformità a livello di area, vedere [geografia di Azure](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="next-steps"></a>Passaggi successivi

Scopri di più sulla progettazione per la [resilienza di Azure](/azure/architecture/reliability/architect).
