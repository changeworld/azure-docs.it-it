---
title: Implementare i widget nel portale per sviluppatori
titleSuffix: Azure API Management
description: Informazioni su come implementare i widget che utilizzano i dati da API esterne e visualizzarli nel portale API Management per sviluppatori.
author: dlepow
ms.author: apimpm
ms.date: 04/15/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: 4bda136a1abe8f9ffb443973731ebbe13b56ac3b
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741690"
---
# <a name="implement-widgets-in-the-developer-portal"></a>Implementare i widget nel portale per sviluppatori

In questa esercitazione si implementa un widget che utilizza i dati di un'API esterna e lo visualizza nel portale API Management per sviluppatori.

Il widget recupererà le descrizioni della sessione [dall'API conference di esempio.](https://conferenceapi.azurewebsites.net/?format=json) L'identificatore di sessione verrà impostato tramite un editor di widget designato.

Per facilitare il processo di sviluppo, fare riferimento al widget completato che si trova nella cartella del repository GitHub del portale API Management per `examples` sviluppatori: [](https://github.com/Azure/api-management-developer-portal/) `/examples/widgets/conference-session` .

:::image type="content" source="media/developer-portal-implement-widgets/widget-published.png" alt-text="Screenshot del widget pubblicato":::

## <a name="prerequisites"></a>Prerequisiti

* Configurare un [ambiente locale per](developer-portal-self-host.md#step-1-set-up-local-environment) la versione più recente del portale per sviluppatori.

* È necessario comprendere [l'anatomia del widget Paperbits.](https://paperbits.io/wiki/widget-anatomy)


## <a name="copy-the-scaffold"></a>Copiare lo scaffold

Usare uno `widget` scaffolder dalla `/scaffolds` cartella come punto di partenza per compilare il nuovo widget.

1. Copiare la cartella `/scaffolds/widget` in `/community/widgets` .
1. Rinominare la cartella in `conference-session` .

## <a name="rename-exported-module-classes"></a>Rinominare le classi di modulo esportate

Rinominare le classi di modulo esportate sostituendo il `Widget` prefisso con nei file `ConferenceSession` seguenti:

- `widget.design.module.ts`

- `widget.publish.module.ts`

- `widget.runtime.module.ts`
    
Ad esempio, nel `widget.design.module.ts` file modificare `WidgetDesignModule` in `ConferenceSessionDesignModule` :
    
```typescript
export class WidgetDesignModule implements IInjectorModule {
```
in 
    
```typescript
export class ConferenceSessionDesignModule implements IInjectorModule {
```
    
   
## <a name="register-the-widget"></a>Registrare il widget

Registrare i moduli del widget nei moduli radice del portale aggiungendo le righe seguenti nei rispettivi file:

1. `src/apim.design.module.ts` : modulo che registra le dipendenze in fase di progettazione.

   ```typescript
   import { ConferenceSessionDesignModule } from "../community/widgets/conference-session/widget.design.module";
   
   ...
   injector.bindModule(new ConferenceSessionDesignModule());
   ```
1. `src/apim.publish.module.ts` : modulo che registra le dipendenze in fase di pubblicazione.

   ```typescript
   import { ConferenceSessionPublishModule } from "../community/widgets/conference-session/widget.publish.module";

    ...

    injector.bindModule(new ConferenceSessionPublishModule());
    ```

1. `src/apim.runtime.module.ts` : dipendenze di runtime.

    ```typescript
    import { ConferenceSessionRuntimeModule } from "../community/widgets/conference-session/widget.runtime.module";

    ...

    injector.bindModule(new ConferenceSessionRuntimeModule());
    ```

## <a name="place-the-widget-in-the-portal"></a>Inserire il widget nel portale

A questo punto è possibile collegare lo scaffold duplicato e usarlo nel portale per sviluppatori.

1. Eseguire il comando `npm start`.

1. Quando l'applicazione viene caricata, inserire il nuovo widget in una pagina. È possibile trovarlo sotto il nome `Your widget` nella `Community` categoria nel selettore del widget.

    :::image type="content" source="media/developer-portal-implement-widgets/widget-selector.png" alt-text="Screenshot del selettore di widget":::

1. Salvare la pagina premendo **CTRL** + **S** (o **⌘** + **S** in macOS).

    > [!NOTE]
    > In fase di progettazione è comunque possibile interagire con il sito Web tenendo premuto **CTRL** (o **⌘**).

## <a name="add-custom-properties"></a>Aggiungere proprietà personalizzate

Per recuperare le descrizioni delle sessioni, il widget deve essere a conoscenza dell'identificatore di sessione. Aggiungere la `Session ID` proprietà alle rispettive interfacce e classi:

Perché il widget recuperi la descrizione della sessione, deve essere a conoscenza dell'identificatore di sessione. Aggiungere la proprietà ID sessione alle rispettive interfacce e classi:

1. `widgetContract.ts` : contratto dati (livello dati) che definisce la modalità di persistenza della configurazione del widget.

    ```typescript
    export interface WidgetContract extends Contract {
        sessionNumber: string;
    }
    ```

1. `widgetModel.ts` - modello (livello business): rappresentazione principale del widget nel sistema. Viene aggiornato dagli editor e sottoposto a rendering dal livello di presentazione.

    ```typescript
    export class WidgetModel {
        public sessionNumber: string;
    }
    ```

1. `ko/widgetViewModel.ts` - modello di visualizzazione (livello di presentazione): oggetto specifico del framework dell'interfaccia utente di cui il portale per sviluppatori esegue il rendering con il modello HTML.

    > [!NOTE]
    > Non è necessario modificare alcun elemento in questo file.

## <a name="configure-binders"></a>Configurare i binder

Abilitare il flusso `sessionNumber` dell'oggetto dall'origine dati alla presentazione del widget. Modificare le `ModelBinder` `ViewModelBinder` entità e :

1. `widgetModelBinder.ts` consente di preparare il modello usando i dati descritti nel contratto.

    ```typescript
    export class WidgetModelBinder implements IModelBinder<WidgetModel> {
        public async contractToModel(contract: WidgetContract): Promise<WidgetModel> {
            model.sessionNumber = contract.sessionNumber || "107"; // 107 is the default session id
            ...
        }
    
        public modelToContract(model: WidgetModel): Contract {
            const contract: WidgetContract = {
                sessionNumber: model.sessionNumber
                ...
            };
            ...
        }
    }
    ```

1. `ko/widgetViewModelBinder.ts` sa come il portale per sviluppatori deve presentare il modello (come modello di visualizzazione) in un framework dell'interfaccia utente specifico.

    ```typescript
    ...
    public async updateViewModel(model: WidgetModel, viewModel: WidgetViewModel): Promise<void> {
            viewModel.runtimeConfig(JSON.stringify({
                sessionNumber: model.sessionNumber
            }));
        }
    }
    ...
    ```

## <a name="adjust-design-time-widget-template"></a>Modificare il modello di widget in fase di progettazione

I componenti di ogni ambito vengono eseguiti in modo indipendente. Hanno contenitori di inserimento delle dipendenze separati, la propria configurazione, il ciclo di vita e così via. Possono anche essere basati su framework dell'interfaccia utente diversi (in questo esempio è Knockout JS).

Dal punto di vista della fase di progettazione, qualsiasi componente di runtime è semplicemente un tag HTML con determinati attributi e/o contenuto. La configurazione, se necessario, viene passata con markup normale. In casi semplici, come in questo esempio, il parametro viene passato nell'attributo . Se la configurazione è più complessa, è possibile usare un identificatore delle impostazioni richieste recuperate da un provider di configurazione designato ,ad esempio `ISettingsProvider` .

1. Aggiornare il `ko/widgetView.html` file:

    ```html
    <widget-runtime data-bind="attr: { params: runtimeConfig }"></widget-runtime>
    ```

    Quando il portale per sviluppatori esegue l'associazione in fase di progettazione o `attr` *di* pubblicazione, il codice HTML risultante è: 

    ```html
    <widget-runtime params="{ sessionNumber: 107 }"></widget-runtime>
    ```

    Quindi, in fase di esecuzione, `widget-runtime` il componente lo `sessionNumber` leggerà e lo userà nel codice di inizializzazione (vedere di seguito).

1. Aggiornare il `widgetHandlers.ts` file per assegnare l'ID sessione al momento della creazione:

    ```typescript
    ...
    createModel: async () => {
        var model = new ConferenceSessionModel();
        model.sessionNumber = "107";
            return model;
        }
    ...
    ```

## <a name="revise-runtime-view-model"></a>Rivedere il modello di visualizzazione di runtime

I componenti di runtime sono il codice in esecuzione nel sito Web stesso. Nel portale per sviluppatori di API Management, ad esempio, sono tutti gli script dietro componenti dinamici (ad esempio, i dettagli *dell'API,* la *console API),* la gestione di operazioni come la generazione di esempi di codice, l'invio di richieste e così via.

Il modello di visualizzazione del componente di runtime deve avere i metodi e le proprietà seguenti:

- Proprietà (contrassegnata con decorator) utilizzata come parametro di input del componente passato dall'esterno (markup generato in fase di `sessionNumber` `Param` progettazione; vedere il passaggio precedente).
- Proprietà `sessionDescription` associata al modello di widget (vedere `widget-runtime.html` più avanti in questo articolo).
- Metodo `initialize` (con `OnMounted` elemento Decorator) richiamato dopo la creazione del widget e l'assegnazione di tutti i relativi parametri. È un buon punto per leggere e `sessionNumber` richiamare l'API usando `HttpClient` . è `HttpClient` una dipendenza inserita dal contenitore IoC (Inversion of Control).

- In primo luogo, il portale per sviluppatori crea il widget e assegna tutti i relativi parametri. Quindi richiama il `initialize` metodo .

    ```typescript
    ...
    import * as ko from "knockout";
    import { Component, RuntimeComponent, OnMounted, OnDestroyed, Param } from "@paperbits/common/ko/decorators";
    import { HttpClient, HttpRequest } from "@paperbits/common/http";
    ...

    export class WidgetRuntime {
        public readonly sessionDescription: ko.Observable<string>;

        constructor(private readonly httpClient: HttpClient) {
            ...
            this.sessionNumber = ko.observable();
            this.sessionDescription = ko.observable();
            ...
        }

        @Param()
        public readonly sessionNumber: ko.Observable<string>;

        @OnMounted()
        public async initialize(): Promise<void> {
            ...
            const sessionNumber = this.sessionNumber();

            const request: HttpRequest = {
                url: `https://conferenceapi.azurewebsites.net/session/${sessionNumber}`,
                method: "GET"
            };

            const response = await this.httpClient.send<string>(request);
            const sessionDescription = response.toText();
    
            this.sessionDescription(sessionDescription);
            ...
        }
        ...
    }
    ```

## <a name="tweak-the-widget-template"></a>Modificare il modello di widget

Aggiornare il widget per visualizzare la descrizione della sessione.

Usare un tag di paragrafo e `markdown` un'associazione `text` (o ) nel `ko/runtime/widget-runtime.html` file per eseguire il rendering della descrizione:

```html
<p data-bind="markdown: sessionDescription"></p>
```

## <a name="add-the-widget-editor"></a>Aggiungere l'editor di widget

Il widget è ora configurato per recuperare la descrizione della sessione `107` . È stata `107` specificata nel codice come sessione predefinita. Per verificare di aver eseguito tutte le attività, eseguire e verificare che il portale per `npm start` sviluppatori visualizza la descrizione nella pagina.

A questo punto, eseguire questi passaggi per consentire all'utente di configurare l'ID sessione tramite un editor di widget:

1. Aggiornare il `ko/widgetEditorViewModel.ts` file:

    ```typescript
    export class WidgetEditor implements WidgetEditor<WidgetModel> {
        public readonly sessionNumber: ko.Observable<string>;

        constructor() {
            this.sessionNumber = ko.observable();
        }

        @Param()
        public model: WidgetModel;

        @Event()
        public onChange: (model: WidgetModel) => void;

        @OnMounted()
        public async initialize(): Promise<void> {
            this.sessionNumber(this.model.sessionNumber);
            this.sessionNumber.subscribe(this.applyChanges);
        }

        private applyChanges(): void {
            this.model.sessionNumber = this.sessionNumber();
            this.onChange(this.model);
        }
    }
    ```

    Il modello di visualizzazione dell'editor usa lo stesso approccio illustrato in precedenza, ma è disponibile una nuova proprietà `onChange` , decorata con `@Event()` . Collega il callback per notificare ai listener (in questo caso, un editor di contenuto) le modifiche al modello.

1. Aggiornare il `ko/widgetEditorView.html` file:

    ```html
    <input type="text" class="form-control" data-bind="textInput: sessionNumber" />
    ```

1. Eseguire di nuovo `npm start`. Dovrebbe essere possibile modificare `sessionNumber` nell'editor dei widget. Modificare l'ID `108` in , salvare le modifiche e aggiornare la scheda del browser. Se si verificano problemi, potrebbe essere necessario aggiungere nuovamente il widget alla pagina.

    :::image type="content" source="media/developer-portal-implement-widgets/widget-editor.png" alt-text="Screenshot dell'editor widget":::

## <a name="rename-the-widget"></a>Rinominare il widget

Modificare il nome del widget nel `constants.ts` file:

```typescript
...
export const widgetName = "conference-session";
export const widgetDisplayName = "Conference session";
...
```

> [!NOTE]
> Se si contribuisce con il widget al repository, l'oggetto deve essere uguale al nome della cartella e deve essere derivato dal nome visualizzato (lettere minuscole e spazi sostituiti con `widgetName` trattini). La categoria deve rimanere `Community` .

## <a name="next-steps"></a>Passaggi successivi


Altre informazioni sul portale per sviluppatori:

- [Panoramica del portale per sviluppatori di Gestione API](api-management-howto-developer-portal.md)

- [Widget di collaborazione:](developer-portal-widget-contribution-guidelines.md) i contributi della community sono benvenuti e incoraggiati.

- Vedere [Usare i widget della](developer-portal-use-community-widgets.md) community per informazioni su come usare i widget con contributi della community.
