---
title: 'Guida introduttiva: creare un profilo standard/Premium di Azure front door-portale di Azure'
description: Questa Guida introduttiva illustra come usare il servizio Azure front door standard/Premium per un'applicazione Web globale a disponibilità elevata e a elevate prestazioni usando il portale di Azure.
services: frontdoor
author: duongau
manager: KumudD
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 175fb82a5fdf300915f89c3d8cdc238638a742e1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105565130"
---
# <a name="quickstart-create-an-azure-front-door-standardpremium-profile---azure-portal"></a>Guida introduttiva: creare un profilo standard/Premium di Azure front door-portale di Azure

> [!Note]
> Questa documentazione è destinata a Azure front door standard/Premium (Preview). Stai cercando informazioni su Azure front door? Visualizzare i [documenti di Azure front door](../front-door-overview.md).

In questa Guida introduttiva si apprenderà come creare un profilo standard/Premium di Azure front door usando il portale di Azure. È possibile creare il profilo standard/Premium di Azure front door tramite *creazione rapida* con configurazioni di base o tramite *creazione personalizzata* con configurazioni più avanzate. Con *creazione personalizzata* si distribuiscono due app Web. Si crea quindi il profilo standard/Premium di Azure front door usando le due app Web come origine. Si verificherà quindi la connettività alle app Web usando il nome host front-end di Azure front door standard/Premium.

> [!IMPORTANT]
> Azure front door standard/Premium (anteprima) è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-front-door-profile---quick-create"></a>Crea profilo sportello anteriore-creazione rapida

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Dal menu home page o Azure selezionare **+ Crea una risorsa**. Cercare *front door standard/Premium (Preview)*. Quindi selezionare **Crea**

1. Nella pagina **Confronta offerte** selezionare **creazione rapida**. Quindi selezionare **continua per creare una porta anteriore**.

   :::image type="content" source="../media/create-front-door-portal/front-door-quick-create.png" alt-text="Screenshot delle offerte di confronto.":::

1. Nella pagina **Crea un profilo di sportello anteriore** immettere o selezionare le impostazioni seguenti.

    :::image type="content" source="../media/create-front-door-portal/front-door-quick-create-2.png" alt-text="Screenshot della pagina di creazione rapida di sportello anteriore.":::    

    | Impostazioni | valore |
    | --- | --- |
    | **Sottoscrizione**  | Selezionare la propria sottoscrizione. |
    | **Gruppo di risorse**  | Selezionare **Crea nuovo** e immettere *Contoso-Appservice* nella casella di testo.|
    | **Nome** | Assegnare un nome al profilo. Questo esempio usa **Contoso-AFD-quickcreate**. |
    | **Livello** | Selezionare uno SKU standard o Premium. Lo SKU standard è ottimizzato per la distribuzione di contenuti. Lo SKU Premium si basa su SKU standard ed è incentrato sulla sicurezza. Vedere [confronto tra livelli](tier-comparison.md).  |
    | **Nome endpoint** | Immettere un nome univoco globale per l'endpoint. |
    | **Tipo di origine** | Selezionare il tipo di risorsa per l'origine. In questo esempio si seleziona un servizio app come origine con collegamento privato abilitato. |
    | **Nome host di origine** | Immettere il nome host per l'origine. |
    | **Abilita collegamento privato** | Se si vuole avere una connessione privata tra il front-end di Azure e l'origine. Per altri dettagli, fare riferimento alle [linee guida per il collegamento privato](concept-private-link.md) e [abilitare il collegamento privato](./how-to-enable-private-link-web-app.md).
    | **Memorizzazione nella cache** | Selezionare la casella di controllo se si desidera memorizzare nella cache il contenuto più vicino agli utenti a livello globale usando i pop Edge e la rete Microsoft di Azure front door. |
    | **Criteri di WAF** | Selezionare **Crea nuovo** o selezionare un criterio WAF esistente nell'elenco a discesa se si vuole abilitare questa funzionalità. |

    > [!NOTE]
    > Quando si crea un profilo standard/Premium di Azure front door, è necessario selezionare un'origine dalla stessa sottoscrizione in cui viene creata la porta anteriore.

1. Selezionare **Verifica + crea** per fare in modo che il profilo della porta anteriore sia attivo e in esecuzione.

   > [!NOTE]
   > Potrebbero essere necessari alcuni minuti per la propagazione delle configurazioni a tutti i pop perimetrali.

1. Fare quindi clic su **Crea** per fare in modo che il profilo della porta anteriore sia distribuito e in esecuzione.

1. Se è stato abilitato il collegamento privato, passare all'origine (servizio app in questo esempio). Selezionare **rete**  >  **Configura collegamento privato**. Quindi selezionare la richiesta in sospeso da sportello anteriore di Azure e fare clic su approva. Dopo alcuni secondi, l'applicazione sarà accessibile tramite l'ingresso di Azure in modo sicuro.

## <a name="create-front-door-profile---custom-create"></a>Crea profilo sportello anteriore-creazione personalizzata

### <a name="create-a-web-app-with-two-instances-as-the-origin"></a>Creare un'app Web con due istanze come origine

Se è già stata configurata un'origine o un gruppo di origine, passare a creare una porta anteriore standard/Premium (anteprima) per l'applicazione.

In questo esempio viene creata un'applicazione Web con due istanze in esecuzione in diverse aree di Azure. Entrambe le istanze dell'applicazione Web vengono eseguite in modalità *attiva/attiva*, in modo una possa acquisire traffico. Questa configurazione è diversa da una configurazione *attiva/standby*, in cui un'istanza agisce da failover.

Se non si ha già un'app Web, usare la procedura seguente per configurare un'app Web di esempio.

1. Accedere al portale di Azure all'indirizzo https://portal.azure.com.

1. Nell'angolo in alto a sinistra dello schermo selezionare **Crea una risorsa** > **App Web**.

1. Nella scheda **nozioni di base** della pagina **Crea app Web** immettere o selezionare le informazioni seguenti.

    | Impostazione                 | Valore                                              |
    | ---                     | ---                                                |
    | **Sottoscrizione**               | Selezionare la propria sottoscrizione. |
    | **Gruppo di risorse**       | Selezionare **Crea nuovo** e immettere *FrontDoorQS_rg1* nella casella di testo.|
    | **Nome**                   | Immettere un **Nome** univoco per l'app Web. Questo esempio usa *WebAppContoso-001*. |
    | **Pubblica** | Selezionare **Codice**. |
    | **Stack di runtime**         | Selezionare **.NET Core 2.1 (LTS)** . |
    | **Sistema operativo**          | Selezionare **Windows**. |
    | **Area**           | Selezionare **Stati Uniti centrali**. |
    | **Piano Windows** | Selezionare **Crea nuovo** e immettere *myAppServicePlanCentralUS* nella casella di testo. |
    | **SKU e dimensioni** | Selezionare **Standard S1 100 ACU totali, 1,75 GB di memoria**. |

     :::image type="content" source="../media/create-front-door-portal/create-web-app.png" alt-text="Creazione rapida di uno SKU front door Premium nella portale di Azure":::

1. Selezionare **Verifica + crea**, esaminare il riepilogo e quindi selezionare **Crea**. La distribuzione in un

Al termine della distribuzione, creare una seconda app Web. Usare le stesse impostazioni indicate sopra, eccetto le impostazioni seguenti:

| Impostazione          | valore     |
| ---              | ---  |
| **Gruppo di risorse**   | Selezionare **Crea nuovo** e immettere *FrontDoorQS_rg2*. |
| **Nome**             | Immettere un nome univoco per l'app Web, in questo esempio *WebAppContoso-002*.  |
| **Area**           | Scegliere un'area diversa, in questo esempio *Stati Uniti centro-meridionali* |
| **Piano di servizio app** > **Piano Windows**         | Selezionare **nuovo** e immettere *myAppServicePlanSouthCentralUS*, quindi fare clic su **OK**. |

### <a name="create-a-front-door-standardpremium-preview-for-your-application"></a>Creare una porta anteriore standard/Premium (anteprima) per l'applicazione

Configurare Azure front door standard/Premium (Preview) per indirizzare il traffico utente in base alla latenza più bassa tra i due server di app Web. Proteggere anche la porta anteriore con il Web Application Firewall. 

1. Accedere al [portale di Azure](https://portal.azure.com).
 
1. Dal menu home page o Azure selezionare **+ Crea una risorsa**. Cercare *front door standard/Premium (Preview)*. Quindi selezionare **Crea**

1. Nella pagina **Confronta offerte** selezionare **creazione personalizzata**. Quindi selezionare **continua per creare una porta anteriore**.

1. Nella scheda informazioni di **base**   immettere o selezionare le informazioni seguenti e quindi selezionare **Next: Secret (segreto**). 

    | Impostazione | Valore |
    | --- | --- |
    | **Sottoscrizione** | Selezionare la propria sottoscrizione. |
    | **Gruppo di risorse** | Selezionare **Crea nuovo** e immettere *FrontDoorQS_rg0* nella casella di testo. |
    | **Località del gruppo di risorse** | Selezionare **Stati Uniti orientali**. |
    | **Nome profilo** | Immettere un nome univoco in questa sottoscrizione **webapp-contoso-AFD** |
    | **Livello** | Selezionare **Premium**. |
    
    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-2.png" alt-text="Crea profilo sportello anteriore":::

1. *Facoltativo*: **segreti**. Se si prevede di usare i certificati gestiti, questo passaggio è facoltativo. Se si dispone di un Key Vault esistente in Azure che si prevede di usare per portare il proprio certificato per il dominio personalizzato, selezionare **Aggiungi un certificato**. È anche possibile aggiungere il certificato nell'esperienza di gestione dopo la creazione.

    > [!NOTE]
    > È necessario disporre dell'autorizzazione giusta per aggiungere il certificato da Azure Key Vault come utente. 

    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-secret.png" alt-text="Screenshot dell'aggiunta di un segreto in creazione personalizzata.":::

1. Nella scheda **endpoint** selezionare **Aggiungi un endpoint** e assegnare all'endpoint un nome univoco globale. Al termine dell'esperienza di creazione, è possibile creare più endpoint nel profilo standard o Premium di Azure front door. Questo esempio usa *contoso-frontend*. Lascia timeout risposta origine (in secondi) e stato come predefinito. Selezionare **Aggiungi** per aggiungere l'endpoint.
    
    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-endpoint.png" alt-text="Screenshot dell'aggiunta di un endpoint.":::

1. Successivamente, aggiungere un gruppo di origine che contenga le due app Web. Selezionare **+ Aggiungi**   per aprire **la pagina Aggiungi un gruppo di origine** . Per nome immettere *myOrignGroup*, quindi selezionare **+ Aggiungi un'origine**.
 
     :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-origin-group.png" alt-text="Screenshot dell'aggiunta di un gruppo di origine.":::

1. Nella pagina **Aggiungi un'origine**   immettere o selezionare le informazioni riportate di seguito. Quindi selezionare **Aggiungi**.

    | Impostazione | Valore |
    | --- | --- |
    | **Nome** | Immettere **app Web 1** |
    | **Tipo di origine** | Selezionare i **Servizi app** |
    | **Nome host** | Selezionare `WebAppContoso-001.azurewebsites.net` |
    | **Intestazione host dell'origine** | Selezionare `WebAppContoso-001.azurewebsites.net` |
    | **Altri campi** | Lasciare tutti gli altri campi come predefiniti. |

    > [!NOTE]
    > Quando si crea un profilo standard o Premium di Azure front door, è necessario selezionare un'origine dalla stessa sottoscrizione in cui viene creato lo standard o Premium di Azure front door.
   
   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-origin-1.png" alt-text="Screenshot dell'aggiunta di altre origini.":::

1. Ripetere il passaggio 8 per aggiungere la seconda origine webapp002. Selezionare `webappcontoso-002.azurewebsite.net` come **nome host di origine** e **intestazione host di origine**.

1. Nella pagina **Aggiungi un gruppo di origine** verranno visualizzate due origini, lasciando tutti gli altri campi predefiniti.
  
   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-origin-group-2.png" alt-text="Screenshot della pagina Aggiungi un gruppo di origine.":::

1. Aggiungere quindi una route per eseguire il mapping dell'endpoint front-end al gruppo di origine. Questa route inoltra le richieste dall'endpoint a myOriginGroup. Selezionare **+ Aggiungi** nella route per configurare una route.  

1. Nella pagina **Aggiungi route** immettere o selezionare le informazioni riportate di seguito. Quindi selezionare **Aggiungi**.
  
    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-route-without-caching.png" alt-text="Aggiungi route senza Caching":::

    | Impostazione | Valore |
    | --- | --- |
    | **Nome** | Immettere la **Route** |    
    | **Dominio** | Selezionare `contoso-frontend.z01.azurefd.net` | 
    | **Nome host** | Selezionare `WebAppContoso-001.azurewebsites.net` |
    | **Criteri di corrispondenza** | Lasciare il valore predefinito. |
    | **Protocolli accettati** | Lasciare il valore predefinito. | 
    | **reindirizzamento** | Lasciare l'impostazione predefinita per **reindirizzare tutto il traffico per l'uso di HTTPS**. | 
    | **Gruppo di origine** | Selezionare **MyOriginGroup**. | 
    | **Percorso dell'origine** | Lasciare il valore predefinito. | 
    | **Protocollo di inoltro** | Selezionare **corrisponde alla richiesta in ingresso**. | 
    | **Memorizzazione nella cache** | Lasciare deselezionata in questa Guida introduttiva. Per fare in modo che il contenuto venga memorizzato nella cache sui bordi, selezionare la casella di controllo **Abilita Caching**. |
    | **Regole** | Lasciare il valore predefinito. Dopo aver creato il profilo della porta anteriore, è possibile creare regole personalizzate e applicarle alle route. |  
       
    >[!WARNING]
    > **Verificare** che sia presente una route per ogni endpoint. Un'assenza di una route può causare l'esito negativo di un endpoint.

1. Selezionare quindi **+ Aggiungi** in sicurezza per aggiungere un criterio WAF. Selezionare **Aggiungi** nuovo e assegnare un nome univoco ai criteri. Selezionare la casella di controllo **Aggiungi protezione bot**. Selezionare l'endpoint in **domini**, quindi selezionare **Aggiungi**.

   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-waf-policy-2.png" alt-text="Aggiungi criteri WAF":::

1. Selezionare **Verifica + crea** e quindi **Crea**. Sono necessari alcuni minuti per la propagazione delle configurazioni a tutti i pop perimetrali. A questo punto si dispone del primo profilo e dell'endpoint della porta anteriore.

   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-review.png" alt-text="Verifica creazione personalizzata":::

## <a name="verify-azure-front-door"></a>Verificare lo sportello anteriore di Azure

Quando si crea il profilo standard/Premium di Azure front door, sono necessari alcuni minuti prima che la configurazione venga distribuita a livello globale. Al termine, è possibile accedere all'host front-end creato. In un browser passare all'indirizzo `contoso-frontend.z01.azurefd.net`. La richiesta verrà automaticamente indirizzata al server più vicino dai server specificati nel gruppo di origine.

Se queste app sono state create in questo argomento di avvio rapido, verrà visualizzata una pagina di informazioni.

Per testare il failover globale immediato, verranno usati i passaggi seguenti:

1. Aprire un browser, come descritto sopra, e passare all'indirizzo del front-end `contoso-frontend.azurefd.net`.

1. Nel portale di Azure cercare e selezionare *Servizi app*. Scorrere verso il basso per trovare una delle app Web, **WebAppContoso-001** , in questo esempio.

1. Selezionare l'app Web, quindi selezionare **Arresta** e **Sì** per verifica.

1. Aggiornare il browser. Si dovrebbe visualizzare la stessa pagina di informazioni.

   >[!TIP]
   >Per queste azioni si verifica un leggero ritardo. Può essere necessario eseguire di nuovo l'aggiornamento.

1. Trovare l'altra app Web e arrestarla.

1. Aggiornare il browser. Questa volta dovrebbe essere visualizzato un messaggio di errore.

    :::image type="content" source="../media/create-front-door-portal/web-app-stopped-message.png" alt-text="Entrambe le istanze dell'app Web sono state arrestate":::

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine, è possibile rimuovere tutti gli elementi creati. Con l'eliminazione di un gruppo di risorse viene eliminato anche il relativo contenuto. Se non si intende usare questa frontdoor, è consigliabile rimuovere le risorse pe evitare addebiti non necessari.

1. Nel portale di Azure cercare e selezionare **Gruppi di risorse** oppure scegliere **Gruppi di risorse** dal menu del portale di Azure.

1. Filtrare o scorrere verso il basso per trovare un gruppo di risorse, ad esempio **FrontDoorQS_rg0**.

1. Selezionare il gruppo di risorse e quindi **Elimina gruppo di risorse**.

   >[!WARNING]
   >Questa azione è irreversibile.

1. Digitare il nome del gruppo di risorse per verifica, quindi selezionare **Elimina**.

Ripetere la procedura per gli altri due gruppi.

## <a name="next-steps"></a>Passaggi successivi

Passare all'articolo successivo per informazioni su come aggiungere un dominio personalizzato alla frontdoor.
> [!div class="nextstepaction"]
> [Aggiungere un dominio personalizzato](how-to-add-custom-domain.md)