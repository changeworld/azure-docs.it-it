---
title: Fare clic sul plug-in di analisi automatica della raccolta per Application Insights JavaScript SDK
description: Come installare e usare il plug-in per la raccolta automatica di Analytics per Application Insights JavaScript SDK.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: lagayhar
ms.openlocfilehash: 5ad3e1a5a4ff47fe3d5fee8b8bc79235838995b8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100593632"
---
# <a name="click-analytics-auto-collection-plugin-for-application-insights-javascript-sdk"></a>Fare clic sul plug-in di analisi automatica della raccolta per Application Insights JavaScript SDK

Questo plug-in consente di tenere traccia automaticamente degli eventi click sulle pagine Web e di usare gli attributi data-* sugli elementi HTML per popolare la telemetria degli eventi.

## <a name="getting-started"></a>Guida introduttiva

Gli utenti possono configurare il plug-in di raccolta automatica di analisi dei clic tramite NPM.

### <a name="npm-setup"></a>installazione di NPM

Installare il pacchetto NPM:

```bash
npm install --save @microsoft/applicationinsights-clickanalytics-js @microsoft/applicationinsights-web
```

```js

import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ClickAnalyticsPlugin } from '@microsoft/applicationinsights-clickanalytics-js';

const clickPluginInstance = new ClickAnalyticsPlugin();
// Click Analytics configuration
const clickPluginConfig = {
  autoCapture: true
};
// Application Insights Configuration
const configObj = {
  instrumentationKey: "YOUR INSTRUMENTATION KEY",
  extensions: [clickPluginInstance],
  extensionConfig: {
    [clickPluginInstance.identifier]: clickPluginConfig
  },
};

const appInsights = new ApplicationInsights({ config: configObj });
appInsights.loadAppInsights();
```

## <a name="how-to-effectively-use-the-plugin"></a>Come usare efficacemente il plug-in

1. I dati di telemetria generati dagli eventi click vengono archiviati come `customEvents` nella sezione Application Insights della portale di Azure.
2. Il `name` di customEvent viene popolato in base alle regole seguenti:
    1.  Il `id` specificato in `data-*-id` verrà usato come nome customEvent. Se, ad esempio, l'elemento HTML selezionato ha l'attributo "Data-Sample-ID" = "Button1", "Button1" sarà il nome customEvent.
    2. Se non esiste alcun attributo di questo tipo e se `useDefaultContentNameOrId` è impostato su `true` nella configurazione, l'attributo HTML `id` o il nome del contenuto dell'elemento selezionato sarà utilizzato come nome customEvent.
    3. Se `useDefaultContentNameOrId` è false, il nome del customEvent sarà "not_specified".

    > [!TIP]
    > `useDefaultContentNameOrId`Per generare dati significativi, è necessario impostare su true.  
3. `parentDataTag` esegue due operazioni:
    1. Se questo tag è presente, il plug-in recupererà gli `data-*` attributi e i valori di tutti gli elementi HTML padre dell'elemento selezionato.
    2. Per migliorare l'efficienza, il plug-in USA questo tag come flag, quando viene rilevato, si interrompe dall'ulteriore elaborazione del DOM (Document Object Model) verso l'alto.
    
    > [!CAUTION]
    > Quando `parentDataTag` si usa, l'SDK inizierà a cercare i tag padre nell'intera applicazione e non solo l'elemento HTML in cui è stato usato.
4. `customDataPrefix` fornito dall'utente deve sempre iniziare con `data-` , ad esempio `data-sample-` . In HTML gli `data-*` attributi globali formano una classe di attributi chiamati attributi di dati personalizzati che consentono lo scambio di informazioni proprietarie tra il codice HTML e la relativa rappresentazione DOM da parte degli script. I browser meno recenti (Internet Explorer, Safari) elimineranno gli attributi che non comprendono, a meno che non inizino con `data-` .

    `*`In `data-*` può essere sostituito da qualsiasi nome che segue la [regola di produzione dei nomi XML](https://www.w3.org/TR/REC-xml/#NT-Name) con le restrizioni seguenti:
    - Il nome non deve iniziare con "XML", qualunque sia il caso usato per queste lettere.
    - Il nome non deve contenere punti e virgola (U + 003A).
    - Il nome non deve contenere lettere maiuscole.

## <a name="configuration"></a>Configurazione

| Nome                  | Type                               | Predefinito | Descrizione                                                                                                                              |
| --------------------- | -----------------------------------| --------| ---------------------------------------------------------------------------------------------------------------------------------------- |
| Acquisizione           | boolean                            | true    | Configurazione di acquisizione automatica.                                                                                                         |
| callback              | [IValueCallback](#ivaluecallback)  | Null    | Configurazione di callback.                                                                                                                 |
| pageTags              | string                             | Null    | Tag della pagina.                                                                                                                               |
| Tag datatags              | [ICustomDataTags](#icustomdatatags)| Null    | Tag di dati personalizzati forniti per sostituire i tag predefiniti usati per acquisire i dati di clic.                                                           |
| urlCollectHash        | boolean                            | false   | Abilita la registrazione dei valori dopo un carattere "#" dell'URL.                                                                          |
| urlCollectQuery       | boolean                            | false   | Abilita la registrazione della stringa di query dell'URL.                                                                                      |
| behaviorValidator     | Funzione                           | Null  | Funzione di callback da utilizzare per la `data-*-bhvr` convalida del valore. Per ulteriori informazioni, vedere la [sezione behaviorValidator](#behaviorvalidator).|
| defaultRightClickBhvr | numero di stringa (o)                 | ''      | Valore di comportamento predefinito quando si è verificato l'evento click destro. Se l'elemento ha l'attributo, verrà eseguito l'override di questo valore `data-*-bhvr` . |
| dropInvalidEvents     | boolean                            | false   | Flag per eliminare gli eventi che non hanno utili dati di clic.                                                                                   |

### <a name="ivaluecallback"></a>IValueCallback

| Nome               | Type     | Predefinito | Descrizione                                                                             |
| ------------------ | -------- | ------- | --------------------------------------------------------------------------------------- |
| pageName           | Funzione | Null    | Funzione per eseguire l'override del comportamento di acquisizione predefinito di pagename.                           |
| pageActionPageTags | Funzione | Null    | Funzione di callback per aumentare il valore predefinito di pageTags raccolto durante l'evento pageAction.  |
| contentName        | Funzione | Null    | Funzione di callback per popolare ContentName personalizzato.                                 |

### <a name="icustomdatatags"></a>ICustomDataTags

| Nome                      | Type    | Predefinito   | Tag predefinito da usare in HTML |   Descrizione                                                                                |
|---------------------------|---------|-----------|-------------|----------------------------------------------------------------------------------------------|
| useDefaultContentNameOrId | boolean | false     | N/D         |Raccoglie l'attributo HTML standard per ContentName quando un particolare elemento non è contrassegnato con customDataPrefix predefinito o quando customDataPrefix non è fornito dall'utente. |
| customDataPrefix          | string  | `data-`   | `data-*`| Acquisisci automaticamente il nome del contenuto e il valore degli elementi contrassegnati con il prefisso specificato. Ad esempio, `data-*-id` `data-<yourcustomattribute>` può essere usato nei tag HTML.   |
| aiBlobAttributeTag        | string  | `ai-blob` |  `data-ai-blob`| Il plug-in supporta un attributo BLOB JSON anziché singoli `data-*` attributi. |
| metaDataPrefix            | string  | Null      | N/D  | Acquisisci automaticamente il nome e il contenuto dell'elemento meta del codice HTML con il prefisso specificato durante l'acquisizione. Ad esempio, `custom-` può essere usato nel tag meta HTML. |
| captureAllMetaDataContent | boolean | false     | N/D   | Acquisisci automaticamente tutti i nomi e il contenuto dell'elemento meta del codice HTML. L'impostazione predefinita è false. Se abilitata, verrà eseguito l'override di metaDataPrefix fornito. |
| parentDataTag             | string  | Null      |  N/D  | Interrompe l'attraversamento del DOM per acquisire il nome del contenuto e il valore degli elementi quando vengono rilevati con questo tag. Ad esempio, `data-<yourparentDataTag>` può essere usato nei tag HTML.|
| dntDataTag                | string  | `ai-dnt`  |  `data-ai-dnt`| Gli elementi HTML con questo attributo verranno ignorati dal plug-in per l'acquisizione dei dati di telemetria.|

### <a name="behaviorvalidator"></a>behaviorValidator

Le funzioni behaviorValidator verificano automaticamente che i comportamenti contrassegnati nel codice siano conformi a un elenco predefinito. Ciò garantisce che i comportamenti contrassegnati siano coerenti con la tassonomia stabilita dall'azienda. Non è obbligatorio o previsto che la maggior parte dei clienti di monitoraggio di Azure utilizzerà questo, ma è disponibile per gli scenari avanzati. Sono presenti tre diverse funzioni di callback behaviorValidator esposte come parte di questa estensione. Tuttavia, gli utenti possono usare le proprie funzioni di callback se le funzioni esposte non risolvono il requisito. Lo scopo è quello di usare la struttura dei dati dei comportamenti personalizzati, il plug-in USA questa funzione di convalida durante l'estrazione dei comportamenti dai tag di dati.

| Nome                   | Descrizione                                                                        |
| ---------------------- | -----------------------------------------------------------------------------------|
| BehaviorValueValidator | Usare questa funzione di callback se la struttura dei dati dei comportamenti è una matrice di stringhe.|
| BehaviorMapValidator   | Utilizzare questa funzione di callback se la struttura dei dati dei comportamenti è un dizionario.       |
| BehaviorEnumValidator  | Usare questa funzione di callback se la struttura dei dati dei comportamenti è un'enumerazione.            |

#### <a name="sample-usage-with-behaviorvalidator"></a>Esempio di utilizzo con behaviorValidator

```js
var clickPlugin = Microsoft.ApplicationInsights.ClickAnalyticsPlugin;
var clickPluginInstance = new clickPlugin();

// Behavior enum values
var behaviorMap = {
  UNDEFINED: 0, // default, Undefined

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Page Experience [1-19]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  NAVIGATIONBACK: 1, // Advancing to the previous index position within a webpage
  NAVIGATION: 2, // Advancing to a specific index position within a webpage
  NAVIGATIONFORWARD: 3, // Advancing to the next index position within a webpage
  APPLY: 4, // Applying filter(s) or making selections
  REMOVE: 5, // Applying filter(s) or removing selections
  SORT: 6, // Sorting content
  EXPAND: 7, // Expanding content or content container
  REDUCE: 8, // Sorting content
  CONTEXTMENU: 9, // Context Menu
  TAB: 10, // Tab control
  COPY: 11, // Copy the contents of a page
  EXPERIMENTATION: 12, // Used to identify a third party experimentation event
  PRINT: 13, // User printed page
  SHOW: 14, //  Displaying an overlay
  HIDE: 15, //  Hiding an overlay
  MAXIMIZE: 16, //  Maximizing an overlay
  MINIMIZE: 17, // Minimizing an overlay
  BACKBUTTON: 18, //  Clicking the back button

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Scenario Process [20-39]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  STARTPROCESS: 20, // Initiate a web process unique to adopter
  PROCESSCHECKPOINT: 21, // Represents a checkpoint in a web process unique to adopter
  COMPLETEPROCESS: 22, // Page Actions that complete a web process unique to adopter
  SCENARIOCANCEL: 23, // Actions resulting from cancelling a process/scenario

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Download [40-59]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  DOWNLOADCOMMIT: 40, // Initiating an unmeasurable off-network download
  DOWNLOAD: 41, // Initiating a download

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Search [60-79]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SEARCHAUTOCOMPLETE: 60, // Auto-completing a search query during user input
  SEARCH: 61, // Submitting a search query
  SEARCHINITIATE: 62, // Initiating a search query
  TEXTBOXINPUT: 63, // Typing or entering text in the text box

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Commerce [80-99]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  VIEWCART: 82, // Viewing the cart
  ADDWISHLIST: 83, // Adding a physical or digital good or services to a wishlist
  FINDSTORE: 84, // Finding a physical store
  CHECKOUT: 85, // Before you fill in credit card info
  REMOVEFROMCART: 86, // Remove an item from the cart
  PURCHASECOMPLETE: 87, // Used to track the pageView event that happens when the CongratsPage or Thank You page loads after a successful purchase
  VIEWCHECKOUTPAGE: 88, // View the checkout page
  VIEWCARTPAGE: 89, // View the cart page
  VIEWPDP: 90, // View a PDP
  UPDATEITEMQUANTITY: 91, // Update an item's quantity
  INTENTTOBUY: 92, // User has the intent to buy an item
  PUSHTOINSTALL: 93, // User has selected the push to install option

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Authentication [100-119]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SIGNIN: 100, // User sign-in
  SIGNOUT: 101, // User sign-out

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Social [120-139]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SOCIALSHARE: 120, // "Sharing" content for a specific social channel
  SOCIALLIKE: 121, // "Liking" content for a specific social channel
  SOCIALREPLY: 122, // "Replying" content for a specific social channel
  CALL: 123, // Click on a "call" link
  EMAIL: 124, // Click on an "email" link
  COMMUNITY: 125, // Click on a "community" link

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Feedback [140-159]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  VOTE: 140, // Rating content or voting for content
  SURVEYCHECKPOINT: 145, // reaching the survey page/form

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Registration, Contact [160-179]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  REGISTRATIONINITIATE: 161, // Initiating a registration process
  REGISTRATIONCOMPLETE: 162, // Completing a registration process
  CANCELSUBSCRIPTION: 163, // Canceling a subscription
  RENEWSUBSCRIPTION: 164, // Renewing a subscription
  CHANGESUBSCRIPTION: 165, // Changing a subscription
  REGISTRATIONCHECKPOINT: 166, // Reaching the registration page/form

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Chat [180-199]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  CHATINITIATE: 180, // Initiating a chat experience
  CHATEND: 181, // Ending a chat experience

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Trial [200-209]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  TRIALSIGNUP: 200, // Signing-up for a trial
  TRIALINITIATE: 201, // Initiating a trial

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Signup [210-219]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SIGNUP: 210, // Signing-up for a notification or service
  FREESIGNUP: 211, // Signing-up for a free service

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Referals [220-229]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  PARTNERREFERRAL: 220, // Navigating to a partner's web property

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Intents [230-239]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  LEARNLOWFUNNEL: 230, // Engaging in learning behavior on a commerce page (ex. "Learn more click")
  LEARNHIGHFUNNEL: 231, // Engaging in learning behavior on a non-commerce page (ex. "Learn more click")
  SHOPPINGINTENT: 232, // Shopping behavior prior to landing on a commerce page

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Video [240-259]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  VIDEOSTART: 240, // Initiating a video
  VIDEOPAUSE: 241, // Pausing a video
  VIDEOCONTINUE: 242, // Pausing or resuming a video.
  VIDEOCHECKPOINT: 243, // Capturing predetermined video percentage complete.
  VIDEOJUMP: 244, // Jumping to a new video location.
  VIDEOCOMPLETE: 245, // Completing a video (or % proxy)
  VIDEOBUFFERING: 246, // Capturing a video buffer event
  VIDEOERROR: 247, // Capturing a video error
  VIDEOMUTE: 248, // Muting a video
  VIDEOUNMUTE: 249, // Unmuting a video
  VIDEOFULLSCREEN: 250, // Making a video full screen
  VIDEOUNFULLSCREEN: 251, // Making a video return from full screen to original size
  VIDEOREPLAY: 252, // Making a video replay
  VIDEOPLAYERLOAD: 253, // Loading the video player
  VIDEOPLAYERCLICK: 254, //  Click on a button within the interactive player
  VIDEOVOLUMECONTROL: 255, //  Click on video volume control
  VIDEOAUDIOTRACKCONTROL: 256, // Click on audio control within a video
  VIDEOCLOSEDCAPTIONCONTROL: 257, //  Click on the closed caption control
  VIDEOCLOSEDCAPTIONSTYLE: 258, //  Click to change closed caption style
  VIDEORESOLUTIONCONTROL: 259, //  Click to change resolution

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  //    Advertisement Engagement [280-299]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  ADBUFFERING: 283, // Ad is buffering
  ADERROR: 284, // Ad error
  ADSTART: 285, // Ad start
  ADCOMPLETE: 286, // Ad complete
  ADSKIP: 287, // Ad skipped
  ADTIMEOUT: 288, // Ad timed-out
  OTHER: 300 // Other
};

// Application Insights Configuration
var configObj = {
  instrumentationKey: "YOUR INSTRUMENTATION KEY",
  extensions: [clickPluginInstance],
  extensionConfig: {
    [clickPluginInstance.identifier]: {
      behaviorValidator: Microsoft.ApplicationInsights.BehaviorMapValidator(behaviorMap),
      defaultRightClickBhvr: 9
    },
  },
};
var appInsights = new Microsoft.ApplicationInsights.ApplicationInsights({
  config: configObj
});
appInsights.loadAppInsights();
```

## <a name="sample-app"></a>App di esempio

[App Web semplice con il plug-in di raccolta automatica di analisi clic abilitato](https://go.microsoft.com/fwlink/?linkid=2152871).

## <a name="next-steps"></a>Passaggi successivi

- Vedere il [repository GitHub](https://github.com/microsoft/ApplicationInsights-JS/tree/master/extensions/applicationinsights-clickanalytics-js) e il [pacchetto NPM](https://www.npmjs.com/package/@microsoft/applicationinsights-clickanalytics-js) per il plug-in di analisi automatica della raccolta dei clic.
- Usare l' [analisi degli eventi in esperienza di utilizzo](usage-segmentation.md) per analizzare i primi clic e sezionare in base alle dimensioni disponibili.
- Trovare fare clic su dati sotto il campo contenuto all'interno dell'attributo customDimensions nella tabella CustomEvents in [log Analytics](../logs/log-analytics-tutorial.md#write-a-query). Per ulteriori informazioni, vedere l' [app di esempio](https://go.microsoft.com/fwlink/?linkid=2152871) .
- Compilare una [cartella di lavoro](../visualize/workbooks-overview.md) per creare visualizzazioni personalizzate dei dati di clic.
