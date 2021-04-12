---
title: Introduzione al controllo mappa di Android - Mappe di Microsoft Azure
description: Acquisire familiarità con le mappe di Azure Android SDK. Vedere come creare un progetto in Android Studio, installare l'SDK e creare una mappa interattiva.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: a60ee8faf8d19afba59e46c52aaba9395c3a5292
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604448"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Introduzione ad Android SDK per Mappe di Azure

Android SDK per Mappe di Azure è una raccolta di mappe in formato vettoriale per Android. Questo articolo illustra i processi di installazione di Android SDK per Mappe di Microsoft Azure e caricamento di una mappa.

## <a name="prerequisites"></a>Prerequisiti

Assicurarsi di completare la procedura descritta nella [Guida introduttiva: creare un documento dell'app Android](quick-android-map.md) .

## <a name="localizing-the-map"></a>Localizzare la mappa

Android SDK per Mappe di Azure offre tre modi diversi per impostare la lingua e la visualizzazione a livello di area della mappa. Nel codice riportato di seguito viene illustrato come impostare la lingua sul francese ("fr-FR") e la vista regionale su "auto".

La prima opzione consiste nel passare le informazioni sulla lingua e sulla visualizzazione a livello di area nella classe `AzureMaps` usando i metodi statici `setLanguage` e `setView` a livello globale. In questo modo la lingua e la visualizzazione a livello di area predefinite vengono impostate in tutti i controlli di Mappe di Azure caricati nell'app.

::: zone pivot="programming-language-java-android"

```java
static {
    //Alternatively use Azure Active Directory authenticate.
    AzureMaps.setAadProperties("<Your aad clientId>", "<Your aad AppId>", "<Your aad Tenant>");

    //Set your Azure Maps Key.
    //AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");   

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("Auto");
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
companion object {
    init {
        //Alternatively use Azure Active Directory authenticate.
        AzureMaps.setAadProperties("<Your aad clientId>", "<Your aad AppId>", "<Your aad Tenant>");

        //Set your Azure Maps Key.
        //AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");
    
        //Set the language to be used by Azure Maps.
        AzureMaps.setLanguage("fr-FR");
    
        //Set the regional view to be used by Azure Maps.
        AzureMaps.setView("Auto");
    }
}
```

::: zone-end

La seconda opzione consiste nel passare le informazioni sulla lingua e la visualizzazione nel codice XML del controllo mappa.

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="Auto"
    />
```

La terza opzione consiste nell'impostare a livello di codice la lingua e la visualizzazione a livello di area della mappa usando il metodo `setStyle` della mappa. Questa operazione può essere eseguita in qualsiasi momento per cambiare la lingua e la visualizzazione a livello di area della mappa.

::: zone pivot="programming-language-java-android"

```java
mapControl.onReady(map -> {
    map.setStyle(
        language("fr-FR"),
        view("Auto")
    );
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
mapControl.onReady(OnReady { map: AzureMap ->
    map.setStyle(
        language("fr-FR"),
        view("Auto")
    )
})
```

::: zone-end

Di seguito è riportato un esempio di mappe di Azure con la lingua impostata su "fr-FR" e la vista regionale impostata su "auto".

![Mappe di Azure, immagine di mappa con etichette in francese](media/how-to-use-android-map-control-library/android-localization.png)

Un elenco completo delle lingue e delle visualizzazioni a livello di area supportate è disponibile [qui](supported-languages.md).

## <a name="navigating-the-map&quot;></a>Esplorazione della mappa

Esistono diversi modi in cui è possibile ingrandire, fare una panoramica, ruotare e inclinare la mappa. Di seguito vengono descritti tutti i diversi modi per esplorare la mappa.

**Zoom della mappa**

* Toccare la mappa con due dita e avvicinarle per fare zoom indietro o allontanarle per fare zoom avanti.
* Effettuare un doppio tocco sulla mappa per ingrandire di un livello.
* Doppio tocco con due dita per eseguire lo zoom indietro della mappa di un livello.
* Toccare due volte; al secondo tocco, mantenere il dito sulla mappa e trascinare verso l'alto per fare zoom avanti o verso il basso per fare zoom indietro.

**Panoramica della mappa**

* Toccare la mappa e trascinare in qualsiasi direzione.

**Rotazione della mappa**

* Toccare la mappa con due dita e ruotare.

**Inclinazione della mappa**

* Toccare la mappa con due dita e trascinarle insieme verso l'alto o verso il basso.

## <a name=&quot;azure-government-cloud-support&quot;></a>Supporto cloud di Azure per enti pubblici

Azure Maps Android SDK supporta il cloud di Azure per enti pubblici. È possibile accedere al Android SDK mappe di Azure dallo stesso repository maven. Per connettersi alla versione cloud di Azure per enti pubblici della piattaforma Azure Maps è necessario eseguire le attività seguenti.

Nella stessa posizione in cui sono specificati i dettagli di autenticazione di Azure Maps, aggiungere la seguente riga di codice per indicare alla mappa di usare il dominio Cloud Azure Maps Government.

::: zone pivot=&quot;programming-language-java-android&quot;

```java
AzureMaps.setDomain(&quot;atlas.azure.us");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
AzureMaps.setDomain("atlas.azure.us")
```

::: zone-end

Assicurarsi di usare i dettagli di autenticazione di Azure Maps dalla piattaforma cloud di Azure per enti pubblici durante l'autenticazione della mappa e dei servizi.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come aggiungere dati di sovrapposizione sulla mappa:

> [!div class="nextstepaction"]
> [Gestire l'autenticazione in Mappe di Azure](how-to-manage-authentication.md)

> [!div class="nextstepaction"]
> [Cambiare gli stili della mappa nelle mappe Android](set-android-map-styles.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per i simboli](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per le linee](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Aggiungere un livello per i poligoni](how-to-add-shapes-to-android-map.md)
