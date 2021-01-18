---
title: Panoramica del controllo di accesso a un'area di lavoro Synapse
description: Questo articolo descrive i meccanismi usati per controllare l'accesso a un'area di lavoro Synapse e alle risorse e gli artefatti del codice che contiene.
services: synapse-analytics
author: billgib
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 12/03/2020
ms.author: billgib
ms.reviewer: jrasnick
ms.openlocfilehash: 30cc917e2db3a7c4c6d5d6ebd5a8a47afff5d505
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98133141"
---
# <a name="synapse-access-control"></a>Controllo di accesso a Synapse 

Questo articolo fornisce una panoramica dei meccanismi disponibili per controllare l'accesso alle risorse di calcolo e ai dati di Synapse.  

## <a name="overview"></a>Panoramica

Synapse offre un sistema di controllo di accesso completo e granulare che integra: 
- **Ruoli di Azure** per la gestione delle risorse e l'accesso ai dati nell'archiviazione 
- **Ruoli di Synapse** per la gestione dell'accesso in tempo reale al codice e all'esecuzione 
- **Ruoli SQL** per l'accesso del piano dati ai dati nei pool SQL 
- **Autorizzazioni Git** per il controllo del codice sorgente, inclusi il supporto per l'integrazione e la distribuzione continue  

I ruoli di Synapse forniscono set di autorizzazioni che possono essere applicati in ambiti diversi. Grazie a questa granularità, è facile concedere ad amministratori, sviluppatori, addetti alla sicurezza e operatori l'accesso appropriato a risorse di calcolo e dati.

Il controllo di accesso può essere semplificato usando gruppi di sicurezza allineati ai ruoli lavorativi delle persone. Per gestire l'accesso è sufficiente aggiungere e rimuovere utenti dai gruppi di sicurezza appropriati.

## <a name="access-control-elements"></a>Elementi del controllo di accesso

### <a name="creating-and-managing-synapse-compute-resources"></a>Creazione e gestione di risorse di calcolo di Synapse

I ruoli di Azure vengono usati per controllare la gestione di: 
- Pool SQL dedicati 
- Pool di Apache Spark 
- Runtime di integrazione 

Per *creare* queste risorse, è necessario avere il ruolo di Proprietario o Collaboratore di Azure sul gruppo di risorse. Per *gestirle* dopo la creazione, occorre avere il ruolo di Proprietario o Collaboratore di Azure sul gruppo di risorse o sulle singole risorse. 

### <a name="developing-and-executing-code-in-synapse"></a>Sviluppo ed esecuzione di codice in Synapse 

Synapse supporta due modelli di sviluppo.

- **Sviluppo attivo di Synapse**. Lo sviluppo e il debug del codice avvengono in Synapse Studio, quindi il codice viene **pubblicato** per salvarlo ed eseguirlo.  Il servizio Synapse è la fonte più attendibile per la modifica e l'esecuzione di codice.  Qualsiasi lavoro non pubblicato viene perso quando si chiude Synapse Studio.  
- **Sviluppo abilitato per Git**. Lo sviluppo e il debug del codice avvengono in Synapse Studio, quindi viene eseguito il **commit** delle modifiche in un ramo di lavoro di un repository Git. Il lavoro di uno o più rami è integrato in un ramo di collaborazione, da cui viene **pubblicato** nel servizio. Il repository Git è la fonte più attendibile per la modifica del codice, mentre il servizio lo è per l'esecuzione. Occorre eseguire il commit delle modifiche nel repository Git oppure pubblicarle nel servizio prima di chiudere Synapse Studio. [Altre informazioni](../cicd/continuous-integration-deployment.md) sull'uso di Synapse Analytics con Git.

In entrambi i modelli di sviluppo, qualsiasi utente che abbia accesso a Synapse Studio può creare artefatti di codice. Sono però necessarie autorizzazioni aggiuntive per pubblicare gli artefatti nel servizio, leggere gli artefatti pubblicati, eseguire il commit delle modifiche in Git, eseguire il codice e accedere ai dati collegati protetti da credenziali.

### <a name="synapse-roles"></a>Ruoli di Synapse

I ruoli di Synapse vengono usati per controllare l'accesso al servizio Synapse, che consente di: 
- Elencare gli artefatti di codice pubblicati 
- Pubblicare artefatti di codice, servizi collegati e definizioni di credenziali
- Eseguire codice o pipeline che usano risorse di calcolo di Synapse
- Eseguire codice o pipeline che accedono a dati collegati protetti da credenziali
- Visualizzare gli output associati ad artefatti di codice pubblicati
- Monitorare lo stato delle risorse di calcolo e visualizzare i log di runtime

I ruoli di Synapse possono essere assegnati nell'ambito dell'area di lavoro o in ambiti più granulari per limitare le autorizzazioni concesse a specifiche risorse di Synapse.

### <a name="git-permissions"></a>Autorizzazioni Git

Se si usa lo sviluppo abilitato per Git in modalità Git, le autorizzazioni Git stabiliscono se è possibile leggere ed eseguire il commit delle modifiche agli artefatti di codice, inclusi i servizi collegati e le definizioni di credenziali.   
   
### <a name="accessing-data-in-sql"></a>Accesso ai dati in SQL

Quando si usano pool SQL dedicati e serverless, l'accesso al piano dati viene controllato tramite le autorizzazioni SQL. 

All'autore di un'area di lavoro viene assegnato il ruolo di amministratore di Active Directory nell'area di lavoro. Dopo la creazione, questo ruolo può essere assegnato a un altro utente o a un gruppo di sicurezza nel portale di Azure.

**Pool SQL serverless**: agli amministratori di Synapse vengono concesse le autorizzazioni `db_owner` (`DBO`) sul pool SQL serverless predefinito. Per concedere ad altri utenti l'accesso a pool SQL serverless, gli amministratori di Synapse devono eseguire script SQL su ogni pool serverless.  

**Pool SQL dedicati**: l'autorizzazione di amministratore di Active Directory viene concessa all'autore dell'area di lavoro e all'identità del servizio gestita dell'area di lavoro.  L'autorizzazione di accesso ai pool SQL dedicati non viene concessa automaticamente. Per concedere ad altri utenti o gruppi l'accesso a pool SQL dedicati, l'amministratore di Active Directory deve eseguire script SQL su ogni pool SQL dedicato.

Per esempi di script SQL per la concessione di autorizzazioni SQL in pool SQL, vedere [Come configurare il controllo di accesso per l'area di lavoro Synapse](./how-to-set-up-access-control.md).  

 ### <a name="accessing-system-managed-data-in-storage"></a>Accesso a dati gestiti dal sistema nell'archiviazione

I pool SQL serverless e le tabelle Apache Spark archiviano i dati in un contenitore di ADLS Gen2 associato all'area di lavoro. Anche le librerie di Apache Spark installate dall'utente vengono gestite nello stesso account di archiviazione. Per abilitare questi casi d'uso, agli utenti e all'identità del servizio gestita dell'area di lavoro deve essere assegnato il ruolo di **Collaboratore ai dati dei BLOB di archiviazione** per poter accedere a questo contenitore di archiviazione di ADLS Gen2.  

## <a name="using-security-groups-as-a-best-practice"></a>Uso dei gruppi di sicurezza come procedura consigliata

Per semplificare la gestione del controllo di accesso, si possono usare i gruppi di sicurezza per assegnare ruoli a singoli utenti e gruppi. I gruppi di sicurezza possono essere creati per eseguire il mirroring di utenti tipo o funzioni lavorative dell'organizzazione che devono poter accedere alle risorse o agli artefatti di Synapse.  A questi gruppi di sicurezza basati su utente tipo è quindi possibile assegnare uno o più ruoli di Azure, ruoli di Synapse, autorizzazioni SQL o autorizzazioni Git. Scegliendo bene i gruppi di sicurezza, sarà facile assegnare a un utente le autorizzazioni necessarie aggiungendolo al gruppo di sicurezza appropriato. 

>[!Note]
>Se si usano i gruppi di sicurezza per gestire l'accesso, Azure Active Directory aggiunge latenza prima che le modifiche abbiano effetto. 

## <a name="access-control-enforcement-in-synapse-studio"></a>Applicazione del controllo di accesso in Synapse Studio

Il comportamento di Synapse Studio cambia in base alle autorizzazioni e alla modalità corrente:
- **Modalità attiva di Synapse:** Synapse Studio impedisce agli utenti senza le autorizzazioni necessarie di visualizzare i contenuti pubblicati, di pubblicare contenuti o di eseguire altre azioni.  In alcuni casi, impedisce di creare artefatti di codice che non possono essere usati o salvati. 
- **Modalità Git:** se si hanno autorizzazioni Git che consentono di eseguire il commit delle modifiche al ramo corrente, l'azione di commit sarà consentita anche se non si dispone dell'autorizzazione per pubblicare le modifiche nel servizio attivo.  

In alcuni casi è consentito creare artefatti di codice anche senza l'autorizzazione per la pubblicazione o il commit. In questo modo è possibile eseguire il codice (con le autorizzazioni di esecuzione necessarie). Vedere [altre informazioni](./synapse-workspace-understand-what-role-you-need.md) sui ruoli necessari per le attività comuni. 

Se una funzionalità è disabilitata in Synapse Studio, una descrizione comando indicherà l'autorizzazione necessaria. Usare la [guida ai ruoli di controllo degli accessi in base al ruolo di Synapse](./synapse-workspace-synapse-rbac-roles.md#synapse-rbac-actions-and-the-roles-that-permit-them) per sapere qual è il ruolo necessario per fornire l'autorizzazione mancante.


## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sul [controllo degli accessi in base al ruolo di Synapse](./synapse-workspace-synapse-rbac.md)
- Altre informazioni sui [ruoli Controllo degli accessi in base al ruolo di Synapse](./synapse-workspace-synapse-rbac-roles.md)
- Informazioni su come [configurare il controllo di accesso](./how-to-set-up-access-control.md) per un'area di lavoro Synapse usando i gruppi di sicurezza.
- Informazioni su come [verificare le assegnazioni di ruolo per il controllo degli accessi in base al ruolo](./how-to-review-synapse-rbac-role-assignments.md)
- Informazioni su come [gestire le assegnazioni di ruolo per il controllo degli accessi in base al ruolo](./how-to-manage-synapse-rbac-role-assignments.md)
