---
title: Opzioni di conversione di Progettazione viste di monitoraggio di Azure in cartelle di lavoro
description: Opzioni di conversione per la transizione da viste a cartelle di lavoro di monitoraggio di Azure.
author: austonli
ms.author: aul
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: b8b6b8b41c729c3cbb6cf4589d679e93149e5314
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102043406"
---
# <a name="azure-monitor-view-designer-to-workbooks-conversion-options"></a>Opzioni di conversione di Progettazione viste di monitoraggio di Azure in cartelle di lavoro
[Progettazione](view-designer.md) viste è una funzionalità di monitoraggio di Azure che consente di creare visualizzazioni personalizzate che consentono di visualizzare i dati nell'area di lavoro log Analytics, con grafici, elenchi e sequenze temporali. Vengono eliminati e sostituiti con cartelle di lavoro che forniscono funzionalità aggiuntive. In questo articolo vengono confrontati i concetti fondamentali tra le due opzioni e per la conversione delle visualizzazioni in cartelle di lavoro.

## <a name="basic-workbook-designs"></a>Progettazioni di cartelle di lavoro di base

Progettazione viste presenta uno stile statico fisso di rappresentazione, mentre le cartelle di lavoro consentono di includere e modificare il modo in cui i dati vengono rappresentati. Le immagini seguenti illustrano due esempi di come è possibile disporre le cartelle di lavoro durante la conversione delle visualizzazioni.

[Cartella di lavoro verticale](view-designer-conversion-examples.md#vertical) 
 ![ Verticale](media/view-designer-conversion-options/view-designer-vertical.png)

[Cartella di lavoro](view-designer-conversion-examples.md#tabbed) 
 ![ a schede Scheda distribuzione tipo di dati scheda ](media/view-designer-conversion-options/distribution-tab.png)
 ![ tipi di dati nel tempo](media/view-designer-conversion-options/over-time-tab.png)

## <a name="tile-conversion"></a>Conversione di sezioni
Progettazione viste usa la funzionalità Panoramica del riquadro per rappresentare e riepilogare lo stato generale. Questi sono rappresentati in sette riquadri, che variano da numeri a grafici. Nelle cartelle di lavoro, gli utenti possono creare visualizzazioni simili e aggiungerle allo stile originale dei riquadri di panoramica. 

![Raccolta](media/view-designer-conversion-options/overview.png)


## <a name="view-dashboard-conversion"></a>Visualizza la conversione del dashboard
I riquadri di Progettazione viste sono costituiti in genere da due sezioni, una visualizzazione e un elenco che corrisponde ai dati della visualizzazione, ad esempio il riquadro dell' **elenco di & ad anello** .

![Grafico ad anello](media/view-designer-conversion-options/donut-example.png)

Con le cartelle di lavoro di, si consente all'utente di scegliere di eseguire una query su una o entrambe le sezioni della vista. La formulazione di query nelle cartelle di lavoro di è un semplice processo in due passaggi. In primo luogo, i dati vengono generati dalla query e in secondo luogo viene eseguito il rendering dei dati come una visualizzazione.  Di seguito è riportato un esempio di come verrà ricreata questa vista nelle cartelle di lavoro:

![Conversione](media/view-designer-conversion-options/convert-donut.png)


## <a name="next-steps"></a>Passaggi successivi
- [Accesso alle cartelle di lavoro & autorizzazioni](view-designer-conversion-access.md)