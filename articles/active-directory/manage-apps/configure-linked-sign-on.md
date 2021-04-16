---
title: Informazioni sull'accesso collegato in Azure Active Directory
description: Informazioni sull'accesso collegato in Azure Active Directory.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/30/2020
ms.author: iangithinji
ms.reviewer: arvinh,luleon
ms.openlocfilehash: 6ed6f6b69326157573ea043457dbc8d8a3079146
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374575"
---
# <a name="understand-linked-sign-on"></a>Informazioni sull'accesso collegato

Nella serie [di avvio rapido sulla](view-applications-portal.md) gestione delle applicazioni si è appreso come usare Azure AD come provider di identità (IdP) per un'applicazione. Nella guida introduttiva viene configurato l'accesso SSO basato su SAML o OIDC. Un'altra opzione è **Collegata**. Questo articolo illustra in dettaglio l'opzione collegata.

**L'opzione** Collegato consente di configurare il percorso di destinazione quando un utente seleziona l'app nel portale App personali o office 365 [dell'organizzazione.](https://myapps.microsoft.com/)

Alcuni scenari comuni in cui l'opzione di collegamento è utile includono:
- Aggiungere un collegamento a un'applicazione Web personalizzata che attualmente usa la federazione, ad esempio Active Directory Federation Services (AD FS).
- Aggiungere collegamenti diretti a pagine di SharePoint specifiche o ad altre pagine Web che si desidera visualizzare solo nei pannelli di accesso dell'utente.
- Aggiungere un collegamento a un'app che non richiede l'autenticazione. 
 
 **L'opzione** Collegato non fornisce funzionalità di accesso tramite Azure AD credenziali. Tuttavia, è comunque possibile usare alcune delle altre funzionalità delle **applicazioni aziendali.** Ad esempio, è possibile usare i log di controllo e aggiungere un logo personalizzato e un nome di app.

## <a name="before-you-begin"></a>Prima di iniziare

Per aumentare rapidamente le conoscenze, vedere la [serie di avvio rapido](view-applications-portal.md) sulla gestione delle applicazioni. Nella guida introduttiva, in cui si configura l'accesso Single Sign-On, è disponibile anche **l'opzione** Collegato. 

**L'opzione** Collegato non offre funzionalità di accesso tramite Azure AD. L'opzione imposta semplicemente la posizione a cui gli utenti verranno inviati quando selezionano l'app [App personali](https://myapps.microsoft.com/) o l'icona Microsoft 365'icona di avvio delle app.  Poiché l'accesso non fornisce funzionalità di accesso tramite Azure AD, l'accesso condizionale non è disponibile per le applicazioni configurate con l'accesso Single Sign-On collegato.

> [!IMPORTANT] 
> Esistono alcuni scenari in cui **l'opzione Single Sign-On** non sarà disponibile nella navigazione per un'applicazione in **applicazioni aziendali.** 
>
> Se l'applicazione è stata registrata **Registrazioni app,** la funzionalità Single Sign-On è impostata per l'uso di OAuth OIDC per impostazione predefinita. In questo caso, **l'opzione Single Sign-On** non verrà mostrata nel riquadro di spostamento in **Applicazioni aziendali.** Quando si usa **Registrazioni app** per aggiungere l'app personalizzata, si configurano le opzioni nel file manifesto. Per altre informazioni sul file manifesto, vedere l'Azure Active Directory [dell'app.](../develop/reference-app-manifest.md) Per altre informazioni sugli standard SSO, vedere [Autenticazione e autorizzazione con Microsoft Identity Platform.](../develop/authentication-vs-authorization.md#authentication-and-authorization-using-the-microsoft-identity-platform) 
>
> Altri scenari in cui **l'accesso Single Sign-On** non è presente nella navigazione includono quando un'applicazione è ospitata in un altro tenant o se l'account non dispone delle autorizzazioni necessarie (amministratore globale, amministratore dell'applicazione cloud, amministratore dell'applicazione o proprietario dell'entità servizio). Le autorizzazioni possono anche causare uno scenario in cui è possibile aprire **l'accesso Single Sign-On,** ma non sarà possibile salvarlo. Per altre informazioni sui Azure AD amministrativi, vedere ( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) .

### <a name="configure-link"></a>Configurare il collegamento

Per impostare un collegamento per un'app, selezionare **Collegato** nella **pagina Single Sign-On.** Immettere quindi il collegamento e selezionare **Salva.** È necessario un promemoria su dove trovare queste opzioni? Vedere la serie [di guide introduttive](view-applications-portal.md).
 
Dopo aver configurato un'app, assegnarle utenti e gruppi. Quando si assegnano gli utenti, è possibile controllare quando l'applicazione viene visualizzata [App personali](https://myapps.microsoft.com/) o nell'utilità di avvio Microsoft 365 app.

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare utenti o gruppi all'applicazione](./assign-user-or-group-access-portal.md)
- [Configurare il provisioning automatico degli account utente](../app-provisioning/configure-automatic-user-provisioning-portal.md)
