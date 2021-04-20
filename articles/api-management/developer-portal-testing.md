---
title: Testare il portale per sviluppatori self-hosted
titleSuffix: Azure API Management
description: Informazioni su come configurare unit test e test end-to-end per il portale API Management self-hosted.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: ab6a7aa8fc4f11c34126415379294ef1e48fa286
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741711"
---
# <a name="test-the-self-hosted-developer-portal"></a>Testare il portale per sviluppatori self-hosted

Questo articolo illustra come configurare unit test e test end-to-end per il [portale self-hosted.](developer-portal-self-host.md)

## <a name="unit-tests"></a>Unit test

Un unit test è un approccio per convalidare piccole parti di funzionalità. Questa operazione viene eseguita in isolamento rispetto ad altre parti dell'applicazione.

### <a name="example-scenario"></a>Scenario di esempio

In questo scenario si sta testando un controllo di input della password. Accetta solo password contenenti almeno:

- Una lettera

- Un numero

- Un carattere speciale
 
Il test per convalidare questi requisiti sarà quindi simile al seguente:

```typescript
const passwordInput = new PasswordInput();

passwordInput.value = "";
expect(passwordInput.isValid).to.equal(false);

passwordInput.value = "password";
expect(passwordInput.isValid).to.equal(false);

passwordInput.value = "p@ssw0rd";
expect(passwordInput.isValid.to.equal(true);
```
 
### <a name="project-structure"></a>Struttura del progetto

È comune mantenere un unit test accanto al componente che dovrebbe convalidare.

```console
component.ts
component.spec.ts
```

### <a name="mock-http-requests"></a>Mock HTTP requests ( Mock delle richieste HTTP)

In alcuni casi si prevede che un componente eserciti richieste HTTP. Il componente deve rispondere correttamente a diversi tipi di risposte. Per simulare risposte HTTP specifiche, usare `MockHttpClient` . Implementa `HttpClient` l'interfaccia utilizzata da molti altri componenti del progetto.

```typescript
const httpClient = new MockHttpClient();

httpClient.mock()
    .get("/users/jane")
    .reply(200, {
        firstName: "Jane",
        lastName: "Doe"
    });
```

## <a name="end-to-end-tests"></a>Test end-to-end

Un test end-to-end esegue uno scenario utente specifico che esegue esattamente i passaggi previsti dall'utente. In un'applicazione Web come Azure API Management per sviluppatori, l'utente scorre il contenuto e seleziona le opzioni per ottenere determinati risultati. 

Per replicare la navigazione utente, è possibile usare librerie helper di manipolazione del browser come [Puppeteer.](https://github.com/puppeteer/puppeteer) Consente di simulare le azioni dell'utente e automatizzare gli scenari presunti. Puppeteer crea automaticamente anche screenshot di pagine o componenti in qualsiasi fase del test. Confrontarli in un secondo momento con i risultati precedenti per rilevare le deviazioni e le potenziali regressioni.

### <a name="example-scenario"></a>Scenario di esempio

In questo scenario è necessario convalidare un flusso di accesso utente. Questo scenario richiede i passaggi seguenti:

1. Aprire il browser e passare alla pagina di accesso.

1. Immettere l'indirizzo di posta elettronica.

1. Consente di immettere la password.

1. Selezionare **Accedi**.

1. Verificare che l'utente sia stato reindirizzato alla home page.

1. Verificare che la pagina includa la **voce di** menu Profilo. Si tratta di uno dei possibili indicatori a cui è stato eseguito l'accesso.

Per eseguire automaticamente il test, creare uno script con esattamente gli stessi passaggi:

```typescript
// 1. Open browser and navigate to the sign-in page.
const page = await browser.newPage();
await page.goto("https://contoso.com/signin");

// 2. Enter email.
await this.page.type("#email", "john.doe@contoso.com");

// 3. Enter password.
await this.page.type("#password", "p@s$w0rd");

// 4. Click Sign-in.
await this.page.click("#signin");

// 5. Verify that user got redirected to Home page.
expect(page.url()).to.equal("https://contoso.com");

// 6. Verify that the page includes the Profile menu item.
const profileMenuItem = await this.page.$("#profile");
expect(profileMenuItem).not.equals(null);
```

> [!NOTE]
> Stringhe come "#email", "#password" e "#signin" sono selettori di tipo CSS che identificano gli elementi HTML nella pagina. Per altre informazioni, vedere la specifica W3C dei selettori di livello [3.](https://www.w3.org/TR/selectors-3/)

### <a name="ui-component-maps"></a>Mappe dei componenti dell'interfaccia utente

I flussi utente spesso passano attraverso le stesse pagine o componenti. Un buon esempio è il menu principale del sito Web presente in ogni pagina. 

Creare una mappa dei componenti dell'interfaccia utente per evitare di configurare e aggiornare gli stessi selettori per ogni test. Ad esempio, è possibile sostituire i passaggi da 2 a 6 nell'esempio precedente con solo due righe:

```typescript
const signInWidget = new SigninBasicWidget(page);
await signInWidget.signInWithBasic({ email: "...", password: "..." });
```

### <a name="test-configuration"></a>Configurazione di test

Alcuni scenari possono richiedere la configurazione o i dati creati in precedenza. Ad esempio, potrebbe essere necessario automatizzare l'accesso utente con gli account di social media. È difficile creare i dati in modo semplice o rapido.

A questo scopo, è possibile aggiungere un file di configurazione speciale allo scenario di test. Gli script di test possono prelevare i dati necessari dal file. A seconda della pipeline di compilazione e test, i test possono eseguire il pull dei segreti da un archivio sicuro denominato.

Di seguito è riportato un esempio `validate.config.json` di che verrebbe archiviato nella cartella del `src` progetto.

```json
{
    "environment": "validation",
    "urls": {
        "home": "https://contoso.com",
        "signin": "https://contoso.com/signin",
        "signup": "https://contoso.com/signup/"
    },
    "signin": {
        "firstName": "John",
        "lastName": "Doe",
        "credentials": {
            "basic": {
                "email": "johndoe@contoso.com",
                "password": "< password >"
            },
            "aadB2C": {
                "email": "johndoe@contoso.com",
                "password": "< password >"
            }
        }
    },
    "signup": {
        "firstName": "John",
        "lastName": "Doe",
        "credentials": {
            "basic": {
                "email": "johndoe@contoso.com",
                "password": "< password >"
            }
        }
    }
}

```

### <a name="headless-vs-normal-tests"></a>Test headless e normali

I browser moderni, ad esempio Chrome o Microsoft Edge, consentono di eseguire l'automazione sia in modalità headless che in modalità normale. Il browser funziona senza un'interfaccia utente grafica in modalità headless. Esegue comunque la stessa pagina e le Document Object Model (DOM). L'interfaccia utente del browser in genere non è necessaria nelle pipeline di recapito. In tal caso, l'esecuzione di test in modalità headless è un'ottima opzione.

Quando si sviluppa uno script di test, è utile vedere cosa accade esattamente nel browser. È il momento di usare la modalità normale.

Per passare da una modalità all'altra, modificare `headless` l'opzione nel `constants.ts` file. Si tratta della `tests` cartella del progetto:

```typescript
export const LaunchOptions = {
    headless: false
};
```

Un'altra opzione utile è `slowMo` . Sospende l'esecuzione del test tra ogni azione:

```typescript
export const LaunchOptions = {
    slowMo: 200 // milliseconds
};
```

## <a name="run-tests"></a>Esecuzione dei test

Esistono due modi predefiniti per eseguire i test in questo progetto:

**Comando npm**

```console
npm run test
```

**Esplora test**

L'estensione Esplora test per VS Code (ad esempio, [Mocha Test Explorer](https://marketplace.visualstudio.com/items?itemName=hbenl.vscode-mocha-test-adapter)) ha un'interfaccia utente comoda e un'opzione per eseguire automaticamente i test a ogni modifica del codice sorgente:

:::image type="content" source="media/developer-portal-testing/visual-studio-code-test-explorer.png" alt-text="Screenshot di esplora Visual Studio Code test":::

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul portale per sviluppatori:

- [Panoramica del portale per sviluppatori di Gestione API](api-management-howto-developer-portal.md)

- [Ospitare in modo self-host il portale per sviluppatori](developer-portal-self-host.md)
