---
title: Accedere al portale LUIS
description: Se si è un nuovo utente che accede al portale LUIS, l'esperienza di accesso sarà leggermente diversa in base all'account utente corrente.
services: cognitive-services
ms.custom: ''
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 09/08/2020
ms.topic: how-to
ms.author: nitinme
author: nitinme
ms.openlocfilehash: aeb84fca47dbf2922f17a3e8931e3158f9ee2cca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "101706776"
---
# <a name="sign-in-to-luis-portal"></a>Accedere al portale LUIS

[!INCLUDE [LUIS Free account](includes/luis-portal-note.md)]

Usare questo articolo per iniziare a usare il portale LUIS e creare una risorsa di creazione. Dopo aver completato i passaggi descritti in questo articolo, sarà possibile creare e pubblicare app LUIS.

## <a name="access-the-portal"></a>Accedere al portale


1. Per iniziare a usare LUIS, visitare il [portale di Luis](https://www.luis.ai). Se non si ha già una sottoscrizione, verrà richiesto di creare un [account gratuito](https://azure.microsoft.com//free/cognitive-services/) e tornare al portale.
2. Aggiornare la pagina per aggiornarla con la sottoscrizione appena creata
3. Selezionare la sottoscrizione dall'elenco a discesa

    > [!div class="mx-imgBorder"]
    > ![Selezionare le sottoscrizioni](./media/migrate-authoring-key/select-subscription-sign-in-2.png)

4. Se la sottoscrizione si trova in un altro tenant, non sarà possibile passare i tenant dalla finestra esistente. È possibile cambiare i tenant chiudendo questa finestra e selezionando l'avatar più a destra contenente le iniziali nella barra superiore. Fare clic su **scegliere una risorsa di creazione diversa** dalla parte superiore per riaprire la finestra.

    > [!div class="mx-imgBorder"]
    > ![Cambia directory](./media/migrate-authoring-key/switch-directories.png)

5. Se è presente una risorsa LUIS authoring associata alla sottoscrizione, selezionarla dall'elenco a discesa. È possibile visualizzare tutte le applicazioni create in questa risorsa di creazione.
6. In caso contrario, fare clic su **Crea una nuova risorsa di creazione** nella parte inferiore di questo modale.
7.  Durante la creazione di una nuova risorsa di creazione specificare le informazioni seguenti:

    > [!div class="mx-imgBorder"]
    > ![Create new resource (Crea nuova risorsa)](./media/migrate-authoring-key/create-new-authoring-resource-2.png)

    * **Nome del tenant** : il tenant a cui è associata la sottoscrizione di Azure. Non sarà possibile cambiare i tenant dalla finestra esistente. È possibile cambiare i tenant chiudendo questa finestra e selezionando l'avatar nell'angolo superiore destro della schermata, contenente le iniziali. Selezionare **scegliere una risorsa di creazione diversa** dalla parte superiore per riaprire la finestra.
    * **Nome del gruppo di risorse di Azure** : nome del gruppo di risorse personalizzato scelto nella sottoscrizione. I gruppi di risorse consentono di raggruppare le risorse di Azure per l'accesso e la gestione. Se non si dispone attualmente di un gruppo di risorse nella sottoscrizione, non sarà possibile crearne uno nel portale LUIS. Passare a [portale di Azure](https://ms.portal.azure.com/#create/Microsoft.ResourceGroup) per crearne uno, quindi passare a Luis per continuare il processo di accesso.
    * **Nome risorsa di Azure** : nome personalizzato scelto, usato come parte dell'URL per le transazioni di creazione. Il nome della risorsa può includere solo caratteri alfanumerici, `-` e non può iniziare o terminare con `-` . Se nel nome sono inclusi altri simboli, la creazione di una risorsa avrà esito negativo.
    * **Località** : scegliere di creare le applicazioni in una delle [tre posizioni di creazione](./luis-reference-regions.md) attualmente supportate da Luis, tra cui: Stati Uniti occidentali, Europa occidentale e Australia orientale.
    * Piano **tariffario** : per impostazione predefinita, il piano tariffario per la creazione di F0 è selezionato come indicato. Creare una [chiave gestita dal cliente](./encrypt-data-at-rest.md#customer-managed-keys-for-language-understanding) dal portale di Azure se si sta cercando un livello aggiuntivo di sicurezza.
8. Ora è stato eseguito l'accesso a LUIS. È ora possibile iniziare a creare applicazioni.

## <a name="troubleshooting"></a>Risoluzione dei problemi

* Quando si crea una nuova risorsa, verificare che il nome della risorsa includa solo caratteri alfanumerici,'-' e non può iniziare o terminare con '-'. In caso contrario, l'operazione non riesce.
* Assicurarsi di disporre delle [autorizzazioni appropriate per la sottoscrizione per creare una risorsa di Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles). Se non si dispone delle autorizzazioni appropriate, contattare l'amministratore della sottoscrizione per concedere autorizzazioni sufficienti.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [avviare una nuova app](luis-how-to-start-new-app.md)