---
title: Aggiornare un gruppo di contenitori
description: Informazioni su come aggiornare i contenitori in esecuzione nei gruppi di contenitori in Istanze di Azure Container.
ms.topic: article
ms.date: 04/17/2020
ms.openlocfilehash: cbb2e830490d2591645b8156ee830856da0f9049
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786964"
---
# <a name="update-containers-in-azure-container-instances"></a>Aggiornare i contenitori in Istanze di Azure Container

Durante il normale funzionamento delle istanze di contenitore, potrebbe essere necessario aggiornare i contenitori in esecuzione in un gruppo [di contenitori](./container-instances-container-groups.md). Ad esempio, è possibile aggiornare una proprietà, ad esempio una versione dell'immagine, un nome DNS o una variabile di ambiente, oppure aggiornare una proprietà in un contenitore la cui applicazione si è arrestata in modo anomalo.

Aggiornare i contenitori in un gruppo di contenitori in esecuzione ridistribuendo un gruppo esistente con almeno una proprietà modificata. Quando si aggiorna un gruppo di contenitori, tutti i contenitori in esecuzione nel gruppo vengono riavviati sul posto, in genere nello stesso host contenitore sottostante.

> [!NOTE]
> Non è possibile aggiornare i gruppi di contenitori terminati o eliminati. Dopo che un gruppo di contenitori è terminato (si trova nello stato Succeeded o Failed) o è stato eliminato, il gruppo deve essere distribuito come nuovo. Vedere altre [limitazioni.](#limitations)

## <a name="update-a-container-group"></a>Aggiornare un gruppo di contenitori

Per aggiornare un gruppo di contenitori esistente:

* Eseguire il comando create (o usare il portale di Azure) e specificare il nome di un gruppo esistente 
* Modificare o aggiungere almeno una proprietà del gruppo che supporta l'aggiornamento durante la ridistribuzione. Alcune proprietà [non supportano gli aggiornamenti](#properties-that-require-container-delete).
* Impostare altre proprietà con i valori specificati in precedenza. Se non si imposta un valore per una proprietà, viene ripristinato il valore predefinito.

> [!TIP]
> Un [file YAML consente](./container-instances-container-groups.md#deployment) di gestire la configurazione della distribuzione di un gruppo di contenitori e fornisce un punto di partenza per distribuire un gruppo aggiornato. Se è stato usato un metodo diverso per creare il gruppo, è possibile esportare la configurazione in YAML usando [az container export][az-container-export], 

### <a name="example"></a>Esempio

L'esempio di interfaccia della riga di comando di Azure seguente aggiorna un gruppo di contenitori con una nuova etichetta del nome DNS. Poiché la proprietà dell'etichetta del nome DNS del gruppo può essere aggiornata, il gruppo di contenitori viene ridistribuito e i relativi contenitori vengono riavviati.

Distribuzione iniziale con l'etichetta del nome DNS *myapplication-staging*:

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

Aggiornare il gruppo di contenitori con una nuova etichetta del nome DNS, *myapplication,* e impostare le proprietà rimanenti con i valori usati in precedenza:

```azurecli-interactive
# Update DNS name label (restarts container), leave other properties unchanged
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication
```

## <a name="update-benefits"></a>Vantaggi dell'aggiornamento

Il vantaggio principale dell'aggiornamento di un gruppo di contenitori esistente è una distribuzione più veloce. Quando si ridistribuisce un gruppo di contenitori esistente, viene eseguito il pull dei relativi livelli dell'immagine del contenitore da quelli memorizzati nella cache per la distribuzione precedente. Invece di eseguire il pull di tutti i livelli dell'immagine aggiornata dal registro come avviene con le nuove distribuzioni, viene eseguito il pull dei soli livelli modificati (se presenti).

Le applicazioni basate su immagini del contenitore di dimensioni maggiori, ad esempio Windows Server Core, possono ottenere un miglioramento significativo della velocità di distribuzione quando si esegue l'aggiornamento anziché eliminarle ed eseguire nuovamente la distribuzione.

## <a name="limitations"></a>Limitazioni

* Non tutte le proprietà di un gruppo di contenitori supportano gli aggiornamenti. Per modificare alcune proprietà di un gruppo di contenitori è necessario eliminare e ridistribuire il gruppo. Vedere [Proprietà che richiedono l'eliminazione del contenitore.](#properties-that-require-container-delete)
* Quando si aggiorna un gruppo di contenitori, tutti i contenitori in esso presenti vengono riavviati. È possibile eseguire un aggiornamento o un riavvio sul posto di un contenitore specifico in un gruppo multi-contenitore.
* L'indirizzo IP di un gruppo di contenitori viene in genere mantenuto tra gli aggiornamenti, ma non è garantito che rimanga invariato. Se distribuito nello stesso host sottostante, il gruppo di contenitori mantiene il proprio indirizzo IP. Anche se raramente, esistono alcuni eventi interni di Azure che possono causare la ridistribuzione in un host diverso. Per risolvere questo problema, è consigliabile usare un'etichetta del nome DNS per le istanze del contenitore.
* Non è possibile aggiornare i gruppi di contenitori terminati o eliminati. Dopo l'arresto o l'eliminazione di un gruppo di contenitori (con stato Terminato), il gruppo viene distribuito come nuovo. 

## <a name="properties-that-require-container-delete"></a>Proprietà che richiedono l'eliminazione del contenitore

Non tutte le proprietà del gruppo di contenitori possono essere aggiornate. Ad esempio, per modificare i criteri di riavvio di un contenitore, è necessario prima eliminare il gruppo di contenitori e quindi crearlo di nuovo.

Le modifiche a queste proprietà richiedono l'eliminazione del gruppo di contenitori prima della ridistribuzione:

* Tipo di sistema operativo
* Risorse CPU, memoria o GPU
* Criteri di riavvio
* Profilo di rete

Quando si elimina un gruppo di contenitori e lo si ricrea, questo non viene "ridistribuito", ma creato nuovo. Il pull di tutti i livelli dell'immagine viene eseguito dai dati aggiornati del registro, non da quelli memorizzati nella cache da una distribuzione precedente. L'indirizzo IP del contenitore può cambiare anche a causa della distribuzione in un altro host sottostante.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è menzionato più volte il **gruppo di contenitori**. Ogni contenitore di Istanze di Azure Container viene distribuito in un gruppo di contenitori, che può contenere più contenitori.

[Gruppi di contenitori in Istanze di Azure Container](./container-instances-container-groups.md)

[Distribuire un gruppo multi-contenitore](container-instances-multi-container-group.md)

[Arrestare o avviare manualmente i contenitori in Istanze di Azure Container](container-instances-stop-start.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[azure-cli-install]: /cli/azure/install-azure-cli
[az-container-export]: /cli/azure/container#az_container_export
