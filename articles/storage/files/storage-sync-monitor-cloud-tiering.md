---
title: Monitorare Sincronizzazione file di Azure la suddivisione in livelli cloud | Microsoft Docs
description: Informazioni dettagliate sulle metriche da usare per monitorare i criteri di suddivisione in livelli nel cloud.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/4/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: c27916afb0d199bcb32db9d43202e552a4a04f53
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104593136"
---
# <a name="monitor-cloud-tiering"></a>Monitorare la suddivisione in livelli cloud
Esistono due modi per monitorare i criteri di suddivisione in livelli nel cloud: il pannello delle proprietà dell'endpoint server e monitoraggio di Azure.

## <a name="monitoring-via-server-endpoint"></a>Monitoraggio tramite endpoint server

Accedere al [portale di Azure](https://portal.azure.com/), quindi passare al pannello delle **proprietà dell'endpoint server** per l'endpoint server che si desidera monitorare e scorrere verso il basso fino alla sezione suddivisione in livelli nel cloud. 

![Screenshot della sezione di suddivisione in livelli nel cloud in proprietà endpoint server.](media/storage-sync-monitoring-cloud-tiering/cloud-tiering-monitoring-5.png)

Il **risparmio di spazio** è la quantità di spazio salvato abilitando la suddivisione in livelli nel cloud. Se le dimensioni del volume locale sono sufficientemente grandi da contenere tutti i dati, si otterrà un risparmio di spazio pari allo 0%. Se si ha uno 0% o una percentuale di risparmio di spazio ridotta, potrebbe indicare che la suddivisione in livelli cloud non è vantaggiosa per le dimensioni correnti della cache locale. 

**Frequenza riscontri cache** è la percentuale di file aperti nella cache locale. La percentuale di riscontri nella cache viene calcolata in base ai file aperti direttamente dalla cache/numero totale di file aperti. Una percentuale di riscontri nella cache pari al 100% indica che tutti i file aperti nell'ultima ora erano già nella cache locale. Se l'obiettivo è quello di ridurre l'uscita, questo indicherebbe che i criteri correnti funzionano correttamente.

> [!NOTE]
> I carichi di lavoro con modelli di accesso casuale hanno in genere tassi di riscontri nella cache inferiori. 

**Dimensioni totali (cloud)** è la dimensione dei file sincronizzati con il cloud. 

**Dimensioni cache (Server)** è la dimensione totale dei file nel server, scaricati e a livelli. In alcuni casi, la dimensione della cache può essere maggiore delle dimensioni totali dei file nel cloud. Le variabili come le dimensioni del cluster del volume nel server possono causare questa operazione. Se la dimensione della cache è maggiore di quella desiderata, provare ad aumentare i criteri di spazio disponibile nel volume. 

I **criteri di volume effettivi** vengono usati da sincronizzazione file di Azure per determinare la quantità di spazio disponibile da lasciare sul volume in cui si trova l'endpoint server. Quando nello stesso volume sono presenti più endpoint server, i criteri di spazio libero del volume più elevati tra gli endpoint server diventano i criteri di volume effettivi per tutti gli endpoint server in tale volume. Se, ad esempio, sono presenti due endpoint server nello stesso volume, uno con spazio disponibile sul volume pari al 30% e un altro con spazio disponibile del volume pari al 50%, il criterio di spazio disponibile del volume effettivo per entrambi gli endpoint server sarà 50%.

Lo spazio disponibile nel **volume corrente** è lo spazio disponibile nel volume attualmente disponibile nel server locale. Se si dispone di un'uscita elevata, ma è disponibile più spazio libero sul volume prima di avviare i criteri di spazio libero del volume, provare a disabilitare i criteri di data. Un altro problema potrebbe essere quello dei file attualmente a livelli, il file a cui si accede più di recente è maggiore dello spazio disponibile nel volume rimanente prima di avviare il criterio. In questo caso, è consigliabile aumentare le dimensioni del volume locale. 

## <a name="monitoring-via-azure-monitor"></a>Monitoraggio tramite monitoraggio di Azure

Passare al **servizio di sincronizzazione archiviazione** e selezionare **metriche** nella finestra di spostamento laterale. 

Sono disponibili tre metriche diverse che è possibile usare per monitorare l'uscita in modo specifico dalla suddivisione in livelli nel cloud:

- Dimensioni del richiamo di suddivisione in livelli nel cloud
    - Si tratta della dimensione totale dei dati richiamati in byte e può essere utilizzata per identificare la quantità di dati richiamati.
- Dimensioni del richiamo di suddivisione in livelli nel cloud per applicazione
    - Si tratta delle dimensioni totali dei dati richiamati in byte da un'applicazione e possono essere usati per identificare i dati che richiamano i dati.
- Velocità effettiva di richiamo di suddivisione in livelli nel cloud
    - Questo consente di misurare la velocità con cui i dati vengono richiamati in byte e devono essere usati in caso di problemi di prestazioni. 

Per essere più specifici su ciò che si vuole visualizzare nei grafici, provare a usare **Aggiungi filtro** e **applica suddivisione**.
 
Per informazioni dettagliate sui diversi tipi di metriche per Sincronizzazione file di Azure e su come usarli, vedere [monitorare sincronizzazione file di Azure](storage-sync-files-monitoring.md).

Per informazioni dettagliate su come usare le metriche, vedere la pagina relativa all' [Introduzione ad Azure Esplora metriche.](../../azure-monitor/essentials/metrics-getting-started.md)

Per modificare i criteri di suddivisione in livelli nel cloud, vedere scegliere i criteri di suddivisione in [livelli cloud](storage-sync-choose-cloud-tiering-policies.md).

## <a name="next-steps"></a>Passaggi successivi
* [Pianificazione per la distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md)