---
title: Creare una porta anteriore con Reindirizzamento da HTTP a HTTPS usando il portale di Azure
description: Informazioni su come creare una porta anteriore con traffico reindirizzato da HTTP a HTTPS usando il portale di Azure.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: edc1ebe53969ceac0452818bee016e35de1e9322
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106167839"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>Creare una porta anteriore con Reindirizzamento da HTTP a HTTPS usando il portale di Azure

È possibile usare la portale di Azure per [creare una porta anteriore](quickstart-create-front-door.md) con un certificato per la terminazione TLS. Viene usata una regola di routing per reindirizzare il traffico HTTP a HTTPS.

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>Creare una porta anteriore con una risorsa dell'app Web esistente

1. Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

1. Selezionare **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure.

1. Cercare **sportello anteriore** usando la barra di ricerca e, una volta individuato il tipo di risorsa, selezionare **Crea**.

1. Scegliere una *sottoscrizione* e quindi usare un gruppo di risorse esistente o crearne uno nuovo. Selezionare **Avanti** per immettere la scheda configurazione.

    > [!NOTE]
    > Il percorso richiesto nell'interfaccia utente è solo per il gruppo di risorse. La configurazione della porta anteriore viene distribuita in tutte le [località pop di Azure front door](front-door-faq.yml#what-are-the-pop-locations-for-azure-front-door-).

    :::image type="content" source="./media/front-door-url-redirect/front-door-create-basics.png" alt-text="Configurare le nozioni di base per la nuova porta anteriore":::

1. La configurazione per la porta anteriore si verifica in tre passaggi: aggiungere un host front-end predefinito, aggiungere back-end in un pool back-end e quindi creare regole di routing per eseguire il mapping del comportamento di routing per l'host front-end. Selezionare l' **+** icona "" sugli host front- _end_ per creare un host front-end.

    :::image type="content" source="./media/front-door-url-redirect/front-door-designer.png" alt-text="Finestra di progettazione della configurazione della porta anteriore":::

1. Immettere un nome univoco globale per l'host front-end predefinito per la porta anteriore. Selezionare **Aggiungi** per continuare con il passaggio successivo.

    :::image type="content" source="./media/front-door-url-redirect/front-door-create-frontend-host.png" alt-text="Aggiungere un host front-end":::

### <a name="create-backend-pool"></a>Crea pool back-end

1. Selezionare l' **+** icona '' nei _pool back-end_ per creare un pool back-end. Specificare un nome per il pool back-end e quindi selezionare **Aggiungi un back-end**.

    :::image type="content" source="./media/front-door-url-redirect/front-door-designer-backend-pool.png" alt-text="Pool back-end della finestra di progettazione configurazione porta anteriore":::

1. Selezionare il tipo di host back-end come _servizio app_. Selezionare la sottoscrizione in cui è ospitata l'app Web e quindi selezionare l'app Web specifica nell'elenco a discesa per il **nome host back-end**.

    :::image type="content" source="./media/front-door-url-redirect/front-door-create-backend-pool.png" alt-text="Aggiungere un back-end in un pool back-end":::

1. Selezionare **Aggiungi** per salvare il back-end e selezionare di nuovo **Aggiungi** per salvare la configurazione del pool back-end. 

## <a name="create-http-to-https-redirect-rule"></a>Crea regola di Reindirizzamento da HTTP a HTTPS

1. Selezionare l' **+** icona '' sulle *regole di routing* per creare una route. Specificare un nome per la route, ad esempio ' HttpToHttpsRedirect ', quindi impostare il campo *protocollo accettato* su **' http only '**. Verificare che sia selezionata l'opzione front *-end/domini* appropriati.  

    :::image type="content" source="./media/front-door-url-redirect/front-door-designer-routing-rule.png" alt-text="Regola di routing della finestra di progettazione della configurazione della porta anteriore":::

1. Nella sezione *Dettagli Route* impostare il tipo di *Route* su **Reindirizzamento**. Verificare che il *tipo di reindirizzamento* Get sia impostato su **trovato (302)** e che il *protocollo di reindirizzamento* venga impostato **solo su HTTPS**. 

    :::image type="content" source="./media/front-door-url-redirect/front-door-redirect-config-example.png" alt-text="Aggiungere una route di Reindirizzamento da HTTP a HTTPS":::

1. Selezionare **Aggiungi** per salvare la regola di routing per il Reindirizzamento da http a HTTPS.

## <a name="create-forwarding-rule"></a>Crea regola di invio

1. Aggiungere un'altra regola di routing per gestire il traffico HTTPS. Selezionare il **+** segno "" per le *regole di routing* e specificare un nome per la route, ad esempio "DefaultForwardingRoute". Impostare quindi il campo *protocolli accettati* **solo su HTTPS**. Verificare che sia selezionata l'opzione front *-end/domini* appropriati.

1. Nella sezione dettagli Route impostare il tipo di *Route* su **Avanti**. Assicurarsi che venga selezionato il pool back-end corretto e che il *protocollo di trasmissione* sia impostato **solo su HTTPS**. 

    :::image type="content" source="./media/front-door-url-redirect/front-door-forward-route-example.png" alt-text="Aggiungere una route di inoltro per il traffico HTTPS" border="false":::

1. Selezionare **Aggiungi** per salvare la regola di routing per l'inoltro della richiesta.

1. Selezionare **Verifica + crea** e quindi **Crea** per creare il profilo di porta anteriore. Passare alla risorsa dopo la creazione.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [funzionamento di Frontdoor](front-door-routing-architecture.md).
- Altre informazioni sul [Reindirizzamento degli URL sull'anta anteriore](front-door-url-redirect.md).
