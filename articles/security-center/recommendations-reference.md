---
title: Tabella di riferimento per tutte le raccomandazioni del Centro sicurezza di Azure
description: Questo articolo elenca le Centro sicurezza di Azure sulla sicurezza che consentono di proteggere le risorse in modo più rigido.
author: memildin
ms.service: security-center
ms.topic: reference
ms.date: 04/06/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: e994aead1840fd3ef9b57e92cf95e94837608d7a
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719134"
---
# <a name="security-recommendations---a-reference-guide"></a>Raccomandazioni sulla sicurezza: una guida di riferimento

Questo articolo elenca le raccomandazioni che è possibile visualizzare nel Centro sicurezza di Azure. Le raccomandazioni visualizzate all'interno dell'ambiente dipendono dalle risorse protette e dalla configurazione personalizzata.

Le raccomandazioni del Centro sicurezza si basano su [Azure Security Benchmark.](https://docs.microsoft.com/security/benchmark/azure/introduction)
Azure Security Benchmark è il set di linee guida specifiche di Azure create da Microsoft per le procedure consigliate per la sicurezza e la conformità basate su framework di conformità comuni. Questo benchmark ampiamente rispettato si basa sui controlli del [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) e del National Institute of Standards and Technology [(NIST)](https://www.nist.gov/) con particolare attenzione alla sicurezza incentrata sul cloud.

Per informazioni su come rispondere a queste raccomandazioni, vedere [Correzione delle raccomandazioni nel Centro sicurezza di Azure](security-center-remediate-recommendations.md).

Il punteggio di sicurezza si basa sul numero di raccomandazioni del Centro sicurezza completate. Per decidere quali raccomandazioni risolvere per prime, esaminare la gravità di ognuna e il potenziale impatto sul punteggio di sicurezza.

> [!TIP]
> Se la descrizione di una raccomandazione mostra "No related policy" (Nessun criterio correlato), è in genere dovuto al fatto che la raccomandazione dipende da una raccomandazione diversa e dai _relativi_ criteri. Ad esempio, la raccomandazione "È consigliabile correggere gli errori di integrità di Endpoint Protection nei set di scalabilità di macchine virtuali", si basa sulla raccomandazione che controlla se una soluzione di Endpoint Protection è anche _installata_ ("La soluzione Endpoint Protection deve essere installata nei set di scalabilità di macchine virtuali"). La raccomandazione sottostante _dispone_ di un criterio.
> Limitando i criteri solamente alla raccomandazione fondamentale semplifica la gestione dei criteri.

## <a name="appservices-recommendations"></a><a name='recs-appservices'></a>Raccomandazioni per AppServices

[!INCLUDE [asc-recs-appservices](../../includes/asc-recs-appservices.md)]

## <a name="compute-recommendations"></a><a name='recs-compute'></a>Raccomandazioni per le istanze di calcolo

[!INCLUDE [asc-recs-compute](../../includes/asc-recs-compute.md)]

## <a name="container-recommendations"></a><a name='recs-container'></a>Consigli per i contenitori

[!INCLUDE [asc-recs-container](../../includes/asc-recs-container.md)]

## <a name="data-recommendations"></a><a name='recs-data'></a>Raccomandazioni per i dati

[!INCLUDE [asc-recs-data](../../includes/asc-recs-data.md)]

## <a name="identityandaccess-recommendations"></a><a name='recs-identityandaccess'></a>Raccomandazioni per IdentityAndAccess

[!INCLUDE [asc-recs-identityandaccess](../../includes/asc-recs-identityandaccess.md)]

## <a name="iot-recommendations"></a><a name='recs-iot'></a>Raccomandazioni IoT

[!INCLUDE [asc-recs-iot](../../includes/asc-recs-iot.md)]

## <a name="networking-recommendations"></a><a name='recs-networking'></a>Raccomandazioni di rete

[!INCLUDE [asc-recs-networking](../../includes/asc-recs-networking.md)]

## <a name="deprecated-recommendations"></a>Raccomandazioni rimosse

|Recommendation|Descrizione e criteri correlati|Gravità|
|----|----|----|
|L'accesso ai Servizi app deve essere limitato|Limitare l'accesso a Servizi app modificando la configurazione di rete per negare il traffico in ingresso da intervalli troppo ampi.<br>(Criterio correlato: [Anteprima]: l'accesso ai Servizi app deve essere limitato)|Alto|
|È consigliabile applicare la protezione avanzata alle regole per le applicazioni Web nei gruppi di sicurezza di rete IaaS|Harden the network security group (NSG) of your virtual machines that are running web applications, with NSG rules that are overly permissive with regard to web application ports.<br>(Criterio correlato: le regole per i gruppi di sicurezza di rete delle applicazioni Web in IaaS devono essere rafforzate)|Alto|
|I criteri di sicurezza pod devono essere definiti per ridurre il vettore di attacco rimuovendo i privilegi delle applicazioni non necessari (Anteprima)|Consente di definire i criteri di sicurezza pod per ridurre il vettore di attacco rimuovendo i privilegi delle applicazioni non necessari. È consigliabile configurare i criteri di sicurezza pod per consentire ai pod di accedere solo alle risorse per cui sono autorizzati.<br>(Criterio correlato: [Anteprima]: i criteri di sicurezza pod devono essere definiti nei servizi Kubernetes)|Media|
|Install Azure Security Center for IoT security module to get more visibility into your IoT devices (Installare il modulo di protezione del Centro sicurezza di Azure per IoT per ottenere maggiore visibilità nei dispositivi IoT)|Installare il modulo di protezione del Centro sicurezza di Azure per IoT per ottenere maggiore visibilità nei dispositivi IoT.|Basso|
|È consigliabile riavviare i computer per applicare gli aggiornamenti del sistema|Riavviare i computer per applicare gli aggiornamenti del sistema e proteggere i computer dalle vulnerabilità. (Criterio correlato: gli aggiornamenti di sistema devono essere installati nelle macchine)|Medio|
| È necessario installare l'agente di monitoraggio nei computer|Questa azione installa un agente di monitoraggio nelle macchine virtuali selezionate. Selezionare un'area di lavoro a cui l'agente deve inviare report. (Nessun criterio correlato)|Alto|
||||

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle raccomandazioni, vedere quanto segue:

- [Che cosa sono i criteri di sicurezza, le iniziative e le raccomandazioni?](security-policy-concept.md)
- [Esaminare le raccomandazioni sulla sicurezza](security-center-recommendations.md)
