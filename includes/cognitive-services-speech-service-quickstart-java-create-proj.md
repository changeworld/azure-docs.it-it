---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.openlocfilehash: 68e83ca0fc92247a31e840e76fc8019736b71dd9
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99214700"
---
1. Avviare Eclipse.

1. Nell’utilità di avvio di Eclipse, nel campo **Area di lavoro**, inserire il nome di una nuova directory dell’area di lavoro. Selezionare quindi **Avvio**.

   ![Screenshot dell'utilità di avvio di Eclipse](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-01-create-new-eclipse-workspace.png)

1. Dopo qualche istante verrà visualizzata la finestra principale dell'IDE di Eclipse. Se è presente, chiudere la **schermata iniziale**.

1. Dalla barra dei menu di Eclipse, creare un nuovo progetto selezionando **File** > **Nuovo** > **Progetto**.

1. Verrà visualizzata la finestra di dialogo **Nuovo progetto** . Selezionare **Progetto Java** e selezionare **Avanti**.

   ![Screenshot della finestra di dialogo Nuovo progetto, con il progetto Java evidenziato](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-02-select-wizard.png)

1. Viene avviata la procedura guidata **Nuovo progetto Java**. Nel campo **Nome progetto**, inserire **quickstart** e selezionare **JavaSE-1.8** come ambiente di esecuzione. Selezionare **Fine**.

   ![Screenshot della procedura guidata Nuovo progetto Java](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-03-create-java-project.png)

1. Se viene visualizzata la finestra **Aprire prospettiva associata?** , selezionare **Apri prospettiva**.

1. In **Esplora pacchetti**, fare doppio clic sul progetto **quickstart**. Selezionare **Configura** > **Converti in progetto Maven** dal menu di scelta rapida.

   ![Screenshot di Esplora pacchetti](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-04-convert-to-maven-project.png)

1. Viene visualizzata la finestra **Crea nuovo POM**. Nel campo **ID gruppo** immettere *com.microsoft.cognitiveservices.speech.samples* e nel campo **ID artefatto** specificare *quickstart*. Quindi selezionare **Fine**.

   ![Screenshot di Crea nuova finestra POM](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-05-configure-maven-pom.png)

1. Aprire il file *pom.xml* e modificarlo.

   * Alla fine del file, prima del tag di chiusura `</project>`, creare un elemento `repositories` con un riferimento al repository Maven per Speech SDK, come mostrato di seguito:

     [!code-xml[POM Repositories](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-microphone/pom.xml#repositories)]

   * Aggiungere anche un `dependencies` elemento, con la versione 1.15.0 dell'SDK vocale come dipendenza:

     [!code-xml[POM Dependencies](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-microphone/pom.xml#dependencies)]

   * Salvare le modifiche.
