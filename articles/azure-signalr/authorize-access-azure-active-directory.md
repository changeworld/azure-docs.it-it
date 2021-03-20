---
title: Autorizzare l'accesso con Azure Active Directory
description: Questo articolo fornisce informazioni su come autorizzare l'accesso alle risorse del servizio Azure SignalR usando Azure Active Directory.
author: terencefan
ms.author: tefa
ms.date: 08/03/2020
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: 37c2e41b5c81f941245b895ecd144baee3b9db6d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97797516"
---
# <a name="authorize-access-to-azure-signalr-service-resources-using-azure-active-directory"></a>Autorizzare l'accesso alle risorse del servizio Azure SignalR usando Azure Active Directory
Il servizio Azure SignalR supporta l'uso di Azure Active Directory (Azure AD) per autorizzare le richieste alle risorse del servizio Azure SignalR. Con Azure AD, è possibile usare il controllo degli accessi in base al ruolo (RBAC) per concedere le autorizzazioni a un'entità di sicurezza, che può essere un utente o un'entità servizio dell'applicazione. Per ulteriori informazioni sui ruoli e le assegnazioni di ruolo, vedere [informazioni sui diversi ruoli](../role-based-access-control/overview.md).

## <a name="overview"></a>Panoramica
Quando un'entità di sicurezza (un'applicazione) tenta di accedere a una risorsa del servizio Azure SignalR, la richiesta deve essere autorizzata. Con Azure AD, l'accesso a una risorsa è un processo in due passaggi. 

 1. In primo luogo, l'identità dell'entità di sicurezza viene autenticata e viene restituito un token OAuth 2,0. Il nome della risorsa per richiedere un token è `https://signalr.azure.com/` .
 2. Successivamente, il token viene passato come parte di una richiesta al servizio Azure SignalR per autorizzare l'accesso alla risorsa specificata.

Il passaggio di autenticazione richiede che una richiesta dell'applicazione contenga un token di accesso OAuth 2.0 in fase di esecuzione. Se il server Hub è in esecuzione in un'entità di Azure, ad esempio una VM di Azure, un set di scalabilità di macchine virtuali o un'app per le funzioni di Azure, può usare un'identità gestita per accedere alle risorse. Per informazioni su come autenticare le richieste effettuate da un'identità gestita nel servizio Azure SignalR, vedere [autenticare l'accesso alle risorse del servizio Azure SignalR con Azure Active Directory e identità gestite per le risorse di Azure](authenticate-managed-identity.md). 

Il passaggio di autorizzazione richiede che uno o più ruoli di controllo degli accessi in base al ruolo siano assegnati all'entità di sicurezza. Il servizio Azure SignalR fornisce ruoli RBAC che includono i set di autorizzazioni per le risorse di Azure SignalR. I ruoli assegnati a un'entità di sicurezza determinano le autorizzazioni che saranno disponibili nell'entità. Per altre informazioni sui ruoli RBAC, vedere [ruoli predefiniti di Azure per il servizio Azure SignalR](#azure-built-in-roles-for-azure-signalr-service). 

Il server Hub SignalR che non è in esecuzione all'interno di un'entità di Azure può anche autorizzare con Azure AD. Per informazioni su come richiedere un token di accesso e usarlo per autorizzare le richieste di risorse del servizio Azure SignalR, vedere [autenticare l'accesso al servizio Azure SignalR con Azure ad da un'applicazione](authenticate-application.md). 

## <a name="azure-built-in-roles-for-azure-signalr-service"></a>Ruoli predefiniti di Azure per il servizio Azure SignalR

- [Server App SignalR]
- [Lettore del servizio SignalR]
- [Proprietario del servizio SignalR]

## <a name="assign-rbac-roles-for-access-rights"></a>Assegnare i ruoli di controllo degli accessi in base al ruolo per i diritti di accesso
Azure Active Directory (Azure AD) autorizza diritti di accesso a risorse protette tramite il [controllo degli accessi in base al ruolo](../role-based-access-control/overview.md). Il servizio Azure SignalR definisce un set di ruoli predefiniti di Azure che comprende i set comuni di autorizzazioni usate per accedere al servizio Azure SignalR ed è anche possibile definire ruoli personalizzati per l'accesso alla risorsa.

Quando un ruolo di controllo degli accessi in base al ruolo viene assegnato a un'entità di sicurezza di Azure AD, Azure concede l'accesso a tali risorse per quell'entità di sicurezza. L'ambito di accesso può essere limitato al livello di sottoscrizione, al gruppo di risorse o a qualsiasi risorsa del servizio Azure SignalR. Un'entità di sicurezza Azure AD può essere un utente, un'applicazione o un' [identità gestita per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="built-in-roles-for-azure-signalr-service"></a>Ruoli predefiniti per il servizio Azure SignalR
Azure fornisce i seguenti ruoli predefiniti di Azure per autorizzare l'accesso alla risorsa del servizio Azure SignalR usando Azure AD e OAuth:

### <a name="signalr-app-server"></a>Server App SignalR

Usare questo ruolo per concedere l'accesso per ottenere una chiave di accesso temporanea per la firma dei token client.

### <a name="signalr-serverless-contributor"></a>Collaboratore senza server SignalR

Usare questo ruolo per concedere l'accesso per ottenere un token client direttamente dal servizio Azure SignalR.

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli correlati seguenti:

- [Autenticare un'applicazione con Azure AD per accedere al servizio Azure SignalR](authenticate-application.md)
- [Autenticare un'identità gestita con Azure AD per accedere al servizio Azure SignalR](authenticate-managed-identity.md)