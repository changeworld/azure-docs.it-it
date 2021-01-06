---
title: Piano Premium di funzioni di Azure
description: Dettagli e opzioni di configurazione (VNet, nessun avvio a freddo, durata di esecuzione illimitata) per il piano Premium di funzioni di Azure.
author: jeffhollan
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: jehollan
ms.custom:
- references_regions
- fasttrack-edit
- devx-track-azurecli
ms.openlocfilehash: d944512e5f6126920ab4fba99fb70513b93177ba
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936822"
---
# <a name="azure-functions-premium-plan"></a>Piano Premium di funzioni di Azure

Il piano Premium di funzioni di Azure, noto anche come piano Premium elastico, è un'opzione di hosting per le app per le funzioni. Per altre opzioni del piano di hosting, vedere l'articolo relativo al [piano di hosting](functions-scale.md).

L'hosting del piano Premium offre i vantaggi seguenti per le funzioni:

* Evitare l'avvio a freddo con istanze perennemente calde
* Connettività di rete virtuale.
* Durata di esecuzione illimitata, con garanzia di 60 minuti.
* Dimensioni delle istanze Premium: un'unica core, due core e quattro istanze principali.
* Prezzo più prevedibile rispetto al piano a consumo.
* Allocazione di app ad alta densità per i piani con più app per le funzioni.

Quando si usa il piano Premium, le istanze dell'host di funzioni di Azure vengono aggiunte e rimosse in base al numero di eventi in ingresso, proprio come il [piano a consumo](consumption-plan.md). È possibile distribuire più app per le funzioni nello stesso piano Premium e il piano consente di configurare le dimensioni dell'istanza di calcolo, le dimensioni del piano di base e la dimensione massima del piano. 

## <a name="billing"></a>Fatturazione

La fatturazione per il piano Premium si basa sul numero di secondi core e della memoria allocata tra le istanze. Questa fatturazione differisce dal piano a consumo, fatturato in base all'esecuzione e alla memoria utilizzata. Non sono previsti addebiti per l'esecuzione con il piano Premium. Almeno un'istanza deve essere allocata in qualsiasi momento per ogni piano. Questa fatturazione comporta un costo mensile minimo per piano attivo, indipendentemente dal fatto che la funzione sia attiva o inattiva. Tenere presente che tutte le app per le funzioni in un piano Premium condividono istanze allocate. Per altre informazioni, vedere la [pagina dei prezzi di funzioni di Azure](https://azure.microsoft.com/pricing/details/functions/).

## <a name="create-a-premium-plan"></a>Creare un piano Premium

Quando si crea un'app per le funzioni nella portale di Azure, il piano a consumo è l'impostazione predefinita. Per creare un'app per le funzioni che viene eseguita in un piano Premium, è necessario creare un piano di servizio app in modo esplicito usando uno degli SKU _Premium elastici_ . L'app per le funzioni creata viene quindi ospitata nel piano. Il portale di Azure semplifica la creazione contemporaneamente del piano Premium e dell'app per le funzioni. È possibile eseguire più app per le funzioni nello stesso piano Premium, ma la maggior parte viene eseguita nello stesso sistema operativo (Windows o Linux). 

Gli articoli seguenti illustrano come creare un'app per le funzioni con un piano Premium, a livello di codice o nel portale di Azure:

+ [Azure portal](create-premium-plan-function-app-portal.md)
+ [Interfaccia della riga di comando di Azure](scripts/functions-cli-create-premium-plan.md)
+ [Modello di Azure Resource Manager](functions-infrastructure-as-code.md#deploy-on-premium-plan)

## <a name="eliminate-cold-starts"></a>Elimina avvio a freddo

Quando gli eventi o le esecuzioni non si verificano nel piano a consumo, l'app può essere ridimensionata a zero istanze. Quando vengono generati nuovi eventi, deve essere specializzata una nuova istanza con l'app in esecuzione su di essa. La specializzazione di nuove istanze può richiedere del tempo a seconda dell'app. Questa latenza aggiuntiva alla prima chiamata viene spesso chiamata _avvio a freddo_ dell'app.

Il piano Premium offre due funzionalità che interagiscono per eliminare efficacemente le partenze a freddo nelle funzioni: le _istanze sempre pronte_ e le _istanze preriscaldate_. 

### <a name="always-ready-instances"></a>Istanze sempre pronte

Nel piano Premium è possibile fare in maniera che l'app sia sempre pronta per un numero specificato di istanze. Il numero massimo di istanze sempre pronte è 20. Quando gli eventi iniziano ad attivare l'app, vengono innanzitutto indirizzati alle istanze sempre pronte. Quando la funzione diventa attiva, le istanze aggiuntive verranno scaldate come buffer. Questo buffer impedisce l'avvio a freddo per le nuove istanze richieste durante la scalabilità. Queste istanze memorizzate nel buffer sono denominate [istanze pre-surriscaldate](#pre-warmed-instances). Con la combinazione delle istanze sempre pronte e di un buffer già riscaldato, l'app può eliminare efficacemente l'avvio a freddo.

> [!NOTE]
> Ogni piano Premium ha almeno un'istanza attiva (fatturata) in qualsiasi momento.

# <a name="portal"></a>[Portale](#tab/portal)

È possibile configurare il numero di istanze sempre pronte nel portale di Azure selezionando il **app per le funzioni**, passando alla scheda **funzionalità della piattaforma** e selezionando le opzioni di **scale out** . Nella finestra di modifica dell'app per le funzioni, le istanze sempre pronte sono specifiche dell'app.

![Impostazioni di scalabilità elastica](./media/functions-premium-plan/scale-out.png)

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azurecli)

È anche possibile configurare le istanze sempre pronte per un'app con l'interfaccia della riga di comando di Azure.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.minimumElasticInstanceCount=<desired_always_ready_count> --resource-type Microsoft.Web/sites
```
---

### <a name="pre-warmed-instances"></a>Istanze pre-riscaldate

Le istanze pre-riscaldate sono istanze scaldate come buffer durante gli eventi di scalabilità e attivazione. Le istanze pre-surriscaldate continuano a essere memorizzate nel buffer fino a quando non viene raggiunto il limite massimo di scalabilità orizzontale. Il numero predefinito di istanze pre-riscaldate è 1 e per la maggior parte degli scenari questo valore deve essere pari a 1.

Quando un'app ha un tempo di riscaldamento prolungato (ad esempio un'immagine del contenitore personalizzata), potrebbe essere necessario aumentare questo buffer. Un'istanza pre-riscaldata diventa attiva solo dopo che tutte le istanze attive sono state sufficientemente utilizzate.

Si consideri questo esempio del modo in cui le istanze sempre pronte e le istanze pre-surriscaldate interagiscono. Per un'app per le funzioni Premium sono configurate cinque istanze sempre pronte e l'impostazione predefinita di un'istanza pre-riscaldata. Quando l'app è inattiva e non viene attivato alcun evento, l'app viene sottoposta a provisioning e in esecuzione con cinque istanze. Al momento, non viene addebitata un'istanza pre-riscaldata perché non vengono usate le istanze sempre pronte e non viene allocata un'istanza pre-riscaldata.

Non appena viene attivato il primo trigger, le cinque istanze sempre pronte diventano attive e viene allocata un'istanza pre-riscaldata. L'app viene ora eseguita con sei istanze di cui è stato effettuato il provisioning: le cinque istanze always ready attive e il sesto buffer preriscaldato e inattivo. Se la frequenza delle esecuzioni continua ad aumentare, verranno usate le cinque istanze attive. Quando la piattaforma decide di scalare oltre cinque istanze, viene ridimensionata nell'istanza pre-riscaldata. Quando si verifica questo problema, sono ora presenti sei istanze attive e viene eseguito immediatamente il provisioning di una settima istanza e viene riempito il buffer pre-riscaldato. Questa sequenza di ridimensionamento e pre-riscaldamento continua fino a quando non viene raggiunto il numero massimo di istanze per l'app. Nessuna istanza è pre-riscaldata o attivata oltre il valore massimo.

È possibile modificare il numero di istanze pre-surriscaldate per un'app usando l'interfaccia della riga di comando di Azure.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites
```

### <a name="maximum-function-app-instances"></a>Numero massimo di istanze di app per le funzioni

Oltre al [numero massimo di istanze del piano](#plan-and-sku-settings), è possibile configurare un valore massimo per app. Il numero massimo di app può essere configurato usando il [limite di scalabilità dell'app](./event-driven-scaling.md#limit-scale-out).

## <a name="private-network-connectivity"></a>Connettività di rete privata

Le app per le funzioni distribuite in un piano Premium possono sfruttare l' [integrazione VNet per le app Web](../app-service/web-sites-integrate-with-vnet.md). Quando viene configurata, l'app può comunicare con le risorse all'interno della VNet o essere protetta tramite endpoint di servizio. Le restrizioni IP sono disponibili anche nell'app per limitare il traffico in ingresso.

Quando si assegna una subnet all'app per le funzioni in un piano Premium, è necessaria una subnet con un numero sufficiente di indirizzi IP per ogni potenziale istanza. È necessario un blocco IP con almeno 100 indirizzi disponibili.

Per altre informazioni, vedere [integrare l'app per le funzioni con un VNet](functions-create-vnet.md).

## <a name="rapid-elastic-scale"></a>Scalabilità elastica rapida

Altre istanze di calcolo vengono aggiunte automaticamente per l'app usando la stessa logica di scalabilità rapida del piano a consumo. Le app nello stesso piano di servizio app vengono ridimensionate indipendentemente l'una dall'altra in base alle esigenze di una singola app. Tuttavia, le app per le funzioni nello stesso piano di servizio app condividono le risorse delle macchine virtuali per ridurre i costi, quando possibile. Il numero di app associate a una macchina virtuale dipende dal footprint di ogni app e dalle dimensioni della macchina virtuale.

Per altre informazioni sul funzionamento della scalabilità, vedere [scalabilità guidata dagli eventi in funzioni di Azure](event-driven-scaling.md).

## <a name="longer-run-duration"></a>Durata dell'esecuzione più lunga

Per una singola esecuzione, le funzioni di Azure in un piano a consumo sono limitate a 10 minuti. Nel piano Premium, per la durata dell'esecuzione viene impostato un valore predefinito di 30 minuti per impedire l'esecuzione di Runaway. Tuttavia, è possibile [modificare il host.jsnella configurazione](./functions-host-json.md#functiontimeout) per rendere la durata non vincolata per le app del piano Premium. Se impostato su una durata illimitata, l'esecuzione dell'app per le funzioni è garantita per almeno 60 minuti. 

## <a name="plan-and-sku-settings"></a>Impostazioni del piano e dello SKU

Quando si crea il piano, sono disponibili due impostazioni relative alle dimensioni del piano, ovvero il numero minimo di istanze (o le dimensioni del piano) e il limite massimo di picchi.

Se l'app richiede istanze oltre le istanze sempre pronte, può continuare a eseguire la scalabilità orizzontale fino a quando il numero di istanze raggiunge il limite massimo di picchi. Verranno addebitati i costi per le istanze oltre le dimensioni del piano solo quando vengono eseguite e allocate all'utente, in base al secondo. La piattaforma consente di ottimizzare la scalabilità dell'app fino al limite massimo definito.

È possibile configurare le dimensioni del piano e i valori massimi nel portale di Azure selezionando le opzioni **scale out** nel piano o in un'app per le funzioni distribuita in tale piano (in **funzionalità della piattaforma**).

È anche possibile aumentare il limite massimo di picchi dall'interfaccia della riga di comando di Azure:

```azurecli-interactive
az functionapp plan update -g <resource_group> -n <premium_plan_name> --max-burst <desired_max_burst>
```

Il valore minimo per ogni piano sarà almeno un'istanza. Il numero minimo effettivo di istanze verrà configurato automaticamente in base alle istanze sempre pronte richieste dalle app del piano. Ad esempio, se l'app A richiede cinque istanze sempre pronte e l'app B richiede due istanze sempre pronte nello stesso piano, le dimensioni minime del piano verranno calcolate come cinque. L'app A verrà eseguita in tutti i 5 e l'app B verrà eseguita solo su 2.

> [!IMPORTANT]
> Viene addebitato il costo di ogni istanza allocata nel numero minimo di istanze indipendentemente dall'esecuzione delle funzioni.

Nella maggior parte dei casi, questo valore minimo calcolato automaticamente è sufficiente. Tuttavia, il ridimensionamento oltre il minimo si verifica al massimo sforzo. È possibile, anche se improbabile, che in un determinato momento la scalabilità orizzontale venga posticipata se non sono disponibili istanze aggiuntive. Impostando un valore minimo superiore al minimo calcolato automaticamente, le istanze vengono riservate in anticipo rispetto alla scalabilità orizzontale.

L'aumento del valore minimo calcolato per un piano può essere eseguito usando l'interfaccia della riga di comando di Azure.

```azurecli-interactive
az functionapp plan update -g <resource_group> -n <premium_plan_name> --min-instances <desired_min_instances>
```

### <a name="available-instance-skus"></a>SKU di istanze disponibili

Quando si crea o si ridimensiona il piano, è possibile scegliere tra tre dimensioni delle istanze. Verrà addebitato il numero totale di core e di memoria di cui viene effettuato il provisioning, al secondo che ogni istanza viene allocata all'utente. L'app può essere ridimensionata automaticamente a più istanze in base alle esigenze.

|SKU|Core|Memoria|Archiviazione|
|--|--|--|--|
|EP1|1|3,5 GB|250 GB|
|EP2|2|7 GB|250 GB|
|EP3|4|14 GB|250 GB|

### <a name="memory-usage-considerations"></a>Considerazioni sull'utilizzo della memoria

L'esecuzione in un computer con una maggiore quantità di memoria non implica sempre che l'app per le funzioni usi tutta la memoria disponibile.

Ad esempio, un'app per le funzioni JavaScript è vincolata dal limite di memoria predefinito in Node.js. Per aumentare il limite di memoria fisso, aggiungere l'impostazione dell'app `languageWorkers:node:arguments` con un valore di `--max-old-space-size=<max memory in MB>` .

## <a name="region-max-scale-out"></a>Scale Out area massima

Di seguito sono riportati i valori di scalabilità orizzontale massimi attualmente supportati per un singolo piano in ogni area e configurazione del sistema operativo. Per richiedere un aumento, è possibile aprire un ticket di supporto.

Vedere l'intera disponibilità a livello di area delle funzioni nel [sito Web di Azure](https://azure.microsoft.com/global-infrastructure/services/?products=functions).

|Region| Windows | Linux |
|--| -- | -- |
|Australia centrale| 100 | Non disponibile |
|Australia centrale 2| 100 | Non disponibile |
|Australia orientale| 100 | 20 |
|Australia sud-orientale | 100 | 20 |
|Brasile meridionale| 100 | 20 |
|Canada centrale| 100 | 20 |
|Stati Uniti centrali| 100 | 20 |
|Cina orientale 2| 100 | 20 |
|Cina settentrionale 2| 100 | 20 |
|Asia orientale| 100 | 20 |
|Stati Uniti orientali | 100 | 20 |
|Stati Uniti orientali 2| 100 | 20 |
|Francia centrale| 100 | 20 |
|Germania centro-occidentale| 100 | Non disponibile |
|Giappone orientale| 100 | 20 |
|Giappone occidentale| 100 | 20 |
|Corea centrale| 100 | 20 |
|Corea meridionale| Non disponibile | 20 |
|Stati Uniti centro-settentrionali| 100 | 20 |
|Europa settentrionale| 100 | 20 |
|Norvegia orientale| 100 | 20 |
|Stati Uniti centro-meridionali| 100 | 20 |
|India meridionale | 100 | Non disponibile |
|Asia sud-orientale| 100 | 20 |
|Svizzera settentrionale| 100 | Non disponibile |
|Svizzera occidentale| 100 | Non disponibile |
|Regno Unito meridionale| 100 | 20 |
|Regno Unito occidentale| 100 | 20 |
|USGov Arizona| 100 | 20 |
|USGov Virginia| 100 | 20 |
|USNat est| 100 | Non disponibile |
|USNat ovest| 100 | Non disponibile |
|Europa occidentale| 100 | 20 |
|India occidentale| 100 | 20 |
|Stati Uniti centro-occidentali| 100 | 20 |
|Stati Uniti occidentali| 100 | 20 |
|Stati Uniti occidentali 2| 100 | 20 |

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sulle opzioni di hosting di funzioni di Azure](functions-scale.md)
