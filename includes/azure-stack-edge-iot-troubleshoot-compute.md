---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 03/02/2021
ms.openlocfilehash: 57415ec76a3e8d9fc3c160b47668d3419ff6ea5c
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103621992"
---
Usare le risposte di runtime dell'agente IoT Edge per risolvere gli errori correlati al calcolo. Ecco un elenco di risposte possibili:

* 200 - OK
* 400 - La configurazione della distribuzione è in formato non corretto o non valida.
* 417: il dispositivo non ha un set di configurazione della distribuzione.
* 412 - La versione dello schema nella configurazione della distribuzione non è valida.
* 406 - Il dispositivo è offline o non invia segnalazioni sullo stato.
* 500 - Si è verificato un errore nel runtime di IoT Edge.

Per ulteriori informazioni, vedere [IOT Edge Agent](../articles/iot-edge/iot-edge-runtime.md?preserve-view=true&view=iotedge-2018-06#iot-edge-agent).

Il seguente errore è correlato al servizio IoT Edge sul dispositivo Azure Stack Edge Pro.

### <a name="compute-modules-have-unknown-status-and-cant-be-used"></a>I moduli di calcolo presentano uno stato sconosciuto e non possono essere usati

#### <a name="error-description"></a>Descrizione errore

Tutti i moduli del dispositivo mostrano lo stato sconosciuto e non possono essere usati. Lo stato sconosciuto viene mantenuto in un riavvio.<!--Original Support ticket relates to trying to deploy a container app on a Hub. Based on the work item, I assume the error description should not be that specific, and that the error applies to Azure Stack Edge Devices, which is the focus of this troubleshooting.-->

#### <a name="suggested-solution"></a>Soluzione suggerita

Eliminare il servizio IoT Edge e quindi ridistribuire i moduli. Per ulteriori informazioni, vedere [Remove IOT Edge service](../articles/databox-online/azure-stack-edge-j-series-manage-compute.md#remove-iot-edge-service).


### <a name="modules-show-as-running-but-are-not-working"></a>I moduli vengono mostrati come in esecuzione, ma non funzionano

#### <a name="error-description"></a>Descrizione errore

Lo stato di runtime del modulo viene visualizzato come in esecuzione, ma i risultati previsti non vengono visualizzati. 

Questa condizione può essere dovuta a un problema relativo alla configurazione della route del modulo che non funziona o `edgehub` non esegue il routing dei messaggi come previsto. È possibile controllare i `edgehub` log. Se si verificano errori, ad esempio la mancata connessione al servizio hub Internet delle cose, il motivo più comune è che i problemi di connettività. È possibile che si verifichino problemi di connettività perché la porta AMPQ utilizzata come porta predefinita dal servizio hub Internet per la comunicazione è bloccata o il server proxy Web blocca tali messaggi.

#### <a name="suggested-solution"></a>Soluzione suggerita

Seguire questa procedura:
1. Per risolvere l'errore, passare alla risorsa hub Internet delle cose per il dispositivo e quindi selezionare il dispositivo perimetrale. 
1. Passare a **impostare i moduli > impostazioni di runtime**. 
1. Aggiungere la `Upstream protocol` variabile di ambiente e assegnarle un valore `AMQPWS` . I messaggi configurati in questo caso vengono inviati tramite WebSocket tramite la porta 443.

### <a name="modules-show-as-running-but-do-not-have-an-ip-assigned"></a>I moduli vengono visualizzati come in esecuzione, ma non è stato assegnato un indirizzo IP

#### <a name="error-description"></a>Descrizione errore

Lo stato di runtime del modulo viene visualizzato come in esecuzione, ma l'app in contenitori non dispone di un indirizzo IP assegnato. 

Questa condizione è dovuta al fatto che l'intervallo di indirizzi IP forniti per gli indirizzi IP del servizio esterno Kubernetes non è sufficiente. È necessario estendere questo intervallo per assicurarsi che vengano analizzati tutti i contenitori o le macchine virtuali distribuite.

#### <a name="suggested-solution"></a>Soluzione suggerita

Nell'interfaccia utente Web locale del dispositivo, seguire questa procedura:
1. Passare alla pagina **calcolo** . Selezionare la porta per cui è stata abilitata la rete di calcolo. 
1. Immettere un intervallo statico e contiguo di indirizzi IP per gli **indirizzi IP del servizio esterno Kubernetes**. È necessario 1 IP per il `edgehub` servizio. Inoltre, è necessario un IP per ogni modulo di IoT Edge e per ogni macchina virtuale che verrà distribuita. 
1. Selezionare **Applica**. L'intervallo di indirizzi IP modificati dovrebbe essere applicato immediatamente.

Per altre informazioni, vedere [modificare gli IP del servizio esterno per i contenitori](../articles/databox-online/azure-stack-edge-j-series-manage-compute.md#change-external-service-ips-for-containers).