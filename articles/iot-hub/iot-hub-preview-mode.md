---
title: Attivare la modalità di anteprima per l'hub Azure.
description: Informazioni su come attivare la modalità di anteprima per l'hub Internet delle cose, i motivi per cui si desidera e alcuni avvisi
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: jlian
ms.openlocfilehash: 5299a049ab2cc2246a861dd3fb81f466b56afad3
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2020
ms.locfileid: "96603488"
---
# <a name="turn-on-preview-mode-for-iot-hub-to-try-select-new-features"></a>Attivare la modalità di anteprima per l'hub delle cose per provare a selezionare nuove funzionalità

<!-- 
- We are working hard to bring you new features
- Some of these features require a brand new iot hub with preview mode on
- some features may not work at all or have unexpected behavior
- "Normal preview features" do NOT require preview mode 
- Support opt-in at creation time only
- Customer cannot opt back out post creation
- If customer wants to evaluate, they must use new hub dedicated for the preview
- Banners, documentations and all materials indicate preview quality: no GA guarantee at all
-->

Le nuove funzionalità sono disponibili in anteprima pubblica per l'hub Internet, tra cui:

- MQTT 5
- Certificato server ECC
- Negoziazione della lunghezza del frammento TLS
- Supporto della catena di CA X509 per HTTPS/WebSocket

Queste funzionalità sono miglioramenti al protocollo dell'hub Internet e ai livelli di autenticazione, quindi sono disponibili solo per il **nuovo** hub Internet. *Non* sono ancora disponibili per gli hub Internet esistenti. Per visualizzare l'anteprima di queste funzionalità, è necessario creare l'hub Internet con la modalità di anteprima attivata.

## <a name="turn-on-preview-mode-for-a-new-iot-hub"></a>Attivare la modalità di anteprima per un nuovo hub Internet

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Nella home page di Azure selezionare il pulsante **+ Crea una risorsa** e quindi immettere *Hub IoT* nel campo **Cerca nel Marketplace**.

1. Nei risultati della ricerca selezionare **Hub IoT** e quindi fare clic su **Crea**.

1. Nella scheda **nozioni di base** , completare i campi [normalmente](iot-hub-create-through-portal.md) , ad eccezione di **Region**. Selezionare una delle aree seguenti:
    
    - Stati Uniti centrali
    - Europa occidentale
    - Asia sud-orientale

1. Selezionare la scheda **gestione** , quindi espandere la sezione **Impostazioni avanzate** .

1. Accanto a **modalità anteprima** selezionare **on**. Esaminare attentamente il testo dell'avviso.

    :::image type="content" source="media/iot-hub-preview-mode/turn-on-preview-mode-at-create.png" alt-text="Immagine che mostra la posizione in cui selezionare l'opzione della modalità di anteprima quando si crea un nuovo hub":::

1. Selezionare **Avanti: verifica + crea**, quindi **Crea**.

Una volta creato, un hub di Internet delle cose in modalità di anteprima Visualizza sempre questo banner, per informare che è possibile usare questo hub Internet per l'anteprima solo a scopo di anteprima: 

:::image type="content" source="media/iot-hub-preview-mode/banner.png" alt-text="Immagine che mostra il banner per la modalità anteprima Hub":::

## <a name="using-an-iot-hub-in-preview-mode"></a>Uso di un hub Internet in modalità anteprima

*Non* usare un hub Internet delle cose in modalità di anteprima per la produzione. La modalità di anteprima è destinata *solo* all'anteprima delle funzionalità di selezione elencate all'inizio di questa pagina. Alcune altre limitazioni alla modalità di anteprima dell'hub Internet sono

- Alcune funzionalità dell'hub Internet esistente quali collegamento privato, identità gestita, flussi di dispositivi e failover possono funzionare in modo imprevisto o meno.
- Un hub Internet delle cose in modalità di anteprima non può essere modificato o aggiornato a un hub delle cose normali.
- Non è possibile garantire il [contratto di contratto](https://azure.microsoft.com/support/legal/sla/iot-hub/v1_2/) con l'hub delle cose normali, non usare per la produzione.

> [!TIP]
> La modalità di anteprima non è necessaria per i [flussi del dispositivo](iot-hub-device-streams-overview.md) e la [traccia distribuita](iot-hub-distributed-tracing.md). Per usare queste funzionalità di anteprima precedenti, seguire la relativa documentazione come di consueto. 

## <a name="next-steps"></a>Passaggi successivi

- Per visualizzare l'anteprima del supporto di MQTT 5, vedere Panoramica del supporto per l' [Hub MQTT 5 (anteprima)](iot-hub-mqtt-5.md)
- Per visualizzare l'anteprima del certificato del server ECC, vedere [certificato TLS del server di crittografia a curva ellittica (ecc) (anteprima)](iot-hub-tls-support.md#elliptic-curve-cryptography-ecc-server-tls-certificate-preview)
- Per visualizzare l'anteprima della negoziazione delle dimensioni del frammento TLS, vedere [TLS max Fragment Length negotiation (Preview)](iot-hub-tls-support.md#tls-maximum-fragment-length-negotiation-preview)