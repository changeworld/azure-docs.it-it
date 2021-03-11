---
title: "Guida introduttiva: creare un modello di ancoraggio degli oggetti da usare in un'app"
description: In questa Guida introduttiva si apprenderà come creare un modello di ancoraggio di oggetti da un modello 3D.
author: craigktreasure
manager: virivera
ms.author: crtreasu
ms.date: 02/22/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: 69d23b9d02eb176a2e42985ef5c3673e83d9bb7e
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102607901"
---
# <a name="quickstart-create-an-object-anchors-model-from-a-3d-model"></a>Guida introduttiva: creare un modello di ancoraggio di oggetti da un modello 3D

L'ancoraggio di oggetti di Azure è un servizio cloud gestito che converte i modelli 3D in modelli di intelligenza artificiale che consentono esperienze di realtà miste compatibili con gli oggetti per la HoloLens. Questa Guida introduttiva illustra come creare un modello di ancoraggio di oggetti da un modello 3D usando C#/.NET Core SDK.

Si apprenderà come:

> [!div class="checklist"]
> * Creare un account di ancoraggio di oggetti
> * Convertire un modello 3D per creare un modello di ancoraggio di oggetti

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida introduttiva, accertarsi di disporre di quanto segue:

* Un computer Windows con <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a>.
* <a href="https://git-scm.com" target="_blank">Git per Windows</a>.
* <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1">.NET Core 3.1 SDK</a>.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-object-anchors-account"></a>Creare un account di ancoraggio di oggetti

In primo luogo, è necessario creare un account con il servizio ancoraggi oggetti.

1. Passare alla [portale di Azure](https://portal.azure.com/) e selezionare **Crea una risorsa**.

   :::image type="content" source="./media/create-aoa-resource-1.png" alt-text="Creare una nuova risorsa":::

2. Cercare la risorsa di **ancoraggio degli oggetti** .

   Cercare "Anchor oggetto".

   :::image type="content" source="./media/create-aoa-resource-2.png" alt-text="Selezionare la risorsa ancoraggi oggetti":::

   Nella risorsa **ancoraggi oggetto** nei risultati della ricerca selezionare **Crea-> gli ancoraggi degli oggetti**.

   :::image type="content" source="./media/create-aoa-resource-3.png" alt-text="Creare una risorsa ancoraggi oggetto":::

3. Nella finestra di dialogo **account ancoraggi oggetti** :
    * Immettere un nome di risorsa univoco.
    * Selezionare la sottoscrizione a cui si vuole alleghi la risorsa.
    * Creare o usare un gruppo di risorse esistente.
    * Selezionare l'area in cui si vuole che esista la risorsa.

    :::image type="content" source="./media/create-aoa-resource-4.png" alt-text="Immettere i dettagli dell'account della risorsa ancoraggi degli oggetti":::

    Selezionare **Crea** per iniziare a creare la risorsa.

4. Creata la risorsa, selezionare **Vai alla risorsa**.

   :::image type="content" source="./media/create-aoa-resource-5.png" alt-text="Vai alla risorsa":::

5. Nella pagina Overview (panoramica):

   Prendere nota del **dominio dell'account**. Sarà necessario più avanti.

   :::image type="content" source="./media/create-aoa-resource-6.1.png" alt-text="Copiare il dominio dell'account per la risorsa di ancoraggio degli oggetti":::

   Prendere nota dell' **ID account**. Sarà necessario più avanti.

   :::image type="content" source="./media/create-aoa-resource-6.2.png" alt-text="Copiare l'ID account per la risorsa di ancoraggio degli oggetti":::

   Passare alla pagina **chiavi di accesso** e prendere nota della **chiave primaria**. Sarà necessario più avanti.

   :::image type="content" source="./media/create-aoa-resource-7.png" alt-text="Copiare la chiave dell'account per la risorsa di ancoraggio degli oggetti":::

## <a name="get-the-sample-project"></a>Ottenere il progetto di esempio

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

## <a name="convert-a-3d-model"></a>Convertire un modello 3D

A questo punto, è possibile procedere con la conversione del modello 3D.

1. Aprire `quickstarts/conversion/Conversion.sln` in Visual Studio. Questa soluzione contiene un progetto console C#.

2. Aprire il `Configuration.cs` file che si trova nella radice del progetto e sostituire i `set-me` valori nei campi seguenti:

   | Campo         | Descrizione                                                         |
   |---------------|---------------------------------------------------------------------|
   | AccountDomain | **Dominio dell'account** dell'account di ancoraggio degli oggetti creato in precedenza. |
   | AccountId     | **ID account** dell'account di ancoraggio degli oggetti creato in precedenza.     |
   | AccountKey    | **Chiave primaria** dell'account di ancoraggio oggetti creato in precedenza     |

   Sono necessari quattro campi aggiuntivi da verificare:

    | Campo                    | Descrizione                       |
    | ---                      | ---                               |
    | InputAssetPath           | Percorso assoluto di un modello 3D nel computer locale. I formati di file supportati sono `fbx` ,, `ply` `obj` , `glb` e `gltf` . |
    | AssetDimensionUnit       | Unità di misura del modello 3D. È possibile accedere a tutte le unità di misura supportate usando l' `Azure.MixedReality.ObjectAnchors.Conversion.AssetLengthUnit` enumerazione. |
    | Gravità                  | Direzione del vettore gravitazionale del modello 3D. Questo vettore 3D fornisce la direzione verso il basso nel sistema di coordinate del modello. Se, ad esempio `y` , il valore negativo rappresenta la direzione verso il basso nello spazio 3D del modello, questo valore sarà `Vector3(0.0f, -1.0f, 0.0f)` . |

3. Compilare ed eseguire il progetto per caricare il modello 3D, registrare un nuovo processo di conversione con il servizio e attenderne il completamento. Una volta completato il processo, il modello di ancoraggio degli oggetti verrà scaricato accanto al file specificato in `InputAssetPath` . Verrà visualizzato un output simile al seguente:

   ```shell
    Asset   : ***********
    Gravity : ***********
    Unit    : ***********
    Attempting to upload asset...
    Attempting to create asset conversion job...
    Successfully created asset conversion job. Job ID: ***********
    Waiting for job completion...

    Asset conversion job completed successfully.
    Attempting to download result as '***********'...
    Success!
   ```

   Prendere nota dell'ID del **processo** per riferimento futuro. Può essere utile durante il debug o la risoluzione dei problemi.

4. Una volta che il processo è stato completato correttamente, verrà visualizzato un file con il formato `<Model-Filename-Without-Extension>_<JobID>.ou` nel percorso di output specificato. Ad esempio, se il nome file del modello 3D è `chair.ply` e l'ID del processo è, il `00000000-0000-0000-0000-000000000000` nome del file sarà costituito dall'output del servizio `chair_00000000-0000-0000-0000-000000000000.ou` .

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva è stato creato un account di ancoraggio degli oggetti e viene convertito un modello 3D per creare un modello di ancoraggio degli oggetti. Per informazioni su come integrare tale modello con l'SDK di oggetti Anchor nell'app per la realtà mista, continuare con gli articoli seguenti:

> [!div class="nextstepaction"]
> [Unity in HoloLens](get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Unity HoloLens con MRTK](get-started-unity-hololens-mrtk.md)

> [!div class="nextstepaction"]
> [DirectX HoloLens](get-started-hololens-directx.md)
