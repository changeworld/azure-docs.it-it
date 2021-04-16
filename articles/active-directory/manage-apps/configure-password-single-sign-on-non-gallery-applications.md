---
title: Informazioni sull'accesso Single Sign-On (SSO) basato su password per le app in Azure Active Directory
description: Informazioni sull'accesso Single Sign-On (SSO) basato su password per le app in Azure Active Directory
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: iangithinji
ms.openlocfilehash: ffa517f068dbc13f2734630216466373d9014ae6
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374557"
---
# <a name="understand-password-based-single-sign-on"></a>Informazioni sull'accesso Single Sign-On basato su password

Nella serie [di avvio rapido sulla](view-applications-portal.md) gestione delle applicazioni si è appreso come usare Azure AD come provider di identità (IdP) per un'applicazione. Nella guida introduttiva viene configurato l'accesso SSO basato su SAML o OIDC. Un'altra opzione è l'accesso SSO basato su password. Questo articolo illustra in dettaglio l'opzione SSO basata su password. 

Questa opzione è disponibile per qualsiasi sito Web con una pagina di accesso HTML. L'accesso SSO basato su password è noto anche come insieme di credenziali delle password. L'accesso SSO basato su password consente di gestire l'accesso utente e le password alle applicazioni Web che non supportano la federazione delle identità. È anche utile quando diversi utenti devono condividere un singolo account, ad esempio con gli account dell'app di social media dell'organizzazione.

L'accesso SSO basato su password è un ottimo modo per iniziare a integrare rapidamente le applicazioni in Azure AD e consente di:

- Abilitare l'accesso Single Sign-On per gli utenti archiviando e riproducendo in modo sicuro nomi utente e password

- Supportare applicazioni che richiedono più campi di accesso per applicazioni che richiedono non solo i campi di nome utente e password per accedere

- Personalizzare le etichette dei campi nome utente e password visualizzati dagli utenti App personali [immettere](../user-help/my-apps-portal-end-user-access.md) le proprie credenziali

- Consentire agli utenti di fornire i propri nomi utente e password per tutti gli account dell'applicazione esistenti digitati manualmente.

- Consentire a un membro del gruppo aziendale di specificare i nome utente e le password assegnati a un utente tramite la funzionalità di [accesso all'applicazione self-service](./manage-self-service-access.md)

-   Consentire a un amministratore di specificare un nome utente e una password che devono essere usati da singoli utenti o gruppi quando a loro volta a un accesso all'applicazione con la funzionalità Aggiorna credenziali 

## <a name="before-you-begin"></a>Prima di iniziare

L Azure AD come provider di identità (IdP) e la configurazione dell'accesso Single Sign-On (SSO) possono essere semplici o complessi a seconda dell'applicazione in uso. Alcune applicazioni possono essere configurate con poche azioni. Altri richiedono una configurazione approfondita. Per aumentare rapidamente le conoscenze, vedere la [serie di avvio rapido](view-applications-portal.md) sulla gestione delle applicazioni. Se l'applicazione che si sta aggiungendo è semplice, probabilmente non è necessario leggere questo articolo. Se l'applicazione che si sta aggiungendo richiede una configurazione personalizzata ed è necessario usare l'accesso Single Sign-On basato su password, questo articolo fa per te.

> [!IMPORTANT] 
> Esistono alcuni scenari in cui **l'opzione Single Sign-On** non sarà disponibile nella navigazione per un'applicazione in **applicazioni aziendali.** 
>
> Se l'applicazione è stata **registrata Registrazioni app,** la funzionalità Single Sign-On è configurata per usare OAuth OIDC per impostazione predefinita. In questo caso, **l'opzione Single Sign-On** non verrà mostrata nel riquadro di spostamento in **Applicazioni aziendali.** Quando si usa **Registrazioni app** per aggiungere l'app personalizzata, si configurano le opzioni nel file manifesto. Per altre informazioni sul file manifesto, vedere l'Azure Active Directory [dell'app.](../develop/reference-app-manifest.md) Per altre informazioni sugli standard SSO, vedere [Autenticazione e autorizzazione con Microsoft Identity Platform.](../develop/authentication-vs-authorization.md#authentication-and-authorization-using-the-microsoft-identity-platform) 
>
> Altri scenari in cui **l'accesso Single Sign-On** non è presente nella navigazione includono quando un'applicazione è ospitata in un altro tenant o se l'account non dispone delle autorizzazioni necessarie (amministratore globale, amministratore dell'applicazione cloud, amministratore dell'applicazione o proprietario dell'entità servizio). Le autorizzazioni possono anche causare uno scenario in cui è possibile aprire **l'accesso Single Sign-On,** ma non sarà possibile salvarlo. Per altre informazioni sui Azure AD amministrativi, vedere ( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) .


## <a name="basic-configuration"></a>Configurazione di base

Nella serie [di guide introduttive](view-applications-portal.md)si è appreso come aggiungere un'app al tenant, che consente a Azure AD di sapere che viene usata come provider di identità (IdP) per l'app. Alcune app sono già preconfigurato e vengono Azure AD raccolta. Altre app non sono presenti nella raccolta ed è necessario creare un'app generica e configurarla manualmente. A seconda dell'app, l'opzione SSO basata su password potrebbe non essere disponibile. Se l'elenco delle opzioni basate su password non viene visualizzato nella pagina Single Sign-On per l'app, non è disponibile.

> [!IMPORTANT]
> L App personali'estensione del browser è necessaria per l'accesso SSO basato su password. Per altre informazioni, vedere [Pianificare una distribuzione App personali distribuzione.](my-apps-deployment-plan.md)

La pagina di configurazione per l'accesso SSO basato su password è semplice. Include solo l'URL della pagina di accesso utilizzata dall'app. Questa stringa deve essere la pagina che include il campo di input del nome utente.

Dopo aver immesso l'URL, selezionare **Salva**. Azure AD analizza il codice HTML della pagina di accesso per i campi di input del nome utente e della password. Se il tentativo ha esito positivo, l'operazione è stata completata.
 
Il passaggio successivo consiste [nell'assegnare utenti o gruppi all'applicazione](./assign-user-or-group-access-portal.md). Dopo aver assegnato utenti e gruppi, è possibile specificare le credenziali da usare per un utente quando accede all'applicazione. Selezionare **Utenti e gruppi,** selezionare la casella di controllo per la riga dell'utente o del gruppo e quindi **selezionare Aggiorna credenziali**. Immettere infine il nome utente e la password da usare per l'utente o il gruppo. In caso contrario, agli utenti verrà richiesto di immettere le credenziali all'avvio.
 

## <a name="manual-configuration"></a>Configurazione manuale

Se Azure AD tentativo di analisi non riesce, è possibile configurare manualmente l'accesso.

1. In **\<application name> Configurazione** selezionare **Configura impostazioni di Accesso Single \<application name> Sign-On** per la password per visualizzare **la pagina Configura accesso.** 

2. Selezionare **Rileva manualmente i campi di accesso**. Vengono visualizzate istruzioni aggiuntive che descrivono il rilevamento manuale dei campi di accesso.

   ![Configurazione manuale dell'accesso Single Sign-On basato su password](./media/configure-password-single-sign-on/password-configure-sign-on.png)
3. Selezionare **Acquisisci campi di accesso**. Viene visualizzata una pagina di stato di acquisizione in una nuova scheda che mostra che l'acquisizione dei metadati del **messaggio è attualmente in corso.**

4. Se la **App personali richiesta** di estensione per l'accesso sicuro viene visualizzata in una nuova scheda, selezionare Installa ora per installare l App personali del browser  **dell'estensione** per l'accesso sicuro. L'estensione del browser richiede Microsoft Edge, Chrome o Firefox. Quindi installare, avviare e abilitare l'estensione e aggiornare la pagina dello stato di acquisizione.

   L'estensione del browser apre quindi un'altra scheda che visualizza l'URL immesso.
5. Nella scheda con l'URL immesso eseguire il processo di accesso. Compilare i campi nome utente e password e provare ad accedere. Non è necessario specificare la password corretta.

   Viene chiesto di salvare i campi di accesso acquisiti.
6. Selezionare **OK**. L'estensione del browser aggiorna la pagina dello stato di acquisizione con il messaggio **Metadati aggiornati per l'applicazione**. La scheda del browser viene chiusa.

7. Nella pagina Azure AD **Configura accesso** selezionare OK. È stato possibile accedere **all'app correttamente.**

8. Selezionare **OK**.

## <a name="next-steps"></a>Passaggi successivi

- [Assegnare utenti o gruppi all'applicazione](./assign-user-or-group-access-portal.md)
- [Configurare il provisioning automatico degli account utente](../app-provisioning/configure-automatic-user-provisioning-portal.md)
