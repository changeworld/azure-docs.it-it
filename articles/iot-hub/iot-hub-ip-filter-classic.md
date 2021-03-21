---
title: Filtro IP classico dell'hub Azure (obsoleto) | Microsoft Docs
description: Come eseguire l'aggiornamento dal filtro IP classico e quali sono i vantaggi
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/16/2020
ms.author: jlian
ms.openlocfilehash: 6f326bafb311acedc48c5a349c78f1cd6bcebc87
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101661155"
---
# <a name="iot-hub-classic-ip-filter-and-how-to-upgrade"></a>Filtro IP classico dell'hub Internet e modalità di aggiornamento 

Il filtro IP aggiornato per l'hub tutto protegge l'endpoint predefinito ed è protetto per impostazione predefinita. Sebbene ci impegniamo a non apportare mai modifiche di rilievo, il modello di sicurezza avanzato del filtro IP aggiornato non è compatibile con il filtro IP classico, quindi si annuncia il ritiro. Per ulteriori informazioni sul nuovo filtro IP aggiornato, vedere la pagina relativa alle [novità](#whats-new) e ai [filtri IP dell'hub](iot-hub-ip-filtering.md)Internet.

Per evitare l'alterazione del servizio, è necessario eseguire l'aggiornamento guidato prima della scadenza della migrazione, a quel punto l'aggiornamento verrà eseguito automaticamente. Per altre informazioni sulla sequenza temporale della migrazione, vedere [aggiornamento di Azure](https://aka.ms/ipfilterv2azupdate).

## <a name="how-to-upgrade"></a>Come eseguire l'aggiornamento

1.  Visita portale di Azure
2.  Passare all'hub IoT.
3.  Selezionare **rete** dal menu a sinistra.
4.  Verrà visualizzato un banner che richiede di aggiornare il filtro IP al nuovo modello. Selezionare **Sì** per continuare.
    :::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-upgrade-banner.png" alt-text="Immagine che mostra la richiesta del banner per eseguire l'aggiornamento dal filtro IP classico":::
5.  Poiché il nuovo filtro IP blocca tutti gli indirizzi IP per impostazione predefinita, l'aggiornamento rimuove le singole regole di rifiuto, ma consente di esaminarle prima del salvataggio. Esaminare attentamente le regole per assicurarsi che funzionino per l'utente.
6.  Seguire le istruzioni per completare l'aggiornamento.

## <a name="whats-new"></a>Novità

### <a name="secure-by-default"></a>Protezione per impostazione predefinita

Il filtro IP classico consente implicitamente a tutti gli indirizzi IP di connettersi all'hub Internet per impostazione predefinita, che non è perfettamente allineato agli scenari di sicurezza di rete più comuni. In genere, è consigliabile che solo gli indirizzi IP attendibili siano in grado di connettersi all'hub Internet e rifiutare tutto il resto. Per raggiungere questo obiettivo con il filtro IP classico, si tratta di un processo in più passaggi. Ad esempio, se si vuole accettare solo il traffico da `192.168.100.0/22` , è necessario

1. Configurare una singola regola di *autorizzazione* per `192.168.100.0/22` .
1. Configurare una regola di *blocco* diversa per `0.0.0.0/0` (regola "blocca tutto")
1. Verificare che le regole siano ordinate correttamente, con la regola Consenti ordinata sopra la regola blocco.

In pratica, questo processo in più passaggi causa confusione. Gli utenti non hanno configurato la regola "blocca tutto" o non hanno ordinato correttamente le regole, causando un'esposizione imprevista. 

Il nuovo filtro IP blocca tutti gli indirizzi IP per impostazione predefinita. Solo gli intervalli IP aggiunti esplicitamente possono connettersi all'hub Internet. Nell'esempio precedente i passaggi 2 e 3 non sono più necessari. Questo nuovo comportamento semplifica la configurazione e rispetta il [principio protetto per impostazione predefinita](https://wikipedia.org/wiki/Secure_by_default).

### <a name="protect-the-built-in-event-hub-compatible-endpoint"></a>Proteggere l'endpoint compatibile con l'hub eventi predefinito

Non è possibile applicare il filtro IP classico all'endpoint predefinito. Questa limitazione significa che, l'evento con blocco di tutte le regole (blocco `0.0.0.0/0` ) configurato, l'endpoint predefinito è ancora accessibile da qualsiasi indirizzo IP.

Il nuovo filtro IP offre un'opzione per applicare le regole all'endpoint predefinito, riducendo l'esposizione alle minacce alla sicurezza della rete.

:::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-built-in-endpoint.png" alt-text="Immagine che mostra l'interruttore da applicare all'endpoint predefinito":::

> [!NOTE]
> Questa opzione non è disponibile per gli hub Internet (F1). Per applicare le regole di filtro IP all'endpoint predefinito, usare un hub per le cose a pagamento.

### <a name="api-impact"></a>Effetto API

Il filtro IP aggiornato è disponibile nell'API delle risorse dell'hub Internet `2020-08-31` (così come `2020-08-31-preview` ) e in avanti. Il filtro IP classico è ancora disponibile in tutte le versioni dell'API, ma verrà rimosso in una versione futura dell'API accanto alla scadenza della migrazione. Per altre informazioni sulla sequenza temporale della migrazione, vedere [aggiornamento di Azure](https://aka.ms/ipfilterv2azupdate).

## <a name="tip-try-the-changes-before-they-apply"></a>Suggerimento: provare le modifiche prima di applicarle

Poiché il nuovo filtro IP blocca tutti gli indirizzi IP per impostazione predefinita, le singole regole di blocco non sono più compatibili. Il processo di aggiornamento guidato rimuove quindi le singole regole di blocco. 

Per provare a modificare con il filtro IP classico:

1. Visitare la scheda **rete** nell'hub Internet delle cose
1. Annotare la configurazione del filtro IP esistente (classico), nel caso in cui si desideri eseguire il rollback
1. Accanto a regole con **blocco** selezionare l'icona del cestino per rimuoverle.
1. Aggiungere un'altra regola nella parte inferiore con `0.0.0.0/0` e scegliere **blocca**
1. Selezionare **Salva**

Questa configurazione simula il comportamento del nuovo filtro IP dopo l'aggiornamento dal modello classico. Un'eccezione è rappresentata dall'endpoint protection incorporato, che non è possibile provare a usare il filtro IP classico. Questa funzionalità, tuttavia, è facoltativa, pertanto non è necessario utilizzarla se si ritiene che sia possibile che si verifichi un problema.

## <a name="tip-check-diagnostic-logs-for-all-ip-connections-to-your-iot-hub"></a>Suggerimento: controllare i log di diagnostica per tutte le connessioni IP all'hub Internet

Per garantire una transizione uniforme, controllare i log di diagnostica nella categoria connessioni. Controllare la `maskedIpAddress` proprietà per verificare se gli intervalli sono quelli previsti. Ricorda: il nuovo filtro IP bloccherà tutti gli indirizzi IP che non sono stati aggiunti in modo esplicito.

## <a name="iot-hub-classic-ip-filter-documentation-retired"></a>Documentazione del filtro IP classico dell'hub Internet (ritirata)

> [!IMPORTANT]
> Di seguito è riportata la documentazione originale per il filtro IP classico, che verrà ritirato.

La sicurezza è un aspetto importante di qualsiasi soluzione IoT basata su un hub IoT di Azure. Talvolta è necessario specificare in modo esplicito gli indirizzi IP da cui possono connettersi i dispositivi come parte della configurazione di sicurezza. La funzionalità *Filtro IP* consente di configurare regole per rifiutare o accettare il traffico da specifici indirizzi IPv4.

### <a name="when-to-use"></a>Utilizzo

Esistono due casi d'uso specifici in cui è utile bloccare gli endpoint dell'hub IoT per determinati indirizzi IP:

* L'hub IoT deve ricevere il traffico solo da un intervallo di indirizzi IP specificato e rifiutare tutto il resto. Si usa ad esempio l'hub IoT con [Azure ExpressRoute](../expressroute/expressroute-faqs.md#supported-services) per creare connessioni private tra un hub IoT e l'infrastruttura locale.

* È necessario rifiutare il traffico proveniente da indirizzi IP identificati come sospetti dall'amministratore dell'hub IoT.

### <a name="how-filter-rules-are-applied"></a>Come vengono applicate le regole di filtro

Le regole del filtro IP vengono applicate a livello del servizio dell'hub IoT. Le regole del filtro IP vengono quindi applicate a tutte le connessioni provenienti dai dispositivi e dalle app back-end con qualsiasi protocollo supportato. Tuttavia, i client che leggono direttamente dall'[endpoint compatibile con l'hub eventi integrato](iot-hub-devguide-messages-read-builtin.md) (non tramite la stringa di connessione dell'hub IoT) non sono associati alle regole del filtro IP. 

Qualsiasi tentativo di connessione da un indirizzo IP corrispondente a una regola di rifiuto nell'hub IoT riceve un codice di stato 401 - Non autorizzato e la descrizione. Il messaggio di risposta non indica la regola IP. Il rifiuto di indirizzi IP può impedire l'interazione di altri servizi di Azure, ad esempio Analisi di flusso di Azure, Macchine virtuali di Azure o Device Explorer nel portale di Azure, con l'hub IoT.

> [!NOTE]
> Se è necessario usare Analisi di flusso di Azure (ASA) per leggere i messaggi da un hub IoT con i filtri IP abilitati, usare il nome e l'endpoint compatibile con l'hub eventi dell'hub IoT per aggiungere manualmente un [input del flusso di Hub eventi](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs) nell'ASA.

### <a name="default-setting"></a>Impostazione predefinita

Per impostazione predefinita, la griglia **Filtro IP** nel portale di un hub IoT è vuota. Questa impostazione predefinita indica che l'hub in uso accetta connessioni da qualsiasi indirizzo IP. Questa impostazione predefinita equivale a una regola che accetta l'intervallo di indirizzi IP 0.0.0.0/0.

Per accedere alla pagina Impostazioni del filtro IP, selezionare **Rete**, **Accesso pubblico** e quindi scegliere **Intervalli IP selezionati**:

:::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-default.png" alt-text="Impostazioni predefinite del filtro IP dell'hub IoT":::

### <a name="add-or-edit-an-ip-filter-rule"></a>Aggiungere o modificare una regola del filtro IP

Per aggiungere una regola di filtro IP, selezionare **+ Aggiungi regola di filtro IP**.

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-add-rule.png" alt-text="Aggiungere una regola di filtro IP a un hub IoT":::

Dopo aver selezionato **Aggiungi regola di filtro IP**, compilare i campi.

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-after-selecting-add.png" alt-text="Dopo aver selezionato Aggiungi una regola di filtro IP":::

* Specificare un **nome** per la regola di Filtro IP. Questo deve essere univoco e costituito da una stringa alfanumerica che non fa distinzione tra maiuscole e minuscole e ha una lunghezza massima di 128 caratteri. Sono ammessi solo caratteri alfanumerici ASCII a 7 bit più `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`.

* Specificare un singolo indirizzo IPv4 o un blocco di indirizzi IP in notazione CIDR. In notazione CIDR, ad esempio, 192.168.100.0/22 rappresenta gli indirizzi IPv4 1024 da 192.168.100.0 a 192.168.103.255.

* Selezionare **Consenti** o **Blocca** come **azione** per la regola del Filtro IP.

Dopo aver compilato i campi, selezionare **Salva** per salvare la regola. Viene visualizzato un avviso che informa che l'aggiornamento è in corso.

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-save-new-rule.png" alt-text="Notifica sul salvataggio di una regola di filtro IP":::

L'opzione **Aggiungi** è disabilitata quando si raggiunge il numero massimo di dieci regole del filtro IP.

Per modificare una regola esistente, selezionare i dati che si desidera modificare, apportare le modifiche, quindi selezionare **Salva** per salvare la modifica.

### <a name="delete-an-ip-filter-rule"></a>Eliminare una regola del filtro IP

Per eliminare una regola di filtro IP, selezionare l'icona del cestino sulla riga e quindi selezionare **Salva**. La regola viene rimossa e la modifica viene salvata.

:::image type="content" source="./media/iot-hub-ip-filter-classic/ip-filter-delete-rule.png" alt-text="Eliminare una regola del filtro IP dell'hub IoT":::

### <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>Recuperare e aggiornare i filtri IP tramite l'interfaccia della riga di comando di Azure

I filtri IP dell'hub IoT possono essere recuperati e aggiornati tramite l'[interfaccia della riga di comando di Azure](/cli/azure/).

Per recuperare i filtri IP correnti dell'hub IoT, eseguire:

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

Verrà restituito un oggetto JSON in cui i filtri IP esistenti sono elencati sotto la chiave `properties.ipFilterRules`:

```json
{
...
    "properties": {
        "ipFilterRules": [
        {
            "action": "Reject",
            "filterName": "MaliciousIP",
            "ipMask": "6.6.6.6/6"
        },
        {
            "action": "Allow",
            "filterName": "GoodIP",
            "ipMask": "131.107.160.200"
        },
        ...
        ],
    },
...
}
```

Per aggiungere un nuovo filtro IP per l'hub IoT, eseguire:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules "{\"action\":\"Reject\",\"filterName\":\"MaliciousIP\",\"ipMask\":\"6.6.6.6/6\"}"
```

Per rimuovere un filtro IP esistente nell'hub IoT, eseguire:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules <ipFilterIndexToRemove>
```

Si noti che `<ipFilterIndexToRemove>` deve corrispondere all'ordinamento dei filtri IP nella chiave `properties.ipFilterRules` dell'hub IoT.

### <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>Recuperare e aggiornare i filtri IP tramite Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

I filtri IP dell'hub IoT possono essere recuperati e impostati tramite [Azure PowerShell](/powershell/azure/).

```powershell
# Get your IoT Hub resource using its name and its resource group name
$iothubResource = Get-AzResource -ResourceGroupName <resourceGroupNmae> -ResourceName <iotHubName> -ExpandProperties

# Access existing IP filter rules
$iothubResource.Properties.ipFilterRules |% { Write-host $_ }

# Construct a new IP filter
$filter = @{'filterName'='MaliciousIP'; 'action'='Reject'; 'ipMask'='6.6.6.6/6'}

# Add your new IP filter rule
$iothubResource.Properties.ipFilterRules += $filter

# Remove an existing IP filter rule using its name, e.g., 'GoodIP'
$iothubResource.Properties.ipFilterRules = @($iothubResource.Properties.ipFilterRules | Where 'filterName' -ne 'GoodIP')

# Update your IoT Hub resource with your updated IP filters
$iothubResource | Set-AzResource -Force
```

### <a name="update-ip-filter-rules-using-rest"></a>Aggiornare le regole di filtro IP tramite REST

È possibile recuperare e modificare il filtro IP dell'hub IoT anche usando l'endpoint REST del provider di risorse di Azure. Vedere `properties.ipFilterRules` nel [metodo createorupdate](/rest/api/iothub/iothubresource/createorupdate).

### <a name="ip-filter-rule-evaluation"></a>Valutazione delle regole del filtro IP

Le regole del filtro IP vengono applicate in ordine e la prima regola corrispondente all'indirizzo IP determina l'azione di accettazione o rifiuto.

Se ad esempio si vogliono accettare gli indirizzi nell'intervallo 192.168.100.0/22 e rifiutare tutti gli altri, la prima regola nella griglia dovrà accettare l'intervallo di indirizzi 192.168.100.0/22. La regola successiva dovrà rifiutare tutti gli indirizzi usando l'intervallo 0.0.0.0/0.

È possibile modificare l'ordine delle regole del filtro IP nella griglia facendo clic sui tre punti verticali all'inizio di una riga e trascinando la selezione.

Per salvare il nuovo ordine delle regole del filtro IP, fare clic su **Salva**.

:::image type="content" source="media/iot-hub-ip-filter-classic/ip-filter-rule-order.png" alt-text="Modificare l'ordine delle regole del filtro IP dell'hub Internet":::

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Usare i filtri IP](iot-hub-ip-filtering.md)