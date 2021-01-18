---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/11/2020
ms.author: mikben
ms.openlocfilehash: 90bc9def9c1a78799f4931b58ad0a661602985e3
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98013193"
---
## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-nodejs-application"></a>Creare una nuova applicazione Node.js

Aprire la finestra del terminale o di comando, creare una nuova directory per l'app e passare a tale directory.

```console
mkdir calling-quickstart && cd calling-quickstart
```

Eseguire `npm init -y` per creare un file **package.json** con le impostazioni predefinite.

```console
npm init -y
```

### <a name="install-the-package"></a>Installare il pacchetto

Usare il comando `npm install` per installare la libreria client Chiamate di Servizi di comunicazione di Azure per JavaScript.

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

Per questa guida di avvio rapido sono consigliate le versioni di webpack seguenti:

```console
"webpack": "^4.42.0",
"webpack-cli": "^3.3.11",
"webpack-dev-server": "^3.10.3"
```

L'opzione `--save` elenca la libreria come dipendenza nel file **package.json**.

### <a name="set-up-the-app-framework"></a>Configurare il framework dell'app

Questa guida di avvio rapido usa webpack per aggregare gli asset dell'applicazione. Eseguire il comando seguente per installare i pacchetti npm webpack, webpack-cli e webpack-dev-server ed elencarli come dipendenze di sviluppo in **package.json**:

```console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```

Creare un file **index.html** nella directory radice del progetto. Questo file verrà usato per configurare un layout di base che consentirà all'utente di effettuare una chiamata.
