---
title: Usare i criteri di sicurezza dei pod in servizio Azure Kubernetes (AKS)
description: Informazioni su come controllare le ammissioni di pod usando PodSecurityPolicy in servizio Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 03/25/2021
ms.openlocfilehash: d70b8e8efbf96e50575845ac88993012fed936d5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767422"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>Anteprima- Proteggere il cluster usando i criteri di sicurezza dei pod servizio Azure Kubernetes (AKS)

> [!WARNING]
> **La funzionalità descritta in questo documento, criteri [](https://kubernetes.io/blog/2021/04/06/podsecuritypolicy-deprecation-past-present-and-future/) di sicurezza dei pod (anteprima), inizierà la deprecazione con Kubernetes versione 1.21, con la rimozione nella versione 1.25.** Man mano che Kubernetes Upstream si avvicina a tale attività cardine, la community di Kubernetes si sta lavorando per documentare alternative funzionanti. L'annuncio di deprecazione precedente è stato fatto al momento perché non esisteva un'opzione valida per i clienti. Ora che la community di Kubernetes sta lavorando a un'alternativa, non c'è più una necessità pressante di deprecazione prima di Kubernetes. 
>
> Dopo la deprecazione di Criteri di sicurezza dei pod (anteprima), sarà necessario disabilitare la funzionalità in eventuali cluster esistenti che usano la funzionalità deprecata per eseguire aggiornamenti futuri del cluster e mantenere il supporto tecnico di Azure.

Per migliorare la sicurezza del cluster del servizio Servizio Contenitore di Microsoft, è possibile limitare i pod che possono essere pianificati. I pod che richiedono risorse non consentite non possono essere eseguiti nel cluster del servizio Contenitore di AKS. Questo accesso viene definito usando i criteri di sicurezza dei pod. Questo articolo illustra come usare i criteri di sicurezza dei pod per limitare la distribuzione di pod nel servizio Contenitore di AKS.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si disponga di un cluster del servizio Azure Kubernetes esistente. Se è necessario un cluster del servizio Azure Kubernetes, vedere la guida di avvio rapido sul servizio Azure Kubernetes [Uso dell'interfaccia della riga di comando di Azure][aks-quickstart-cli] oppure [Uso del portale di Azure][aks-quickstart-portal].

È necessario che sia installata e configurata l'interfaccia della riga di comando di Azure 2.0.61 o versioni successive. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Installare l'estensione dell'interfaccia della riga di comando aks-preview

Per usare i criteri di sicurezza dei pod, è necessaria l'estensione dell'interfaccia della riga di comando *aks-preview* versione 0.4.1 o successiva. Installare l'estensione *aks-preview* dell'interfaccia della riga di comando di Azure usando il comando [az extension add][az-extension-add], quindi verificare la disponibilità di eventuali aggiornamenti usando il comando [az extension update][az-extension-update]:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-pod-security-policy-feature-provider"></a>Registrare il provider di funzionalità dei criteri di sicurezza dei pod

**Questo documento e questa funzionalità sono stati impostati per la deprecazione il 15 ottobre 2020.**

Per creare o aggiornare un cluster del servizio AzureKs per l'uso dei criteri di sicurezza dei pod, abilitare prima un flag di funzionalità nella sottoscrizione. Per registrare il flag *di funzionalità PodSecurityPolicyPreview,* usare il [comando az feature register][az-feature-register] come illustrato nell'esempio seguente:

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

Sono necessari alcuni minuti per visualizzare lo stato *Registered*. È possibile controllare lo stato di registrazione usando il comando [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

Quando si è pronti, aggiornare la registrazione del provider di risorse *Microsoft.ContainerService* usando il [comando az provider register:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>Panoramica dei criteri di sicurezza dei pod

In un cluster Kubernetes viene usato un controller di ammissione per intercettare le richieste al server API quando deve essere creata una risorsa. Il controller di ammissione può *quindi convalidare* la richiesta di risorsa rispetto a un set di regole o *modificare* la risorsa per modificare i parametri di distribuzione.

*PodSecurityPolicy è* un controller di ammissione che convalida una specifica di pod che soddisfa i requisiti definiti. Questi requisiti possono limitare l'uso di contenitori con privilegi, l'accesso a determinati tipi di archiviazione o l'utente o il gruppo con cui può essere eseguito il contenitore. Quando si tenta di distribuire una risorsa in cui le specifiche del pod non soddisfano i requisiti descritti nei criteri di sicurezza del pod, la richiesta viene negata. Questa possibilità di controllare quali pod possono essere pianificati nel cluster del servizio AKS impedisce alcune possibili vulnerabilità di sicurezza o escalation dei privilegi.

Quando si abilitano i criteri di sicurezza dei pod in un cluster del servizio Gateway Gateway, vengono applicati alcuni criteri predefiniti. Questi criteri predefiniti offrono un'esperienza predefinita per definire quali pod possono essere pianificati. Tuttavia, gli utenti del cluster possono verificarsi problemi durante la distribuzione dei pod fino a quando non si definiscono i propri criteri. L'approccio consigliato consiste nel:

* Creare un cluster AKS
* Definire criteri di sicurezza dei pod personalizzati
* Abilitare la funzionalità dei criteri di sicurezza dei pod

Per mostrare come i criteri predefiniti limitano le distribuzioni di pod, in questo articolo viene prima abilitata la funzionalità criteri di sicurezza dei pod e quindi viene creato un criterio personalizzato.

### <a name="behavior-changes-between-pod-security-policy-and-azure-policy"></a>Modifiche del comportamento tra i criteri di sicurezza dei pod e Criteri di Azure

Di seguito è riportato un riepilogo delle modifiche del comportamento tra i criteri di sicurezza dei pod e Criteri di Azure.

|Scenario| Criteri di sicurezza dei pod | Criteri di Azure |
|---|---|---|
|Installazione|Abilitare la funzionalità dei criteri di sicurezza dei pod |Abilitare Criteri di Azure componente aggiuntivo
|Distribuire i criteri| Distribuire la risorsa criteri di sicurezza dei pod| Assegnare i criteri di Azure all'ambito della sottoscrizione o del gruppo di risorse. Il Criteri di Azure aggiuntivo è necessario per le applicazioni di risorse Kubernetes.
| Criteri predefiniti | Quando i criteri di sicurezza dei pod sono abilitati nel servizio Contenitore di AKS, vengono applicati i criteri predefiniti Con privilegi e Senza restrizioni. | Non vengono applicati criteri predefiniti abilitando Criteri di Azure componente aggiuntivo. È necessario abilitare in modo esplicito i criteri Criteri di Azure.
| Utenti che possono creare e assegnare criteri | L'amministratore del cluster crea una risorsa criteri di sicurezza dei pod | Gli utenti devono avere un ruolo minimo di "proprietario" o di "collaboratore ai criteri delle risorse" nel gruppo di risorse del cluster del servizio Diaks. - Tramite l'API, gli utenti possono assegnare criteri nell'ambito delle risorse del cluster del servizio Web Diaks. L'utente deve avere almeno le autorizzazioni "proprietario" o "Collaboratore ai criteri delle risorse" per la risorsa cluster del servizio Web Diaks. - Nell'portale di Azure i criteri possono essere assegnati a livello di gruppo di gestione/sottoscrizione/gruppo di risorse.
| Autorizzazione dei criteri| Gli utenti e gli account del servizio richiedono autorizzazioni esplicite per usare i criteri di sicurezza dei pod. | Non è necessaria alcuna assegnazione aggiuntiva per autorizzare i criteri. Dopo aver assegnato i criteri in Azure, tutti gli utenti del cluster possono usarli.
| Applicabilità dei criteri | L'utente amministratore ignora l'applicazione dei criteri di sicurezza dei pod. | Tutti gli utenti (& non amministratori) vedono gli stessi criteri. Non esiste una distinzione tra maiuscole e minuscole speciale basata sugli utenti. L'applicazione dei criteri può essere esclusa a livello di spazio dei nomi.
| Ambito dei criteri | I criteri di sicurezza dei pod non sono spazi dei nomi | I modelli di vincolo usati dai Criteri di Azure non sono spazi dei nomi.
| Azione Deny/Audit/Mutation | I criteri di sicurezza dei pod supportano solo azioni di rifiuto. La modifica può essere eseguita con i valori predefiniti nelle richieste di creazione. La convalida può essere eseguita durante le richieste di aggiornamento.| Criteri di Azure supporta entrambe le azioni di & di rifiuto. La mutazione non è ancora supportata, ma pianificata.
| Conformità dei criteri di sicurezza dei pod | Non esiste visibilità sulla conformità dei pod prima di abilitare i criteri di sicurezza dei pod. I pod non conformi creati dopo l'abilitazione dei criteri di sicurezza dei pod vengono negati. | I pod non conformi esistenti prima dell'applicazione dei criteri di Azure vengono visualizzati nelle violazioni dei criteri. I pod non conformi creati dopo l'abilitazione dei criteri di Azure vengono negati se i criteri vengono impostati con un effetto di rifiuto.
| Come visualizzare i criteri nel cluster | `kubectl get psp` | `kubectl get constrainttemplate` - Vengono restituiti tutti i criteri.
| Standard dei criteri di sicurezza dei pod - Con privilegi | Per impostazione predefinita, quando si abilita la funzionalità viene creata una risorsa criteri di sicurezza dei pod con privilegi. | La modalità con privilegi non implica alcuna restrizione, di conseguenza equivale a non avere alcuna Criteri di Azure assegnazione.
| [Standard dei criteri di sicurezza dei pod - Baseline/default](https://kubernetes.io/docs/concepts/security/pod-security-standards/#baseline-default) | L'utente installa una risorsa di base dei criteri di sicurezza del pod. | Criteri di Azure offre [un'iniziativa di base](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d) incorporata che esegue il mapping ai criteri di sicurezza dei pod di base.
| [Standard dei criteri di sicurezza dei pod - Con restrizioni](https://kubernetes.io/docs/concepts/security/pod-security-standards/#restricted) | L'utente installa una risorsa con restrizioni per i criteri di sicurezza dei pod. | Criteri di Azure offre [un'iniziativa con](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2F42b8ef37-b724-4e24-bbc8-7a7708edfe00) restrizioni incorporata che esegue il mapping ai criteri di sicurezza dei pod con restrizioni.

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>Abilitare i criteri di sicurezza dei pod in un cluster del servizio Contenitore di AKS

È possibile abilitare o disabilitare i criteri di sicurezza dei pod usando il [comando az servizio][az-aks-update] Web Sdk update. L'esempio seguente abilita i criteri di sicurezza dei pod nel nome del cluster *myAKSCluster* nel gruppo di risorse *denominato myResourceGroup*.

> [!NOTE]
> Per l'uso reale, non abilitare i criteri di sicurezza dei pod fino a quando non sono stati definiti criteri personalizzati. In questo articolo vengono abilitati i criteri di sicurezza dei pod come primo passaggio per vedere come i criteri predefiniti limitano le distribuzioni di pod.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>Criteri predefiniti del gateway di controllo di accesso

Quando si abilitano i criteri di sicurezza dei pod, il gateway di controllo di accesso crea un criterio predefinito denominato *con privilegi.* Non modificare o rimuovere i criteri predefiniti. Creare invece criteri personalizzati che definiscono le impostazioni da controllare. Si esamini prima di tutto il modo in cui questi criteri predefiniti influiscono sulle distribuzioni di pod.

Per visualizzare i criteri disponibili, usare il [comando kubectl get psp,][kubectl-get] come illustrato nell'esempio seguente

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *     configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

I *criteri di sicurezza* dei pod con privilegi vengono applicati a qualsiasi utente autenticato nel cluster del servizio Contenitore di AKS. Questa assegnazione è controllata da ClusterRoles e ClusterRoleBindings. Usare il [comando kubectl get rolebindings][kubectl-get] e cercare il binding *default:privileged:* nello spazio dei *nomi kube-system:*

```console
kubectl get rolebindings default:privileged -n kube-system -o yaml
```

Come illustrato nell'output condensato seguente, il ruolo ClusterRole *psp:privileged* viene assegnato a qualsiasi *utente system:authenticated.* Questa possibilità offre un livello di privilegio di base senza che vengano definiti criteri personalizzati.

```
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  [...]
  name: default:privileged
  [...]
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp:privileged
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:masters
```

È importante comprendere in che modo questi criteri predefiniti interagiscono con le richieste degli utenti per pianificare i pod prima di iniziare a creare criteri di sicurezza dei pod personalizzati. Nelle sezioni successive verranno pianificati alcuni pod per visualizzare i criteri predefiniti in azione.

## <a name="create-a-test-user-in-an-aks-cluster"></a>Creare un utente di test in un cluster del servizio AKS

Per impostazione predefinita, quando si usa il [comando az aks get-credentials,][az-aks-get-credentials] le credenziali di amministratore per il cluster del servizio Gateway Gateway vengono aggiunte alla  `kubectl` configurazione. L'utente amministratore ignora l'applicazione dei criteri di sicurezza dei pod. Se si usa l Azure Active Directory integra per i cluster del servizio Web del servizio App, è possibile accedere con le credenziali di un utente non amministratore per visualizzare l'applicazione dei criteri in azione. In questo articolo verrà creato un account utente di test nel cluster del servizio Web Del servizio Web di Microsoft Windows che è possibile usare.

Creare uno spazio dei nomi di esempio denominato *psp-aks* per le risorse di test usando il [comando kubectl create namespace.][kubectl-create] Creare quindi un account del servizio *denominato nonadmin-user* usando il [comando kubectl create serviceaccount:][kubectl-create]

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

Creare quindi un roleBinding per *l'utente non* amministratore per eseguire azioni di base nello spazio dei nomi usando il [comando kubectl create rolebinding:][kubectl-create]

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>Creare comandi alias per l'utente amministratore e non amministratore

Per evidenziare la differenza tra l'utente amministratore normale quando si usa e l'utente non amministratore creato nei passaggi precedenti, creare due alias della `kubectl` riga di comando:

* **L'alias kubectl-admin** è per l'utente amministratore normale e ha come ambito lo spazio dei nomi *psp-aks.*
* **L'alias kubectl-nonadminuser** è per l'utente *non* amministratore creato nel passaggio precedente e ha come ambito lo spazio dei nomi *psp-aks.*

Creare questi due alias come illustrato nei comandi seguenti:

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>Testare la creazione di un pod con privilegi

Si esamini prima cosa accade quando si pianifica un pod con il contesto di sicurezza di `privileged: true` . Questo contesto di sicurezza aumenta i privilegi del pod. Nella sezione precedente in cui sono stati mostrati i criteri di sicurezza dei pod del servizio Contenitore di AKS predefiniti, i criteri *dei* privilegi devono rifiutare questa richiesta.

Creare un file denominato `nginx-privileged.yaml` e incollare il manifesto YAML seguente:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.14.2-alpine
      securityContext:
        privileged: true
```

Creare il pod usando il [comando kubectl apply][kubectl-apply] e specificare il nome del manifesto YAML:

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

Non è possibile programmare il pod, come illustrato nell'output di esempio seguente:

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: []
```

Il pod non raggiunge la fase di pianificazione, quindi non ci sono risorse da eliminare prima di procedere.

## <a name="test-creation-of-an-unprivileged-pod"></a>Testare la creazione di un pod senza privilegi

Nell'esempio precedente la specifica del pod ha richiesto l'escalation dei privilegi. Questa richiesta viene negata dai criteri di sicurezza dei *pod* con privilegi predefiniti, quindi non è possibile programmare il pod. Provare ora a eseguire lo stesso pod NGINX senza la richiesta di escalation dei privilegi.

Creare un file denominato `nginx-unprivileged.yaml` e incollare il manifesto YAML seguente:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.14.2-alpine
```

Creare il pod usando il [comando kubectl apply][kubectl-apply] e specificare il nome del manifesto YAML:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Non è possibile programmare il pod, come illustrato nell'output di esempio seguente:

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged.yaml": pods "nginx-unprivileged" is forbidden: unable to validate against any pod security policy: []
```

Il pod non raggiunge la fase di pianificazione, quindi non ci sono risorse da eliminare prima di procedere.

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>Testare la creazione di un pod con un contesto utente specifico

Nell'esempio precedente l'immagine del contenitore ha tentato automaticamente di usare root per associare NGINX alla porta 80. Questa richiesta è stata negata dai criteri di sicurezza *dei* pod con privilegi predefiniti, quindi il pod non viene avviato. Provare ora a eseguire lo stesso pod NGINX con un contesto utente specifico, ad esempio `runAsUser: 2000` .

Creare un file denominato `nginx-unprivileged-nonroot.yaml` e incollare il manifesto YAML seguente:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged-nonroot
spec:
  containers:
    - name: nginx-unprivileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.14.2-alpine
      securityContext:
        runAsUser: 2000
```

Creare il pod usando il [comando kubectl apply][kubectl-apply] e specificare il nome del manifesto YAML:

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

Non è possibile programmare il pod, come illustrato nell'output di esempio seguente:

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged-nonroot.yaml": pods "nginx-unprivileged-nonroot" is forbidden: unable to validate against any pod security policy: []
```

Il pod non raggiunge la fase di pianificazione, quindi non ci sono risorse da eliminare prima di procedere.

## <a name="create-a-custom-pod-security-policy"></a>Creare criteri di sicurezza dei pod personalizzati

Dopo aver visto il comportamento dei criteri di sicurezza dei pod predefiniti, è possibile consentire *all'utente non* amministratore di pianificare correttamente i pod.

Si creerà un criterio per rifiutare i pod che richiedono l'accesso con privilegi. Altre opzioni, ad esempio *runAsUser* o i volumi *consentiti,* non sono limitate in modo esplicito. Questo tipo di criteri nega una richiesta di accesso con privilegi, ma in caso contrario consente al cluster di eseguire i pod richiesti.

Creare un file denominato `psp-deny-privileged.yaml` e incollare il manifesto YAML seguente:

```yaml
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: psp-deny-privileged
spec:
  privileged: false
  seLinux:
    rule: RunAsAny
  supplementalGroups:
    rule: RunAsAny
  runAsUser:
    rule: RunAsAny
  fsGroup:
    rule: RunAsAny
  volumes:
  - '*'
```

Creare i criteri usando il [comando kubectl apply][kubectl-apply] e specificare il nome del manifesto YAML:

```console
kubectl apply -f psp-deny-privileged.yaml
```

Per visualizzare i criteri disponibili, usare il [comando kubectl get psp,][kubectl-get] come illustrato nell'esempio seguente. Confrontare i *criteri psp-deny-privileged* con i criteri dei privilegi predefiniti applicati negli esempi precedenti per creare un pod.  Il criterio nega solo l'uso dell'escalation *PRIV.* Non sono presenti restrizioni per l'utente o il gruppo per i *criteri psp-deny-privileged.*

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *          
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>Consentire all'account utente di usare i criteri di sicurezza dei pod personalizzati

Nel passaggio precedente sono stati creati criteri di sicurezza dei pod per rifiutare i pod che richiedono l'accesso con privilegi. Per consentire l'uso dei criteri, creare un *ruolo* o *un clusterRole*. Quindi, associare uno di questi ruoli usando *roleBinding* o *ClusterRoleBinding*.

Per questo esempio, creare un clusterRole che consente di usare *il* *criterio psp-deny-privileged* creato nel passaggio precedente. Creare un file denominato `psp-deny-privileged-clusterrole.yaml` e incollare il manifesto YAML seguente:

```yaml
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: psp-deny-privileged-clusterrole
rules:
- apiGroups:
  - extensions
  resources:
  - podsecuritypolicies
  resourceNames:
  - psp-deny-privileged
  verbs:
  - use
```

Creare ClusterRole usando il [comando kubectl apply][kubectl-apply] e specificare il nome del manifesto YAML:

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

Creare ora un oggetto ClusterRoleBinding per usare clusterRole creato nel passaggio precedente. Creare un file denominato `psp-deny-privileged-clusterrolebinding.yaml` e incollare il manifesto YAML seguente:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: psp-deny-privileged-clusterrolebinding
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp-deny-privileged-clusterrole
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:serviceaccounts
```

Creare un ClusterRoleBinding usando il [comando kubectl apply][kubectl-apply] e specificare il nome del manifesto YAML:

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> Nel primo passaggio di questo articolo la funzionalità dei criteri di sicurezza dei pod è stata abilitata nel cluster del servizio Contenitore di AKS. La procedura consigliata consisteva nell'abilitare la funzionalità dei criteri di sicurezza dei pod solo dopo aver definito i propri criteri. Questa è la fase in cui si abiliterà la funzionalità dei criteri di sicurezza dei pod. Sono stati definiti uno o più criteri personalizzati e a tali criteri sono stati associati account utente. È ora possibile abilitare in modo sicuro la funzionalità dei criteri di sicurezza dei pod e ridurre al minimo i problemi causati dai criteri predefiniti.

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>Testare di nuovo la creazione di un pod senza privilegi

Dopo aver applicato i criteri di sicurezza dei pod personalizzati e avere un'associazione per consentire all'account utente di usare i criteri, provare a creare di nuovo un pod senza privilegi. Usare lo stesso `nginx-privileged.yaml` manifesto per creare il pod usando il comando [kubectl apply:][kubectl-apply]

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Il pod è stato pianificato correttamente. Quando si controlla lo stato del pod usando il [comando kubectl get pods,][kubectl-get] il pod è *In esecuzione:*

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

Questo esempio illustra come creare criteri di sicurezza dei pod personalizzati per definire l'accesso al cluster del servizio Contenitore di AKS per utenti o gruppi diversi. I criteri predefiniti del servizio Web Diaks forniscono controlli rigidi sui pod che possono essere eseguiti, quindi creare criteri personalizzati per definire correttamente le restrizioni necessarie.

Eliminare il pod senza privilegi NGINX usando il [comando kubectl delete][kubectl-delete] e specificare il nome del manifesto YAML:

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>Pulire le risorse

Per disabilitare i criteri di sicurezza dei pod, usare di nuovo [il comando az aks update.][az-aks-update] L'esempio seguente disabilita i criteri di sicurezza dei pod nel nome del cluster *myAKSCluster* nel gruppo di risorse *denominato myResourceGroup*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --disable-pod-security-policy
```

Eliminare quindi ClusterRole e ClusterRoleBinding:

```console
kubectl delete -f psp-deny-privileged-clusterrolebinding.yaml
kubectl delete -f psp-deny-privileged-clusterrole.yaml
```

Eliminare i criteri di sicurezza [usando il comando kubectl delete][kubectl-delete] e specificare il nome del manifesto YAML:

```console
kubectl delete -f psp-deny-privileged.yaml
```

Infine, eliminare lo spazio dei *nomi psp-aks:*

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha illustrato come creare criteri di sicurezza dei pod per impedire l'uso dell'accesso con privilegi. I criteri possono applicare numerose funzionalità, ad esempio il tipo di volume o l'utente RunAs. Per altre informazioni sulle opzioni disponibili, vedere la documentazione di riferimento sui criteri di sicurezza dei [pod Kubernetes][kubernetes-policy-reference].

Per altre informazioni sulla limitazione del traffico di rete dei pod, vedere Proteggere il traffico tra [pod usando i criteri di rete nel servizio Web Del servizio App.][network-policies]

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubernetes-policy-reference]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#policy-reference
<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policies]: use-network-policies.md
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-extension-add]: /cli/azure/extension#az_extension_add
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[policy-samples]: ./policy-reference.md#microsoftcontainerservice
[azure-policy-add-on]: ../governance/policy/concepts/policy-for-kubernetes.md
