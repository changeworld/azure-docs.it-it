---
title: Esegui rilevamento anomalie su IoT Edge
titleSuffix: Azure Cognitive Services
description: Distribuire il modulo di rilevamento anomalie per IoT Edge.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 12/03/2020
ms.author: mbullwin
ms.openlocfilehash: b4153b07b153a9ee0b16dc032ab5e7810e236d7d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98936271"
---
# <a name="deploy-an-anomaly-detector-module-to-iot-edge"></a>Distribuire un modulo di rilevamento anomalie per IoT Edge

Informazioni su come distribuire il modulo [Rilevamento anomalie](../anomaly-detector-container-howto.md) dei servizi cognitivi in un dispositivo IOT Edge. Una volta distribuito in IoT Edge, il modulo viene eseguito in IoT Edge insieme ad altri moduli come istanze di contenitore. Espone esattamente le stesse API di un'istanza del contenitore del rilevatore di anomalie in esecuzione in un ambiente contenitore Docker standard. 

## <a name="prerequisites"></a>Prerequisiti

* Usare una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free) prima di iniziare.
* Installare l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).
* Un [Hub](../../../iot-hub/iot-hub-create-through-portal.md) e un dispositivo [IOT Edge](../../../iot-edge/quickstart-linux.md) .

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

## <a name="deploy-the-anomaly-detection-module-to-the-edge"></a>Distribuire il modulo di rilevamento anomalie sul perimetro

1. Nel portale di Azure, immettere il **rilevatore di anomalie sulla IOT Edge** nella ricerca e aprire il risultato di Azure Marketplace.
2. Verrà visualizzata la [pagina dispositivi di destinazione portale di Azure per IOT Edge modulo](https://portal.azure.com/#create/azure-cognitive-service.edge-anomaly-detector). Specificare le informazioni obbligatorie seguenti.

    1. Selezionare la propria sottoscrizione.

    1. Selezionare l'hub Internet.

    1. Selezionare **trova dispositivo** e trovare un dispositivo IOT Edge.

3. Selezionare il pulsante **Crea**.

4. Selezionare il modulo **AnomalyDetectoronIoTEdge** .

    :::image type="content" source="../media/deploy-anomaly-detection-on-iot-edge/iot-edge-modules.png" alt-text="Immagine dell'interfaccia utente IoT Edge modules con il collegamento AnomalyDetectoronIoTEdge evidenziato con una casella rossa per indicare che si tratta dell'elemento da selezionare.":::

5. Passare a **Variabili di ambiente** e specificare le informazioni seguenti.

    1.  Lasciare il valore accetto per **Condizioni di licenza**.

    1. Per **Fatturazione** immettere l'endpoint di Servizi cognitivi.

    1. Per **Chiave API** immettere la chiave API di Servizi cognitivi.

    :::image type="content" source="../media/deploy-anomaly-detection-on-iot-edge/environment-variables.png" alt-text="Variabili di ambiente con caselle rosse intorno alle aree che necessitano di valori da compilare per l'endpoint e la chiave API":::

6. Fare clic su **Update** (Aggiorna).

7. Selezionare **Avanti: Route** per definire la route. Tutti i messaggi da tutti i moduli devono essere inviati all'hub IoT di Azure.

8. Selezionare **Avanti: Rivedi e crea**. È possibile visualizzare in anteprima il file JSON che definisce tutti i moduli che vengono distribuiti nel dispositivo IoT Edge.
    
9. Selezionare **Crea** per avviare la distribuzione del modulo.

10. Al termine della distribuzione del modulo, si tornerà alla pagina IoT Edge dell'hub IoT. Selezionare il dispositivo nell'elenco dei dispositivi IoT Edge per visualizzarne i dettagli.

11. Scorrere verso il basso e visualizzare i moduli elencati. Verificare che lo stato di Runtime sia in esecuzione per il nuovo modulo. 

Per risolvere i problemi relativi allo stato di runtime del dispositivo IoT Edge, consultare la [Guida alla risoluzione dei problemi](../../../iot-edge/troubleshoot.md)

## <a name="test-anomaly-detector-on-an-iot-edge-device"></a>Rilevamento anomalie di test in un dispositivo IoT Edge

Si effettuerà una chiamata HTTP al dispositivo Azure IoT Edge in cui è in esecuzione il contenitore di Servizi cognitivi di Azure. Il contenitore fornisce API endpoint basate su REST. Usare l'host, `http://<your-edge-device-ipaddress>:5000` , per le API del modulo.

Se il dispositivo perimetrale non consente ancora la comunicazione in ingresso sulla porta 5000, sarà necessario creare una nuova **regola di porta in ingresso**. 

Per una macchina virtuale di Azure, questa impostazione può essere impostata in impostazioni **macchina virtuale**  >    >  **rete**  >  **regola porta in ingresso** regola  >  **Aggiungi regola porta in ingresso**.

Esistono diversi modi per verificare che il modulo sia in esecuzione. Individuare l'indirizzo *IP esterno* e la porta esposta del dispositivo perimetrale in questione e aprire il Web browser preferito. Usare i vari URL di richiesta indicati di seguito per verificare che il contenitore sia in esecuzione. Gli URL di richiesta di esempio elencati `http://<your-edge-device-ipaddress:5000` di seguito sono, ma il contenitore specifico può variare. Tenere presente che è necessario usare l'indirizzo *IP esterno* del dispositivo perimetrale.

| URL della richiesta | Scopo |
|:-------------|:---------|
| `http://<your-edge-device-ipaddress>:5000/` | Il contenitore fornisce un home page. |
| `http://<your-edge-device-ipaddress>:5000/status` | Richiesto anche con GET, verifica se la chiave API usata per avviare il contenitore è valida senza causare una query dell'endpoint. Questa richiesta può essere usata per i [probe di attività e di idoneità](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) di Kubernetes. |
| `http://<your-edge-device-ipaddress>:5000/swagger` | Il contenitore fornisce un set completo di documentazione per gli endpoint e una funzionalità **Prova**. Con questa funzionalità, è possibile immettere le impostazioni in un modulo HTML basato sul Web ed eseguire la query senza scrivere codice. Dopo che la query restituisce il risultato, viene fornito un comando CURL di esempio per illustrare il formato richiesto per il corpo e le intestazioni HTTP. |

![Home page del contenitore](../../../../includes/media/cognitive-services-containers-api-documentation/container-webpage.png)

## <a name="next-steps"></a>Passaggi successivi

* Esaminare [installare ed eseguire i contenitori](../anomaly-detector-container-configuration.md) per il pull dell'immagine del contenitore ed eseguire il contenitore
* Rivedere [Configurare i contenitori](../anomaly-detector-container-configuration.md) per informazioni sulle impostazioni di configurazione.
* [Altre informazioni sul servizio API del rilevatore di anomalie](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
