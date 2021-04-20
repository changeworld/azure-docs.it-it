---
title: Tag Service Fabric nodo dinamico di Azure
description: Azure Service Fabric consente di aggiungere e rimuovere in modo dinamico i tag di nodo.
author: yu-supersonic
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: branim
ms.custom: devx-track-csharp
ms.openlocfilehash: 712e6422060619e5567a74d6335320eff9ed8e66
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741746"
---
# <a name="introduction-to-dynamic-node-tags"></a>Introduzione ai tag dei nodi dinamici
I tag di nodo consentono di aggiungere e rimuovere in modo dinamico i tag dai nodi per influenzare il posizionamento dei servizi. L'assegnazione di tag ai nodi è molto flessibile e consente modifiche al posizionamento del servizio senza aggiornamenti dell'applicazione o del cluster. I tag possono essere aggiunti o rimossi dai nodi in qualsiasi momento e i servizi possono specificare i requisiti per determinati tag quando vengono creati. I requisiti dei tag di un servizio possono anche essere aggiornati dinamicamente durante l'esecuzione.

L'assegnazione di tag ai nodi è simile [ai vincoli di posizionamento](service-fabric-cluster-resource-manager-configure-services.md) e viene in genere usata per controllare i nodi in cui viene eseguito un servizio. Ogni Service Fabric può essere configurato in modo da richiedere il tag da inserire o continuare a eseguire.

L'assegnazione di tag ai nodi è supportata per Service Fabric tipi di servizio ospitati (Reliable Services, eseguibili guest e contenitori). Per usare l'assegnazione di tag ai nodi, è necessario eseguire la versione 8.0 o successiva del runtime Service Fabric.

Nella parte restante di questo articolo vengono descritti i modi per abilitare o disabilitare l'assegnazione di tag ai nodi e vengono forniti esempi su come usare questa funzionalità.


## <a name="describing-dynamic-node-tags"></a>Descrizione dei tag di nodo dinamici
I servizi possono specificare i tag necessari. Esistono due tipi di tag:
* **I tag necessari per la selezione** host descrivono un set di tag, che sono necessari solo per il posizionamento del servizio. Dopo aver inserito la replica, questi tag possono essere rimossi senza interrompere il servizio. Se uno di questi tag viene rimosso dal nodo, la replica del servizio continuerà a funzionare e Service Fabric non rimuoverà il servizio

* **I tag necessari per l'esecuzione** descrivono un set di tag, necessari sia per la selezione host che per l'esecuzione del servizio. Se uno dei tag in esecuzione necessari viene rimosso, Service Fabric sposterà il servizio in un altro nodo in cui sono specificati tali tag.

Esempio: obbligatorio per i tag di posizionamento può essere utilizzato quando si usa un qualche tipo di servizio di attivazione del contenitore ed è necessario che il servizio per il contenitore venga inserito e non appena il contenitore viene attivato, non è più necessario l'attivatore ed è possibile rimuovere il tag associato, ma il contenitore deve continuare a essere in esecuzione.
Obbligatorio per l'esecuzione dei tag può essere usato quando si dispone di un servizio di fatturazione, che è utile per essere collocato con il servizio rivolto all'utente. Quando il servizio di fatturazione ha esito negativo nel nodo, si rimuove il tag associato e il servizio rivolto all'utente viene spostato in un altro nodo, che include il servizio di fatturazione e il relativo tag.

È possibile aggiungere, aggiornare o rimuovere un tag o un set di tag da un singolo nodo usando meccanismi di interfaccia Service Fabric standard, ad esempio API C#, API REST o comandi di PowerShell.

> [!NOTE]
> Service Fabric mantiene le distribuzioni UD/FD quando si usano i tag di nodo. Gestire i tag di nodo in modo appropriato, in modo da non ottenere violazioni FD/UD a causa di una distribuzione non appropriata dei tag tra domini.

## <a name="enabling-dynamic-node-tags"></a>Abilitazione di tag di nodo dinamici
Per il funzionamento di questa funzionalità, è necessario abilitare la configurazione NodeTaggingEnabled nella sezione PlacementAndLoadBalancing del manifesto del cluster usando XML o JSON:

``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="NodeTaggingEnabled" Value="true" />
</Section>
```

mediante ClusterConfig.json per le distribuzioni autonome o Template.json per i cluster ospitati in Azure:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": " NodeTaggingEnabled ",
          "value": "true"
      }
    ]
  }
]
```

## <a name="setting-dynamic-node-tags"></a>Impostazione di tag di nodi dinamici

### <a name="using-powershell"></a>Uso di PowerShell

Aggiunta di tag di nodo al nodo:

```posh
Add-ServiceFabricNodeTags -NodeName "DB.1" -NodeTags @("SampleTag1", "SampleTag2")
```
Questo comando aggiungerà i tag "SampleTag1" e "SampleTag2" nel nodo DB.1.

Rimuovere i tag di nodo dal nodo:

```posh
Remove-ServiceFabricNodeTags -NodeName "DB.1" -NodeTags @("SampleTag1", "SampleTag2")
```
Questo comando rimuoverà i tag "SampleTag1" e "SampleTag2" nel nodo DB.1.

### <a name="using-c-apis"></a>Tramite le API C#

Aggiunta di tag di nodo al nodo:

```csharp
FabricClient fabricClient = new FabricClient();
List<string> nodeTagsList = new List<string>();
nodeTagsList.Add("SampleTag1");
nodeTagsList.Add("SampleTag2");
await fabricClient.ClusterManager.AddNodeTagsAsync("DB.1", nodeTagsList);
```

Rimuovere i tag di nodo dal nodo:

```csharp
FabricClient fabricClient = new FabricClient();
List<string> nodeTagsList = new List<string>();
nodeTagsList.Add("SampleTag1");
nodeTagsList.Add("SampleTag2");
await fabricClient.ClusterManager.RemoveNodeTagsAsync("DB.1", nodeTagsList);
```

## <a name="setting-required-tags-for-services"></a>Impostazione dei tag obbligatori per i servizi

### <a name="using-powershell"></a>Uso di PowerShell

Creazione di un nuovo servizio:

```posh
New-ServiceFabricService -ApplicationName fabric:/HelloWorld -ServiceName fabric:/HelloWorld/svc1 -ServiceTypeName HelloWorldStateful -Stateful -PartitionSchemeSingleton -TargetReplicaSetSize 5 -MinReplicaSetSize 3 -TagsRequiredToRun @("SampleTag1") - TagsRequiredToPlace @("SampleTag2")
```
Questo comando crea un servizio, che richiede che "SampleTag2" sia presente in un nodo in modo che il servizio sia posizionato in tale nodo e che "SampleTag1" sia presente per consentire al servizio di continuare l'esecuzione in tale nodo.

Aggiornamento del servizio esistente:

```posh
Update-ServiceFabricService -Stateful -ServiceName fabric:/myapp/test -TagsRequiredToRun @("SampleTag1") -TagsRequiredToPlace @("SampleTag2")
```
Questo comando aggiorna un servizio, che richiede che "SampleTag2" sia presente in un nodo in modo che il servizio sia posizionato in tale nodo e che "SampleTag1" sia presente per consentire al servizio di continuare l'esecuzione in tale nodo.

### <a name="using-c-apis"></a>Tramite le API C#

Creazione di un nuovo servizio:

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
//set up the rest of the ServiceDescription
ServiceTags serviceTags = new ServiceTags();
serviceTags.TagsRequiredToPlace.Add("SampleTag1");
serviceTags.TagsRequiredToRun.Add("SampleTag2");
serviceDescription.ServiceTags = serviceTags;
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Aggiornamento del servizio esistente:

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceUpdateDescription serviceUpdate = new StatefulServiceUpdateDescription();
ServiceTags serviceTags = new ServiceTags();
serviceTags.TagsRequiredToPlace.Add("SampleTag1");
serviceTags.TagsRequiredToRun.Add("SampleTag2");
serviceUpdate.ServiceTags = serviceTags;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/AppName/ServiceName"), serviceUpdate);
```

Tutti questi comandi si applicano ugualmente ai servizi senza stato.

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sui vincoli [di posizionamento](service-fabric-cluster-resource-manager-configure-services.md)
