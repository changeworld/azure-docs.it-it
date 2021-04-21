---
title: Monitorare Sincronizzazione file di Azure cloud a livelli | Microsoft Docs
description: Informazioni dettagliate sulle metriche da usare per monitorare i criteri di cloud a livelli.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: cbdde16ff214077d7fe4814c8841d805fe05c37c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796862"
---
# <a name="monitor-cloud-tiering"></a>Monitorare il cloud a livelli
Esistono due modi per monitorare i criteri di cloud a livelli: il pannello delle proprietà dell'endpoint server e Monitoraggio di Azure.

## <a name="monitoring-via-server-endpoint"></a>Monitoraggio tramite endpoint server

Accedere al portale di Azure [,](https://portal.azure.com/)quindi passare al pannello delle proprietà dell'endpoint server per l'endpoint server che si vuole monitorare e scorrere verso il basso fino alla sezione cloud a livelli.  

![Screenshot della sezione cloud a livelli nelle proprietà dell'endpoint server.](media/storage-sync-monitoring-cloud-tiering/cloud-tiering-monitoring-5.png)

**Il risparmio di spazio** è la quantità di spazio risparmiata abilitando il cloud a livelli. Se le dimensioni del volume locale sono sufficientemente grandi da contenere tutti i dati, si avrà un risparmio di spazio dello 0%. Se si dispone di uno 0% o di una percentuale bassa di risparmio di spazio, potrebbe indicare che la distribuzione a livelli nel cloud non è vantaggiosa con le dimensioni correnti della cache locale. 

**La percentuale di riscontri** nella cache è la percentuale di file aperti nella cache locale. La frequenza di riscontri nella cache viene calcolata dai file aperti direttamente dalla cache o dal numero totale di file aperti. Una percentuale di riscontri nella cache del 100% indica che tutti i file aperti nell'ultima ora si trovavano già nella cache locale. Se l'obiettivo è ridurre il traffico in uscita, il criterio corrente funziona correttamente.

> [!NOTE]
> I carichi di lavoro con modelli di accesso casuale hanno in genere frequenze di riscontri nella cache inferiori. 

**Le dimensioni totali (cloud)** sono le dimensioni dei file sincronizzati con il cloud. 

**Dimensioni memorizzate nella cache (server)** è la dimensione totale dei file nel server, sia scaricati che a livelli. In alcuni casi, le dimensioni memorizzate nella cache possono essere maggiori delle dimensioni totali dei file nel cloud. Variabili come le dimensioni del cluster del volume nel server possono causare questo problema. Se le dimensioni memorizzate nella cache sono superiori a quelle desiderate, è consigliabile aumentare i criteri di spazio disponibile nel volume. 

**I criteri di volume** effettivi vengono usati Sincronizzazione file di Azure per determinare la quantità di spazio libero da lasciare nel volume in cui si trova l'endpoint server. Quando sono presenti più endpoint server nello stesso volume, i criteri di spazio libero più elevato tra gli endpoint server diventano i criteri di volume effettivi per tutti gli endpoint server in tale volume. Ad esempio, se sono presenti due endpoint server nello stesso volume, uno con il 30% di spazio disponibile nel volume e un altro con il 50% di spazio disponibile nel volume, i criteri di spazio libero del volume effettivo per entrambi gli endpoint server saranno il 50%.

**Lo spazio disponibile nel** volume corrente è lo spazio disponibile nel volume attualmente disponibile nel server locale. Se si ha un'uscita elevata ma è disponibile più spazio disponibile per il volume prima dell'avvio dei criteri di spazio libero del volume, è consigliabile disabilitare i criteri di data. Un altro problema potrebbe essere che, tra i file attualmente a livelli, il file a cui si accede più di recente è maggiore dello spazio disponibile del volume rimanente prima dell'avvio del criterio. In questo caso, è consigliabile aumentare le dimensioni del volume locale. 

## <a name="monitoring-via-azure-monitor"></a>Monitoraggio tramite Monitoraggio di Azure

Passare al servizio **di sincronizzazione archiviazione e** selezionare Metriche **nel** riquadro di spostamento laterale. 

Esistono tre metriche diverse che è possibile usare per monitorare l'uscita in modo specifico dal cloud a livelli:

- Dimensioni di richiamo a livelli cloud
    - Si tratta della dimensione totale dei dati richiamati in byte e può essere usata per identificare la quantità di dati richiamati.
- Dimensioni di richiamo a livelli cloud per applicazione
    - Si tratta della dimensione totale dei dati richiamati in byte da un'applicazione e può essere usata per identificare ciò che richiama i dati.
- Velocità effettiva di richiamo a livelli nel cloud
    - Questo misura la velocità di richiamo dei dati in byte e deve essere usata in caso di problemi di prestazioni. 

Per essere più specifici sugli elementi da visualizzare nei grafici, è consigliabile usare Aggiungi filtro **e** **Applica suddivisione.**
 
Per informazioni dettagliate sui diversi tipi di metriche Sincronizzazione file di Azure e su come usarle, vedere [Monitorare](file-sync-monitoring.md)Sincronizzazione file di Azure .

Per informazioni dettagliate su come usare le metriche, vedere [Introduzione ad Azure Esplora metriche.](../../azure-monitor/essentials/metrics-getting-started.md)

Per modificare i criteri di cloud a livelli, vedere Scegliere i criteri [di cloud a livelli.](file-sync-choose-cloud-tiering-policies.md)

## <a name="next-steps"></a>Passaggi successivi

* [Pianificazione per la distribuzione di Sincronizzazione file di Azure](file-sync-planning.md)