---
title: Effettuare richieste con Postman
titleSuffix: Azure Digital Twins
description: Informazioni su come configurare e usare Postman per testare Gemelli digitali di Azure API.
ms.author: baanders
author: baanders
ms.service: digital-twins
services: digital-twins
ms.topic: how-to
ms.date: 11/10/2020
ms.openlocfilehash: a528e224511fda363afb80a7749a018e07b5fa26
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588004"
---
# <a name="how-to-use-postman-to-send-requests-to-the-azure-digital-twins-apis"></a>Come usare Postman per inviare richieste alle API Gemelli digitali di Azure servizio

[Postman](https://www.getpostman.com/) è uno strumento di test REST che fornisce le principali funzionalità di richiesta HTTP in un'interfaccia utente grafica basata su desktop e plug-in. È possibile usarlo per creare richieste HTTP e inviarle [alle API REST Gemelli digitali di Azure.](how-to-use-apis-sdks.md)

Questo articolo descrive come configurare il [client REST Postman](https://www.getpostman.com/) per interagire con le API Gemelli digitali di Azure, seguendo questa procedura:

1. Usare l'interfaccia della riga di comando di Azure [**per ottenere bearer token**](#get-bearer-token) che si userà per effettuare richieste API in Postman.
1. Configurare una raccolta [**Postman e**](#about-postman-collections) il client REST Postman per usare il bearer token per l'autenticazione. Quando si configura la raccolta, è possibile scegliere una di queste opzioni:
    1. [**Importare**](#import-collection-of-azure-digital-twins-apis) una raccolta di richieste API Gemelli digitali di Azure predefinite.
    1. [**Creare**](#create-your-own-collection) una raccolta da zero.
1. [**Aggiungere richieste**](#add-an-individual-request) alla raccolta configurata e inviarle alle API Gemelli digitali di Azure servizio.

Gemelli digitali di Azure dispone di due set di API che è possibile usare: piano **dati** e piano **di controllo.** Per altre informazioni sulla differenza tra questi set di API, vedere Procedura: Usare le API Gemelli digitali di Azure [*e gli SDK.*](how-to-use-apis-sdks.md) Questo articolo contiene informazioni per entrambi i set di API.

## <a name="prerequisites"></a>Prerequisiti

Per procedere con l'uso di Postman per accedere alle API Gemelli digitali di Azure, è necessario configurare un'istanza Gemelli digitali di Azure e scaricare Postman. Nella parte restante di questa sezione vengono illustrati questi passaggi.

### <a name="set-up-azure-digital-twins-instance"></a>Configurare l'istanza di Gemelli digitali di Azure

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="download-postman"></a>Scaricare Postman

Scaricare quindi la versione desktop del client Postman. Passare a [*www.getpostman.com/apps*](https://www.getpostman.com/apps) e seguire le istruzioni per scaricare l'app.

## <a name="get-bearer-token"></a>Ottenere bearer token

Dopo aver configurato Postman e l'istanza di Gemelli digitali di Azure, è necessario ottenere un bearer token che le richieste Postman possono usare per autorizzare le API Gemelli digitali di Azure.

Esistono diversi modi per ottenere questo token. Questo articolo usa [l'interfaccia della riga di](/cli/azure/install-azure-cli) comando di Azure per accedere all'account Azure e ottenere un token in questo modo.

Se è installata un'interfaccia [della](/cli/azure/install-azure-cli)riga di comando di Azure in locale, è possibile avviare un prompt dei comandi nel computer per eseguire i comandi seguenti.
In caso contrario, è possibile [aprire Azure Cloud Shell](https://shell.azure.com) nel browser ed eseguire i comandi.

1. Prima di tutto, assicurarsi di aver eseguito l'accesso ad Azure con le credenziali appropriate eseguendo questo comando:

    ```azurecli-interactive
    az login
    ```

2. Usare quindi il [comando az account get-access-token](/cli/azure/account#az_account_get_access_token) per ottenere un bearer token con accesso al Gemelli digitali di Azure servizio. In questo comando si passerà l'ID risorsa per l'endpoint del servizio Gemelli digitali di Azure, per ottenere un token di accesso che possa accedere alle Gemelli digitali di Azure risorse. 

    Il contesto necessario per il token dipende dal set di API in uso, [](how-to-use-apis-sdks.md#overview-data-plane-apis) quindi usare [](how-to-use-apis-sdks.md#overview-control-plane-apis) le schede seguenti per selezionare tra le API del piano dati e del piano di controllo.

    # <a name="data-plane"></a>[Piano dati](#tab/data-plane)
    
    Per ottenere un token da usare con le API del **piano** dati, usare il valore statico seguente per il contesto del token: `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` . Si tratta dell'ID risorsa per l'endpoint Gemelli digitali di Azure servizio.
    
    ```azurecli-interactive
    az account get-access-token --resource 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```
    
    # <a name="control-plane"></a>[Piano di controllo](#tab/control-plane)
    
    Per ottenere un token da usare con le API del **piano** di controllo, usare il valore seguente per il contesto del token: `https://management.azure.com/` .
    
    ```azurecli-interactive
    az account get-access-token --resource https://management.azure.com/
    ```
    ---

    >[!NOTE]
    > Se è necessario accedere all'istanza di Gemelli digitali di Azure usando un'entità servizio o un account utente appartenente a un tenant Azure Active Directory diverso dall'istanza, è necessario richiedere un **token** dal tenant "home" dell'istanza di Gemelli digitali di Azure. Per altre informazioni su questo processo, vedere Procedura: Scrivere il codice [*di autenticazione dell'app.*](how-to-authenticate-client.md#authenticate-across-tenants)

3. Copiare il `accessToken` valore di nel risultato e salvarlo per usarlo nella sezione successiva. Si tratta del **valore del token** che verrà fornito a Postman per autorizzare le richieste.

    :::image type="content" source="media/how-to-use-postman/console-access-token.png" alt-text="Screenshot della console che mostra il risultato del comando az account get-access-token. Il campo accessToken e il relativo valore di esempio sono evidenziati.":::

>[!TIP]
>Questo token è valido per almeno cinque minuti e un massimo di 60 minuti. Se si esauri il tempo assegnato per il token corrente, è possibile ripetere i passaggi in questa sezione per ottenerne uno nuovo.

Successivamente, si configura Postman per l'uso di questo token per inviare richieste API Gemelli digitali di Azure.

## <a name="about-postman-collections"></a>Informazioni sulle raccolte Postman

Le richieste in Postman vengono salvate in **raccolte** (gruppi di richieste). Quando si crea una raccolta per raggruppare le richieste, è possibile applicare le impostazioni comuni a più richieste contemporaneamente. Ciò può semplificare notevolmente l'autorizzazione se si prevede di creare più di una richiesta per le API Gemelli digitali di Azure, perché è necessario configurare questi dettagli una sola volta per l'intera raccolta.

Quando si lavora Gemelli digitali di Azure, è possibile iniziare importando una raccolta predefinita di tutte le richieste [Gemelli digitali di Azure.](#import-collection-of-azure-digital-twins-apis) È possibile eseguire questa operazione se si esplorano le API e si vuole configurare rapidamente un progetto con esempi di richiesta.

In alternativa, è anche possibile scegliere [](#create-your-own-collection) di iniziare da zero, creando una raccolta vuota e popolarla con singole richieste che chiamano solo le API necessarie. 

Le sezioni seguenti descrivono entrambi questi processi. Il resto dell'articolo avviene nell'applicazione Postman locale, quindi procedere e aprire l'applicazione Postman nel computer.

## <a name="import-collection-of-azure-digital-twins-apis"></a>Importare la raccolta Gemelli digitali di Azure API

Un modo rapido per iniziare a Gemelli digitali di Azure in Postman è importare una raccolta di richieste predefinite per le API Gemelli digitali di Azure predefinite.

### <a name="download-the-collection-file"></a>Scaricare il file della raccolta

Il primo passaggio per importare il set di API consiste nel scaricare una raccolta. Scegliere la scheda seguente per scegliere il piano dati o il piano di controllo per visualizzare le opzioni di raccolta predefinite.

# <a name="data-plane"></a>[Piano dati](#tab/data-plane)

Attualmente sono disponibili due Gemelli digitali di Azure di piani dati tra cui è possibile scegliere:
* [**Gemelli digitali di Azure Postman Collection:**](https://github.com/microsoft/azure-digital-twins-postman-samples)questa raccolta offre un'esperienza introduttiva semplice per Gemelli digitali di Azure in Postman. Le richieste includono dati di esempio, quindi è possibile eseguirle con modifiche minime necessarie. Scegliere questa raccolta se si vuole un set di richieste API chiave digeribile contenente informazioni di esempio.
    - Per trovare la raccolta, passare al collegamento al repo e aprire il file denominato *postman_collection.jsin*.
* Gemelli digitali di Azure piano dati **[Swagger:](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins)** questo repo contiene il file Swagger completo per il set di API Gemelli digitali di Azure, che può essere scaricato e importato in Postman come raccolta. In questo modo verrà fornito un set completo di tutte le richieste API, ma con corpi dati vuoti anziché dati di esempio. Scegliere questa raccolta se si vuole avere accesso a ogni chiamata API e compilare tutti i dati manualmente.
    - Per trovare la raccolta, passare al collegamento al repo e scegliere la cartella per la versione più recente della specifica. Da qui aprire il file denominatodigitaltwins.js *in*.

# <a name="control-plane"></a>[Piano di controllo](#tab/control-plane)

La raccolta attualmente disponibile per il piano di controllo è [**Gemelli digitali di Azure controllo Swagger**](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins). Questo repo contiene il file Swagger completo per il set di API Gemelli digitali di Azure, che può essere scaricato e importato in Postman come raccolta. In questo modo verrà fornito un set completo di ogni richiesta API.

Per trovare la raccolta, passare al collegamento al repo e scegliere la cartella per la versione più recente della specifica. Da qui aprire il file denominatodigitaltwins.js *in*.

---

Ecco come scaricare la raccolta scelta nel computer in modo da poterla importare in Postman.
1. Usare i collegamenti precedenti per aprire il file di raccolta in GitHub nel browser.
1. Selezionare il **pulsante Non** elaborato per aprire il testo non elaborato del file.
    :::image type="content" source="media/how-to-use-postman/swagger-raw.png" alt-text="Screenshot del piano dati digitaltwins.jsfile in GitHub. Intorno al pulsante Non elaborato è presente un'evidenziazione." lightbox="media/how-to-use-postman/swagger-raw.png":::
1. Copiare il testo dalla finestra e incollarlo in un nuovo file nel computer.
1. Salvare il file con estensione *json* (il nome del file può essere quello desiderato, purché sia possibile ricordarlo per trovare il file in un secondo momento).

### <a name="import-the-collection"></a>Importare la raccolta

Importare quindi la raccolta in Postman.

1. Nella finestra principale di Postman selezionare il pulsante Import **(Importa).**
    :::image type="content" source="media/how-to-use-postman/postman-import-collection.png" alt-text="Screenshot di una finestra Postman appena aperta. Il pulsante &quot;Importa&quot; è evidenziato." lightbox="media/how-to-use-postman/postman-import-collection.png":::

1. Nella finestra Importa seguente selezionare **Carica** file e passare al file della raccolta nel computer creato in precedenza. Selezionare Apri.
1. Selezionare il **pulsante Importa** per confermare.

    :::image type="content" source="media/how-to-use-postman/postman-import-collection-2.png" alt-text="Screenshot della finestra &quot;Importa&quot; di Postman, che mostra il file da importare come raccolta e il pulsante Importa.":::

La raccolta appena importata è ora visibile dalla visualizzazione Principale di Postman, nella scheda Raccolte.

:::image type="content" source="media/how-to-use-postman/postman-post-collection-imported.png" alt-text="Screenshot della finestra principale di Postman. La raccolta appena importata è evidenziata nella scheda &quot;Raccolte&quot;." lightbox="media/how-to-use-postman/postman-post-collection-imported.png":::

Continuare quindi con la sezione successiva per aggiungere un bearer token alla raccolta per l'autorizzazione e connetterlo all'istanza di Gemelli digitali di Azure.

### <a name="configure-authorization"></a>Configurare l'autorizzazione

Modificare quindi la raccolta creata per configurare alcuni dettagli di accesso. Evidenziare la raccolta creata e selezionare **l'icona Visualizza altre azioni** per visualizzare un menu. Selezionare **Modifica**.

:::image type="content" source="media/how-to-use-postman/postman-edit-collection.png" alt-text="Screenshot di Postman. L'icona &quot;Visualizza altre azioni&quot; per la raccolta importata è evidenziata e &quot;Modifica&quot; è evidenziata nelle opzioni." lightbox="media/how-to-use-postman/postman-edit-collection.png":::

Seguire questa procedura per aggiungere un bearer token alla raccolta per l'autorizzazione. Qui si userà il valore del **token** raccolto nella sezione [Get bearer token](#get-bearer-token) per usarlo per tutte le richieste API nella raccolta.

1. Nella finestra di dialogo di modifica per la raccolta assicurarsi di essere nella **scheda** Autorizzazione. 

    :::image type="content" source="media/how-to-use-postman/postman-authorization-imported.png" alt-text="Screenshot della finestra di dialogo di modifica della raccolta importata in Postman, che mostra la scheda &quot;Autorizzazione&quot;." lightbox="media/how-to-use-postman/postman-authorization-imported.png":::

1. Impostare Tipo su **OAuth 2.0,** incollare il token di accesso nella casella Token di accesso e selezionare **Salva.**

    :::image type="content" source="media/how-to-use-postman/postman-paste-token-imported.png" alt-text="Screenshot della finestra di dialogo di modifica di Postman per la raccolta importata, nella scheda &quot;Autorizzazione&quot;. Il tipo è &quot;OAuth 2.0&quot; e la casella Token di accesso è evidenziata." lightbox="media/how-to-use-postman/postman-paste-token-imported.png":::

### <a name="additional-configuration"></a>Configurazione aggiuntiva

# <a name="data-plane"></a>[Piano dati](#tab/data-plane)

Se si crea una [raccolta](how-to-use-apis-sdks.md#overview-data-plane-apis) di piani dati, consentire alla raccolta di connettersi facilmente alle risorse Gemelli digitali di Azure impostando alcune variabili **fornite** con le raccolte. Quando molte richieste in una raccolta richiedono lo stesso valore ,ad esempio il nome host dell'istanza di Gemelli digitali di Azure, è possibile archiviare il valore in una variabile che si applica a ogni richiesta nella raccolta. Entrambe le raccolte scaricabili Gemelli digitali di Azure con variabili pre-create che è possibile impostare a livello di raccolta.

1. Sempre nella finestra di dialogo di modifica per la raccolta, passare alla **scheda** Variabili.

1. Usare il nome **host** dell'istanza nella [*sezione Prerequisiti*](#prerequisites) per impostare il campo CURRENT VALUE della variabile pertinente. Selezionare **Salva**.

    :::image type="content" source="media/how-to-use-postman/postman-variables-imported.png" alt-text="Screenshot della finestra di dialogo di modifica della raccolta importata in Postman, che mostra la scheda &quot;Variabili&quot;. Il campo &quot;CURRENT VALUE&quot; è evidenziato." lightbox="media/how-to-use-postman/postman-variables-imported.png":::

1. Se la raccolta include variabili aggiuntive, compilare e salvare anche questi valori.

Al termine dei passaggi precedenti, è stata completata la configurazione della raccolta. Se si vuole, è possibile chiudere la scheda di modifica per la raccolta.

# <a name="control-plane"></a>[Piano di controllo](#tab/control-plane)

Se si sta effettuando una raccolta [di piani di](how-to-use-apis-sdks.md#overview-control-plane-apis) controllo, sono stati evasi tutti gli elementi necessari per configurare la raccolta. Se si vuole, è possibile chiudere la scheda di modifica per la raccolta e passare alla sezione successiva.

--- 

### <a name="explore-requests"></a>Esplorare le richieste

Esaminare quindi le richieste all'interno della raccolta Gemelli digitali di Azure API. È possibile espandere la raccolta per visualizzare le richieste create in precedenza (ordinate per categoria di operazioni). 

Richieste diverse richiedono informazioni diverse sull'istanza e sui relativi dati. Per visualizzare tutte le informazioni necessarie per creare una richiesta specifica, cercare i dettagli della richiesta nella Gemelli digitali di Azure di riferimento [dell'API REST.](/rest/api/azure-digitaltwins/)

È possibile modificare i dettagli di una richiesta nella raccolta Postman usando questa procedura:

1. Selezionarlo dall'elenco per visualizzare i dettagli modificabili. 

1. Immettere i valori per le variabili elencate nella **scheda Parametri** in **Variabili percorso**.

    :::image type="content" source="media/how-to-use-postman/postman-request-details-imported.png" alt-text="Screenshot di Postman. La raccolta viene espansa per visualizzare una richiesta. La sezione &quot;Variabili di percorso&quot; è evidenziata nei dettagli della richiesta." lightbox="media/how-to-use-postman/postman-request-details-imported.png":::

1. Specificare le **intestazioni o** i dettagli **del** corpo necessari nelle rispettive schede.

Dopo aver specificato tutti i dettagli necessari, è possibile eseguire la richiesta con il **pulsante Invia.**

È anche possibile aggiungere richieste alla raccolta usando la procedura descritta nella sezione Aggiungere [*una singola*](#add-an-individual-request) richiesta di seguito.

## <a name="create-your-own-collection"></a>Creare una raccolta personale

Invece di importare la raccolta esistente di tutte Gemelli digitali di Azure API, è anche possibile creare una raccolta da zero. È quindi possibile popolarla con singole richieste usando la documentazione di riferimento [dell Gemelli digitali di Azure api REST.](/rest/api/azure-digitaltwins/)

### <a name="create-a-postman-collection"></a>Creare una raccolta di Postman

1. Per creare una raccolta, selezionare il **pulsante New** (Nuovo) nella finestra principale di Postman.

    :::image type="content" source="media/how-to-use-postman/postman-new.png" alt-text="Screenshot della finestra principale di Postman. Il pulsante &quot;Nuovo&quot; è evidenziato." lightbox="media/how-to-use-postman/postman-new.png":::

    Scegliere un tipo di **raccolta**.

    :::image type="content" source="media/how-to-use-postman/postman-new-collection-2.png" alt-text="Screenshot della finestra di dialogo &quot;Crea nuovo&quot; in Postman. L'opzione 'Collection' è evidenziata.":::

1. Verrà aperta una scheda per compilare i dettagli della nuova raccolta. Selezionare l'icona Modifica accanto al nome predefinito della raccolta (**Nuova** raccolta ) per sostituirla con il nome desiderato. 

    :::image type="content" source="media/how-to-use-postman/postman-new-collection-3.png" alt-text="Screenshot della finestra di dialogo di modifica della nuova raccolta in Postman. L'icona Modifica accanto al nome &quot;Nuova raccolta&quot; è evidenziata." lightbox="media/how-to-use-postman/postman-new-collection-3.png":::

Continuare quindi con la sezione successiva per aggiungere un bearer token alla raccolta per l'autorizzazione.

### <a name="configure-authorization"></a>Configurare l'autorizzazione

Seguire questa procedura per aggiungere un bearer token alla raccolta per l'autorizzazione. Qui si userà il valore del **token** raccolto nella sezione [Get bearer token](#get-bearer-token) per usarlo per tutte le richieste API nella raccolta.

1. Sempre nella finestra di dialogo di modifica per la nuova raccolta passare alla **scheda** Autorizzazione.

    :::image type="content" source="media/how-to-use-postman/postman-authorization-custom.png" alt-text="Screenshot della finestra di dialogo di modifica della nuova raccolta in Postman, che mostra la scheda &quot;Autorizzazione&quot;." lightbox="media/how-to-use-postman/postman-authorization-custom.png":::

1. Impostare Tipo su **OAuth 2.0,** incollare il token di accesso nella casella Token di accesso e selezionare **Salva**.

    :::image type="content" source="media/how-to-use-postman/postman-paste-token-custom.png" alt-text="Screenshot della finestra di dialogo di modifica postman per la nuova raccolta, nella scheda &quot;Autorizzazione&quot;. Il tipo è &quot;OAuth 2.0&quot; e la casella Token di accesso è evidenziata." lightbox="media/how-to-use-postman/postman-paste-token-custom.png":::

Al termine dei passaggi precedenti, è stata completata la configurazione della raccolta. Se si vuole, è possibile chiudere la scheda Modifica per la nuova raccolta.

La nuova raccolta può essere vista dalla visualizzazione Postman principale, nella scheda Raccolte.

:::image type="content" source="media/how-to-use-postman/postman-post-collection-custom.png" alt-text="Screenshot della finestra principale di Postman. La raccolta personalizzata appena creata è evidenziata nella scheda &quot;Raccolte&quot;." lightbox="media/how-to-use-postman/postman-post-collection-custom.png":::

## <a name="add-an-individual-request"></a>Aggiungere una singola richiesta

Ora che la raccolta è stata impostata, è possibile aggiungere le proprie richieste alle API di Gemelli digitali di Azure.

1. Per creare una richiesta, usare di **nuovo il pulsante** Nuovo.

    :::image type="content" source="media/how-to-use-postman/postman-new.png" alt-text="Screenshot della finestra principale di Postman. Il pulsante &quot;Nuovo&quot; è evidenziato." lightbox="media/how-to-use-postman/postman-new.png":::

    Scegliere un tipo di **richiesta**.

    :::image type="content" source="media/how-to-use-postman/postman-new-request-2.png" alt-text="Screenshot della finestra di dialogo &quot;Crea nuovo&quot; in Postman. L'opzione &quot;Richiesta&quot; è evidenziata.":::

1. Questa azione apre la finestra SAVE REQUEST, in cui è possibile immettere un nome per la richiesta, assegnargli una descrizione facoltativa e scegliere la raccolta di cui fa parte. Compilare i dettagli e salvare la richiesta nella raccolta creata in precedenza.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-use-postman/postman-save-request.png" alt-text="Screenshot della finestra &quot;Salva richiesta&quot; in Postman che mostra i campi descritti. Il pulsante &quot;Salva Gemelli digitali di Azure raccolta&quot; è evidenziato.":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

È ora possibile visualizzare la richiesta nella raccolta e selezionarla per estrarre i dettagli modificabili.

:::image type="content" source="media/how-to-use-postman/postman-request-details-custom.png" alt-text="Screenshot di Postman. La Gemelli digitali di Azure viene espansa per visualizzare i dettagli della richiesta." lightbox="media/how-to-use-postman/postman-request-details-custom.png":::

### <a name="set-request-details"></a>Impostare i dettagli della richiesta

Per inviare una richiesta Postman a una delle API Gemelli digitali di Azure, sono necessari l'URL dell'API e informazioni sui dettagli necessari. Queste informazioni sono disponibili nella documentazione di riferimento Gemelli digitali di Azure [API REST](/rest/api/azure-digitaltwins/).

Per procedere con una query di esempio, questo articolo userà l'API Query (e la relativa documentazione di [riferimento)](/rest/api/digital-twins/dataplane/query/querytwins)per eseguire una query per tutti i gemelli digitali in un'istanza.

1. Ottenere l'URL e il tipo della richiesta dalla documentazione di riferimento. Per l'API query, si tratta attualmente *di POST `https://digitaltwins-hostname/query?api-version=2020-10-31`*.
1. In Postman impostare il tipo per la richiesta e immettere l'URL della richiesta, inserendo i segnaposto nell'URL in base alle esigenze. Qui si userà il nome **host dell'istanza** nella [*sezione*](#prerequisites) Prerequisiti.
    
   :::image type="content" source="media/how-to-use-postman/postman-request-url.png" alt-text="Screenshot dei dettagli della nuova richiesta in Postman. L'URL della query dalla documentazione di riferimento è stato inserito nella casella url della richiesta." lightbox="media/how-to-use-postman/postman-request-url.png":::
    
1. Verificare che i parametri mostrati per la richiesta nella **scheda Parametri** corrispondano a quelli descritti nella documentazione di riferimento. Per questa richiesta in Postman, il parametro è stato compilato automaticamente quando è stato immesso `api-version` l'URL della richiesta nel passaggio precedente. Per l'API query, questo è l'unico parametro obbligatorio, quindi questo passaggio viene eseguito.
1. Nella scheda **Authorization (Autorizzazione)** impostare Type (Tipo) **su Inherit auth from parent (Eredita autenticazione da padre).** Ciò indica che questa richiesta userà l'autorizzazione impostata in precedenza per l'intera raccolta.
1. Verificare che le intestazioni visualizzate per la richiesta nella scheda **Intestazioni** corrispondano a quelle descritte nella documentazione di riferimento. Per questa richiesta, sono state compilate automaticamente diverse intestazioni. Per l'API query, nessuna delle opzioni di intestazione è obbligatoria, quindi questo passaggio viene eseguito.
1. Verificare che il corpo visualizzato per la richiesta nella **scheda Corpo** corrisponda alle esigenze descritte nella documentazione di riferimento. Per l'API Query è necessario un corpo JSON per fornire il testo della query. Di seguito è riportato un corpo di esempio per questa richiesta che esegue una query per tutti i gemelli digitali nell'istanza:

   :::image type="content" source="media/how-to-use-postman/postman-request-body.png" alt-text="Screenshot dei dettagli della nuova richiesta in Postman, nella scheda Corpo. Contiene un corpo JSON non elaborato con una query &quot;SELECT * FROM DIGITALTWINS&quot;." lightbox="media/how-to-use-postman/postman-request-body.png":::

   Per altre informazioni sulla creazione di Gemelli digitali di Azure query, vedere [*Procedura: Eseguire query sul grafo gemello.*](how-to-query-graph.md)

1. Controllare la documentazione di riferimento per tutti gli altri campi che potrebbero essere necessari per il tipo di richiesta. Per l'API Query, tutti i requisiti sono stati soddisfatti nella richiesta Postman, quindi questo passaggio viene eseguito.
1. Usare il **pulsante** Invia per inviare la richiesta completata.
   :::image type="content" source="media/how-to-use-postman/postman-request-send.png" alt-text="Screenshot di Postman che mostra i dettagli della nuova richiesta. Il pulsante Invia è evidenziato." lightbox="media/how-to-use-postman/postman-request-send.png":::

Dopo aver inviato la richiesta, i dettagli della risposta verranno visualizzati nella finestra Postman sotto la richiesta. È possibile visualizzare il codice di stato della risposta e qualsiasi testo del corpo.

:::image type="content" source="media/how-to-use-postman/postman-request-response.png" alt-text="Screenshot della richiesta inviata in Postman. Sotto i dettagli della richiesta viene visualizzata la risposta. Lo stato è 200 OK e il corpo mostra i risultati della query." lightbox="media/how-to-use-postman/postman-request-response.png":::

È anche possibile confrontare la risposta ai dati di risposta previsti nella documentazione di riferimento, per verificare il risultato o ottenere altre informazioni sugli eventuali errori che si verificano.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle API di Gemelli digitali, vedere [*Procedura:*](how-to-use-apis-sdks.md)Usare le API Gemelli digitali di Azure e gli SDK oppure visualizzare la documentazione di riferimento per le [API REST.](/rest/api/azure-digitaltwins/)