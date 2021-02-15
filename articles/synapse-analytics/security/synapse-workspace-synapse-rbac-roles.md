---
title: Ruoli Controllo degli accessi in base al ruolo di Synapse
description: Questo articolo descrive i ruoli predefiniti di sinapsi RBAC
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/1/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 35f66732fa9cb48b94f80bab203534c9d04b7a7b
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/10/2021
ms.locfileid: "100102122"
---
# <a name="synapse-rbac-roles"></a>Ruoli di controllo delle sinapsi

L'articolo descrive i ruoli di controllo degli accessi in base al ruolo delle sinapsi, le autorizzazioni concesse e gli ambiti in cui possono essere usati.  

## <a name="whats-changed-since-the-preview"></a>Cosa è cambiato rispetto all'anteprima?
Per gli utenti che hanno familiarità con i ruoli di controllo degli accessi in base al ruolo di sinapsi forniti durante l'anteprima, vengono
- L'amministratore dell'area di lavoro è stato rinominato **amministratore della sinapsi**
- Apache Spark amministratore è stata rinominata **sinapsi Apache Spark amministratore** e dispone dell'autorizzazione per visualizzare tutti gli elementi di codice pubblicati, inclusi gli script SQL.  Questo ruolo non concede più l'autorizzazione per usare l'identità del servizio gestito dell'area di lavoro, che richiede il ruolo utente credenziale sinapsi.  Questa autorizzazione è necessaria per eseguire le pipeline. 
- L'amministratore SQL è stato rinominato **amministratore SQL sinapsi** e dispone dell'autorizzazione per visualizzare tutti gli elementi di codice pubblicati, inclusi i blocchi appunti e i processi di Spark.  Questo ruolo non concede più l'autorizzazione per usare l'identità del servizio gestito dell'area di lavoro, che richiede il ruolo utente credenziale sinapsi. Questa autorizzazione è necessaria per eseguire le pipeline.
- Sono stati introdotti nuovi ruoli di controllo degli accessi in base al **ruolo di sinapsi con granularità fine** che si concentrano sul supporto di persone di sviluppo e di operazioni anziché di runtime  
- Sono stati introdotti **nuovi ambiti di livello inferiore** per diversi ruoli.  Questi ambiti consentono di limitare i ruoli a risorse o oggetti specifici.

>[!Note]
>I nuovi ruoli di controllo degli accessi in base al **ruolo e gli ambiti di livello inferiore sono attualmente in anteprima**.  Si consiglia di usare questi nuovi ruoli e ambiti, che sono completamente supportati, e di fornire commenti e suggerimenti sul loro utilizzo.

## <a name="built-in-synapse-rbac-roles-and-scopes"></a>Ambiti e ruoli RBAC predefiniti per le sinapsi

Nella tabella seguente vengono descritti i ruoli predefiniti e gli ambiti in cui possono essere utilizzati.

>[!Note]
> Gli utenti con un ruolo di controllo degli accessi in base al ruolo sinapsi in qualsiasi ambito hanno automaticamente il ruolo utente sinapsi nell'ambito dell'area 

|Ruolo |Autorizzazioni|Ambiti|
|---|---|-----|
|Synapse Administrator  |Accesso completo a sinapsi per pool SQL senza server, pool di Apache Spark e runtime di integrazione.  Include l'accesso di creazione, lettura, aggiornamento ed eliminazione a tutti gli elementi di codice pubblicati.  Include le autorizzazioni operatore di calcolo, Data Manager collegato e credenziali utente per le credenziali di identità del sistema dell'area di lavoro.  Include l'assegnazione di ruoli RBAC di sinapsi. Oltre all'amministratore della sinapsi, i proprietari di Azure possono assegnare anche i ruoli RBAC. Le autorizzazioni di Azure sono necessarie per creare, eliminare e gestire le risorse di calcolo. </br></br>_Consente di leggere e scrivere artefatti </br> per eseguire tutte le azioni sulle attività Spark. </br> Può visualizzare i log del pool Spark </br> può visualizzare il notebook salvato e </br> l'output della pipeline può usare i segreti archiviati da servizi collegati oppure le credenziali </br> possono connettersi a endpoint SQL senza server con le `db_datareader` autorizzazioni SQL,, `db_datawriter` `connect` e `grant` possono assegnare e revocare i ruoli di controllo degli accessi in base </br> al ruolo di sinapsi nell'ambito corrente_|Area di lavoro </br> Pool Spark<br/>Runtime di integrazione </br>Servizio collegato</br>Credenziale |
|Amministratore Apache Spark sinapsi</br>|Accesso completo a sinapsi per i pool di Apache Spark.  Consente di creare, leggere, aggiornare ed eliminare l'accesso alle definizioni dei processi Spark pubblicate, ai notebook e ai relativi output, nonché a librerie, servizi collegati e credenziali.  Include l'accesso in lettura a tutti gli altri elementi di codice pubblicati. Non include l'autorizzazione per l'uso delle credenziali e l'esecuzione di pipeline. Non include la concessione dell'accesso. </br></br>_Può eseguire tutte le azioni sugli artefatti Spark per </br> eseguire tutte le azioni sulle attività Spark_|Area di lavoro</br>Pool Spark|
|Synapse SQL Administrator|Accesso sinapsi completo ai pool SQL senza server.  Consente di creare, leggere, aggiornare ed eliminare l'accesso agli script SQL pubblicati, alle credenziali e ai servizi collegati.  Include l'accesso in lettura a tutti gli altri elementi di codice pubblicati.  Non include l'autorizzazione per l'uso delle credenziali e l'esecuzione di pipeline. Non include la concessione dell'accesso. </br></br>*È possibile eseguire tutte le azioni sugli script SQL <br/> per connettersi a endpoint SQL senza server con le `db_datareader` autorizzazioni SQL,, `db_datawriter` `connect` e `grant`*|Area di lavoro|
|Collaboratore sinapsi|Accesso a sinapsi completo ai pool di Apache Spark e ai runtime di integrazione. Include l'accesso di creazione, lettura, aggiornamento ed eliminazione a tutti gli elementi di codice pubblicati e ai relativi output, incluse le credenziali e i servizi collegati.  Include le autorizzazioni dell'operatore di calcolo. Non include l'autorizzazione per l'uso delle credenziali e l'esecuzione di pipeline. Non include la concessione dell'accesso. </br></br>_È possibile leggere e scrivere artefatti </br> per visualizzare il notebook salvato e l'output della pipeline </br> può eseguire tutte le azioni sulle attività Spark </br> può visualizzare i log del pool Spark_|Area di lavoro </br> Pool Spark<br/> Runtime di integrazione|
|Server di pubblicazione artefatto sinapsi|Consente di creare, leggere, aggiornare ed eliminare l'accesso agli elementi di codice pubblicati e ai rispettivi output. Non include l'autorizzazione per eseguire codice o pipeline o per concedere l'accesso. </br></br>_Può leggere gli artefatti pubblicati e pubblicare artefatti </br> può visualizzare il notebook salvato, il processo Spark e l'output della pipeline_|Area di lavoro
|Utente artefatto sinapsi|Accesso in lettura agli artefatti di codice pubblicati e ai relativi output. Consente di creare nuovi elementi ma non di pubblicare le modifiche o di eseguire codice senza autorizzazioni aggiuntive.|Area di lavoro
|Operatore di calcolo sinapsi |Inviare processi Spark e notebook e visualizzare i log.  Include l'annullamento dei processi Spark inviati da qualsiasi utente. È necessario disporre di autorizzazioni use Credential aggiuntive sull'identità del sistema dell'area di lavoro per eseguire pipeline, visualizzare gli output e le esecuzioni di pipeline. </br></br>_Può inviare e annullare i processi, inclusi i processi inviati da altri utenti </br> possono visualizzare i log del pool Spark_|Area di lavoro</br>Pool Spark</br>Runtime di integrazione|
|Utente credenziale sinapsi|Runtime e configurazione: uso dei segreti all'interno di credenziali e servizi collegati in attività come le esecuzioni di pipeline. Per eseguire pipeline, questo ruolo è obbligatorio, con ambito per l'identità del sistema dell'area di lavoro. </br></br>_Con ambito a una credenziale, consente l'accesso ai dati tramite un servizio collegato protetto dalla credenziale (richiede anche l'autorizzazione per l'uso di calcolo) </br> consente l'esecuzione di pipeline protette dalle credenziali di identità del sistema dell'area di lavoro (con l'autorizzazione di utilizzo aggiuntivo di calcolo)_|Area di lavoro </br>Servizio collegato</br>Credenziale
|Data Manager collegata a sinapsi|Creazione e gestione di endpoint privati gestiti, servizi collegati e credenziali. Consente di creare endpoint privati gestiti che utilizzano servizi collegati protetti da credenziali|Area di lavoro|
|Utente sinapsi|Elencare e visualizzare i dettagli di pool SQL, pool di Apache Spark, Runtime di integrazione e servizi collegati pubblicati e credenziali. Non include altri artefatti di codice pubblicati.  Consente di creare nuovi artefatti, ma non di eseguire o pubblicare senza autorizzazioni aggiuntive.</br></br>_Consente di elencare e leggere i pool Spark, i runtime di integrazione._|Area di lavoro, pool Spark</br>Servizio collegato </br>Credenziale|

## <a name="synapse-rbac-roles-and-the-actions-they-permit"></a>Ruoli controllo degli accessi in base al ruolo e azioni che consentono

>[!Note]
>- Tutte le azioni elencate nelle tabelle seguenti sono precedute dal prefisso "Microsoft. sinapsi/..."</br>
>- Tutte le azioni di lettura, scrittura ed eliminazione degli artefatti sono relative agli elementi pubblicati nel servizio Live.  Queste autorizzazioni non influiscono sull'accesso agli elementi in un repository git connesso.  

Nella tabella seguente sono elencati i ruoli predefiniti e le azioni/autorizzazioni supportate da ciascuno di essi.

Ruolo|Azioni
--|--
Synapse Administrator|aree di lavoro/lettura</br>aree di lavoro/roleAssignment/scrittura, eliminazione</br>aree di lavoro/managedPrivateEndpoint/scrittura, eliminazione</br>aree di lavoro/bigDataPools/useCompute/azione</br>aree di lavoro/bigDataPools/viewLogs/azione</br>aree di lavoro/integrationRuntimes/useCompute/azione</br>aree di lavoro/elementi/lettura</br>aree di lavoro/notebook/scrittura, eliminazione</br>aree di lavoro/sparkJobDefinitions/scrittura, eliminazione</br>aree di lavoro/SqlScript/scrittura, eliminazione</br>aree di lavoro/flussi di lavoro/scrittura, eliminazione</br>aree di lavoro/pipeline/scrittura, eliminazione</br>aree di lavoro/trigger/scrittura, eliminazione</br>aree di lavoro/set di impostazioni/scrittura, eliminazione</br>aree di lavoro/librerie/scrittura, eliminazione</br>aree di lavoro/linkedServices/scrittura, eliminazione</br>aree di lavoro/credenziali/scrittura, eliminazione</br>aree di lavoro/notebook/viewOutputs/azione</br>aree di lavoro/pipeline/viewOutputs/azione</br>aree di lavoro/linkedServices/useSecret/azione</br>aree di lavoro/credenziali/useSecret/azione|
|Amministratore Apache Spark sinapsi|aree di lavoro/lettura</br>aree di lavoro/bigDataPools/useCompute/azione</br>aree di lavoro/bigDataPools/viewLogs/azione</br>aree di lavoro/notebook/viewOutputs/azione</br>aree di lavoro/elementi/lettura</br>aree di lavoro/notebook/scrittura, eliminazione</br>aree di lavoro/sparkJobDefinitions/scrittura, eliminazione</br>aree di lavoro/librerie/scrittura, eliminazione</br>aree di lavoro/linkedServices/scrittura, eliminazione</br>aree di lavoro/credenziali/scrittura, eliminazione|
|Synapse SQL Administrator|aree di lavoro/lettura</br>aree di lavoro/elementi/lettura</br>aree di lavoro/SqlScript/scrittura, eliminazione</br>aree di lavoro/linkedServices/scrittura, eliminazione</br>aree di lavoro/credenziali/scrittura, eliminazione|
|Collaboratore sinapsi|aree di lavoro/lettura</br>aree di lavoro/bigDataPools/useCompute/azione</br>aree di lavoro/bigDataPools/viewLogs/azione</br>aree di lavoro/integrationRuntimes/useCompute/azione</br>aree di lavoro/integrationRuntimes/viewLogs/azione</br>aree di lavoro/elementi/lettura</br>aree di lavoro/notebook/scrittura, eliminazione</br>aree di lavoro/sparkJobDefinitions/scrittura, eliminazione</br>aree di lavoro/SqlScript/scrittura, eliminazione</br>aree di lavoro/flussi di lavoro/scrittura, eliminazione</br>aree di lavoro/pipeline/scrittura, eliminazione</br>aree di lavoro/trigger/scrittura, eliminazione</br>aree di lavoro/set di impostazioni/scrittura, eliminazione</br>aree di lavoro/librerie/scrittura, eliminazione</br>aree di lavoro/linkedServices/scrittura, eliminazione</br>aree di lavoro/credenziali/scrittura, eliminazione</br>aree di lavoro/notebook/viewOutputs/azione</br>aree di lavoro/pipeline/viewOutputs/azione|
|Server di pubblicazione artefatto sinapsi|aree di lavoro/lettura</br>aree di lavoro/elementi/lettura</br>aree di lavoro/notebook/scrittura, eliminazione</br>aree di lavoro/sparkJobDefinitions/scrittura, eliminazione</br>aree di lavoro/SqlScript/scrittura, eliminazione</br>aree di lavoro/flussi di lavoro/scrittura, eliminazione</br>aree di lavoro/pipeline/scrittura, eliminazione</br>aree di lavoro/trigger/scrittura, eliminazione</br>aree di lavoro/set di impostazioni/scrittura, eliminazione</br>aree di lavoro/librerie/scrittura, eliminazione</br>aree di lavoro/linkedServices/scrittura, eliminazione</br>aree di lavoro/credenziali/scrittura, eliminazione</br>aree di lavoro/notebook/viewOutputs/azione</br>aree di lavoro/pipeline/viewOutputs/azione|
|Utente artefatto sinapsi|aree di lavoro/lettura</br>aree di lavoro/elementi/lettura</br>aree di lavoro/notebook/viewOutputs/azione</br>aree di lavoro/pipeline/viewOutputs/azione|
|Operatore di calcolo sinapsi |aree di lavoro/lettura</br>aree di lavoro/bigDataPools/useCompute/azione</br>aree di lavoro/bigDataPools/viewLogs/azione</br>aree di lavoro/integrationRuntimes/useCompute/azione</br>aree di lavoro/integrationRuntimes/viewLogs/azione|
|Utente credenziale sinapsi|aree di lavoro/lettura</br>aree di lavoro/linkedServices/useSecret/azione</br>aree di lavoro/credenziali/useSecret/azione|
|Data Manager collegata a sinapsi|aree di lavoro/lettura</br>aree di lavoro/managedPrivateEndpoint/scrittura, eliminazione</br>aree di lavoro/linkedServices/scrittura, eliminazione</br>aree di lavoro/credenziali/scrittura, eliminazione|
|Utente sinapsi|aree di lavoro/lettura|

## <a name="synapse-rbac-actions-and-the-roles-that-permit-them"></a>Azioni RBAC RBAC e i ruoli che li consentono

La tabella seguente elenca le azioni sinapsi e i ruoli predefiniti che consentono le operazioni seguenti:

Azione|Ruolo
--|--
aree di lavoro/lettura|Synapse Administrator</br>Amministratore Apache Spark sinapsi</br>Synapse SQL Administrator</br>Collaboratore sinapsi</br>Server di pubblicazione artefatto sinapsi</br>Utente artefatto sinapsi</br>Operatore di calcolo sinapsi </br>Utente credenziale sinapsi</br>Data Manager collegata a sinapsi</br>Utente sinapsi 
aree di lavoro/roleAssignment/scrittura, eliminazione|Synapse Administrator
aree di lavoro/managedPrivateEndpoint/scrittura, eliminazione|Synapse Administrator</br>Data Manager collegata a sinapsi
aree di lavoro/bigDataPools/useCompute/azione|Synapse Administrator</br>Amministratore Apache Spark sinapsi</br>Collaboratore sinapsi</br>Operatore di calcolo sinapsi 
aree di lavoro/bigDataPools/viewLogs/azione|Synapse Administrator</br>Amministratore Apache Spark sinapsi</br>Collaboratore sinapsi</br>Operatore di calcolo sinapsi 
aree di lavoro/integrationRuntimes/useCompute/azione|Synapse Administrator</br>Collaboratore sinapsi</br>Operatore di calcolo sinapsi 
aree di lavoro/elementi/lettura|Synapse Administrator</br>Amministratore Apache Spark sinapsi</br>Synapse SQL Administrator</br>Collaboratore sinapsi</br>Server di pubblicazione artefatto sinapsi</br>Utente artefatto sinapsi
aree di lavoro/notebook/scrittura, eliminazione|Synapse Administrator</br>Amministratore Apache Spark sinapsi</br>Collaboratore sinapsi</br>Server di pubblicazione artefatto sinapsi
aree di lavoro/sparkJobDefinitions/scrittura, eliminazione|Synapse Administrator</br>Amministratore Apache Spark sinapsi</br>Collaboratore sinapsi</br>Server di pubblicazione artefatto sinapsi
aree di lavoro/SqlScript/scrittura, eliminazione|Synapse Administrator</br>Synapse SQL Administrator</br>Collaboratore sinapsi</br>Server di pubblicazione artefatto sinapsi
aree di lavoro/flussi di lavoro/scrittura, eliminazione|Synapse Administrator</br>Collaboratore sinapsi</br>Server di pubblicazione artefatto sinapsi
aree di lavoro/pipeline/scrittura, eliminazione|Synapse Administrator</br>Collaboratore sinapsi</br>Server di pubblicazione artefatto sinapsi
aree di lavoro/trigger/scrittura, eliminazione|Synapse Administrator</br>Collaboratore sinapsi</br>Server di pubblicazione artefatto sinapsi
aree di lavoro/set di impostazioni/scrittura, eliminazione|Synapse Administrator</br>Collaboratore sinapsi</br>Server di pubblicazione artefatto sinapsi
aree di lavoro/librerie/scrittura, eliminazione|Synapse Administrator</br>Amministratore Apache Spark sinapsi</br>Collaboratore sinapsi</br>Server di pubblicazione artefatto sinapsi
aree di lavoro/linkedServices/scrittura, eliminazione|Synapse Administrator</br>Collaboratore sinapsi</br>Server di pubblicazione artefatto sinapsi</br>Data Manager collegata a sinapsi
aree di lavoro/credenziali/scrittura, eliminazione|Synapse Administrator</br>Collaboratore sinapsi</br>Server di pubblicazione artefatto sinapsi</br>Data Manager collegata a sinapsi
aree di lavoro/notebook/viewOutputs/azione|Synapse Administrator</br>Amministratore Apache Spark sinapsi</br>Collaboratore sinapsi</br>Server di pubblicazione artefatto sinapsi</br>Utente artefatto sinapsi
aree di lavoro/pipeline/viewOutputs/azione|Synapse Administrator</br>Collaboratore sinapsi</br>Server di pubblicazione artefatto sinapsi</br>Utente artefatto sinapsi
aree di lavoro/linkedServices/useSecret/azione|Synapse Administrator</br>Utente credenziale sinapsi
aree di lavoro/credenziali/useSecret/azione|Synapse Administrator</br>Utente credenziale sinapsi

## <a name="synapse-rbac-scopes-and-their-supported-roles"></a>Ambiti di controllo degli accessi in base al ruolo e ruoli supportati

Nella tabella seguente sono elencati gli ambiti di controllo degli accessi in base al ruolo e i ruoli che possono essere assegnati a ogni ambito. 

>[!note]
>Per creare o eliminare un oggetto è necessario disporre delle autorizzazioni a un ambito di livello superiore.

Ambito|Ruoli
--|--
Area di lavoro |Synapse Administrator</br>Amministratore Apache Spark sinapsi</br>Synapse SQL Administrator</br>Collaboratore sinapsi</br>Server di pubblicazione artefatto sinapsi</br>Utente artefatto sinapsi</br>Operatore di calcolo sinapsi </br>Utente credenziale sinapsi</br>Data Manager collegata a sinapsi</br>Utente sinapsi
Pool di Apache Spark | Synapse Administrator </br>Collaboratore sinapsi </br> Operatore di calcolo sinapsi
Runtime di integrazione | Synapse Administrator </br>Collaboratore sinapsi </br> Operatore di calcolo sinapsi
Servizio collegato |Synapse Administrator </br>Utente credenziale sinapsi
Credenziale |Synapse Administrator </br>Utente credenziale sinapsi
 
>[!note]
>Tutti i ruoli e le azioni dell'artefatto hanno come ambito il livello dell'area di lavoro. 

## <a name="next-steps"></a>Passaggi successivi

Informazioni [su come esaminare le assegnazioni di ruolo RBAC](./how-to-review-synapse-rbac-role-assignments.md) per un'area di lavoro.

Informazioni [su come assegnare i ruoli di](./how-to-manage-synapse-rbac-role-assignments.md) controllo delle sinapsi
