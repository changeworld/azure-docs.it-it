---
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: erhopf
ms.openlocfilehash: a3345ff9a6cc1d5f8e2fbc78c2a76ba6f183aeb6
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102620084"
---
## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services)
* Una risorsa dello strumento di lettura immersiva configurata per l'autenticazione Azure Active Directory. Seguire [queste istruzioni](../../how-to-create-immersive-reader.md) per configurare l'ambiente.  Per la configurazione delle proprietà dell'ambiente, saranno necessari alcuni dei valori creati qui. Salvare l'output della sessione in un file di testo per riferimento futuro.
* [macOS](https://www.apple.com/macos).
* [Git](https://git-scm.com/).
* [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk).
* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12).

## <a name="configure-authentication-credentials"></a>Configurare le credenziali di autenticazione

1. In Xcode aprire il progetto **immersive-reader-sdk/js/samples/ios/quickstart-swift/quickstart-swift.xcodeproj**.
1. Nel menu in alto selezionare **Prodotto** > **Schema** > **Modifica schema**.
1. Nella visualizzazione **Esegui** selezionare la scheda **Argomenti**.
1. Nella sezione **Variabili di ambiente** aggiungere i nomi e i valori seguenti. Specificare i valori forniti quando è stata creata la risorsa dello strumento di lettura immersiva.

    ```text
    TENANT_ID=<YOUR_TENANT_ID>
    CLIENT_ID=<YOUR_CLIENT_ID>
    CLIENT_SECRET<YOUR_CLIENT_SECRET>
    SUBDOMAIN=<YOUR_SUBDOMAIN>
    ```

Non eseguire il commit di questa modifica nel controllo del codice sorgente, perché contiene segreti che non devono essere resi pubblici.

## <a name="start-the-immersive-reader-with-sample-content"></a>Avviare lo strumento di lettura immersiva con contenuto di esempio

In Xcode premere **CTRL+R** per eseguire il progetto.

## <a name="next-steps"></a>Passaggi successivi

* Esplorare [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) e le [informazioni di riferimento su Immersive Reader SDK](../../reference.md).
* Visualizzare gli esempi di codice in [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/).
