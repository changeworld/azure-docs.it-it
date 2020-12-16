---
title: Ruoli predefiniti desktop virtuale Windows-Azure
description: Panoramica dei ruoli predefiniti per desktop virtuale di Windows disponibili per il controllo degli accessi in base al ruolo di Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 52f0151fd4be7505cf7beea0eeb54f8e34404997
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97577703"
---
# <a name="built-in-roles-for-windows-virtual-desktop"></a>Ruoli predefiniti per desktop virtuale di Windows

Desktop virtuale di Windows usa i controlli degli accessi in base al ruolo (RBAC) di Azure per assegnare i ruoli a utenti e amministratori. Questi ruoli consentono agli amministratori di eseguire determinate attività. Per altre informazioni sui ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure, vedere [ruoli predefiniti di Azure](../role-based-access-control/built-in-roles.md).

I ruoli predefiniti standard per Azure sono proprietario, collaboratore e lettore. Tuttavia, desktop virtuale di Windows presenta ruoli aggiuntivi che consentono di separare i ruoli di gestione per i pool di host, i gruppi di app e le aree di lavoro. Questa separazione consente di avere un controllo più granulare sulle attività amministrative. Questi ruoli sono denominati in conformità ai ruoli standard di Azure e alla metodologia con privilegi minimi.

Il desktop virtuale Windows non dispone di un ruolo proprietario specifico. Tuttavia, è possibile utilizzare un ruolo di proprietario standard per gli oggetti del servizio.

## <a name="desktop-virtualization-contributor"></a>Collaboratore di virtualizzazione desktop

Il ruolo Collaboratore Desktop Virtualization consente di gestire tutti gli aspetti della distribuzione. Tuttavia, non concede l'accesso alle risorse di calcolo. È necessario anche il ruolo amministratore accesso utenti per pubblicare gruppi di app per utenti o gruppi di utenti.


- Microsoft. DesktopVirtualization/\* 
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/\*
- Microsoft.Authorization/\*/lettura
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="desktop-virtualization-reader"></a>Lettore di virtualizzazione desktop

Il ruolo lettore di virtualizzazione Desktop consente di visualizzare tutti gli elementi della distribuzione, ma non consente di apportare modifiche.

- Microsoft. DesktopVirtualization/ \* /Read
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/read
- Microsoft.Authorization/\*/lettura
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="host-pool-contributor"></a>Collaboratore pool host

Il ruolo Collaboratore pool host consente di gestire tutti gli aspetti dei pool host, incluso l'accesso alle risorse. Per creare macchine virtuali, è necessario un ruolo di collaboratore aggiuntivo, collaboratore macchina virtuale. Per creare un pool host tramite il portale, è necessario disporre dei ruoli collaboratore AppGroup e area di lavoro. in alternativa, è possibile usare il ruolo Collaboratore Desktop Virtualization.

Nell'elenco seguente vengono descritte le autorizzazioni accessibili per il ruolo:

- Microsoft. DesktopVirtualization/hostpools/\*
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/\*
- Microsoft.Authorization/\*/lettura
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="host-pool-reader"></a>Lettore pool host

Il ruolo lettore pool host consente di visualizzare tutti gli elementi nel pool host, ma non consente di apportare modifiche.

- Microsoft. DesktopVirtualization/hostpools/ \* /Read
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/read
- Microsoft.Authorization/\*/lettura
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="application-group-contributor"></a>Collaboratore gruppo di applicazioni

Il ruolo Collaboratore gruppo di applicazioni consente di gestire tutti gli aspetti dei gruppi di app. Se si desidera pubblicare gruppi di app per utenti o gruppi di utenti, è necessario il ruolo amministratore accesso utenti.

Nell'elenco seguente vengono descritte le autorizzazioni accessibili per il ruolo:

- Microsoft. DesktopVirtualization/applicationgroups/\*
- Microsoft. DesktopVirtualization/hostpools/Read
- Microsoft. DesktopVirtualization/hostpools/sessionhosts/Read
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/\*
- Microsoft.Authorization/\*/lettura
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="application-group-reader"></a>Lettore del gruppo di applicazioni

Il ruolo lettore gruppo di applicazioni consente di visualizzare tutti gli elementi del gruppo di app e non consente di apportare modifiche.

Nell'elenco seguente vengono descritte le autorizzazioni accessibili per il ruolo:

- Microsoft. DesktopVirtualization/applicationgroups/ \* /Read
- Microsoft. DesktopVirtualization/applicationgroups/Read
- Microsoft. DesktopVirtualization/hostpools/Read
- Microsoft. DesktopVirtualization/hostpools/sessionhosts/Read
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/read
- Microsoft.Authorization/\*/lettura
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="workspace-contributor"></a>Collaboratore area di lavoro

Il ruolo Collaboratore area di lavoro consente di gestire tutti gli aspetti delle aree di lavoro. Per ottenere informazioni sulle applicazioni aggiunte ai gruppi di app, è necessario assegnare anche il ruolo di lettore del gruppo di applicazioni.

Nell'elenco seguente vengono descritte le autorizzazioni accessibili per il ruolo:

- Microsoft. DesktopVirtualization/Workspaces/\*
- Microsoft. DesktopVirtualization/applicationgroups/Read
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/\*
- Microsoft.Authorization/\*/lettura
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="workspace-reader"></a>Lettore area di lavoro

Il ruolo di lettore dell'area di lavoro consente di visualizzare tutti gli elementi nell'area di lavoro, ma non di apportare modifiche.

Nell'elenco seguente vengono descritte le autorizzazioni accessibili per il ruolo:

- Microsoft. DesktopVirtualization/Workspaces/Read
- Microsoft. DesktopVirtualization/applicationgroups/Read
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/read
- Microsoft.Authorization/\*/lettura
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="user-session-operator"></a>Operatore sessione utente

Il ruolo Operatore sessione utente consente di inviare messaggi, disconnettere sessioni e utilizzare la funzione di disconnessione per firmare le sessioni dall'host sessione. Questo ruolo, tuttavia, non consente di eseguire la gestione degli host di sessione, ad esempio la rimozione di host sessione, la modifica della modalità di svuotamento e così via. Questo ruolo può visualizzare le assegnazioni, ma non può modificare gli amministratori. Si consiglia di assegnare questo ruolo a pool host specifici. Se si assegna questa autorizzazione a livello di gruppo di risorse, l'amministratore disporrà dell'autorizzazione di lettura per tutti i pool host in un gruppo di risorse.

Nell'elenco seguente vengono descritte le autorizzazioni accessibili per il ruolo:

- Microsoft. DesktopVirtualization/hostpools/Read
- Microsoft. DesktopVirtualization/hostpools/sessionhosts/Read
- Microsoft. DesktopVirtualization/hostpools/sessionhosts/usersessions/\*
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/read
- Microsoft.Authorization/\*/lettura
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="session-host-operator"></a>Operatore Session Host

Il ruolo Collaboratore host sessione consente di visualizzare e rimuovere gli host di sessione, nonché di modificare la modalità di svuotamento. Non possono aggiungere host di sessione usando il portale di Azure perché non hanno l'autorizzazione di scrittura per gli oggetti del pool host. Se il token di registrazione è valido (generato e non scaduto), è possibile usare questo ruolo per aggiungere gli host di sessione al pool di host all'esterno di portale di Azure se l'amministratore ha autorizzazioni di calcolo tramite il ruolo Collaboratore macchina virtuale.

Nell'elenco seguente vengono descritte le autorizzazioni accessibili per il ruolo:

- Microsoft. DesktopVirtualization/hostpools/Read
- Microsoft. DesktopVirtualization/hostpools/sessionhosts/\*
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/read
- Microsoft.Authorization/\*/lettura
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*
