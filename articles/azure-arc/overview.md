---
title: Panoramica di Azure Arc
description: Informazioni su Azure Arc e su come può essere usato dai clienti per abilitare la gestione e la governance delle risorse ibride con altri servizi e funzionalità di Azure.
ms.date: 03/02/2021
ms.topic: overview
ms.openlocfilehash: 33c9d6ca87c3d8d2d8920ff429902f5876bbdc59
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101650193"
---
# <a name="azure-arc-overview"></a>Panoramica di Azure Arc

Oggi le aziende faticano a controllare e gestire ambienti sempre più complessi. Questi ambienti si estendono tra diversi data center, più cloud e reti perimetrali. Ogni ambiente e cloud dispone di un proprio set di strumenti di gestione indipendenti che è necessario apprendere e usare.

Parallelamente, i nuovi modelli operativi DevOps e ITOps sono difficili da implementare, perché gli strumenti esistenti non includono il supporto per i nuovi modelli nativi del cloud.

Azure Arc semplifica la governance e la gestione grazie a una piattaforma di gestione coerente multi-cloud e locale. Azure Arc consente di gestire l'intero ambiente in un unico riquadro, proiettando le risorse esistenti in Azure Resource Manager. È ora possibile gestire macchine virtuali, cluster Kubernetes e database come se fossero in esecuzione in Azure. Indipendentemente dalla posizione di tali risorse, è possibile usare i servizi e le funzionalità di gestione di Azure già note. Con Azure Arc è possibile continuare a usare ITOps tradizionali, introducendo allo stesso tempo procedure DevOps per supportare i nuovi modelli nativi del cloud nel proprio ambiente.

:::image type="content" source="./media/overview/azure-arc-control-plane.png" alt-text="Diagramma del piano di controllo di gestione di Azure Arc" border="false":::

Attualmente, Azure Arc consente di gestire i tipi di risorse seguenti ospitati all'esterno di Azure:

* Server, sia computer fisici che macchine virtuali che eseguono Windows o Linux.
* Cluster Kubernetes, che supportano più distribuzioni di Kubernetes.
* Servizi dati di Azure, ovvero il database SQL di Azure e PostgreSQL Hyperscale.

## <a name="what-does-azure-arc-deliver"></a>Che cosa offre Azure Arc?

Le funzionalità chiave di Azure Arc includono:

* Implementazione di funzionalità coerenti per inventario, gestione, governance e sicurezza dei server nell'ambiente.

* Configurazione di [estensioni della macchina virtuale di Azure](./servers/manage-vm-extensions.md) per usare i servizi di gestione di Azure per il monitoraggio, la protezione e l'aggiornamento die server.

* Gestione e governance dei cluster Kubernetes su larga scala.

* Usare GitOps per distribuire la configurazione in uno o più cluster da repository git.

*  Conformità e configurazione a zero touch per i cluster Kubernetes usando criteri di Azure.

* Esegui i servizi dati di Azure in qualsiasi ambiente Kubernetes come se fosse in esecuzione in Azure, in particolare Azure SQL Istanza gestita e la scalabilità di database di Azure per PostgreSQL, con vantaggi come aggiornamenti, aggiornamenti, sicurezza e monitoraggio. Usa la scalabilità elastica e applica gli aggiornamenti senza tempi di inattività dell'applicazione, anche senza connessione continua ad Azure

* Esperienza unificata che consente di visualizzare le risorse con abilitazione di Azure Arc sia che si usi il portale di Azure, l'interfaccia della riga di comando di Azure, Azure PowerShell o l'API REST di Azure.

## <a name="how-much-does-azure-arc-cost"></a>Quanto costa Azure Arc?

Ecco i dettagli relativi ai prezzi per le funzionalità attualmente disponibili con Azure Arc.

### <a name="arc-enabled-servers"></a>Server con abilitazione di Azure Arc

Le funzionalità del piano di controllo di Azure Arc seguenti sono offerte senza costi aggiuntivi:

* Organizzazione delle risorse tramite i gruppi di gestione di Azure e tag.g.

* Ricerca e indicizzazione tramite Azure Resource Graph.

* Accesso e sicurezza tramite controllo degli accessi in base al ruolo e sottoscrizioni di Azure.

* Ambienti e automazione tramite i modelli e le estensioni.

* Gestione degli aggiornamenti

Tutti i servizi di Azure usati nei server con abilitazione di Arc, ad esempio Centro sicurezza di Azure o Monitoraggio di Azure, verranno addebitati in base ai prezzi del relativo servizio. Per altre informazioni, vedere la [pagina dei prezzi di Azure](https://azure.microsoft.com/pricing/).

### <a name="azure-arc-enabled-kubernetes"></a>Kubernetes con abilitazione di Azure Arc

Tutti i servizi di Azure usati in Arc abilitato Kubernetes, ad esempio il Centro sicurezza di Azure o il monitoraggio di Azure, verranno addebitati in base ai prezzi per il servizio. Per altre informazioni sui prezzi per le configurazioni in Azure Arc abilitato Kubernetes, vedere la [pagina dei prezzi di Azure](https://azure.microsoft.com/pricing/).

### <a name="azure-arc-enabled-data-services"></a>Servizi dati con abilitazione di Azure Arc

Nella fase di anteprima corrente, i servizi dati abilitati per Azure Arc sono offerti senza costi aggiuntivi.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sui server con abilitazione di Arc, vedere la [panoramica](./servers/overview.md) seguente

* Per altre informazioni su Kubernetes con abilitazione di Arc, vedere la [panoramica](./kubernetes/overview.md) seguente

* Per altre informazioni sui servizi dati con abilitazione di Arc, vedere la [panoramica](https://azure.microsoft.com/services/azure-arc/hybrid-data-services/) seguente

* Per sperimentare i servizi abilitati per Arc, vedere il [modello di verifica Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/)
