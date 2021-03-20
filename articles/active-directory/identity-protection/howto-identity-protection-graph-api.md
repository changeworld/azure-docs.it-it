---
title: Microsoft Graph PowerShell SDK e Azure Active Directory Identity Protection
description: Informazioni su come eseguire query sui rilevamenti dei rischi Microsoft Graph e sulle informazioni associate da Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 01/25/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2db8cfe652c0fca4b68b00d846e345c1b60cd05d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98880237"
---
# <a name="azure-active-directory-identity-protection-and-the-microsoft-graph-powershell-sdk"></a>Azure Active Directory Identity Protection e Microsoft Graph PowerShell SDK

Microsoft Graph è l'endpoint API unificato di Microsoft e la posizione in cui risiedono le API di [Azure Active Directory Identity Protection](./overview-identity-protection.md) . Questo articolo illustra come usare l' [SDK di Microsoft Graph PowerShell](/graph/powershell/get-started) per ottenere informazioni dettagliate sugli utenti usando PowerShell. Le organizzazioni che desiderano eseguire query direttamente sulle API Microsoft Graph possono usare l'articolo [esercitazione: identificare e correggere i rischi usando le api Microsoft Graph](/graph/tutorial-riskdetection-api) per iniziare tale viaggio.


## <a name="connect-to-microsoft-graph"></a>Connetti a Microsoft Graph

La procedura per accedere ai dati di Identity Protection tramite Microsoft Graph si articola in quattro passaggi:

- [Creare un certificato](#create-a-certificate)
- [Crea una nuova registrazione dell'app](#create-a-new-app-registration)
- [Configurare le autorizzazioni API](#configure-api-permissions)
- [Configurare credenziali valide](#configure-a-valid-credential)

### <a name="create-a-certificate"></a>Creare un certificato

In un ambiente di produzione è necessario usare un certificato dell'autorità di certificazione di produzione, ma in questo esempio verrà usato un certificato autofirmato. Creare ed esportare il certificato usando i comandi di PowerShell seguenti.

```powershell
$cert = New-SelfSignedCertificate -Subject "CN=MSGraph_ReportingAPI" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256
Export-Certificate -Cert $cert -FilePath "C:\Reporting\MSGraph_ReportingAPI.cer"
```

### <a name="create-a-new-app-registration"></a>Creare una nuova registrazione di app

1. Nella portale di Azure individuare **Azure Active Directory**  >  **registrazioni app**.
1. Selezionare **Nuova registrazione**.
1. Nella pagina **Crea** seguire questa procedura:
   1. Nella casella di testo **Name (nome** ) digitare un nome per l'applicazione (ad esempio: Azure ad API di rilevamento del rischio).
   1. In **tipi di account supportati** selezionare il tipo di account che utilizzeranno le API.
   1. Selezionare **Registra**.
1. Prendere nota dell'ID dell' **applicazione (client)** e della **Directory (tenant)** , perché questi elementi sono necessari in un secondo momento.

### <a name="configure-api-permissions"></a>Configurare le autorizzazioni API

In questo esempio vengono configurate le autorizzazioni dell'applicazione che consentono l'utilizzo automatico di questo esempio. Se si concedono le autorizzazioni a un utente che avrà eseguito l'accesso, scegliere invece autorizzazioni delegate. Altre informazioni sui diversi tipi di autorizzazioni sono disponibili nell'articolo [autorizzazioni e consenso nella piattaforma di identità Microsoft](../develop/v2-permissions-and-consent.md#permission-types).

1. Dall' **applicazione** creata selezionare **autorizzazioni API**.
1. Nella pagina **autorizzazioni configurate** , nella barra degli strumenti in alto, fare clic su **Aggiungi un'autorizzazione**.
1. Nella pagina **Aggiungi accesso all'API** fare clic su **Selezionare un'API**.
1. Nella pagina **Selezionare un'API** selezionare **Microsoft Graph** e quindi fare clic su **Seleziona**.
1. Nella pagina **autorizzazioni API richiesta** : 
   1. Selezionare **Autorizzazioni applicazione**.
   1. Selezionare le caselle di controllo accanto a `IdentityRiskEvent.Read.All` e `IdentityRiskyUser.Read.All` .
   1. Selezionare **Aggiungi autorizzazioni**.
1. Selezionare **concedi il consenso dell'amministratore per il dominio** 

### <a name="configure-a-valid-credential"></a>Configurare credenziali valide

1. Dall' **applicazione** creata selezionare **certificati & segreti**.
1. In **certificati** selezionare **Carica certificato**.
   1. Selezionare il certificato esportato in precedenza dalla finestra che si apre.
   1. Selezionare **Aggiungi**.
1. Prendere nota dell' **identificazione personale** del certificato, perché sarà necessario nel passaggio successivo.

## <a name="list-risky-users-using-powershell"></a>Elencare utenti rischiosi con PowerShell

Per consentire la possibilità di eseguire query Microsoft Graph, è necessario installare il `Microsoft.Graph` modulo nella finestra di PowerShell usando il `Install-Module Microsoft.Graph` comando.

Modificare le variabili seguenti in modo da includere le informazioni generate nei passaggi precedenti, quindi eseguirle nel suo complesso per ottenere dettagli sugli utenti rischiosi usando PowerShell.

```powershell
$ClientID       = "<your client ID here>"        # Application (client) ID gathered when creating the app registration
$tenantdomain   = "<your tenant domain here>"    # Directory (tenant) ID gathered when creating the app registration
$Thumbprint     = "<your client secret here>"    # Certificate thumbprint gathered when configuring your credential

Select-MgProfile -Name "beta"
  
Connect-MgGraph -ClientId $ClientID -TenantId $tenantdomain -CertificateThumbprint $Thumbprint

Get-MgRiskyUser -All
```

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione a Microsoft Graph PowerShell SDK](/graph/powershell/get-started)
- [Esercitazione: identificare e correggere i rischi usando le API di Microsoft Graph](/graph/tutorial-riskdetection-api)
- [Overview of Microsoft Graph (Panoramica di Microsoft Graph)](https://developer.microsoft.com/graph/docs)
- [Ottenere l'accesso senza un utente](/graph/auth-v2-service)
- [Azure AD Identity Protection Service Root (Radice del servizio Azure AD Identity Protection)](/graph/api/resources/identityprotectionroot)
- [Azure Active Directory Identity Protection](./overview-identity-protection.md)
