---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 4a493d5f0d34cd4621d55c0371036c03e267c466
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105108281"
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

Usare il `npm install` comando per installare i servizi di comunicazione di Azure che chiamano SDK per JavaScript.

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
