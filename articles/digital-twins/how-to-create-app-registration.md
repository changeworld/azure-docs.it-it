---
title: Creare la registrazione di un'app
titleSuffix: Azure Digital Twins
description: Informazioni su come creare una registrazione Azure AD'app, come opzione di autenticazione per le app client.
author: baanders
ms.author: baanders
ms.date: 10/13/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: bd45bb264f8e29a2aad870a7daff45fdd44e0d3c
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478812"
---
# <a name="create-an-app-registration-to-use-with-azure-digital-twins"></a>Creare una registrazione dell'app da usare con Gemelli digitali di Azure

Quando si lavora con un'istanza Gemelli digitali di Azure, è comune interagire con tale istanza tramite applicazioni client, ad esempio un'app client personalizzata o un esempio [come Azure Digital Twins Explorer](quickstart-azure-digital-twins-explorer.md). Queste applicazioni devono eseguire l'autenticazione con Gemelli digitali di Azure per interagire con [](how-to-authenticate-client.md) esso e alcuni dei meccanismi di autenticazione che le app possono usare comportano la registrazione di **un'app** [Azure Active Directory (Azure AD).](../active-directory/fundamentals/active-directory-whatis.md)

Questa operazione non è necessaria per tutti gli scenari di autenticazione. Tuttavia, se si usa una strategia di autenticazione o un esempio di codice che richiede una registrazione dell'app, inclusi un **ID client** e un **ID tenant,** questo articolo illustra come configurarne uno.

## <a name="using-azure-ad-app-registrations"></a>Uso Azure AD registrazioni di app

[Azure Active Directory (Azure AD) è il](../active-directory/fundamentals/active-directory-whatis.md) servizio microsoft di gestione delle identità e degli accessi basato sul cloud. La configurazione della registrazione **di un'app** in Azure AD è un modo per concedere a un'app client l'accesso Gemelli digitali di Azure.

In questa registrazione dell'app è possibile configurare le autorizzazioni di accesso [Gemelli digitali di Azure API .](how-to-use-apis-sdks.md) In un secondo momento, le app client possono eseguire l'autenticazione in base alla registrazione dell'app usando i valori **di ID client** e tenant della registrazione e, di conseguenza, le autorizzazioni di accesso configurate per le API.

>[!TIP]
> È preferibile configurare una nuova registrazione dell'app  ogni volta che è necessaria oppure, per eseguire questa operazione una sola volta, stabilire una singola registrazione dell'app che verrà condivisa tra tutti gli scenari che la richiedono.

## <a name="create-the-registration"></a>Creare la registrazione

Per iniziare, [passare](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) Azure Active Directory nella portale di Azure (è possibile usare questo collegamento o trovarlo con la barra di ricerca del portale). Selezionare *Registrazioni app* dal menu del servizio e quindi *+ Nuova registrazione.*

:::image type="content" source="media/how-to-create-app-registration/new-registration.png" alt-text="Visualizzazione della pagina Azure AD servizio nel portale di Azure, evidenziando l'opzione di menu &quot;Registrazioni app&quot; e il pulsante &quot;+ Nuova registrazione&quot;":::

Nella pagina *Registra un'applicazione* immettere i valori richiesti:
* **Nome:** Azure AD nome visualizzato dell'applicazione da associare alla registrazione
* **Tipi di account supportati:** selezionare *Account solo in questa directory organizzativa (solo directory predefinita - tenant singolo)*
* **URI di reindirizzamento:** *AZURE AD url di risposta dell'applicazione* per l Azure AD app. Aggiungere un *URI client/nativo pubblico (& desktop) per* `http://localhost` .

Al termine, fare clic sul *pulsante* Registra.

:::image type="content" source="media/how-to-create-app-registration/register-an-application.png" alt-text="Visualizzazione della pagina &quot;Registra un'applicazione&quot; con i valori descritti compilati":::

Al termine della configurazione della registrazione, il portale reindirizza l'utente alla pagina dei dettagli.

## <a name="collect-client-id-and-tenant-id"></a>Raccogliere l'ID client e l'ID tenant

Successivamente, raccogliere alcuni valori importanti sulla registrazione dell'app dalla pagina dei dettagli:

:::image type="content" source="media/how-to-create-app-registration/app-important-values.png" alt-text="Visualizzazione portale dei valori importanti per la registrazione dell'app":::

Prendere nota _**dell'ID applicazione (client)**_ _**e dell'ID directory (tenant)**_ visualizzati **nella** pagina. Questi sono i valori che un'app client dovrà usare questa registrazione per eseguire l'autenticazione con Gemelli digitali di Azure.

## <a name="provide-azure-digital-twins-api-permission"></a>Fornire l Gemelli digitali di Azure apisto API

Configurare quindi la registrazione dell'app creata con le autorizzazioni di base per Gemelli digitali di Azure API.

Nella pagina del portale per la registrazione dell'app selezionare *Autorizzazioni API* dal menu. Nella pagina autorizzazioni seguente fare clic sul *pulsante + Aggiungi un'autorizzazione.*

:::image type="content" source="media/how-to-create-app-registration/add-permission.png" alt-text="Visualizzazione della registrazione dell'app nel portale di Azure, evidenziando l'opzione di menu &quot;Autorizzazioni API&quot; e il pulsante &quot;+ Aggiungi un'autorizzazione&quot;":::

Nella pagina *Richiedi autorizzazioni API* che segue passare alle *API* che l'organizzazione usa e cercare gemelli digitali *di Azure.* Selezionare _**Gemelli digitali di Azure**_ dai risultati della ricerca per procedere con l'assegnazione delle autorizzazioni per Gemelli digitali di Azure api.

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1.png" alt-text="Visualizzazione del risultato della ricerca nella pagina &quot;Richiedi autorizzazioni API&quot; con Gemelli digitali di Azure, con ID applicazione (client) 0b07f429-9f4b-4714-9392-cc5e8e80c8b0.":::

>[!NOTE]
> Se la sottoscrizione ha ancora un'istanza di Gemelli digitali di Azure esistente dall'anteprima pubblica precedente del servizio (prima di luglio 2020), sarà invece necessario cercare e selezionare Servizio _**Azure Smart Spaces.**_ Si tratta di un nome precedente per lo stesso set di API (si noti che l'ID applicazione *(client)* è lo stesso dello screenshot precedente) e l'esperienza non verrà modificata oltre questo passaggio.
> :::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1-smart-spaces.png" alt-text="Visualizzazione del risultato della ricerca della pagina &quot;Richiedi autorizzazioni API&quot; che mostra il servizio Azure Smart Spaces":::

Successivamente, si selezioneranno le autorizzazioni da concedere per queste API. Espandere **l'autorizzazione Lettura (1) e selezionare** la casella *Read.Write* per concedere le autorizzazioni di lettura e scrittura per la registrazione dell'app.

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-2.png" alt-text="Visualizzazione della pagina &quot;Richiedi autorizzazioni API&quot; selezionando le autorizzazioni &quot;Read.Write&quot; per le API Gemelli digitali di Azure":::

Al *termine, fare clic* su Aggiungi autorizzazioni.

### <a name="verify-success"></a>Verificare l'esito positivo

Nella pagina *Autorizzazioni API* verificare che sia ora presente una voce per l'Gemelli digitali di Azure che riflette le autorizzazioni di lettura/scrittura:

:::image type="content" source="media/how-to-create-app-registration/verify-api-permissions.png" alt-text="Visualizzazione del portale delle autorizzazioni API per la registrazione dell Azure AD app, che mostra &quot;Accesso in lettura/scrittura&quot; per Gemelli digitali di Azure":::

È anche possibile verificare la connessione Gemelli digitali di Azure all'interno del *manifest.js* della registrazione dell'app in , che è stato aggiornato automaticamente con le informazioni Gemelli digitali di Azure quando sono state aggiunte le autorizzazioni api.

A tale scopo, selezionare *Manifesto dal* menu per visualizzare il codice manifesto della registrazione dell'app. Scorrere fino alla fine della finestra del codice e cercare questi campi in `requiredResourceAccess` . I valori devono corrispondere a quelli nello screenshot seguente:

:::image type="content" source="media/how-to-create-app-registration/verify-manifest.png" alt-text="Visualizzazione portale del manifesto per la registrazione dell Azure AD app. Annidato in 'requiredResourceAccess', è presente un valore 'resourceAppId' pari a 0b07f429-9f4b-4714-9392-cc5e8e80c8b0 e un valore &quot;resourceAccess > id&quot; pari a 4589bd03-58cb-4e6c-b17f-b580e39652f8":::

Se questi valori non sono presenti, ripetere i passaggi nella sezione [per aggiungere l'autorizzazione API](#provide-azure-digital-twins-api-permission).

## <a name="other-possible-steps-for-your-organization"></a>Altri passaggi possibili per l'organizzazione

È possibile che l'organizzazione richieda azioni aggiuntive da parte dei proprietari/amministratori della sottoscrizione per configurare correttamente la registrazione di un'app. I passaggi necessari possono variare a seconda delle impostazioni specifiche dell'organizzazione.

Di seguito sono riportate alcune possibili attività comuni che un proprietario/amministratore della sottoscrizione potrebbe dover eseguire. Queste e altre operazioni possono essere eseguite dalla pagina App Azure AD [*registrazioni*](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) nel portale di Azure.
* Concedere il consenso dell'amministratore per la registrazione dell'app. L'organizzazione potrebbe avere *il consenso amministratore* richiesto a livello globale attivato a livello Azure AD per tutte le registrazioni dell'app all'interno della sottoscrizione. In tal caso, il proprietario/amministratore dovrà selezionare questo pulsante per l'azienda nella pagina delle autorizzazioni *api* della registrazione dell'app per la validità della registrazione dell'app:

    :::image type="content" source="media/how-to-create-app-registration/grant-admin-consent.png" alt-text="Visualizzazione portale del pulsante &quot;Concedi consenso amministratore&quot; in Autorizzazioni API":::
  - Se il consenso è stato concesso correttamente, la voce per Gemelli digitali di Azure dovrebbe quindi visualizzare il valore *Stato* _concesso per **(società)**_
   
    :::image type="content" source="media/how-to-create-app-registration/granted-admin-consent-done.png" alt-text="Visualizzazione del portale del consenso dell'amministratore concesso per l'azienda con autorizzazioni API":::
* Attivare l'accesso client pubblico
* Impostare URL di risposta specifici per l'accesso Web e desktop
* Consenti flussi di autenticazione OAuth2 impliciti

Per altre informazioni sulla registrazione delle app e sulle diverse opzioni di configurazione, vedere [*Registrare un'applicazione con Microsoft Identity Platform.*](/graph/auth-register-app-v2)

## <a name="next-steps"></a>Passaggi successivi

In questo articolo viene impostata una registrazione dell Azure AD app che può essere usata per autenticare le applicazioni client con le API Gemelli digitali di Azure client.

Leggere quindi i meccanismi di autenticazione, tra cui uno che usa le registrazioni delle app e altri che non lo fanno:
* [*Procedura: Scrivere il codice di autenticazione dell'app*](how-to-authenticate-client.md)