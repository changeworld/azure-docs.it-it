---
title: Prerequisiti per l'Azure Active Directory di creazione di report | Microsoft Docs
description: Informazioni sui prerequisiti di accesso all'API di creazione report di Azure AD
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/04/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00c519ef06637c5193b347f0bbc906c6232a7ca8
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532546"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Prerequisiti di accesso all'API di creazione report di Azure AD

Le [API di creazione report di Azure Active Directory (Azure AD)](./concept-reporting-api.md) forniscono l'accesso ai dati dal codice tramite un set di API basate su REST. È possibile chiamare queste API da linguaggi e strumenti di programmazione.

L'API di creazione report usa l'autenticazione [OAuth](../../api-management/api-management-howto-protect-backend-with-aad.md) per autorizzare l'accesso alle API Web.

Per preparare l'accesso all'API di creazione report, è necessario:

1. [Assegnare ruoli](#assign-roles)
2. [Requisiti di licenza](#license-requirements)
3. [Registrare un'applicazione](#register-an-application)
4. [Concedere le autorizzazioni](#grant-permissions)
5. [Ottenere le impostazioni di configurazione](#gather-configuration-settings)

## <a name="assign-roles"></a>Assegnare ruoli

Per accedere ai dati di creazione dei report tramite l'API, è necessario disporre di uno dei seguenti ruoli assegnati:

- Ruolo con autorizzazioni di lettura per la sicurezza

- Amministratore della protezione

- Amministratore globale

## <a name="license-requirements"></a>Requisiti di licenza

Per accedere ai report di accesso per un tenant, a un tenant Azure AD deve essere associata Azure AD Premium licenza. Azure AD Premium la licenza P1 (o superiore) è necessaria per accedere ai report di accesso per qualsiasi tenant Azure AD. In alternativa, se il tipo di directory è Azure AD B2C, i report di accesso sono accessibili tramite l'API senza alcun requisito di licenza aggiuntivo. 


## <a name="register-an-application"></a>Registrare un'applicazione

La registrazione è necessaria anche se si accede all'API di creazione report usando uno script. La registrazione fornisce un **ID applicazione,** che è necessario per le chiamate di autorizzazione e consente al codice di ricevere i token.

Per configurare la directory per l'accesso all'API di creazione report di Azure AD, è necessario accedere al [portale di Azure](https://portal.azure.com) con un account di amministratore di Azure che è anche membro del ruolo di directory **Amministratore globale** nel tenant di Azure AD.

> [!IMPORTANT]
> Le applicazioni eseguite con credenziali con privilegi di amministratore possono essere molto potenti, quindi assicurarsi di conservare l'ID e il segreto dell'applicazione in un luogo sicuro.
> 

**Per registrare un'applicazione Azure AD:**

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Azure Active Directory** nel riquadro di spostamento sinistro.
   
    ![Screenshot che Azure Active Directory selezionato dal menu portale di Azure comando.](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Nella pagina **Azure Active Directory** selezionare **Registrazioni per l'app**.

    ![Screenshot che Registrazioni app selezionato dal menu Gestisci.](./media/howto-configure-prerequisites-for-reporting-api/02.png) 

3. Nella pagina **Registrazioni app** selezionare **Nuova registrazione.**

    ![Screenshot che mostra la nuova registrazione selezionata.](./media/howto-configure-prerequisites-for-reporting-api/03.png)

4. Pagina **Registrazione di un'applicazione:**

    ![Screenshot che mostra la pagina Registra un'applicazione in cui è possibile immettere i valori in questo passaggio.](./media/howto-configure-prerequisites-for-reporting-api/04.png)

    a. Nella casella di testo **Nome** digitare `Reporting API application`.

    b. Per **Tipo di account supportati** selezionare Account solo **nell'organizzazione.**

    c. Nella casella **di testo URL di** **reindirizzamento selezionare Web** digitare `https://localhost` .

    d. Selezionare **Registra**. 


## <a name="grant-permissions"></a>Concedere le autorizzazioni 

A seconda delle API a cui si desidera accedere, è necessario concedere all'app le autorizzazioni seguenti:  

| API | Autorizzazione |
| --- | --- |
| Microsoft Azure Active Directory | Lettura dati directory |
| Microsoft Graph | Leggere tutti i dati dei log di controllo |

![Screenshot che mostra dove è possibile selezionare Aggiungi un'autorizzazione nel riquadro Autorizzazioni A P I.](./media/howto-configure-prerequisites-for-reporting-api/36.png)

Nella sezione seguente viene elencata la procedura per entrambe le API. Se non si desidera accedere a una delle API, è possibile ignorare le procedure relative.

**Per concedere l'autorizzazione dell'applicazione per l'uso delle API:**


1. Selezionare **Autorizzazioni API** e quindi Aggiungi **un'autorizzazione**. 

    ![Screenshot che mostra la pagina A P I Permissions in cui è possibile selezionare Aggiungi un'autorizzazione.](./media/howto-configure-prerequisites-for-reporting-api/05.png)

2. Nella pagina **Richiedi autorizzazioni API individuare** Support legacy **API** **Azure Active Directory Graph**. 

    ![Screenshot che mostra la pagina Request A P I permissions in cui è possibile selezionare Azure Active Directory Graph.](./media/howto-configure-prerequisites-for-reporting-api/06.png)

3. Nella pagina **Autorizzazioni necessarie selezionare** Autorizzazioni applicazione ed espandere la casella di controllo  **Directory.ReadAll**.   Selezionare **Aggiungi autorizzazioni**.

    ![Screenshot che mostra la pagina Request A P I permissions in cui è possibile selezionare Autorizzazioni applicazione.](./media/howto-configure-prerequisites-for-reporting-api/07.png)

4. Nella pagina **Applicazione API di creazione report - Autorizzazioni API** selezionare **Concedi consenso amministratore.** 

    ![Screenshot che mostra la pagina Reporting A P I Application A P I permissions in cui è possibile selezionare Grant admin consent (Concedi consenso amministratore).](./media/howto-configure-prerequisites-for-reporting-api/08.png)

5. Nota: la **Microsoft Graph** viene aggiunta per impostazione predefinita durante la registrazione dell'API.

    ![Screenshot che mostra la pagina delle autorizzazioni A P I in cui è possibile selezionare Aggiungi un'autorizzazione.](./media/howto-configure-prerequisites-for-reporting-api/15.png)

## <a name="gather-configuration-settings"></a>Ottenere le impostazioni di configurazione 

Questa sezione illustra come ottenere le impostazioni seguenti dalla directory:

- Nome di dominio
- ID client
- Segreto client

Questi valori sono necessari quando si configurano le chiamate all'API di creazione report. 

### <a name="get-your-domain-name"></a>Ottenere il nome di dominio

**Per ottenere il nome di dominio:**

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Azure Active Directory** nel riquadro di spostamento a sinistra.
   
    ![Screenshot che Azure Active Directory selezionato dal menu portale di Azure.](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Nella pagina **Azure Active Directory** selezionare **Nomi di dominio personalizzati**.

    ![Screenshot che mostra i nomi di dominio personalizzati selezionati Azure Active Directory.](./media/howto-configure-prerequisites-for-reporting-api/09.png) 

3. Copiare il nome del dominio dall'elenco dei domini.


### <a name="get-your-applications-client-id"></a>Ottenere l'ID client dell'applicazione

**Per ottenere l'ID client dell'applicazione:**

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Azure Active Directory** nel riquadro di spostamento sinistro.
   
    ![Screenshot che Azure Active Directory selezionato dal menu portale di Azure.](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Selezionare l'applicazione nella pagina **Registrazioni per l'app**.

3. Nella pagina dell'applicazione, passare a **ID applicazione** e selezionare **Fare clic per copiare**.

    ![Screenshot che mostra la pagina Reporting A P I Application in cui è possibile copiare l'ID dell'applicazione.](./media/howto-configure-prerequisites-for-reporting-api/11.png) 


### <a name="get-your-applications-client-secret"></a>Ottenere il segreto client dell'applicazione
 Evitare errori durante il tentativo di accesso ai log di controllo o di accesso tramite l'API.

**Per ottenere il segreto client dell'applicazione:**

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Azure Active Directory** nel riquadro di spostamento sinistro.
   
    ![Screenshot che Azure Active Directory selezionato dal menu portale di Azure.](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2.  Selezionare l'applicazione nella pagina **Registrazioni per l'app**.

3.  Selezionare **Certificati e segreti** nella pagina Applicazione **API,** nella sezione **Segreti client** fare clic su + Nuovo **segreto client.** 

    ![Screenshot che mostra la pagina Certificati & segreti in cui è possibile aggiungere un segreto client.](./media/howto-configure-prerequisites-for-reporting-api/12.png)

5. Nella pagina **Aggiungi un segreto client** aggiungere:

    a. Nella casella di testo **Descrizione** digitare `Reporting API`.

    b. Per **Scadenza** selezionare **In 2 years** (In 2 anni).

    c. Fare clic su **Salva**.

    d. Copiare il valore della chiave.

## <a name="troubleshoot-errors-in-the-reporting-api"></a>Risolvere gli errori relativi all'API di creazione report

Questa sezione elenca i messaggi di errore comuni che possono verificarsi durante l'accesso ai report attività usando l'API Microsoft Graph e i passaggi per la risoluzione.

### <a name="error-failed-to-get-user-roles-from-microsoft-graph"></a>Errore: Impossibile ottenere i ruoli utente da Microsoft Graph

 Accedere all'account usando entrambi i pulsanti di accesso nell'interfaccia utente di Graph Explorer per evitare di ricevere un errore quando si tenta di accedere usando Graph Explorer. 

![Graph Explorer](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-microsoft-graph"></a>Errore: Impossibile eseguire il controllo delle licenze Premium da Microsoft Graph 

Se compare questo messaggio di errore durante il tentativo di accedere agli accessi tramite Graph Explorer, scegliere **Autorizzazioni di modifica** sotto l'account sulla barra di spostamento a sinistra e selezionare **Tasks.ReadWrite** e **Directory.Read.All**. 

![Interfaccia utente di Autorizzazioni di modifica](./media/troubleshoot-graph-api/modify-permissions.png)

### <a name="error-tenant-is-not-b2c-or-tenant-doesnt-have-premium-license"></a>Errore: Il tenant non è B2C o il tenant non ha una licenza Premium

L'accesso ai report sugli accessi richiede una licenza Azure Active Directory Premium 1 (P1). Se viene visualizzato questo messaggio di errore durante l'accesso agli accessi, assicurarsi che il tenant sia concesso in licenza con una licenza Azure AD P1.

### <a name="error-the-allowed-roles-does-not-include-user"></a>Errore: i ruoli consentiti non includono User. 

 Evitare errori durante il tentativo di accesso ai log di controllo o di accesso tramite l'API. Assicurarsi che l'account sia parte del ruolo **Security Reader** o **Report Reader** nel tenant Azure Active Directory sicurezza.

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Errore: L'applicazione non possiede l'autorizzazione "Leggi i dati della directory" di AAD 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>Errore: applicazione mancante Microsoft Graph'API 'Lettura tutti i dati del log di controllo'

Seguire la procedura descritta in [Prerequisiti per accedere all'API](howto-configure-prerequisites-for-reporting-api.md) Azure Active Directory reporting per assicurarsi che l'applicazione sia in esecuzione con il set di autorizzazioni giusto. 

## <a name="next-steps"></a>Passaggi successivi

* [Ottenere dati con l'API di creazione report di Azure Active Directory con certificati](tutorial-access-api-with-certificates.md)
* [Informazioni di riferimento sulle API di controllo](/graph/api/resources/directoryaudit) 
* [Informazioni di riferimento sulle API di report di attività di accesso](/graph/api/resources/signin)