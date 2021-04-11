---
title: Procedure consigliate per la sicurezza del cluster
titleSuffix: Azure Kubernetes Service
description: Procedure consigliate per l'operatore del cluster per la gestione della sicurezza e degli aggiornamenti dei cluster nel servizio Azure Kubernetes
services: container-service
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: ea63db2d8868a1333ae264c9cfdf31d0b7397a83
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105154"
---
# <a name="best-practices-for-cluster-security-and-upgrades-in-azure-kubernetes-service-aks"></a>Procedure consigliate per la sicurezza e gli aggiornamenti dei cluster nel servizio Azure Kubernetes

Quando si gestiscono i cluster in Azure Kubernetes Service (AKS), il carico di lavoro e la sicurezza dei dati è una considerazione fondamentale. Quando si eseguono cluster multi-tenant usando l'isolamento logico, è necessario proteggere l'accesso alle risorse e al carico di lavoro. Ridurre al minimo il rischio di attacco applicando gli aggiornamenti della sicurezza del sistema operativo Kubernetes e node più recenti.

Questo articolo illustra in particolare come proteggere il cluster del servizio Azure Kubernetes (AKS). Si apprenderà come:

> [!div class="checklist"]
> * Usare Azure Active Directory e il controllo degli accessi in base al ruolo Kubernetes (Kubernetes RBAC) per proteggere l'accesso al server API.
> * Proteggere l'accesso ai contenitori alle risorse del nodo.
> * Aggiornare un cluster AKS alla versione più recente di Kubernetes.
> * Mantieni i nodi aggiornati e applica automaticamente le patch di sicurezza.

È anche possibile leggere le procedure consigliate per la [gestione delle immagini del contenitore][best-practices-container-image-management] e la [sicurezza dei pod][best-practices-pod-security].

È anche possibile usare l' [integrazione dei servizi Kubernetes di Azure con il Centro sicurezza][security-center-aks] per individuare le minacce e visualizzare le raccomandazioni per la protezione dei cluster AKS.

## <a name="secure-access-to-the-api-server-and-cluster-nodes"></a>Proteggere l'accesso al server dell'API e ai nodi del cluster

> **Indicazioni sulle procedure consigliate** 
>
> Uno dei modi più importanti per proteggere il cluster consiste nel proteggere l'accesso al server dell'API Kubernetes. Per controllare l'accesso al server API, integrare Kubernetes RBAC con Azure Active Directory (Azure AD). Con questi controlli è possibile proteggere AKS nello stesso modo in cui si protegge l'accesso alle sottoscrizioni di Azure.

Il server dell'API Kubernetes fornisce un singolo punto di connessione per le richieste di esecuzione di azioni all'interno di un cluster. Per proteggere e controllare l'accesso al server API, limitare l'accesso e fornire i livelli di autorizzazione più bassi possibili. Sebbene questo approccio non sia univoco per Kubernetes, è particolarmente importante quando si isola logicamente il cluster AKS per l'uso multi-tenant.

Azure AD offre una soluzione di gestione delle identità pronta per l'azienda che si integra con i cluster AKS. Poiché Kubernetes non fornisce una soluzione di gestione delle identità, è possibile che si verifichi un problema per limitare in modo granulare l'accesso al server API. Grazie ai cluster integrati con Azure AD nel servizio Azure Kubernetes è possibile usare gli account utente e di gruppo esistenti per autenticare gli utenti nel server dell'API.

![Integrazione di Azure Active Directory per i cluster del servizio Azure Kubernetes](media/operator-best-practices-cluster-security/aad-integration.png)

Usando il controllo degli accessi in base al ruolo e l'integrazione Azure AD, è possibile proteggere il server API e fornire le autorizzazioni minime necessarie a un set di risorse con ambito, ad esempio un singolo spazio dei nomi. È possibile concedere diversi Azure AD utenti o gruppi diversi ruoli Kubernetes. Con le autorizzazioni granulari, è possibile limitare l'accesso al server API e fornire una chiara audit trail di azioni eseguite.

La procedura consigliata consiste nell'usare i *gruppi* per fornire l'accesso a file e cartelle anziché a singole identità. Usare, ad esempio, un'appartenenza a un *gruppo* Azure ad per associare gli utenti ai ruoli Kubernetes anziché ai singoli *utenti*. Quando l'appartenenza a un gruppo di un utente cambia, le autorizzazioni di accesso per il cluster AKS cambiano di conseguenza. 

Nel frattempo, si consentirà di associare il singolo utente direttamente a un ruolo e le relative funzioni di processo cambiano. Durante l'aggiornamento delle appartenenze ai gruppi di Azure AD, le autorizzazioni per il cluster AKS non lo sono. In questo scenario, l'utente finisce con più autorizzazioni del necessario.

Per altre informazioni sull'integrazione di Azure AD, Kubernetes RBAC e il controllo degli accessi in base al ruolo di Azure, vedere [procedure consigliate per l'autenticazione e l'autorizzazione in AKS][aks-best-practices-identity]

## <a name="secure-container-access-to-resources"></a>Proteggere l'accesso del contenitore alle risorse

> **Indicazioni sulle procedure consigliate** 
> 
> Limitare l'accesso alle azioni che i contenitori possono eseguire. Fornire il minor numero di autorizzazioni ed evitare l'uso dell'accesso radice o dell'escalation dei privilegi.

Nello stesso modo in cui è necessario concedere agli utenti o ai gruppi i privilegi minimi necessari, è necessario limitare anche i contenitori solo alle azioni e ai processi necessari. Per ridurre al minimo il rischio di attacco, evitare di configurare le applicazioni e i contenitori che richiedono privilegi escalated o accesso alla radice. 

Ad esempio, impostare `allowPrivilegeEscalation: false` nel manifesto del pod. Questi *contesti di sicurezza Kubernetes Pod* predefiniti consentono di definire autorizzazioni aggiuntive, ad esempio l'utente o il gruppo per l'esecuzione o le funzionalità Linux da esporre. Per altre procedure consigliate, vedere [Proteggere l'accesso dei pod alle risorse][pod-security-contexts].

Per un controllo ancora più granulare delle azioni del contenitore, è anche possibile usare le funzionalità di sicurezza Linux predefinite, ad esempio *AppArmor* e *seccomp*. 
1. Definire le funzionalità di sicurezza di Linux a livello di nodo.
1. Implementare le funzionalità tramite un manifesto pod. 

Le funzionalità di sicurezza Linux predefinite sono disponibili solo nei nodi e nei Pod Linux.

> [!NOTE]
> Attualmente, gli ambienti Kubernetes non sono completamente sicuri per l'utilizzo di multi-tenant ostili. Funzionalità di sicurezza aggiuntive, ad esempio *AppArmor*, *seccomp*,*criteri di sicurezza Pod* o controllo degli accessi in base al ruolo per i nodi, bloccano efficacemente gli exploit. 
>
>Per una sicurezza reale quando si eseguono carichi di lavoro multi-tenant ostili, è sufficiente considerare attendibile un hypervisor. Il dominio di sicurezza per Kubernetes diventa l'intero cluster, non un singolo nodo. 
>
> Per questi tipi di carichi di lavoro multi-tenant ostili è consigliabile usare cluster fisicamente isolati.

### <a name="app-armor"></a>AppArmor

Per limitare le azioni del contenitore, è possibile usare il modulo di sicurezza kernel di [AppArmor][k8s-apparmor] Linux. Il modulo è disponibile come parte del sistema operativo del nodo del servizio Azure Kubernetes sottostante ed è abilitato per impostazione predefinita. È possibile creare profili AppArmor che limitano le azioni di lettura, scrittura o esecuzione oppure funzioni di sistema come il montaggio di file System. I profili AppArmor predefiniti limitano l'accesso a vari percorsi `/proc` e `/sys` e consentono di isolare logicamente i contenitori dal nodo sottostante. AppArmor funziona non solo per i pod Kubernetes ma per qualsiasi applicazione in esecuzione su Linux.

![Profili AppArmor in uso in un cluster del servizio Azure Kubernetes per limitare le azioni dei contenitori](media/operator-best-practices-container-security/apparmor.png)

Per una dimostrazione di AppArmor in azione, l'esempio seguente crea un profilo che impedisce la scrittura nei file. 
1. Da [SSH][aks-ssh] a un nodo AKS.
1. Creare un file denominato *Deny-Write. profile*.
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
1. Aggiungere il profilo a AppArmor.
1. Specificare il nome del profilo creato nel passaggio precedente:

    ```console
    sudo apparmor_parser deny-write.profile
    ```

    Se il profilo viene correttamente analizzato e applicato a AppArmor, non verrà visualizzato alcun output e verrà restituito il prompt dei comandi.

1. Dal computer locale creare un manifesto Pod denominato *AKS-AppArmor. YAML*. Questo manifesto:
    * Definisce un'annotazione per `container.apparmor.security.beta.kubernetes` .
    * Fa riferimento al profilo *Deny-Write* creato nei passaggi precedenti.

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

1. Con il Pod distribuito, usare verifica che il Pod *Hello-AppArmor* venga visualizzato come *bloccato*:

    ```
    $ kubectl get pods

    NAME             READY   STATUS    RESTARTS   AGE
    aks-ssh          1/1     Running   0          4m2s
    hello-apparmor   0/1     Blocked   0          50s
    ```

Per altre informazioni su AppArmor, vedere [AppArmor][k8s-apparmor] nella documentazione di Kubernetes.

### <a name="secure-computing"></a>seccomp

Mentre AppArmor funziona con qualsiasi applicazione Linux, [seccomp (*sec* ure *comp* uting, elaborazione sicura)][seccomp] funziona a livello di processo. Anche seccomp è un modulo di protezione del kernel di Linux ed è supportato in modo nativo dal runtime Docker usato dai nodi del servizio Azure Kubernetes. Con seccomp è possibile limitare le chiamate al processo del contenitore. Allineare alla procedura consigliata per concedere al contenitore l'autorizzazione minima solo per l'esecuzione da parte di:
* La definizione di con filtra le azioni da concedere o negare.
* Annotazione all'interno di un manifesto del Pod YAML da associare al filtro seccomp. 

Per una dimostrazione di seccomp in azione, creare un filtro che impedisce di cambiare le autorizzazioni su un file. 
1. Da [SSH][aks-ssh] a un nodo AKS.
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

    Nella versione 1,19 e successive, è necessario configurare quanto segue:

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

1. Dal computer locale creare un manifesto Pod denominato *AKS-seccomp. YAML* e incollare il contenuto seguente. Questo manifesto:
    * Definisce un'annotazione per `seccomp.security.alpha.kubernetes.io` .
    * Fa riferimento al filtro *Impedisci* creato nel passaggio precedente.

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

    Nella versione 1,19 e successive, è necessario configurare quanto segue:

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

1. Visualizzare lo stato del pod usando il comando [kubectl Get Pod][kubectl-get] . 
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
> Per rimanere aggiornati sulle nuove funzionalità e sulle correzioni di bug, aggiornare regolarmente la versione Kubernetes nel cluster AKS.

Kubernetes rilascia nuove funzionalità con maggiore frequenza rispetto alle piattaforme di infrastruttura più tradizionali. Gli aggiornamenti di Kubernetes includono:
* Nuove funzionalità
* Bug o correzioni per la sicurezza 

Le nuove funzionalità di solito passano attraverso lo stato *Alpha* e *beta* prima che diventino *stabili*. Una volta stabile, sono disponibili a livello generale e sono consigliate per l'uso in produzione. Il ciclo di rilascio delle nuove funzionalità di Kubernetes consente di aggiornare Kubernetes senza regolare le modifiche di rilievo o modificare le distribuzioni e i modelli.

AKS supporta tre versioni secondarie di Kubernetes. Una volta introdotta una nuova versione della patch secondaria, sono state ritirate le versioni secondarie meno recenti e le versioni di patch supportate. Gli aggiornamenti Kubernetes secondari avvengono periodicamente. Per rimanere al passo con il supporto tecnico, assicurarsi di avere un processo di governance per verificare la presenza di aggiornamenti necessari. Per altre informazioni, vedere [versioni di Kubernetes supportate AKS][aks-supported-versions].

Per verificare le versioni disponibili per il cluster in uso, usare il comando [az aks get-upgrades][az-aks-get-upgrades] come illustrato nell'esempio seguente:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

È quindi possibile aggiornare il cluster AKS tramite il comando [az aks upgrade][az-aks-upgrade]. Il processo di aggiornamento è sicuro:
* Cordon e svuota un nodo alla volta.
* Pianifica i pod nei nodi rimanenti.
* Distribuisce un nuovo nodo che esegue le versioni più recenti del sistema operativo e di Kubernetes.

>[!IMPORTANT]
> Testare le nuove versioni secondarie in un ambiente di test di sviluppo e verificare che il carico di lavoro rimanga integro con la nuova versione di Kubernetes. 
>
> Kubernetes può deprecare le API (come nella versione 1,16) su cui si basano i carichi di lavoro. Quando si importano nuove versioni in produzione, è consigliabile usare [più pool di nodi in versioni separate](use-multiple-node-pools.md) e aggiornare i singoli pool uno alla volta per eseguire progressivamente il Rolling dell'aggiornamento in un cluster. Se si eseguono più cluster, eseguire l'aggiornamento di un cluster alla volta per monitorare progressivamente l'effetto o le modifiche.
>
>```azurecli-interactive
>az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version KUBERNETES_VERSION
>```

Per altre informazioni sugli aggiornamenti nel servizio Azure Container, vedere [Versioni Kubernetes supportate nel servizio Azure Kubernetes][aks-supported-versions] e [Aggiornare un cluster del servizio Azure Kubernetes][aks-upgrade].

## <a name="process-linux-node-updates-and-reboots-using-kured"></a>Elaborare gli aggiornamenti e i riavvii del nodo Linux usando KURED

> **Indicazioni sulle procedure consigliate** 
> 
> Quando AKS Scarica e installa automaticamente le correzioni di sicurezza in ogni nodo Linux, non viene riavviato automaticamente. 
> 1. Usare `kured` per controllare i riavvii in sospeso.
> 1. Eseguire il cordoning sicuro e svuotare il nodo per consentire il riavvio del nodo.
> 1. Applicare gli aggiornamenti.
> 1. Essere il più sicuro possibile per quanto riguarda il sistema operativo. 

Per i nodi di Windows Server, eseguire regolarmente un'operazione di aggiornamento AKS per cordonare e svuotare in modo sicuro i pod e distribuire i nodi aggiornati.

Ogni sera, i nodi Linux in AKS ottengono patch di sicurezza tramite il canale di aggiornamento della distribuzione. Questo comportamento viene configurato automaticamente durante la distribuzione dei nodi in un cluster AKS. Per ridurre al minimo le interruzioni del servizio e l'impatto sui carichi di lavoro in esecuzione, i nodi non vengono riavviati automaticamente se una patch di protezione o un aggiornamento del kernel lo richiede.

Il progetto open-source [kured (KUbernetes REboot Daemon)][kured] di Weaveworks controlla i riavvii di nodi in sospeso. Quando un nodo Linux applica gli aggiornamenti che richiedono un riavvio, il nodo viene protetto in modo sicuro e svuotato per spostare e pianificare i pod in altri nodi del cluster. Una volta riavviato il nodo, questo viene aggiunto nuovamente al cluster e Kubernetes riprende la pianificazione dei pod. Per ridurre al minimo le interruzioni del servizio, a `kured` è consentito riavviare un solo nodo alla volta.

![Processo di riavvio dei nodi del servizio Azure Kubernetes tramite kured](media/operator-best-practices-cluster-security/node-reboot-process.png)

Se si desidera un maggiore controllo sui riavvii, `kured` può essere integrato con Prometeo per evitare i riavvii se sono in corso altri eventi di manutenzione o problemi del cluster. Questa integrazione riduce la complicazione riavviando i nodi mentre si risolvono attivamente altri problemi.

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
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
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
