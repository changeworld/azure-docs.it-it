---
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: erhopf
ms.openlocfilehash: d5a483cb239893d04d2c2581fb378f411878f4ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102620072"
---
## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services)
* Una risorsa dello strumento di lettura immersiva configurata per l'autenticazione Azure Active Directory. Seguire [queste istruzioni](../../how-to-create-immersive-reader.md) per configurare l'ambiente.  Per la configurazione delle proprietà dell'ambiente, saranno necessari alcuni dei valori creati qui. Salvare l'output della sessione in un file di testo per riferimento futuro.
* [Git](https://git-scm.com/).
* [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk).
* [Android Studio](https://developer.android.com/studio).

## <a name="configure-authentication-credentials"></a>Configurare le credenziali di autenticazione

1. Avviare Android Studio e aprire il progetto dalla directory **immersive-reader-sdk/js/samples/quickstart-java-android** (Java) o dalla directory **immersive-reader-sdk/js/samples/quickstart-kotlin** (Kotlin).

1. Creare un file denominato **env** all'interno della cartella **/assets**. Aggiungere i nomi e i valori seguenti e specificare i valori appropriati. Non eseguire il commit di questo file nel controllo del codice sorgente, perché contiene segreti che non devono essere resi pubblici.
    
    ```text
    TENANT_ID=<YOUR_TENANT_ID>
    CLIENT_ID=<YOUR_CLIENT_ID>
    CLIENT_SECRET=<YOUR_CLIENT_SECRET>
    SUBDOMAIN=<YOUR_SUBDOMAIN>
    ```

## <a name="start-the-immersive-reader-with-sample-content"></a>Avviare lo strumento di lettura immersiva con contenuto di esempio

Scegliere un emulatore di dispositivo da AVD Manager ed eseguire il progetto.

## <a name="next-steps"></a>Passaggi successivi

* Esplorare [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) e le [informazioni di riferimento su Immersive Reader SDK](../../reference.md).
* Visualizzare gli esempi di codice in [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/).