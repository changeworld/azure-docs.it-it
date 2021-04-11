---
title: Configurare l'autenticazione di Twitter
description: Informazioni su come configurare l'autenticazione di Twitter come provider di identità per il servizio app o l'app funzioni di Azure.
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.topic: article
ms.date: 03/29/2021
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: 6ecce954991d9f3901c54a6f87fc803b32469862
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077976"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-twitter-login"></a>Configurare il servizio app o l'app funzioni di Azure per usare l'account di accesso di Twitter

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Questo articolo illustra come configurare app Azure servizio o funzioni di Azure per usare Twitter come provider di autenticazione.

Per completare la procedura descritta in questo articolo, è necessario un account Twitter con un indirizzo di posta elettronica verificato e un numero di telefono. Per creare un nuovo account Twitter, visitare il sito Web all'indirizzo [twitter.com].

## <a name="register-your-application-with-twitter"></a><a name="register"> </a>Registrare l'applicazione con Twitter

1. Accedere al [portale di Azure] e passare all'applicazione. Copiare l' **URL**. Verrà usato per configurare l'app Twitter.
1. Visitare il sito Web degli [sviluppatori di Twitter] , accedere con le credenziali dell'account Twitter e selezionare **Crea un'app**.
1. Immettere il **nome dell'app** e la **Descrizione dell'applicazione** per la nuova app. Incollare l' **URL** dell'applicazione nel campo **URL del sito Web** . Nella sezione **URL di callback** immettere l'URL HTTPS dell'app del servizio app e aggiungere il percorso `/.auth/login/twitter/callback` . Ad esempio: `https://contoso.azurewebsites.net/.auth/login/twitter/callback`.
1. Nella parte inferiore della pagina digitare almeno 100 caratteri in **indicare come verrà usata questa app**, quindi selezionare **Crea**. Fare di nuovo clic su **Crea** nella finestra popup. Verranno visualizzati i dettagli dell'applicazione.
1. Selezionare la scheda **Keys and Access Tokens** .

   Prendere nota di questi valori:
   - Chiave API
   - Chiave privata API

   > [!IMPORTANT]
   > La chiave privata API è una credenziale di sicurezza importante. Non condividere questo valore con altri né distribuirlo con l'app.

## <a name="add-twitter-information-to-your-application"></a><a name="secrets"> </a>Aggiungere informazioni di Twitter all'applicazione

1. Accedere al [portale di Azure] e passare all'app.
1. Selezionare **autenticazione** nel menu a sinistra. Fare clic su **Aggiungi provider di identità**.
1. Selezionare **Twitter** nell'elenco a discesa provider di identità. Incollare i `API key` valori e `API secret key` ottenuti in precedenza.

    Il segreto verrà archiviato come [impostazione dell'applicazione](./configure-common.md#configure-app-settings) con slot-Sticky denominata `TWITTER_PROVIDER_AUTHENTICATION_SECRET` . È possibile aggiornare tale impostazione in un secondo momento per usare i [riferimenti Key Vault](./app-service-key-vault-references.md) se si vuole gestire il segreto in Azure Key Vault.

1. Se questo è il primo provider di identità configurato per l'applicazione, verrà visualizzata anche una sezione **impostazioni di autenticazione del servizio app** . In caso contrario, è possibile passare al passaggio successivo.
    
    Queste opzioni determinano il modo in cui l'applicazione risponde alle richieste non autenticate e le selezioni predefinite reindirizza tutte le richieste di accesso con questo nuovo provider. È possibile modificare la personalizzazione di questo comportamento ora oppure modificare le impostazioni in un secondo momento dalla schermata principale di **autenticazione** scegliendo **modifica** accanto a **impostazioni di autenticazione**. Per altre informazioni su queste opzioni, vedere [flusso di autenticazione](overview-authentication-authorization.md#authentication-flow).

1. Fare clic su **Aggiungi**.

A questo punto si è pronti per usare Twitter per l'autenticazione nell'app. Il provider verrà elencato nella schermata **Authentication** . Da qui è possibile modificare o eliminare questa configurazione del provider.

## <a name="next-steps"></a><a name="related-content"> </a>Passaggi successivi

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[sviluppatori Twitter]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[twitter.com]: https://go.microsoft.com/fwlink/p/?LinkID=268287
[Azure portal]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
