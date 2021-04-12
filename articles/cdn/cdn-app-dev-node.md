---
title: Introduzione ad Azure CDN SDK per Node.js | Documentazione Microsoft
description: Informazioni su come creare una semplice applicazione console Node.js che illustra come automatizzare la creazione e la gestione dei profili e degli endpoint della rete CDN di Azure.
services: cdn
documentationcenter: nodejs
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: c4bb6a61-de3d-4f0c-9dca-202554c43dfa
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/02/2021
ms.author: mazha
ms.custom: devx-track-js
ms.openlocfilehash: 386a424e45d1b718b68cbbf53322fd704317a06b
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285225"
---
# <a name="get-started-with-azure-cdn-development"></a>Introduzione allo sviluppo della rete CDN di Azure
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
> 
> 

È possibile usare [Azure CDN SDK per JavaScript](https://www.npmjs.com/package/@azure/arm-cdn) per automatizzare la creazione e la gestione dei profili e degli endpoint della rete CDN.  Questa esercitazione illustra nel dettaglio la creazione di una semplice applicazione console Node.js che dimostra varie operazioni tra quelle disponibili.  Questa esercitazione non è destinata alla descrizione dettagliata di tutti gli aspetti di Azure CDN SDK per JavaScript.

Per completare questa esercitazione, è necessario che sia già stata installata e configurata [Node.js](https://www.nodejs.org) **6. x. x** o versione successiva.  Per creare l'applicazione Node.js è possibile usare qualsiasi editor di testo.  Per scrivere questa esercitazione è stato usato [Visual Studio Code](https://code.visualstudio.com).  


[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-npm-dependencies"></a>Creare il progetto e aggiungere le dipendenze NPM
Ora che abbiamo creato un gruppo di risorse per i profili di rete CDN e assegnato all'applicazione Azure AD l'autorizzazione per gestire i profili e gli endpoint della rete CDN all'interno del gruppo, è possibile iniziare a creare l'applicazione.

Creare una cartella in cui archiviare l'applicazione.  Da una console con gli strumenti Node.js nel percorso corrente, posizionarsi sulla nuova cartella e inizializzare il progetto eseguendo:

```console
npm init
```

Verrà quindi visualizzata una serie di domande per inizializzare il progetto.  Come **punto di ingresso** questa esercitazione usa *app.js*.  È possibile visualizzare le altre scelte nell'esempio seguente.

![Output NPM iniziale](./media/cdn-app-dev-node/cdn-npm-init.png)

A questo punto il progetto viene inizializzato con un file *packages.json* .  Il progetto userà alcune librerie di Azure contenute in pacchetti NPM.  La libreria verrà usata per l'autenticazione Azure Active Directory in Node.js ( @azure/ms-rest-nodeauth ) e nella libreria client della rete CDN di Azure per JavaScript ( @azure/arm-cdn ).  Aggiungere tali elementi al progetto come dipendenze.

```console
npm install --save @azure/ms-rest-nodeauth
npm install --save @azure/arm-cdn
```

Al termine dell'installazione dei pacchetti, il file *package.json* dovrebbe avere un aspetto simile a questo esempio, anche se i numeri di versione possono variare:

``` json
{
  "name": "cdn_node",
  "version": "1.0.0",
  "description": "Azure CDN Node.js tutorial project",
  "main": "app.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "Cam Soper",
  "license": "MIT",
  "dependencies": {
    "@azure/arm-cdn": "^5.2.0",
    "@azure/ms-rest-nodeauth": "^3.0.0"
  }
}
```

Infine, usare l'editor di testo per creare un file di testo vuoto e salvarlo nella radice della cartella di progetto denominandolo *app.js*.  Ora è possibile iniziare a scrivere codice.

## <a name="requires-constants-authentication-and-structure"></a>Istruzioni require, costanti, autenticazione e struttura
Aprire *app.js* nell'editor e scrivere la struttura di base del programma.

1. Aggiungere le istruzioni "require" per i pacchetti NPM all'inizio inserendo il codice seguente:
   
    ``` javascript
    var msRestAzure = require('@azure/ms-rest-nodeauth');
    const { CdnManagementClient } = require('@azure/arm-cdn');
    ```
2. È necessario definire alcune costanti che i metodi useranno.  Aggiungere il codice seguente.  Sostituire i segnaposto, incluse le **&lt;parentesi acute&gt;** , con i valori necessari.
   
    ``` javascript
    //Tenant app constants
    const clientId = "<YOUR CLIENT ID>";
    const clientSecret = "<YOUR CLIENT AUTHENTICATION KEY>"; //Only for service principals
    const tenantId = "<YOUR TENANT ID>";
   
    //Application constants
    const subscriptionId = "<YOUR SUBSCRIPTION ID>";
    const resourceGroupName = "CdnConsoleTutorial";
    const resourceLocation = "<YOUR PREFERRED AZURE LOCATION, SUCH AS Central US>";
    ```
3. Successivamente, verrà creata un'istanza del client di gestione della rete CDN a cui verranno assegnate le credenziali.
   
    ``` javascript
    var credentials = new msRestAzure.ApplicationTokenCredentials(clientId, tenantId, clientSecret);
    var cdnClient = new CdnManagementClient(credentials, subscriptionId);
    ```

4. L'applicazione console Node.js necessita di alcuni parametri della riga di comando.  Verificare che venga passato almeno un parametro.
   
   ```javascript
   //Collect command-line parameters
   var parms = process.argv.slice(2);
   
   //Do we have parameters?
   if(parms == null || parms.length == 0)
   {
       console.log("Not enough parameters!");
       console.log("Valid commands are list, delete, create, and purge.");
       process.exit(1);
   }
   ```
5. Nella parte principale del programma si passa poi ad altre funzioni, in base ai parametri che sono stati passati.
   
    ```javascript
    switch(parms[0].toLowerCase())
    {
        case "list":
            cdnList();
            break;
   
        case "create":
            cdnCreate();
            break;
   
        case "delete":
            cdnDelete();
            break;
   
        case "purge":
            cdnPurge();
            break;
   
        default:
            console.log("Valid commands are list, delete, create, and purge.");
            process.exit(1);
    }
    ```
6. In diversi punti del programma sarà necessario assicurarsi che venga passato il numero appropriato di parametri e visualizzare gli argomenti della Guida in caso di inesattezze.  Creare le funzioni necessarie a tale scopo.
   
   ```javascript
   function requireParms(parmCount) {
       if(parms.length < parmCount) {
           usageHelp(parms[0].toLowerCase());
           process.exit(1);
       }
   }
   
   function usageHelp(cmd) {
       console.log("Usage for " + cmd + ":");
       switch(cmd)
       {
           case "list":
               console.log("list profiles");
               console.log("list endpoints <profile name>");
               break;
   
           case "create":
               console.log("create profile <profile name>");
               console.log("create endpoint <profile name> <endpoint name> <origin hostname>");
               break;
   
           case "delete":
               console.log("delete profile <profile name>");
               console.log("delete endpoint <profile name> <endpoint name>");
               break;
   
           case "purge":
               console.log("purge <profile name> <endpoint name> <path>");
               break;
   
           default:
               console.log("Invalid command.");
       }
   }
   ```
7. Le funzioni da usare nel client di gestione della rete CDN sono asincrone. È quindi necessario un metodo di richiamata dopo l'esecuzione delle funzioni.  Creare un metodo che possa visualizzare l'output del client di gestione dell'eventuale rete CDN e uscire dal programma normalmente.
   
    ```javascript
    function callback(err, result, request, response) {
        if (err) {
            console.log(err);
            process.exit(1);
        } else {
            console.log((result == null) ? "Done!" : result);
            process.exit(0);
        }
    }
    ```

Ora che la struttura di base del programma è stata scritta, è necessario creare le funzioni che vengono chiamate in base ai parametri.

## <a name="list-cdn-profiles-and-endpoints"></a>Elencare i profili e gli endpoint della rete CDN
Scrivere il codice necessario per elencare i profili e gli endpoint esistenti.  I commenti al codice forniscono la sintassi prevista che permette di determinare la posizione dei parametri.

```javascript
// list profiles
// list endpoints <profile name>
function cdnList(){
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        case "profiles":
            console.log("Listing profiles...");
            cdnClient.profiles.listByResourceGroup(resourceGroupName, callback);
            break;

        case "endpoints":
            requireParms(3);
            console.log("Listing endpoints...");
            cdnClient.endpoints.listByProfile(resourceGroupName, parms[2], callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="create-cdn-profiles-and-endpoints"></a>Creare profili ed endpoint della rete CDN
Scrivere le funzioni per la creazione dei profili e degli endpoint.

```javascript
function cdnCreate() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        case "profile":
            cdnCreateProfile();
            break;

        case "endpoint":
            cdnCreateEndpoint();
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}

// create profile <profile name>
function cdnCreateProfile() {
    requireParms(3);
    console.log("Creating profile...");
    var standardCreateParameters = {
        location: resourceLocation,
        sku: {
            name: 'Standard_Verizon'
        }
    };

    cdnClient.profiles.create( resourceGroupName, parms[2], standardCreateParameters, callback);
}

// create endpoint <profile name> <endpoint name> <origin hostname>        
function cdnCreateEndpoint() {
    requireParms(5);
    console.log("Creating endpoint...");
    var endpointProperties = {
        location: resourceLocation,
        origins: [{
            name: parms[4],
            hostName: parms[4]
        }]
    };

    cdnClient.endpoints.create(resourceGroupName, parms[2], parms[3], endpointProperties, callback);
}
```

## <a name="purge-an-endpoint"></a>Ripulire un endpoint
Supponendo che l'endpoint sia stato creato, è consigliabile eseguire nel programma un'attività comune di eliminazione del contenuto dell'endpoint.

```javascript
// purge <profile name> <endpoint name> <path>
function cdnPurge() {
    requireParms(4);
    console.log("Purging endpoint...");
    var purgeContentPaths = [ parms[3] ];
    cdnClient.endpoints.purgeContent(resourceGroupName, parms[2], parms[3], purgeContentPaths, callback);
}
```

## <a name="delete-cdn-profiles-and-endpoints"></a>Eliminare profili ed endpoint della rete CDN
L'ultima funzione da includere elimina gli endpoint e i profili.

```javascript
function cdnDelete() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        // delete profile <profile name>
        case "profile":
            requireParms(3);
            console.log("Deleting profile...");
            cdnClient.profiles.deleteMethod(resourceGroupName, parms[2], callback);
            break;

        // delete endpoint <profile name> <endpoint name>
        case "endpoint":
            requireParms(4);
            console.log("Deleting endpoint...");
            cdnClient.endpoints.deleteMethod(resourceGroupName, parms[2], parms[3], callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="running-the-program"></a>Esecuzione del programma
Ora è possibile eseguire il programma Node.js con un debugger a scelta o alla console.

> [!TIP]
> Se si usa Visual Studio Code come debugger, è necessario configurare l'ambiente per passare i parametri della riga di comando.  Visual Studio Code esegue questa operazione nel file **launch.json**.  Cercare una proprietà denominata **args** e aggiungere una matrice di valori stringa per i parametri, in modo che abbia un aspetto analogo al seguente: `"args": ["list", "profiles"]`.
> 
> 

Iniziare a elencare i profili.

![Elencare i profili](./media/cdn-app-dev-node/cdn-list-profiles.png)

Viene restituita una matrice vuota.  Si tratta di un risultato previsto, dato che non c'è alcun profilo nel gruppo di risorse.  Creare un profilo.

![Creare il profilo](./media/cdn-app-dev-node/cdn-create-profile.png)

Aggiungere un endpoint.

![Creare un endpoint](./media/cdn-app-dev-node/cdn-create-endpoint.png)

Infine, eliminare il profilo.

![Elimina profilo](./media/cdn-app-dev-node/cdn-delete-profile.png)

## <a name="next-steps"></a>Passaggi successivi

Per informazioni di riferimento sull'SDK della rete CDN di Azure per JavaScript, vedere il [riferimento](https://docs.microsoft.com/javascript/api/@azure/arm-cdn).

Per ulteriori informazioni su Azure SDK per JavaScript, vedere la pagina relativa al [riferimento completo](https://docs.microsoft.com/javascript/api/?view=azure-node-latest).

Gestire le risorse della rete CDN con [PowerShell](cdn-manage-powershell.md).
