---
title: Connettere i dati Azure Active Directory ad Azure Sentinel | Microsoft Docs
description: Informazioni su come raccogliere dati da Azure Active Directory e trasmettere in streaming Azure AD log di accesso, controllo e provisioning in Sentinel di Azure.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: f8931fedb380cf81d72b7b5280a5795498daaa57
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99251982"
---
# <a name="connect-azure-active-directory-azure-ad-data-to-azure-sentinel"></a>Connettere i dati Azure Active Directory (Azure AD) ad Azure Sentinel

È possibile usare il connettore integrato di Sentinel di Azure per raccogliere i dati da [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) e trasmetterli in Azure Sentinel. Il connettore consente di trasmettere i tipi di log seguenti:

- [**Log di accesso**](../active-directory/reports-monitoring/concept-all-sign-ins.md), che contengono informazioni sugli [accessi utente interattivi](../active-directory/reports-monitoring/concept-all-sign-ins.md#user-sign-ins) in cui un utente fornisce un fattore di autenticazione.

    Il connettore Azure AD include ora le tre categorie aggiuntive seguenti di log di accesso, attualmente in **Anteprima**:
    
    - [**Log di accesso utente non interattivo**](../active-directory/reports-monitoring/concept-all-sign-ins.md#non-interactive-user-sign-ins), che contengono informazioni sugli accessi eseguiti da un client per conto di un utente senza alcuna interazione o fattore di autenticazione da parte dell'utente.
    
    - [**Log di accesso dell'entità servizio**](../active-directory/reports-monitoring/concept-all-sign-ins.md#service-principal-sign-ins), che contengono informazioni sugli accessi in base alle app e alle entità servizio che non coinvolgono alcun utente. In questi accessi l'app o il servizio fornisce una credenziale per il proprio conto per l'autenticazione o l'accesso alle risorse.
    
    - [**Log di accesso dell'identità gestita**](../active-directory/reports-monitoring/concept-all-sign-ins.md#managed-identity-for-azure-resources-sign-ins), che contengono informazioni sugli accessi da risorse di Azure con segreti gestiti da Azure. Per altre informazioni, vedere [che cosa sono le identità gestite per le risorse di Azure?](../active-directory/managed-identities-azure-resources/overview.md)

- [**Log di controllo**](../active-directory/reports-monitoring/concept-audit-logs.md), che contengono informazioni sulle attività di sistema relative alla gestione di utenti e gruppi, alle applicazioni gestite e alle attività di directory.

- [**Log di provisioning**](../active-directory/reports-monitoring/concept-provisioning-logs.md) (anche in **Anteprima**) che contengono informazioni sulle attività del sistema relative a utenti, gruppi e ruoli di cui è stato effettuato il provisioning dal servizio Azure ad provisioning. 

> [!IMPORTANT]
> Come indicato in precedenza, alcuni dei tipi di log disponibili sono attualmente in **Anteprima**. Vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) per le note legali aggiuntive che si applicano alle funzionalità di Azure disponibili in versione beta, in anteprima o non ancora rilasciate a livello generale.
## <a name="prerequisites"></a>Prerequisiti

- Eventuali licenze Azure AD (free/O365/P1/P2) sono sufficienti per inserire i log di accesso in Sentinel di Azure. Potrebbero essere applicati addebiti aggiuntivi per Gigabyte per monitoraggio di Azure (Log Analytics) e Sentinel di Azure.

- All'utente deve essere assegnato il ruolo Collaboratore sentinella di Azure nell'area di lavoro.

- All'utente deve essere assegnato il ruolo di amministratore globale o amministratore della sicurezza nel tenant da cui si desidera trasmettere i log.

- Per poter visualizzare lo stato della connessione, l'utente deve disporre delle autorizzazioni di lettura e scrittura per le impostazioni di diagnostica Azure AD. 

## <a name="connect-to-azure-active-directory"></a>Connessione ad Azure Active Directory

1. In Sentinel di Azure selezionare **connettori dati** dal menu di navigazione.

1. Dalla raccolta Data Connector selezionare **Azure Active Directory** e quindi fare clic su **Apri connettore pagina**.

1. Selezionare le caselle di controllo accanto ai tipi di log che si vuole trasmettere in Azure Sentinel (vedere sopra) e fare clic su **Connetti**.

## <a name="find-your-data"></a>Trovare i dati

Una volta stabilita la connessione, i dati vengono visualizzati nei **log**, nella sezione **LogManagement** , nelle tabelle seguenti:

- `SigninLogs`
- `AuditLogs`
- `AADNonInteractiveUserSignInLogs`
- `AADServicePrincipalSignInLogs`
- `AADManagedIdentitySignInLogs`
- `AADProvisioningLogs`

Per eseguire una query sui log di Azure AD, immettere il nome della tabella pertinente nella parte superiore della finestra della query.

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere Azure Active Directory ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
