---
title: "Guida introduttiva: creare un'app HoloLens con Unity e MRTK"
description: Questa Guida introduttiva illustra come creare un'app HoloLens Unity usando MRTK e gli ancoraggi degli oggetti.
author: craigktreasure
manager: virivera
services: azure-object-anchors
ms.author: crtreasu
ms.date: 02/02/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: 78a8136c3f66d0c790f6fd7508ca37b55a1541fd
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748658"
---
# <a name="quickstart-create-a-hololens-app-with-azure-object-anchors-in-unity-with-mrtk"></a>Guida introduttiva: creare un'app HoloLens con gli ancoraggi degli oggetti di Azure in Unity con MRTK

In questa Guida introduttiva verrà creata un'app HoloLens Unity che usa gli [ancoraggi degli oggetti di Azure](../overview.md). L'ancoraggio di oggetti di Azure è un servizio cloud gestito che converte asset 3D in modelli di intelligenza artificiale che consentono esperienze di realtà miste compatibili con gli oggetti per HoloLens. Al termine, si avrà un'app HoloLens compilata con Unity in grado di rilevare oggetti nel mondo fisico.

Si apprenderà come:

> [!div class="checklist"]
> * Preparare le impostazioni di compilazione di Unity.
> * Esportare il progetto Visual Studio HoloLens.
> * Distribuire l'app ed eseguirla in un dispositivo HoloLens 2.

[!INCLUDE [Unity quickstart prerequisites](../../../includes/object-anchors-quickstart-unity-prerequisites.md)]

[!INCLUDE [Unity device setup](../../../includes/object-anchors-quickstart-unity-device-setup.md)]

## <a name="open-the-sample-project"></a>Aprire il progetto di esempio

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

[!INCLUDE [Download Unity Package](../../../includes/object-anchors-quickstart-unity-download-package.md)]

In Unity aprire il `quickstarts/apps/unity/mrtk` progetto.

[!INCLUDE [Import Unity Package](../../../includes/object-anchors-quickstart-unity-import-package.md)]

[!INCLUDE [Unity build and run](../../../includes/object-anchors-quickstart-unity-build-run.md)]

[!INCLUDE [Unity build sample scene 1](../../../includes/object-anchors-quickstart-unity-build-sample-scene-1.md)]

Quando viene visualizzata una finestra di dialogo "utilità di importazione TMP", viene richiesto di importare le risorse di TextMesh Pro.
:::image type="content" source="./media/textmesh-pro-importer-dialog.png" alt-text="Importare le risorse di TextMesh Pro":::

[!INCLUDE [Unity build sample scene 2](../../../includes/object-anchors-quickstart-unity-build-sample-scene-2.md)]

[!INCLUDE [Unity build and deploy](../../../includes/object-anchors-quickstart-unity-build-deploy.md)]

### <a name="run-the-sample-app"></a>Eseguire l'app di esempio

Accendere il dispositivo, selezionare **tutte le app**, quindi individuare e avviare l'app. Dopo la schermata iniziale di Unity verrà visualizzato un rettangolo di delimitazione. È possibile utilizzare la mano per spostare, ridimensionare o ruotare il rettangolo di delimitazione. Posizionare la casella per coprire l'oggetto che si desidera rilevare.

Aprire il <a href="https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/README_HandMenu.html" target="_blank">menu a mano</a> e selezionare **blocca SearchArea** per impedire l'ulteriore spostamento del rettangolo di selezione. Selezionare **Avvia ricerca** per avviare il rilevamento degli oggetti. Quando viene rilevato l'oggetto, viene eseguito il rendering di una mesh sull'oggetto. I dettagli di un'istanza rilevata vengono visualizzati sullo schermo, ad esempio il rapporto timestamp aggiornato e copertura superficie. Selezionare **Arresta ricerca** per arrestare il rilevamento e tutte le istanze rilevate verranno rimosse.

#### <a name="the-app-menus"></a>Menu dell'app

È anche possibile eseguire altre azioni usando il <a href="https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/README_HandMenu.html" target="_blank">menu a mano</a>.

##### <a name="primary-menu"></a>Menu primario

* **Avvia ricerca/Interrompi ricerca** : avvia o arresta il processo di rilevamento oggetti.
* **Attiva/Nascondi mapping spaziale** : Mostra/Nascondi il rendering del mapping spaziale. Questa opzione può essere utilizzata per eseguire il debug se l'analisi è stata completata o meno.
* **Impostazioni di rilevamento** : attiva o disattiva il menu impostazioni di rilevamento.
* **Impostazioni dell'area di ricerca** : attiva o disattiva il menu Impostazioni area di ricerca.
* **Avviare la traccia** : acquisire i dati di diagnostica e salvarli nel dispositivo. Per altri dettagli, vedere la sezione **problemi di rilevamento del debug e acquisire la diagnostica**.
* **Caricare la traccia** : caricare i dati di diagnostica nel servizio ancoraggi oggetti. Un utente deve fornire il proprio account di sottoscrizione in `subscription.json` e caricarlo nella `LocalState` cartella. Un file di esempio `subscription.json` è disponibile di seguito.

    :::image type="content" source="./media/mrtk-hand-menu-primary.png" alt-text="Menu della mano primaria Unity":::

##### <a name="tracker-settings-menu"></a>Menu impostazioni di rilevamento

* **Accuratezza elevata** : una funzionalità sperimentale usata per ottenere una posa più accurata. Per abilitare questa opzione, è necessario disporre di più risorse di sistema durante il rilevamento degli oggetti. Il rendering della mesh oggetto verrà eseguito in rosa in questa modalità. Selezionare di nuovo questo pulsante per tornare alla modalità di rilevamento normale.
* **Allineamento verticale rilassato** : se abilitata, consente di rilevare un oggetto in un angolo non verticale. Utile per il rilevamento di oggetti sulle rampe.
* **Consenti modifica di scalabilità** : consente allo strumento di rilevamento di modificare le dimensioni dell'oggetto rilevato in base alle informazioni ambientali.
* **Dispositivo di scorrimento rapporto di code coverage** : regola la proporzione di punti della superficie che deve corrispondere affinché lo strumento di rilevamento rilevi un oggetto.  I valori inferiori consentono al Tracker di rilevare meglio gli oggetti difficili da rilevare per i sensori HoloLens, ad esempio oggetti scuri o oggetti altamente riflettenti. I valori più alti ridurranno la frequenza dei rilevamenti falsi.

    :::image type="content" source="./media/mrtk-hand-menu-tracker.png" alt-text="Menu della mano di rilevamento Unity":::

##### <a name="search-area-settings-menu"></a>Menu Impostazioni area di ricerca

* **Area di ricerca blocchi** : riquadro di blocco per evitare spostamenti accidentali.
* **Area di ricerca con regolazione automatica** : consente di riposizionare l'area di ricerca durante il rilevamento degli oggetti.
* **Mesh ciclo** : scorre la visualizzazione dei mesh caricati nell'area di ricerca.  Questa opzione consente agli utenti di allineare la casella di ricerca per individuare gli oggetti difficili da rilevare.

    :::image type="content" source="./media/mrtk-hand-menu-search-area.png" alt-text="Menu a mano area di ricerca Unity":::

Esempio `subscription.json`:

```json
{
  "AccountId": "<your account id>",
  "AccountKey": "<your account key>",
  "AccountDomain": "<your account domain>"
}
```

[!INCLUDE [Unity setup Windows Device Portal](../../../includes/object-anchors-quickstart-unity-setup-device-portal.md)]

[!INCLUDE [Unity upload your model](../../../includes/object-anchors-quickstart-unity-upload-model.md)]

[!INCLUDE [Unity troubleshooting](../../../includes/object-anchors-quickstart-unity-troubleshooting.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Concetti: Panoramica dell'SDK](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [Domande frequenti](../faq.md)
