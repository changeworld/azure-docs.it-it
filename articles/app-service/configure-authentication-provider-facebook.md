---
title: Configurare l'autenticazione di Facebook
description: Informazioni su come configurare l'autenticazione di Facebook come provider di identità per il servizio app o l'app funzioni di Azure.
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.topic: article
ms.date: 03/29/2021
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: bc639eaea76b3309d6ed047e73c726040da19639
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078010"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-facebook-login"></a>Configurare il servizio app o l'app funzioni di Azure per usare l'account di accesso di Facebook

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Questo articolo illustra come configurare app Azure servizio o funzioni di Azure per usare Facebook come provider di autenticazione.

Per completare la procedura descritta in questo articolo, è necessario un account Facebook con un indirizzo di posta elettronica verificato e un numero di telefono cellulare. Per creare un nuovo account di Facebook, visitare il sito [facebook.com].

## <a name="register-your-application-with-facebook"></a><a name="register"> </a>Registrare l'applicazione con Facebook

1. Visitare il sito Web [Facebook Developers] e accedere con le credenziali dell'account Facebook.

   Se non si dispone di un account Facebook for Developers **, selezionare inizia** e seguire la procedura di registrazione.
1. Selezionare **app personali**  >  **Aggiungi nuova app**.
1. Nel campo **nome visualizzato** :
   1. Digitare un nome univoco per l'app.
   1. Fornire il proprio **indirizzo di posta elettronica di contatto**.
   1. Selezionare **Crea ID app**.
   1. Completare il controllo di sicurezza.

   Verrà visualizzato il dashboard per sviluppatori per la nuova app Facebook.
1. Selezionare **Dashboard**  >  **Facebook login**  >  **set up**  >  **Web**.
1. Nel percorso di spostamento a sinistra in **account di accesso di Facebook** selezionare **Impostazioni**.
1. Nel campo **validi URI di reindirizzamento OAuth** immettere `https://<app-name>.azurewebsites.net/.auth/login/facebook/callback` . Ricordarsi di sostituire `<app-name>` con il nome dell'app di servizio app Azure.
1. Selezionare **Save changes** (Salva modifiche).
1. Nel riquadro sinistro selezionare impostazioni di   >  **base**. 
1. Nel campo **Secret app** selezionare **show**. Copiare i valori di **ID app** e **segreto app**. Vengono usati in un secondo momento per configurare l'app del servizio app in Azure.

   > [!IMPORTANT]
   > Il segreto dell'app è una credenziale di sicurezza importante. Non condividere questo valore con altri e non distribuirlo all'interno di un'applicazione client.
   >

1. L'account Facebook usato per registrare l'applicazione è un amministratore dell'app. A questo punto, solo gli amministratori possono accedere a questa applicazione.

   Per autenticare altri account Facebook, selezionare **Verifica app** e abilitare **Rendi \<your-app-name> pubblico** per consentire al pubblico generale di accedere all'app usando l'autenticazione di Facebook.

## <a name="add-facebook-information-to-your-application"></a><a name="secrets"> </a>Aggiungere le informazioni di Facebook all'applicazione

1. Accedere al [portale di Azure] e passare all'app.
1. Selezionare **autenticazione** nel menu a sinistra. Fare clic su **Aggiungi provider di identità**.
1. Selezionare **Facebook** nell'elenco a discesa provider di identità. Incollare i valori di ID app e segreto app ottenuti in precedenza.

    Il segreto verrà archiviato come [impostazione dell'applicazione](./configure-common.md#configure-app-settings) con slot-Sticky denominata `FACEBOOK_PROVIDER_AUTHENTICATION_SECRET` . È possibile aggiornare tale impostazione in un secondo momento per usare i [riferimenti Key Vault](./app-service-key-vault-references.md) se si vuole gestire il segreto in Azure Key Vault.

1. Se questo è il primo provider di identità configurato per l'applicazione, verrà visualizzata anche una sezione **impostazioni di autenticazione del servizio app** . In caso contrario, è possibile passare al passaggio successivo.
    
    Queste opzioni determinano il modo in cui l'applicazione risponde alle richieste non autenticate e le selezioni predefinite reindirizza tutte le richieste di accesso con questo nuovo provider. È possibile modificare la personalizzazione di questo comportamento ora oppure modificare le impostazioni in un secondo momento dalla schermata principale di **autenticazione** scegliendo **modifica** accanto a **impostazioni di autenticazione**. Per altre informazioni su queste opzioni, vedere [flusso di autenticazione](overview-authentication-authorization.md#authentication-flow).

1. Opzionale Fare clic su **Avanti: ambiti** e aggiungere gli ambiti necessari per l'applicazione. Questi verranno richiesti al momento dell'accesso per i flussi basati sul browser.
1. Fare clic su **Aggiungi**.

A questo punto si è pronti per usare Facebook per l'autenticazione nell'app. Il provider verrà elencato nella schermata **Authentication** . Da qui è possibile modificare o eliminare questa configurazione del provider.

## <a name="next-steps"></a><a name="related-content"> </a>Passaggi successivi

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->
[Facebook Developers]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure portal]: https://portal.azure.com/
