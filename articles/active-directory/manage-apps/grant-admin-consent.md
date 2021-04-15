---
title: Concedere il consenso dell'amministratore a livello di tenant a un'applicazione - Azure AD
description: Informazioni su come concedere il consenso a livello di tenant a un'applicazione in modo che agli utenti finali non venga richiesto il consenso durante l'accesso a un'applicazione.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/04/2019
ms.author: iangithinji
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd5017b1437b0f07553e798ab1d96de15fafb3f9
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374183"
---
# <a name="grant-tenant-wide-admin-consent-to-an-application"></a>Concedere a un'applicazione il consenso amministratore a livello di tenant

  Informazioni su come concedere il consenso dell'amministratore a livello di tenant a un'applicazione. Questo articolo illustra i diversi modi per ottenere questo risultato.

Per altre informazioni sul consenso alle applicazioni, vedere [framework di consenso di Azure Active Directory](../develop/consent-framework.md).

## <a name="prerequisites"></a>Prerequisiti

Per concedere il consenso dell'amministratore a livello di tenant, è necessario accedere come utente autorizzato a dare il consenso per conto dell'organizzazione. Sono inclusi [l'amministratore globale](../roles/permissions-reference.md#global-administrator) [e l'amministratore dei](../roles/permissions-reference.md#privileged-role-administrator)ruoli con privilegi e, per alcune applicazioni, [l'amministratore di applicazioni](../roles/permissions-reference.md#application-administrator) e [l'amministratore di applicazioni cloud.](../roles/permissions-reference.md#cloud-application-administrator) Un utente può anche essere autorizzato a concedere il consenso a livello di tenant se gli viene assegnato un ruolo [della directory personalizzato](../roles/custom-create.md) che include l'autorizzazione per concedere le autorizzazioni alle [applicazioni](../roles/custom-consent-permissions.md).

> [!WARNING]
> La concessione del consenso dell'amministratore a livello di tenant a un'applicazione concederà all'app e all'editore dell'app l'accesso ai dati dell'organizzazione. Esaminare attentamente le autorizzazioni richieste dall'applicazione prima di concedere il consenso.

> [!IMPORTANT]
> Quando a un'applicazione viene concesso il consenso dell'amministratore a livello di tenant, tutti gli utenti potranno accedere all'app a meno che non sia stata configurata per richiedere l'assegnazione utente. Per limitare gli utenti che possono accedere a un'applicazione, richiedere l'assegnazione degli utenti e quindi assegnare utenti o gruppi all'applicazione. Per altre informazioni, vedere [Metodi per l'assegnazione di utenti e gruppi](./assign-user-or-group-access-portal.md).

## <a name="grant-admin-consent-from-the-azure-portal"></a>Concedere il consenso dell'amministratore dal portale di Azure

### <a name="grant-admin-consent-in-enterprise-apps"></a>Concedere il consenso dell'amministratore nelle app aziendali

È possibile concedere il consenso dell'amministratore a livello di tenant *tramite applicazioni aziendali* se è già stato effettuato il provisioning dell'applicazione nel tenant. Ad esempio, è possibile eseguire il provisioning di un'app nel tenant se almeno un utente ha già dato il consenso all'applicazione. Per altre informazioni, vedere [Come e perché le applicazioni vengono aggiunte Azure Active Directory](../develop/active-directory-how-applications-are-added.md).

Per concedere il consenso dell'amministratore a livello di tenant a un'app elencata in **Applicazioni aziendali:**

1. Accedere al portale di Azure [come](https://portal.azure.com) amministratore [globale,](../roles/permissions-reference.md#global-administrator)amministratore di [applicazioni](../roles/permissions-reference.md#application-administrator)o amministratore di [applicazioni cloud.](../roles/permissions-reference.md#cloud-application-administrator)
2. Selezionare **Azure Active Directory** quindi **Applicazioni aziendali**.
3. Selezionare l'applicazione a cui si vuole concedere il consenso dell'amministratore a livello di tenant.
4. Selezionare **Autorizzazioni e** quindi fare clic su **Concedi il consenso dell'amministratore.**
5. Esaminare attentamente le autorizzazioni richieste dall'applicazione.
6. Se si accettano le autorizzazioni richieste dall'applicazione, concedere il consenso. In caso contrario, fare **clic su Annulla** o chiudere la finestra.

> [!WARNING]
> La concessione del consenso dell'amministratore a livello di tenant tramite le **app aziendali** revoca tutte le autorizzazioni precedentemente concesse a livello di tenant. Le autorizzazioni precedentemente concesse dagli utenti per proprio conto non saranno interessate. 

### <a name="grant-admin-consent-in-app-registrations"></a>Concedere il consenso dell'amministratore Registrazioni app

Per le applicazioni sviluppate dall'organizzazione o registrate direttamente nel tenant di Azure AD, è  anche possibile concedere il consenso dell'amministratore a livello di tenant Registrazioni app nel portale di Azure.

Per concedere il consenso dell'amministratore a livello di tenant **Registrazioni app**:

1. Accedere al portale di Azure [come](https://portal.azure.com) amministratore [globale,](../roles/permissions-reference.md#global-administrator)amministratore di [applicazioni](../roles/permissions-reference.md#application-administrator)o amministratore di [applicazioni cloud.](../roles/permissions-reference.md#cloud-application-administrator)
2. Selezionare **Azure Active Directory** quindi **Registrazioni app**.
3. Selezionare l'applicazione a cui si vuole concedere il consenso dell'amministratore a livello di tenant.
4. Selezionare **Autorizzazioni API e** quindi fare clic su **Concedi il consenso dell'amministratore.**
5. Esaminare attentamente le autorizzazioni richieste dall'applicazione.
6. Se si accettano le autorizzazioni richieste dall'applicazione, concedere il consenso. In caso contrario, fare **clic su Annulla** o chiudere la finestra.

> [!WARNING]
> La concessione del consenso dell'amministratore a livello di tenant **Registrazioni app** revocare tutte le autorizzazioni precedentemente concesse a livello di tenant. Le autorizzazioni concesse in precedenza dagli utenti per proprio conto non saranno interessate. 

## <a name="construct-the-url-for-granting-tenant-wide-admin-consent"></a>Costruire l'URL per concedere il consenso dell'amministratore a livello di tenant

Quando si concede il consenso dell'amministratore a livello di tenant usando uno dei metodi descritti in precedenza, viene visualizzata una finestra dal portale di Azure per richiedere il consenso dell'amministratore a livello di tenant. Se si conosce l'ID client (noto anche come ID applicazione) dell'applicazione, è possibile compilare lo stesso URL per concedere il consenso dell'amministratore a livello di tenant.

L'URL di consenso dell'amministratore a livello di tenant segue il formato seguente:

```http
https://login.microsoftonline.com/{tenant-id}/adminconsent?client_id={client-id}
```

dove:

* `{client-id}` è l'ID client dell'applicazione (noto anche come ID app).
* `{tenant-id}` è l'ID tenant dell'organizzazione o qualsiasi nome di dominio verificato.

Come sempre, esaminare attentamente le autorizzazioni richieste da un'applicazione prima di concedere il consenso.

> [!WARNING]
> La concessione del consenso dell'amministratore a livello di tenant tramite questo URL revoca tutte le autorizzazioni che in precedenza erano state concesse a livello di tenant. Le autorizzazioni concesse in precedenza dagli utenti per proprio conto non saranno interessate. 

## <a name="next-steps"></a>Passaggi successivi

[Configurare la modalità con cui gli utenti finali consentono le applicazioni](configure-user-consent.md)

[Configurare il flusso di lavoro di consenso dell'amministratore](configure-admin-consent-workflow.md)

[Autorizzazioni e consenso in Microsoft Identity Platform](../develop/v2-permissions-and-consent.md)

[Azure AD su Microsoft Q&A](/answers/topics/azure-active-directory.html)
