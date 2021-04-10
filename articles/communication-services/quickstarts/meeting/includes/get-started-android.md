---
title: "Guida introduttiva: aggiungere un team che partecipa a un'app Android usando i servizi di comunicazione di Azure"
description: Questa Guida introduttiva illustra come usare la libreria di incorporamento dei team di servizi di comunicazione di Azure per Android.
author: palatter
ms.author: palatter
ms.date: 01/25/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 5ac4c53550468d33e9ed533303749d29e772d766
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105108477"
---
In questa Guida introduttiva si apprenderà come partecipare a una riunione di team con i team di servizi di comunicazione di Azure incorporare la libreria per Android.

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Android Studio](https://developer.android.com/studio) per creare l'applicazione Android.
- Una risorsa di Servizi di comunicazione distribuita. [Creare una risorsa di Servizi di comunicazione](../../create-communication-resource.md).
- Un [token di accesso utente](../../access-tokens.md) per il servizio di comunicazione di Azure.

## <a name="setting-up"></a>Configurazione

### <a name="create-an-android-app-with-an-empty-activity"></a>Creare un'app Android con un'attività vuota

In Android Studio avviare un nuovo progetto.

:::image type="content" source="../media/android/studio-new-project.png" alt-text="Screenshot che mostra il pulsante per l'avvio di un nuovo progetto di Android Studio selezionato in Android Studio.":::

Selezionare il modello di progetto "Empty Activity" (Attività vuota) in "Phone and Tablet" (Telefono e tablet).

:::image type="content" source="../media/android/studio-blank-activity.png" alt-text="Screenshot che mostra l'opzione 'Empty Activity' selezionata nella schermata del modello di progetto.":::

Assegnare un nome al progetto `TeamsEmbedAndroidGettingStarted` , impostare lingua su Java e selezionare SDK minimo di "API 21: Android 5,0 (Lollipop)" o versione successiva.

:::image type="content" source="../media/android/studio-calling-min-api.png" alt-text="Screenshot che mostra l'opzione 'Empty Activity' selezionata nella schermata 2 del modello di progetto.":::


### <a name="install-the-azure-package"></a>Installare il pacchetto di Azure

Nel livello di app Build. Gradle aggiungere le righe seguenti alle dipendenze e alle sezioni Android

```groovy
android {
    ...
    packagingOptions {
        pickFirst  'META-INF/*'
    }
}
```

```groovy
dependencies {
    ...
    implementation 'com.azure.android:azure-communication-common:1.0.0-beta.6'
    ...
}
```

### <a name="install-the-teams-embed-package"></a>Installare il pacchetto di incorporamento dei team

Scaricare il `MicrosoftTeamsSDK` pacchetto.

Decomprimere quindi la cartella MicrosoftTeamsSDK nella cartella projects app. Ex. `TeamsEmbedAndroidGettingStarted/app/MicrosoftTeamsSDK`.

### <a name="add-teams-embed-package-to-your-buildgradle"></a>Aggiungere il pacchetto di incorporamento teams al Build. Gradle

A livello `build.gradle` di App aggiungere la riga seguente alla fine del file:

```groovy
apply from: 'MicrosoftTeamsSDK/MicrosoftTeamsSDK.gradle'
```

Sincronizzare il progetto con i file Gradle.

### <a name="create-application-class"></a>Crea classe dell'applicazione

Creare un nuovo file di classe Java denominato `TeamsEmbedAndroidGettingStarted` . Si tratta della classe dell'applicazione che deve estendere `TeamsSDKApplication` . [Documentazione di Android](https://developer.android.com/reference/android/app/Application)

:::image type="content" source="../media/android/application-class-location.png" alt-text="Screenshot che mostra dove creare la classe dell'applicazione in Android Studio":::

```java
package com.microsoft.teamsembedandroidgettingstarted;

import com.microsoft.teamssdk.app.TeamsSDKApplication;

public class TeamsEmbedAndroidGettingStarted extends TeamsSDKApplication {
}
```

### <a name="update-themes"></a>Temi aggiornamento

Impostare il nome dello stile su `AppTheme` in entrambi `theme.xml` i `theme.xml (night)` file e.

:::image type="content" source="../media/android/theme-settings.png" alt-text="Screenshot che illustra i file di theme.xml in Android Studio":::

```xml
<style name="AppTheme" parent="Theme.AppCompat.DayNight.DarkActionBar">
```

```xml
<style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
```

### <a name="add-permissions-to-application-manifest"></a>Aggiungere autorizzazioni al manifesto dell'applicazione

Aggiungere l' `RECORD_AUDIO` autorizzazione al manifesto dell'applicazione ( `app/src/main/AndroidManifest.xml` ):  


```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.yourcompany.TeamsEmbedAndroidGettingStarted">
    <uses-permission android:name="android.permission.RECORD_AUDIO"/>
    <application
```

### <a name="add-app-name-and-theme-to-application-manifest"></a>Aggiungere il nome e il tema dell'app al manifesto dell'applicazione

Aggiungere ' xmlns: Tools = " http://schemas.android.com/tools " al manifesto.

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    package="com.yourcompany.TeamsEmbedAndroidGettingStarted">
```

Aggiungere `.TeamsEmbedAndroidGettingStarted` a `android:name` , `android:name` a `tools:replace` e modificare `android:theme` in `@style/AppTheme`

```xml
<application
    android:name=".TeamsEmbedAndroidGettingStarted"
    tools:replace="android:name"
    android:theme="@style/AppTheme"
    android:allowBackup="true"
    android:icon="@mipmap/ic_launcher"
    android:label="@string/app_name"
    android:roundIcon="@mipmap/ic_launcher_round"
    android:supportsRtl="true">
```

### <a name="set-up-the-layout-for-the-app"></a>Configurare il layout per l'app

Creare un pulsante con un ID `join_meeting` . Passare a `app/src/main/res/layout/activity_main.xml` e sostituire il contenuto del file con quanto segue:

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/join_meeting"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Join Meeting"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

### <a name="create-the-main-activity-scaffolding-and-bindings"></a>Creare lo scaffolding e i binding per l'attività principale

Con il layout creato, è possibile aggiungere l'impalcatura di base dell'attività insieme alle associazioni obbligatorie. L'attività gestirà la richiesta delle autorizzazioni di runtime, la creazione del client della riunione e l'Unione di una riunione quando viene premuto il pulsante. Ogni procedura verrà descritta in una sezione specifica. 

`onCreate`Verrà eseguito l'override del metodo per richiamare `getAllPermissions` e `createAgent` aggiungere le associazioni per il `Join Meeting` pulsante. Questa operazione verrà eseguita dopo la creazione dell'attività. Per altre informazioni su `onCreate`, vedere la guida sul [ciclo di vita dell'attività](https://developer.android.com/guide/components/activities/activity-lifecycle).

Passare a **MainActivity.java** e sostituire il contenuto con il codice seguente:

```java
package com.yourcompany.teamsembedandroidgettingstarted;

import androidx.appcompat.app.AppCompatActivity;
import androidx.core.app.ActivityCompat;

import android.Manifest;
import android.content.pm.PackageManager;
import android.os.Bundle;
import android.widget.Button;
import android.widget.Toast;

import com.azure.android.communication.common.CommunicationTokenCredential;
import com.azure.android.communication.common.CommunicationTokenRefreshOptions;
import com.azure.android.communication.ui.meetings.MeetingJoinOptions;
import com.azure.android.communication.ui.meetings.MeetingUIClient;

import java.util.ArrayList;
import java.util.concurrent.Callable;

public class MainActivity extends AppCompatActivity {

    private final String displayName = "John Smith";

    private MeetingUIClient meetingUIClient;
    private MeetingJoinOptions meetingJoinOptions;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        meetingJoinOptions = new MeetingJoinOptions(displayName);
        
        getAllPermissions();
        createMeetingClient();

        Button joinMeeting = findViewById(R.id.join_meeting);
        joinMeeting.setOnClickListener(l -> joinMeeting());
    }

    private void createMeetingClient() {
        // See section on creating meeting client
    }

    private void joinMeeting() {
        // See section on joining a meeting
    }

    private void getAllPermissions() {
        // See section on getting permissions
    }
}
```

### <a name="request-permissions-at-runtime"></a>Richiedere autorizzazioni in fase di esecuzione

Per Android 6.0 e versioni successive (livello API 23) e `targetSdkVersion` 23 o versione successiva, le autorizzazioni vengono concesse in fase di esecuzione invece che durante l'installazione dell'app. Per supportare questo requisito, è possibile implementare `getAllPermissions` per chiamare `ActivityCompat.checkSelfPermission` e `ActivityCompat.requestPermissions` per ogni autorizzazione richiesta.

```java
/**
 * Request each required permission if the app doesn't already have it.
 */
private void getAllPermissions() {
    String[] requiredPermissions = new String[]{Manifest.permission.RECORD_AUDIO};
    ArrayList<String> permissionsToAskFor = new ArrayList<>();
    for (String permission : requiredPermissions) {
        if (ActivityCompat.checkSelfPermission(this, permission) != PackageManager.PERMISSION_GRANTED) {
            permissionsToAskFor.add(permission);
        }
    }
    if (!permissionsToAskFor.isEmpty()) {
        ActivityCompat.requestPermissions(this, permissionsToAskFor.toArray(new String[0]), 202);
    }
}
```

> [!NOTE]
> Quando si progetta l'app, valutare quando dovranno essere richieste queste autorizzazioni. È consigliabile che vengano richieste quando è necessario, non prima. Per altre informazioni, vedere la [guida alle autorizzazioni di Android.](https://developer.android.com/training/permissions/requesting)

## <a name="object-model"></a>Modello a oggetti

Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità della libreria di incorporamento dei team di servizi di comunicazione di Azure:

| Nome                                  | Descrizione                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| MeetingUIClient| MeetingUIClient è il punto di ingresso principale della libreria di incorporamento dei team. |
| MeetingJoinOptions | MeetingJoinOptions vengono utilizzati per le opzioni configurabili, ad esempio il nome visualizzato. |
| CallState | CallState viene usato per la segnalazione delle modifiche allo stato di chiamata. Le opzioni sono le seguenti: `connecting` , `waitingInLobby` , `connected` e `ended` . |

## <a name="create-a-meetingclient-from-the-user-access-token"></a>Creare un MeetingClient dal token di accesso utente

È possibile creare un'istanza di un client di riunione autenticato con un token di accesso utente. Questo token viene in genere generato da un servizio con autenticazione specifica per l'applicazione. Per altre informazioni sui token di accesso degli utenti, vedere la guida relativa ai [token di accesso utente](../../access-tokens.md) . Per la guida di avvio rapido, sostituire `<USER_ACCESS_TOKEN>` con un token di accesso utente generato per la risorsa del servizio di comunicazione di Azure.

```java
private void createMeetingClient() {
    try {
        CommunicationTokenRefreshOptions refreshOptions = new CommunicationTokenRefreshOptions(tokenRefresher, true, "<USER_ACCESS_TOKEN>");
        CommunicationTokenCredential credential = new CommunicationTokenCredential(refreshOptions);
        meetingUIClient = new MeetingUIClient(credential);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to create meeting client: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

## <a name="setup-token-refreshing"></a>Aggiornamento del token di installazione

Creare un metodo chiamabile `tokenRefresher` . Quindi, creare un `fetchToken` metodo per ottenere il token utente. [È possibile trovare istruzioni su come eseguire questa operazione qui](https://docs.microsoft.com/azure/communication-services/quickstarts/access-tokens?pivots=programming-language-java)

```java
Callable<String> tokenRefresher = () -> {
    return fetchToken();
};

public String fetchToken() {
    // Get token
    return USER_ACCESS_TOKEN;
}
```

## <a name="get-the-teams-meeting-link"></a>Ottenere il collegamento alla riunione di Teams

Il collegamento alla riunione di Teams può essere recuperato usando le API Graph. Questa operazione è descritta in dettaglio nella [documentazione di Graph](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true).
L'SDK per le chiamate di Servizi di comunicazione accetta un collegamento alla riunione di Teams completo. Questo collegamento viene restituito come parte della risorsa `onlineMeeting`, accessibile nella [proprietà `joinWebUrl`](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true). È anche possibile ottenere le informazioni sulla riunione necessarie dall'URL di **Partecipa alla riunione** nell'invito alla riunione di Teams stesso.

## <a name="start-a-meeting-using-the-meeting-client"></a>Avviare una riunione usando il client di riunione

L'Unione di una riunione può essere eseguita tramite e `MeetingClient` richiede solo un `meetingURL` e il `JoinOptions` . Sostituire `<MEETING_URL>` con un URL della riunione teams.

```java
/**
 * Join a meeting with a meetingURL.
 */
private void joinMeeting() {
    try {
        meetingUIClient.join("<MEETING_URL>", meetingJoinOptions);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to join meeting: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

## <a name="launch-the-app-and-join-a-meeting"></a>Avviare l'app e partecipare a una riunione

È ora possibile avviare l'app usando il pulsante "Run App" (Esegui app) sulla barra degli strumenti (MAIUSC+F10). 

:::image type="content" source="../media/android/quickstart-android-join-meeting.png" alt-text="Screenshot che mostra l'applicazione completata.":::

:::image type="content" source="../media/android/quickstart-android-joined-meeting.png" alt-text="Screenshot che mostra l'applicazione completata dopo che la riunione è stata unita.":::

## <a name="sample-code"></a>Codice di esempio

È possibile scaricare l'app di esempio da [GitHub](https://github.com/Azure-Samples/teams-embed-android-getting-started)
