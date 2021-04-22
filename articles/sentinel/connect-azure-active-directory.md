---
title: Connettere Azure Active Directory dati a Azure Sentinel | Microsoft Docs
description: Informazioni su come raccogliere dati da Azure Active Directory e trasmettere in streaming Azure AD log di accesso, controllo e provisioning in Azure Sentinel.
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
ms.date: 04/21/2021
ms.author: yelevin
ms.openlocfilehash: d2cfc2d592c24cf5d15a489ea4bde36ea3c2f863
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872372"
---
# <a name="connect-azure-active-directory-azure-ad-data-to-azure-sentinel"></a>Connettere Azure Active Directory dati (Azure AD) a Azure Sentinel

È possibile usare Azure Sentinel connettore incorporato di Azure Active Directory [](../active-directory/fundamentals/active-directory-whatis.md) e trasmettere i dati in Azure Sentinel. Il connettore consente di trasmettere i tipi di log seguenti:

- [**Log di accesso**](../active-directory/reports-monitoring/concept-all-sign-ins.md), che contengono informazioni sugli account di accesso utente interattivi [in](../active-directory/reports-monitoring/concept-all-sign-ins.md#user-sign-ins) cui un utente fornisce un fattore di autenticazione.

    Il connettore Azure AD ora include le tre categorie aggiuntive seguenti di log di accesso, tutti attualmente in **ANTEPRIMA:**
    
    - [**Log di accesso**](../active-directory/reports-monitoring/concept-all-sign-ins.md#non-interactive-user-sign-ins)utente non interattivi, che contengono informazioni sugli account di accesso eseguiti da un client per conto di un utente senza alcun fattore di interazione o autenticazione da parte dell'utente.
    
    - [**Log di accesso dell'entità**](../active-directory/reports-monitoring/concept-all-sign-ins.md#service-principal-sign-ins)servizio , che contengono informazioni sugli accessi da parte di app ed entità servizio che non coinvolgono alcun utente. In questi accessi, l'app o il servizio fornisce credenziali per conto proprio per autenticare o accedere alle risorse.
    
    - [**Log di accesso di**](../active-directory/reports-monitoring/concept-all-sign-ins.md#managed-identity-for-azure-resources-sign-ins)Identità gestita, che contengono informazioni sugli account di accesso delle risorse di Azure con segreti gestiti da Azure. Per altre informazioni, vedere [Che cosa sono le identità gestite per le risorse di Azure?](../active-directory/managed-identities-azure-resources/overview.md)

- [**Log di controllo**](../active-directory/reports-monitoring/concept-audit-logs.md), che contengono informazioni sull'attività di sistema relative alla gestione di utenti e gruppi, applicazioni gestite e attività di directory.

- [**Log di provisioning**](../active-directory/reports-monitoring/concept-provisioning-logs.md) (anche in **ANTEPRIMA),** che contengono informazioni sulle attività di sistema relative a utenti, gruppi e ruoli di cui è stato eseguito il provisioning dal Azure AD di provisioning. 

> [!IMPORTANT]
> Come indicato in precedenza, alcuni dei tipi di log disponibili sono attualmente disponibili in **ANTEPRIMA**. Per altre note legali applicabili alle funzionalità di Azure in versione beta, in anteprima o altrimenti non ancora rilasciate nella disponibilità [generale, vedere](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Condizioni supplementari per l'utilizzo per le anteprime di Microsoft Azure.
## <a name="prerequisites"></a>Prerequisiti

- È Azure Active Directory una licenza P1 o P2 per inserire i log di accesso Azure Sentinel. Qualsiasi Azure AD licenza (gratuita/O365/P1/P2) è sufficiente per inserire gli altri tipi di log. Possono essere applicati costi aggiuntivi per gigabyte per Monitoraggio di Azure (Log Analytics) e Azure Sentinel.

- All'utente deve essere assegnato il Azure Sentinel Collaboratore nell'area di lavoro.

- All'utente devono essere assegnati i ruoli Di amministratore globale o Amministratore della sicurezza nel tenant da cui si vogliono trasmettere i log.

- L'utente deve avere le autorizzazioni di lettura e scrittura Azure AD impostazioni di diagnostica per poter visualizzare lo stato della connessione. 

## <a name="connect-to-azure-active-directory"></a>Connessione ad Azure Active Directory

1. In Azure Sentinel selezionare **Connettori dati** dal menu di spostamento.

1. Nella raccolta di connettori dati selezionare **Azure Active Directory** e quindi selezionare Apri **pagina connettore**.

1. Selezionare le caselle di controllo accanto ai tipi di log da trasmettere in Azure Sentinel (vedere sopra) e fare clic su **Connetti**.

## <a name="find-your-data"></a>Trovare i dati

Dopo aver stabilito una connessione, i dati vengono visualizzati in **Log**, nella **sezione LogManagement** , nelle tabelle seguenti:

- `SigninLogs`
- `AuditLogs`
- `AADNonInteractiveUserSignInLogs`
- `AADServicePrincipalSignInLogs`
- `AADManagedIdentitySignInLogs`
- `AADProvisioningLogs`

Per eseguire una query Azure AD log, immettere il nome della tabella pertinente nella parte superiore della finestra di query.

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettersi Azure Active Directory a Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce.](quickstart-get-visibility.md)
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
