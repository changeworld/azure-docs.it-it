---
title: Visualizzazione di più risorse in Esplora metriche
description: Informazioni su come visualizzare più risorse in monitoraggio di Azure Esplora metriche
author: ritaroloff
services: azure-monitor
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: riroloff
ms.subservice: metrics
ms.openlocfilehash: 9d1460a8bebe75a3ee471eb304fcf642d566b5dd
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97614643"
---
# <a name="viewing-multiple-resources-in-metrics-explorer"></a>Visualizzazione di più risorse in Esplora metriche

La selezione dell'ambito delle risorse consente di visualizzare le metriche tra più risorse che si trovano nella stessa sottoscrizione e nella stessa area. Di seguito sono riportate le istruzioni su come visualizzare più risorse in monitoraggio di Azure Esplora metriche. 

## <a name="selecting-a-resource"></a>Selezione di una risorsa 

Selezionare **Metriche** dal menu **Monitoraggio di Azure** o dalla sezione **Monitoraggio** del menu di una risorsa. Fare clic sul pulsante "selezionare un ambito" per aprire la selezione dell'ambito delle risorse, che consente di selezionare le risorse per cui si vogliono visualizzare le metriche. Questa operazione dovrebbe essere già popolata se si apre Esplora metriche dal menu di una risorsa. 

![Screenshot della selezione dell'ambito delle risorse evidenziato in rosso](./media/metrics-charts/019.png)

## <a name="selecting-multiple-resources"></a>Selezione di più risorse 

Alcuni tipi di risorse hanno permesso di eseguire query per le metriche su più risorse, purché si trovino nella stessa sottoscrizione e nella stessa posizione. Questi tipi di risorse sono disponibili nella parte superiore dell'elenco a discesa "tipi di risorse". 

![Screenshot che mostra un elenco a discesa di risorse compatibili con più risorse ](./media/metrics-charts/020.png)

> [!WARNING] 
> Per visualizzare le metriche tra più risorse, gruppi di risorse o una sottoscrizione, è necessario disporre dell'autorizzazione di lettura monitoraggio a livello di sottoscrizione. A tale scopo, seguire le istruzioni riportate in [questo documento](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

Per visualizzare le metriche su più risorse, iniziare selezionando più risorse all'interno della selezione dell'ambito delle risorse. 

![Screenshot che illustra come selezionare più risorse](./media/metrics-charts/021.png)

> [!NOTE]
> È possibile selezionare più risorse solo all'interno dello stesso tipo di risorsa, posizione e sottoscrizione. Le risorse esterne a questo criterio non saranno selezionabili. 

Al termine della selezione, fare clic sul pulsante "applica" per salvare la selezione. 

## <a name="selecting-a-resource-group-or-subscription"></a>Selezione di un gruppo di risorse o di una sottoscrizione 

> [!WARNING]
> Per visualizzare le metriche tra più risorse, gruppi di risorse o una sottoscrizione, è necessario disporre dell'autorizzazione di lettura monitoraggio a livello di sottoscrizione. 

Per i tipi compatibili con più risorse, è anche possibile eseguire una query per le metriche in una sottoscrizione o in più gruppi di risorse. Per iniziare, selezionare una sottoscrizione o uno o più gruppi di risorse: 

![Screenshot che illustra come eseguire query su più gruppi di risorse ](./media/metrics-charts/022.png)

Sarà quindi necessario selezionare un tipo di risorsa e un percorso per poter continuare ad applicare il nuovo ambito. 

![Screenshot che mostra i gruppi di risorse selezionati ](./media/metrics-charts/023.png)

È anche possibile espandere gli ambiti selezionati per verificare le risorse a cui si applicherà.

![Screenshot che mostra le risorse selezionate all'interno dei gruppi ](./media/metrics-charts/024.png)

Al termine della selezione degli ambiti, fare clic su "applica" per salvare le selezioni. 

## <a name="splitting-and-filtering-by-resource-group-or-resources"></a>Suddivisione e filtro per gruppo di risorse o risorse

Dopo aver tracciato le risorse, è possibile usare lo strumento di suddivisione e filtro per ottenere maggiori informazioni sui dati. 

La suddivisione consente di visualizzare il modo in cui i diversi segmenti della metrica si confrontano tra loro. Ad esempio, quando si traccia una metrica per più risorse, è possibile usare lo strumento "applica suddivisione" per dividere per ID risorsa o gruppo di risorse. Ciò consentirà di confrontare facilmente una singola metrica tra più risorse o gruppi di risorse.  

Ad esempio, di seguito è riportato un grafico della percentuale di CPU tra 9VMs. Con la suddivisione in base all'ID di risorsa, è possibile vedere in che modo la percentuale di CPU è diversa per ogni macchina virtuale. 

![Screenshot che illustra come è possibile usare la suddivisione per visualizzare la percentuale di CPU per macchina virtuale](./media/metrics-charts/026.png)

Oltre alla suddivisione, è possibile usare la funzionalità di filtro per visualizzare solo i gruppi di risorse che si desidera visualizzare.  Se ad esempio si vuole visualizzare la percentuale di CPU per le macchine virtuali per un determinato gruppo di risorse, è possibile usare lo strumento "Aggiungi filtro" per filtrare in base al gruppo di risorse. In questo esempio viene filtrato in base a TailspinToysDemo, che rimuove le metriche associate alle risorse in TailspinToys. 

![Screenshot che Mostra come filtrare in base al gruppo di risorse](./media/metrics-charts/027.png)

## <a name="pinning-your-multi-resource-charts"></a>Aggiunta di grafici a più risorse 

> [!WARNING] 
> Per visualizzare le metriche tra più risorse, gruppi di risorse o una sottoscrizione, è necessario disporre dell'autorizzazione di lettura monitoraggio a livello di sottoscrizione. A tale scopo, seguire le istruzioni riportate in [questo documento](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal). 

Per aggiungere il grafico a più risorse, seguire le istruzioni riportate [qui](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts#pin-charts-to-dashboards). 

## <a name="next-steps"></a>Passaggi successivi

* [Risoluzione dei problemi di Esplora metriche](metrics-troubleshoot.md)
* [Elenco di metriche disponibili per i servizi di Azure](metrics-supported.md)
* [Esempi di grafici configurati](metric-chart-samples.md)

