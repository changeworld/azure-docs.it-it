---
title: Il messaggio di errore viene visualizzato nella pagina dell'app dopo l'accesso | Microsoft Docs
description: Come risolvere i problemi relativi al Azure AD accesso quando l'app restituisce un messaggio di errore.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: iangithinji
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ddebc4778d923bc3a002f14fc4b4db1b7bb730d
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379299"
---
# <a name="an-app-page-shows-an-error-message-after-the-user-signs-in"></a>Una pagina dell'app contiene un messaggio di errore dopo l'accesso dell'utente

In questo scenario, Azure Active Directory (Azure AD) l'accesso dell'utente. Ma l'applicazione visualizza un messaggio di errore e non consente all'utente di completare il flusso di accesso. Il problema è che l'app non ha accettato la risposta Azure AD rilasciata.

Esistono diversi motivi per cui l'app non ha accettato la risposta dal Azure AD. Se il messaggio di errore non identifica chiaramente gli elementi mancanti nella risposta, provare a eseguire le operazioni seguenti:

-   Se l'app è la raccolta Azure AD, verificare di aver seguito i passaggi descritti in Come eseguire il debug dell'accesso [Single Sign-On](./debug-saml-sso-issues.md)basato su SAML alle applicazioni in Azure AD .

-   Usare uno strumento come [Fiddler per](https://www.telerik.com/fiddler) acquisire la richiesta, la risposta e il token SAML.

-   Inviare la risposta SAML al fornitore dell'app e chiedere cosa manca.

## <a name="attributes-are-missing-from-the-saml-response"></a>Attributi mancanti nella risposta SAML

Per aggiungere un attributo nella Azure AD che verrà inviato nella risposta Azure AD, seguire questa procedura:

1. Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come amministratore globale o co-amministratore.

2. Nella parte superiore del riquadro di spostamento a sinistra selezionare **Tutti** i servizi per aprire l'Azure AD predefinita.

3. Digitare **Azure Active Directory** nella casella di ricerca del filtro e quindi **selezionare Azure Active Directory**.

4. Selezionare **Applicazioni aziendali** nel riquadro Azure AD di spostamento.

5. Selezionare **Tutte le** applicazioni per visualizzare un elenco delle app.

   > [!NOTE]
   > Se l'app desiderata non è visualizzata, usare il **controllo** Filtro nella parte superiore dell'elenco **Tutte le applicazioni**. Impostare **l'opzione** Mostra su "Tutte le applicazioni".

6. Selezionare l'applicazione che si vuole configurare per l'accesso Single Sign-On.

7. Dopo il caricamento dell'app, **selezionare Single Sign-On** nel riquadro di spostamento.

8. Nella sezione **Attributi utente** selezionare Visualizza e modifica tutti gli altri **attributi utente.** Qui è possibile modificare gli attributi da inviare all'app nel token SAML quando gli utenti effettuano l'accesso.

   Per aggiungere un attributo:

   1. Selezionare **Aggiungi attributo**. Immettere Il **nome** e selezionare il **valore** nell'elenco a discesa.

   1.  Selezionare **Salva**. Il nuovo attributo verrà visualizzato nella tabella.

9. Salvare la configurazione.

   Al successivo accesso dell'utente all'app, Azure AD il nuovo attributo nella risposta SAML.

## <a name="the-app-doesnt-identify-the-user"></a>L'app non identifica l'utente

L'accesso all'app ha esito negativo perché nella risposta SAML manca un attributo, ad esempio un ruolo. Oppure non riesce perché l'app prevede un formato o un valore diverso per l'attributo **NameID** (Identificatore utente).

Se si usa il [provisioning](../app-provisioning/user-provisioning.md) Azure AD utenti automatizzato per creare, gestire e rimuovere utenti nell'app, verificare che sia stato effettuato il provisioning dell'utente nell'app SaaS. Per altre informazioni, vedere Nessun utente viene effettuato [il provisioning in un'applicazione Azure AD Gallery](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md).

## <a name="add-an-attribute-to-the-azure-ad-app-configuration"></a>Aggiungere un attributo alla configurazione dell Azure AD app

Per modificare il valore dell'ID utente, seguire questa procedura:

1. Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come amministratore globale o co-amministratore.

2. Selezionare **Tutti i servizi** nella parte superiore del riquadro di spostamento a sinistra per aprire l'Azure AD predefinita.

3. Digitare **Azure Active Directory** nella casella di ricerca del filtro e quindi selezionare **Azure Active Directory**.

4. Selezionare **Applicazioni aziendali** nel riquadro Azure AD di spostamento.

5. Selezionare **Tutte le applicazioni** per visualizzare un elenco delle app.

   > [!NOTE]
   > Se l'app desiderata non è visualizzata, usare il **controllo** Filtro nella parte superiore dell'elenco **Tutte le applicazioni**. Impostare **l'opzione** Mostra su "Tutte le applicazioni".

6. Selezionare l'app che si vuole configurare per l'accesso SSO.

7. Dopo il caricamento dell'app, **selezionare Single Sign-On** nel riquadro di spostamento.

8. In **Attributi utente** selezionare l'identificatore univoco per l'utente dall'elenco a discesa Identificatore utente. 

## <a name="change-the-nameid-format"></a>Modificare il formato NameID

Se l'applicazione prevede un altro formato per l'attributo **NameID** (Identificatore utente), vedere [Modifica di nameID](../develop/active-directory-saml-claims-customization.md#editing-nameid) per modificare il formato nameID.

Azure AD seleziona il formato per l'attributo **NameID** (Identificatore utente) in base al valore selezionato o al formato richiesto dall'app in SAML AuthRequest. Per altre informazioni, vedere la sezione "NameIDPolicy" del [protocollo SAML per l'accesso Single Sign-On.](../develop/single-sign-on-saml-protocol.md#nameidpolicy)

## <a name="the-app-expects-a-different-signature-method-for-the-saml-response"></a>L'app prevede un metodo di firma diverso per la risposta SAML

Per modificare le parti del token SAML firmate digitalmente da Azure AD, seguire questa procedura:

1. Aprire il [portale di Azure](https://portal.azure.com/) e accedere come amministratore globale o co-amministratore.

2. Selezionare **Tutti i** servizi nella parte superiore del riquadro di spostamento a sinistra per aprire l'Azure AD predefinita.

3. Digitare **Azure Active Directory** nella casella di ricerca del filtro e quindi **selezionare Azure Active Directory**.

4. Selezionare **Applicazioni aziendali** nel riquadro Azure AD di spostamento.

5. Selezionare **Tutte le** applicazioni per visualizzare un elenco delle app.

   > [!NOTE]
   > Se l'applicazione desiderata non è visualizzata, usare il **controllo** Filtro nella parte superiore dell'elenco **Tutte le applicazioni**. Impostare **l'opzione** Mostra su "Tutte le applicazioni".

6. Selezionare l'applicazione che si vuole configurare per l'accesso Single Sign-On.

7. Dopo il caricamento dell'applicazione, **selezionare Single Sign-On** nel riquadro di spostamento.

8. In **Certificato di firma SAML** selezionare Mostra impostazioni avanzate per la firma del **certificato.**

9. Selezionare **l'opzione di** firma prevista dall'app tra queste opzioni:

   * **Firmare la risposta SAML**
   * **Firmare la risposta e l'asserzione SAML**
   * **Firmare l'asserzione SAML**

   Al successivo accesso dell'utente all'app, Azure AD la parte della risposta SAML selezionata.

## <a name="the-app-expects-the-sha-1-signing-algorithm"></a>L'app prevede l'algoritmo di firma SHA-1

Per impostazione predefinita, Azure AD il token SAML usando l'algoritmo più sicuro. È consigliabile non modificare l'algoritmo di firma in *SHA-1* a meno che l'app non richieda SHA-1.

Per modificare l'algoritmo di firma, seguire questa procedura:

1. Aprire il [portale di Azure](https://portal.azure.com/) e accedere come amministratore globale o co-amministratore.

2. Selezionare **Tutti i servizi** nella parte superiore del riquadro di spostamento a sinistra per aprire l'Azure AD predefinita.

3. Digitare **Azure Active Directory** nella casella di ricerca del filtro e quindi selezionare **Azure Active Directory**.

4. Selezionare **Applicazioni aziendali** nel riquadro Azure AD di spostamento.

5. Selezionare **Tutte le applicazioni** per visualizzare un elenco delle applicazioni.

   > [!NOTE]
   > Se l'applicazione desiderata non è visualizzata, usare il **controllo** Filtro nella parte superiore dell'elenco **Tutte le applicazioni**. Impostare **l'opzione** Mostra su "Tutte le applicazioni".

6. Selezionare l'app che si vuole configurare per l'accesso Single Sign-On.

7. Dopo il caricamento dell'app, selezionare **Single Sign-On** nel riquadro di spostamento sul lato sinistro dell'app.

8. In **Certificato di firma SAML** selezionare Mostra impostazioni avanzate per la firma del **certificato.**

9. Selezionare **SHA-1** come **Algoritmo di firma**.

   Al successivo accesso dell'utente all'app, Azure AD il token SAML usando l'algoritmo SHA-1.

## <a name="next-steps"></a>Passaggi successivi
[Come eseguire il debug dell'accesso Single Sign-On](./debug-saml-sso-issues.md)basato su SAML alle applicazioni in Azure AD .