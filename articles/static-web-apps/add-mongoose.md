---
title: 'Esercitazione: accedere ai dati in Cosmos DB usando mangusta con le app Web statiche di Azure'
description: Informazioni su come accedere ai dati in Cosmos DB usando Mangusta da una funzione API di app Web statiche di Azure.
author: GeekTrainer
ms.author: chrhar
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 01/25/2021
ms.openlocfilehash: f64cc67ad6f0296ad289d858795ee783943f3daf
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107260073"
---
# <a name="tutorial-access-data-in-cosmos-db-using-mongoose-with-azure-static-web-apps"></a>Esercitazione: accedere ai dati in Cosmos DB usando mangusta con le app Web statiche di Azure

[Mangusta](https://mongoosejs.com/) è il client ODM (Object document mapping) più diffuso per Node.js. Per progettare una struttura di dati e applicare la convalida, Mangusta fornisce tutti gli strumenti necessari per interagire con i database che supportano l'API Mangusta. [Cosmos DB](../cosmos-db/mongodb-introduction.md) supporta le API Mangusta necessarie ed è disponibile come opzione del server back-end in Azure.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> - Creare un account Cosmos DB senza server
> - Creare app Web statiche di Azure
> - Aggiornare le impostazioni dell'applicazione per archiviare la stringa di connessione

Se non si ha una sottoscrizione di Azure, creare un [account di valutazione gratuito](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Prerequisiti

- Un [account Azure](https://azure.microsoft.com/free/)
- Un [account GitHub](https://github.com/join)

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="create-a-cosmos-db-serverless-database"></a>Creare un Cosmos DB database senza server

Per iniziare, creare un account [Cosmos DB senza server](https://docs.microsoft.com/azure/cosmos-db/serverless) . Se si usa un account senza server, si paga solo per le risorse man mano che vengono usate ed evitare la necessità di creare un'infrastruttura completa.

1. Passare a [https://portal.azure.com](https://portal.azure.com)
2. Fare clic su **Crea una risorsa**
3. Immettere **Azure Cosmos DB** nella casella di ricerca
4. Fare clic su **Azure Cosmos DB**
5. Fare clic su **Crea**
6. Configurare l'account Azure Cosmos DB con le informazioni seguenti
    - Sottoscrizione: scegliere la sottoscrizione che si vuole usare
    - Risorsa: fare clic su **Crea nuovo** e impostare il nome su **Aswa-Mangusta**
    - Nome account: è necessario un valore univoco
    - API: **Azure Cosmos DB per l'API MongoDB**
    - Notebook (anteprima): **disattivato**
    - Località: **Stati Uniti occidentali 2**
    - Modalità di capacità: senza **Server (anteprima)**
    - Versione: **3,6**
    - Zone di disponibilità: **Disabilita** 
 :::image type="content" source="media/add-mongoose/cosmos-db.png" alt-text="creazione nuova istanza Cosmos DB":::
7. Fare clic su **Rivedi e crea**.
8. Fare clic su **Crea**

Il processo di creazione può richiedere alcuni minuti. I passaggi successivi torneranno al database per raccogliere la stringa di connessione.

## <a name="create-a-static-web-app"></a>Creare un'app Web statica

Questa esercitazione usa un repository di modelli GitHub per semplificare la creazione dell'applicazione.

1. Passare al [modello di avvio](https://github.com/login?return_to=/staticwebdev/mongoose-starter/generate)
2. Scegliere il **proprietario** (se si usa un'organizzazione diversa dall'account principale)
3. Denominare il repository **Aswa-Mangusta-tutorial**
4. Fare clic su **Crea repository da modello**
5. Tornare al [portale di Azure](https://portal.azure.com)
6. Fare clic su **Crea una risorsa**
7. Digitare **app Web statiche** nella casella di ricerca
8. Seleziona **app Web statica (anteprima)**
9. Fare clic su **Crea**
10. Configurare l'app Web statica di Azure con le informazioni seguenti
    - Sottoscrizione: scegliere la stessa sottoscrizione di prima
    - Gruppo di risorse: selezionare **Aswa-Mangusta**
    - Nome: **Aswa-Mangusta-esercitazione**
    - Area: **Stati Uniti occidentali 2**
    - Fare clic su **Accedi con GitHub**
    - Fare clic su **autorizza** se viene richiesto di consentire alle app Web statiche di Azure di creare l'azione GitHub per abilitare la distribuzione
    - Organizzazione: nome dell'account
    - Repository: **Aswa-Mangusta-esercitazione**
    - Ramo: **Main**
    - Set di impostazioni di compilazione: scegliere **personalizzata**
    - Percorso dell'app: **/public**
    - Percorso API: **API**
    - Percorso artefatto dell'app: *lasciare vuoto* il 
     :::image type="content" source="media/add-mongoose/azure-static-web-apps.png" alt-text="modulo delle app Web statiche di Azure completate":::
11. Fare clic su **Verifica e crea**
12. Fare clic su **Crea**

Il processo di creazione richiede alcuni minuti, ma è possibile fare clic su **Vai alla risorsa** una volta eseguito il provisioning dell'app.

## <a name="configure-database-connection-string"></a>Configurare la stringa di connessione del database

Per consentire all'app Web di comunicare con il database, la stringa di connessione del database viene archiviata come impostazione dell'applicazione. L'impostazione dei valori è accessibile in Node.js utilizzando l' `process.env` oggetto.

1. Fare clic su **Home** nell'angolo superiore sinistro del portale di Azure (oppure tornare a [https://portal.azure.com](https://portal.azure.com) )
2. Fare clic su **Gruppi di risorse**
3. Fare clic su **Aswa-Mangusta**
4. Fare clic sul nome dell'account del database, che avrà un tipo di **account Azure Cosmos DB**
5. In **Impostazioni** fare clic su **stringa di connessione**
6. Copiare la stringa di connessione elencata in **stringa di connessione primaria**
7. Nella finestra di navigazione fare clic su **Aswa-Mangusta**
8. Fare clic su **Aswa-Mangusta-tutorial** per tornare all'istanza del sito Web
9. In **Impostazioni** fare clic su **configurazione**
10. Fare clic su **Aggiungi** e creare una nuova impostazione dell'applicazione con i valori seguenti
    - Nome: **CONNECTION_STRING**
    - Valore: incollare la stringa di connessione copiata in precedenza
11. Fare clic su **OK**.
12. Fare clic su **Save** (Salva).

## <a name="navigate-to-your-site"></a>Passare al sito

È ora possibile esplorare l'app Web statica.

1. Fare clic su **Panoramica**
1. Fare clic sull'URL visualizzato in alto a destra
    1. L'aspetto sarà simile a `https://calm-pond-05fcdb.azurestaticapps.net`
1. Creare una nuova attività digitando un titolo e facendo clic su **Aggiungi attività**
1. Verificare che l'attività sia visualizzata (l'operazione potrebbe richiedere qualche istante)
1. Contrassegnare l'attività come completa facendo **clic sulla casella** di controllo
1. **Aggiornare la pagina** per confermare l'uso di un database

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare questa applicazione, eliminare il gruppo di risorse seguendo questa procedura:

1. Tornare al [portale di Azure](https://portal.azure.com)
2. Fare clic su **Gruppi di risorse**
3. Fare clic su **Aswa-Mangusta**
4. Fare clic su **Elimina gruppo di risorse**
5. Digitare **Aswa-Mangusta** nella casella di testo
6. Fare clic su **Elimina**

## <a name="next-steps"></a>Passaggi successivi

Passare all'articolo successivo per informazioni su come configurare lo sviluppo locale...
> [!div class="nextstepaction"]
> [Configurare lo sviluppo locale](./local-development.md)
 
