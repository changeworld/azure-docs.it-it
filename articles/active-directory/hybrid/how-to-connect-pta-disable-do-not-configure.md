---
title: Disabilitare PTA quando si usa Azure AD Connect "non configurare" | Microsoft Docs
description: Questo articolo descrive come disabilitare PTA con la funzionalità di Azure AD Connect "non configurare".
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 04/20/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26112b1e799cbde3145e7137c686b4b336db4bab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98919936"
---
# <a name="disable-pta-when-using-azure-ad-connect"></a>Disabilitare PTA quando si usa Azure AD Connect

Se si usa l'autenticazione pass-through con Azure AD Connect ed è impostata su "non **configurare"**, è possibile disabilitarla. 

>[!NOTE]
>Se la funzionalità pH è già abilitata, la disabilitazione di PTA determinerà il fallback del tenant a pH.

La disabilitazione di PTA può essere eseguita usando i cmdlet seguenti. 

## <a name="prerequisites"></a>Prerequisiti
Sono richiesti i prerequisiti seguenti:
- Qualsiasi computer Windows in cui è installato l'agente PTA. 
- Agent deve avere la versione 1.5.1742.0 o successiva. 
- Un account di amministratore globale di Azure per eseguire i cmdlet di PowerShell per disabilitare PTA.

>[!NOTE]
> Se l'agente è meno recente, potrebbe non avere i cmdlet necessari per completare questa operazione. È possibile ottenere un nuovo agente dal portale di Azure e installarlo in qualsiasi computer Windows e fornire le credenziali di amministratore. (L'installazione dell'agente non influisce sullo stato di PTA nel cloud)

> [!IMPORTANT]
> Se si usa il cloud di Azure per enti pubblici, sarà necessario passare il parametro ENVIRONMENTname con il valore seguente. 
>
>| Nome ambiente | Cloud |
>| - | - |
>| AzureUSGovernment | US Gov|


## <a name="to-disable-pta"></a>Per disabilitare PTA
In una sessione di PowerShell usare il comando seguente per disabilitare PTA:
1. PowerShell c:\Programmi\Microsoft Azure AD Connect Authentication Agent> `Import-Module .\Modules\PassthroughAuthPSModule`
2. `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth` o `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth -EnvironmentName <identifier>`
3. `Disable-PassthroughAuthentication  -Feature PassthroughAuth` o `Disable-PassthroughAuthentication -Feature PassthroughAuth -EnvironmentName <identifier>`

## <a name="if-you-dont-have-access-to-an-agent"></a>Se non si ha accesso a un agente

Se non si dispone di un computer agente, è possibile utilizzare il comando seguente per installare un agente.

1. Scaricare l'agente di autenticazione più recente da portal.azure.com.
2. Installare la funzionalità: `.\AADConnectAuthAgentSetup.exe` o `.\AADConnectAuthAgentSetup.exe ENVIRONMENTNAME=<identifier>`


## <a name="next-steps"></a>Passaggi successivi

- [Accesso utente con l'autenticazione pass-through di Azure Active Directory](how-to-connect-pta.md)
