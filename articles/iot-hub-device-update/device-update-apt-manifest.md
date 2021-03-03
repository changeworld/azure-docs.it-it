---
title: Informazioni sull'aggiornamento del dispositivo per il manifesto APT di hub Azure Microsoft Docs
description: Informazioni su come l'aggiornamento dei dispositivi per l'hub Internet usa il manifesto apt per un aggiornamento basato su pacchetti.
author: vimeht
ms.author: vimeht
ms.date: 2/17/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 0b68b78499aa3bf0d84d8bd0fa5ab55d1f969113
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679589"
---
# <a name="device-update-apt-manifest"></a>Manifesto APT aggiornamento dispositivo

Il manifesto APT è un file JSON che descrive i dettagli dell'aggiornamento richiesti dal gestore di aggiornamento APT. Questo file può essere importato nell'aggiornamento del dispositivo per l'hub Internet in modo analogo a qualsiasi altro aggiornamento.

[Altre](import-update.md) informazioni sull'importazione di aggiornamenti nell'aggiornamento del dispositivo.

## <a name="overview"></a>Panoramica

Quando un manifesto APT viene recapitato a un agente di aggiornamento del dispositivo come aggiornamento, l'agente elaborerà il manifesto ed effettuerà le operazioni necessarie. Queste operazioni includono il download e l'installazione dei pacchetti specificati nel file manifesto APT e le relative dipendenze.

L'aggiornamento del dispositivo supporta il gestore APT UpdateType e APT Update. Questo supporto consente all'agente di aggiornamento del dispositivo di valutare i pacchetti Debian installati e aggiornare i pacchetti necessari. 

## <a name="schema"></a>SCHEMA

Un file manifesto APT è un file JSON con uno schema con versione.

```json
{
    "name": "<name>",
    "version": "<version>",
    "packages": [
        {
            "name": "<package name>",
            "version": "<version specifier>"
        }
    ]
}
```

Esempio:

```json
{
    "name": "contoso-iot-edge",
    "version": "1.0.0.0",
    "packages": [
        {
            "name" : "thermocontrol",
            "version" : "1.0.1"
        },
        {
            "name" : "tempreport",
            "version" : "2.0.0"
        }
    ]
}
```

### <a name="name"></a>name

Nome del manifesto APT. Questo può essere qualsiasi nome o ID è significativo per gli scenari. Ad esempio: `contoso-iot-edge`.

### <a name="version"></a>version

Numero di versione del manifesto APT. Ad esempio: `1.0.0.0`.


### <a name="packages"></a>packages

Elenco di oggetti contenenti proprietà specifiche del pacchetto.

#### <a name="name"></a>name

Nome o ID del pacchetto. Ad esempio: `iotedge`.

#### <a name="version"></a>version

Criteri di versione desiderati per il pacchetto. Ad esempio: `1.0.8-2`.

Attualmente è supportato solo il numero di versione esatto. Il numero di versione è la versione del pacchetto Debian desiderata nel formato [Epoch:] upstream_version [-debian_revision].

**Epoch** è un int senza segno.

**upstream_version** possono includere caratteri alfanumerici e caratteri quali ".", "+", "-" e "~". Deve iniziare con una cifra.
> [!NOTE]
> ' 1.0.8' è uguale a' 1.0.8-0'

Ad esempio, **`"name":"iotedge" and "version":"1.0.8-2"`** equivale a installare un pacchetto usando il comando `apt-get install iotedge=1.0.8-2`

> [!NOTE]
> Il valore della versione non contiene un segno di uguale

Se la versione viene omessa, verrà installata la versione disponibile più recente del pacchetto specificato.

[Altre](https://www.debian.org/doc/debian-policy/ch-controlfields.html#s-f-version) informazioni sul controllo delle versioni dei pacchetti Debian.

> [!NOTE]
> Gestione pacchetti APT ignora i requisiti di controllo delle versioni forniti da un pacchetto quando i pacchetti dipendenti da installare vengono risolti automaticamente. A meno che non vengano concesse versioni esplicite di pacchetti dipendenti, utilizzeranno la versione più recente anche se il pacchetto stesso può specificare un requisito rigoroso (=) in una determinata versione. Questa risoluzione automatica può causare errori relativi a una dipendenza non soddisfatta. [Altre informazioni](https://unix.stackexchange.com/questions/350192/apt-get-not-properly-resolving-a-dependency-on-a-fixed-version-in-a-debian-ubunt)

Se si sta aggiornando una versione specifica del daemon di sicurezza di Azure IoT Edge, è necessario includere la versione desiderata del `iotedge` pacchetto e il relativo `libiothsm-std` pacchetto dipendente nel manifesto apt.
[Altre informazioni](https://docs.microsoft.com/azure/iot-edge/how-to-update-iot-edge#update-the-security-daemon)

> [!NOTE]
> Un manifesto apt può essere usato per aggiornare l'agente di aggiornamento del dispositivo e le relative dipendenze. Elencare il nome dell'agente di aggiornamento del dispositivo e la versione desiderata nel manifesto apt, come per qualsiasi altro pacchetto. Questo manifesto apt può quindi essere importato e distribuito tramite l'aggiornamento del dispositivo per la pipeline dell'hub Internet. 

## <a name="removing-packages"></a>Rimozione di pacchetti

È anche possibile usare un manifesto apt per rimuovere i pacchetti installati dal dispositivo. È possibile utilizzare un singolo manifesto apt per rimuovere, aggiungere e aggiornare più pacchetti. Per rimuovere un pacchetto, aggiungere un segno meno "-" dopo il nome del pacchetto. Non è necessario includere un numero di versione per i pacchetti da rimuovere. La rimozione di pacchetti tramite un manifesto apt non comporta la rimozione delle relative dipendenze e configurazioni.

Esempio:

```json
{
    "name": "contoso-video",
    "version": "2.0.0.1",
    "packages": [
        {
            "name" : "foo-"
        }
    ]
}
```
Questo manifesto apt rimuoverà il pacchetto "foo" dal dispositivo o dai dispositivi in cui viene distribuito. 

## <a name="recommended-value-for-installed-criteria"></a>Valore consigliato per i criteri di installazione

I criteri installati per un manifesto APT sono `<name>-<version>` Where `<name>` è il nome del manifesto apt ed `<version>` è la versione del manifesto apt. Ad esempio: `contoso-iot-edge-1.0.0.0`. 

## <a name="guidelines-on-creating-an-apt-manifest"></a>Linee guida per la creazione di un manifesto APT

Quando si crea il manifesto APT, è necessario tenere presenti alcune linee guida:

- Assicurarsi sempre che il manifesto APT sia un file JSON ben formato
- Ogni manifesto APT deve avere una versione univoca. Provare a usare una metodologia standardizzata per incrementare la versione del manifesto APT, in modo che abbia senso per gli scenari e possa essere facilmente seguita
- Quando si tratta dello stato desiderato di ogni singolo pacchetto, specificare il nome e la versione esatti del pacchetto che si vuole installare nel dispositivo. Convalidare sempre i valori rispetto al repository del pacchetto che si desidera utilizzare come origine per il pacchetto
- Verificare che i pacchetti nel manifesto APT siano elencati nell'ordine in cui devono essere installati o rimossi
- Convalidare sempre l'installazione dei pacchetti in un dispositivo di test per assicurarsi che il risultato sia necessario
- Quando si installa una versione specifica di un pacchetto (ad esempio,), è consigliabile che `iotedge 1.0.9-1` nel manifesto apt siano installate anche le versioni esplicite dei pacchetti dipendenti (ad esempio, `libiothsm 1.0.9-1` )
- Sebbene non sia obbligatorio, assicurarsi sempre che il manifesto APT sia cumulativo per evitare che il dispositivo sia in uno stato sconosciuto. Un aggiornamento cumulativo garantisce che i dispositivi abbiano la versione desiderata di ogni pacchetto di cui si è interessati anche se il dispositivo ha ignorato una distribuzione degli aggiornamenti APT a causa di un errore di installazione o se è stato portato offline

Ad esempio:

**Manifesto APT di base**

```JSON
{
    "name": "contoso-iot-edge",
    "version": "1.0",
    "packages": [
        {
            "name": "foo",
            "version": "1.0.1"
        }
    ]
}
```

**AGGIORNAMENTO NON VALIDO**

Questo aggiornamento include il pacchetto a barre, ma non il pacchetto foo.

```JSON
{
    "name": "contoso-iot-edge",
    "version": "2.0",
    "packages": [
        {
            "name": "bar",
            "version": "3.0.2"
        }
    ]
}
```

**AGGIORNAMENTO VALIDO**

Questo aggiornamento include il pacchetto foo e include anche il pacchetto a barre.

```JSON
{
    "name": "contoso-iot-edge",
    "version": "2.0",
    "packages": [
        {
            "name": "foo",
            "version": "1.0.1"
        },
        {
            "name": "bar",
            "version": "3.0.2"
        }
    ]
}
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Importa nuovo aggiornamento](import-update.md)

