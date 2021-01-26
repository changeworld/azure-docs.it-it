---
title: Connettere i dati Azure Active Directory ad Azure Sentinel | Microsoft Docs
description: Informazioni su come raccogliere dati da Azure Active Directory e trasmettere Azure AD log di accesso e log di controllo in Sentinel di Azure.
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
ms.openlocfilehash: eb89d2a4e719e34ad5ea31656dc9e3c02472b07d
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98802262"
---
# <a name="connect-data-from-azure-active-directory-azure-ad"></a>Connettere i dati da Azure Active Directory (Azure AD)

È possibile usare il connettore integrato di Sentinel di Azure per raccogliere i dati da [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) e trasmetterli in Azure Sentinel. Il connettore consente di eseguire lo streaming di [log di accesso](../active-directory/reports-monitoring/concept-sign-ins.md) e [log di controllo](../active-directory/reports-monitoring/concept-audit-logs.md).

## <a name="prerequisites"></a>Prerequisiti

- È necessario avere una sottoscrizione di [Azure ad Premium P2](https://azure.microsoft.com/pricing/details/active-directory/) per inserire i log di accesso in Sentinel di Azure. Potrebbero essere applicati addebiti aggiuntivi per Gigabyte per monitoraggio di Azure (Log Analytics) e Sentinel di Azure.

- All'utente deve essere assegnato il ruolo Collaboratore sentinella di Azure nell'area di lavoro.

- All'utente deve essere assegnato il ruolo di amministratore globale o amministratore della sicurezza nel tenant da cui si desidera trasmettere i log.

- Per poter visualizzare lo stato della connessione, l'utente deve disporre delle autorizzazioni di lettura e scrittura per le impostazioni di diagnostica Azure AD. 

## <a name="connect-to-azure-active-directory"></a>Connessione ad Azure Active Directory

1. In Sentinel di Azure selezionare **connettori dati** dal menu di navigazione.

1. Dalla raccolta Data Connector selezionare **Azure Active Directory** e quindi fare clic su **Apri connettore pagina**.

1. Selezionare le caselle di controllo accanto ai tipi di log che si vuole trasmettere in Azure Sentinel, quindi fare clic su **Connetti**. Questi sono i tipi di log tra cui è possibile scegliere:

    - **Log di accesso**: informazioni sull'uso delle applicazioni gestite e delle attività di accesso degli utenti.
    - **Log di controllo**: informazioni relative alle attività di sistema sulla gestione di utenti e gruppi, sulle applicazioni gestite e sulle attività di directory.

## <a name="find-your-data"></a>Trovare i dati

Una volta stabilita la connessione, i dati vengono visualizzati nei **log**, nella sezione **LogManagement** , nelle tabelle seguenti:

- `SigninLogs`
- `AuditLogs`

Per eseguire una query sui log di Azure AD, immettere il nome della tabella pertinente nella parte superiore della finestra della query.

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere Azure Active Directory ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
