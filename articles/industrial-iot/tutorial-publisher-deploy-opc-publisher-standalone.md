---
title: Distribuire Microsoft OPC Publisher
description: In questa esercitazione si apprenderà come distribuire il server di pubblicazione OPC in modalità autonoma.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: c82d15541459b5b482e427fc707b92755aa02c6c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104787688"
---
# <a name="tutorial-deploy-the-opc-publisher"></a>Esercitazione: distribuire il server di pubblicazione OPC

Publisher OPC è un prodotto Microsoft completamente supportato, sviluppato in modo aperto, che colma il divario tra le risorse industriali e il cloud Microsoft Azure. Questa operazione viene eseguita connettendosi alle risorse abilitate per OPC UA o al software di connettività industriale e pubblica i dati di telemetria nell' [Hub Azure](https://azure.microsoft.com/services/iot-hub/) Internet in diversi formati, tra cui IEC62541 OPC UA PubSub Standard Format (dalla versione 2,6 in poi).

Viene eseguito in [Azure IOT Edge](https://azure.microsoft.com/services/iot-edge/) come un modulo o in un Docker normale come contenitore. Poiché si avvale del [Runtime .NET multipiattaforma](https://docs.microsoft.com/dotnet/core/introduction), viene eseguito anche in modalità nativa in Linux e Windows 10.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Distribuire il server di pubblicazione OPC
> * Eseguire la versione rilasciata più recente del server di pubblicazione OPC come contenitore
> * Specificare le opzioni di creazione del contenitore nel portale di Azure

Se non si ha una sottoscrizione di Azure, creare un account di valutazione gratuito

## <a name="prerequisites"></a>Prerequisiti

- È necessario creare un hub Internet.
- È necessario creare un dispositivo IoT Edge

## <a name="deploy-the-opc-publisher-from-the-azure-marketplace"></a>Distribuire il server di pubblicazione OPC da Azure Marketplace

1. Selezionare la sottoscrizione di Azure da usare. Se non è disponibile alcuna sottoscrizione di Azure, è necessario crearne una.
2. Selezionare l'hub Internet delle cose a cui il server di pubblicazione OPC deve inviare dati. Se non è disponibile alcun Hub, è necessario crearne uno.
3. Selezionare il dispositivo IoT Edge in cui deve essere eseguito il server di pubblicazione OPC (oppure immettere un nome per un nuovo dispositivo IoT Edge da creare).
4. Fare clic su Crea. Viene visualizzata la pagina "imposta moduli nel dispositivo" per il dispositivo IoT Edge selezionato.
5. Fare clic su "OPCPublisher" per aprire la pagina "Update IoT Edge Module" del server di pubblicazione OPC e quindi selezionare "opzioni di creazione del contenitore".
6. Specificare le opzioni di creazione aggiuntive del contenitore in base all'utilizzo del server di pubblicazione OPC, vedere la sezione successiva.


### <a name="accessing-the-microsoft-container-registry-docker-containers-for-opc-publisher-manually"></a>Accesso manuale ai contenitori Docker di Microsoft Container Registry per il server di pubblicazione OPC

La versione rilasciata più recente del server di pubblicazione OPC può essere eseguita manualmente tramite:

```
docker run mcr.microsoft.com/iotedge/opc-publisher:latest <name>
```

Dove "Name" è il nome del contenitore.

## <a name="specifying-container-create-options-in-the-azure-portal"></a>Specifica delle opzioni di creazione del contenitore nel portale di Azure
Quando si distribuisce il server di pubblicazione OPC tramite il portale di Azure, è possibile specificare le opzioni di creazione del contenitore nella pagina Aggiorna modulo IoT Edge del server di pubblicazione OPC. Queste opzioni di creazione devono essere in formato JSON. Gli argomenti della riga di comando del server di pubblicazione OPC possono essere specificati tramite la chiave cmd, ad esempio:
```
"Cmd": [
    "--pf=./pn.json",
    "--aa"
],
```

Un set tipico di opzioni di creazione di contenitori di IoT Edge modulo per il server di pubblicazione OPC è il seguente:
```
{
    "Hostname": "opcpublisher",
    "Cmd": [
        "--pf=./pn.json",
        "--aa"
    ],
    "HostConfig": {
        "Binds": [
            "/iiotedge:/appdata"
        ]
    }
}
```

Con queste opzioni specificate, il file di configurazione viene letto da server di pubblicazione OPC `./pn.json` . La directory di lavoro del server di pubblicazione OPC è impostata su `/appdata` all'avvio, quindi il server di pubblicazione OPC leggerà il file `/appdata/pn.json` all'interno del contenitore docker. Il file di log del server di pubblicazione OPC verrà scritto `/appdata` in e la `CertificateStores` Directory (usata per i certificati OPC UA) verrà creata anche in questa directory. Per rendere questi file disponibili nell'host IoT Edge file system, la configurazione del contenitore richiede un volume di montaggio di binding. Il `/iiotedge:/appdata` Binding eseguirà il mapping della directory `/appdata` alla directory host, `/iiotedge` che verrà creata dal runtime di IOT Edge se non esiste già.
**Senza questo volume di montaggio di binding, tutti i file di configurazione del server di pubblicazione OPC andranno perduti quando il contenitore viene riavviato.**

Una connessione a un server OPC UA usando il nome host senza un server DNS configurato sulla rete può essere eseguita aggiungendo una `ExtraHosts` voce alla `HostConfig` sezione:

```
"HostConfig": {
    "ExtraHosts": [
        "opctestsvr:192.168.178.26"
    ]
}
```

## <a name="next-steps"></a>Passaggi successivi 
Ora che è stato distribuito il modulo OPC Publisher Edge, il passaggio successivo consiste nel configurarlo:

> [!div class="nextstepaction"]
> [Configurare il server di pubblicazione OPC](tutorial-publisher-configure-opc-publisher.md)