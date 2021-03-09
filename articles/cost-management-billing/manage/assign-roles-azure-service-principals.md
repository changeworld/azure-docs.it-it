---
title: Assegnare ruoli ai nomi dell'entità servizio Contratto Enterprise di Azure
description: Questo articolo consente di assegnare ruoli ai nomi dell'entità servizio usando PowerShell e le API REST.
author: bandersmsft
ms.reviewer: ruturajd
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/07/2021
ms.author: banders
ms.openlocfilehash: 0f30c90bf81a837b1e78ca5f91450cf085cc91bc
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102495144"
---
# <a name="assign-roles-to-azure-enterprise-agreement-service-principal-names"></a>Assegnare ruoli ai nomi dell'entità servizio Contratto Enterprise di Azure

È possibile gestire la registrazione di Contratto Enterprise (EA) in [Azure Enterprise Portal](https://ea.azure.com/). È possibile creare ruoli diversi per gestire l'organizzazione, visualizzare i costi e creare sottoscrizioni. Questo articolo consente di automatizzare alcune di queste attività usando Azure PowerShell e le API REST con i nomi dell'entità servizio (SPN) di Azure.

Prima di iniziare, assicurarsi di avere familiarità con gli articoli seguenti:

- [Ruoli Enterprise Agreement](understand-ea-roles.md)
- [Accedere con Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-5.5.0&preserve-view=true)
- [Come chiamare le API REST con il post](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

## <a name="create-and-authenticate-your-service-principal"></a>Creare e autenticare l'entità servizio

Per automatizzare le azioni EA usando un nome SPN, è necessario creare un'applicazione Azure Active Directory (Azure AD). È possibile eseguire l'autenticazione in modo automatico. Leggere gli articoli seguenti e seguire i passaggi per creare e autenticare l'entità servizio.

1. [Creare un'entità servizio](../../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)
2. [Ottenere i valori di ID tenant e app per l'accesso](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)

Ecco una schermata di esempio che mostra la registrazione dell'applicazione.

:::image type="content" source="./media/assign-roles-azure-service-principals/register-an-application.png" alt-text="Screenshot che illustra la registrazione di un'applicazione." lightbox="./media/assign-roles-azure-service-principals/register-an-application.png" :::

### <a name="find-your-spn-and-tenant-id"></a>Trovare il nome SPN e l'ID tenant

È necessario anche l'ID oggetto dell'SPN e l'ID tenant dell'app. Sono necessarie le informazioni per le operazioni di assegnazione delle autorizzazioni nelle sezioni successive.

È possibile trovare l'ID tenant dell'app Azure AD nella pagina Panoramica per l'applicazione. Per trovarlo nel portale di Azure, passare a Azure Active Directory e selezionare **applicazioni aziendali**. Cercare l'app.

:::image type="content" source="./media/assign-roles-azure-service-principals/enterprise-application.png" alt-text="Screenshot che mostra un'applicazione aziendale di esempio." lightbox="./media/assign-roles-azure-service-principals/enterprise-application.png" :::

Selezionare l'app. Di seguito è riportato un esempio che mostra l'ID dell'applicazione e l'ID oggetto.

:::image type="content" source="./media/assign-roles-azure-service-principals/application-id-object-id.png" alt-text="Screenshot che mostra l'ID dell'applicazione e l'ID oggetto per un'applicazione aziendale." lightbox="./media/assign-roles-azure-service-principals/application-id-object-id.png" :::

È possibile trovare l'ID tenant nella pagina Panoramica Microsoft Azure AD.

:::image type="content" source="./media/assign-roles-azure-service-principals/tenant-id.png" alt-text="Screenshot che mostra dove visualizzare l'ID tenant." lightbox="./media/assign-roles-azure-service-principals/tenant-id.png" :::

L'ID tenant principale viene definito anche ID entità, SPN e ID oggetto in varie posizioni. Il valore dell'ID tenant del Azure AD è simile a un GUID con il formato seguente: `11111111-1111-1111-1111-111111111111` .

## <a name="permissions-that-can-be-assigned-to-the-spn"></a>Autorizzazioni che possono essere assegnate al nome SPN

Per i passaggi successivi, si concede l'autorizzazione all'app Azure AD per eseguire azioni usando un ruolo EA. È possibile assegnare al nome SPN solo i ruoli seguenti. L'ID di definizione del ruolo, esattamente come illustrato, viene usato più avanti nei passaggi di assegnazione.

| Ruolo | Azioni consentite | ID di definizione del ruolo |
| --- | --- | --- |
| EnrollmentReader | Consente di visualizzare l'utilizzo e le tariffe per tutti gli account e le sottoscrizioni. Può visualizzare il saldo prepagato di Azure (in precedenza denominato impegno monetario) associato alla registrazione. | 24f8edb6-1668-4659-b5e2-40bb5f3a7d7e |
| DepartmentReader | Scaricare i dettagli di utilizzo per il reparto amministrato. Consente di visualizzare l'utilizzo e gli addebiti associati al reparto. | db609904-a47f-4794-9be8-9bd86fbffd8a |
| SubscriptionCreator | Crea nuove sottoscrizioni nell'ambito dell'account specificato. | a0bcee42-bf30-4d1b-926a-48d21664ef71 |

- Un lettore di registrazione può essere assegnato a un SPN solo da un utente con ruolo di writer di registrazione.
- Un lettore di reparto può essere assegnato a un SPN solo da un utente che dispone del ruolo di writer di registrazione o del ruolo di writer del reparto.
- Un ruolo di autore della sottoscrizione può essere assegnato a un SPN solo da un utente che è il proprietario dell'account di registrazione.

## <a name="assign-enrollment-account-role-permission-to-the-spn"></a>Assegnazione dell'autorizzazione per il ruolo dell'account di registrazione al nome SPN

Vedere l'articolo relativo alle [assegnazioni di ruolo-put](/rest/api/billing/2019-10-01-preview/roleassignments/put) API REST.

Durante la lettura dell'articolo, selezionare **prova** per iniziare a usare il nome SPN.

:::image type="content" source="./media/assign-roles-azure-service-principals/put-try-it.png" alt-text="Screenshot che mostra l'opzione try it nell'articolo put." lightbox="./media/assign-roles-azure-service-principals/put-try-it.png" :::

Accedere con l'account al tenant che ha accesso alla registrazione a cui si vuole assegnare l'accesso.

Specificare i parametri seguenti come parte della richiesta dell'API.

**billingAccountName**

Il parametro è l'ID dell'account di fatturazione. È possibile trovarlo nella portale di Azure nella pagina Panoramica gestione costi e fatturazione.

:::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="Screenshot che mostra l'ID dell'account di fatturazione." lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

**billingRoleAssignmentName**

Il parametro è un GUID univoco che è necessario fornire. È possibile generare un GUID usando il comando [New-GUID di](/powershell/module/microsoft.powershell.utility/new-guid?view=powershell-7.1&preserve-view=true) PowerShell.

In alternativa, è possibile usare il sito Web [GUID/UUID generator](https://guidgenerator.com/) per generare un GUID univoco.

**versione API**

Usare la versione **2019-10-01-Preview** .

Il corpo della richiesta contiene codice JSON che è necessario usare.

Usare il corpo della richiesta di esempio in [assegnazioni di ruolo-put-examples](/rest/api/billing/2019-10-01-preview/roleassignments/put#examples).

È necessario usare tre parametri come parte di JSON.

| Parametro | Posizione |
| --- | --- |
| Properties. principalId | Vedere [trovare il nome SPN e l'ID tenant](#find-your-spn-and-tenant-id). |
| Properties. principalTenantId | Vedere [trovare il nome SPN e l'ID tenant](#find-your-spn-and-tenant-id). |
| Properties. roleDefinitionId | "/providers/Microsoft.Billing/billingAccounts/{BillingAccountName}/billingRoleDefinitions/24f8edb6-1668-4659-b5e2-40bb5f3a7d7e" |

Il nome dell'account di fatturazione corrisponde al parametro usato nei parametri dell'API. Si tratta dell'ID di registrazione visualizzato nel portale EA e portale di Azure.

Si noti che `24f8edb6-1668-4659-b5e2-40bb5f3a7d7e` è un ID di definizione del ruolo di fatturazione per un EnrollmentReader.

Selezionare **Esegui** per avviare il comando.

:::image type="content" source="./media/assign-roles-azure-service-principals/roleassignments-put-try-it-run.png" alt-text="Screenshot che mostra un'assegnazione di ruolo di esempio, inserire try it con le informazioni di esempio pronte per l'esecuzione." lightbox="./media/assign-roles-azure-service-principals/roleassignments-put-try-it-run.png" :::

Una `200 OK` risposta indica che il nome SPN è stato aggiunto correttamente.

A questo punto è possibile usare il nome SPN (App Azure AD con l'ID oggetto) per accedere alle API EA in modo automatico. Il nome SPN dispone del ruolo EnrollmentReader.

## <a name="assign-the-department-reader-role-to-the-spn"></a>Assegnare il ruolo di lettore del reparto al nome SPN

Prima di iniziare, leggere l'articolo relativo alle [assegnazioni dei ruoli del reparto di registrazione-put](/rest/api/billing/2019-10-01-preview/enrollmentdepartmentroleassignments/put) API REST.

Durante la lettura dell'articolo, selezionare **try it (prova**).

:::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" alt-text="Screenshot che mostra l'opzione prova nell'articolo assegnazioni di ruoli del reparto di registrazione." lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" :::

Accedere con l'account al tenant che ha accesso alla registrazione a cui si vuole assegnare l'accesso.

Specificare i parametri seguenti come parte della richiesta dell'API.

**billingAccountName**

Si tratta dell'ID account di fatturazione. È possibile trovarlo nella portale di Azure nella pagina Panoramica gestione costi e fatturazione.

:::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="Screenshot che mostra l'ID dell'account di fatturazione." lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

**billingRoleAssignmentName**

Il parametro è un GUID univoco che è necessario fornire. È possibile generare un GUID usando il comando [New-GUID di](/powershell/module/microsoft.powershell.utility/new-guid?view=powershell-7.1&preserve-view=true) PowerShell.

In alternativa, è possibile usare il sito Web [GUID/UUID generator](https://guidgenerator.com/) per generare un GUID univoco.

**departmentName**

Si tratta dell'ID del reparto. È possibile visualizzare gli ID reparto nella portale di Azure. Passare a gestione costi e fatturazione > **reparti**.

Per questo esempio è stato usato il reparto ACE. L'ID dell'esempio è `84819` .

:::image type="content" source="./media/assign-roles-azure-service-principals/department-id.png" alt-text="Screenshot che mostra un esempio di ID reparto." lightbox="./media/assign-roles-azure-service-principals/department-id.png" :::

**versione API**

Usare la versione **2019-10-01-Preview** .

Il corpo della richiesta contiene codice JSON che è necessario usare.

Usare l'esempio in [assegnazioni di ruolo del reparto di registrazione-put](/billing/2019-10-01-preview/enrollmentdepartmentroleassignments/put). È necessario usare tre parametri come parte di JSON.

| Parametro | Posizione |
| --- | --- |
| Properties. principalId | Vedere [trovare il nome SPN e l'ID tenant](#find-your-spn-and-tenant-id). |
| Properties. principalTenantId | Vedere [trovare il nome SPN e l'ID tenant](#find-your-spn-and-tenant-id). |
| Properties. roleDefinitionId | "/providers/Microsoft.Billing/billingAccounts/{BillingAccountName}/billingRoleDefinitions/db609904-a47f-4794-9be8-9bd86fbffd8a" |

Il nome dell'account di fatturazione corrisponde al parametro usato nei parametri dell'API. Si tratta dell'ID di registrazione visualizzato nel portale EA e portale di Azure.

L'ID di definizione del ruolo di fatturazione di `db609904-a47f-4794-9be8-9bd86fbffd8a` è per un lettore di reparto.

Selezionare **Esegui** per avviare il comando.

:::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it-run.png" alt-text="Screenshot che mostra un esempio di assegnazioni di ruolo del reparto di registrazione: è possibile riprovare con le informazioni di esempio pronte per l'esecuzione." lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it-run.png" :::

Una `200 OK` risposta indica che il nome SPN è stato aggiunto correttamente.

A questo punto è possibile usare il nome SPN (App Azure AD con l'ID oggetto) per accedere alle API EA in modo automatico. Il nome SPN dispone del ruolo DepartmentReader.

## <a name="assign-the-subscription-creator-role-to-the-spn"></a>Assegnare il ruolo di autore della sottoscrizione al nome SPN

Vedere l'articolo relativo alle [assegnazioni di ruolo dell'account di registrazione](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put) .

Durante la lettura, selezionare **prova** per assegnare il ruolo di autore della sottoscrizione al nome SPN.

:::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" alt-text="Screenshot che mostra l'opzione prova nell'articolo relativo alle assegnazioni di ruolo dell'account di registrazione." lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" :::

Accedere con l'account al tenant che ha accesso alla registrazione a cui si vuole assegnare l'accesso.

Specificare i parametri seguenti come parte della richiesta dell'API. Per informazioni, vedere l'articolo relativo alle [assegnazioni di ruolo dell'account di registrazione-parametri put-URI](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put#uri-parameters).

**billingAccountName**

Il parametro è l'ID dell'account di fatturazione. È possibile trovarlo nella portale di Azure nella pagina Panoramica gestione costi e fatturazione.

:::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="Screenshot che mostra l'ID dell'account di fatturazione." lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

**billingRoleAssignmentName**

Il parametro è un GUID univoco che è necessario fornire. È possibile generare un GUID usando il comando [New-GUID di](/powershell/module/microsoft.powershell.utility/new-guid?view=powershell-7.1&preserve-view=true) PowerShell.

In alternativa, è possibile usare il sito Web [GUID/UUID generator](https://guidgenerator.com/) per generare un GUID univoco.
**enrollmentAccountName**

Il parametro è l'ID account. Trovare l'ID account per il nome dell'account nella portale di Azure in gestione costi + fatturazione nell'ambito registrazione e reparto.

Per questo esempio è stato usato l'account di test di GTM. L'ID è `196987` .

:::image type="content" source="./media/assign-roles-azure-service-principals/account-id.png" alt-text="Screenshot che mostra l'ID account." lightbox="./media/assign-roles-azure-service-principals/account-id.png" :::

**versione API**

Usare la versione **2019-10-01-Preview** .

Il corpo della richiesta contiene codice JSON che è necessario usare.

Usare l'esempio in [assegnazioni di ruolo del reparto di registrazione-put-examples](/rest/api/billing/2019-10-01-preview/enrollmentdepartmentroleassignments/put#putenrollmentdepartmentadministratorroleassignment).

È necessario usare tre parametri come parte di JSON.

| Parametro | Posizione |
| --- | --- |
| Properties. principalId | Vedere [trovare il nome SPN e l'ID tenant](#find-your-spn-and-tenant-id). |
| Properties. principalTenantId | Vedere [trovare il nome SPN e l'ID tenant](#find-your-spn-and-tenant-id). |
| Properties. roleDefinitionId | "/providers/Microsoft.Billing/billingAccounts/{BillingAccountID}/enrollmentAccounts/196987/billingRoleDefinitions/a0bcee42-bf30-4d1b-926a-48d21664ef71" |

Il nome dell'account di fatturazione corrisponde al parametro usato nei parametri dell'API. Si tratta dell'ID di registrazione visualizzato nel portale EA e portale di Azure.

L'ID di definizione del ruolo di fatturazione di `a0bcee42-bf30-4d1b-926a-48d21664ef71` è per il ruolo di autore della sottoscrizione.

Selezionare **Esegui** per avviare il comando.

:::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-account-role-assignments-put-try-it.png" alt-text="Screenshot che mostra l'opzione prova nell'articolo relativo alle assegnazioni di ruolo dell'account di registrazione-put" lightbox="./media/assign-roles-azure-service-principals/enrollment-account-role-assignments-put-try-it.png" :::

Una `200 OK` risposta indica che il nome SPN è stato aggiunto correttamente.

A questo punto è possibile usare il nome SPN (App Azure AD con l'ID oggetto) per accedere alle API EA in modo automatico. Il nome SPN dispone del ruolo SubscriptionCreator.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sull' [amministrazione del portale EA di Azure](ea-portal-administration.md).