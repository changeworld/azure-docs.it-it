---
title: Configurare l'autenticazione di Google
description: Informazioni su come configurare l'autenticazione di Google come provider di identità per il servizio app o l'app funzioni di Azure.
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.topic: article
ms.date: 03/29/2021
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: f6bec32fa928e840569ed95c35a056db91ea9737
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077993"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-google-login"></a>Configurare il servizio app o l'app funzioni di Azure per usare l'account di accesso di Google

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Questo argomento illustra come configurare app Azure servizio o funzioni di Azure per usare Google come provider di autenticazione.

Per completare la procedura descritta in questo argomento, è necessario avere un account Google con un indirizzo di posta elettronica verificato. Per creare un nuovo account Google, visitare il sito Web all'indirizzo [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).

## <a name="register-your-application-with-google"></a><a name="register"> </a>Registrare l'applicazione con Google

1. Per creare un ID client e un segreto client, seguire la documentazione di Google in [google Sign-In per le app sul lato server](https://developers.google.com/identity/sign-in/web/server-side-flow) . Non è necessario apportare modifiche al codice. Usare semplicemente le seguenti informazioni:
    - Per le **origini JavaScript autorizzate**, usare `https://<app-name>.azurewebsites.net` con il nome dell'app in *\<app-name>* .
    - Per l' **URI di reindirizzamento autorizzato**, usare `https://<app-name>.azurewebsites.net/.auth/login/google/callback` .
1. Copiare i valori di ID app e segreto app.

    > [!IMPORTANT]
    > Il segreto dell'app è una credenziale di sicurezza importante. Non condividere questo valore con altri e non distribuirlo all'interno di un'applicazione client.

## <a name="add-google-information-to-your-application"></a><a name="secrets"> </a>Aggiungere le informazioni di Google all'applicazione

1. Accedere al [portale di Azure] e passare all'app.
1. Selezionare **autenticazione** nel menu a sinistra. Fare clic su **Aggiungi provider di identità**.
1. Selezionare **Google** nell'elenco a discesa provider di identità. Incollare i valori di ID app e segreto app ottenuti in precedenza.

    Il segreto verrà archiviato come [impostazione dell'applicazione](./configure-common.md#configure-app-settings) con slot-Sticky denominata `GOOGLE_PROVIDER_AUTHENTICATION_SECRET` . È possibile aggiornare tale impostazione in un secondo momento per usare i [riferimenti Key Vault](./app-service-key-vault-references.md) se si vuole gestire il segreto in Azure Key Vault.

1. Se questo è il primo provider di identità configurato per l'applicazione, verrà visualizzata anche una sezione **impostazioni di autenticazione del servizio app** . In caso contrario, è possibile passare al passaggio successivo.
    
    Queste opzioni determinano il modo in cui l'applicazione risponde alle richieste non autenticate e le selezioni predefinite reindirizza tutte le richieste di accesso con questo nuovo provider. È possibile modificare la personalizzazione di questo comportamento ora oppure modificare le impostazioni in un secondo momento dalla schermata principale di **autenticazione** scegliendo **modifica** accanto a **impostazioni di autenticazione**. Per altre informazioni su queste opzioni, vedere [flusso di autenticazione](overview-authentication-authorization.md#authentication-flow).

1. Opzionale Fare clic su **Avanti: ambiti** e aggiungere gli ambiti necessari per l'applicazione. Questi verranno richiesti al momento dell'accesso per i flussi basati sul browser.
1. Fare clic su **Aggiungi**.

È ora possibile usare un account Google per l'autenticazione nell'app. Il provider verrà elencato nella schermata **Authentication** . Da qui è possibile modificare o eliminare questa configurazione del provider.

## <a name="next-steps"></a><a name="related-content"> </a>Passaggi successivi

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure portal]: https://portal.azure.com/

