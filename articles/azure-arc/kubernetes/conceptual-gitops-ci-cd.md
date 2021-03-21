---
title: Flusso di lavoro CI/CD con GitOps-Azure Arc Enabled Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: conceptual
author: tcare
ms.author: tcare
description: Questo articolo fornisce una panoramica concettuale di un flusso di lavoro CI/CD con GitOps
keywords: GitOps, Kubernetes, K8s, Azure, Helm, Arc, AKS, Azure Kubernetes Service, contenitori, CI, CD, Azure DevOps
ms.openlocfilehash: a51a9f2b32f1088cec390dc4d74300a38f37b160
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121780"
---
# <a name="cicd-workflow-using-gitops---azure-arc-enabled-kubernetes"></a>Flusso di lavoro CI/CD con GitOps-Azure Arc Enabled Kubernetes

Le distribuzioni Kubernetes moderne ospitano più applicazioni, cluster e ambienti. Con GitOps è possibile gestire più facilmente queste configurazioni complesse, tenendo traccia dello stato desiderato degli ambienti Kubernetes in modo dichiarativo con git. Usando gli strumenti Git comuni per tenere traccia dello stato del cluster, è possibile aumentare la responsabilità, semplificare l'analisi degli errori e abilitare l'automazione per la gestione degli ambienti.

Questa panoramica concettuale illustra GitOps come realtà nell'intero ciclo di vita delle modifiche dell'applicazione usando Azure Arc, Azure Repos e Azure Pipelines. [Passare a un esempio](#example-workflow) di una singola modifica di applicazione negli ambienti Kubernetes controllati da GitOps.

## <a name="architecture"></a>Architettura

Si consideri un'applicazione distribuita in uno o più ambienti Kubernetes.

![Architettura CI/CD GitOps](./media/gitops-arch.png)

### <a name="application-repo"></a>Repository dell'applicazione
Il repository dell'applicazione contiene il codice dell'applicazione su cui lavorano gli sviluppatori durante il ciclo interno. I modelli di distribuzione dell'applicazione risiedono in questo repository in un formato generico, ad esempio Helm o Kustomize. I valori specifici dell'ambiente non vengono archiviati. Le modifiche apportate a questo repository richiamano una pipeline pull o CI che avvia il processo di distribuzione.
### <a name="container-registry"></a>Registro contenitori
Il registro contenitori include tutte le immagini di prima e di terze parti usate negli ambienti Kubernetes. Contrassegnare le immagini dell'applicazione di terze parti con tag leggibili e il commit Git usati per compilare l'immagine. Memorizza nella cache immagini di terze parti per sicurezza, velocità e resilienza. Impostare un piano per i test tempestivi e l'integrazione degli aggiornamenti della sicurezza. Per ulteriori informazioni, vedere la guida relativa all' [utilizzo e alla manutenzione del contenuto pubblico di ACR](https://docs.microsoft.com/azure/container-registry/tasks-consume-public-content) per un esempio.
### <a name="pr-pipeline"></a>Pipeline PR
Le richieste pull al repository dell'applicazione vengono gestite in modo corretto per l'esecuzione della pipeline di richiesta pull. Questa pipeline esegue le attività di controllo di qualità di base, ad esempio le operazioni di pelucchi e unit test sul codice dell'applicazione. La pipeline verifica l'applicazione e non i modelli Dockerfile e Helm usati per la distribuzione in un ambiente Kubernetes. Le immagini Docker devono essere compilate e testate, ma non inserite. Tenere la durata della pipeline relativamente breve per consentire l'iterazione rapida.
### <a name="ci-pipeline"></a>Pipeline CI
La pipeline dell'applicazione CI esegue tutti i passaggi della pipeline di richiesta pull ed espande i controlli di test e distribuzione. La pipeline può essere eseguita per ogni commit o a cadenza regolare con un gruppo di commit. A questo punto, eseguire il test dell'applicazione troppo lungo per una pipeline di richiesta pull. Eseguire il push delle immagini Docker nella Container Registry dopo la compilazione in preparazione per la distribuzione. Per il modello sostituito è possibile usare un set di valori di test. A questo punto le immagini utilizzate nel runtime del servizio devono essere incorporate, compilate e testate. Nella compilazione CI in particolare, gli artefatti vengono pubblicati affinché il passaggio del CD venga utilizzato in preparazione per la distribuzione.
### <a name="flux"></a>Flux
Flux è un servizio che viene eseguito in ogni cluster ed è responsabile della gestione dello stato desiderato. Il servizio esegue spesso il polling del repository GitOps per le modifiche al cluster e le applica.
### <a name="cd-pipeline"></a>Pipeline CD
La pipeline CD viene attivata automaticamente da compilazioni CI riuscite. USA i modelli pubblicati in precedenza, sostituisce i valori dell'ambiente e apre una richiesta pull al repository GitOps per richiedere una modifica allo stato desiderato di uno o più cluster Kubernetes. Gli amministratori del cluster riesaminano la richiesta di modifica dello stato e approvano il merge nel repository GitOps. La pipeline attende quindi il completamento della richiesta pull, che consente a Flux di selezionare la modifica dello stato.
### <a name="gitops-repo"></a>Repository GitOps
Il repository GitOps rappresenta lo stato desiderato corrente di tutti gli ambienti tra i cluster. Qualsiasi modifica apportata a questo repository viene prelevata dal servizio Flux in ogni cluster e distribuita. Le richieste pull vengono create con modifiche allo stato desiderato, riviste e unite. Queste richieste pull contengono modifiche sia ai modelli di distribuzione sia ai manifesti Kubernetes di rendering risultanti. I manifesti sottoposti a rendering di basso livello consentono un controllo più accurato delle modifiche generalmente non visualizzate a livello di modello.
### <a name="kubernetes-clusters"></a>Cluster Kubernetes
Almeno un cluster Kubernetes abilitato in Azure Arc serve i diversi ambienti richiesti dall'applicazione. Ad esempio, un singolo cluster può gestire un ambiente di sviluppo e di controllo di qualità tramite diversi spazi dei nomi. Un secondo cluster può offrire una separazione più semplice degli ambienti e un controllo più granulare.
## <a name="example-workflow"></a>Esempio di flusso di lavoro
In qualità di sviluppatore di applicazioni, Alice:
* Scrive il codice dell'applicazione.
* Determina come eseguire l'applicazione in un contenitore docker.
* Definisce i modelli che eseguono il contenitore e i servizi dipendenti in un cluster Kubernetes.

Anche se Alice sa che l'applicazione deve essere eseguita in più ambienti, non conosce le impostazioni specifiche per ogni ambiente.

Si supponga che Alice voglia apportare una modifica all'applicazione che modifichi l'immagine Docker usata nel modello di distribuzione dell'applicazione.

1. Alice modifica il modello di distribuzione, lo inserisce in un ramo remoto nel repository dell'applicazione e apre una richiesta pull per la revisione.
2. Alice chiede al team di rivedere la modifica.
    * La pipeline di richiesta pull esegue la convalida.
    * Al termine dell'esecuzione corretta della pipeline, il team si disconnette e la modifica viene unita.
3. La pipeline CI convalida la modifica di Alice e viene completata correttamente.
    * La modifica è sicura per la distribuzione nel cluster e gli artefatti vengono salvati nell'esecuzione della pipeline CI.
4. Le modifiche di Alice si uniscono e attivano la pipeline CD.
    * La pipeline CD preleva gli elementi archiviati dall'esecuzione della pipeline CI di Alice.
    * La pipeline CD sostituisce i modelli con valori specifici dell'ambiente ed è in corso le modifiche apportate allo stato del cluster esistente nel repository GitOps.
    * La pipeline CD crea una richiesta pull al repository GitOps con le modifiche desiderate allo stato del cluster.
5. Il team di Alice esamina e approva la richiesta pull.
    * La modifica viene unita al ramo di destinazione corrispondente all'ambiente.
6. In pochi minuti, Flux rileva una modifica nel repository GitOps e estrae le modifiche di Alice.
    * A causa della modifica dell'immagine Docker, il Pod dell'applicazione richiede un aggiornamento.
    * Flux applica la modifica al cluster.
7. Alice verifica l'endpoint dell'applicazione per verificare che la distribuzione sia stata completata correttamente.
   > [!NOTE]
   > Per altri ambienti destinati alla distribuzione, la pipeline CD esegue l'iterazione creando una richiesta pull per l'ambiente successivo e ripete i passaggi 4-7. Il processo richiede l'approvazione aggiuntiva per le distribuzioni o gli ambienti più rischiosi, ad esempio una modifica relativa alla sicurezza o un ambiente di produzione.
8.  Una volta che tutti gli ambienti hanno ricevuto correttamente le distribuzioni, la pipeline viene completata.

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sulla creazione di connessioni tra il cluster e un repository Git come [risorsa di configurazione con Azure Arc Enabled Kubernetes](./conceptual-configurations.md)
