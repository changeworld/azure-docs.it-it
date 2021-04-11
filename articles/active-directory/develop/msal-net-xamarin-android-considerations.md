---
title: Configurazione del codice e risoluzione dei problemi di Novell Android (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Informazioni sulle considerazioni sull'uso di Novell Android con Microsoft Authentication Library per .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 11642480ac817b50d102e396b8ab5e200948a615
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103199556"
---
# <a name="configuration-requirements-and-troubleshooting-tips-for-xamarin-android-with-msalnet"></a>Requisiti di configurazione e suggerimenti per la risoluzione dei problemi per Novell Android con MSAL.NET

Quando si usa Novell Android con Microsoft Authentication Library per .NET (MSAL.NET), è necessario apportare alcune modifiche alla configurazione nel codice. Le sezioni seguenti descrivono le modifiche necessarie, seguite da una sezione relativa alla [risoluzione dei problemi](#troubleshooting) che consente di evitare alcuni dei problemi più comuni.

## <a name="set-the-parent-activity"></a>Impostare l'attività padre

In Novell Android impostare l'attività padre in modo che il token venga restituito dopo l'interazione:

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

In MSAL.NET 4,2 e versioni successive è anche possibile impostare questa funzionalità a livello di [PublicClientApplication][PublicClientApplication]. A tale scopo, usare un callback:

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

Se si usa [CurrentActivityPlugin](https://github.com/jamesmontemagno/CurrentActivityPlugin), il [`PublicClientApplication`][PublicClientApplication] codice del compilatore dovrebbe essere simile al frammento di codice seguente:

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

## <a name="ensure-that-control-returns-to-msal"></a>Verificare che il controllo torni a MSAL

Al termine della parte interattiva del flusso di autenticazione, restituire il controllo a MSAL eseguendo l'override di [`Activity`][Activity] .[`OnActivityResult()`][OnActivityResult] ProcessOnStatus.

Nella sostituzione chiamare MSAL. NET `AuthenticationContinuationHelper` .`SetAuthenticationContinuationEventArgs()` Metodo per restituire il controllo a MSAL alla fine della parte interattiva del flusso di autenticazione.

```csharp
protected override void OnActivityResult(int requestCode,
                                         Result resultCode,
                                         Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);

    // Return control to MSAL
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode,
                                                                            resultCode,
                                                                            data);
}
```

## <a name="update-the-android-manifest-for-system-webview-support"></a>Aggiornare il manifesto Android per il supporto di System WebView 

Per supportare WebView di sistema, il file di *AndroidManifest.xml* deve contenere i valori seguenti:

```xml
<activity android:name="microsoft.identity.client.BrowserTabActivity" android:configChanges="orientation|screenSize">
  <intent-filter>
    <action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.DEFAULT" />
    <category android:name="android.intent.category.BROWSABLE" />
    <data android:scheme="msal{Client Id}" android:host="auth" />
  </intent-filter>
</activity>
```

Il `android:scheme` valore viene creato dall'URI di reindirizzamento configurato nel portale per le applicazioni. Ad esempio, se l'URI di reindirizzamento è `msal4a1aa1d5-c567-49d0-ad0b-cd957a47f842://auth` , la `android:scheme` voce nel manifesto avrà un aspetto simile all'esempio seguente:

```xml
<data android:scheme="msal4a1aa1d5-c567-49d0-ad0b-cd957a47f842" android:host="auth" />
```

In alternativa, [creare l'attività nel codice](/xamarin/android/platform/android-manifest#the-basics) anziché modificare manualmente *AndroidManifest.xml*. Per creare l'attività nel codice, creare prima una classe che includa l' `Activity` attributo e l' `IntentFilter` attributo.

Di seguito è riportato un esempio di una classe che rappresenta i valori del file XML:

```csharp
  [Activity]
  [IntentFilter(new[] { Intent.ActionView },
        Categories = new[] { Intent.CategoryBrowsable, Intent.CategoryDefault },
        DataHost = "auth",
        DataScheme = "msal{client_id}")]
  public class MsalActivity : BrowserTabActivity
  {
  }
```

### <a name="use-system-webview-in-brokered-authentication"></a>Usare la visualizzazione del sistema nell'autenticazione negoziata

Per usare System WebView come fallback per l'autenticazione interattiva quando l'applicazione è stata configurata per l'uso dell'autenticazione negoziata e nel dispositivo non è installato un broker, abilitare MSAL per acquisire la risposta di autenticazione usando l'URI di reindirizzamento del broker. MSAL tenterà di eseguire l'autenticazione usando la visualizzazione di sistema predefinita nel dispositivo quando rileva che il broker non è disponibile. L'uso di questa impostazione predefinita non riuscirà perché l'URI di reindirizzamento è configurato per l'uso di un broker e System WebView non sa come usarlo per tornare a MSAL. Per risolvere questo problema, creare un _filtro preventivo_ usando l'URI di reindirizzamento del broker configurato in precedenza. Aggiungere il filtro preventivo modificando il manifesto dell'applicazione come nell'esempio seguente:

```xml
<!--Intent filter to capture System WebView or Authenticator calling back to our app after sign-in-->
<activity
      android:name="microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
          <action android:name="android.intent.action.VIEW" />
          <category android:name="android.intent.category.DEFAULT" />
          <category android:name="android.intent.category.BROWSABLE" />
          <data android:scheme="msauth"
              android:host="Enter_the_Package_Name"
              android:path="/Enter_the_Signature_Hash" />
    </intent-filter>
</activity>
```

Sostituire il nome del pacchetto registrato nell'portale di Azure per il `android:host=` valore. Sostituire l'hash della chiave registrato nell'portale di Azure per il `android:path=` valore. L'hash della firma *non* deve essere codificato in URL. Assicurarsi che una barra iniziale ( `/` ) venga visualizzata all'inizio dell'hash della firma.

### <a name="xamarinforms-43x-manifest"></a>Manifesto Novell. Forms 4.3. x

Novell. Forms 4.3. x genera il codice che imposta l' `package` attributo su `com.companyname.{appName}` in *AndroidManifest.xml*. Se si usa `DataScheme` As `msal{client_id}` , potrebbe essere necessario modificare il valore in modo che corrisponda al valore dello `MainActivity.cs` spazio dei nomi.

## <a name="android-11-support"></a>Supporto per Android 11

Per usare il browser di sistema e l'autenticazione negoziata in Android 11, è necessario prima dichiarare questi pacchetti, in modo che siano visibili all'app. Le app destinate a Android 10 (API 29) e versioni precedenti possono eseguire query sul sistema operativo per un elenco di pacchetti disponibili nel dispositivo in un determinato momento. Per supportare la privacy e la sicurezza, Android 11 riduce la visibilità dei pacchetti in un elenco predefinito di pacchetti del sistema operativo e i pacchetti specificati nel file di *AndroidManifest.xml* dell'app. 

Per consentire all'applicazione di eseguire l'autenticazione tramite il browser di sistema e il broker, aggiungere la seguente sezione a *AndroidManifest.xml*:

```xml
<!-- Required for API Level 30 to make sure the app can detect browsers and other apps where communication is needed.-->
<!--https://developer.android.com/training/basics/intents/package-visibility-use-cases-->
<queries>
  <package android:name="com.azure.authenticator" />
  <package android:name="{Package Name}" />
  <package android:name="com.microsoft.windowsintune.companyportal" />
  <!-- Required for API Level 30 to make sure the app detect browsers
      (that don't support custom tabs) -->
  <intent>
    <action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.BROWSABLE" />
    <data android:scheme="https" />
  </intent>
  <!-- Required for API Level 30 to make sure the app can detect browsers that support custom tabs -->
  <!-- https://developers.google.com/web/updates/2020/07/custom-tabs-android-11#detecting_browsers_that_support_custom_tabs -->
  <intent>
    <action android:name="android.support.customtabs.action.CustomTabsService" />
  </intent>
</queries>
``` 

Sostituire `{Package Name}` con il nome del pacchetto dell'applicazione. 

Il manifesto aggiornato, che ora include il supporto per il browser di sistema e l'autenticazione negoziata, dovrebbe avere un aspetto simile all'esempio seguente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionCode="1" android:versionName="1.0" package="com.companyname.XamarinDev">
    <uses-sdk android:minSdkVersion="21" android:targetSdkVersion="30" />
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <application android:theme="@android:style/Theme.NoTitleBar">
        <activity android:name="microsoft.identity.client.BrowserTabActivity" android:configChanges="orientation|screenSize">
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data android:scheme="msal4a1aa1d5-c567-49d0-ad0b-cd957a47f842" android:host="auth" />
            </intent-filter>
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data android:scheme="msauth" android:host="com.companyname.XamarinDev" android:path="/Fc4l/5I4mMvLnF+l+XopDuQ2gEM=" />
            </intent-filter>
        </activity>
    </application>
    <!-- Required for API Level 30 to make sure we can detect browsers and other apps we want to
     be able to talk to.-->
    <!--https://developer.android.com/training/basics/intents/package-visibility-use-cases-->
    <queries>
        <package android:name="com.azure.authenticator" />
        <package android:name="com.companyname.xamarindev" />
        <package android:name="com.microsoft.windowsintune.companyportal" />
        <!-- Required for API Level 30 to make sure we can detect browsers
        (that don't support custom tabs) -->
        <intent>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="https" />
        </intent>
        <!-- Required for API Level 30 to make sure we can detect browsers that support custom tabs -->
        <!-- https://developers.google.com/web/updates/2020/07/custom-tabs-android-11#detecting_browsers_that_support_custom_tabs -->
        <intent>
            <action android:name="android.support.customtabs.action.CustomTabsService" />
        </intent>
    </queries>
</manifest>
```

## <a name="use-the-embedded-web-view-optional"></a>Usare la visualizzazione Web incorporata (facoltativo)

Per impostazione predefinita, MSAL.NET usa il Web browser di sistema. Questo browser consente di ottenere Single Sign-On (SSO) usando le applicazioni Web e altre app. In alcuni casi rari, potrebbe essere necessario che il sistema utilizzi una visualizzazione Web incorporata.

In questo esempio di codice viene illustrato come configurare una visualizzazione Web incorporata:

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

Per altre informazioni, vedere [usare i Web browser per](msal-net-web-browsers.md) le [considerazioni sul browser di sistema](msal-net-system-browser-android-considerations.md)di MSAL.NET e Novell Android.

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="general-tips"></a>Suggerimenti generali

- Aggiornare il pacchetto NuGet MSAL.NET esistente alla [versione più recente di MSAL.NET](https://www.nuget.org/packages/Microsoft.Identity.Client/).
- Verificare che Novell. Forms sia nella versione più recente.
- Verificare che Novell. Android. support. v4 sia nella versione più recente.
- Verificare che tutti i pacchetti Novell. Android. support siano destinati alla versione più recente.
- Pulire o ricompilare l'applicazione.
- In Visual Studio provare a impostare il numero massimo di compilazioni di progetto parallele su **1**. A tale scopo, selezionare **Opzioni**  >  **progetti e soluzioni**  >  **Compila ed Esegui**  >  **numero massimo di compilazioni di progetti paralleli**.
- Se si esegue la compilazione dalla riga di comando e il comando USA `/m` , provare a rimuovere questo elemento dal comando.

### <a name="error-the-name-authenticationcontinuationhelper-doesnt-exist-in-the-current-context"></a>Errore: il nome AuthenticationContinuationHelper non esiste nel contesto corrente

Se un errore indica che `AuthenticationContinuationHelper` non esiste nel contesto corrente, è possibile che in Visual Studio il file *Android. csproj \** sia stato aggiornato in modo errato. Talvolta il percorso del file nell' `<HintPath>` elemento contiene erroneamente `netstandard13` anziché `monoandroid90` .

Questo esempio contiene un percorso file corretto:

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere l'esempio di un' [applicazione per dispositivi mobili Novell che usa la piattaforma di identità Microsoft](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations). Nella tabella seguente sono riepilogate le informazioni rilevanti nel file Leggimi.

| Esempio | Piattaforma | Descrizione |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Novell. iOS, Android, UWP | Semplice app Novell. Forms che illustra come usare MSAL per autenticare gli account personali Microsoft e Azure AD tramite l'endpoint Azure AD 2,0. L'app mostra anche come accedere a Microsoft Graph e visualizzare il token risultante. <br>![Diagramma del flusso di autenticazione](media/msal-net-xamarin-android-considerations/topology.png) |

<!-- REF LINKS -->
[PublicClientApplication]: /dotnet/api/microsoft.identity.client.publicclientapplication
[OnActivityResult]: /dotnet/api/android.app.activity.onactivityresult
[Activity]: /dotnet/api/android.app.activity
