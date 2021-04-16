---
title: Distribuire i servizi API Management Azure in più aree di Azure
titleSuffix: Azure API Management
description: Informazioni su come distribuire un'istanza del servizio Gestione API di Azure in più aree di Azure.
author: mikebudzynski
ms.service: api-management
ms.topic: how-to
ms.date: 04/13/2021
ms.author: apimpm
ms.openlocfilehash: 9546813173e72b1f264c3668ee889bbeea07ce7f
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534083"
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Come distribuire un'istanza del servizio Gestione API di Azure in più aree di Azure

Azure API Management supporta la distribuzione in più aree, che consente agli editori di API di distribuire un singolo servizio Gestione API di Azure in un numero qualsiasi di aree di Azure supportate. La funzionalità in più aree consente di ridurre la latenza delle richieste percepita dai consumer di API distribuite geograficamente e migliora la disponibilità del servizio se un'area passa offline.

Un nuovo servizio azure API Management inizialmente contiene una sola [unità][unit] in una singola area di Azure, l'area primaria. È possibile aggiungere unità aggiuntive alle aree primaria o secondaria. Un API Management gateway virtuale viene distribuito in ogni area primaria e secondaria selezionata. Le richieste API in ingresso vengono indirizzate automaticamente all'area più vicina. Se un'area passa offline, le richieste API verranno indirizzate automaticamente all'area non riuscita al gateway più vicino successivo.

> [!NOTE]
> Solo il componente gateway di API Management viene distribuito in tutte le aree. Il componente di gestione del servizio e il portale per sviluppatori sono ospitati solo nell'area primaria. Pertanto, in caso di interruzione dell'area primaria, l'accesso al portale per sviluppatori e la possibilità di modificare la configurazione (ad esempio, l'aggiunta di API, l'applicazione di criteri) saranno compromessi fino a quando l'area primaria non torna online. Mentre l'area primaria è offline, le aree secondarie disponibili continueranno a gestire il traffico API usando la configurazione più recente disponibile. Facoltativamente, [abilitare la riondanza della zona](zone-redundancy.md) per migliorare la disponibilità e la resilienza delle aree primarie o secondarie.

>[!IMPORTANT]
> La funzionalità per abilitare l'archiviazione dei dati dei clienti in una singola area è attualmente disponibile solo nell'area Asia sud-orientale (Singapore) del Asia Pacifico geografica. Per tutte le altre aree i dati dei clienti vengono archiviati in Geo.

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]


## <a name="prerequisites"></a>Prerequisiti

* Se non è ancora stata creata un'istanza del servizio Gestione API, vedere [Creare un'istanza di Gestione API](get-started-create-service-instance.md). Selezionare il livello di servizio Premium.
* Se l'istanza di API Management viene distribuita in una rete [virtuale,](api-management-using-with-vnet.md)assicurarsi di configurare una rete virtuale, una subnet e un indirizzo IP pubblico nella posizione che si intende aggiungere.

## <a name="deploy-api-management-service-to-an-additional-location"></a><a name="add-region"> </a>Distribuire API Management servizio in un percorso aggiuntivo

1. Nel portale di Azure passare al servizio API Management e selezionare **Località** nel menu.
1. Selezionare **+ Aggiungi** nella barra superiore.
1. Selezionare la posizione dall'elenco a discesa.
1. Selezionare il numero di unità **[di scala](upgrade-and-scale.md)** nella posizione.
1. Facoltativamente, abilitare [**zone di disponibilità**](zone-redundancy.md).
1. Se l'API Management è distribuita in una [rete virtuale,](api-management-using-with-vnet.md)configurare le impostazioni della rete virtuale nel percorso. Selezionare una rete virtuale, una subnet e un indirizzo IP pubblico esistenti disponibili nel percorso.
1. Selezionare **Aggiungi per** confermare.
1. Ripetere questo processo fino a configurare tutte le posizioni.
1. Selezionare **Salva** nella barra superiore per avviare il processo di distribuzione.

## <a name="delete-an-api-management-service-location"></a><a name="remove-region"> </a>Eliminare un percorso API Management servizio

1. Nel portale di Azure passare al servizio API Management e fare clic sulla **voce Località** nel menu.
2. Per la località che si vuole rimuovere aprire il menu di scelta rapida usando il pulsante **...** sul lato destro della tabella. Selezionare l'opzione **Elimina**.
3. Confermare l'eliminazione e fare clic su **Salva** per applicare le modifiche.

## <a name="route-api-calls-to-regional-backend-services"></a><a name="route-backend"> </a>Chiamate di route API per servizi back-end a livello di area

Gestione API di Azure include un solo URL del servizio back-end. Anche se sono presenti istanze di gestione API di Azure in diverse aree, il gateway API inoltrerà comunque le richieste al servizio back-end stesso, che viene distribuito in una sola area. In questo caso, il miglioramento delle prestazioni proverrà solo dalle risposte memorizzate nella cache in Gestione API di Azure in un'area specifica per la richiesta, ma il tentativo di contattare il back-end in tutto il mondo potrebbe comunque causare una latenza elevata.

Per sfruttare completamente la distribuzione geografica del sistema, è necessario disporre di servizi di back-end distribuiti nelle stesse aree come istanze di gestione API di Azure. Quindi, usando i criteri e la proprietà `@(context.Deployment.Region)`, è possibile instradare il traffico alle istanze locali di back-end.

1. Passare all'istanza di gestione API di Azure e fare clic su **API** nel menu a sinistra.
2. Selezionare l'API desiderata.
3. Fare clic su **editor di codice** dall'elenco a discesa sulla freccia nel **l'elaborazione in ingresso**.

    ![Editor di codice API](./media/api-management-howto-deploy-multi-region/api-management-api-code-editor.png)

4. Usare i criteri `set-backend` combinati con condizionale`choose` per creare un criterio di routing appropriato nella `<inbound> </inbound>` sezione del file.

    Ad esempio, il file XML seguente funzionerà per le aree Stati Uniti occidentali e Asia orientale:

    ```xml
    <policies>
        <inbound>
            <base />
            <choose>
                <when condition="@("West US".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-us.com/" />
                </when>
                <when condition="@("East Asia".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-asia.com/" />
                </when>
                <otherwise>
                    <set-backend-service base-url="http://contoso-other.com/" />
                </otherwise>
            </choose>
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>
    ```

> [!TIP]
> È anche possibile eseguire il front-end dei servizi [back-end](https://azure.microsoft.com/services/traffic-manager/)con Gestione traffico di Azure, indirizzare le chiamate API a Gestione traffico e consentire la risoluzione automatica del routing.

## <a name="use-custom-routing-to-api-management-regional-gateways"></a><a name="custom-routing"> </a>Usare il routing personalizzato per API Management gateway regionali

API Management instrada le richieste a un _gateway a livello di regione_ in base alla [latenza più bassa.](../traffic-manager/traffic-manager-routing-methods.md#performance) Anche se non è possibile eseguire l'override di questa impostazione in API Management, è possibile usare Gestione traffico con regole di routing personalizzate.

1. Creare il proprio [Gestione traffico di Azure](https://azure.microsoft.com/services/traffic-manager/).
1. Se si usa un dominio personalizzato, [usarlo con Gestione traffico](../traffic-manager/traffic-manager-point-internet-domain.md) anziché con il API Management servizio.
1. [Configurare gli API Management endpoint regionali in Gestione traffico](../traffic-manager/traffic-manager-manage-endpoints.md). Gli endpoint di zona seguono il modello di URL `https://<service-name>-<region>-01.regional.azure-api.net` di , ad esempio `https://contoso-westus2-01.regional.azure-api.net` .
1. [Configurare gli API Management endpoint di stato regionali in Gestione traffico.](../traffic-manager/traffic-manager-monitoring.md) Gli endpoint di stato a livello di regione seguono il modello di URL `https://<service-name>-<region>-01.regional.azure-api.net/status-0123456789abcdef` di , ad esempio `https://contoso-westus2-01.regional.azure-api.net/status-0123456789abcdef` .
1. Specificare [il metodo di routing](../traffic-manager/traffic-manager-routing-methods.md) di Gestione traffico.

[create an api management service instance]: get-started-create-service-instance.md
[get started with azure api management]: get-started-create-service-instance.md
[deploy an api management service instance to a new region]: #add-region
[delete an api management service instance from a region]: #remove-region
[unit]: https://azure.microsoft.com/pricing/details/api-management/
[premium]: https://azure.microsoft.com/pricing/details/api-management/
