---
title: Abilitare l'accesso privato con collegamento privato (anteprima)
titleSuffix: Azure Digital Twins
description: Vedere come abilitare l'accesso privato per le soluzioni di dispositivi gemelli digitali di Azure con collegamento privato
author: baanders
ms.author: baanders
ms.date: 1/25/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: a5328f52975e72298b93107792692b178dac8a97
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98948816"
---
# <a name="enable-private-access-with-private-link-preview"></a>Abilitare l'accesso privato con collegamento privato (anteprima)

Questo articolo descrive i diversi modi per [abilitare il collegamento privato con un endpoint privato per un'istanza di dispositivi gemelli digitali di Azure](concepts-security.md#private-network-access-with-azure-private-link-preview) (attualmente in anteprima). La configurazione di un endpoint privato per l'istanza di Azure Digital Twins consente di proteggere l'istanza di Azure Digital Twins ed eliminare l'esposizione pubblica, nonché di evitare i dati exfiltration dalla [rete virtuale di Azure (VNet)](../virtual-network/virtual-networks-overview.md).

Ecco i passaggi descritti in questo articolo: 
1. Attivare il collegamento privato e configurare un endpoint privato per un'istanza di Azure Digital gemelli.
1. Disabilitare o abilitare i flag di accesso alla rete pubblici, per limitare l'accesso all'API solo alle connessioni a collegamenti privati.

## <a name="prerequisites"></a>Prerequisiti

Prima di poter configurare un endpoint privato, è necessaria una [**rete virtuale di Azure (VNet)**](../virtual-network/virtual-networks-overview.md) in cui è possibile distribuire l'endpoint. Se non si dispone già di un VNet, è possibile seguire una delle [guide introduttive](../virtual-network/quick-create-portal.md) per la rete virtuale di Azure per impostare questa impostazione.

## <a name="add-a-private-endpoint-for-an-azure-digital-twins-instance"></a>Aggiungere un endpoint privato per un'istanza di Azure Digital Twins 

È possibile attivare il collegamento privato con un endpoint privato per un'istanza di Azure Digital Twins come parte dell'installazione iniziale dell'istanza o abilitarlo in un secondo momento in un'istanza già esistente. 

Uno di questi metodi di creazione fornirà le stesse opzioni di configurazione e lo stesso risultato finale per l'istanza. In questa sezione viene descritto come eseguire ogni operazione nel [portale di Azure](https://portal.azure.com).

>[!TIP]
> È anche possibile configurare un endpoint di collegamento privato tramite il servizio di collegamento privato, anziché tramite l'istanza di Azure Digital gemelli. Vengono inoltre fornite le stesse opzioni di configurazione e lo stesso risultato finale.
>
> Per altre informazioni sulla configurazione delle risorse di collegamento privato, vedere la documentazione di collegamento privato per la [portale di Azure](../private-link/create-private-endpoint-portal.md), l'interfaccia della riga di comando di [Azure](../private-link/create-private-endpoint-cli.md), [ARM](../private-link/create-private-endpoint-template.md)o [PowerShell](../private-link/create-private-endpoint-powershell.md).

### <a name="add-a-private-endpoint-during-instance-creation"></a>Aggiungere un endpoint privato durante la creazione dell'istanza

In questa sezione si userà il [portale di Azure](https://portal.azure.com) per abilitare il collegamento privato con un endpoint privato in un'istanza di Azure Digital Twins attualmente in fase di creazione. Questa sezione è incentrata sul passaggio di rete del processo di creazione; per una procedura dettagliata completa per la creazione di una nuova istanza di dispositivi gemelli digitali di Azure, vedere [*procedura: configurare un'istanza e l'autenticazione*](how-to-set-up-instance-portal.md).

Le opzioni di collegamento privato si trovano nella scheda **rete** di configurazione dell'istanza.

In questa scheda è possibile abilitare gli endpoint privati selezionando l'opzione **endpoint privato** per il **metodo di connettività**.

Verrà aggiunta una sezione denominata **connessioni a endpoint privati** in cui è possibile configurare i dettagli dell'endpoint privato. Per continuare, selezionare il pulsante **+ Aggiungi** .

:::image type="content" source="media/how-to-enable-private-link/create-instance-networking-1.png" alt-text="Screenshot della portale di Azure che mostra la scheda rete della finestra di dialogo Crea risorsa per i dispositivi gemelli digitali di Azure. C'è un'evidenziazione intorno al nome della scheda, l'opzione dell'endpoint privato per il metodo di connettività e il pulsante + Aggiungi per creare una nuova connessione all'endpoint privato." lightbox="media/how-to-enable-private-link/create-instance-networking-1.png":::

Verrà aperta una pagina per immettere i dettagli di un nuovo endpoint privato.

:::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-full.png" alt-text="Screenshot della portale di Azure che mostra la pagina Crea endpoint privato. Contiene i campi descritti di seguito.":::

1. Compilare le selezioni per la **sottoscrizione** e il **gruppo di risorse**. Impostare il **percorso** sullo stesso percorso del VNet da usare. Scegliere un **nome** per l'endpoint e per **risorse secondarie di destinazione** Selezionare *API*.

1. Selezionare quindi la **rete virtuale** e la **Subnet** che si vuole usare per distribuire l'endpoint.

1. Infine, scegliere se eseguire l' **integrazione con la zona DNS privata**. È possibile usare il valore predefinito **Sì** o, per informazioni su questa opzione, è possibile seguire il collegamento nel portale per [altre informazioni sull'integrazione con DNS privato](../private-link/private-endpoint-overview.md#dns-configuration).

Dopo aver compilato le opzioni di configurazione, fare clic su **OK** per terminare.

Verrà visualizzata di nuovo la scheda **rete** dell'installazione dell'istanza di Azure Digital Twins, in cui il nuovo endpoint dovrebbe essere visibile in * * connessioni a endpoint privati.

:::image type="content" source="media/how-to-enable-private-link/create-instance-networking-2.png" alt-text="Screenshot della portale di Azure che mostra la scheda rete della finestra di dialogo Crea risorsa per i dispositivi gemelli digitali di Azure. Viene evidenziata la nuova connessione all'endpoint privato e i pulsanti di navigazione (Revisione + creazione, precedente, avanti: avanzata)." lightbox="media/how-to-enable-private-link/create-instance-networking-2.png":::

È quindi possibile usare i pulsanti di spostamento in basso per continuare con il resto dell'installazione dell'istanza.

### <a name="add-a-private-endpoint-to-an-existing-instance"></a>Aggiungere un endpoint privato a un'istanza esistente

In questa sezione si userà il [portale di Azure](https://portal.azure.com) per abilitare il collegamento privato con un endpoint privato per un'istanza di Azure Digital Twins già esistente.

1. Per prima cosa, passare alla [portale di Azure](https://portal.azure.com) in un browser. Visualizzare l'istanza di Azure Digital gemelli cercandone il nome nella barra di ricerca del portale.

1. Selezionare **rete (anteprima)** nel menu a sinistra.

1. Passare alla scheda **connessioni endpoint privato** .

1. Selezionare **+ endpoint privato** per aprire l'installazione di **Crea un endpoint privato** .

    :::image type="content" source="media/how-to-enable-private-link/add-endpoint-digital-twins.png" alt-text="Screenshot della portale di Azure che mostra la pagina rete (anteprima) per un'istanza di Azure Digital gemelli. Viene evidenziata la scheda connessioni endpoint privato e viene evidenziato anche il pulsante + endpoint privato." lightbox="media/how-to-enable-private-link/add-endpoint-digital-twins.png":::

1. Nella scheda  **nozioni**   di base immettere o selezionare la **sottoscrizione** e il gruppo di **risorse** del progetto e un **nome** e un' **area** per l'endpoint. L'area deve essere la stessa dell'area per il VNet in uso.

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-1.png" alt-text="Screenshot del portale di Azure che mostra la prima scheda (nozioni di base) della finestra di dialogo Crea un endpoint privato. Contiene i campi descritti in precedenza.":::

    Al termine, selezionare il pulsante **Next: Resource >** per passare alla scheda successiva.

1. Nella scheda **risorsa** immettere o selezionare queste informazioni: 
    * **Metodo di connessione**: selezionare **Connetti a una risorsa di Azure nella directory** per cercare l'istanza di Azure Digital gemelli.
    * **Sottoscrizione**: immettere la sottoscrizione.
    * **Tipo di risorsa**: selezionare **Microsoft. DigitalTwins/digitalTwinsInstances**
    * **Risorsa**: selezionare il nome dell'istanza di Azure Digital gemelli.
    * **Risorsa secondaria di destinazione**: selezionare l' **API**.

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-2.png" alt-text="Screenshot della portale di Azure che mostra la seconda scheda (risorsa) della finestra di dialogo Crea un endpoint privato. Contiene i campi descritti in precedenza.":::

    Al termine, selezionare il pulsante **Next: Configuration >** per passare alla scheda successiva.    

1. Nella scheda **configurazione** immettere o selezionare queste informazioni:
    * **Rete virtuale**: selezionare la rete virtuale.
    * **Subnet**: scegliere una subnet dalla rete virtuale.
    * **Integrazione con la zona DNS privata**: scegliere se eseguire l' **integrazione con la zona DNS privata**. È possibile usare il valore predefinito **Sì** o, per informazioni su questa opzione, è possibile seguire il collegamento nel portale per [altre informazioni sull'integrazione con DNS privato](../private-link/private-endpoint-overview.md#dns-configuration).
    Se si seleziona **Sì**, è possibile lasciare le informazioni di configurazione predefinite.

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-3.png" alt-text="Screenshot del portale di Azure che mostra la terza scheda (configurazione) della finestra di dialogo Crea un endpoint privato. Contiene i campi descritti in precedenza.":::

    Al termine, è possibile selezionare il pulsante **Verifica + crea** per completare l'installazione. 

1. Nella scheda **Verifica e crea** verificare le selezioni effettuate e selezionare il pulsante  **Crea** . 

Al termine della distribuzione, l'endpoint dovrebbe essere visualizzato nelle connessioni all'endpoint privato per l'istanza di Azure Digital gemelli.

>[!TIP]
> L'endpoint può essere visualizzato anche dal centro collegamenti privati nell'portale di Azure.

## <a name="disable--enable-public-network-access-flags"></a>Disabilitare/abilitare i flag di accesso alla rete pubblica

È possibile configurare l'istanza di Azure Digital Twins per negare tutte le connessioni pubbliche e consentire solo le connessioni tramite endpoint privati per migliorare la sicurezza della rete. Questa operazione viene eseguita con un **flag di accesso alla rete pubblico**. 

Questo criterio consente di limitare l'accesso all'API solo alle connessioni di collegamento privato. Quando il flag di accesso alla rete pubblica è impostato su *disabilitato*, tutte le chiamate API REST al piano dati dell'istanza di Azure Digital Twins dal cloud pubblico restituiranno `403, Unauthorized` . In alternativa, quando il criterio è impostato su *disabilitato* e una richiesta viene effettuata tramite un endpoint privato, la chiamata API avrà esito positivo.

È possibile aggiornare il valore del flag di rete usando il [portale di Azure](https://portal.azure.com), l' [interfaccia](/cli/azure/)della riga di comando di Azure o lo [strumento di comando ARMClient](https://github.com/projectkudu/ARMClient).

### <a name="option-1-using-the-azure-portal"></a>Opzione 1: utilizzo del portale di Azure

Per disabilitare o abilitare l'accesso alla rete pubblica nel [portale di Azure](https://portal.azure.com), aprire il portale e passare all'istanza di Azure Digital gemelli.

1. Selezionare **rete (anteprima)** nel menu a sinistra.

1. Nella scheda **accesso pubblico** impostare **Consenti accesso alla rete pubblica su** **disabilitato** o su **tutte le reti**.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-enable-private-link/network-flag-portal.png" alt-text="Screenshot della portale di Azure che mostra la pagina rete (anteprima) per un'istanza di dispositivi gemelli digitali di Azure. Viene evidenziata la scheda accesso pubblico e viene evidenziata anche l'opzione per scegliere se consentire l'accesso alla rete pubblica." lightbox="media/how-to-enable-private-link/network-flag-portal.png":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

    Selezionare **Salva**.

### <a name="option-2-using-the-azure-cli"></a>Opzione 2: uso dell'interfaccia della riga di comando di Azure

Con l'interfaccia della riga di comando di Azure, questa azione viene eseguita usando il `az resource update` comando come illustrato di seguito. La proprietà publicNetworkAccess in un'istanza di Azure Digital Twins può essere impostata su `disabled` o `enabled` .

Per disabilitare il flag, utilizzare il comando seguente, sostituendo i segnaposto con i valori dell'istanza.
 
```azurecli
az resource update --ids /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance> --set properties.publicNetworkAccess=disabled  
```

Di seguito è riportata una schermata dell'aspetto dell'output.

:::image type="content" source="media/how-to-enable-private-link/network-flag-cli.png" alt-text="Screenshot di una finestra del terminale che esegue il comando dell'interfaccia della riga di comando di Azure. L'output contiene i dettagli dell'istanza, inclusa una proprietà denominata publicNetworkAccess con un valore disabilitato." lightbox="media/how-to-enable-private-link/network-flag-cli.png":::

Per abilitare il flag, utilizzare il seguente comando simile.
 
```azurecli
az resource update --ids /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance> --set properties.publicNetworkAccess=enabled  
```

### <a name="option-3-usingarmclient"></a>Opzione 3: uso di ARMClient  

Con lo [strumento di comando ARMClient](https://github.com/projectkudu/ARMClient), l'accesso alla rete pubblica viene abilitato o disabilitato usando i comandi seguenti. 

Per disabilitare l'accesso alla rete pubblica:
  
```cmd/sh
armclient login 

armclient PATCH /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance>?api-version=2020-12-01 "{ 'properties': { 'publicNetworkAccess': 'disabled' } }"  
```

Per abilitare l'accesso alla rete pubblica:  
  
```cmd/sh
armclient login 

armclient PATCH /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance>?api-version=2020-12-01 "{ 'properties': { 'publicNetworkAccess': 'enabled' } }"  
``` 

## <a name="next-steps"></a>Passaggi successivi

Scopri di più sul collegamento privato per Azure: 
* [*Che cos'è il servizio di collegamento privato di Azure?*](../private-link/private-link-service-overview.md)