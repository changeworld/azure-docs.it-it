---
title: Individuare la strategia di ancoraggio
description: Informazioni sulle diverse strategie quando si chiama l'API di individuazione
author: pamistel
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: pamistel
ms.date: 02/11/2021
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 13aa12be5a336363bbe3bcbf3e3fb354a8fa3074
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048477"
---
# <a name="understanding-the-anchorlocatecriteria-class"></a>Informazioni sulla classe AnchorLocateCriteria
In questo articolo vengono illustrate le diverse opzioni che è possibile usare quando si eseguono query su un ancoraggio. Si passerà alla classe AnchorLocateCriteria, alle opzioni e alle combinazioni di opzioni valide.

## <a name="anchor-locate-criteria"></a>Individuare i criteri di ancoraggio
La [classe AnchorLocateCriteria](/dotnet/api/microsoft.azure.spatialanchors.anchorlocatecriteria) consente di eseguire query sul servizio per gli ancoraggi creati in precedenza. Un oggetto AnchorLocateCriteria può essere usato per Watcher in qualsiasi momento. Ogni oggetto AnchorLocateCriteria deve includere **esattamente una** delle proprietà seguenti: [identificatori](#identifiers), [NearAnchor](#nearanchor)o [NearDevice](#neardevice). Se lo si desidera, è possibile impostare proprietà aggiuntive, ad esempio [strategie](#strategy), [BypassCache](#bypasscache)e [RequestedCategories](#requestedcategories) . 

### <a name="properties"></a>Proprietà
Definire **esattamente una** delle proprietà seguenti nel Watcher:
#### <a name="identifiers"></a>Identificatori
*Valore predefinito: matrice di stringhe vuota*

Utilizzando gli identificatori, è possibile definire un elenco di ID di ancoraggio per gli ancoraggi che si desidera individuare. Gli ID di ancoraggio vengono inizialmente restituiti all'utente dopo la corretta creazione dell'ancoraggio. Con gli identificatori specificati, AnchorLocateCriteria limita il set di ancoraggi richiesti agli ancoraggi con ID ancoraggio corrispondenti. Questa proprietà viene specificata utilizzando una matrice di stringhe. 

#### <a name="nearanchor"></a>NearAnchor
*Valore predefinito: non impostato*

Utilizzando NearAnchor, è possibile specificare che AnchorLocateCriteria limita il set di ancoraggi richiesti agli ancoraggi all'interno di una distanza desiderata da un ancoraggio scelto. È necessario fornire questo ancoraggio scelto come ancoraggio di origine. È anche possibile impostare la distanza desiderata dall'ancoraggio di origine e il numero massimo di ancoraggi restituiti per limitare ulteriormente la ricerca.
Questa proprietà viene specificata utilizzando un oggetto NearAnchorCriteria.

#### <a name="neardevice"></a>NearDevice
*Valore predefinito: non impostato*

Usando NearDevice, è possibile specificare che AnchorLocateCriteria limita il set di ancoraggi richiesti a quelli vicini alla posizione fisica del dispositivo. Tutti i sensori abilitati verranno usati per individuare gli ancoraggi intorno al dispositivo. Per avere la possibilità di trovare ancoraggi, è necessario configurare SensorCapabilities per consentire l'accesso alla sessione a tutti i sensori appropriati. Per altre informazioni sulla configurazione e l'uso di questa proprietà, vedere [rilocalizzazione grossolana-ancoraggi spaziali di Azure | Microsoft docs](./coarse-reloc.md) e *come creare e individuare ancoraggi usando la rilocalizzazione grossolana* in [C#](../how-tos/set-up-coarse-reloc-unity.md), [Objective-C](../how-tos/set-up-coarse-reloc-unity.md), [Swift](../how-tos/set-up-coarse-reloc-swift.md), [java](../how-tos/set-up-coarse-reloc-java.md), [c++/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md), [c++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md).
Questa proprietà viene specificata utilizzando un oggetto NearDeviceCriteria.

### <a name="additional-properties"></a>Proprietà aggiuntive
#### <a name="bypasscache"></a>BypassCache
*Valore predefinito: false*

Quando un ancoraggio è stato creato o trovato in una sessione, viene archiviato anche nella cache.  Se questa proprietà è impostata su false, tutte le query successive nella stessa sessione restituiranno il valore memorizzato nella cache. Non è stata effettuata alcuna richiesta al servizio ASA.

#### <a name="requestedcategories"></a>RequestedCategories
*Valore predefinito: Properties | Spaziali*

Questa proprietà viene utilizzata per determinare quali dati vengono restituiti da una query utilizzando AnchorLocateCriteria. Il valore predefinito restituisce sia le proprietà sia i dati spaziali. questa impostazione non deve essere modificata se si desiderano entrambe le proprietà e i dati spaziali. Questa proprietà può essere specificata usando l'enumerazione AnchorDataCategory.

Valore enum AnchorDataCategory | Dati restituiti
-----|------------
nessuno | Non vengono restituiti dati
Proprietà| Vengono restituite le proprietà di ancoraggio, tra cui oggetto appproperties.
Spaziali| Vengono restituite informazioni spaziali su un ancoraggio.

#### <a name="strategy"></a>Strategia
*Valore predefinito: AnyStrategy*

La strategia definisce ulteriormente il modo in cui devono trovarsi gli ancoraggi. È possibile specificare la proprietà Strategy usando un'enumerazione LocateStrategy.

Valore enum LocateStrategy | Descrizione
---------------|------------
AnyStrategy | Questa strategia consente al sistema di usare combinazioni di strategie VisualInformation e Relationship per trovare ancoraggi. 
VisualInformation|Questa strategia tenta di trovare ancoraggi mediante la corrispondenza tra le informazioni visive dell'ambiente corrente e quelle del footprint visivo dell'ancoraggio. Il footprint visivo di un ancoraggio si riferisce alle informazioni visive attualmente associate all'ancoraggio. Queste informazioni visive vengono in genere raccolte ma non esclusivamente durante la creazione dell'ancoraggio. Attualmente questa strategia è consentita solo in combinazione con le proprietà NearDevice o Identifiers.
Relationship|Questa strategia tenta di trovare ancoraggi usando [ancoraggi connessi](./anchor-relationships-way-finding.md#connect-anchors)esistenti. Attualmente questa strategia è consentita solo in combinazione con le proprietà NearAnchor o Identifiers. Quando viene usato con la proprietà identifiers, è necessario che nella stessa sessione l'utente abbia individuato in precedenza uno o più ancoraggi con relazioni connesse già stabilite con le relazioni di ancoraggio i cui ID sono specificati nella matrice Identifiers. 


### <a name="valid-combinations-of-locatestrategy-and-anchorlocatecriteria-properties"></a>Combinazioni valide di proprietà LocateStrategy e AnchorLocateCriteria 

Non tutte le combinazioni di proprietà Strategy e AnchorLocateCriteria sono attualmente consentite dal sistema. Nella tabella seguente vengono illustrate le combinazioni consentite:



Proprietà | AnyStrategy | Relationship | VisualInformation
-------- | ------------|--------------|-------------------
Identificatori | &check;    | &check;     | &check;
NearAnchor  | &check;   (per impostazione predefinita, relazione) | &check;    | 
NearDevice  | &check;    |   | &check;




## <a name="next-steps"></a>Passaggi successivi

Per altri esempi di uso della classe AnchorLocateCriteria, vedere [come creare e individuare ancoraggi usando ancoraggi spaziali di Azure](../create-locate-anchors-overview.md) .