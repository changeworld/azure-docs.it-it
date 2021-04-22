---
title: "Esercitazione: Eseguire l'acquisizione di pacchetti rete WAN virtuale di Azure connessioni da sito a sito"
description: Questa esercitazione illustra come eseguire l'acquisizione di pacchetti nel gateway VPN da sito a sito della rete WAN virtuale.
services: virtual-wan
author: wellee
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 04/13/2021
ms.author: wellee
Customer intent: As someone with a networking background using Virtual WAN, I want to perform a packet capture on my Site-to-site VPN Gateway.
ms.openlocfilehash: dbe7e06484797063ed4122ee3fdde625dc66c41f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107879156"
---
# <a name="perform-packet-capture-on-the-azure-virtual-wan-site-to-site-vpn-gateway"></a>Eseguire l'acquisizione di pacchetti rete WAN virtuale di Azure gateway VPN da sito a sito 

I problemi relativi alla connettività e alle prestazioni sono spesso complessi. Per restringere la causa del problema possono essere necessario tempo e impegno significativi. L'acquisizione di pacchetti consente di limitare l'ambito di un problema a determinate parti della rete. Può essere utile per determinare se il problema si trova sul lato cliente della rete, sul lato Azure della rete o in un punto qualsiasi. Dopo aver ristretto il problema, è più efficiente eseguire il debug e intraprendere azioni correttive.

L'articolo seguente descrive come avviare e arrestare un'acquisizione di pacchetti rete WAN virtuale di Azure gateway VPN da sito a sito. Attualmente, questa funzionalità è disponibile solo tramite PowerShell.

## <a name="prerequisites"></a>Prerequisiti

Per usare i passaggi descritti in questo articolo, è necessario che la configurazione seguente sia già stata impostata nell'ambiente:

* Una rete WAN virtuale, un hub virtuale e un gateway VPN da sito a sito distribuiti nell'hub virtuale. È anche possibile avere connessioni che connettono siti VPN al gateway VPN da sito a sito.


### <a name="working-with-azure-powershell"></a>Uso di Azure PowerShell

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="set-up-the-environment"></a>Configurare l'ambiente

Assicurarsi di essere nel contesto di sottoscrizione giusto eseguendo lo script seguente in PowerShell. In questo modo si è connessi a un utente che dispone delle autorizzazioni per eseguire l'acquisizione di pacchetti nel gateway VPN da sito a sito.

   ```azurepowershell-interactive
    $subid = “<insert Virtual WAN subscription ID here>”
    Set-AzContext -SubscriptionId $subid
   ```

## <a name="create-a-storage-account"></a><a name="createstorage"></a> Creare un account di archiviazione

È necessario creare un account di archiviazione nella sottoscrizione di Azure per archiviare i risultati dell'acquisizione di pacchetti. Fare riferimento a questo [documento](.././storage/common/storage-account-create.md) per istruzioni su come creare un account di archiviazione.

Dopo aver creato l'account, eseguire i comandi seguenti per generare un URL di firma di accesso condiviso. I risultati dell'acquisizione di pacchetti verranno archiviati tramite questo URL.
   ```azurepowershell-interactive
  $rgname = “<resource group name containing storage account>” 
$storeName = “<name of storage account> “
$containerName = “<name of container you want to store packet capture in>
$key = Get-AzStorageAccountKey -ResourceGroupName $rgname -Name $storeNAme
$context = New-AzStorageContext -StorageAccountName  $storeName -StorageAccountKey $key[0].value
New-AzStorageContainer -Name $containerName -Context $context
$container = Get-AzStorageContainer -Name $containerName  -Context $context
$now = get-date
$sasurl = New-AzureStorageContainerSASToken -Name $containerName -Context $context -Permission "rwd" -StartTime $now.AddHours(-1) -ExpiryTime $now.AddDays(1) -FullUri
   ```

## <a name="start-the-packet-capture"></a>Avviare l'acquisizione di pacchetti

Per avviare l'acquisizione di pacchetti, sono disponibili due opzioni, l'acquisizione di pacchetti in una singola connessione VPN o nell'intero gateway VPN da sito a sito. Si noti che se si sceglie di eseguire acquisizioni sulle connessioni VPN, è possibile eseguire acquisizioni solo su 6 connessioni contemporaneamente.

### <a name="packet-capture-on-a-site-to-site-vpn-gateway-all-connections"></a>Acquisizione di pacchetti in un gateway VPN da sito a sito (tutte le connessioni)

Eseguire i comandi seguenti. Per trovare il nome del gateway VPN da sito a sito, passare all'hub virtuale e fare clic su VPN (da sito a sito) in Connettività.

:::image type="content" source="./media/virtual-wan-pcap-screenshots/vpn-gateway-name.png" alt-text="Immagine del nome del gateway della rete WAN virtuale." lightbox="./media/virtual-wan-pcap-screenshots/vpn-gateway-name.png":::

   ```azurepowershell-interactive
Start-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name "<name of the Gateway>" -Sasurl $sasurl
   ```

### <a name="packet-capture-on-specific-site-to-site-vpn-connections"></a>Acquisizione di pacchetti in connessioni VPN da sito a sito specifiche

>[!NOTE]
> Si noti che è possibile eseguire un'acquisizione di pacchetti solo su 5 connessioni VPN contemporaneamente.


Eseguire i comandi seguenti. Per trovare il nome delle connessioni VPN da sito a sito, passare all'hub virtuale e fare clic su VPN (da sito a sito) in Connettività. Passare quindi al sito VPN su cui si vuole eseguire l'acquisizione di pacchetti e fare clic sui tre puntini a destra. Fare **clic su Modifica** connessione VPN nel menu visualizzato.

:::image type="content" source="./media/virtual-wan-pcap-screenshots/sample-connection.png" alt-text="Immagine di come trovare i nomi delle connessioni VPN." lightbox="./media/virtual-wan-pcap-screenshots/sample-connection.png":::

Per trovare il nome dei collegamenti connessi a un sito VPN specifico, fare clic sul sito VPN della sezione precedente ed esaminando la **tabella Collegamenti.** 

:::image type="content" source="./media/virtual-wan-pcap-screenshots/link-name-sample.png" alt-text="Immagine di come trovare il nome del collegamento VPN." lightbox="./media/virtual-wan-pcap-screenshots/link-name-sample.png":::

   ```azurepowershell-interactive
Start-AzVpnConnectionPacketCapture -ResourceGroupName $rg -Name "<name of the VPN connection>" -ParentResourceName “<name of the Gateway>” -LinkConnection “<comma separated list of links eg. "link1,link2">” -Sasurl $sasurl 
   ```

## <a name="optional-specifying-filters"></a>Facoltativo: Specifica di filtri

Sono disponibili alcuni strumenti comunemente disponibili per l'acquisizione di pacchetti. Ottenere acquisizioni di pacchetti pertinenti con questi strumenti può essere complicato, soprattutto in scenari con volumi elevati di traffico. Per semplificare le acquisizioni di pacchetti, è possibile specificare filtri sull'acquisizione di pacchetti per concentrarsi su comportamenti specifici. Di seguito sono riportati i parametri disponibili:

>[!NOTE]
> Per TracingFlags e TCPFlags, è possibile specificare più protocolli sommando i valori numerici per i protocolli che si desidera acquisire (come un OR logico). Ad esempio, se si desidera acquisire solo pacchetti ESP e OPVN, è necessario specificare un valore TracingFlag pari a 8+1 = 9.  

| Parametro | Descrizione | Valori predefiniti | Valori disponibili|
|--- |--- | --- | ---|
| TracingFlags | Numero intero che determina quali tipi di pacchetti vengono acquisiti | 11 (ESP, IKE, OVPN) | ESP = 1 IKE = 2 OPVN = 8 |
| TCPFlags | Numero intero che determina i tipi di pacchetti TCP acquisiti | 0 (nessuno) | FIN = 1, SYN = 2, RST = 4, PSH = 8, ACK = 16,URG = 32, ECE = 64, CWR = 128| 
| MaxPacketBufferSize|Dimensione massima in byte di un pacchetto acquisito. I pacchetti vengono troncati se maggiori del valore specificato. |120|Qualsiasi|
| MaxFileSize |Dimensioni massime del file di acquisizione in Mb. Le acquisizioni vengono archiviate in un buffer circolare in modo che l'overflow venga gestito in modo FIFO (pacchetti meno recenti rimossi per primi)|100|Qualsiasi|
|SourceSubnets|I pacchetti dagli intervalli CIDR specificati vengono acquisiti. Specificato come matrice.|[] (tutti gli indirizzi IPv4)|Matrice di subnet IPV4 delimitati da virgole|
| DestinationSubnets |Vengono acquisiti i pacchetti destinati per gli intervalli CIDR specificati. Specificato come matrice. |[] (tutti gli indirizzi IPv4)|Matrice di subnet IPV4 delimitati da virgole|
|SourcePort |Vengono acquisiti i pacchetti con origine negli intervalli specificati. Specificato come matrice.|[] (tutte le porte)|Matrice di porte delimitati da virgole|
|DestinationPort |Vengono acquisiti i pacchetti con destinazione negli intervalli specificati. Specificato come matrice.|[] (tutte le porte)|Matrice di porte delimitati da virgole|
| CaptureSingleDirectionTrafficOnly |Se true, nell'acquisizione di pacchetti verrà mostrata solo una direzione di un flusso bidirezionale. Verranno acquisite tutte le possibili combinazioni di IP e porte.|Vero|True, False|
|Protocollo|Matrice di interi che corrispondono ai protocolli IANA. |[] (tutti i protocolli)| Tutti i protocolli disponibili [qui](https://www.iana.org/assignments/protocol-numbers/protocol-numbers.xhtml) |

Di seguito è riportato un esempio di acquisizione di pacchetti usando una stringa di filtro. È possibile modificare i parametri in base alle esigenze in base alla tabella precedente.  

   ```azurepowershell-interactive
$filter="{`"TracingFlags`":11,`"MaxPacketBufferSize`":120,`"MaxFileSize`":500,`"Filters`":[{`"SourceSubnets`":[`"10.19.0.4/32`",`"10.20.0.4/32`"],`"DestinationSubnets`":[`"10.20.0.4/32`",`"10.19.0.4/32`"],`"TcpFlags`":9,`"CaptureSingleDirectionTrafficOnly`":true}]}"
Start-AzVpnConnectionPacketCapture -ResourceGroupName $rg -Name "<name of the VPN connection>" -ParentResourceName “<name of the Gateway>” -LinkConnection “<comma separated list of links>” -Sasurl $sasurl -FilterData $filter

Start-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name "<name of the Gateway>" -Sasurl $sasurl -FilterData $filter
   ```

## <a name="stopping-the-packet-capture"></a>Arresto dell'acquisizione di pacchetti
È consigliabile lasciare l'esecuzione dell'acquisizione pacchetti per almeno 600 secondi. A causa di problemi di sincronizzazione tra più componenti nel percorso, le acquisizioni di pacchetti più brevi potrebbero non fornire dati completi. Quando si è pronti per arrestare l'acquisizione di pacchetti, eseguire il comando seguente.

I parametri sono simili a quelli nella sezione Avvio di un'acquisizione di pacchetti. L'URL di firma di accesso condiviso è stato generato nella [sezione Creare un account di](#createstorage) archiviazione.

### <a name="gateway-level-packet-capture"></a>Acquisizione di pacchetti a livello di gateway

   ```azurepowershell-interactive
Stop-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name <GatewayName> -SasUrl $sas
   ```

### <a name="connection-level-packet-captures"></a>Acquisizioni di pacchetti a livello di connessione

   ```azurepowershell-interactive
Stop-AzVpnConnectionPacketCapture -ResourceGroupName $rg -Name <name of the VPN connection> -ParentResourceName "<name of VPN Gateway>" -LinkConnectionName <comma separated list of links e.g. "link1,link2">-SasUrl $sas
   ```

## <a name="viewing-your-packet-capture"></a>Visualizzazione dell'acquisizione di pacchetti

In portale di Azure passare all'account di archiviazione creato in "Crea un account di archiviazione" Fare clic sul contenitore e scaricare il file. I file di dati di acquisizione pacchetti vengono generati in formato PCAP. È possibile usare Wireshark o un'altra applicazione comunemente disponibile per aprire i file PCAP.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla rete WAN virtuale, vedere:

> [!div class="nextstepaction"]
> * [Domande frequenti sulla rete WAN virtuale](virtual-wan-faq.md)