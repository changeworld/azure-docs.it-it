---
title: Panoramica degli account utente in Azure Active Directory B2C
description: Informazioni sui tipi di account utente che è possibile usare in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: f2473955db5439911280360b169f469b8c93eb93
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105043598"
---
# <a name="overview-of-user-accounts-in-azure-active-directory-b2c"></a>Panoramica degli account utente in Azure Active Directory B2C

In Azure Active Directory B2C (Azure AD B2C), è possibile creare diversi tipi di account. Azure Active Directory, Active Directory B2B e Active Directory B2C condividere i tipi di account utente che è possibile utilizzare.

Sono disponibili i tipi di account seguenti:

- **Account aziendale**: un account aziendale può accedere alle risorse in un tenant; se dispone di un ruolo di amministratore, può gestire i tenant.
- **Account guest**: un account guest può essere solo un account Microsoft o un utente di Azure AD che può essere usato per accedere alle applicazioni o gestire i tenant.
- **Account del consumer** : un account utente viene usato da un utente delle applicazioni registrate con Azure ad B2C. Gli account utente possono essere creati da:
  - Utente che attraversa un flusso utente di iscrizione in un'applicazione Azure AD B2C
  - Uso dell'API Microsoft Graph
  - Uso del portale di Azure

## <a name="work-account"></a>Account aziendale

La procedura per la creazione di un account aziendale è la stessa per tutti i tenant basati su Azure AD. Per creare un account aziendale, usare le informazioni contenute in [Guida introduttiva: Aggiungere nuovi utenti ad anteprima di Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md). Per creare un account aziendale usare l'opzione **Nuovo utente** nel portale di Azure.

Quando si aggiunge un nuovo account aziendale, è necessario prendere in considerazione le impostazioni di configurazione seguenti:

- **Nome** e **Nome utente** - La proprietà **Nome** contiene il nome e il cognome dell'utente. Il **Nome utente** è l'identificatore che l'utente immette per accedere e include il dominio completo. La parte del nome di dominio del nome utente deve essere il nome di dominio predefinito iniziale *dominio.onmicrosoft.com* o un nome di dominio verificato, non federato, [personalizzato](../active-directory/fundamentals/add-custom-domain.md), ad esempio *contoso.com*. 
- **Posta elettronica** : il nuovo utente può accedere anche usando un indirizzo di posta elettronica. Non sono supportati caratteri speciali o caratteri multibyte nel messaggio di posta elettronica, ad esempio i caratteri giapponesi.
- **Profilo** - L'account è configurato con un profilo di dati utente. È possibile immettere nome, cognome, posizione e nome del reparto. Dopo aver creato l'account, è possibile modificare il profilo.
- **Gruppi** : usare i gruppi per eseguire attività di gestione, ad esempio l'assegnazione di licenze o autorizzazioni a molti utenti o dispositivi contemporaneamente. È possibile inserire il nuovo account in un [gruppo](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) esistente del tenant.
- **Ruolo della directory** - È necessario specificare il livello di accesso dell'account utente alle risorse del tenant. Sono disponibili i seguenti livelli di autorizzazione:

    - **Utente** - Gli utenti possono accedere alle risorse assegnate, ma non possono gestire la maggior parte delle risorse del tenant.
    - **Amministratore globale**- Gli amministratori globali hanno il controllo completo su tutte le risorse del tenant.
    - **Amministratore con limitazioni** - Selezionare il ruolo o i ruoli amministrativi per l'utente. Per altre informazioni sui ruoli che è possibile selezionare, vedere [Assegnazione dei ruoli di amministratore in Azure Active Directory](../active-directory/roles/permissions-reference.md).

### <a name="create-a-work-account"></a>Creare un account aziendale

Per creare un nuovo account aziendale è possibile usare le informazioni seguenti:

- [Azure portal](../active-directory/fundamentals/add-users-azure-active-directory.md)
- [Microsoft Graph](/graph/api/user-post-users)

### <a name="update-a-user-profile"></a>Aggiornare un profilo utente

Per aggiornare un profilo utente è possibile usare le informazioni seguenti:

- [Azure portal](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
- [Microsoft Graph](/graph/api/user-update)

### <a name="reset-a-password-for-a-user"></a>Reimpostare la password di un utente

Per reimpostare la password di un utente è possibile usare le informazioni seguenti:

- [Azure portal](../active-directory/fundamentals/active-directory-users-reset-password-azure-portal.md)
- [Microsoft Graph](/graph/api/user-update)

## <a name="guest-user"></a>Utente guest

È possibile invitare utenti esterni al tenant assegnando loro il ruolo di utente guest. Uno scenario tipico per questo tipo di invito al tenant di Azure AD B2C è quello in cui è necessario condividere le responsabilità di amministrazione. Per un esempio di utilizzo di un account guest, vedere [Proprietà di un utente di Collaborazione B2B di Azure Active Directory](../active-directory/external-identities/user-properties.md).

Quando si invita un utente guest al tenant, viene fornito l'indirizzo di posta elettronica del destinatario e un messaggio che descrive l'invito. Il collegamento all'invito porta l'utente alla pagina di consenso. Se all'indirizzo di posta elettronica non è collegata una casella di posta in arrivo, l'utente può aprire la pagina di consenso da una pagina Microsoft, usando le credenziali fornite nell'invito. L'utente deve quindi riscattare l'invito con la stessa procedura che avrebbe eseguito usando il collegamento presente nel messaggio di posta elettronica. Ad esempio: `https://myapps.microsoft.com/B2CTENANTNAME`.

Per invitare un utente guest è anche possibile usare l'[API Graph di Microsoft](/graph/api/invitation-post?view=graph-rest-beta).

## <a name="consumer-user"></a>Utente consumer

L'utente consumer può accedere ad applicazioni protette da Azure AD B2C, ma non alle risorse di Azure, ad esempio il portale di Azure. Può usare un account locale o account federati come ad esempio Facebook o Twitter. Un account utente viene creato usando un [flusso utente di iscrizione o di accesso](user-flow-overview.md), usando l'API Microsoft Graph o l'portale di Azure.

È possibile specificare i dati raccolti quando viene creato un account utente del consumer. Per altre informazioni, vedere [aggiungere attributi utente e personalizzare l'input dell'utente](configure-user-input.md).

Per ulteriori informazioni sulla gestione degli account utente, vedere [manage Azure ad B2C user accounts with Microsoft Graph](./microsoft-graph-operations.md).

### <a name="migrate-consumer-user-accounts"></a>Migrare gli account utente consumer

Può essere necessario eseguire la migrazione degli account utente consumer esistenti da qualsiasi provider di identità ad Azure AD B2C. Per altre informazioni, vedere [Eseguire la migrazione degli utenti ad Azure AD B2C](user-migration.md).
