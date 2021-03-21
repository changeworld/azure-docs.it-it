---
title: Filtri IP dell'hub IoT di Azure | Documentazione Microsoft
description: Come usare il filtro IP per consentire le connessioni da indirizzi IP specifici per all'hub Azure.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/12/2021
ms.author: jlian
ms.openlocfilehash: 2a76cede4bc72da9f30564f98ab9bb84028680f7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104581491"
---
# <a name="use-ip-filters"></a>Usare i filtri IP

La sicurezza è un aspetto importante di qualsiasi soluzione IoT basata su un hub IoT di Azure. Talvolta è necessario specificare in modo esplicito gli indirizzi IP da cui possono connettersi i dispositivi come parte della configurazione di sicurezza. La funzionalità *Filtro IP* consente di configurare regole per rifiutare o accettare il traffico da specifici indirizzi IPv4.

## <a name="when-to-use"></a>Utilizzo

Usare il filtro IP per ricevere traffico solo da un intervallo di indirizzi IP specificato e rifiutare tutto il resto. Ad esempio, si usa l'hub Internet delle cose con [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services) per creare connessioni private tra un hub Internet e l'infrastruttura locale.

## <a name="default-setting"></a>Impostazione predefinita

Per accedere alla pagina Impostazioni del filtro IP, selezionare **Rete**, **Accesso pubblico** e quindi scegliere **Intervalli IP selezionati**:

:::image type="content" source="media/iot-hub-ip-filtering/ip-filter-default.png" alt-text="Impostazioni predefinite del filtro IP dell'hub IoT":::

Per impostazione predefinita, la griglia **Filtro IP** nel portale di un hub IoT è vuota. Questa impostazione predefinita indica che l'hub blocca le connessioni da tutti gli indirizzi IP. Questa impostazione predefinita è equivalente a una regola che blocca l' `0.0.0.0/0` intervallo di indirizzi IP.

## <a name="add-or-edit-an-ip-filter-rule"></a>Aggiungere o modificare una regola del filtro IP

Per aggiungere una regola di filtro IP, selezionare **+ Aggiungi regola di filtro IP**.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-add-rule.png" alt-text="Aggiungere una regola di filtro IP a un hub IoT":::

Dopo aver selezionato **Aggiungi regola di filtro IP**, compilare i campi.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-after-selecting-add.png" alt-text="Dopo aver selezionato Aggiungi una regola di filtro IP":::

* Specificare un **nome** per la regola di Filtro IP. Questo nome deve essere una stringa alfanumerica univoca, senza distinzione tra maiuscole e minuscole, con una lunghezza di 128 caratteri. Sono ammessi solo caratteri alfanumerici ASCII a 7 bit più `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`.

* Specificare un singolo indirizzo IPv4 o un blocco di indirizzi IP in notazione CIDR. In notazione CIDR, ad esempio, 192.168.100.0/22 rappresenta gli indirizzi IPv4 1024 da 192.168.100.0 a 192.168.103.255.

Dopo aver compilato i campi, selezionare **Salva** per salvare la regola. Viene visualizzato un avviso che informa che l'aggiornamento è in corso.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png" alt-text="Notifica sul salvataggio di una regola di filtro IP":::

L'opzione **Aggiungi** è disabilitata quando si raggiunge il numero massimo di dieci regole del filtro IP.

Per modificare una regola esistente, selezionare i dati che si desidera modificare, apportare le modifiche, quindi selezionare **Salva** per salvare la modifica.

## <a name="delete-an-ip-filter-rule"></a>Eliminare una regola del filtro IP

Per eliminare una regola di filtro IP, selezionare l'icona del cestino sulla riga e quindi selezionare **Salva**. La regola viene rimossa e la modifica viene salvata.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-delete-rule.png" alt-text="Eliminare una regola del filtro IP dell'hub IoT":::

## <a name="apply-ip-filter-rules-to-the-built-in-event-hub-compatible-endpoint"></a>Applicare le regole di filtro IP all'endpoint compatibile con hub eventi predefinito

Per applicare le regole di filtro IP all'endpoint compatibile con hub eventi predefinito, selezionare la casella accanto a **Applica filtri IP all'endpoint predefinito?** e quindi selezionare **Salva**.

:::image type="content" source="media/iot-hub-ip-filtering/ip-filter-built-in-endpoint.png" alt-text="Immagine che mostra l'interruttore per l'endpoint predefinito e Salva":::

> [!NOTE]
> Questa opzione non è disponibile per gli hub Internet (F1). Per applicare le regole di filtro IP all'endpoint predefinito, usare un hub per le cose a pagamento.

Abilitando questa opzione, le regole del filtro IP vengono replicate nell'endpoint predefinito, in modo che solo gli intervalli di indirizzi IP attendibili possano accedervi.

Se si disabilita questa opzione, l'endpoint incorporato sarà accessibile a tutti gli indirizzi IP. Questo comportamento può essere utile se si vuole leggere dall'endpoint con servizi con la modifica di indirizzi IP come analisi di flusso di Azure. 

## <a name="how-filter-rules-are-applied"></a>Come vengono applicate le regole di filtro

Le regole del filtro IP vengono applicate a livello del servizio dell'hub IoT. Le regole del filtro IP vengono quindi applicate a tutte le connessioni provenienti dai dispositivi e dalle app back-end con qualsiasi protocollo supportato. È anche possibile scegliere se l' [endpoint compatibile con l'hub eventi predefinito](iot-hub-devguide-messages-read-builtin.md) , non tramite la stringa di connessione dell'hub Internet, è associato a queste regole. 

Qualsiasi tentativo di connessione da un indirizzo IP che non è consentito in modo esplicito riceve un codice di stato 401 non autorizzato e una descrizione. Il messaggio di risposta non indica la regola IP. Il rifiuto di indirizzi IP può impedire l'interazione di altri servizi di Azure, ad esempio Analisi di flusso di Azure, Macchine virtuali di Azure o Device Explorer nel portale di Azure, con l'hub IoT.

> [!NOTE]
> Se è necessario usare analisi di flusso di Azure (ASA) per leggere i messaggi da un hub delle cose con il filtro IP abilitato, **disabilitare** l'opzione **Applica filtri IP all'endpoint predefinito** e quindi usare il nome e l'endpoint compatibili con l'hub eventi dell'hub Internet per aggiungere manualmente un [input di flusso di hub eventi](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs) in ASA.

### <a name="ordering"></a>Ordering

Le regole del filtro IP sono *Consenti* regole e applicate senza ordinamento. Solo gli indirizzi IP aggiunti possono connettersi all'hub Internet. 

Se ad esempio si desidera accettare indirizzi nell'intervallo `192.168.100.0/22` e rifiutare tutto il resto, è sufficiente aggiungere una regola nella griglia con intervallo di indirizzi `192.168.100.0/22` .

## <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>Recuperare e aggiornare i filtri IP tramite l'interfaccia della riga di comando di Azure

I filtri IP dell'hub IoT possono essere recuperati e aggiornati tramite l'[interfaccia della riga di comando di Azure](/cli/azure/).

Per recuperare i filtri IP correnti dell'hub IoT, eseguire:

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

Verrà restituito un oggetto JSON in cui i filtri IP esistenti sono elencati sotto la chiave `properties.networkRuleSets`:

```json
{
...
    "properties": {
        "networkRuleSets": {
            "defaultAction": "Deny",
            "applyToBuiltInEventHubEndpoint": true,
            "ipRules": [{
                    "filterName": "TrustedFactories",
                    "action": "Allow",
                    "ipMask": "1.2.3.4/5"
                },
                {
                    "filterName": "TrustedDevices",
                    "action": "Allow",
                    "ipMask": "1.1.1.1/1"
                }
            ]
        }
    }
}
```

Per aggiungere un nuovo filtro IP per l'hub IoT, eseguire:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.networkRuleSets.ipRules "{\"action\":\"Allow\",\"filterName\":\"TrustedIP\",\"ipMask\":\"192.168.0.1\"}"
```

Per rimuovere un filtro IP esistente nell'hub IoT, eseguire:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.networkRuleSets.ipRules <ipFilterIndexToRemove>
```

In questo caso, `<ipFilterIndexToRemove>` deve corrispondere all'ordine dei filtri IP negli hub Internet delle cose `properties.networkRuleSets.ipRules` .

## <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>Recuperare e aggiornare i filtri IP tramite Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

I filtri IP dell'hub IoT possono essere recuperati e impostati tramite [Azure PowerShell](/powershell/azure/).

```powershell
# Get your IoT Hub resource using its name and its resource group name
$iothubResource = Get-AzResource -ResourceGroupName <resourceGroupNmae> -ResourceName <iotHubName> -ExpandProperties

# Access existing IP filter rules
$iothubResource.Properties.networkRuleSets.ipRules |% { Write-host $_ }

# Construct a new IP filter
$filter = @{'filterName'='TrustedIP'; 'action'='Allow'; 'ipMask'='192.168.0.1'}

# Add your new IP filter rule
$iothubResource.Properties.networkRuleSets.ipRules += $filter

# Remove an existing IP filter rule using its name, e.g., 'GoodIP'
$iothubResource.Properties.networkRuleSets.ipRules = @($iothubResource.Properties.networkRuleSets.ipRules | Where 'filterName' -ne 'GoodIP')

# Update your IoT Hub resource with your updated IP filters
$iothubResource | Set-AzResource -Force
```

## <a name="update-ip-filter-rules-using-rest"></a>Aggiornare le regole di filtro IP tramite REST


È possibile recuperare e modificare il filtro IP dell'hub IoT anche usando l'endpoint REST del provider di risorse di Azure. Vedere `properties.networkRuleSets` nel [metodo createorupdate](/rest/api/iothub/iothubresource/createorupdate).

## <a name="ip-filter-classic-retirement"></a>Il ritiro del filtro IP (classico)

Il filtro IP classico è stato ritirato. Per altre informazioni, vedere [il filtro IP classico dell'hub Internet e come eseguire l'aggiornamento](iot-hub-ip-filter-classic.md).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Metriche di hub IoT](./monitor-iot-hub.md)
* [Supporto dell'hub IoT per le reti virtuali con collegamento privato e identità gestita](virtual-network-support.md)
* [Gestione dell'accesso alla rete pubblica per l'hub Internet delle cose](iot-hub-public-network-access.md)
* [Monitorare l'hub IoT](monitor-iot-hub.md)
