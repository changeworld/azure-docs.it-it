---
title: App Azure Spring Access Cloud in rete virtuale
description: Accedi all'app in un cloud Spring di Azure nella rete virtuale.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/11/2020
ms.custom: devx-track-java
ms.openlocfilehash: 37c8b4bc186c217ecb27638f5f50297102345de7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99576532"
---
# <a name="access-your-application-in-a-private-network"></a>Accedere all'applicazione in una rete privata

In questo documento viene illustrato come accedere a un endpoint per l'applicazione in una rete privata.  Per ottenere l'accesso, è necessario creare una zona di **DNS privato di Azure** nella sottoscrizione per tradurre/risolvere il nome di dominio completo (FQDN) privato nel relativo indirizzo IP.

Quando si distribuisce un **endpoint** per le applicazioni in un'istanza del servizio cloud di Azure Spring nella rete virtuale, l'endpoint è un FQDN privato. Il dominio è accessibile solo nella rete privata. App e servizi usano l'endpoint dell'applicazione. Includono l' **endpoint di test** descritto in [visualizzare le app e le distribuzioni](spring-cloud-howto-staging-environment.md#view-apps-and-deployments). Il flusso di **log**, descritto in [trasmettere i log delle app di Azure Spring cloud in tempo reale](spring-cloud-howto-log-streaming.md), funziona anche solo all'interno della rete privata.

## <a name="create-a-private-dns-zone"></a>Creare una zona DNS privato

La procedura seguente consente di creare una zona DNS privata per un'applicazione nella rete privata.

1. Aprire il portale di Azure. Nella casella di ricerca superiore cercare **zone DNS privato** e selezionare **DNS privato zone** dal risultato.

2. Nella pagina **zone DNS privato** selezionare **+ Aggiungi**.

3. Compilare il modulo nella pagina **Create DNS privato zone** . Immettere **<span>private.azuremicroservices.io</span>** come **nome** della zona.

4. Selezionare **Rivedi e crea**.

5. Selezionare **Create** (Crea).

La creazione della zona può richiedere alcuni minuti.

## <a name="link-the-virtual-network"></a>Collegare la rete virtuale

Per collegare la zona DNS privata alla rete virtuale, è necessario creare un collegamento di rete virtuale.

1. Selezionare la risorsa di zona DNS privata creata in precedenza: **<span>private.azuremicroservices.io</span>** 

2. Nel riquadro di sinistra selezionare **Collegamenti di rete virtuale**.

3. Selezionare **Aggiungi**.

4. Immettere **Azure-Spring-Cloud-DNS-link** per il **nome del collegamento**.

5. Per **rete virtuale** selezionare la rete virtuale creata come descritto in [distribuire il cloud di Azure Spring nella rete virtuale di Azure (VNet Injection)](spring-cloud-tutorial-deploy-in-azure-virtual-network.md).

    ![Aggiungere un collegamento di rete virtuale](media/spring-cloud-access-app-vnet/add-virtual-network-link.png)

6. Fare clic su **OK**.

## <a name="create-dns-record"></a>Creare record DNS

Per usare la zona DNS privata per tradurre/risolvere il DNS, è necessario creare un record di tipo "A" nella zona.

1. Selezionare la risorsa di rete virtuale creata come descritto in [distribuire Azure Spring cloud nella rete virtuale di Azure (VNet Injection)](spring-cloud-tutorial-deploy-in-azure-virtual-network.md).

2. Nella casella di ricerca **dispositivi connessi** immettere *kubernetes-Internal*.

3. Nel risultato filtrato trovare il **dispositivo** connesso alla **Subnet** di runtime del servizio dell'istanza del servizio e copiarne l' **indirizzo IP**. In questo esempio, l'indirizzo IP è *10.1.0.7*.

    [![Crea record ](media/spring-cloud-access-app-vnet/create-dns-record.png) DNS](media/spring-cloud-access-app-vnet/create-dns-record.png)

In alternativa, è possibile recuperare l'indirizzo IP usando il comando AZ CLI seguente:

```azurecli
SPRING_CLOUD_RG= # Resource group name of your Azure Spring Cloud service instance
SPRING_CLOUD= # Name of your Azure Spring Cloud service instance

SERVICE_RUNTIME_RG=`az spring-cloud show -g $SPRING_CLOUD_RG -n $SPRING_CLOUD --query \
"properties.networkProfile.serviceRuntimeNetworkResourceGroup" -o tsv`

IP_ADDRESS=`az network lb frontend-ip list --lb-name kubernetes-internal -g \
$SERVICE_RUNTIME_RG --query "[0].privateIpAddress" -o tsv`
```

4. Selezionare la risorsa di zona DNS privata creata in precedenza: **<span>private.azuremicroservices.io</span>**.

5. Selezionare **+ Set di record**.

6. In **Aggiungi set di record** immettere o selezionare queste informazioni:

    |Impostazione     |Valore                                                                      |
    |------------|---------------------------------------------------------------------------|
    |Nome        |Immettere *\**                                                                 |
    |Tipo        |Selezionare **un**                                                               |
    |TTL         |Immettere *1*                                                                  |
    |Unità TTL    |Seleziona **ore**                                                           |
    |Indirizzo IP  |Immettere l'indirizzo IP copiato nel passaggio 3. Nell'esempio immettere *10.1.0.7*.    |

    Selezionare **OK**.

    ![Aggiungere un record della zona DNS privata](media/spring-cloud-access-app-vnet/private-dns-zone-add-record.png)

## <a name="assign-private-fqdn-for-your-application"></a>Assegnare FQDN privato per l'applicazione

Dopo aver seguito la procedura descritta in [compilare e distribuire applicazioni di microservizi](spring-cloud-tutorial-deploy-in-azure-virtual-network.md), è possibile assegnare un nome di dominio completo privato per l'applicazione.

1. Selezionare l'istanza del servizio cloud Spring di Azure distribuita nella rete virtuale e aprire la scheda **app** nel menu a sinistra.

2. Selezionare l'applicazione per visualizzare la pagina **Panoramica** .

3. Selezionare **assegna endpoint** per assegnare un FQDN privato all'applicazione. L'operazione potrebbe richiedere alcuni minuti.

    ![Assegnare un endpoint privato](media/spring-cloud-access-app-vnet/assign-private-endpoint.png)

4. È ora disponibile l'FQDN privato assegnato ( **URL** con etichetta). È possibile accedervi solo all'interno della rete privata, ma non in Internet.

## <a name="access-application-private-fqdn"></a>Accedere al nome FQDN privato dell'applicazione

Dopo l'assegnazione, è possibile accedere al nome di dominio completo privato dell'applicazione nella rete privata. Ad esempio, è possibile creare una macchina JumpBox nella stessa rete virtuale o in una rete virtuale con peering e nel computer JumpBox l'FQDN privato è accessibile.

![Accedere a un endpoint privato in VNET](media/spring-cloud-access-app-vnet/access-private-endpoint.png)

## <a name="next-steps"></a>Passaggi successivi

- [Esporre le applicazioni a Internet-usando il gateway applicazione e il firewall di Azure](spring-cloud-expose-apps-gateway-azure-firewall.md)

## <a name="see-also"></a>Vedere anche

- [Risoluzione dei problemi di Azure Spring Cloud nella rete virtuale](spring-cloud-troubleshooting-vnet.md)
- [Responsabilità del cliente per l'esecuzione di Azure Spring Cloud nella rete virtuale](spring-cloud-vnet-customer-responsibilities.md)