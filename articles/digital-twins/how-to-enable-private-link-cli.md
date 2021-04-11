---
title: Abilitare l'accesso privato con collegamento privato (anteprima)-interfaccia della riga di comando
titleSuffix: Azure Digital Twins
description: Vedere come abilitare l'accesso privato per le soluzioni di dispositivi gemelli digitali di Azure con collegamento privato usando l'interfaccia della riga di comando di Azure.
author: baanders
ms.author: baanders
ms.date: 02/09/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 4dab08983fc1348ca49e728a65d48aa65fe19a47
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105715"
---
# <a name="enable-private-access-with-private-link-preview-azure-cli"></a>Abilitare l'accesso privato con collegamento privato (anteprima): interfaccia della riga di comando di Azure

[!INCLUDE [digital-twins-private-link-selector.md](../../includes/digital-twins-private-link-selector.md)]

Questo articolo descrive i diversi modi per [abilitare il collegamento privato con un endpoint privato per un'istanza di dispositivi gemelli digitali di Azure](concepts-security.md#private-network-access-with-azure-private-link-preview) (attualmente in anteprima). La configurazione di un endpoint privato per l'istanza di Azure Digital Twins consente di proteggere l'istanza di Azure Digital Twins ed eliminare l'esposizione pubblica, nonché di evitare i dati exfiltration dalla [rete virtuale di Azure (VNet)](../virtual-network/virtual-networks-overview.md).

Questo articolo illustra il processo usando l'interfaccia della riga di comando di [**Azure**](/cli/azure/what-is-azure-cli).

Ecco i passaggi descritti in questo articolo: 
1. Attivare il collegamento privato e configurare un endpoint privato per un'istanza di Azure Digital gemelli.
1. Disabilitare o abilitare i flag di accesso alla rete pubblici, per limitare l'accesso all'API solo alle connessioni a collegamenti privati.

## <a name="prerequisites"></a>Prerequisiti

Prima di poter configurare un endpoint privato, è necessaria una [**rete virtuale di Azure (VNet)**](../virtual-network/virtual-networks-overview.md) in cui è possibile distribuire l'endpoint. Se non si dispone già di un VNet, è possibile seguire una delle [guide introduttive](../virtual-network/quick-create-portal.md) per la rete virtuale di Azure per impostare questa impostazione.

## <a name="manage-private-endpoints-for-an-azure-digital-twins-instance"></a>Gestire endpoint privati per un'istanza di Azure Digital Twins 

Quando si usa l'interfaccia della riga di comando di [Azure](/cli/azure/what-is-azure-cli), è possibile configurare endpoint privati con collegamento privato in un'istanza di dispositivi gemelli digitali di Azure già esistente. non è possibile aggiungerlo come parte della creazione dell'istanza. È quindi possibile continuare a visualizzarli e gestirli tramite altri comandi dell'interfaccia della riga di comando. 

>[!TIP]
> È anche possibile configurare un endpoint di collegamento privato tramite il servizio di collegamento privato, anziché tramite l'istanza di Azure Digital gemelli. Vengono inoltre fornite le stesse opzioni di configurazione e lo stesso risultato finale.
>
> Per altre informazioni sulla configurazione delle risorse di collegamento privato, vedere la documentazione di collegamento privato per la [portale di Azure](../private-link/create-private-endpoint-portal.md), l' [interfaccia](../private-link/create-private-endpoint-cli.md)della riga di comando di Azure, i [modelli ARM](../private-link/create-private-endpoint-template.md)o [PowerShell](../private-link/create-private-endpoint-powershell.md).

### <a name="add-a-private-endpoint-to-an-existing-instance"></a>Aggiungere un endpoint privato a un'istanza esistente

Per creare un endpoint privato e collegarlo a un'istanza di Azure Digital Twins, usare il comando [**AZ network private-endpoint create**](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) . Identificare l'istanza di Azure Digital Twins usando l'ID completo nel `--private-connection-resource-id` parametro.

Di seguito è riportato un esempio che usa il comando per creare un endpoint privato con solo i parametri obbligatori.

```azurecli-interactive
az network private-endpoint create --connection-name {private_link_service_connection} -n {name_for_private_endpoint} -g {resource_group} --subnet {subnet_ID} --private-connection-resource-id "/subscriptions/{subscription_ID}/resourceGroups/{resource_group}/providers/Microsoft.DigitalTwins/digitalTwinsInstances/{Azure_Digital_Twins_instance_name}" 
```

Per un elenco completo dei parametri obbligatori e facoltativi, oltre ad esempi di creazione di endpoint privati, vedere la [documentazione **AZ network private-endpoint create** Reference](/cli/azure/network/private-endpoint#az_network_private_endpoint_create).

### <a name="manage-private-endpoint-connections-on-the-instance"></a>Gestire le connessioni a endpoint privati nell'istanza

Dopo la creazione di un endpoint privato per l'istanza di Azure Digital gemelli, è possibile usare i comandi di connessione dell'endpoint privato di [**rete AZ DT**](/cli/azure/dt/network/private-endpoint/connection) per continuare a gestire le **connessioni** degli endpoint privati relativamente all'istanza. Alcune operazioni sono:
* Mostra una connessione all'endpoint privato
* Impostare lo stato della connessione dell'endpoint privato
* Eliminare la connessione all'endpoint privato
* Elencare tutte le connessioni di endpoint privato per un'istanza

Per ulteriori informazioni ed esempi, vedere la [documentazione di riferimento **AZ DT network private-endpoint**](/cli/azure/dt/network/private-endpoint).

### <a name="manage-other-private-link-information-on-an-azure-digital-twins-instance"></a>Gestire altre informazioni sui collegamenti privati in un'istanza di dispositivi gemelli digitali di Azure

È possibile ottenere informazioni aggiuntive sullo stato del collegamento privato dell'istanza con i comandi di [**collegamento privato di rete AZ DT**](/cli/azure/dt/network/private-link) . Alcune operazioni sono:
* Elencare i collegamenti privati associati a un'istanza di Azure Digital Twins
* Mostra un collegamento privato associato all'istanza

Per ulteriori informazioni ed esempi, vedere la documentazione di riferimento per il [ **collegamento privato di rete AZ DT**](/cli/azure/dt/network/private-link).

## <a name="disable--enable-public-network-access-flags"></a>Disabilitare/abilitare i flag di accesso alla rete pubblica

È possibile configurare l'istanza di Azure Digital Twins per negare tutte le connessioni pubbliche e consentire solo le connessioni tramite endpoint privati per migliorare la sicurezza della rete. Questa operazione viene eseguita con un **flag di accesso alla rete pubblico**. 

Questo criterio consente di limitare l'accesso all'API solo alle connessioni di collegamento privato. Quando il flag di accesso alla rete pubblica è impostato su *disabilitato*, tutte le chiamate API REST al piano dati dell'istanza di Azure Digital Twins dal cloud pubblico restituiranno `403, Unauthorized` . In alternativa, quando il criterio è impostato su *disabilitato* e una richiesta viene effettuata tramite un endpoint privato, la chiamata API avrà esito positivo.

Questo articolo illustra come aggiornare il valore del flag di rete usando l'interfaccia della riga di comando di [Azure](/cli/azure/) o lo [strumento di comando ARMClient](https://github.com/projectkudu/ARMClient). Per istruzioni su come eseguire questa operazione con il portale di Azure, vedere la [versione del portale](how-to-enable-private-link-portal.md) di questo articolo.

### <a name="use-the-azure-cli"></a>Usare l'interfaccia della riga di comando di Azure

Nell'interfaccia della riga di comando di Azure è possibile disabilitare o abilitare l'accesso alla rete pubblica aggiungendo un `--public-network-access` parametro al `az dt create` comando. Sebbene questo comando possa essere utilizzato anche per creare una nuova istanza di, è possibile utilizzarla per modificare le proprietà di un'istanza esistente fornendogli il nome di un'istanza già esistente. Per altre informazioni su questo comando, vedere la [documentazione di riferimento](/cli/azure/dt#az_dt_create) o le [istruzioni generali per la configurazione di un'istanza di dispositivi gemelli digitali di Azure](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance).

Per **disabilitare** l'accesso alla rete pubblica per un'istanza di Azure Digital Twins, usare il `--public-network-access` parametro come segue:

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --public-network-access Disabled
```

Per **abilitare** l'accesso alla rete pubblica in un'istanza di in cui è attualmente disabilitato, usare il comando simile seguente:

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --public-network-access Enabled
```

### <a name="usethe-armclientcommand-tool"></a>Usare lo strumento di comando ARMClient 

Con lo [strumento di comando ARMClient](https://github.com/projectkudu/ARMClient), l'accesso alla rete pubblica viene abilitato o disabilitato usando i comandi seguenti. 

Per **disabilitare** l'accesso alla rete pubblica:
  
```cmd/sh
armclient login 

armclient PATCH /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance>?api-version=2020-12-01 "{ 'properties': { 'publicNetworkAccess': 'disabled' } }"  
```

Per **abilitare** l'accesso alla rete pubblica:  
  
```cmd/sh
armclient login 

armclient PATCH /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance>?api-version=2020-12-01 "{ 'properties': { 'publicNetworkAccess': 'enabled' } }"  
``` 

## <a name="next-steps"></a>Passaggi successivi

Scopri di più sul collegamento privato per Azure: 
* [*Che cos'è il servizio di collegamento privato di Azure?*](../private-link/private-link-service-overview.md)