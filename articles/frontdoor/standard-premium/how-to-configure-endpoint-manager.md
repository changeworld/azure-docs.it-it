---
title: Configurare l'endpoint Azure front door standard/Premium con Gestione endpoint
description: Questo articolo illustra come configurare un endpoint con Gestione endpoint.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: 241f4a61e8d8c8de7a5573e8de534cb927a71b30
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101099667"
---
# <a name="configure-an-azure-front-door-standardpremium-preview-endpoint-with-endpoint-manager"></a>Configurare un endpoint di Azure front door standard/Premium (Preview) con Gestione endpoint

> [!NOTE]
> Questa documentazione è destinata a Azure front door standard/Premium (Preview). Stai cercando informazioni su Azure front door? Visualizzare i **[documenti di Azure front door](../front-door-overview.md)**.

Questo articolo illustra come creare un endpoint per un profilo standard o Premium di Azure front door esistente con Gestione endpoint.

> [!IMPORTANT]
> Azure front door standard/Premium (anteprima) è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Prima di poter creare un endpoint di Azure front door standard/Premium con Gestione endpoint, è necessario aver creato almeno un profilo di porta anteriore di Azure creato. Il profilo deve avere almeno uno o più endpoint standard/Premium di Azure front door. Per organizzare gli endpoint standard/Premium di Azure front door per dominio Internet, applicazione Web o altri criteri, è possibile usare più profili. 

Per creare un profilo di sportello anteriore di Azure, vedere [creare un nuovo profilo standard/Premium di Azure front door](create-front-door-portal.md).

## <a name="create-a-new-azure-front-door-standardpremium-endpoint"></a>Creare un nuovo endpoint standard/Premium di Azure front door

1. Accedere al [portale di Azure](https://portal.azure.com) e passare al profilo standard/Premium di Azure front door.

1. Selezionare **Gestione endpoint**. Quindi selezionare **Aggiungi un endpoint** per creare un nuovo endpoint.
   
    :::image type="content" source="../media/how-to-configure-endpoint-manager/select-create-endpoint.png" alt-text="Screenshot di aggiungere un endpoint tramite Gestione endpoint.":::

1. Nella pagina **Aggiungi endpoint** immettere e selezionare le impostazioni seguenti.
    
    :::image type="content" source="../media/how-to-configure-endpoint-manager/create-endpoint-page.png" alt-text="Screenshot della pagina Aggiungi endpoint.":::

    | Impostazioni | valore |
    | -------- | ----- |
    | Nome | Immettere un nome univoco per il nuovo endpoint standard/Premium di Azure front door. Questo nome viene usato per accedere alle risorse memorizzate nella cache nel dominio `<endpointname>.az01.azurefd.net` |
    | Timeout risposta origine (sec) | Immettere un valore di timeout in secondi di attesa prima che la connessione con l'origine abbia il timeout. |
    | Stato | Selezionare la casella di controllo per abilitare l'endpoint. |

## <a name="add-domains-origin-group-routes-and-security"></a>Aggiungere domini, gruppo di origine, route e sicurezza

1. Selezionare **Modifica endpoint** nell'endpoint per configurare la route.

1. Nella pagina **Modifica endpoint** selezionare **+ Aggiungi** in domini.
   
    :::image type="content" source="../media/how-to-configure-endpoint-manager/select-add-domain.png" alt-text="Screenshot della pagina selezionare un dominio nella pagina di modifica dell'endpoint.":::

### <a name="add-domain"></a>Aggiungi dominio

1. Nella pagina **Aggiungi dominio** scegliere di associare un dominio *dal profilo di sportello anteriore di Azure* o di *aggiungere un nuovo dominio*. Per informazioni su come creare un nuovo dominio, vedere [creare un nuovo dominio personalizzato di Azure front door standard/Premium](how-to-add-custom-domain.md).

    :::image type="content" source="../media/how-to-configure-endpoint-manager/add-domain-page.png" alt-text="Screenshot della pagina Aggiungi un dominio.":::

1. Selezionare **Aggiungi** per aggiungere il dominio all'endpoint corrente. Il dominio selezionato dovrebbe essere visualizzato all'interno del pannello del dominio.

    :::image type="content" source="../media/how-to-configure-endpoint-manager/domain-in-domainview.png" alt-text="Screenshot dei domini nella visualizzazione dominio.":::

### <a name="add-origin-group"></a>Aggiungi gruppo di origine

1. Selezionare **Aggiungi** nella visualizzazione gruppi di origine. Viene visualizzata la pagina **Aggiungi un gruppo di origine** 

    :::image type="content" source="../media/how-to-configure-endpoint-manager/add-origin-group-view.png" alt-text="Screenshot della pagina Aggiungi un gruppo di origine":::

1. Per **nome** immettere un nome univoco per il nuovo gruppo di origine

1. Selezionare **Aggiungi un'origine** per aggiungere una nuova origine al gruppo corrente.
 
#### <a name="health-probes"></a>Probe di integrità
Il portello anteriore invia richieste di probe HTTP/HTTPS periodiche a ognuna delle origini. Le richieste di probe determinano la vicinanza e l'integrità di ogni origine per il bilanciamento del carico delle richieste degli utenti finali. Le impostazioni del probe di integrità per un gruppo di origine definiscono come viene eseguito il polling dello stato di integrità dell'origine dell'app Per la configurazione del bilanciamento del carico sono disponibili le impostazioni seguenti:

> [!WARNING]
> Poiché la porta anteriore ha molti ambienti perimetrali a livello globale, il volume del probe di integrità per l'origine può essere molto elevato da 25 richieste ogni minuto fino a 1200 richieste al minuto, a seconda della frequenza di probe di integrità configurata. Con la frequenza Probe predefinita di 30 secondi, il volume Probe sull'origine deve essere di circa 200 richieste al minuto.

* **Stato**: specificare se attivare il probe di integrità. Se si dispone di una singola origine nel gruppo di origine, è possibile scegliere di disabilitare i probe di integrità riducendo il carico sul back-end dell'applicazione. Anche se si hanno più origini nel gruppo, ma solo una di esse è nello stato abilitato, è possibile disabilitare i probe di integrità.
   
* **Path**: URL usato per le richieste di probe per tutte le origini in questo gruppo di origine. Ad esempio, se una delle origini è contoso-westus.azurewebsites.net e il percorso è impostato su/probe/test.aspx, quindi gli ambienti front-end, supponendo che il protocollo sia impostato su HTTP, invierà richieste di probe di integrità a `http://contoso-westus.azurewebsites.net/probe/test.aspx` . 
   
* **Protocollo**: definisce se inviare le richieste di probe di integrità dalla porta anteriore all'origine con il protocollo http o HTTPS.
   
* **Metodo Probe**: il metodo HTTP da usare per l'invio di probe di integrità. Le opzioni includono GET o HEAD (impostazione predefinita).

    > [!NOTE]
    > Per un carico inferiore e un costo sull'origine, la porta anteriore consiglia di usare le richieste HEAD per i probe di integrità.

* **Intervallo (in secondi)**: definisce la frequenza di probe di integrità per l'origine o gli intervalli in cui ognuno degli ambienti della porta anteriore Invia un probe.
   
    >[!NOTE]
    >Per un failover più veloce, impostare l'intervallo su un valore inferiore. Più basso è il valore, maggiore è il volume del probe di integrità ricevuto dall'origine. Se, ad esempio, l'intervallo è impostato su 30 secondi, ad esempio, 100 la porta anteriore viene visualizzata a livello globale, ogni back-end riceverà circa 200 richieste di probe al minuto.

#### <a name="load-balancing"></a>Bilanciamento del carico
Le impostazioni di bilanciamento del carico per il gruppo Origin definiscono il modo in cui vengono valutati i probe di integrità. Queste impostazioni determinano se il back-end è integro o non integro. Viene inoltre controllato come bilanciare il carico del traffico tra origini diverse nel gruppo di origine. Per la configurazione del bilanciamento del carico sono disponibili le impostazioni seguenti:

- **Dimensioni campione**. Identifica il numero di campioni di probe di integrità che è necessario considerare per la valutazione dell'integrità di origine.

- **Dimensioni del campione riuscite**. Definisce le dimensioni del campione come indicato in precedenza, il numero di campioni riusciti necessari per chiamare l'origine integro. Si supponga ad esempio che un intervallo di probe di integrità della porta anteriore sia 30 secondi, che le dimensioni del campione siano pari a 5 e che le dimensioni del campione siano 3. Ogni volta che si valutano i probe di integrità per l'origine, si esaminano gli ultimi cinque campioni oltre 150 secondi (5 x 30). Per dichiarare il back-end come integro sono necessari almeno tre Probe con esito positivo.

- **Sensibilità alla latenza (latenza aggiuntiva)**. Definisce se si vuole che la porta anteriore invii la richiesta all'origine entro l'intervallo di sensibilità della misurazione della latenza o inoltra la richiesta al back-end più vicino.

Selezionare **Aggiungi** per aggiungere il gruppo di origine all'endpoint corrente. Il gruppo di origine dovrebbe essere visualizzato all'interno del pannello gruppo di origine

:::image type="content" source="../media/how-to-configure-endpoint-manager/origin-in-origin-group.png" alt-text="Screenshot delle origini nel gruppo di origine.":::

### <a name="add-route"></a>Aggiungi route

Selezionare **Aggiungi** nella visualizzazione route. verrà visualizzata la pagina **Aggiungi route** . Per informazioni su come associare il dominio e il gruppo di origine, vedere [creare una nuova route di Azure front door](how-to-configure-route.md)

### <a name="add-security"></a>Aggiungi sicurezza

1. Selezionare **Aggiungi** nella visualizzazione sicurezza. verrà visualizzata la pagina **Aggiungi criteri WAF**
 
    :::image type="content" source="../media/how-to-configure-endpoint-manager/add-waf-policy-page.png" alt-text="Screenshot della pagina Aggiungi criteri WAF.":::

1. **Criteri WAF**: selezionare un criterio WAF da applicare per il dominio selezionato all'interno dell'endpoint. 
  
   Selezionare **Crea nuovo** per creare un nuovo criterio WAF.

    :::image type="content" source="../media/how-to-configure-endpoint-manager/create-new-waf-policy.png" alt-text="Screenshot della creazione di un nuovo criterio WAF.":::
   
    **Nome**: immettere un nome univoco per il nuovo criterio WAF. È possibile modificare questo criterio con una maggiore configurazione dalla pagina Web Application Firewall.

    **Domini**: selezionare il dominio a cui applicare il criterio WAF.

1. Selezionare **Aggiungi** pulsante. Il criterio WAF verrà visualizzato nel pannello sicurezza

    :::image type="content" source="../media/how-to-configure-endpoint-manager/waf-in-security-view.png" alt-text="Screenshot del criterio WAF nella visualizzazione sicurezza.":::

## <a name="clean-up-resources"></a>Pulire le risorse

Per eliminare un endpoint quando non è più necessario, selezionare **Elimina endpoint** alla fine della riga dell'endpoint. 

:::image type="content" source="../media/how-to-configure-endpoint-manager/delete-endpoint.png" alt-text="Screenshot dell'eliminazione di un endpoint.":::

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sui domini personalizzati, continuare ad [aggiungere un dominio personalizzato](how-to-add-custom-domain.md).
