---
title: 'Avvio rapido: Esaminare le raccomandazioni sulla sicurezza'
description: Esaminare le raccomandazioni sulla sicurezza con il servizio di sicurezza Defender per IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 66b5694823096f0747ea099b631a53198a819816
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/31/2020
ms.locfileid: "97835041"
---
# <a name="quickstart-investigate-security-recommendations"></a>Avvio rapido: Esaminare le raccomandazioni sulla sicurezza


L'analisi tempestiva e la mitigazione delle raccomandazioni di Defender per IoT sono l'approccio ottimale per migliorare il comportamento di sicurezza e ridurre la superficie di attacco in una soluzione IoT.

In questa guida di avvio rapido si esamineranno le informazioni disponibili in ogni raccomandazione sulla sicurezza per IoT e verrà illustrato come eseguire il drill-down e usare i dettagli di ogni raccomandazione e dei dispositivi correlati per ridurre il rischio.

A questo punto, procedere con l'esercitazione.

## <a name="investigate-new-recommendations"></a>Esaminare le nuove raccomandazioni

Nell'elenco delle raccomandazioni dell'hub IoT vengono visualizzate tutte le raccomandazioni sulla sicurezza aggregate per l'hub IoT.

1.  Nel portale di Azure aprire l'**hub IoT** di cui si vogliono esaminare le nuove raccomandazioni.

1.  Scegliere **Raccomandazioni** dal menu **Sicurezza**. Verranno visualizzate tutte le raccomandazioni sulla sicurezza per l'hub IoT, con quelle delle ultime 24 ore contrassegnate con il flag **Nuova**. 

    :::image type="content" source="media/quickstart/investigate-security-recommendations-expanded.png#lightbox" alt-text="Esaminare le raccomandazioni sulla sicurezza con il Centro sicurezza di Azure per IoT](media/quickstart/investigate-security-recommendations-inline.png)":::


1.  Selezionare e aprire qualsiasi raccomandazione nell'elenco per visualizzarne i dettagli ed eseguire il drill-down sulle specifiche.

## <a name="security-recommendation-details"></a>Dettagli delle raccomandazioni sulla sicurezza

Aprire ogni raccomandazione aggregata per visualizzare la relativa descrizione dettagliata, la procedura di correzione e l'ID di ogni dispositivo che ha attivato la raccomandazione. Sono riportati anche la gravità della raccomandazione e l'accesso per l'indagine diretta con Log Analytics.

1.  Selezionare e aprire una qualsiasi raccomandazione sulla sicurezza da **Hub IoT** \> **Sicurezza** \> **Raccomandazioni**.

1.  Esaminare la **descrizione**, la **gravità** e i **dettagli dispositivo** di tutti i dispositivi che hanno emesso questa raccomandazione nel periodo di aggregazione. 

1.  Dopo aver esaminato le specifiche della raccomandazione, usare le istruzioni della **procedura di correzione manuale** per correggere e risolvere il problema che ha generato la raccomandazione. 

    [ :::image type="content" source="media/quickstart/remediate-security-recommendations-expanded.png#lightbox" alt-text="Correggere le raccomandazioni sulla sicurezza con il Centro sicurezza di Azure per IoT](media/quickstart/remediate-security-recommendations-inline.png)":::


1.  Esplorare i dettagli delle raccomandazioni per un dispositivo specifico selezionando il dispositivo desiderato nella pagina di drill-down.

    [ :::image type="content" source="media/quickstart/explore-security-recommendation-detail-expanded.png#lightbox" alt-text="Esaminare le raccomandazioni sulla sicurezza specifiche per un dispositivo con il Centro sicurezza di Azure per IoT](media/quickstart/explore-security-recommendation-detail-inline.png)":::


1.  Se sono necessarie ulteriori indagini, **esaminare la raccomandazione in Log Analytics** usando l'apposito collegamento. 


## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come creare avvisi personalizzati, passare all'articolo successivo:

> [!div class="nextstepaction"]
> [Creare avvisi personalizzati](quickstart-create-custom-alerts.md)
