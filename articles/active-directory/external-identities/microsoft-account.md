---
title: Provider di identità dell'account Microsoft (MSA) in Azure AD
description: Usare Azure AD per consentire a un utente esterno (Guest) di accedere alle app Azure AD con il proprio account Microsoft (MSA).
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25d3380277d0e653fd5cb03069b7d91cb363dd95
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101693143"
---
# <a name="microsoft-account-msa-identity-provider-for-external-identities-preview"></a>Provider di identità dell'account Microsoft (MSA) per le identità esterne (anteprima)

> [!NOTE]
> Il provider di identità dell'account Microsoft è una funzionalità di anteprima pubblica di Azure Active Directory. Per altre informazioni sulle anteprime, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Gli utenti Guest B2B possono usare i propri account Microsoft personali per la collaborazione B2B senza ulteriori configurazioni. Gli utenti guest possono riscattare gli inviti per la collaborazione B2B o completare i flussi utente di iscrizione usando il proprio account Microsoft personale.

Gli account Microsoft sono configurati da un utente per ottenere l'accesso ai prodotti e ai servizi cloud Microsoft orientati agli utenti, ad esempio Outlook, OneDrive, Xbox LIVE o Microsoft 365. L'account viene creato e archiviato nel sistema di account Microsoft Consumer Identity, eseguito da Microsoft.

## <a name="guest-sign-in-using-microsoft-accounts"></a>Accesso guest con account Microsoft

Per impostazione predefinita, l'account Microsoft è disponibile nell'elenco dei provider di identità di identità esterne. Non è necessaria alcuna configurazione aggiuntiva per consentire agli utenti guest di accedere con la loro account Microsoft usando il flusso di invito o un flusso utente di iscrizione self-service.

![Account Microsoft nell'elenco dei provider di identità](media/microsoft-account/microsoft-account-identity-provider.png)

### <a name="microsoft-account-in-the-invitation-flow"></a>Account Microsoft nel flusso di invito

Quando si [invita un utente Guest](add-users-administrator.md) a collaborare B2B, è possibile specificare la loro account Microsoft come indirizzo di posta elettronica da usare per l'accesso.

![Invita a usare un account Microsoft](media/microsoft-account/microsoft-account-invite.png)

### <a name="microsoft-account-in-self-service-sign-up-user-flows"></a>Account Microsoft in flussi utente di iscrizione self-service

L'account Microsoft è un'opzione del provider di identità per i flussi utente di iscrizione self-service. Gli utenti possono iscriversi alle applicazioni usando i propri account Microsoft. In primo luogo, è necessario [abilitare l'iscrizione self-service](self-service-sign-up-user-flow.md) per il tenant. È quindi possibile configurare un flusso utente per l'applicazione e selezionare account Microsoft come una delle opzioni di accesso.

![Account Microsoft in un flusso utente di iscrizione self-service](media/microsoft-account/microsoft-account-user-flow.png)

## <a name="next-steps"></a>Passaggi successivi

- [Aggiungere Azure Active Directory utenti di collaborazione B2B](add-users-administrator.md)
- [Aggiungere l'iscrizione self-service a un'app](self-service-sign-up-user-flow.md)