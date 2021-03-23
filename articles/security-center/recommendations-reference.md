---
title: Tabella di riferimento per tutte le raccomandazioni del Centro sicurezza di Azure
description: Questo articolo elenca le raccomandazioni sulla sicurezza del Centro sicurezza di Azure che consentono di rafforzare e proteggere le risorse.
author: memildin
ms.service: security-center
ms.topic: reference
ms.date: 03/22/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: 2d9f76bd3c6ad0060a90f52abba1c7378b310e67
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801483"
---
# <a name="security-recommendations---a-reference-guide"></a>Raccomandazioni sulla sicurezza: una guida di riferimento

Questo articolo elenca le raccomandazioni che è possibile visualizzare nel Centro sicurezza di Azure. Le raccomandazioni visualizzate all'interno dell'ambiente dipendono dalle risorse protette e dalla configurazione personalizzata.

Le raccomandazioni del Centro sicurezza sono basate sul [benchmark di sicurezza di Azure](../security/benchmarks/introduction.md). Azure Security Benchmark è il set di linee guida specifiche di Azure create da Microsoft per le procedure consigliate per la sicurezza e la conformità basate su framework di conformità comuni. Questo benchmark ampiamente rispettato si basa sui controlli di [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) e [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) con particolare attenzione alla sicurezza incentrata sul cloud.

Per informazioni su come rispondere a queste raccomandazioni, vedere [Correzione delle raccomandazioni nel Centro sicurezza di Azure](security-center-remediate-recommendations.md).

Il Punteggio sicuro si basa sul numero di raccomandazioni del Centro sicurezza completate. Per decidere quali raccomandazioni risolvere prima, esaminare la gravità di ciascuna di esse e il relativo impatto potenziale sul punteggio sicuro.

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

## <a name="iot-recommendations"></a><a name='recs-iot'></a>Suggerimenti per le cose

[!INCLUDE [asc-recs-iot](../../includes/asc-recs-iot.md)]

## <a name="networking-recommendations"></a><a name='recs-networking'></a>Raccomandazioni di rete

[!INCLUDE [asc-recs-networking](../../includes/asc-recs-networking.md)]

## <a name="deprecated-recommendations"></a>Raccomandazioni rimosse

|Recommendation|Descrizione e criteri correlati|Gravità|Correzione rapida abilitata? ([Altre informazioni](security-center-remediate-recommendations.md#quick-fix-remediation))|Tipo di risorsa|
|----|----|----|----|----|
|**L'accesso ai Servizi app deve essere limitato**|Limitare l'accesso a Servizi app modificando la configurazione di rete per negare il traffico in ingresso da intervalli troppo ampi.<br>(Criterio correlato: [Anteprima]: l'accesso ai Servizi app deve essere limitato)|Alto|N|Servizio app|
|**È consigliabile applicare la protezione avanzata alle regole per le applicazioni Web nei gruppi di sicurezza di rete IaaS**|Rafforzare il gruppo di sicurezza di rete (NSG) delle macchine virtuali che eseguono applicazioni Web, con regole NSG che sono eccessivamente permissive per quanto concerne le porte dell'applicazione Web.<br>(Criterio correlato: le regole per i gruppi di sicurezza di rete delle applicazioni Web in IaaS devono essere rafforzate)|Alto|N|Macchina virtuale|
|**I criteri di sicurezza pod devono essere definiti per ridurre il vettore di attacco rimuovendo i privilegi delle applicazioni non necessari (Anteprima)**|Consente di definire i criteri di sicurezza pod per ridurre il vettore di attacco rimuovendo i privilegi delle applicazioni non necessari. È consigliabile configurare i criteri di sicurezza pod per consentire ai pod di accedere solo alle risorse per cui sono autorizzati.<br>(Criterio correlato: [Anteprima]: i criteri di sicurezza pod devono essere definiti nei servizi Kubernetes)|Media|N|Calcolo delle risorse (contenitori)|
|**Install Azure Security Center for IoT security module to get more visibility into your IoT devices (Installare il modulo di protezione del Centro sicurezza di Azure per IoT per ottenere maggiore visibilità nei dispositivi IoT)**|Installare il modulo di protezione del Centro sicurezza di Azure per IoT per ottenere maggiore visibilità nei dispositivi IoT.|Basso|N|Dispositivo IoT|

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle raccomandazioni, vedere quanto segue:

- [Che cosa sono i criteri di sicurezza, le iniziative e le raccomandazioni?](security-policy-concept.md)
- [Esaminare le raccomandazioni sulla sicurezza](security-center-recommendations.md)
