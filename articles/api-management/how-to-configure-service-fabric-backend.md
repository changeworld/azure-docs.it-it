---
title: Configurare Service Fabric back-end in gestione API di Azure | Microsoft Docs
description: Come creare un back-end del servizio Service Fabric in gestione API di Azure usando il portale di Azure
services: api-management
documentationcenter: ''
author: dlepow
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 01/29/2021
ms.author: apimpm
ms.openlocfilehash: f6474dbd02c501612b951ddae490385a5d843fbf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99500630"
---
# <a name="set-up-a-service-fabric-backend-in-api-management-using-the-azure-portal"></a>Configurare un back-end Service Fabric in gestione API usando il portale di Azure

Questo articolo illustra come configurare un servizio di [Service Fabric](../service-fabric/service-fabric-api-management-overview.md) come back-end dell'API personalizzata usando il portale di Azure. A scopo dimostrativo, viene illustrato come configurare un servizio di base senza stato ASP.NET Core Reliable Service come Service Fabric back-end.

Per informazioni di base, vedere [backend in gestione API](backends.md).

## <a name="prerequisites"></a>Prerequisiti

Prerequisiti per la configurazione di un servizio di esempio in un cluster Service Fabric che esegue Windows come back-end personalizzato:

* **Ambiente di sviluppo Windows** : installare [Visual Studio 2019](https://www.visualstudio.com) e i carichi di lavoro sviluppo, **ASP.NET e sviluppo Web** di **Azure** e sviluppo **multipiattaforma .NET Core** . Configurare un [ambiente di sviluppo .NET](../service-fabric/service-fabric-get-started.md).

* **Service Fabric cluster** : vedere [esercitazione: distribuire un cluster Service fabric che esegue Windows in una rete virtuale di Azure](../service-fabric/service-fabric-tutorial-create-vnet-and-windows-cluster.md). È possibile creare un cluster con un certificato X. 509 esistente o a scopo di test creare un nuovo certificato autofirmato. Il cluster viene creato in una rete virtuale.

* **Esempio di app Service Fabric** : creare un'app per le API Web e distribuirla nel cluster di Service Fabric, come descritto in [integrare gestione API con Service fabric in Azure](../service-fabric/service-fabric-tutorial-deploy-api-management.md).

    Questi passaggi creano un servizio di base senza stato ASP.NET Core Reliable Service usando il modello di progetto API Web predefinito. Successivamente, viene esposto l'endpoint HTTP per questo servizio tramite gestione API di Azure.

    Prendere nota del nome dell'applicazione, ad esempio `fabric:/myApplication/myService` . 

* **Istanza di gestione API** : istanza di gestione API nuova o esistente nel livello **Premium** o  **Developer** e nella stessa area del cluster Service Fabric. Se necessario, [creare un'istanza di gestione API](get-started-create-service-instance.md).

* **Rete virtuale** : aggiungere l'istanza di gestione API alla rete virtuale creata per il cluster Service Fabric. Gestione API richiede una subnet dedicata nella rete virtuale.

  Per la procedura di abilitazione della connettività di rete virtuale per l'istanza di gestione API, vedere [come usare gestione API di Azure con le reti virtuali](api-management-using-with-vnet.md).

## <a name="create-backend---portal"></a>Creare back-end-portale

### <a name="add-service-fabric-cluster-certificate-to-api-management"></a>Aggiungere Service Fabric certificato del cluster a gestione API

Il certificato del cluster Service Fabric viene archiviato e gestito in un insieme di credenziali delle chiavi di Azure associato al cluster. Aggiungere questo certificato all'istanza di gestione API come certificato client.

Per la procedura di aggiunta di un certificato all'istanza di gestione API, vedere [come proteggere i servizi back-end usando l'autenticazione con certificati client in gestione API di Azure](api-management-howto-mutual-certificates.md). 

> [!NOTE]   
> È consigliabile aggiungere il certificato a gestione API facendo riferimento al certificato di Key Vault. 

### <a name="add-service-fabric-backend"></a>Aggiungi Service Fabric back-end

1. Nel [portale di Azure](https://portal.azure.com) accedere all'istanza di Gestione API.
1. In **API** selezionare **backend**  >  **+ Aggiungi**.
1. Immettere un nome di back-end e una descrizione facoltativa
1. In **tipo** selezionare **Service Fabric**.
1. In **URL runtime** immettere il nome del servizio back-end Service Fabric al quale le richieste vengono inviate da gestione API. Esempio: `fabric:/myApplication/myService`. 
1. In **numero massimo di tentativi di risoluzione della partizione** immettere un numero compreso tra 0 e 10.
1. Immettere l'endpoint di gestione del cluster Service Fabric. Questo endpoint è l'URL del cluster sulla porta `19080` , ad esempio `https://mysfcluster.eastus.cloudapp.azure.com:19080` .
1. In **certificato client** selezionare il certificato del cluster Service Fabric aggiunto all'istanza di gestione API nella sezione precedente.
1. In **metodo di autorizzazione endpoint di gestione** immettere un'identificazione personale o il nome X509 del server di un certificato utilizzato dal servizio di gestione cluster Service Fabric per la comunicazione TLS.
1. Abilitare le impostazioni **convalida catena di certificati** e **convalida nome certificato** .
1. In **credenziali di autorizzazione** fornire le credenziali, se necessario, per raggiungere il servizio back-end configurato in Service Fabric. Per l'app di esempio usata in questo scenario, le credenziali di autorizzazione non sono necessarie.
1. Selezionare **Crea**.

:::image type="content" source="media/backends/create-service-fabric-backend.png" alt-text="Creare un back-end di Service Fabric":::

## <a name="use-the-backend"></a>Usare il back-end

Per usare un back-end personalizzato, farvi riferimento usando i [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) criteri. Questo criterio trasforma l'URL di base del servizio back-end predefinito di una richiesta API in ingresso in un back-end specificato, in questo caso il back-end Service Fabric. 

I `set-backend-service` criteri possono essere utili con un'API esistente per trasformare una richiesta in ingresso in un back-end diverso rispetto a quello specificato nelle impostazioni dell'API. A scopo dimostrativo in questo articolo, creare un'API di test e impostare il criterio per indirizzare le richieste API al back-end Service Fabric. 

### <a name="create-api"></a>Create API

Per creare un'API vuota, seguire la procedura descritta in [aggiungere manualmente un'API](add-api-manually.md) .

* In Impostazioni API lasciare vuoto l' **URL del servizio Web** .
* Aggiungere un **suffisso dell'URL dell'API**, ad esempio *Fabric*.

  :::image type="content" source="media/backends/create-blank-api.png" alt-text="Creare un'API vuota":::

### <a name="add-get-operation-to-the-api"></a>Aggiungere un'operazione GET all'API

Come illustrato nella pagina relativa alla [distribuzione di un servizio back-end Service Fabric](../service-fabric/service-fabric-tutorial-deploy-api-management.md#deploy-a-service-fabric-back-end-service), il servizio di ASP.NET Core di esempio distribuito nel cluster Service Fabric supporta una singola operazione HTTP GET sul percorso URL `/api/values` .

La risposta predefinita su tale percorso è una matrice JSON di due stringhe:

```json
["value1", "value2"]
```

Per verificare l'integrazione di gestione API con il cluster, aggiungere l'operazione GET corrispondente all'API nel percorso `/api/values` :

1. Selezionare l'API creata nel passaggio precedente.
1. Selezionare **+ Aggiungi operazione**.
1. Nella finestra front- **end** , immettere i valori seguenti e selezionare **Salva**.

     | Impostazione             | Valore                             | 
    |---------------------|-----------------------------------|
    | **Nome visualizzato**    | *Test back-end*                       |  
    | **URL** | GET                               | 
    | **URL**             | `/api/values`                           | 
    
    :::image type="content" source="media/backends/configure-get-operation.png" alt-text="Aggiungere un'operazione GET all'API":::

### <a name="configure-set-backend-policy"></a>Configurare i `set-backend` criteri

Aggiungere i [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) criteri all'API di test.

1. Nella sezione **elaborazione in ingresso** della scheda **progettazione** selezionare l'icona Editor del codice ( **</>** ). 
1. Posizionare il cursore all'interno dell'elemento in **&lt; ingresso &gt;**
1. Aggiungere la seguente istruzione per i criteri. In `backend-id` sostituire il nome del back-end Service Fabric.

   `sf-resolve-condition`Si tratta di una condizione di ripetizione dei tentativi se la partizione del cluster non viene risolta. Il numero di tentativi è stato impostato durante la configurazione del back-end.

    ```xml
    <set-backend-service backend-id="mysfbackend" sf-resolve-condition="@(context.LastError?.Reason == "BackendConnectionFailure")"  />
    ```
1. Selezionare **Salva**.

    :::image type="content" source="media/backends/set-backend-service.png" alt-text="Configurare set-backend-criteri del servizio":::

### <a name="test-backend-api"></a>Testare l'API back-end

1. Nella scheda **test** selezionare l'operazione **Get** creata in una sezione precedente.
1. Selezionare **Invia**.

Una volta configurata correttamente, la risposta HTTP Mostra un codice di esito positivo HTTP e visualizza il codice JSON restituito dal servizio Service Fabric back-end.

:::image type="content" source="media/backends/test-backend-service.png" alt-text="Test del back-end Service Fabric":::

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [configurare i criteri](api-management-advanced-policies.md) per l'invio di richieste a un back-end
* I backend possono anche essere configurati con i modelli [API REST](/rest/api/apimanagement/2020-06-01-preview/backend)di gestione api, [Azure PowerShell](/powershell/module/az.apimanagement/new-azapimanagementbackend)o [Azure Resource Manager](../service-fabric/service-fabric-tutorial-deploy-api-management.md)

