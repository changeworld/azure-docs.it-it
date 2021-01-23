---
title: Visualizzare più risorse in Esplora metriche di Azure
description: Informazioni su come visualizzare più risorse usando Esplora metriche di Azure.
author: ritaroloff
services: azure-monitor
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: riroloff
ms.subservice: metrics
ms.openlocfilehash: 5f5f75e63a0b201144329a6e2b7c0d503de54692
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736474"
---
# <a name="view-multiple-resources-in-the-azure-metrics-explorer"></a>Visualizzare più risorse in Esplora metriche di Azure

La selezione dell'ambito delle risorse consente di visualizzare le metriche tra più risorse che si trovano nella stessa sottoscrizione e nella stessa area. Questo articolo illustra come visualizzare più risorse usando la funzionalità Esplora metriche di Azure di monitoraggio di Azure. 

## <a name="select-a-resource"></a>Selezionare una risorsa 

Selezionare **Metriche** dal menu **Monitoraggio di Azure** o dalla sezione **Monitoraggio** del menu di una risorsa. Scegliere quindi **selezionare un ambito** per aprire la selezione ambito. 

Usare selezione ambito per selezionare le risorse di cui si desidera visualizzare le metriche. L'ambito deve essere popolato se si apre Esplora metriche dal menu di una risorsa. 

![Screenshot che illustra come aprire la selezione dell'ambito delle risorse.](./media/metrics-charts/019.png)

## <a name="select-multiple-resources"></a>Selezionare più risorse 

Alcuni tipi di risorse possono eseguire query per le metriche su più risorse. Le metriche devono trovarsi nella stessa sottoscrizione e nella stessa località. Trovare questi tipi di risorse nella parte superiore del menu **tipi di risorse** .

![Screenshot che mostra un menu di risorse compatibili con più risorse.](./media/metrics-charts/020.png)

> [!WARNING] 
> Per visualizzare le metriche tra più risorse, gruppi di risorse o una sottoscrizione, è necessario disporre dell'autorizzazione di lettura monitoraggio a livello di sottoscrizione. Per altre informazioni, vedere [aggiungere o rimuovere assegnazioni di ruolo di Azure usando il portale di Azure](../../role-based-access-control/role-assignments-portal.md).

Per visualizzare le metriche su più risorse, iniziare selezionando più risorse all'interno della selezione dell'ambito delle risorse. 

![Screenshot che illustra come selezionare più risorse.](./media/metrics-charts/021.png)

> [!NOTE]
> Le risorse selezionate devono trovarsi all'interno dello stesso tipo di risorsa, posizione e sottoscrizione. Le risorse che non soddisfano questi criteri non sono selezionabili. 

Al termine, scegliere **applica** per salvare le selezioni. 

## <a name="select-a-resource-group-or-subscription"></a>Selezionare un gruppo di risorse o una sottoscrizione 

> [!WARNING]
> Per visualizzare le metriche tra più risorse, gruppi di risorse o una sottoscrizione, è necessario disporre dell'autorizzazione di lettura monitoraggio a livello di sottoscrizione. 

Per i tipi compatibili con più risorse, è possibile eseguire una query per le metriche in una sottoscrizione o in più gruppi di risorse. Per iniziare, selezionare una sottoscrizione o uno o più gruppi di risorse: 

![Screenshot che illustra come eseguire una query su più gruppi di risorse.](./media/metrics-charts/022.png)

Selezionare un tipo di risorsa e un percorso. 

![Screenshot che mostra i gruppi di risorse selezionati.](./media/metrics-charts/023.png)

È possibile espandere gli ambiti selezionati per verificare le risorse alle quali si applicano le selezioni.

![Screenshot che mostra le risorse selezionate all'interno dei gruppi.](./media/metrics-charts/024.png)

Al termine della selezione degli ambiti, selezionare **applica**. 

## <a name="split-and-filter-by-resource-group-or-resources"></a>Suddividere e filtrare per gruppo di risorse o risorse

Dopo aver tracciato le risorse, è possibile usare la suddivisione e il filtro per ottenere maggiori informazioni sui dati. 

La suddivisione consente di visualizzare il modo in cui i diversi segmenti della metrica si confrontano tra loro. Ad esempio, quando si traccia una metrica per più risorse, è possibile scegliere **applica suddivisione** per dividere per ID risorsa o gruppo di risorse. La divisione consente di confrontare una singola metrica tra più risorse o gruppi di risorse.  

Ad esempio, il grafico seguente mostra la percentuale di CPU tra nove macchine virtuali. Quando si divide per ID di risorsa, viene visualizzato il modo in cui la percentuale di CPU differisce per macchina virtuale. 

![Screenshot che illustra come usare la suddivisione per visualizzare la percentuale di CPU tra le macchine virtuali.](./media/metrics-charts/026.png)

Insieme alla suddivisione, è possibile usare il filtro per visualizzare solo i gruppi di risorse che si desidera visualizzare.  Ad esempio, per visualizzare la percentuale di CPU per le macchine virtuali per un determinato gruppo di risorse, è possibile selezionare **Aggiungi filtro** per filtrare in base al gruppo di risorse. 

In questo esempio viene filtrato in base a TailspinToysDemo. In questo caso il filtro rimuove le metriche associate alle risorse in TailspinToys. 

![Screenshot che illustra come filtrare in base al gruppo di risorse.](./media/metrics-charts/027.png)

## <a name="pin-multiple-resource-charts"></a>Aggiungere grafici a più risorse 

Per i grafici a più risorse che visualizzano le metriche tra gruppi di risorse e sottoscrizioni, è necessario che l'utente disponga dell'autorizzazione *monitoraggio lettura* a livello di sottoscrizione. Assicurarsi che tutti gli utenti dei dashboard a cui vengono aggiunti i grafici a più risorse dispongano di autorizzazioni sufficienti. Per altre informazioni, vedere [aggiungere o rimuovere assegnazioni di ruolo di Azure usando il portale di Azure](../../role-based-access-control/role-assignments-portal.md).

Per aggiungere il grafico a più risorse a un dashboard, vedere [aggiunta ai dashboard](./metrics-charts.md#pinning-to-dashboards). 

## <a name="next-steps"></a>Passaggi successivi

* [Risolvere i problemi di Esplora metriche](metrics-troubleshoot.md)
* [Elenco di metriche disponibili per i servizi di Azure](metrics-supported.md)
* [Esempi di grafici configurati](metric-chart-samples.md)