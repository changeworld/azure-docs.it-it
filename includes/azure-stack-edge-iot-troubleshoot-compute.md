---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 03/23/2021
ms.openlocfilehash: 0d912d0ac3f0fcf4c52116e67909038a1973304b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105105444"
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

Eliminare il servizio IoT Edge e quindi ridistribuire i moduli. Per ulteriori informazioni, vedere [Remove IOT Edge service](../articles/databox-online/azure-stack-edge-gpu-manage-compute.md#remove-iot-edge-service).


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

Per altre informazioni, vedere [modificare gli IP del servizio esterno per i contenitori](../articles/databox-online/azure-stack-edge-gpu-manage-compute.md#change-external-service-ips-for-containers).

### <a name="configure-static-ips-for-iot-edge-modules"></a>Configurare gli indirizzi IP statici per i moduli IoT Edge

#### <a name="problem-description"></a>Descrizione del problema

Kubernetes assegna indirizzi IP dinamici a ogni modulo IoT Edge sul dispositivo GPU di Azure Stack Edge Pro. È necessario un metodo per configurare gli indirizzi IP statici per i moduli.

#### <a name="suggested-solution"></a>Soluzione suggerita

È possibile specificare indirizzi IP fissi per i moduli di IoT Edge tramite la sezione K8s-experimental, come descritto di seguito: 

```yaml
{
  "k8s-experimental": {
    "serviceOptions" : {
      "loadBalancerIP" : "100.23.201.78",
      "type" : "LoadBalancer"
    }
  }
}
```
### <a name="expose-kubernetes-service-as-cluster-ip-service-for-internal-communication"></a>Esporre il servizio Kubernetes come servizio IP del cluster per la comunicazione interna

#### <a name="problem-description"></a>Descrizione del problema

Per impostazione predefinita, il tipo di servizio Internet delle cose è di tipo Load Balancer ed è stato assegnato un indirizzo IP esterno. Potrebbe non essere necessario un indirizzo IP esterno per l'applicazione. Potrebbe essere necessario esporre i pod all'interno del cluster KUbernetes per l'accesso come altri pod e non come servizio di bilanciamento del carico esposto esternamente. 

#### <a name="suggested-solution"></a>Soluzione suggerita

È possibile usare le opzioni create tramite la sezione K8s-experimental. L'opzione di servizio seguente dovrebbe funzionare con i binding di porta.

```yaml
{
"k8s-experimental": {
  "serviceOptions" : {
    "type" : "ClusterIP"
    }
  }
}
```