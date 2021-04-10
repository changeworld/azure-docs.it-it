---
title: Come configurare la metrica JMX-monitoraggio di Azure Application Insights per Java
description: Configurare la raccolta di metriche JMX aggiuntive per l'agente Java Application Insights di monitoraggio di Azure
ms.topic: conceptual
ms.date: 03/16/2021
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 020278bf6e5a823f6b3caa22d03f4b5dd003c0d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104609610"
---
# <a name="configuring-jmx-metrics"></a>Configurazione delle metriche di JMX

Per impostazione predefinita, l'agente Java 3,0 di Application Insights raccoglie alcune metriche di JMX, ma in molti casi questa operazione non è sufficiente. In questo documento viene descritta l'opzione di configurazione JMX in dettagli.

## <a name="how-do-i-collect-additional-jmx-metrics"></a>Ricerca per categorie raccogliere altre metriche JMX?

La raccolta di metriche JMX può essere configurata aggiungendo una ```"jmxMetrics"``` sezione all'applicationinsights.jssu file. È possibile specificare il nome della metrica nel modo in cui si vuole che venga visualizzato in portale di Azure nella risorsa di Application Insights. È necessario definire il nome dell'oggetto e l'attributo per ogni metrica che si desidera raccogliere.

## <a name="how-do-i-know-what-metrics-are-available-to-configure"></a>Ricerca per categorie sapere quali metriche sono disponibili per la configurazione?

Che è stato inchiodato: è necessario essere a conoscenza dei nomi degli oggetti e degli attributi, tali proprietà sono diverse per le varie librerie, i Framework e i server applicazioni e spesso non sono ben documentate. Per ottenere i nomi e gli attributi degli oggetti, è necessario visualizzare l'albero MBean. Un MBean è un oggetto Java gestito, che può rappresentare un dispositivo, un'applicazione o una risorsa e dispone di un set di attributi. 

Per visualizzare le metriche disponibili ed esaminare le metriche disponibili, è consigliabile usare [Java Mission Control](https://www.oracle.com/java/technologies/jdk-mission-control.html).

### <a name="how-to-navigate-the-java-mission-control-to-get-to-the-right-metrics"></a>Come passare a Java Mission Control per ottenere le metriche appropriate?

Quando si esegue lo strumento Java Mission Control, è possibile selezionare JVM sul lato sinistro e fare clic sul processo pertinente nella scheda "JVM browser". Attendere fino a quando JMC carica il dashboard per il processo, selezionare la scheda ' MBean browser ' nella parte inferiore (vedere di seguito). Il JMC deve trovarsi nella stessa cartella del JVM e il processo o l'app deve essere operativo.

![Screenshot del browser JMC MBean](media/java-ipa/jmx/jmc-mbean-browser.png)

### <a name="how-to-get-to-the-metrics-i-want-and-the-necessary-attributes"></a>Come ottenere le metriche desiderate e gli attributi necessari?

Il browser MBean apre l'albero di MBean con l'elenco delle categorie che possono essere espanse. Selezionando una categoria a sinistra, viene aperto l'elenco degli attributi a destra. Di seguito è riportato un esempio di metrica, il nome dell'oggetto e gli attributi. Gli attributi possono essere annidati, come nell'esempio riportato di seguito.

![Screenshot dell'albero MBean JMC](media/java-ipa/jmx/jmc-metric-sample.png)

### <a name="configuration-example"></a>Esempio di configurazione

Dalla selezione, come illustrato nell'immagine precedente, consente di configurare alcune metriche. Il primo è un esempio di una metrica annidata `LastGcInfo` che dispone di diverse proprietà e si desidera acquisire `GcThreadCount` .

```json
"jmxMetrics": [
      {
        "name": "Demo - GC Thread Count",
        "objectName": "java.lang:type=GarbageCollector,name=PS MarkSweep",
        "attribute": "LastGcInfo.GcThreadCount"
      },
      {
        "name": "Demo - GC Collection Count",
        "objectName": "java.lang:type=GarbageCollector,name=PS MarkSweep",
        "attribute": "CollectionCount"
      },
      {
        "name": "Demo - Thread Count",
        "objectName": "java.lang:type=Threading",
        "attribute": "ThreadCount"
      }
],
```

### <a name="types-of-collected-metrics-and-available-configuration-options"></a>Tipi di metriche raccolte e opzioni di configurazione disponibili

Sono supportate le metriche JMX numeriche e booleane, mentre altri tipi non sono supportati e verranno ignorati. 

Attualmente, i caratteri jolly e gli attributi aggregati non sono supportati ed è per questo motivo che ogni coppia di attributi ' nome oggetto '/' attributo ' deve essere configurata separatamente. 


## <a name="where-do-i-find-the-jmx-metrics-in-application-insights"></a>Dove è possibile trovare le metriche di JMX in Application Insights?

Quando l'applicazione è in esecuzione e la metrica JMX viene raccolta, è possibile visualizzarla passando a portale di Azure e passare alla risorsa di Application Insights. Nella scheda metriche selezionare l'elenco a discesa, come illustrato di seguito, per visualizzare le metriche.

![Screenshot delle metriche nel portale](media/java-ipa/jmx/jmx-portal.png)
