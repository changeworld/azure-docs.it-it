---
title: Quote e limiti del servizio
description: Informazioni sui vincoli, limiti e quote di Azure Batch predefiniti e su come richiedere incrementi di quota
ms.topic: conceptual
ms.date: 01/28/2021
ms.custom: seodec18
ms.openlocfilehash: 433272c76b9ff27d9cad542cf65a8ec0d8fc0378
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99052381"
---
# <a name="batch-service-quotas-and-limits"></a>Quote e limiti del servizio Batch

Come con altri servizi di Azure, sono previsti limiti per determinate risorse associate al servizio Batch. Molti di questi limiti sono quote predefinite applicate da Azure a livello di account o di sottoscrizione.

Tenere presenti queste quote quando si progettano i carichi di lavoro di Batch e se ne aumentano le prestazioni. Se, ad esempio, il pool non raggiunge il numero previsto di nodi di calcolo specificato, potrebbe essere stato raggiunto il limite di quota di core per l'account Batch.

È possibile eseguire più carichi di lavoro Batch in un solo account Batch o distribuire i carichi di lavoro tra gli account Batch nella stessa sottoscrizione, ma in aree di Azure diverse.

Se si prevede di eseguire carichi di lavoro di produzione in Batch, potrebbe essere necessario incrementare il valore predefinito di una o più quote. Per aumentare una quota, è possibile aprire una [richiesta di assistenza clienti](#increase-a-quota) online gratuitamente.

## <a name="resource-quotas"></a>Quote di risorse

Una quota è un limite, non una garanzia di capacità. Se si hanno esigenze di capacità su larga scala, contattare il supporto di Azure.

Si noti inoltre che le quote non sono valori garantiti. Le quote possono variare in base alle modifiche apportate al servizio Batch o a una richiesta utente per modificare un valore di quota.

[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## <a name="core-quotas"></a>Quote Core

### <a name="cores-quotas-in-batch-service-mode"></a>Quote core in modalità servizio batch

Sono disponibili quote core per ogni serie di VM supportate da batch e visualizzate nella pagina **quote** del portale. I limiti di quota della serie VM possono essere aggiornati con una richiesta di supporto, come descritto di seguito. Per i nodi dedicati, batch impone un limite di quota di core per ogni serie di VM, oltre a un limite di quota Core totale per l'intero account batch. Per i nodi con priorità bassa, batch impone solo una quota di core totale per l'account batch senza alcuna distinzione tra diverse serie di macchine virtuali.

### <a name="cores-quotas-in-user-subscription-mode"></a>Quote di core in modalità di sottoscrizione utente

Se è stato creato un [account batch](accounts.md) con la modalità di allocazione pool impostata su **sottoscrizione utente**, le VM batch e altre risorse vengono create direttamente nella sottoscrizione quando un pool viene creato o ridimensionato. Le quote di Azure Batch Core non si applicano e le quote della sottoscrizione per i core di calcolo regionali, i core di calcolo per serie e altre risorse vengono usate e applicate.

Per altre informazioni sulle quote, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="pool-size-limits"></a>Limiti delle dimensioni del pool

I limiti delle dimensioni del pool vengono impostati dal servizio Batch. A differenza delle [quote di risorse](#resource-quotas), questi valori non possono essere modificati. Solo i pool con comunicazione tra nodi e immagini personalizzate presentano limitazioni diverse dalla quota standard.

| **Risorsa** | **Limite Massimo** |
| --- | --- |
| **Nodi di calcolo nel [pool in cui è abilitata la comunicazione tra i nodi](batch-mpi.md)**  ||
| Modalità di allocazione pool del servizio Batch | 100 |
| Modalità di allocazione pool di sottoscrizione batch | 80 |
| **Nodi di calcolo nel [pool creato con una risorsa immagine gestita](batch-custom-images.md)** <sup>1</sup> ||
| Nodi dedicati | 2000 |
| Nodi a priorità bassa | 1000 |

<sup>1</sup> Per pool non abilitati alla comunicazione tra i nodi.

## <a name="other-limits"></a>Altri limiti

Questi limiti aggiuntivi vengono impostati dal servizio batch. A differenza delle [quote di risorse](#resource-quotas), questi valori non possono essere modificati.

| **Risorsa** | **Limite Massimo** |
| --- | --- |
| [Attività simultanee](batch-parallel-node-tasks.md) per nodo di calcolo | 4 x numero di core del nodo |
| [Applicazioni](batch-application-packages.md) per account Batch | 20 |
| Pacchetti dell'applicazione per applicazione | 40 |
| Pacchetti dell'applicazione per pool | 10 |
| Durata massima dell'attività | 180 giorni<sup>1</sup> |
| [Montaggi](virtual-file-mount.md) per nodo di calcolo | 10 |
| Certificati per pool | 12 |

<sup>1</sup> La durata massima di un'attività, da quando viene aggiunta al processo a quando viene completata, è di 180 giorni. Le attività completate vengono conservate per sette giorni. I dati per le attività non completate entro la durata massima non sono accessibili.

## <a name="view-batch-quotas"></a>Visualizzare le quote Batch

Per visualizzare le quote dell'account Batch nel [portale di Azure](https://portal.azure.com):

1. Selezionare **Account Batch** e quindi selezionare l'account Batch di interesse.
1. Selezionare **Quote** nel menu dell'account Batch.
1. Visualizzare le quote attualmente applicate all'account Batch.

:::image type="content" source="./media/batch-quota-limit/account-quota-portal.png" alt-text="Screenshot che mostra le quote dell'account batch nel portale di Azure.":::

## <a name="increase-a-quota"></a>Aumentare una quota

È possibile richiedere un aumento di quota per la sottoscrizione o l'account Batch usando il [portale di Azure](https://portal.azure.com). Il tipo di aumento delle quote dipende dalla modalità di allocazione del pool dell'account Batch. Per richiedere un aumento di quota, è necessario includere la serie di VM per cui si vuole aumentare la quota. L'aumento di quota viene applicato a tutte le serie di macchine virtuali.

1. Selezionare il riquadro **Guida e supporto** nel dashboard del portale o il punto interrogativo ( **?** ) nell'angolo superiore destro del portale.
1. Selezionare **Nuova richiesta di supporto** > **Informazioni di base**.
1. In **Nozioni di base**:

    1. **Tipo di problema** > **Limiti del servizio e della sottoscrizione (quote)**

    1. Selezionare la propria sottoscrizione.

    1. **Tipo di quota** > **Batch**

       Selezionare **Avanti**.

1. In **Dettagli**:

    1. In **Specifica dettagli** specificare la posizione, il tipo di quota e l'account Batch.

       :::image type="content" source="media/batch-quota-limit/quota-increase.png" alt-text="Screenshot della schermata dei dettagli della quota quando si richiede un aumento della quota.":::

       I tipi di quota includono:

       * **Per account Batch**  
         Valori specifici di un singolo account Batch, inclusi i core dedicati e per priorità bassa, nonché il numero di processi e pool.

       * **Per area**  
         Valori che si applicano a tutti gli account Batch in un'area e che includono il numero di account Batch per area per sottoscrizione.

       La quota per priorità bassa è un singolo valore in tutte le serie di macchine virtuali. Se sono necessari SKU vincolati, selezionare **ore per priorità bassa** e includere le famiglie di macchine virtuali da richiedere.

    1. Selezionare una **Gravità** in base all'[impatto sull'attività aziendale](https://aka.ms/supportseverity).

       Selezionare **Avanti**.

1. In **Informazioni contatto**:

    1. Selezionare il **metodo di contatto preferito**.

    1. Verificare e immettere i dettagli di contatto richiesti.

       Selezionare **Crea** per inviare la richiesta di supporto.

Dopo aver inviato la richiesta di supporto, si verrà contattati dal supporto tecnico di Azure. Le richieste di quota possono essere completate entro pochi minuti o un massimo di due giorni lavorativi.

## <a name="related-quotas-for-vm-pools"></a>Quote correlate per i pool di macchine virtuali

I pool di Batch nella configurazione di macchine virtuali distribuita in una rete virtuale di Azure allocano automaticamente risorse di rete di Azure aggiuntive. Le risorse seguenti sono necessarie per ogni 50 nodi del pool in una rete virtuale:

- Un [gruppo di sicurezza di rete](../virtual-network/network-security-groups-overview.md#network-security-groups)
- Un [indirizzo IP pubblico](../virtual-network/public-ip-addresses.md)
- Un [bilanciamento del carico](../load-balancer/load-balancer-overview.md)

Queste risorse vengono allocate nella sottoscrizione che contiene la rete virtuale fornita durante la creazione del pool di Batch. Queste risorse sono limitate in base alle [quote delle risorse](../azure-resource-manager/management/azure-subscription-service-limits.md) della sottoscrizione. Se sono previste distribuzioni di pool di grandi dimensioni in una rete virtuale, controllare le quote della sottoscrizione per queste risorse. Se necessario, richiedere un aumento selezionando **Guida e supporto** nel portale di Azure.

## <a name="next-steps"></a>Passaggi successivi

* [Creare un account Azure Batch usando il portale di Azure](batch-account-create-portal.md).
* Informazioni sul [Flusso di lavoro del servizio Batch e risorse primarie](batch-service-workflow-features.md), ad esempio pool, nodi, processi e attività.
* Informazioni su [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-resource-manager/management/azure-subscription-service-limits.md).

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.png
[quota_increase]: ./media/batch-quota-limit/quota-increase.png