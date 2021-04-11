---
title: Panoramica di diagnostica del servizio Azure Kubernetes (AKS)
description: Informazioni sui cluster di diagnostica automatica nel servizio Azure Kubernetes.
services: container-service
author: yunjchoi
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: yunjchoi
ms.openlocfilehash: ee11221e5484a796b8dbbcb10a323288d3e74756
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011559"
---
# <a name="azure-kubernetes-service-diagnostics-preview-overview"></a>Panoramica di diagnostica del servizio Kubernetes di Azure (anteprima)

La risoluzione dei problemi del cluster di Azure Kubernetes Service (AKS) svolge un ruolo importante nella gestione del cluster, soprattutto se il cluster esegue carichi di lavoro cruciali. Il servizio di diagnostica AKS è un'esperienza intuitiva e autonoma che:
* Consente di identificare e risolvere i problemi del cluster. 
* È nativo del cloud.
* Non richiede alcun costo aggiuntivo per la configurazione o la fatturazione.

Questa funzionalità è ora disponibile in anteprima pubblica. 

## <a name="open-aks-diagnostics"></a>Apri diagnostica AKS

Per accedere alla diagnostica AKS:

1. Passare al cluster Kubernetes nel [portale di Azure](https://portal.azure.com).
1. Fare clic su **diagnostica e risoluzione dei problemi** nel percorso di spostamento a sinistra per aprire la diagnostica AKS.
1. Scegliere una categoria che descriva meglio il problema del cluster, ad esempio _problemi del nodo del cluster_, da:
    * Usando le parole chiave nel riquadro della Home page.
    * Digitando una parola chiave che descrive meglio il problema nella barra di ricerca.

![Home page](./media/concepts-diagnostics/aks-diagnostics-homepage.png)

## <a name="view-a-diagnostic-report"></a>Visualizzare un report di diagnostica

Dopo aver fatto clic su una categoria, è possibile visualizzare un report di diagnostica specifico per il cluster. I report di diagnostica richiamano in modo intelligente eventuali problemi nel cluster con icone di stato. È possibile eseguire il drill-down in ogni argomento facendo clic su **altre informazioni** per visualizzare una descrizione dettagliata di:
* Problemi
* Azioni consigliate
* Collegamenti a docs utili
* Correlate-metriche
* Registrazione dei dati 

I report di diagnostica vengono generati in base allo stato corrente del cluster dopo l'esecuzione di vari controlli. Possono essere utili per individuare il problema del cluster e comprendere i passaggi successivi per risolvere il problema.

![Report di diagnostica](./media/concepts-diagnostics/diagnostic-report.png)

![Report di diagnostica espanso](./media/concepts-diagnostics/node-issues.png)

## <a name="cluster-insights"></a>Informazioni dettagliate sul cluster

In **cluster Insights** sono disponibili i seguenti controlli di diagnostica.

### <a name="cluster-node-issues"></a>Problemi del nodo del cluster

Problemi del nodo del cluster controlla la presenza di problemi correlati al nodo che determinano un comportamento imprevisto del cluster.

- Problemi di conformità dei nodi
- Errori del nodo
- Risorse insufficienti
- Configurazione IP mancante nel nodo
- Errori CNI nodo
- Nodo non trovato
- Spegnimento del nodo
- Errore di autenticazione del nodo
- Nodo Kube-proxy non aggiornato

### <a name="create-read-update--delete-crud-operations"></a>Operazioni di creazione, lettura, aggiornamento & eliminazione (CRUD)

Le operazioni CRUD controllano le operazioni CRUD che provocano problemi nel cluster.

- Errore dell'operazione di eliminazione della subnet in uso
- Errore dell'operazione di eliminazione del gruppo di sicurezza di rete
- Errore dell'operazione di eliminazione della tabella di route in uso
- Errore di provisioning delle risorse a cui si fa riferimento
- Errore dell'operazione di eliminazione dell'indirizzo IP pubblico
- Errore di distribuzione dovuto alla quota di distribuzione
- Errore dell'operazione a causa di criteri dell'organizzazione
- Registrazione della sottoscrizione mancante
- Errore di provisioning dell'estensione VM
- Capacità subnet
- Errore di superamento della quota

### <a name="identity-and-security-management"></a>Gestione delle identità e della sicurezza

Gestione identità e sicurezza rileva gli errori di autenticazione e autorizzazione che impediscono la comunicazione con il cluster.

- Errori di autorizzazione del nodo
- 401 errori
- Errori 403

## <a name="next-steps"></a>Passaggi successivi

* Raccogliere i log per facilitare la risoluzione dei problemi del cluster usando il [periscopio AKS](https://aka.ms/aksperiscope).

* Leggere la [sezione procedure di valutazione](/azure/architecture/operator-guides/aks/aks-triage-practices) della Guida operativa di AKS Day-2.

* Pubblicare domande o commenti e suggerimenti in [UserVoice](https://feedback.azure.com/forums/914020-azure-kubernetes-service-aks) aggiungendo "[diag]" nel titolo.