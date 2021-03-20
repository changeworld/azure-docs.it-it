---
title: 'Agente di provisioning di Azure AD Connect Cloud: aggiornamento automatico | Microsoft Docs'
description: Questo articolo descrive la funzionalità di aggiornamento automatico integrato nell'agente di provisioning di Azure AD Connect Cloud.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26c0b47dd358826b843143aaf23c469d852e93b3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98613790"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Agente di provisioning di Azure AD Connect Cloud: aggiornamento automatico

Assicurarsi che l'installazione dell'agente di provisioning in Azure Active Directory (Azure AD) sia sempre aggiornata è facile con la funzionalità di aggiornamento automatico.

L'agente è installato qui: "programma files\Azure AD Connect provisioning Agent\AADConnectProvisioningAgent.exe"

Per verificare la versione, fare clic con il pulsante destro del mouse sul file eseguibile e scegliere Proprietà e quindi Dettagli.

![Versione del file dell'agente](media/how-to-automatic-upgrade/agent-1.png)

Agent Updater viene installato qui: "Program files\Azure AD Connect provisioning Agent Updater\AzureADConnectAgentUpdater.exe"

Per verificare la versione, fare clic con il pulsante destro del mouse sul file eseguibile e scegliere Proprietà e quindi Dettagli.

![Versione di aggiornamento agente](media/how-to-automatic-upgrade/agent-2.png)

## <a name="uninstall-the-agent"></a>Disinstallazione dell'agente
Per rimuovere l'agente, passare a **Disinstalla o modificare un programma** e disinstallare quanto segue:

- **Microsoft Azure AD Connect Agent Updater**
- **Microsoft Azure AD Connect Provisioning Agent**
- **Microsoft Azure AD Connect Provisioning Agent Package**

![Rimozione agente](media/how-to-automatic-upgrade/agent-3.png)

## <a name="next-steps"></a>Passaggi successivi 

- [Cos'è il provisioning?](what-is-provisioning.md)
- [Che cos'è Azure AD Connect sincronizzazione cloud?](what-is-cloud-sync.md)

