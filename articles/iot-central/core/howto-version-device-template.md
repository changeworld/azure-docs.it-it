---
title: Informazioni sulle versioni dei modelli di dispositivo per le app Azure IoT Central | Microsoft Docs
description: Eseguire iterazioni con i modelli di dispositivo creando nuove versioni, senza determinare conseguenze per i dispositivi collegati
author: dominicbetts
ms.author: dobett
ms.date: 11/06/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 03d4b0e43c9f692b90f4ab5d4d136dac92b74de6
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107715047"
---
# <a name="create-a-new-device-template-version"></a>Creare una nuova versione di un modello di dispositivo

*Le informazioni di questo articolo sono destinate a generatori di soluzioni e sviluppatori di dispositivi.*

Un modello di dispositivo include uno schema che descrive il modo in cui un dispositivo interagisce con IoT Central. Queste interazioni includono telemetria, proprietà e comandi. Il dispositivo e l'applicazione IoT Central si basano su una conoscenza condivisa di questo schema per lo scambio di informazioni. È possibile apportare modifiche limitate allo schema solo senza interrompere il contratto, per questo motivo la maggior parte delle modifiche dello schema richiede una nuova versione del modello di dispositivo. Il controllo delle versioni del modello di dispositivo consente ai dispositivi meno recenti di continuare con la versione dello schema che comprendono, mentre i dispositivi più recenti o aggiornati usano una versione dello schema successiva.

Lo schema in un modello di dispositivo è definito nel modello di dispositivo e nelle relative interfacce. I modelli di dispositivo includono altre informazioni, ad esempio proprietà cloud, personalizzazioni della visualizzazione e visualizzazioni. Se si apportano modifiche a tali parti del modello di dispositivo che non definiscono il modo in cui il dispositivo scambia i dati con IoT Central, non è necessario creare una versione del modello.

È sempre necessario pubblicare un modello di dispositivo aggiornato prima che un operatore possa usarlo. IoT Central impedisce di pubblicare modifiche di rilievo in un modello di dispositivo senza prima eseguire il controllo delle versioni del modello.

> [!NOTE]
> Per altre informazioni su come creare un modello di dispositivo, vedere [Configurare e gestire un modello di dispositivo](howto-set-up-template.md)

## <a name="versioning-rules"></a>Regole di controllo delle versioni

Questa sezione riepiloga le regole di controllo delle versioni che si applicano ai modelli di dispositivo. Sia i modelli di dispositivo che le interfacce hanno numeri di versione. Il frammento di codice seguente mostra il modello di dispositivo per un dispositivo termostato. Il modello di dispositivo ha una singola interfaccia. È possibile visualizzare il numero di versione alla fine del `@id` campo.

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    // ...
  ]
}
```

Per visualizzare queste informazioni nell'interfaccia IoT Central, selezionare **Visualizza** identità nell'editor dei modelli di dispositivo:

:::image type="content" source="media/howto-version-device-template/view-identity.png" alt-text="Visualizzare l'identità di un'interfaccia per visualizzare il numero di versione":::

Nell'elenco seguente vengono illustrate le regole che determinano quando è necessario creare una nuova versione:

* Dopo la pubblicazione di un modello di dispositivo, non è possibile rimuovere alcuna interfaccia, neanche in una nuova versione del modello di dispositivo.
* Dopo la pubblicazione di un modello di dispositivo, è possibile aggiungere un'interfaccia se si crea una nuova versione del modello di dispositivo.
* Dopo la pubblicazione di un modello di dispositivo, è possibile sostituire un'interfaccia con una versione più recente se si crea una nuova versione del modello di dispositivo. Ad esempio, se il modello di dispositivo sensor v1 usa l'interfaccia thermostat v1, è possibile creare un modello di dispositivo sensor v2 che usa l'interfaccia thermostat v2.
* Dopo la pubblicazione di un'interfaccia, non è possibile rimuovere il contenuto dell'interfaccia, neanche in una nuova versione del modello di dispositivo.
* Dopo la pubblicazione di un'interfaccia, è possibile aggiungere elementi al contenuto di un'interfaccia se si crea una nuova versione dell'interfaccia e del modello di dispositivo. Gli elementi che è possibile aggiungere all'interfaccia includono dati di telemetria, proprietà e comandi.
* Dopo la pubblicazione di un'interfaccia, è possibile apportare modifiche non dello schema agli elementi esistenti nell'interfaccia se si crea una nuova versione dell'interfaccia e del modello di dispositivo. Le parti non dello schema di un elemento dell'interfaccia includono il nome visualizzato e il tipo semantico. Le parti dello schema di un elemento di interfaccia che non è possibile modificare sono il nome, il tipo di funzionalità e lo schema.

Le sezioni seguenti illustrano alcuni esempi di modifica dei modelli di dispositivo in IoT Central.

## <a name="customize-the-device-template-without-versioning"></a>Personalizzare il modello di dispositivo senza controllo delle versioni

Alcuni elementi delle funzionalità del dispositivo possono essere modificati senza dover eseguire la versione del modello di dispositivo e delle interfacce. Ad esempio, alcuni di questi campi includono nome visualizzato, tipo semantico, valore minimo, valore massimo, cifre decimali, colore, unità, unità di visualizzazione, commento e descrizione. Per aggiungere una di queste personalizzazioni:

1. Passare alla **pagina Modelli di** dispositivo.
1. Selezionare il modello di dispositivo da personalizzare.
1. Scegliere la **scheda Personalizza.**
1. Tutte le funzionalità definite nel modello di dispositivo sono elencate qui. È possibile modificare, salvare e usare tutti questi campi senza dover eseguire la versione del modello di dispositivo. Se sono presenti campi da modificare di sola lettura, è necessario creare una versione del modello di dispositivo per modificarli. Selezionare un campo da modificare e immettere eventuali nuovi valori.
1. Selezionare **Salva**. A questo punto questi valori eseguono l'override di tutto ciò che è stato inizialmente salvato nel modello di dispositivo e che vengono usati nell'applicazione.

## <a name="version-a-device-template"></a>Creare la versione di un modello di dispositivo

La creazione di una nuova versione del modello di dispositivo crea una versione bozza del modello in cui è possibile modificare il modello di dispositivo. Tutte le interfacce pubblicate rimangono pubblicate fino a quando non vengono singolarmente con controllo delle versioni. Per modificare un'interfaccia pubblicata, creare prima una nuova versione del modello di dispositivo.

Eseguire la versione del modello di dispositivo solo quando si tenta di modificare una parte del modello di dispositivo che non è possibile modificare nella sezione delle personalizzazioni.

Per creare una versione di un modello di dispositivo:

1. Passare alla pagina **Modelli di** dispositivo.
1. Selezionare il modello di dispositivo di cui si sta tentando di eseguire la versione.
1. Selezionare il **pulsante** Versione nella parte superiore della pagina e assegnare un nuovo nome al modello. IoT Central suggerisce un nuovo nome, che è possibile modificare.
1. Selezionare **Crea**.
1. Il modello di dispositivo è ora in modalità bozza. È possibile vedere che le interfacce sono ancora bloccate. Creare una versione delle interfacce che si desidera modificare.

## <a name="version-an-interface"></a>Creare la versione di un'interfaccia

Il controllo delle versioni di un'interfaccia consente di aggiungere e aggiornare funzionalità all'interno dell'interfaccia già creata.

Per eseguire il controllo delle versioni di un'interfaccia:

1. Passare alla pagina **Modelli di** dispositivo.
1. Selezionare il modello di dispositivo in modalità bozza.
1. Selezionare l'interfaccia in modalità pubblicata di cui si vuole eseguire la versione e la modifica.
1. Selezionare il **pulsante** Versione nella parte superiore della pagina dell'interfaccia.
1. Selezionare **Crea**.
1. L'interfaccia è ora in modalità bozza. È possibile aggiungere o modificare funzionalità all'interfaccia senza interrompere le personalizzazioni e le visualizzazioni esistenti.

## <a name="migrate-a-device-across-versions"></a>Eseguire la migrazione di un dispositivo tra versioni diverse

È possibile creare più versioni del modello di dispositivo. Nel corso del tempo, si hanno più dispositivi connessi usando questi modelli di dispositivo. È possibile eseguire la migrazione dei dispositivi da una versione del modello di dispositivo a un'altra. I passaggi seguenti descrivono come eseguire la migrazione di un dispositivo:

1. Andare alla pagina **Dispositivi**.
1. Selezionare il dispositivo di cui eseguire la migrazione a un'altra versione.
1. Scegliere **Esegui migrazione:**  :::image type="content" source="media/howto-version-device-template/migrate-device.png" alt-text="scegliere l'opzione per avviare la migrazione di un dispositivo":::
1. Selezionare il modello di dispositivo con il numero di versione a cui si vuole eseguire la migrazione del dispositivo e selezionare **Esegui migrazione**.

## <a name="next-steps"></a>Passaggi successivi

Se si è un operatore o un generatore di soluzioni, un passaggio successivo consigliato consiste nell'apprendere come [gestire i dispositivi.](./howto-manage-devices.md)

Se si è uno sviluppatore di dispositivi, un passaggio successivo consigliato consiste nel leggere le informazioni Azure IoT Edge [dispositivi e Azure IoT Central](./concepts-iot-edge.md).
