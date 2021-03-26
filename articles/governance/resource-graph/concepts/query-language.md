---
title: Informazioni sul linguaggio di query
description: Descrive le tabelle di Resource Graph e i tipi di dati, gli operatori e le funzioni di Kusto disponibili utilizzabili con Azure Resource Graph.
ms.date: 03/10/2021
ms.topic: conceptual
ms.openlocfilehash: 5e600439d54a89dd9bd2510b2e47b71b60ee93a7
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105557684"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Informazioni sul linguaggio di query di Azure Resource Graph

Il linguaggio di query di Azure Resource Graph supporta un certo numero di operatori e funzioni, ognuno dei quali opera in base al [linguaggio di query Kusto (KQL)](/azure/kusto/query/index). Per informazioni sul linguaggio di query usato da Resource Graph, iniziare con l'[esercitazione per KQL](/azure/kusto/query/tutorial).

Questo articolo illustra i componenti del linguaggio supportati da Resource Graph:

- [Tabelle di Resource Graph](#resource-graph-tables)
- [Elementi di linguaggio personalizzati del grafico risorse](#resource-graph-custom-language-elements)
- [Elementi supportati del linguaggio KQL](#supported-kql-language-elements)
- [Ambito della query](#query-scope)
- [Caratteri di escape](#escape-characters)

## <a name="resource-graph-tables"></a>Tabelle di Resource Graph

Il grafico risorse fornisce diverse tabelle per i dati archiviati su Azure Resource Manager tipi di risorse e le relative proprietà. Alcune tabelle possono essere utilizzate con `join` `union` operatori o per ottenere proprietà dai tipi di risorse correlati. Di seguito è riportato l'elenco di tabelle disponibili in Resource Graph:

|Tabella Graph delle risorse |È possibile disporre di `join` altre tabelle? |Descrizione |
|---|---|---|
|Risorse |Sì |Tabella predefinita se non ne è stata definita alcuna nella query. In questa tabella sono contenuti quasi tutti i tipi di risorsa di Resource Manager e le relative proprietà. |
|ResourceContainers |Sì |Include i dati e i tipi di risorsa delle sottoscrizioni (in anteprima, `Microsoft.Resources/subscriptions`) e dei gruppi di risorse (`Microsoft.Resources/subscriptions/resourcegroups`). |
|AdvisorResources |Sì (anteprima) |Include le risorse _relative_ a `Microsoft.Advisor`. |
|AlertsManagementResources |Sì (anteprima) |Include le risorse _relative_ a `Microsoft.AlertsManagement`. |
|ExtendedLocationResources |No |Include le risorse _relative_ a `Microsoft.ExtendedLocation`. |
|GuestConfigurationResources |No |Include le risorse _relative_ a `Microsoft.GuestConfiguration`. |
|KubernetesConfigurationResources |No |Include le risorse _relative_ a `Microsoft.KubernetesConfiguration`. |
|MaintenanceResources |Partial, join solo _a_ . (anteprima) |Include le risorse _relative_ a `Microsoft.Maintenance`. |
|PatchAssessmentResources|No |Include risorse _correlate_ alla valutazione delle patch per le macchine virtuali di Azure. |
|PatchInstallationResources|No |Include risorse _correlate_ all'installazione delle patch per macchine virtuali di Azure. |
|PolicyResources |No |Include le risorse _relative_ a `Microsoft.PolicyInsights`. (**Anteprima**)|
|RecoveryServicesResources |Partial, join solo _a_ . (anteprima) |Include le risorse _correlate_ a `Microsoft.DataProtection` e `Microsoft.RecoveryServices` . |
|SecurityResources |Partial, join solo _a_ . (anteprima) |Include le risorse _relative_ a `Microsoft.Security`. |
|ServiceHealthResources |No |Include le risorse _relative_ a `Microsoft.ResourceHealth`. |
|WorkloadMonitorResources |No |Include le risorse _relative_ a `Microsoft.WorkloadMonitor`. |

Per un elenco completo, inclusi i tipi di risorse, vedere informazioni di [riferimento: tabelle e tipi di risorse supportati](../reference/supported-tables-resources.md).

> [!NOTE]
> _Resources_ è la tabella predefinita. Quando si eseguono query sulla tabella _Resources_, non è necessario fornire il nome della tabella, a meno che non vengano usati `join` o `union`. È consigliabile, tuttavia, includere sempre la tabella iniziale nella query.

Usare Resource Graph Explorer nel portale per individuare i tipi di risorsa disponibili in ogni tabella. In alternativa, usare una query, ad esempio `<tableName> | distinct type`, per ottenere un elenco dei tipi di risorsa supportati dalla tabella di Resource Graph specificata che sono presenti nell'ambiente in uso.

La query seguente include un operatore `join` semplice. Il risultato della query combina insieme le colonne e a tutti i nomi di colonna duplicati della tabella unita in join, _ResourceContainers_ in questo esempio, viene aggiunto **1**. Poiché la tabella _ResourceContainers_ include tipi per le sottoscrizioni e i gruppi di risorse, è possibile usare entrambi i tipi per unire in join la risorsa dalla tabella _risorse_ .

```kusto
Resources
| join ResourceContainers on subscriptionId
| limit 1
```

La query seguente illustra un uso più complesso di `join`. In primo luogo, la query USA `project` per ottenere i campi dalle _risorse_ per il tipo di risorsa di insiemi di credenziali Azure Key Vault. Il passaggio successivo usa `join` per unire i risultati con _ResourceContainers_ , in cui il tipo è una sottoscrizione _di_ una proprietà che si trova sia nella prima tabella che nella `project` tabella unita in join `project` . La ridenominazione dei campi evita `join` di aggiungerla come _name1_ , perché la proprietà è già proiettata dalle _risorse_. Il risultato della query è un singolo insieme di credenziali delle chiavi che Visualizza il tipo, il nome, la posizione e il gruppo di risorse dell'insieme di credenziali delle chiavi e il nome della sottoscrizione in cui si trova.

```kusto
Resources
| where type == 'microsoft.keyvault/vaults'
| project name, type, location, subscriptionId, resourceGroup
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project type, name, location, resourceGroup, SubName
| limit 1
```

> [!NOTE]
> Quando si limitano i risultati di `join` con `project`, la proprietà usata da `join` per correlare le due tabelle, _subscriptionId_ nell'esempio precedente, deve essere inclusa in `project`.

## <a name="extended-properties-preview"></a><a name="extended-properties"></a>Proprietà estese (anteprima)

Come funzionalità di _Anteprima_ , alcuni tipi di risorse in Graph di risorse dispongono di proprietà aggiuntive correlate ai tipi disponibili per eseguire query oltre le proprietà fornite da Azure Resource Manager. Questo set di valori, noto come _proprietà estese_, esiste in un tipo di risorsa supportato in `properties.extended` . Per visualizzare i tipi di risorse con _proprietà estese_, usare la query seguente:

```kusto
Resources
| where isnotnull(properties.extended)
| distinct type
| order by type asc
```

Esempio: ottenere il numero di macchine virtuali per `instanceView.powerState.code` :

```kusto
Resources
| where type == 'microsoft.compute/virtualmachines'
| summarize count() by tostring(properties.extended.instanceView.powerState.code)
```

## <a name="resource-graph-custom-language-elements"></a>Elementi di linguaggio personalizzati del grafico risorse

### <a name="shared-query-syntax-preview"></a><a name="shared-query-syntax"></a>Sintassi di query condivise (anteprima)

Come funzionalità di anteprima, è possibile accedere a una [query condivisa](../tutorials/create-share-query.md) direttamente in una query del grafico di risorse. Questo scenario rende possibile la creazione di query standard come query condivise e riutilizzarle. Per chiamare una query condivisa all'interno di una query di Graph di risorse, usare la `{{shared-query-uri}}` sintassi. L'URI della query condivisa è l' _ID risorsa_ della query condivisa nella pagina **delle impostazioni** per la query. In questo esempio, l'URI della query condivisa è `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SharedQueries/providers/Microsoft.ResourceGraph/queries/Count VMs by OS` .
Questo URI punta alla sottoscrizione, al gruppo di risorse e al nome completo della query condivisa a cui si vuole fare riferimento in un'altra query. Questa query è identica a quella creata in [esercitazione: creare e condividere una query](../tutorials/create-share-query.md).

> [!NOTE]
> Non è possibile salvare una query che fa riferimento a una query condivisa come query condivisa.

Esempio 1: usare solo la query condivisa

I risultati di questa query del grafico risorse corrispondono alla query archiviata nella query condivisa.

```kusto
{{/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SharedQueries/providers/Microsoft.ResourceGraph/queries/Count VMs by OS}}
```

Esempio 2: includere la query condivisa come parte di una query di dimensioni maggiori

Questa query USA prima di tutto la query condivisa, quindi usa `limit` per limitare ulteriormente i risultati.

```kusto
{{/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SharedQueries/providers/Microsoft.ResourceGraph/queries/Count VMs by OS}}
| where properties_storageProfile_osDisk_osType =~ 'Windows'
```

## <a name="supported-kql-language-elements"></a>Elementi supportati del linguaggio KQL

Il grafico risorse supporta un subset di [tipi di dati](/azure/kusto/query/scalar-data-types/)KQL, [funzioni scalari](/azure/kusto/query/scalarfunctions), [operatori scalari](/azure/kusto/query/binoperators)e [funzioni di aggregazione](/azure/kusto/query/any-aggfunction). Resource Graph supporta [operatori tabulari](/azure/kusto/query/queries) specifici, alcuni dei quali con comportamenti diversi.

### <a name="supported-tabulartop-level-operators"></a>Operatori tabulari/di livello superiore supportati

Di seguito è riportato l'elenco degli operatori tabulari di KQL supportati da Resource Graph con esempi specifici:

|KQL |Query di esempio di Resource Graph |Note |
|---|---|---|
|[count](/azure/kusto/query/countoperator) |[Contare gli insiemi di credenziali delle chiavi](../samples/starter.md#count-keyvaults) | |
|[distinct](/azure/kusto/query/distinctoperator) |[Mostrare le risorse che contengono archivi](../samples/starter.md#show-storage) | |
|[extend](/azure/kusto/query/extendoperator) |[Contare le macchine virtuali per tipo di sistema operativo](../samples/starter.md#count-os) | |
|[join](/azure/kusto/query/joinoperator) |[Insieme di credenziali delle chiavi con il nome della sottoscrizione](../samples/advanced.md#join) |Tipi di join supportati: [innerunique](/azure/kusto/query/joinoperator#default-join-flavor), [inner](/azure/kusto/query/joinoperator#inner-join), [leftouter](/azure/kusto/query/joinoperator#left-outer-join). Limite di 3 `join` in una singola query, 1 di che può essere una tabella incrociata `join` . Se l'uso di tutte le tabelle `join` è compreso tra _Resource_ e _ResourceContainers_, sono consentite 3 incrociate tra tabelle `join` . Strategie di join personalizzate, ad esempio il join di trasmissione, non sono consentite. Per informazioni sulle tabelle che è possibile usare `join` , vedere [tabelle di grafici delle risorse](#resource-graph-tables). |
|[limit](/azure/kusto/query/limitoperator) |[Elencare tutti gli indirizzi IP pubblici](../samples/starter.md#list-publicip) |Sinonimo di `take` . Non funziona con [Skip](./work-with-data.md#skipping-records). |
|[mvexpand](/azure/kusto/query/mvexpandoperator) | | Operatore legacy. In sostituzione usare `mv-expand`. _RowLimit_ max 400. Il valore predefinito è 128. |
|[mv-expand](/azure/kusto/query/mvexpandoperator) |[Visualizzare Cosmos DB con specifiche posizioni di scrittura](../samples/advanced.md#mvexpand-cosmosdb) |_RowLimit_ max 400. Il valore predefinito è 128. Limite di 2 `mv-expand` in una singola query.|
|[order](/azure/kusto/query/orderoperator) |[Elencare le risorse ordinate per nome](../samples/starter.md#list-resources) |Sinonimo di `sort` |
|[project](/azure/kusto/query/projectoperator) |[Elencare le risorse ordinate per nome](../samples/starter.md#list-resources) | |
|[project-away](/azure/kusto/query/projectawayoperator) |[Rimuovere colonne dai risultati](../samples/advanced.md#remove-column) | |
|[sort](/azure/kusto/query/sortoperator) |[Elencare le risorse ordinate per nome](../samples/starter.md#list-resources) |Sinonimo di `order` |
|[summarize](/azure/kusto/query/summarizeoperator) |[Contare le risorse di Azure](../samples/starter.md#count-resources) |Solo prima pagina semplificata |
|[take](/azure/kusto/query/takeoperator) |[Elencare tutti gli indirizzi IP pubblici](../samples/starter.md#list-publicip) |Sinonimo di `limit` . Non funziona con [Skip](./work-with-data.md#skipping-records). |
|[top](/azure/kusto/query/topoperator) |[Mostrare le prime cinque macchine virtuali per nome e tipo di sistema operativo](../samples/starter.md#show-sorted) | |
|[union](/azure/kusto/query/unionoperator) |[Combinare i risultati di due query in un singolo risultato](../samples/advanced.md#unionresults) |Singola tabella consentita: _T_ `| union` \[`kind=` `inner`\|`outer`\] \[`withsource=`_ColumnName_\] _Table_. Limite di 3 code `union` in una singola query. La risoluzione fuzzy di tabelle di code `union` non è consentita. Può essere usato all'interno di una singola tabella o tra le tabelle _Resources_ e _ResourceContainers_. |
|[where](/azure/kusto/query/whereoperator) |[Mostrare le risorse che contengono archivi](../samples/starter.md#show-storage) | |

Un limite predefinito di 3 e 3 operatori è costituito da `join` `mv-expand` una singola query SDK di Resource Graph. È possibile richiedere un aumento dei limiti per il tenant tramite **Guida e supporto tecnico**.

Per supportare l'esperienza del portale "Apri query", Azure Resource Graph Explorer ha un limite globale maggiore rispetto a Resource Graph SDK.

## <a name="query-scope"></a>Ambito delle query

L'ambito delle sottoscrizioni da cui le risorse vengono restituite da una query dipende dal metodo di accesso al grafico delle risorse. L'interfaccia della riga di comando di Azure e Azure PowerShell popolare l'elenco di sottoscrizioni da includere nella richiesta in base al contesto dell'utente autorizzato. L'elenco delle sottoscrizioni può essere definito manualmente per ogni con le **sottoscrizioni** e i parametri di **sottoscrizione** , rispettivamente.
Nell'API REST e in tutti gli altri SDK, l'elenco di sottoscrizioni per includere le risorse da deve essere definito in modo esplicito come parte della richiesta.

Come **Anteprima**, la versione dell'API REST `2020-04-01-preview` aggiunge una proprietà per definire l'ambito della query a un [gruppo di gestione](../../management-groups/overview.md). Questa API di anteprima rende inoltre la proprietà della sottoscrizione facoltativa. Se un gruppo di gestione o un elenco di sottoscrizioni non è definito, l'ambito della query è costituito da tutte le risorse, incluse le risorse delegate del [Faro di Azure](../../../lighthouse/concepts/azure-delegated-resource-management.md) , a cui l'utente autenticato può accedere. La nuova `managementGroupId` proprietà accetta l'ID del gruppo di gestione, che è diverso dal nome del gruppo di gestione. Quando `managementGroupId` si specifica, vengono incluse le risorse della prima sottoscrizioni 5000 in o sotto la gerarchia del gruppo di gestione specificata. `managementGroupId` non può essere usato contemporaneamente a `subscriptions` .

Esempio: eseguire una query su tutte le risorse nella gerarchia del gruppo di gestione denominato "My Management Group" con ID "myMG".

- URI DELL'API REST

  ```http
  POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2020-04-01-preview
  ```

- Request Body

  ```json
  {
      "query": "Resources | summarize count()",
      "managementGroupId": "myMG"
  }
  ```

## <a name="escape-characters"></a>Caratteri di escape

Alcuni nomi di proprietà, ad esempio quelli che includono `.` o `$`, devono essere sottoposti a wrapping o preceduti da caratteri di escape nella query, altrimenti il nome della proprietà viene interpretato in modo errato e non vengono generati i risultati previsti.

- `.` - Eseguire il wrapping del nome della proprietà nel modo seguente: `['propertyname.withaperiod']`
  
  Query di esempio con wrapping della proprietà _odata.type_:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$` - Anteporre il carattere di escape al nome della proprietà. Il carattere di escape usato dipende dalla shell da cui viene eseguito Resource Graph.

  - **bash** - `\`

    Query di esempio con carattere di escape per la proprietà _\$type_ in bash:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** - Non anteporre il carattere di escape a `$`.

  - **PowerShell** - ``` ` ```

    Query di esempio con carattere di escape per la proprietà _\$type_ in PowerShell:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>Passaggi successivi

- Vedere il linguaggio in uso in [Query di base](../samples/starter.md).
- Vedere gli usi avanzati in [Query avanzate](../samples/advanced.md).
- Altre informazioni su come [esplorare le risorse](explore-resources.md).
