---
title: Registrare un'applicazione Microsoft Graph
titleSuffix: Azure AD B2C
description: Preparare la gestione delle risorse di Azure AD B2C con Microsoft Graph registrando un'applicazione a cui vengono concesse le autorizzazioni di API Graph necessarie.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/21/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 67870a458138101f3b8a009f7c96c74991396284
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98675187"
---
# <a name="register-a-microsoft-graph-application"></a>Registrare un'applicazione Microsoft Graph

[Microsoft Graph][ms-graph] consente di gestire molte delle risorse all'interno del tenant Azure ad B2C, inclusi gli account utente e i criteri personalizzati del cliente. Scrivendo script o applicazioni che chiamano l' [API Microsoft Graph][ms-graph-api], è possibile automatizzare le attività di gestione dei tenant, ad esempio:

* Eseguire la migrazione di un archivio utente esistente a un tenant di Azure AD B2C
* Distribuire criteri personalizzati con una pipeline di Azure in Azure DevOps e gestire chiavi di criteri personalizzate
* Ospitare la registrazione degli utenti nella propria pagina e creare gli account utente nella directory Azure AD B2C dietro le quinte
* Automatizzare la registrazione dell'applicazione
* Ottenere i log di controllo

Le sezioni seguenti consentono di preparare l'uso dell'API Microsoft Graph per automatizzare la gestione delle risorse nella directory Azure AD B2C.

## <a name="microsoft-graph-api-interaction-modes"></a>Modalità interazione API Microsoft Graph

Esistono due modalità di comunicazione che è possibile usare quando si lavora con l'API Microsoft Graph per gestire le risorse nel tenant di Azure AD B2C:

* **Interattivo** : appropriato per le attività di esecuzione, si usa un account amministratore nel tenant B2C per eseguire le attività di gestione. Questa modalità richiede a un amministratore di accedere con le proprie credenziali prima di chiamare l'API Microsoft Graph.

* **Automatizzato** : per le attività pianificate o eseguite in modo continuo, questo metodo usa un account del servizio configurato con le autorizzazioni necessarie per eseguire le attività di gestione. Per creare l'Azure AD B2C account del servizio, è necessario registrare un'applicazione usata dalle applicazioni e dagli script per l'autenticazione usando l' *ID dell'applicazione (client)* e la concessione di **credenziali client OAuth 2,0** . In questo caso, l'applicazione agisce da solo per chiamare l'API Microsoft Graph e non l'utente amministratore come nel metodo interattivo descritto in precedenza.

Per abilitare lo scenario di interazione **automatica** , è possibile creare una registrazione dell'applicazione illustrata nelle sezioni seguenti.

Sebbene il flusso di concessione delle credenziali client OAuth 2,0 non sia attualmente supportato direttamente dal servizio di autenticazione Azure AD B2C, è possibile configurare il flusso di credenziali client usando Azure AD e l'endpoint/token della piattaforma di identità Microsoft per un'applicazione nel tenant di Azure AD B2C. Un tenant di Azure AD B2C condivide alcune funzionalità con i tenant enterprise di Azure AD.

## <a name="register-management-application"></a>Registrare l'applicazione di gestione

Prima che gli script e le applicazioni possano interagire con l' [api Microsoft Graph][ms-graph-api] per gestire le risorse Azure ad B2C, è necessario creare una registrazione dell'applicazione nel tenant di Azure ad B2C che conceda le autorizzazioni necessarie per l'API.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare l'icona **Directory e sottoscrizione** nella barra degli strumenti del portale e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel portale di Azure cercare e selezionare **Azure AD B2C**.
1. Selezionare **Registrazioni app** e quindi **Nuova registrazione**.
1. Immettere un **nome** per l'applicazione. Ad esempio, *managementapp1*.
1. Selezionare **Account solo in questa directory dell'organizzazione**.
1. In **autorizzazioni** deselezionare la casella *di controllo Concedi il consenso dell'amministratore a OpenID e offline_access le autorizzazioni* .
1. Selezionare **Registra**.
1. Registrare l' **ID dell'applicazione (client)** visualizzato nella pagina Panoramica dell'applicazione. Questo valore verrà usato in un passaggio successivo.

### <a name="grant-api-access"></a>Concedere l'accesso all'API

A questo punto, concedere le autorizzazioni dell'applicazione registrata per modificare le risorse tenant tramite chiamate all'API Microsoft Graph.

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="create-client-secret"></a>Crea segreto client

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

A questo punto si dispone di un'applicazione che dispone dell'autorizzazione per *creare*, *leggere*, *aggiornare* ed *eliminare* gli utenti nel tenant di Azure ad B2C. Passare alla sezione successiva per aggiungere le autorizzazioni di *aggiornamento della password* .

## <a name="enable-user-delete-and-password-update"></a>Abilita eliminazione utente e aggiornamento password

L'autorizzazione *per la lettura e la scrittura dei dati della directory* **non** include la possibilità di eliminare gli utenti o aggiornare le password degli account utente.

Se l'applicazione o lo script deve eliminare gli utenti o aggiornare le password, assegnare il ruolo di *amministratore utente* all'applicazione:

1. Accedere al [portale di Azure](https://portal.azure.com) e usare il filtro **directory + sottoscrizione** per passare al tenant di Azure ad B2C.
1. Cercare e selezionare **Azure Active Directory B2C**.
1. In **Gestisci** selezionare **ruoli e amministratori**.
1. Selezionare il ruolo di **amministratore utente** .
1. Selezionare **Aggiungi assegnazioni**.
1. Nella casella di testo **Seleziona** immettere il nome dell'applicazione registrata in precedenza, ad esempio *managementapp1*. Selezionare l'applicazione quando viene visualizzata nei risultati della ricerca.
1. Selezionare **Aggiungi**. Potrebbero essere necessari alcuni minuti per la propagazione completa delle autorizzazioni.

## <a name="next-steps"></a>Passaggi successivi

Ora che l'applicazione di gestione è stata registrata e sono state concesse le autorizzazioni necessarie, le applicazioni e i servizi (ad esempio, Azure Pipelines) possono usare le credenziali e le autorizzazioni per interagire con l'API Microsoft Graph. 

* [Ottenere un token di accesso da Azure AD](/graph/auth-v2-service#4-get-an-access-token)
* [Usare il token di accesso per chiamare Microsoft Graph](/graph/auth-v2-service#4-get-an-access-token)
* [Operazioni B2C supportate da Microsoft Graph](microsoft-graph-operations.md)
* [Gestire gli account utente Azure AD B2C con Microsoft Graph](microsoft-graph-operations.md)
* [Ottenere i log di controllo con l'API per la creazione di report Azure AD](view-audit-logs.md#get-audit-logs-with-the-azure-ad-reporting-api)

<!-- LINKS -->
[ms-graph]: /graph/
[ms-graph-api]: /graph/api/overview
