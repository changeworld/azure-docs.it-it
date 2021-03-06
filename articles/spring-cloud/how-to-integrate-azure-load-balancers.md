---
title: 'Esercitazione: integrare Azure Spring cloud con le soluzioni di bilanciamento del carico di Azure'
description: Come integrare Azure Spring cloud con le soluzioni di bilanciamento del carico di Azure
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 04/20/2020
ms.custom: devx-track-java
ms.openlocfilehash: 50b09fd82461221ae6cd008f6918ac2f3a26fd94
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104877964"
---
# <a name="integrate-azure-spring-cloud-with-azure-load-balance-solutions"></a>Integrare Azure Spring Cloud con le soluzioni di bilanciamento del carico di Azure

**Questo articolo si applica a:** ✔️ Java ✔️ C#

Il cloud Spring di Azure supporta i microservizi in Azure.  L'aumento dell'attività può richiedere più data center con la gestione di più istanze di Azure Spring cloud.

Azure fornisce già diverse soluzioni di bilanciamento del carico. Sono disponibili tre opzioni per integrare Azure Spring cloud con le soluzioni di bilanciamento del carico di Azure:

1.  Integrare Azure Spring cloud con gestione traffico di Azure
2.  Integrare Azure Spring cloud con app Azure gateway
3.  Integrare Azure Spring cloud con lo sportello anteriore di Azure

## <a name="prerequisites"></a>Prerequisiti

* Cloud Spring di Azure: [come creare un servizio cloud Spring di Azure](./spring-cloud-quickstart.md)
* Gestione traffico di Azure: [come creare un gestore traffico](../traffic-manager/quickstart-create-traffic-manager-profile.md)
* Gateway app Azure: [come creare un gateway applicazione](../application-gateway/quick-create-portal.md)
* Sportello anteriore di Azure: [come creare una porta anteriore](../frontdoor/quickstart-create-front-door.md)

## <a name="integrate-azure-spring-cloud-with-azure-traffic-manager"></a>Integrare Azure Spring cloud con gestione traffico di Azure

Per integrare Azure Spring cloud con gestione traffico, aggiungere gli endpoint pubblici come endpoint di Traffic Manager e quindi configurare un dominio personalizzato per Traffic Manager e Azure Spring cloud.

### <a name="add-endpoint-in-traffic-manager"></a>Aggiungere un endpoint in gestione traffico
Aggiungere endpoint in gestione traffico:
1.  Specificare il **tipo** come *endpoint esterno*.
1.  Nome di dominio completo (FQDN) di input di ogni endpoint pubblico di Azure Spring cloud.
1. Fare clic su **OK**.

    ![Gestione traffico 1 ](media/spring-cloud-load-balancers/traffic-manager-1.png) ![ Gestione traffico 2](media/spring-cloud-load-balancers/traffic-manager-2.png)

### <a name="configure-custom-domain"></a>Configurare un dominio personalizzato
Per completare la configurazione:
1.  Accedere al sito Web del provider di dominio e creare un mapping di record CNAME dal dominio personalizzato al nome di dominio predefinito di Azure di gestione traffico.
1.  Seguire le istruzioni [come aggiungere un dominio personalizzato al cloud Spring di Azure](spring-cloud-tutorial-custom-domain.md).
1. Aggiungere sopra il binding del dominio personalizzato a gestione traffico al servizio app corrispondente di Azure Spring cloud e caricare il certificato SSL.

    ![Gestione traffico 3](media/spring-cloud-load-balancers/traffic-manager-3.png)

## <a name="integrate-azure-spring-cloud-with-azure-app-gateway"></a>Integrare Azure Spring cloud con app Azure gateway

Per l'integrazione con il servizio cloud Spring di Azure, completare le configurazioni seguenti:

### <a name="configure-backend-pool"></a>Configurare il pool back-end
1. Specificare il **tipo di destinazione** come *indirizzo IP* o *FQDN*.
1. Immettere gli endpoint pubblici di Azure Spring cloud.

    ![Gateway app 1](media/spring-cloud-load-balancers/app-gateway-1.png)

### <a name="add-custom-probe"></a>Aggiungi Probe personalizzato
1. Selezionare **Probe integrità** e quindi **Aggiungi** per aprire la finestra di dialogo **Probe** personalizzato. 
1. Il punto chiave consiste nel selezionare *Sì* per **Scegli nome host dall'opzione impostazioni http back-end** .

    ![Gateway app 2](media/spring-cloud-load-balancers/app-gateway-2.png)

### <a name="configure-http-setting"></a>Configura impostazione http
1.  Selezionare **impostazioni http** quindi **Aggiungi** per aggiungere un'impostazione http.
1.  **Sostituisci con nuovo nome host:** selezionare *Sì*.
1.  **Override del nome host**: selezionare Seleziona **nome host dalla destinazione back-end**.
1.  **USA Probe personalizzato**: selezionare *Sì* e scegliere il probe personalizzato creato in precedenza.

    ![Gateway app 3](media/spring-cloud-load-balancers/app-gateway-3.png)

### <a name="configure-rewrite-set"></a>Configura set di riscrittura
1.  Selezionare **riscritture** e quindi **riscrivere set** per aggiungere un set di riscrittura.
1.  Selezionare le regole di routing che indirizzano le richieste agli endpoint pubblici di Azure Spring cloud.
1.  Nella scheda **configurazione regola di riscrittura** selezionare **Aggiungi regola di riscrittura**.
1.  **Tipo di riscrittura**: selezionare l' **intestazione della richiesta**
1.  **Tipo di azione**: selezionare **Elimina**
1.  **Nome intestazione**: selezionare l' **intestazione comune**
1.  **Intestazione comune**: selezionare **X-Inoltred-proto**

    ![Gateway app 4](media/spring-cloud-load-balancers/app-gateway-4.png)

## <a name="integrate-azure-spring-cloud-with-azure-front-door"></a>Integrare Azure Spring cloud con lo sportello anteriore di Azure

Per l'integrazione con il servizio cloud Spring di Azure e la configurazione del pool back-end, 
1. **Aggiungi pool back-end**.
1. Specificare l'endpoint back-end aggiungendo host.

    ![Sportello anteriore 1](media/spring-cloud-load-balancers/front-door-1.png)

1.  Specificare il **tipo di host back-end** come *host personalizzato*.
1.  Immettere il nome di dominio completo degli endpoint pubblici di Azure Spring cloud nel **nome host back-end**.
1.  Accettare l'impostazione predefinita dell' **intestazione host back-end** , che corrisponde al **nome host back-end**.

    ![Sportello anteriore 2](media/spring-cloud-load-balancers/front-door-2.png)

## <a name="next-steps"></a>Passaggi successivi
* [Come creare una gestione traffico](../traffic-manager/quickstart-create-traffic-manager-profile.md)
* [Come creare un gateway applicazione](../application-gateway/quick-create-portal.md)
* [Come creare una porta anteriore](../frontdoor/quickstart-create-front-door.md)