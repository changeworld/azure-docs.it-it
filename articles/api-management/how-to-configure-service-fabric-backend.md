---
title: Configurare il Service Fabric back-end in Azure API Management | Microsoft Docs
description: Come creare un back Service Fabric back-end del servizio API Management Azure usando il portale di Azure
services: api-management
documentationcenter: ''
author: dlepow
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 01/29/2021
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3dda6f18c2bf92b537c2f4be1c6a0a3b70cdc28a
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815883"
---
# <a name="set-up-a-service-fabric-backend-in-api-management-using-the-azure-portal"></a>Configurare un back-end Service Fabric in API Management usando il portale di Azure

Questo articolo illustra come configurare un servizio [Service Fabric](../service-fabric/service-fabric-api-management-overview.md) come back-end API personalizzato usando il portale di Azure. A scopo dimostrativo, viene illustrato come configurare un servizio Reliable ASP.NET Core senza stato di base Service Fabric back-end.

Per informazioni di base, [vedere Back-end in API Management](backends.md).

## <a name="prerequisites"></a>Prerequisiti

Prerequisiti per configurare un servizio di esempio in un cluster Service Fabric che esegue Windows come back-end personalizzato:

* **Ambiente di sviluppo Windows:** [installare Visual Studio 2019](https://www.visualstudio.com) e i carichi di lavoro Sviluppo di **Azure,** **sviluppo ASP.NET** e Web e Sviluppo multipiattaforma **.NET Core.** Configurare un [ambiente di sviluppo .NET](../service-fabric/service-fabric-get-started.md).

* **Service Fabric cluster -** Vedere [Esercitazione: Distribuire un cluster Service Fabric che esegue Windows in una rete virtuale di Azure.](../service-fabric/service-fabric-tutorial-create-vnet-and-windows-cluster.md) È possibile creare un cluster con un certificato X.509 esistente o a scopo di test creare un nuovo certificato autofirmato. Il cluster viene creato in una rete virtuale.

* **App Service Fabric di** esempio: creare un'app per le API Web e distribuirlo nel cluster Service Fabric come descritto in Integrare API Management [con Service Fabric in Azure.](../service-fabric/service-fabric-tutorial-deploy-api-management.md)

    Questi passaggi creano un'istanza senza stato di ASP.NET Core Reliable Service usando il modello di progetto API Web predefinito. In seguito si espone l'endpoint HTTP per questo servizio tramite Azure API Management.

    Prendere nota del nome dell'applicazione, ad esempio `fabric:/myApplication/myService` . 

* **API Management istanza:** istanza di API Management esistente o nuova nel livello **Premium** o  **Developer** e nella stessa area del cluster Service Fabric. Se necessario, creare [un'API Management istanza .](get-started-create-service-instance.md)

* **Rete virtuale:** aggiungere l'istanza API Management alla rete virtuale creata per il cluster Service Fabric rete virtuale. API Management richiede una subnet dedicata nella rete virtuale.

  Per la procedura per abilitare la connettività di rete virtuale per l'istanza API Management, vedere Come usare [Azure API Management con le reti virtuali.](api-management-using-with-vnet.md)

## <a name="create-backend---portal"></a>Creare un back-end - portale

### <a name="add-service-fabric-cluster-certificate-to-api-management"></a>Aggiungere Service Fabric certificato del cluster a API Management

Il Service Fabric del cluster viene archiviato e gestito in un insieme di credenziali delle chiavi di Azure associato al cluster. Aggiungere questo certificato all'istanza API Management come certificato client.

Per la procedura per aggiungere un certificato all'istanza API Management, vedere Come proteggere i servizi [back-end](api-management-howto-mutual-certificates.md)usando l'autenticazione del certificato client in Azure API Management . 

> [!NOTE]   
> È consigliabile aggiungere il certificato alla API Management facendo riferimento al certificato dell'insieme di credenziali delle chiavi. 

### <a name="add-service-fabric-backend"></a>Aggiungere Service Fabric back-end

1. Nel [portale di Azure](https://portal.azure.com) accedere all'istanza di Gestione API.
1. In **API selezionare** **Back-end**+  >  **Aggiungi**.
1. Immettere un nome back-end e una descrizione facoltativa
1. In **Tipo** selezionare **Service Fabric**.
1. In **URL di runtime** immettere il nome del Service Fabric back-end a cui API Management inoltrare le richieste. Esempio: `fabric:/myApplication/myService`. 
1. In **Numero massimo di tentativi di** risoluzione della partizione immettere un numero compreso tra 0 e 10.
1. Immettere l'endpoint di gestione Service Fabric cluster. Questo endpoint è l'URL del cluster sulla porta `19080` , ad esempio `https://mysfcluster.eastus.cloudapp.azure.com:19080` .
1. In **Certificato client** selezionare il Service Fabric del cluster aggiunto all'istanza API Management nella sezione precedente.
1. In **Metodo di autorizzazione dell'endpoint** di gestione immettere un'identificazione personale o un nome X509 del server di un certificato usato dal servizio di gestione cluster Service Fabric per la comunicazione TLS.
1. Abilitare le **impostazioni Convalida catena di certificati** e Convalida **nome** certificato.
1. In **Credenziali di autorizzazione** specificare le credenziali, se necessario, per raggiungere il servizio back-end configurato Service Fabric. Per l'app di esempio usata in questo scenario, le credenziali di autorizzazione non sono necessarie.
1. Selezionare **Crea**.

:::image type="content" source="media/backends/create-service-fabric-backend.png" alt-text="Creare un back-end di Service Fabric":::

## <a name="use-the-backend"></a>Usare il back-end

Per usare un back-end personalizzato, fare riferimento a esso usando i [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) criteri. Questo criterio trasforma l'URL di base del servizio back-end predefinito di una richiesta API in ingresso in un back-end specificato, in questo caso Service Fabric back-end. 

I criteri possono essere utili con un'API esistente per trasformare una richiesta in ingresso in un back-end diverso da quello `set-backend-service` specificato nelle impostazioni dell'API. A scopo dimostrativo in questo articolo, creare un'API di test e impostare i criteri per indirizzare le richieste API Service Fabric back-end. 

### <a name="create-api"></a>Create API

Seguire la procedura descritta in [Aggiungere un'API manualmente](add-api-manually.md) per creare un'API vuota.

* Nelle impostazioni API lasciare vuoto **l'URL del servizio** Web.
* Aggiungere un **suffisso DELL'URL dell'API,** ad esempio *fabric*.

  :::image type="content" source="media/backends/create-blank-api.png" alt-text="Creare un'API vuota":::

### <a name="add-get-operation-to-the-api"></a>Aggiungere l'operazione GET all'API

Come illustrato in Distribuire un servizio [back-end](../service-fabric/service-fabric-tutorial-deploy-api-management.md#deploy-a-service-fabric-back-end-service)di Service Fabric , il servizio ASP.NET Core di esempio distribuito nel cluster Service Fabric supporta una singola operazione HTTP GET nel percorso URL `/api/values` .

La risposta predefinita in tale percorso è una matrice JSON di due stringhe:

```json
["value1", "value2"]
```

Per testare l'integrazione API Management con il cluster, aggiungere l'operazione GET corrispondente all'API nel percorso `/api/values` :

1. Selezionare l'API creata nel passaggio precedente.
1. Selezionare **+ Aggiungi operazione**.
1. Nella finestra **Front-end** immettere i valori seguenti e selezionare **Salva**.

     | Impostazione             | Valore                             | 
    |---------------------|-----------------------------------|
    | **Nome visualizzato**    | *Testare il back-end*                       |  
    | **URL** | GET                               | 
    | **URL**             | `/api/values`                           | 
    
    :::image type="content" source="media/backends/configure-get-operation.png" alt-text="Aggiungere l'operazione GET all'API":::

### <a name="configure-set-backend-policy"></a>Configurare i `set-backend` criteri

Aggiungere i [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) criteri all'API di test.

1. Nella sezione **Elaborazione** in ingresso della **scheda** Progettazione selezionare l'icona dell'editor di codice ( **</>** ). 
1. Posizionare il cursore all'interno **&lt; dell'elemento in &gt;** ingresso
1. Aggiungere l'istruzione di criteri seguente. In `backend-id` sostituire il nome dell'Service Fabric back-end.

   è `sf-resolve-condition` una condizione di ripetizione dei tentativi se la partizione del cluster non viene risolta. Il numero di tentativi è stato impostato durante la configurazione del back-end.

    ```xml
    <set-backend-service backend-id="mysfbackend" sf-resolve-condition="@(context.LastError?.Reason == "BackendConnectionFailure")"  />
    ```
1. Selezionare **Salva**.

    :::image type="content" source="media/backends/set-backend-service.png" alt-text="Configurare i criteri set-back-end-service":::

### <a name="test-backend-api"></a>TESTARE l'API back-end

1. Nella scheda **Test** selezionare l'operazione **GET** creata in una sezione precedente.
1. Selezionare **Invia**.

Se configurata correttamente, la risposta HTTP mostra un codice di esito positivo HTTP e visualizza il codice JSON restituito dal servizio Service Fabric back-end.

:::image type="content" source="media/backends/test-backend-service.png" alt-text="Testare Service Fabric back-end":::

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [configurare i criteri per](api-management-advanced-policies.md) inoltrare le richieste a un back-end
* I back-end possono essere configurati anche usando [l'API REST](/rest/api/apimanagement/2020-06-01-preview/backend)di API Management, [Azure PowerShell](/powershell/module/az.apimanagement/new-azapimanagementbackend)o [Azure Resource Manager modello](../service-fabric/service-fabric-tutorial-deploy-api-management.md)

