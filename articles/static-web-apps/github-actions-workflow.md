---
title: Flussi di lavoro di GitHub Actions per App Web statiche di Azure
description: Informazioni su come usare i repository GitHub per configurare la distribuzione continua in App Web statiche di Azure.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: cshoe
ms.openlocfilehash: b20a1670c13a272ed48088567a205d854ac99179
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791248"
---
# <a name="github-actions-workflows-for-azure-static-web-apps-preview"></a>Flussi di lavoro di GitHub Actions per App Web statiche di Azure (anteprima)

Quando si crea una nuova risorsa App Web statiche di Azure, Azure genera un flusso di lavoro GitHub Actions per controllare la distribuzione continua dell'app. Il flusso di lavoro è basato su un file YAML. Questo articolo descrive in dettaglio la struttura e le opzioni del file del flusso di lavoro.

Le distribuzioni vengono avviate da [trigger](#triggers), che eseguono i [processi](#jobs) definiti da singoli [passaggi](#steps).

> [!NOTE]
> App Web statiche di Azure supporta anche Azure DevOps. Vedere [Pubblicare con Azure DevOps](publish-devops.md) per informazioni sulla configurazione di una pipeline.

## <a name="file-location"></a>Percorso del file

Quando si collega il repository GitHub a App Web statiche di Azure, viene aggiunto un file del flusso di lavoro al repository.

Per visualizzare il file del flusso di lavoro generato, seguire questa procedura.

1. Aprire il repository dell'app in GitHub.
1. Nella scheda _Code_ (Codice) fare clic sulla cartella `.github/workflows`.
1. Fare clic sul file con un nome simile a `azure-static-web-apps-<RANDOM_NAME>.yml`.

Il file YAML nel repository sarà simile all'esempio seguente:

```yml
name: Azure Static Web Apps CI/CD

on:
  push:
    branches:
      - main
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
      - main

jobs:
  build_and_deploy_job:
    if: github.event_name == 'push' || (github.event_name == 'pull_request' && github.event.action != 'closed')
    runs-on: ubuntu-latest
    name: Build and Deploy Job
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true
      - name: Build And Deploy
        id: builddeploy
        uses: Azure/static-web-apps-deploy@v0.0.1-preview
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
          repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
          action: 'upload'
          ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
          app_location: '/' # App source code path
          api_location: 'api' # Api source code path - optional
          output_location: 'dist' # Built app content directory - optional
          ###### End of Repository/Build Configurations ######

  close_pull_request_job:
    if: github.event_name == 'pull_request' && github.event.action == 'closed'
    runs-on: ubuntu-latest
    name: Close Pull Request Job
    steps:
      - name: Close Pull Request
        id: closepullrequest
        uses: Azure/static-web-apps-deploy@v0.0.1-preview
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
          action: 'close'
```

## <a name="triggers"></a>Trigger

Un [trigger](https://help.github.com/actions/reference/events-that-trigger-workflows) di GitHub Actions notifica a un flusso di lavoro di GitHub Actions di eseguire un processo in base ai trigger di evento. I trigger vengono elencati usando la proprietà `on` nel file del flusso di lavoro.

```yml
on:
  push:
    branches:
      - main
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
      - main
```

Tramite le impostazioni associate alla proprietà `on`, è possibile definire quali rami attivano un processo e impostare i trigger da attivare per i diversi stati delle richieste pull.

In questo esempio viene avviato un flusso di lavoro quando cambia _il ramo_ principale. Le modifiche che avviano il flusso di lavoro includono il push dei commit e l'apertura delle richieste pull per il ramo scelto.

## <a name="jobs"></a>Processi

Ogni trigger di evento richiede un gestore eventi. I [processi](https://help.github.com/actions/reference/workflow-syntax-for-github-actions#jobs) definiscono cosa accade quando viene attivato un evento.

Nel file del flusso di lavoro di App Web statiche sono disponibili due processi.

| Nome                     | Descrizione                                                                                                    |
| ------------------------ | -------------------------------------------------------------------------------------------------------------- |
| `build_and_deploy_job`   | Viene eseguito quando si effettua il push dei commit o si apre una richiesta pull nel ramo elencato nella proprietà `on`.          |
| `close_pull_request_job` | Viene eseguito SOLO quando si chiude una richiesta pull, rimuovendo così l'ambiente di gestione temporanea creato dalle richieste pull. |

## <a name="steps"></a>Passaggi

I passaggi sono attività sequenziali per un processo. Un passaggio esegue azioni come l'installazione delle dipendenze, l'esecuzione di test e la distribuzione dell'applicazione nell'ambiente di produzione.

Un file del flusso di lavoro definisce i passaggi seguenti.

| Processo                      | Passaggi                                                                                                                              |
| ------------------------ | ---------------------------------------------------------------------------------------------------------------------------------- |
| `build_and_deploy_job`   | <ol><li>Estrae il repository nell'ambiente dell'azione.<li>Compila e distribuisce il repository in App Web statiche di Azure.</ol> |
| `close_pull_request_job` | <ol><li>Notifica ad App Web statiche di Azure che una richiesta pull è stata chiusa.</ol>                                                        |

## <a name="build-and-deploy"></a>Eseguire la compilazione e la distribuzione

Il passaggio denominato `Build and Deploy` esegue la compilazione e la distribuzione nell'istanza di App Web statiche di Azure. Nella sezione `with` è possibile personalizzare i valori seguenti per la distribuzione.

```yml
with:
  azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
  repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
  action: 'upload'
  ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
  app_location: '/' # App source code path
  api_location: 'api' # Api source code path - optional
  output_location: 'dist' # Built app content directory - optional
  ###### End of Repository/Build Configurations ######
```

[!INCLUDE [static-web-apps-folder-structure](../../includes/static-web-apps-folder-structure.md)]

I valori `repo_token`, `action` e `azure_static_web_apps_api_token` sono impostati automaticamente da App Web statiche di Azure e non devono essere modificati manualmente.

## <a name="custom-build-commands"></a>Comandi di compilazione personalizzati

È possibile avere un controllo con granularità fine sui ciò che i comandi eseguono durante una distribuzione. Nella sezione `with` di un processo è possibile definire i comandi seguenti.

La distribuzione chiama sempre `npm install` prima di qualsiasi comando personalizzato.

| Comando             | Descrizione                                                                                                                                                                                                                                                                                                                                                                                |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `app_build_command` | Definisce un comando personalizzato da eseguire durante la distribuzione dell'applicazione per contenuti statici.<br><br>Ad esempio, per configurare una build di produzione per un'applicazione Angular, creare uno script npm denominato da eseguire e `build-prod` `ng build --prod` immettere come comando `npm run build-prod` personalizzato. Se lasciato vuoto, il flusso di lavoro tenterà di eseguire i comandi `npm run build` o `npm run build:azure`. |
| `api_build_command` | Definisce un comando personalizzato da eseguire durante la distribuzione dell'applicazione per le API di Funzioni di Azure.                                                                                                                                                                                                                                                                                                  |

## <a name="skip-app-build"></a>Ignorare la compilazione dell'app

Se è necessario il controllo completo sulla modalità di compilazione dell'applicazione front-end, è possibile aggiungere istruzioni di compilazione personalizzate nel flusso di lavoro. È quindi possibile configurare l'azione App Web statiche per ignorare il processo di compilazione automatica e distribuire semplicemente l'app compilata in un passaggio precedente.

Per ignorare la compilazione dell'app, `skip_app_build` impostare su e sul percorso della cartella da `true` `app_location` distribuire.

```yml
with:
  azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
  repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
  action: 'upload'
  ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
  app_location: 'dist' # Application build output generated by a previous step
  api_location: 'api' # Api source code path - optional
  output_location: '' # Leave this empty
  skip_app_build: true
  ###### End of Repository/Build Configurations ######
```

| Proprietà         | Descrizione                                                 |
| ---------------- | ----------------------------------------------------------- |
| `skip_app_build` | Impostare il valore su `true` per ignorare la compilazione dell'app front-end. |

> [!NOTE]
> È possibile ignorare solo la compilazione per l'app front-end. Se l'app ha un'API, verrà comunque compilata da GitHub Action di App Web statiche.

## <a name="route-file-location"></a>Percorso file di route

È possibile personalizzare il flusso di lavoro affinché cerchi il file [routes.json](routes.md) in qualsiasi cartella del repository. Nella sezione `with` di un processo è possibile definire la proprietà seguente.

| Proprietà          | Descrizione                                                                                                                                 |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| `routes_location` | Definisce il percorso della directory in cui è disponibile il file _routes.json_. Questo percorso è relativo alla radice del repository. |

È particolarmente importante essere espliciti sul percorso del file _routes.json_ se il passaggio di compilazione del framework front-end non sposta questo file in `output_location` per impostazione predefinita.

> [!IMPORTANT]
> La funzionalità definita nelroutes.js _nel_ file è ora deprecata. Vedere il file App Web statiche di Azure [file di](./configuration.md) configurazione per informazionistaticwebapp.config.js _su_.

## <a name="environment-variables"></a>Variabili di ambiente

È possibile impostare le variabili di ambiente per la compilazione tramite `env` la sezione della configurazione di un processo.

```yaml
jobs:
  build_and_deploy_job:
    if: github.event_name == 'push' || (github.event_name == 'pull_request' && github.event.action != 'closed')
    runs-on: ubuntu-latest
    name: Build and Deploy Job
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true
      - name: Build And Deploy
        id: builddeploy
        uses: Azure/static-web-apps-deploy@v0.0.1-preview
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
          repo_token: ${{ secrets.GITHUB_TOKEN }}
          action: 'upload'
          ###### Repository/Build Configurations
          app_location: '/'
          api_location: 'api'
          output_location: 'public'
          ###### End of Repository/Build Configurations ######
        env: # Add environment variables here
          HUGO_VERSION: 0.58.0
```

## <a name="monorepo-support"></a>Supporto monorepo

Un monorepo è un repository che contiene il codice per più di un'applicazione. Per impostazione predefinita, un file del flusso di lavoro di App Web statiche tiene traccia di tutti i file in un repository, ma è possibile adattarlo a una singola app come destinazione. Pertanto, per monorepos, ogni app statica ha il proprio file di configurazione che si risiede side-by-side nella cartella _.github/workflows_ del repository.

```files
├── .github
│   └── workflows
│       ├── azure-static-web-apps-purple-pond.yml
│       └── azure-static-web-apps-yellow-shoe.yml
│
├── app1  👉 controlled by: azure-static-web-apps-purple-pond.yml
├── app2  👉 controlled by: azure-static-web-apps-yellow-shoe.yml
│
├── api1  👉 controlled by: azure-static-web-apps-purple-pond.yml
├── api2  👉 controlled by: azure-static-web-apps-yellow-shoe.yml
│
└── README.md
```

Per impostare come destinazione un file del flusso di lavoro per una singola app, specificare i percorsi nelle `push` sezioni `pull_request` e .

L'esempio seguente illustra come aggiungere un nodo alle sezioni e di un `paths` `push` file denominato `pull_request` _azure-static-web-apps-purple-manipulation.yml_.

```yml
on:
  push:
    branches:
      - main
    paths:
      - app1/**
      - api1/**
      - .github/workflows/azure-static-web-apps-purple-pond.yml
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
      - main
    paths:
      - app1/**
      - api1/**
      - .github/workflows/azure-static-web-apps-purple-pond.yml
```

In questo caso, solo le modifiche apportate ai file seguenti attivano una nuova compilazione:

- Tutti i file all'interno _della cartella app1_
- Tutti i file all'interno della _cartella api1_
- Modifiche apportate al file del flusso di lavoro _azure-static-web-apps-purple-manipulation.yml dell'app_

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esaminare le richieste pull negli ambienti di pre-produzione](review-publish-pull-requests.md)
