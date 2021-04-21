---
title: Procedure consigliate per il registro contenitori
description: Informazioni su come usare Registro Azure Container in modo efficace seguendo queste procedure consigliate.
ms.topic: article
ms.date: 01/07/2021
ms.openlocfilehash: 0811cc4a5bffc21ffba19e64a3887eab6bc36fbb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784138"
---
# <a name="best-practices-for-azure-container-registry"></a>Procedure consigliate per Registro Azure Container

Seguendo queste procedure consigliate, è possibile ottimizzare le prestazioni e l'uso conveniente del registro privato in Azure per archiviare e distribuire immagini del contenitore e altri artefatti.

Per informazioni di base sui concetti relativi al Registro di sistema, vedere Informazioni su [registri, repository e immagini.](container-registry-concepts.md) Vedere anche [Suggerimenti per l'assegnazione di tag e il controllo delle versioni delle immagini del contenitore](container-registry-image-tag-version.md) per le strategie per l'assegnazione di tag e versioni delle immagini nel registro. 

## <a name="network-close-deployment"></a>Distribuzione in reti vicine

Creare il registro contenitori nella stessa area di Azure in cui vengono distribuiti i contenitori. La collocazione del Registro in un'area di rete vicina ai propri host contenitori può ridurre la latenza e i costi.

La distribuzione in reti vicine è una delle ragioni principali per cui si usa un registro contenitori privato. Le immagini di Docker hanno un efficiente [costrutto di livelli](https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/) che consente la distribuzione incrementale. Tuttavia, è necessario che nuovi nodi eseguano il pull di tutti i livelli richiesti per una data immagine. Questo `docker pull` iniziale si può rapidamente aggiungere a più gigabyte. Avere un registro contenitori privato vicino alla propria distribuzione riduce al minimo la latenza di rete.
In aggiunta, tutti i cloud pubblici, Azure incluso, implementano i corrispettivi per il traffico di rete in uscita. Il pull di immagini da un data center a un altro aggiunge corrispettivi di rete in uscita e latenza.

## <a name="geo-replicate-multi-region-deployments"></a>Eseguire la replica geografica della distribuzione in più aree

Usare la funzione [replica geografica](container-registry-geo-replication.md) di Registro Azure Container se si distribuiscono contenitori a più aree. Se si servono clienti globali da data center locali o il proprio team di sviluppo è in più posizioni, è possibile semplificare la gestione del registro e ridurre al minimo la latenza eseguendo la replica geografica del proprio registro. È anche possibile configurare [webhook](container-registry-webhook.md) a livello di regione per notificare gli eventi in repliche specifiche, ad esempio quando viene inserito il push delle immagini.

La replica geografica è disponibile con [i](container-registry-skus.md) registri Premium. Per capire come usare la replica geografica, vedere il tutorial in tre parti [Preparare un'istanza di Registro Azure Container con replica geografica](container-registry-tutorial-prepare-registry.md).

## <a name="maximize-pull-performance"></a>Ottimizzare le prestazioni pull

Oltre a posizionare le immagini vicino alle distribuzioni, le caratteristiche delle immagini stesse possono influire sulle prestazioni pull.

* **Dimensioni immagine:** ridurre al minimo le dimensioni delle immagini rimuovendo i livelli non [necessari](container-registry-concepts.md#manifest) o riducendo le dimensioni dei livelli. Un modo per ridurre le dimensioni dell'immagine consiste nell'usare l'approccio di compilazione [Docker](https://docs.docker.com/develop/develop-images/multistage-build/) in più fasi per includere solo i componenti di runtime necessari. 

  Controllare anche se l'immagine può includere un'immagine del sistema operativo di base più chiara. Se si usa un ambiente di distribuzione come Istanze di Azure Container che memorizza nella cache determinate immagini di base, verificare se è possibile scambiare un livello immagine con una delle immagini memorizzate nella cache. 
* **Numero di livelli:** bilanciare il numero di livelli usati. Se si ha troppo poco, non è possibile trarre vantaggio dal riutilizzo dei livelli e dalla memorizzazione nella cache nell'host. Troppi e l'ambiente di distribuzione impiega più tempo per il pull e la decompressione. I livelli da cinque a 10 sono ottimali.

Scegliere anche un [livello di servizio di](container-registry-skus.md) Registro Azure Container che soddisfi le esigenze di prestazioni. Il livello Premium offre la massima larghezza di banda e la frequenza più elevata di operazioni simultanee di lettura e scrittura in caso di distribuzioni con volumi elevati.

## <a name="repository-namespaces"></a>Spazi dei nomi dell'archivio

Usando gli spazi dei nomi del repository, è possibile consentire la condivisione di un singolo registro tra più gruppi all'interno dell'organizzazione. I registri possono essere condivisi tra le distribuzioni e i team. Registro Azure Container supporta spazi dei nomi annidati, abilitando l'isolamento in gruppo. Tuttavia, il Registro di sistema gestisce tutti i repository in modo indipendente, non come gerarchia.

Si considerino ad esempio i tag delle immagini del contenitore seguenti: Le immagini che vengono usate a livello aziendale, ad esempio `aspnetcore`, vengono inserite nello spazio dei nomi radice, mentre le immagini del contenitore di proprietà dei gruppi di Prodotti e di Marketing usano spazi dei nomi dedicati.

- *contoso.azurecr.io/aspnetcore:2.0*
- *contoso.azurecr.io/products/widget/web:1*
- *contoso.azurecr.io/products/bettermousetrap/refundapi:12.3*
- *contoso.azurecr.io/marketing/2017-fall/concertpromotions/campaign:218.42*

## <a name="dedicated-resource-group"></a>Gruppo di risorse dedicato

Poiché i registri contenitori sono risorse usate in più host contenitori, un registro deve trovarsi nel relativo gruppo di risorse.

Anche se è possibile sperimentare con un tipo di host specifico, ad esempio [Istanze di Azure Container](../container-instances/container-instances-overview.md), è probabile che si voglia eliminare l'istanza del contenitore al termine. Tuttavia, è anche possibile mantenere la raccolta di immagini inserita in Registro Azure Container. Posizionando il registro nel suo gruppo di risorse, si riduce al minimo il rischio di eliminare accidentalmente la raccolta di immagini nel registro quando si elimina il gruppo di risorse delle istanze del contenitore.

## <a name="authentication-and-authorization"></a>Autenticazione e autorizzazione

Quando si esegue l'autenticazione con Registro Azure Container, esistono due scenari principali: autenticazione singola e autenticazione di servizio (o "headless"). La tabella seguente fornisce una breve panoramica di questi scenari e il metodo di autenticazione consigliato per ognuno.

| Type | Scenario di esempio | Metodo consigliato |
|---|---|---|
| Identità singola | Uno sviluppatore che esegue il pull o il push di immagini dal computer di sviluppo. | [az acr login](/cli/azure/acr#az_acr_login) |
| Identità headless/del servizio | Pipeline di compilazione e distribuzione in cui l'utente non è direttamente coinvolto. | [Entità servizio](container-registry-authentication.md#service-principal) |

Per informazioni dettagliate su questi e altri scenari di autenticazione Registro Azure Container, vedere Eseguire l'autenticazione [con un Registro Azure Container.](container-registry-authentication.md)

Registro Azure Container supporta le procedure di sicurezza nell'organizzazione per distribuire compiti e privilegi a identità diverse. Usando [il controllo degli accessi in base](container-registry-roles.md)al ruolo, assegnare le autorizzazioni appropriate a diversi utenti, entità servizio o altre identità che eseguono operazioni del Registro di sistema diverse. Ad esempio, assegnare autorizzazioni push a un'entità servizio usata in una pipeline di compilazione e assegnare autorizzazioni pull a un'identità diversa usata per la distribuzione. Creare [token per](container-registry-repository-scoped-permissions.md) l'accesso con granularità fine e limitato nel tempo a repository specifici.

## <a name="manage-registry-size"></a>Gestire le dimensioni del registro      

I vincoli di [][container-registry-skus] archiviazione di ogni livello di servizio del registro contenitori sono concepiti per essere allineati a uno scenario tipico: **Basic** per iniziare, **Standard** per la maggior parte delle applicazioni di produzione e **Premium** per le prestazioni su vasta scala e la replica [geografica.][container-registry-geo-replication] Per tutta la durata del registro, è necessario gestirne le dimensioni eliminando periodicamente il contenuto non usato.

Usare il comando dell'interfaccia della riga di comando di Azure [az acr show-usage][az-acr-show-usage] per visualizzare le dimensioni correnti del registro:

```azurecli
az acr show-usage --resource-group myResourceGroup --name myregistry --output table
```

```output
NAME      LIMIT         CURRENT VALUE    UNIT
--------  ------------  ---------------  ------
Size      536870912000  185444288        Bytes
Webhooks  100                            Count
```

È anche possibile trovare la risorsa di archiviazione corrente usata nella sezione **Panoramica** del registro nel portale di Azure:

![Informazioni sull'uso del registro nel portale di Azure][registry-overview-quotas]

### <a name="delete-image-data"></a>Eliminare i dati di immagini

Registro Azure Container supporta diversi metodi per l'eliminazione di dati di immagini dal registro contenitori. È possibile eliminare le immagini in base al tag o al digest del manifesto oppure eliminare un intero repository.

Per informazioni dettagliate sull'eliminazione dei dati di immagini dal registro, tra cui immagini senza tag (a volte indicate come "tralasciate" o "orfane"), vedere [Eliminare le immagini del contenitore in Registro Azure Container](container-registry-delete.md).

## <a name="next-steps"></a>Passaggi successivi

Registro Azure Container è disponibile in diversi livelli (detti anche SKU) che forniscono funzionalità diverse. Per informazioni dettagliate sui livelli di servizio disponibili, vedere [Livelli di servizio del Registro Azure Container](container-registry-skus.md).

Per consigli su come migliorare il livello di sicurezza dei registri contenitori, vedere Baseline di [sicurezza di Azure per](security-baseline.md)Registro Azure Container .

<!-- IMAGES -->
[delete-repository-portal]: ./media/container-registry-best-practices/delete-repository-portal.png
[registry-overview-quotas]: ./media/container-registry-best-practices/registry-overview-quotas.png

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az_acr_repository_delete
[az-acr-show-usage]: /cli/azure/acr#az_acr_show_usage
[azure-cli]: /cli/azure
[azure-portal]: https://portal.azure.com
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-skus]: container-registry-skus.md