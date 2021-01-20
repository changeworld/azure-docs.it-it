---
title: Autorizzazioni catalogo (anteprima)
description: Questo articolo fornisce una panoramica su come configurare Role-Based controllo di accesso (RBAC) in Azure
author: yarong
ms.author: yarong
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: b351be1e7212dc9923f701599dd951a73254afe0
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/20/2021
ms.locfileid: "98610370"
---
# <a name="role-based-access-control-in-azure-purviews-data-plane"></a>Controllo degli accessi in base al ruolo nel piano dati di Azure per la competenza

Questo articolo descrive la modalità di implementazione del controllo di accesso Role-Based (RBAC) nel [piano dati](../azure-resource-manager/management/control-plane-and-data-plane.md#data-plane)di Azure.

> [!IMPORTANT]
> L'entità che ha creato un account di competenza riceve automaticamente tutte le autorizzazioni del piano dati indipendentemente dai ruoli del piano dati che possono o meno. Per tutti gli altri utenti che eseguono qualsiasi operazione in Azure, è necessario che si trovino in almeno uno dei ruoli predefiniti del piano dati.

## <a name="azure-purviews-pre-defined-data-plane-roles"></a>Ruoli del piano dati predefiniti di Azure.

Azure competenza definisce un set di ruoli del piano dati predefiniti che possono essere usati per controllare chi può accedere a cosa, in Azure. Questi ruoli sono:

* **Ruolo lettore dati di competenza** : ha accesso al portale di competenza ed è in grado di leggere tutto il contenuto in Azure, ad eccezione delle associazioni di analisi
* **Ruolo di curatore dei dati di competenza** : ha accesso al portale di competenza e può leggere tutto il contenuto in Azure, ad eccezione delle associazioni di analisi, può modificare le informazioni sugli asset, modificare le definizioni di classificazione e i termini di glossario e può applicare le classificazioni e i termini di glossario agli asset.
* **Ruolo di amministratore dell'origine dati** di base: non ha accesso al portale di competenza (l'utente deve anche essere presente nei ruoli lettore dati o curatore dati) ed è in grado di gestire tutti gli aspetti dell'analisi dei dati in Azure, ma non ha accesso in lettura o scrittura ai contenuti in Azure, oltre a quelli correlati alla scansione.

## <a name="understanding-how-to-use-azure-purviews-data-plane-roles"></a>Informazioni su come usare i ruoli del piano dati di Azure

Quando viene creato un account Azure, l'autore viene considerato come se fosse sia nel ruolo di amministratore dei dati di competenza che nei ruoli di amministratore dell'origine dati di competenza. Tuttavia, l'autore dell'account non è assegnato a questi ruoli nell'archivio dei ruoli. Azure Purview riconosce che l'entità di sicurezza è l'autore dell'account ed estende a questo tali funzionalità in base alla propria identità.

Tutti gli altri utenti possono usare l'account di Azure Purview solo se vengono inseriti in almeno uno di questi ruoli. Ciò significa che quando viene creato un account Azure, nessuno, ma il creatore può accedere all'account o usare le relative API fino a quando non vengono inserite in uno o più dei ruoli definiti in precedenza.

Si noti che il ruolo di amministratore dell'origine dati di competenza ha due scenari supportati. Il primo scenario è per gli utenti che sono già in grado di ottenere informazioni sui lettori di competenze o sui curatori dei dati di competenza che devono anche essere in grado di creare analisi. Questi utenti devono essere in due ruoli, almeno uno dei lettori di dati di competenza o il responsabile dei dati di competenza, oltre a essere inseriti nel ruolo di amministratore dell'origine dati di competenza.

L'altro scenario per l'amministratore dell'origine dati di competenza è per i processi a livello di codice, ad esempio le entità servizio, che devono essere in grado di configurare e monitorare le analisi, ma non devono avere accesso a nessuno dei dati del catalogo.

Questo scenario può essere implementato inserendo l'entità servizio nel ruolo di amministratore dell'origine dati di competenza senza che venga inserito in nessuno degli altri due ruoli. Il principale non potrà accedere al portale di competenza, ma questo è OK Poiché si tratta di un'entità a livello di codice e comunica solo tramite API.

## <a name="putting-users-into-roles"></a>Inserimento di utenti in ruoli

Quindi, il primo ordine di lavoro dopo la creazione di un account Azure per la competenza consiste nell'assegnare persone a questi ruoli.

L'assegnazione di ruolo viene gestita tramite [RBAC di Azure](../role-based-access-control/overview.md).

Solo due ruoli del piano di controllo predefiniti in Azure possono assegnare ruoli utente, ovvero proprietari o amministratori accesso utenti. Quindi, per inserire persone in questi ruoli per Azure, è necessario trovare un proprietario o un amministratore di accesso utente o diventare un utente.

### <a name="an-example-of-assigning-someone-to-a-role"></a>Esempio di assegnazione di un utente a un ruolo

1. Passa a https://portal.azure.com e passa al tuo account Azure per le tue competenze
1. Sul lato sinistro fare clic su "controllo di accesso (IAM)".
1. Seguire quindi le istruzioni generali fornite [qui](../role-based-access-control/quickstart-assign-role-user-portal.md#create-a-resource-group)

## <a name="role-definitions-and-actions"></a>Definizioni di ruolo e azioni

Un ruolo è definito come una raccolta di azioni. Per ulteriori informazioni sulla modalità di definizione dei ruoli, vedere [qui](../role-based-access-control/role-definitions.md) . Vedere [qui](../role-based-access-control/built-in-roles.md) per le definizioni di ruolo per i ruoli di Azure.

## <a name="getting-added-to-a-data-plane-role-in-an-azure-purview-account"></a>Aggiunta a un ruolo del piano dati in un account Azure per la competenza

Se si vuole avere accesso a un account Azure, in modo che sia possibile usare il proprio studio o chiamare le relative API, è necessario aggiungere un ruolo del piano dati di competenza di Azure. Gli unici utenti che possono eseguire questa operazione sono i proprietari o gli amministratori di accesso utente nell'account Azure. Per la maggior parte degli utenti, il passaggio successivo consiste nel trovare un amministratore locale che possa aiutare a trovare le persone giuste che possono concedere l'accesso.

Per gli utenti che hanno accesso ai [portale di Azure](https://portal.azure.com) della propria azienda possono cercare il particolare account Azure di competenza a cui si desidera aggiungere un join, fare clic sulla scheda "controllo di accesso (IAM)" e vedere chi sono i proprietari o gli amministratori di accesso utente (UAAs). Si noti tuttavia che in alcuni casi Azure Active Directory gruppi o entità servizio possono essere usati come proprietari o UAAs, nel qual caso potrebbe non essere possibile contattarli direttamente. È invece necessario trovare un amministratore per la guida.

## <a name="who-should-be-assigned-to-what-role"></a>Chi deve essere assegnato al ruolo?

|Scenario utente|Ruolo/i appropriato/i|
|-------------|-----------------|
|È sufficiente trovare gli asset, non voglio modificare nulla|Ruolo lettore dati di competenza|
|È necessario modificare le informazioni sugli asset, inserirvi le classificazioni, associarle a voci di glossario e così via.|Ruolo di curatore dei dati di competenza|
|È necessario modificare il glossario o impostare nuove definizioni di classificazione|Ruolo di curatore dei dati di competenza|
|L'entità servizio dell'applicazione deve effettuare il push dei dati in Azure.|Ruolo di curatore dei dati di competenza|
|È necessario configurare le analisi tramite l'ambito di competenza Studio|Ruolo di amministratore dell'origine dati di competenza con almeno un ruolo di lettore dati di competenza o ruolo di cura dei dati di competenza|
|È necessario abilitare un'entità servizio o un'altra identità programmatica per configurare e monitorare le analisi in Azure, senza consentire all'identità a livello di codice di accedere alle informazioni del catalogo |Ruolo di amministratore dell'origine dati di competenza|
|È necessario inserire gli utenti in ruoli in Azure. | Proprietario o amministratore accesso utenti |

Per altre informazioni su come aggiungere un'entità di sicurezza a un ruolo, vedere [Guida introduttiva: creare un account Azure](create-catalog-portal.md) .

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sui dati](concept-insights.md)
