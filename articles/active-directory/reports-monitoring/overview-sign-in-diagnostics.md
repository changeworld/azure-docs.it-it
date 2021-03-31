---
title: Che cos'è la diagnostica di accesso per Azure Active Directory?
description: Viene fornita una panoramica generale della diagnostica di accesso in Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: e2b3d8ce-708a-46e4-b474-123792f35526
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 12/15/2020
ms.author: markvi
ms.reviewer: tspring
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdef3e1f1a60c9eb0c751855837e9cbe77e015e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98572290"
---
# <a name="what-is-the-sign-in-diagnostic-in-azure-ad"></a>Qual è la diagnostica di accesso in Azure AD?

Azure Active Directory (Azure AD) fornisce un modello di sicurezza flessibile per controllare le operazioni che gli utenti possono eseguire con le risorse gestite. L'accesso a queste risorse viene controllato non solo *dall'utente* , ma anche dalla *modalità* di accesso. In genere, un modello flessibile presenta un certo livello di complessità a causa del numero di opzioni di configurazione disponibili. E con la complessità aumentano i rischi di errori.

In qualità di amministratore IT, è necessaria una soluzione che fornisca informazioni approfondite sulle attività del sistema. Questa visibilità può consentire di diagnosticare e risolvere i problemi quando si verificano. La diagnostica di accesso per Azure AD è un esempio di tale soluzione. È possibile usare la diagnostica per analizzare gli eventi che si sono verificati durante un tentativo di accesso e ottenere consigli per la risoluzione dei problemi senza dover coinvolgere il supporto tecnico Microsoft.

Questo articolo fornisce una panoramica delle funzionalità di questa soluzione e del modo in cui è possibile usarla.

## <a name="requirements"></a>Requisiti

La funzionalità di diagnostica per l'accesso è disponibile in tutte le edizioni di Azure AD.

Per usarla è necessario essere amministratore globale di Azure AD.

## <a name="how-it-works"></a>Funzionamento

In Azure AD la risposta a un tentativo di accesso è legata a *chi* accede e *come* accede al tenant. Ad esempio, un amministratore può in genere configurare tutti gli aspetti del tenant quando accede dalla rete aziendale. Tuttavia, lo stesso utente potrebbe essere bloccato quando accede con lo stesso account da una rete non attendibile.

A causa della maggiore flessibilità offerta dal sistema per rispondere ai tentativi di accesso, ci si potrebbe trovare in situazioni in cui è necessario risolvere i problemi di accesso. La diagnostica per l'accesso è una funzionalità che:

- Analizza i dati dagli eventi di accesso.

- Mostra cosa è successo.

- Fornisce indicazioni per la risoluzione dei problemi.

La diagnostica per l'accesso di Azure AD è progettata per consentire la diagnosi autonoma degli errori di accesso. Per completare il processo di diagnostica è necessario:

![Diagramma che mostra la diagnostica di accesso.](./media/overview-sign-in-diagnostics/process.png)

1. Definire l'ambito degli eventi di accesso a cui si è interessati.

2. Selezionare l'accesso che si desidera esaminare.

3. Esaminare i risultati della diagnostica.

4. Eseguire un'azione.

### <a name="define-scope"></a>Definire l'ambito

L'obiettivo di questo passaggio consiste nel definire l'ambito degli eventi di accesso da analizzare. L'ambito può essere basato su un utente o su un identificatore (correlationId, RequestId) e un intervallo di tempo. Per limitare ulteriormente l'ambito, è possibile specificare un nome di app. Azure AD usa le informazioni sull'ambito per individuare gli eventi corretti.  

### <a name="select-sign-in"></a>Selezionare l'accesso  

In base ai criteri di ricerca, Azure AD recupera tutti gli eventi di accesso corrispondenti e li presenta in una visualizzazione elenco di riepilogo dell'autenticazione.

![Screenshot parziale che mostra la sezione di riepilogo dell'autenticazione.](./media/overview-sign-in-diagnostics/authentication-summary.png)

È possibile personalizzare le colonne di questa visualizzazione.

### <a name="review-diagnostic"></a>Esaminare la diagnostica

Per l'evento di accesso selezionato, Azure AD fornisce risultati diagnostici.

![Screenshot parziale che mostra la sezione dei risultati di diagnostica.](./media/overview-sign-in-diagnostics/diagnostics-results.png)

Questi risultati iniziano con una valutazione, che spiega cosa è accaduto in poche frasi. La spiegazione è utile per comprendere il comportamento del sistema.

Si otterrà quindi un riepilogo dei criteri di accesso condizionale correlati applicati all'evento di accesso selezionato. I risultati diagnostici includono anche procedure di correzione consigliate per risolvere il problema. Poiché non è sempre possibile risolvere i problemi senza ulteriori informazioni, un passaggio consigliato potrebbe consistere nell'aprire un ticket di supporto.

### <a name="take-action"></a>Intervieni

A questo punto si dovrebbe disporre delle informazioni necessarie per risolvere il problema.

## <a name="scenarios"></a>Scenari

Gli scenari seguenti sono coperti dalla diagnostica di accesso:

- Accesso bloccato dai criteri di accesso condizionale

- Accesso condizionale non riuscito

- Autenticazione a più fattori dall'accesso condizionale

- Autenticazione a più fattori in base ad altri requisiti

- Richiesta di configurazione dell'autenticazione a più fattori

- Verifica dell'autenticazione a più fattori richiesta (percorso di accesso rischioso)

- Accesso riuscito

### <a name="blocked-by-conditional-access"></a>Accesso bloccato dai criteri di accesso condizionale

In questo scenario, un tentativo di accesso è stato bloccato da un criterio di accesso condizionale.

![Screenshot che mostra la configurazione dell'accesso con accesso a blocchi selezionato.](./media/overview-sign-in-diagnostics/block-access.png)

La sezione diagnostica per questo scenario mostra i dettagli sull'evento di accesso utente e i criteri applicati.

### <a name="failed-conditional-access"></a>Accesso condizionale non riuscito

Questo scenario è in genere il risultato di un tentativo di accesso non riuscito perché i requisiti di un criterio di accesso condizionale non sono stati soddisfatti. Esempi comuni:

![Screenshot che mostra la configurazione dell'accesso con esempi di criteri comuni e concede l'accesso selezionato.](./media/overview-sign-in-diagnostics/require-controls.png)

- Richiedere un dispositivo aggiunto ad Azure AD ibrido

- Richiedere app client approvata

- Richiedere criteri di protezione dell'app

La sezione diagnostica per questo scenario mostra i dettagli relativi al tentativo di accesso utente e ai criteri applicati.

### <a name="mfa-from-conditional-access"></a>Autenticazione a più fattori in base ad accesso condizionale

In questo scenario, i criteri di accesso condizionale hanno la necessità di eseguire l'accesso con l'autenticazione a più fattori impostata.

![Screenshot che illustra la configurazione dell'accesso con Richiedi autenticazione a più fattori selezionata.](./media/overview-sign-in-diagnostics/require-mfa.png)

La sezione diagnostica per questo scenario mostra i dettagli relativi al tentativo di accesso utente e ai criteri applicati.

### <a name="mfa-from-other-requirements"></a>Autenticazione a più fattori in base ad altri requisiti

In questo scenario, un requisito di autenticazione a più fattori non è stato applicato da un criterio di accesso condizionale. Ad esempio, l'autenticazione a più fattori per ogni singolo utente.

![Screenshot che illustra l'autenticazione a più fattori per ogni configurazione utente.](./media/overview-sign-in-diagnostics/mfa-per-user.png)

Lo scopo di questo scenario di diagnostica è quello di fornire maggiori dettagli su:

- Origine dell'interrupt di autenticazione a più fattori
- Risultato dell'interazione del client

È anche possibile visualizzare tutti i dettagli del tentativo di accesso dell'utente.

### <a name="mfa-proof-up-required"></a>Richiesta di configurazione dell'autenticazione a più fattori

In questo scenario, i tentativi di accesso sono stati interrotti dalle richieste di configurazione dell'autenticazione a più fattori. Questa configurazione è nota anche come prova.

La verifica dell'autenticazione a più fattori si verifica quando un utente è necessario usare l'autenticazione a più fattori ma non lo ha ancora configurato o un amministratore ha richiesto all'utente di configurarlo.

Lo scopo di questo scenario di diagnostica è rivelare che l'interruzione dell'autenticazione a più fattori è dovuta alla mancanza di configurazione utente. La soluzione consigliata prevede che l'utente completi la prova.

### <a name="mfa-proof-up-required-risky-sign-in-location"></a>Verifica dell'autenticazione a più fattori richiesta (percorso di accesso rischioso)

In questo scenario, i tentativi di accesso sono stati interrotti da una richiesta di configurazione dell'autenticazione a più fattori da un percorso di accesso rischioso.

Lo scopo di questo scenario di diagnostica è rivelare che l'interruzione dell'autenticazione a più fattori è dovuta alla mancanza di configurazione utente. La soluzione consigliata prevede che l'utente completi la prova, in particolare da un percorso di rete che non viene visualizzato a rischio.

Se, ad esempio, una rete aziendale è definita come una località denominata, l'utente deve invece provare a eseguire la verifica dalla rete aziendale.

### <a name="successful-sign-in"></a>Accesso riuscito

In questo scenario, gli eventi di accesso non sono stati interrotti dall'accesso condizionale o dall'autenticazione a più fattori.

Questo scenario di diagnostica fornisce informazioni dettagliate sugli eventi di accesso degli utenti che dovrebbero essere interrotti a causa di criteri di accesso condizionale o di autenticazione a più fattori.

## <a name="next-steps"></a>Passaggi successivi

- [Che cosa sono i report di Azure Active Directory?](overview-reports.md)
- [Che cos'è il monitoraggio di Azure Active Directory?](overview-monitoring.md)
