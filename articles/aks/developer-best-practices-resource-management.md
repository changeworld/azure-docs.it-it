---
title: Procedure consigliate per la gestione delle risorse
titleSuffix: Azure Kubernetes Service
description: Procedure consigliate per gli sviluppatori di applicazioni per la gestione delle risorse nel servizio Azure Kubernetes (AKS)
services: container-service
author: zr-msft
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: zarhoads
ms.openlocfilehash: 2cd2bab05346f66b933512e677f1d38f4514796c
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105273"
---
# <a name="best-practices-for-application-developers-to-manage-resources-in-azure-kubernetes-service-aks"></a>Procedure consigliate per gli sviluppatori di applicazioni per la gestione delle risorse nel servizio Azure Kubernetes (AKS)

Per lo sviluppo e l'esecuzione di applicazioni nel servizio Azure Kubernetes (AKS) è necessario tenere in considerazione alcune aree chiave. Il modo in cui vengono gestite le distribuzioni delle applicazioni può influire negativamente sull'esperienza dell'utente finale relativamente all'uso dei servizi forniti. Per avere esito positivo, tenere presenti alcune procedure consigliate che è possibile seguire durante lo sviluppo e l'esecuzione di applicazioni in AKS.

Questo articolo è incentrato sull'esecuzione del cluster e dei carichi di lavoro dal punto di vista dello sviluppatore di applicazioni. Per informazioni sulle procedure consigliate di amministrazione, vedere [Procedure consigliate per l'operatore del cluster per l'isolamento e la gestione delle risorse nel servizio Azure Kubernetes (AKS)][operator-best-practices-isolation]. Contenuto dell'articolo:

> [!div class="checklist"]
> * Richieste e limiti di risorse pod.
> * Modalità di sviluppo e distribuzione di applicazioni con Bridge per Kubernetes e Visual Studio Code.
> * Come usare lo `kube-advisor` strumento per verificare la presenza di problemi con le distribuzioni.

## <a name="define-pod-resource-requests-and-limits"></a>Definire le richieste di risorse e i limiti del pod

> **Indicazioni sulle procedure consigliate**
> 
> Impostare richieste e limiti pod su tutti i pod nei manifesti YAML. Se il cluster AKS usa le *quote delle risorse* e non si definiscono questi valori, la distribuzione potrebbe essere rifiutata.

Usare le richieste e i limiti pod per gestire le risorse di calcolo in un cluster AKS. Richieste e limiti di Pod informa l'utilità di pianificazione Kubernetes che calcola le risorse da assegnare a un pod.

### <a name="pod-cpumemory-requests"></a>Richieste CPU/memoria Pod
Le *richieste Pod* definiscono una quantità impostata di CPU e memoria necessaria regolarmente per il pod.

Nelle specifiche del Pod **è consigliabile definire** queste richieste e limiti in base alle informazioni sopra riportate. Se non si includono questi valori, l'utilità di pianificazione Kubernetes non può prendere in considerazione le risorse richieste dalle applicazioni per facilitare la pianificazione delle decisioni.

Monitorare le prestazioni dell'applicazione per modificare le richieste pod. 
* Se si sottovalutano le richieste Pod, è possibile che l'applicazione riceva prestazioni ridotte a causa dell'eccessiva pianificazione di un nodo. 
* Se le richieste vengono sovrastimate, è possibile che l'applicazione abbia una maggiore difficoltà a essere pianificata.

### <a name="pod-cpumemory-limits"></a>Limiti di CPU/memoria Pod * * 
I *limiti di Pod* consentono di impostare la quantità massima di CPU e memoria utilizzabile da un pod. 

* I *limiti di memoria* definiscono quali Pod devono essere terminati quando i nodi sono instabili a causa di risorse insufficienti. Senza il set di limiti corretti, i pod verranno terminati fino a quando non si solleva la pressione delle risorse. 
* Anche se un pod può superare il *limite di CPU* periodicamente, il Pod non verrà terminato per il superamento del limite della CPU. 

I limiti Pod definiscono quando un pod perde il controllo dell'utilizzo delle risorse. Quando supera il limite, il Pod è contrassegnato per l'uccisione. Questo comportamento gestisce l'integrità dei nodi e riduce al minimo l'effetto sui pod che condividono il nodo. Se non si imposta un limite Pod, il valore predefinito è il valore massimo disponibile in un determinato nodo.

Evitare di impostare un limite di Pod superiore a quello che i nodi possono supportare. Ogni nodo servizio Azure Kubernetes riserva una determinata quantità di CPU e memoria per i componenti principali di Kubernetes. L'applicazione potrebbe tentare di consumare troppe risorse del nodo impedendo la corretta esecuzione di altri pod.

Monitora le prestazioni dell'applicazione in momenti diversi durante il giorno o la settimana. Determinare i tempi di picco della domanda e allineare i limiti dei pod alle risorse necessarie per soddisfare le esigenze massime.

> [!IMPORTANT]
>
> Nelle specifiche Pod definire tali richieste e limiti in base alle informazioni sopra riportate. La mancata inclusione di questi valori impedisce all'utilità di pianificazione di Kubernetes di contabilizzare le risorse richieste dalle applicazioni per facilitare la pianificazione delle decisioni.

Se l'utilità di pianificazione inserisce un pod in un nodo con risorse insufficienti, le prestazioni dell'applicazione saranno ridotte. Gli amministratori del cluster **devono** impostare le *quote di risorse* in uno spazio dei nomi che richiede l'impostazione di richieste e limiti di risorse. Per altre informazioni, vedere le informazioni sulle [quote di risorse nei cluster servizio Azure Kubernetes][resource-quotas].

Quando si definisce una richiesta o un limite per la CPU, il valore viene misurato in unità di CPU. 
* *1.0* CPU corrisponde a un core CPU virtuale sottostante sul nodo. 
    * La stessa misura viene usata per le GPU.
* È possibile definire frazioni misurate in millicore. Ad esempio, *100m* è *0,1* di un core vCPU sottostante.

Nell'esempio di base seguente per un singolo pod NGINX il pod richiede *100m* di tempo CPU e *128Mi* di memoria. I limiti delle risorse per il pod sono impostati su *250m* di CPU e *256Mi* di memoria:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
```

Per altre informazioni sulle misure e le assegnazioni delle risorse, vedere [Managing compute resources for containers][k8s-resource-limits] (Gestione delle risorse di calcolo per i contenitori).

## <a name="develop-and-debug-applications-against-an-aks-cluster"></a>Sviluppare ed eseguire il debug di applicazioni in un cluster servizio Azure Kubernetes

> **Indicazioni sulle procedure consigliate** 
>
> I team di sviluppo devono distribuire ed eseguire il debug in un cluster AKS usando Bridge per Kubernetes.

Con Bridge per Kubernetes è possibile sviluppare, eseguire il debug e testare le applicazioni direttamente in un cluster AKS. Gli sviluppatori di un team collaborano per compilare e testare tutto il ciclo di vita dell'applicazione. È possibile continuare a usare gli strumenti esistenti, ad esempio Visual Studio o Visual Studio Code con l'estensione Bridge to Kubernetes. 

L'uso del processo di sviluppo e test integrato con Bridge per Kubernetes riduce la necessità di ambienti di test locali come [minikube][minikube]. È invece possibile sviluppare e testare in base a un cluster AKS, anche a cluster protetti e isolati. 

> [!NOTE]
> Bridge to Kubernetes è progettato per l'uso con applicazioni eseguite su Pod e nodi Linux.

## <a name="use-the-visual-studio-code-vs-code-extension-for-kubernetes"></a>Usare l'estensione Visual Studio Code (VS Code) per Kubernetes

> **Indicazioni sulle procedure consigliate** 
>
> Installare e usare l'estensione VS Code per Kubernetes quando si scrivono manifesti YAML. È anche possibile usare l'estensione per una soluzione di distribuzione integrata, particolarmente utile per i proprietari delle applicazioni che raramente interagiscono con il cluster servizio Azure Kubernetes.

L'[estensione di Visual Studio Code per Kubernetes][vscode-kubernetes] consente di sviluppare e distribuire applicazioni in servizio Azure Kubernetes. L'estensione fornisce:
* IntelliSense per le risorse Kubernetes, i grafici Helm e i modelli. 
* Esplorare, distribuire e modificare le funzionalità per le risorse di Kubernetes dall'interno VS Code. 
* Verifica IntelliSense per le richieste di risorse o i limiti impostati nelle specifiche pod:

    ![Estensione di VS Code per Kubernetes con un avviso sull'assenza di limiti di memoria](media/developer-best-practices-resource-management/vs-code-kubernetes-extension.png)

## <a name="regularly-check-for-application-issues-with-kube-advisor"></a>Verificare regolarmente la presenza di problemi dell'applicazione con kube-advisor

> **Indicazioni sulle procedure consigliate** 
> 
> Eseguire regolarmente la versione più recente dello `kube-advisor` strumento open source per rilevare i problemi del cluster. Eseguire `kube-advisor` prima di applicare le quote di risorse in un cluster AKS esistente per trovare i pod per i quali non sono definiti limiti e richieste di risorse.

Lo strumento [Kube-Advisor][kube-advisor] è un progetto open source AKS associato che analizza un cluster Kubernetes e segnala i problemi identificati. Un controllo utile consiste nell'identificare i pod senza limitazioni e richieste di risorse.

Sebbene lo `kube-advisor` strumento possa creare report sulle richieste di risorse e sui limiti mancanti in PodSpecs per le applicazioni Windows e Linux, `kube-advisor` deve essere pianificato in un pod Linux. Usare un [selettore di nodo][k8s-node-selector] nella configurazione del Pod per pianificare l'esecuzione di un pod in un pool di nodi con un sistema operativo specifico.

In un cluster AKS che ospita molti team e applicazioni di sviluppo, è più facile tenere traccia dei pod usando le richieste e i limiti delle risorse. Come procedura consigliata, eseguire regolarmente `kube-advisor` nei cluster servizio Azure Kubernetes.

## <a name="next-steps"></a>Passaggi successivi

Questo articolo è incentrato su come eseguire i cluster e i carichi di lavoro dalla prospettiva di un operatore cluster. Per informazioni sulle procedure consigliate di amministrazione, vedere [Procedure consigliate per l'operatore del cluster per l'isolamento e la gestione delle risorse nel servizio Azure Kubernetes (AKS)][operator-best-practices-isolation].

Per implementare alcune di queste procedure consigliate, vedere gli articoli seguenti:

* [Sviluppare con Bridge per Kubernetes][btk]
* [Verificare la presenza di problemi con kube-advisor][aks-kubeadvisor]

<!-- EXTERNAL LINKS -->
[k8s-resource-limits]: https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/
[vscode-kubernetes]: https://github.com/Azure/vscode-kubernetes-tools
[kube-advisor]: https://github.com/Azure/kube-advisor
[minikube]: https://kubernetes.io/docs/setup/minikube/

<!-- INTERNAL LINKS -->
[aks-kubeadvisor]: kube-advisor-tool.md
[btk]: /visualstudio/containers/overview-bridge-to-kubernetes
[operator-best-practices-isolation]: operator-best-practices-cluster-isolation.md
[resource-quotas]: operator-best-practices-scheduler.md#enforce-resource-quotas
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
