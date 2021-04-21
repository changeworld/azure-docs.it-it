---
title: Procedure consigliate per la sicurezza del cluster
titleSuffix: Azure Kubernetes Service
description: Procedure consigliate per l'operatore del cluster per la gestione della sicurezza e degli aggiornamenti dei cluster nel servizio Azure Kubernetes
services: container-service
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 5cb103d843aafbb7f72c03d65b45fe3a84f8d1cd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782982"
---
# <a name="best-practices-for-cluster-security-and-upgrades-in-azure-kubernetes-service-aks"></a>Procedure consigliate per la sicurezza e gli aggiornamenti dei cluster nel servizio Azure Kubernetes

Quando si gestiscono i cluster in servizio Azure Kubernetes(AKS), la sicurezza dei carichi di lavoro e dei dati è una considerazione fondamentale. Quando si eseguono cluster multi-tenant usando l'isolamento logico, è soprattutto necessario proteggere l'accesso alle risorse e al carico di lavoro. Ridurre al minimo il rischio di attacco applicando gli aggiornamenti della sicurezza kubernetes e del sistema operativo del nodo più recenti.

Questo articolo illustra in particolare come proteggere il cluster del servizio Azure Kubernetes (AKS). Si apprenderà come:

> [!div class="checklist"]
> * Usare Azure Active Directory controllo degli accessi in base al ruolo di Kubernetes e Kubernetes per proteggere l'accesso al server API.
> * Proteggere l'accesso al contenitore alle risorse del nodo.
> * Aggiornare un cluster del servizio Kubernetes alla versione più recente di Kubernetes.
> * Mantenere aggiornati i nodi e applicare automaticamente le patch di sicurezza.

È anche possibile leggere le procedure consigliate per la [gestione delle immagini del contenitore][best-practices-container-image-management] e la [sicurezza dei pod][best-practices-pod-security].

È anche possibile usare [l'integrazione di Azure Kubernetes Services][security-center-aks] con il Centro sicurezza per rilevare le minacce e visualizzare le raccomandazioni per la protezione dei cluster del servizio Azure Kubernetes.

## <a name="secure-access-to-the-api-server-and-cluster-nodes"></a>Proteggere l'accesso al server dell'API e ai nodi del cluster

> **Indicazioni sulle procedure consigliate** 
>
> Uno dei modi più importanti per proteggere il cluster è proteggere l'accesso al server API Kubernetes. Per controllare l'accesso al server API, integrare il controllo degli accessi in base al ruolo di Kubernetes con Azure Active Directory (Azure AD). Con questi controlli si protegge il servizio Azure AzureKs nello stesso modo in cui si protegge l'accesso alle sottoscrizioni di Azure.

Il server dell'API Kubernetes fornisce un singolo punto di connessione per le richieste di esecuzione di azioni all'interno di un cluster. Per proteggere e controllare l'accesso al server API, limitare l'accesso e fornire i livelli di autorizzazione più bassi possibili. anche se questo approccio non è univoco per Kubernetes, è particolarmente importante quando il cluster del servizio Kubernetes è stato isolato logicamente per l'uso multi-tenant.

Azure AD offre una soluzione di gestione delle identità pronta per l'azienda che si integra con i cluster del servizio Web Diaks. Poiché Kubernetes non fornisce una soluzione di gestione delle identità, potrebbe essere difficile limitare in modo granulare l'accesso al server API. Grazie ai cluster integrati con Azure AD nel servizio Azure Kubernetes è possibile usare gli account utente e di gruppo esistenti per autenticare gli utenti nel server dell'API.

![Integrazione di Azure Active Directory per i cluster del servizio Azure Kubernetes](media/operator-best-practices-cluster-security/aad-integration.png)

Usando il controllo degli accessi in base al ruolo di Kubernetes e Azure AD-integration, è possibile proteggere il server API e fornire le autorizzazioni minime necessarie per un set di risorse con ambito, ad esempio un singolo spazio dei nomi. È possibile concedere a Azure AD utenti o gruppi diversi ruoli kubernetes. Con autorizzazioni granulari, è possibile limitare l'accesso al server API e fornire una audit trail di azioni eseguite.

La procedura consigliata consiste nell'usare *i gruppi* per fornire l'accesso a file e cartelle anziché a singole identità. Ad esempio, usare un'Azure AD *a un* gruppo per associare gli utenti ai ruoli kubernetes anziché ai singoli *utenti.* Quando l'appartenenza a un gruppo di un utente cambia, le autorizzazioni di accesso nel cluster del servizio Web Di conseguenza cambiano. 

Nel frattempo, si potrebbe associare il singolo utente direttamente a un ruolo e le relative funzioni di lavoro cambiano. Anche se Azure AD le appartenenze ai gruppi vengono aggiornate, le relative autorizzazioni per il cluster del servizio Web AKS non lo farebbe. In questo scenario, l'utente ha più autorizzazioni di quelle necessarie.

Per altre informazioni sull'integrazione Azure AD, il controllo degli accessi in base al ruolo di Kubernetes e il controllo degli accessi in base al ruolo di Azure, vedere Procedure consigliate per l'autenticazione e l'autorizzazione nel servizio Azure [Kubernetes.][aks-best-practices-identity]

## <a name="secure-container-access-to-resources"></a>Proteggere l'accesso del contenitore alle risorse

> **Indicazioni sulle procedure consigliate** 
> 
> Limitare l'accesso alle azioni che i contenitori possono eseguire. Fornire il minor numero di autorizzazioni ed evitare l'uso dell'accesso radice o dell'escalation con privilegi.

Allo stesso modo in cui si concedono a utenti o gruppi i privilegi minimi necessari, è necessario limitare i contenitori solo alle azioni e ai processi necessari. Per ridurre al minimo il rischio di attacchi, evitare di configurare applicazioni e contenitori che richiedono privilegi di escalation o accesso radice. 

Ad esempio, impostare `allowPrivilegeEscalation: false` nel manifesto del pod. Questi contesti di sicurezza *dei pod* Kubernetes predefiniti consentono di definire autorizzazioni aggiuntive, ad esempio l'utente o il gruppo da eseguire o le funzionalità linux da esporre. Per altre procedure consigliate, vedere [Proteggere l'accesso dei pod alle risorse][pod-security-contexts].

Per un controllo ancora più granulare delle azioni del contenitore, è anche possibile usare le funzionalità di sicurezza di Linux incorporate, ad esempio *AppArmor* e *seccomp.* 
1. Definire le funzionalità di sicurezza di Linux a livello di nodo.
1. Implementare le funzionalità tramite un manifesto del pod. 

Le funzionalità di sicurezza di Linux incorporate sono disponibili solo nei pod e nei nodi Linux.

> [!NOTE]
> Attualmente, gli ambienti Kubernetes non sono completamente sicuri per l'utilizzo multi-tenant dannoso. Funzionalità di sicurezza aggiuntive, ad *esempio AppArmor,* *seccomp,* criteri di sicurezza dei *pod* o controllo degli accessi in base al ruolo di Kubernetes per i nodi, bloccano in modo efficiente gli exploit. 
>
>Per una vera sicurezza quando si eseguono carichi di lavoro multi-tenant dannosi, considerare attendibile solo un hypervisor. Il dominio di sicurezza per Kubernetes diventa l'intero cluster, non un singolo nodo. 
>
> Per questi tipi di carichi di lavoro multi-tenant ostili è consigliabile usare cluster fisicamente isolati.

### <a name="app-armor"></a>AppArmor

Per limitare le azioni del contenitore, è possibile usare il modulo di sicurezza del kernel Linux [AppArmor.][k8s-apparmor] Il modulo è disponibile come parte del sistema operativo del nodo del servizio Azure Kubernetes sottostante ed è abilitato per impostazione predefinita. Si creano profili AppArmor che limitano le azioni di lettura, scrittura o esecuzione o funzioni di sistema come il montaggio di file system. I profili AppArmor predefiniti limitano l'accesso a vari percorsi `/proc` e `/sys` e consentono di isolare logicamente i contenitori dal nodo sottostante. AppArmor funziona non solo per i pod Kubernetes ma per qualsiasi applicazione in esecuzione su Linux.

![Profili AppArmor in uso in un cluster del servizio Azure Kubernetes per limitare le azioni dei contenitori](media/operator-best-practices-container-security/apparmor.png)

Per una dimostrazione di AppArmor in azione, l'esempio seguente crea un profilo che impedisce la scrittura nei file. 
1. [SSH][aks-ssh] in un nodo del server del cluster di controllo di accesso.
1. Creare un file denominato *deny-write.profile*.
1. Incollare il contenuto seguente:

    ```
    #include <tunables/global>
    profile k8s-apparmor-example-deny-write flags=(attach_disconnected) {
      #include <abstractions/base>
  
      file,
      # Deny all file writes.
      deny /** w,
    }
    ```

Per aggiungere profili AppArmor occorre usare il comando `apparmor_parser`. 
1. Aggiungere il profilo ad AppArmor.
1. Specificare il nome del profilo creato nel passaggio precedente:

    ```console
    sudo apparmor_parser deny-write.profile
    ```

    Se il profilo viene analizzato correttamente e applicato ad AppArmor, non verrà visualizzato alcun output e si verrà restituiti al prompt dei comandi.

1. Nel computer locale creare un manifesto del pod denominato *aks-apparmor.yaml.* Questo manifesto:
    * Definisce un'annotazione per `container.apparmor.security.beta.kubernetes` .
    * Fa riferimento *al profilo di negazione della* scrittura creato nei passaggi precedenti.

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: hello-apparmor
      annotations:
        container.apparmor.security.beta.kubernetes.io/hello: localhost/k8s-apparmor-example-deny-write
    spec:
      containers:
      - name: hello
        image: mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
        command: [ "sh", "-c", "echo 'Hello AppArmor!' && sleep 1h" ]
    ```

1. Con il pod distribuito, usare verificare che il pod *hello-apparmor* sia *bloccato:*

    ```
    $ kubectl get pods

    NAME             READY   STATUS    RESTARTS   AGE
    aks-ssh          1/1     Running   0          4m2s
    hello-apparmor   0/1     Blocked   0          50s
    ```

Per altre informazioni su AppArmor, vedere [AppArmor][k8s-apparmor] nella documentazione di Kubernetes.

### <a name="secure-computing"></a>seccomp

Mentre AppArmor funziona con qualsiasi applicazione Linux, [seccomp (*sec* ure *comp* uting, elaborazione sicura)][seccomp] funziona a livello di processo. Anche seccomp è un modulo di protezione del kernel di Linux ed è supportato in modo nativo dal runtime Docker usato dai nodi del servizio Azure Kubernetes. Con seccomp è possibile limitare le chiamate al processo contenitore. Allinearsi alla procedura consigliata per concedere al contenitore l'autorizzazione minima solo per l'esecuzione da:
* Definizione con filtri delle azioni da consentire o negare.
* Annotazione all'interno di un manifesto YAML del pod da associare al filtro seccomp. 

Per una dimostrazione di seccomp in azione, creare un filtro che impedisce di cambiare le autorizzazioni su un file. 
1. [SSH][aks-ssh] in un nodo del server del cluster di controllo di accesso.
1. Creare un filtro seccomp denominato */var/lib/kubelet/seccomp/prevent-chmod*.
1. Incollare il contenuto seguente:

    ```json
    {
      "defaultAction": "SCMP_ACT_ALLOW",
      "syscalls": [
        {
          "name": "chmod",
          "action": "SCMP_ACT_ERRNO"
        },
        {
          "name": "fchmodat",
          "action": "SCMP_ACT_ERRNO"
        },
        {
          "name": "chmodat",
          "action": "SCMP_ACT_ERRNO"
        }
      ]
    }
    ```

    Nella versione 1.19 e successive è necessario configurare quanto segue:

    ```json
    {
      "defaultAction": "SCMP_ACT_ALLOW",
      "syscalls": [
        {
          "names": ["chmod","fchmodat","chmodat"],
          "action": "SCMP_ACT_ERRNO"
        }
      ]
    }
    ```

1. Dal computer locale creare un manifesto del pod denominato *aks-seccomp.yaml* e incollare il contenuto seguente. Questo manifesto:
    * Definisce un'annotazione per `seccomp.security.alpha.kubernetes.io` .
    * Fa riferimento *al filtro prevent-chmod* creato nel passaggio precedente.

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: chmod-prevented
      annotations:
        seccomp.security.alpha.kubernetes.io/pod: localhost/prevent-chmod
    spec:
      containers:
      - name: chmod
        image: mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
        command:
          - "chmod"
        args:
         - "777"
         - /etc/hostname
      restartPolicy: Never
    ```

    Nella versione 1.19 e successive è necessario configurare quanto segue:

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: chmod-prevented
    spec:
      securityContext:
        seccompProfile:
          type: Localhost
          localhostProfile: prevent-chmod
      containers:
      - name: chmod
        image: mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
        command:
          - "chmod"
        args:
         - "777"
         - /etc/hostname
      restartPolicy: Never
    ```

1. Distribuire il pod di esempio usando il comando [kubectl apply][kubectl-apply]:

    ```console
    kubectl apply -f ./aks-seccomp.yaml
    ```

1. Visualizzare lo stato dei pod [usando il comando kubectl get pods.][kubectl-get] 
    * Il pod segnala un errore. 
    * Come illustrato nell'output di esempio seguente, il filtro seccomp impedisce l'esecuzione del comando `chmod`:    

    ```
    $ kubectl get pods

    NAME                      READY     STATUS    RESTARTS   AGE
    chmod-prevented           0/1       Error     0          7s
    ```

Per altre informazioni sui filtri disponibili, vedere [Seccomp security profiles for Docker][seccomp] (Profili di sicurezza di seccomp per Docker).

## <a name="regularly-update-to-the-latest-version-of-kubernetes"></a>Eseguire regolarmente l'aggiornamento alla versione più recente di Kubernetes

> **Indicazioni sulle procedure consigliate** 
> 
> Per rimanere aggiornati sulle nuove funzionalità e sulle correzioni di bug, aggiornare regolarmente la versione di Kubernetes nel cluster del servizio Kubernetes.

Kubernetes rilascia nuove funzionalità con maggiore frequenza rispetto alle piattaforme di infrastruttura più tradizionali. Gli aggiornamenti di Kubernetes includono:
* Nuove funzionalità
* Correzioni di bug o di sicurezza 

Le nuove funzionalità in genere passano *attraverso lo stato* alfa e *beta* prima che *diventino stabili.* Una volta stabili, sono disponibili a livello generale e consigliati per l'uso in produzione. Il nuovo ciclo di rilascio delle funzionalità di Kubernetes consente di aggiornare Kubernetes senza riscontrare regolarmente modifiche di rilievo o modificare le distribuzioni e i modelli.

Il servizio Kubernetes supporta tre versioni secondarie di Kubernetes. Dopo l'introduzione di una nuova versione secondaria della patch, la versione secondaria meno recente e le versioni di patch supportate vengono ritirate. Gli aggiornamenti secondari di Kubernetes si verificano periodicamente. Per mantenere il supporto, assicurarsi di disporre di un processo di governance per verificare la disponibilità degli aggiornamenti necessari. Per altre informazioni, vedere Versioni di Kubernetes supportate nel servizio [Kubernetes.][aks-supported-versions]

Per verificare le versioni disponibili per il cluster in uso, usare il comando [az aks get-upgrades][az-aks-get-upgrades] come illustrato nell'esempio seguente:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

È quindi possibile aggiornare il cluster AKS tramite il comando [az aks upgrade][az-aks-upgrade]. Il processo di aggiornamento in modo sicuro:
* Cordon e svuota un nodo alla volta.
* Pianifica i pod nei nodi rimanenti.
* Distribuisce un nuovo nodo che esegue le versioni più recenti del sistema operativo e kubernetes.

>[!IMPORTANT]
> Testare le nuove versioni secondarie in un ambiente di test di sviluppo e verificare che il carico di lavoro rimanga integro con la nuova versione di Kubernetes. 
>
> Kubernetes può deprecazione delle API (come nella versione 1.16) su cui si basano i carichi di lavoro. Quando si portano nuove versioni [](use-multiple-node-pools.md) nell'ambiente di produzione, è consigliabile usare più pool di nodi in versioni separate e aggiornare i singoli pool uno alla volta per eseguire il roll-to-roll progressivo dell'aggiornamento in un cluster. Se si eseguono più cluster, aggiornare un cluster alla volta per monitorare progressivamente l'impatto o le modifiche.
>
>```azurecli-interactive
>az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version KUBERNETES_VERSION
>```

Per altre informazioni sugli aggiornamenti nel servizio Azure Container, vedere [Versioni Kubernetes supportate nel servizio Azure Kubernetes][aks-supported-versions] e [Aggiornare un cluster del servizio Azure Kubernetes][aks-upgrade].

## <a name="process-linux-node-updates-and-reboots-using-kured"></a>Elaborare gli aggiornamenti e i riavvii dei nodi Linux usando kured

> **Indicazioni sulle procedure consigliate** 
> 
> Anche se AKS scarica e installa automaticamente le correzioni di sicurezza in ogni nodo Linux, non viene riavviato automaticamente. 
> 1. Usare `kured` per controllare i riavvii in sospeso.
> 1. Eseguire in modo sicuro il cordon e svuotare il nodo per consentire il riavvio del nodo.
> 1. Applicare gli aggiornamenti.
> 1. Essere il più sicuro possibile rispetto al sistema operativo. 

Per i nodi di Windows Server, eseguire regolarmente un'operazione di aggiornamento del servizio AKS per eseguire in modo sicuro il cordon e svuotare i pod e distribuire i nodi aggiornati.

Ogni sera, i nodi Linux nel servizio AKS ottengono patch di sicurezza tramite il canale di aggiornamento della distribuzione. Questo comportamento viene configurato automaticamente quando i nodi vengono distribuiti in un cluster del servizio AKS. Per ridurre al minimo le interruzioni del servizio e l'impatto sui carichi di lavoro in esecuzione, i nodi non vengono riavviati automaticamente se una patch di protezione o un aggiornamento del kernel lo richiede.

Il progetto open-source [kured (KUbernetes REboot Daemon)][kured] di Weaveworks controlla i riavvii di nodi in sospeso. Quando un nodo Linux applica aggiornamenti che richiedono un riavvio, il nodo viene svuotato in modo sicuro per spostare e pianificare i pod in altri nodi del cluster. Dopo il riavvio, il nodo viene aggiunto nuovamente al cluster e Kubernetes riprende la pianificazione dei pod. Per ridurre al minimo le interruzioni del servizio, a `kured` è consentito riavviare un solo nodo alla volta.

![Processo di riavvio dei nodi del servizio Azure Kubernetes tramite kured](media/operator-best-practices-cluster-security/node-reboot-process.png)

Se si vuole un controllo ancora più completo sui riavvii, è possibile eseguire l'integrazione con Prometheus per evitare riavvii se sono in corso altri eventi di manutenzione o problemi `kured` del cluster. Questa integrazione riduce la complicazione riavviando i nodi durante la risoluzione di altri problemi.

Per altre informazioni su come gestire i riavvii dei nodi, vedere [Applicare aggiornamenti di sicurezza e del kernel ai nodi nel servizio Azure Kubernetes][aks-kured].

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato in particolare come proteggere il cluster del servizio Azure Kubernetes (AKS). Per implementare alcune di queste aree, vedere gli articoli seguenti:

* [Integrare Azure Active Directory con servizio Azure Kubernetes][aks-aad]
* [Aggiornare un cluster di Azure Kubernetes Service (AKS)][aks-upgrade]
* [Applicare aggiornamenti di sicurezza e del kernel ai nodi nel servizio Kubernetes di Azure (AKS)][aks-kured]

<!-- EXTERNAL LINKS -->
[kured]: https://github.com/weaveworks/kured
[k8s-apparmor]: https://kubernetes.io/docs/tutorials/clusters/apparmor/
[seccomp]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#seccomp
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-get-upgrades]: /cli/azure/aks#az_aks_get_upgrades
[az-aks-upgrade]: /cli/azure/aks#az_aks_upgrade
[aks-supported-versions]: supported-kubernetes-versions.md
[aks-upgrade]: upgrade-cluster.md
[aks-best-practices-identity]: concepts-identity.md
[aks-kured]: node-updates-kured.md
[aks-aad]: ./azure-ad-integration-cli.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[pod-security-contexts]: developer-best-practices-pod-security.md#secure-pod-access-to-resources
[aks-ssh]: ssh.md
[security-center-aks]: ../security-center/defender-for-kubernetes-introduction.md
