---
title: Importare ed esportare un file di zona di dominio per DNS privato di Azure-interfaccia della riga di comando di Azure
titleSuffix: Azure DNS
description: Informazioni su come importare ed esportare un file di zona DNS in DNS privato di Azure usando l'interfaccia della riga di comando di Azure
services: dns
author: duongau
ms.service: dns
ms.date: 03/16/2021
ms.author: duau
ms.topic: how-to
ms.openlocfilehash: 2e5babb85dbf4aa7da707e22be5eeaf3ae89972d
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451052"
---
# <a name="import-and-export-a-private-dns-zone-file-for-azure-private-dns"></a>Importare ed esportare un file di zona DNS privato per DNS privato di Azure

Questo articolo spiega come importare ed esportare file di zona DNS per DNS di Azure usando l'interfaccia della riga di comando di Azure.

## <a name="introduction-to-dns-zone-migration"></a>Introduzione alla migrazione di zone DNS

Un file di zona DNS è un file di testo che contiene i dettagli di tutti i record DNS (Domain Name System) nella zona. Segue un formato standard, ideale per il trasferimento dei record DNS tra sistemi DNS. L'uso di un file di zona è un modo rapido, affidabile e pratico per trasferire una zona DNS all'interno o all'esterno di DNS di Azure.

Il servizio DNS privato di Azure supporta l'importazione e l'esportazione di file di zona usando l'interfaccia della riga di comando di Azure. L'importazione di file di zona **non** è attualmente supportata tramite Azure PowerShell o il portale di Azure.

L'interfaccia della riga di comando di Azure è uno strumento da riga di comando multipiattaforma usato per la gestione dei servizi di Azure. È disponibile per le piattaforme Windows, Mac e Linux nella [pagina di download di Azure](https://azure.microsoft.com/downloads/). Il supporto multipiattaforma è importante per importare ed esportare i file di zona, perché il software server più conosciuto, [BIND](https://www.isc.org/downloads/bind/), viene generalmente eseguito in Linux.

## <a name="obtain-your-existing-dns-zone-file"></a>Recupero del file di zona DNS esistente

Prima di importare un file di zona DNS in DNS di Azure, è necessario ottenere una copia del file di zona. L'origine di questo file dipende da dove è attualmente ospitata la zona DNS.

* Se la zona DNS è ospitata da un servizio partner, ad esempio un registrar, un provider di servizi di hosting DNS dedicato o un provider di servizi cloud alternativo, tale servizio deve fornire la possibilità di scaricare il file di zona DNS.
* Se la zona DNS è ospitata nel DNS di Windows, la cartella predefinita per i file di zona è **%systemroot%\system32\dns**. Il percorso completo di ogni file di zona viene visualizzato anche nella scheda **Generale** della console DNS.
* Se la zona DNS è ospitata con BIND, il percorso del file di zona per ogni zona è specificato nel file di configurazione di BIND **named.conf**.

## <a name="import-a-dns-zone-file-into-azure-private-dns"></a>Importare un file di zona DNS in DNS privato di Azure

L'importazione di un file di zona crea una nuova zona nel DNS privato di Azure se non ne esiste già una. Se la zona esiste già, i set di record nel file di zona devono essere uniti a quelli esistenti.

### <a name="merge-behavior"></a>Unione

* Per impostazione predefinita, i set di record nuovi ed esistenti vengono uniti. I record identici all'interno di un set di record unito vengono deduplicati.
* Quando i set di record sono uniti, viene usato il valore di durata (TTL) dei set di record preesistenti.
* I parametri di origine di autorità (SOA), tranne `host`, vengono sempre rilevati dal file di zona importato. Analogamente, per il set di record del server di nomi al vertice della zona viene sempre applicato il valore TTL dal file di zona importato.
* Un record CNAME importato non sostituisce un record CNAME esistente con lo stesso nome.  
* Quando si verifica un conflitto tra un record CNAME e a un altro record con lo stesso nome, ma di tipo diverso, indipendentemente dal fatto che sia nuovo o esistente viene mantenuto il record esistente. 

### <a name="additional-information-about-importing"></a>Altre informazioni sull'importazione

Le note seguenti forniscono altri dettagli tecnici sul processo di importazione di zone.

* La direttiva `$TTL` è facoltativa e supportata. Se non si specifica una direttiva `$TTL`, i record senza una TTL esplicita vengono importati e impostati su una TLL predefinita di 3600 secondi. Quando per due record nello stesso set di record sono specificati TTL diversi, viene usato il valore più basso.
* La direttiva `$ORIGIN` è facoltativa e supportata. Se `$ORIGIN` non è impostata, il valore predefinito usato è il nome della zona specificato nella riga di comando, con "." di terminazione.
* Le direttive `$INCLUDE` e `$GENERATE` non sono supportate.
* Questi tipi di record sono supportati: A, AAAA, CAA, CNAME, MX, NS, SOA, SRV e TXT.
* Il record SOA viene creato automaticamente da DNS di Azure quando si crea una zona. Quando si importa un file di zona, tutti i parametri SOA vengono rilevati dal file di zona *tranne* il parametro `host`. Questo parametro usa il valore fornito da DNS di Azure. Questo parametro deve infatti fare riferimento al server dei nomi primario fornito da DNS di Azure.
* Anche il set di record del server dei nomi al vertice della zona viene creato automaticamente da DNS di Azure quando si crea la zona. Viene importata solo la durata TTL di questo set di record. Questi record contengono i nomi dei server dei nomi forniti da DNS di Azure. I dati dei record non vengono sovrascritti dai valori contenuti nel file di zona importato.
* Durante l'anteprima pubblica, DNS di Azure supporta solo i record TXT a stringa singola. I record TXT multistringa vengono concatenati e troncati a 255 caratteri.

### <a name="cli-format-and-values"></a>Valori e formato dell'interfaccia della riga di comando

Il formato del comando dell'interfaccia della riga di comando di Azure per importare una zona DNS è:

```azurecli
az network private-dns zone import -g <resource group> -n <zone name> -f <zone file name>
```

Valori:

* `<resource group>` è il nome del gruppo di risorse per la zona in DNS di Azure.
* `<zone name>` è il nome della zona.
* `<zone file name>` è il nome/percorso del file di zona da importare.

Se una zona con questo nome non esiste nel gruppo di risorse, viene creata. Se la zona esiste già, i set di record importati vengono uniti con quelli esistenti.

### <a name="import-a-zone-file"></a>Importare un file di zona

Per importare un file di zona per la zona **contoso.com**:

1. È necessario creare un gruppo di risorse di Gestione risorse, se non ne è già disponibile uno.

    ```azurecli
    az group create --resource-group myresourcegroup -l westeurope
    ```

2. Per importare la zona **contoso.com** dal file **contoso.com.txt** in una nuova zona DNS nel gruppo di risorse **myresourcegroup**, eseguire il comando `az network private-dns zone import`.<BR>Questo comando carica il file di zona e lo analizza. Il comando esegue una serie di comandi nel servizio DNS di Azure per creare la zona e tutti i set di record nella zona. Il comando visualizza l'avanzamento nella finestra della console insieme agli errori o agli avvisi. Poiché i set di record vengono creati in serie, l'importazione di un file di zona di grandi dimensioni può richiedere alcuni minuti.

    ```azurecli
    az network private-dns zone import -g myresourcegroup -n contoso.com -f contoso.com.txt
    ```

### <a name="verify-the-zone"></a>Verificare la zona

Per verificare la zona DNS dopo aver importato il file, è possibile usare uno dei metodi seguenti:

* È possibile elencare i record usando il seguente comando dell'interfaccia della riga di comando di Azure:

    ```azurecli
    az network private-dns record-set list -g myresourcegroup -z contoso.com
    ```

* Per verificare la risoluzione dei nomi per i record, è possibile usare `nslookup` . Poiché la zona non è ancora stata delegata, è necessario specificare esplicitamente i server dei nomi DNS di Azure corretti. Il seguente esempio mostra come recuperare i nomi dei server dei nomi assegnati alla zona. Illustra anche come eseguire una query sul record "www" con `nslookup`.

## <a name="export-a-dns-zone-file-from-azure-dns"></a>Esportare un file di zona DNS da DNS di Azure

Il formato del comando dell'interfaccia della riga di comando di Azure per esportare una zona DNS è:

```azurecli
az network private-dns zone export -g <resource group> -n <zone name> -f <zone file name>
```

Valori:

* `<resource group>` è il nome del gruppo di risorse per la zona in DNS di Azure.
* `<zone name>` è il nome della zona.
* `<zone file name>` è il nome/percorso del file di zona da esportare.

Come per l'importazione di una zona, è necessario prima di tutto accedere, scegliere la sottoscrizione e configurare l'interfaccia della riga di comando di Azure per l'uso della modalità Resource Manager.

### <a name="to-export-a-zone-file"></a>Per esportare un file di zona:

Per esportare la zona **contoso.com** di DNS di Azure esistente nel gruppo di risorse **myresourcegroup** nel file **contoso.com.txt** nella cartella corrente, eseguire `azure network private-dns zone export`. Questo comando chiama il servizio DNS di Azure per enumerare i set di record nella zona ed esportare i risultati in un file di zona compatibile con BIND.

```azurecli
az network private-dns zone export -g myresourcegroup -n contoso.com -f contoso.com.txt
```

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [gestire record e set di record](./private-dns-getstarted-cli.md) nella zona DNS.
