---
title: Usare i criteri di sicurezza pod in Azure Kubernetes Service (AKS)
description: Informazioni su come controllare l'ammissione di pod usando PodSecurityPolicy in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 02/12/2021
ms.openlocfilehash: cb317e5e0d1f558121e675f569bad37811768ca6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102180310"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>Anteprima-proteggere il cluster usando i criteri di sicurezza pod in Azure Kubernetes Service (AKS)

> [!WARNING]
> **La funzionalità descritta in questo documento Pod Security Policy (anteprima) è impostata per la deprecazione e non sarà più disponibile dopo il 30 giugno 2021** a favore di criteri di [Azure per AKS](use-azure-policy.md). La data di deprecazione è stata estesa dalla data precedente del 15 ottobre 2020.
>
> Dopo la deprecazione di Criteri di sicurezza dei pod (anteprima), sarà necessario disabilitare la funzionalità in eventuali cluster esistenti che usano la funzionalità deprecata per eseguire aggiornamenti futuri del cluster e mantenere il supporto tecnico di Azure.
>
> È consigliabile iniziare a testare gli scenari con criteri di Azure per AKS, che offre criteri predefiniti per proteggere i pod e le iniziative predefinite che vengono mappate ai criteri di sicurezza pod. Per eseguire la migrazione dai criteri di sicurezza Pod, è necessario eseguire le azioni seguenti in un cluster.
> 
> 1. [Disabilitare i criteri di sicurezza Pod](#clean-up-resources) nel cluster
> 1. Abilitare il [componente aggiuntivo criteri di Azure][kubernetes-policy-reference]
> 1. Abilitare i criteri di Azure desiderati dai [criteri predefiniti disponibili][policy-samples]
> 1. Esaminare [le modifiche del comportamento tra i criteri di sicurezza pod e i criteri di Azure](#behavior-changes-between-pod-security-policy-and-azure-policy)

Per migliorare la sicurezza del cluster AKS, è possibile limitare i pod che è possibile pianificare. I pod che richiedono risorse non consentite non possono essere eseguiti nel cluster AKS. Questo accesso viene definito usando i criteri di sicurezza pod. Questo articolo illustra come usare i criteri di sicurezza di pod per limitare la distribuzione di pod in AKS.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si disponga di un cluster del servizio Azure Kubernetes esistente. Se è necessario un cluster del servizio Azure Kubernetes, vedere la guida di avvio rapido sul servizio Azure Kubernetes [Uso dell'interfaccia della riga di comando di Azure][aks-quickstart-cli] oppure [Uso del portale di Azure][aks-quickstart-portal].

È necessario che sia installata e configurata l'interfaccia della riga di comando di Azure 2.0.61 o versioni successive. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Installare l'estensione dell'interfaccia della riga di comando aks-preview

Per usare i criteri di sicurezza di Pod, è necessaria l'estensione dell'interfaccia della riga di comando *AKS-Preview* 0.4.1 o versione successiva. Installare l'estensione *aks-preview* dell'interfaccia della riga di comando di Azure usando il comando [az extension add][az-extension-add], quindi verificare la disponibilità di eventuali aggiornamenti usando il comando [az extension update][az-extension-update]:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-pod-security-policy-feature-provider"></a>Registrare il provider di funzionalità dei criteri di sicurezza Pod

**Il documento e la funzionalità sono impostati per la deprecazione il 15 ottobre 2020.**

Per creare o aggiornare un cluster AKS per usare i criteri di sicurezza Pod, abilitare prima di tutto un flag funzionalità per la sottoscrizione. Per registrare il flag della funzionalità *PodSecurityPolicyPreview* , usare il comando [AZ feature Register][az-feature-register] , come illustrato nell'esempio seguente:

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

Sono necessari alcuni minuti per visualizzare lo stato *Registered*. È possibile controllare lo stato di registrazione usando il comando [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

Quando si è pronti, aggiornare la registrazione del provider di risorse *Microsoft. servizio contenitore* usando il comando [AZ provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>Panoramica dei criteri di sicurezza di Pod

In un cluster Kubernetes, un controller di ammissione viene usato per intercettare le richieste al server API quando è necessario creare una risorsa. Il controller di ammissione può quindi *convalidare* la richiesta di risorse in base a un set di regole o *mutare* la risorsa per modificare i parametri di distribuzione.

*PodSecurityPolicy* è un controller di ammissione che convalida una specifica Pod soddisfa i requisiti definiti. Questi requisiti possono limitare l'uso di contenitori con privilegi, l'accesso a determinati tipi di archiviazione o l'utente o il gruppo con cui il contenitore può essere eseguito. Quando si tenta di distribuire una risorsa in cui le specifiche del Pod non soddisfano i requisiti indicati nei criteri di sicurezza Pod, la richiesta viene negata. Questa possibilità di controllare quali Pod è possibile pianificare nel cluster AKS impedisce alcune possibili vulnerabilità di sicurezza o escalation dei privilegi.

Quando si abilitano i criteri di sicurezza pod in un cluster AKS, vengono applicati alcuni criteri predefiniti. Questi criteri predefiniti offrono un'esperienza predefinita per definire quali Pod è possibile pianificare. Tuttavia, gli utenti del cluster potrebbero riscontrare problemi durante la distribuzione di pod fino a quando non si definiscono i propri criteri. L'approccio consigliato consiste nell'eseguire le operazioni seguenti:

* Creare un cluster AKS
* Definire i propri criteri di sicurezza Pod
* Abilitare la funzionalità dei criteri di sicurezza Pod

Per mostrare in che modo i criteri predefiniti limitano le distribuzioni Pod, in questo articolo viene prima abilitata la funzionalità criteri di sicurezza Pod, quindi si crea un criterio personalizzato.

### <a name="behavior-changes-between-pod-security-policy-and-azure-policy"></a>Differenze di comportamento tra i criteri di sicurezza pod e i criteri di Azure

Di seguito è riportato un riepilogo delle modifiche del comportamento tra i criteri di sicurezza di Pod e i criteri di Azure.

|Scenario| Criteri di sicurezza Pod | Criteri di Azure |
|---|---|---|
|Installazione|Abilitare la funzionalità dei criteri di sicurezza Pod |Abilita il componente aggiuntivo criteri di Azure
|Distribuisci criteri| Distribuire la risorsa criteri di sicurezza Pod| Assegnare i criteri di Azure all'ambito della sottoscrizione o del gruppo di risorse. Il componente aggiuntivo criteri di Azure è necessario per le applicazioni di risorse Kubernetes.
| Criteri predefiniti | Quando i criteri di sicurezza pod sono abilitati in AKS, vengono applicati i criteri predefiniti con privilegi e senza restrizioni. | Non vengono applicati criteri predefiniti abilitando il componente aggiuntivo criteri di Azure. È necessario abilitare in modo esplicito i criteri in criteri di Azure.
| Utenti che possono creare e assegnare criteri | Amministrazione cluster crea una risorsa criteri di sicurezza Pod | Gli utenti devono avere un ruolo minimo di autorizzazioni ' proprietario ' o ' collaboratore criteri risorse ' nel gruppo di risorse del cluster AKS. -Tramite l'API, gli utenti possono assegnare i criteri nell'ambito delle risorse del cluster AKS. L'utente deve avere almeno le autorizzazioni "proprietario" o "collaboratore criteri risorse" per la risorsa cluster AKS. -Nella portale di Azure i criteri possono essere assegnati a livello di gruppo di gestione/sottoscrizione/gruppo di risorse.
| Autorizzazione di criteri| Gli account utente e del servizio richiedono autorizzazioni esplicite per l'uso di criteri di sicurezza pod. | Per autorizzare i criteri non è necessaria alcuna assegnazione aggiuntiva. Dopo che i criteri sono stati assegnati in Azure, tutti gli utenti del cluster possono usare questi criteri.
| Applicabilità dei criteri | L'utente amministratore ignora l'applicazione dei criteri di sicurezza pod. | Tutti gli utenti (amministratore & non amministratore) vedono gli stessi criteri. Non sono presenti maiuscole e minuscole specifiche in base agli utenti. L'applicazione di criteri può essere esclusa a livello di spazio dei nomi.
| Ambito dei criteri | I criteri di sicurezza Pod non sono spazio dei nomi | I modelli di vincolo usati dai criteri di Azure non sono spazio dei nomi.
| Azione di negazione/controllo/mutazione | I criteri di sicurezza Pod supportano solo le azioni di negazione. La mutazione può essere eseguita con i valori predefiniti per le richieste di creazione. La convalida può essere eseguita durante le richieste di aggiornamento.| Criteri di Azure supporta entrambe le azioni di controllo & negazione. La mutazione non è ancora supportata, ma è pianificata.
| Conformità dei criteri di sicurezza Pod | Non esiste visibilità sulla conformità dei Pod esistenti prima di abilitare i criteri di sicurezza di Pod. I Pod non conformi creati dopo l'abilitazione di criteri di sicurezza pod sono negati. | I Pod non conformi esistenti prima di applicare i criteri di Azure verranno visualizzati nelle violazioni dei criteri. I Pod non conformi creati dopo l'abilitazione di criteri di Azure vengono negati se i criteri vengono impostati con un effetto di negazione.
| Come visualizzare i criteri nel cluster | `kubectl get psp` | `kubectl get constrainttemplate` -Vengono restituiti tutti i criteri.
| Criteri di sicurezza Pod standard-con privilegi | Quando si Abilita la funzionalità, per impostazione predefinita viene creata una risorsa di criteri di sicurezza di Pod con privilegi. | La modalità con privilegi non implica alcuna restrizione, di conseguenza è equivalente all'assenza di un'assegnazione di criteri di Azure.
| [Criteri di sicurezza Pod standard-Baseline/default](https://kubernetes.io/docs/concepts/security/pod-security-standards/#baseline-default) | L'utente installa una risorsa di base dei criteri di sicurezza pod. | Criteri di Azure fornisce un' [iniziativa di base predefinita](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d) che esegue il mapping ai criteri di sicurezza pod di base.
| [Criteri di sicurezza Pod standard-con restrizioni](https://kubernetes.io/docs/concepts/security/pod-security-standards/#restricted) | L'utente installa una risorsa con restrizioni dei criteri di sicurezza pod. | Criteri di Azure fornisce un' [iniziativa limitata incorporata](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2F42b8ef37-b724-4e24-bbc8-7a7708edfe00) che esegue il mapping ai criteri di sicurezza di Pod limitati.

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>Abilitare i criteri di sicurezza pod in un cluster AKS

È possibile abilitare o disabilitare i criteri di sicurezza pod usando il comando [AZ AKS Update][az-aks-update] . Nell'esempio seguente vengono abilitati i criteri di sicurezza Pod nel nome del cluster *myAKSCluster* nel gruppo di risorse denominato *myResourceGroup*.

> [!NOTE]
> Per usare il mondo reale, non abilitare i criteri di sicurezza pod fino a quando non sono stati definiti criteri personalizzati. In questo articolo vengono abilitati i criteri di sicurezza pod come primo passaggio per vedere come i criteri predefiniti limitano le distribuzioni pod.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>Criteri AKS predefiniti

Quando si abilitano i criteri di sicurezza Pod, AKS crea un criterio predefinito denominato *Privileged*. Non modificare o rimuovere i criteri predefiniti. In alternativa, creare criteri personalizzati che definiscono le impostazioni che si desidera controllare. Per prima cosa si esaminano i criteri predefiniti che influiscano sulle distribuzioni pod.

Per visualizzare i criteri disponibili, usare il comando [kubectl Get PSP][kubectl-get] , come illustrato nell'esempio seguente

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *     configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

Il criterio di sicurezza di Pod con *privilegi* viene applicato a qualsiasi utente autenticato nel cluster AKS. Questa assegnazione viene controllata da ClusterRoles e ClusterRoleBindings. Usare il comando [kubectl Get rolebindings][kubectl-get] e cercare il *valore predefinito: Privileged:* binding nello spazio dei nomi *Kube-System* :

```console
kubectl get rolebindings default:privileged -n kube-system -o yaml
```

Come illustrato nel seguente output condensato, il punto di ClusterRole *PSP: Privileged* viene assegnato a qualsiasi *sistema: Authenticated* Users. Questa possibilità fornisce un livello di privilegio di base senza definire i propri criteri.

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

È importante comprendere come questi criteri predefiniti interagiscono con le richieste degli utenti per pianificare i pod prima di iniziare a creare i propri criteri di sicurezza pod. Nelle prossime sezioni si pianificano alcuni pod per visualizzare questi criteri predefiniti in azione.

## <a name="create-a-test-user-in-an-aks-cluster"></a>Creare un utente di test in un cluster AKS

Per impostazione predefinita, quando si usa il comando [AZ AKS Get-credentials][az-aks-get-credentials] , vengono aggiunte alla configurazione le credenziali di *amministratore* per il cluster AKS `kubectl` . L'utente amministratore ignora l'applicazione dei criteri di sicurezza pod. Se si usa Azure Active Directory integrazione per i cluster AKS, è possibile accedere con le credenziali di un utente non amministratore per vedere l'applicazione dei criteri in azione. In questo articolo viene creato un account utente di test nel cluster AKS che è possibile usare.

Creare uno spazio dei nomi di esempio denominato *PSP-AKS* per le risorse di test usando il comando [kubectl create namespace][kubectl-create] . Quindi, creare un account del servizio denominato *nonadmin-User* usando il comando [kubectl create ServiceAccount][kubectl-create] :

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

Successivamente, creare un oggetto Roleing per l' *utente non amministratore* per eseguire le azioni di base nello spazio dei nomi usando il comando [kubectl create rolement][kubectl-create] :

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>Creare comandi alias per utenti amministratori e non amministratori

Per evidenziare la differenza tra l'utente amministratore normale quando si usa `kubectl` e l'utente non amministratore creato nei passaggi precedenti, creare due alias della riga di comando:

* L'alias **kubectl-admin** è per l'utente amministratore normale e ha come ambito lo spazio dei nomi *PSP-AKS* .
* L'alias **kubectl-nonadminuser** è per l' *utente non amministratore* creato nel passaggio precedente e ha come ambito lo spazio dei nomi *PSP-AKS* .

Creare questi due alias, come illustrato nei comandi seguenti:

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>Testare la creazione di un pod con privilegi

Si contesterà innanzitutto cosa accade quando si pianifica un pod con il contesto di sicurezza di `privileged: true` . Questo contesto di sicurezza inoltra i privilegi del Pod. Nella sezione precedente sono stati illustrati i criteri di sicurezza di AKS Pod predefiniti. i criteri dei *privilegi* dovrebbero negare questa richiesta.

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

Creare il pod usando il comando [kubectl Apply][kubectl-apply] e specificare il nome del manifesto YAML:

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

Il Pod non viene pianificato, come illustrato nell'output di esempio seguente:

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: []
```

Il Pod non raggiunge la fase di pianificazione, quindi non sono presenti risorse da eliminare prima di procedere.

## <a name="test-creation-of-an-unprivileged-pod"></a>Creazione di test di un pod senza privilegi

Nell'esempio precedente, la specifica Pod ha richiesto l'escalation dei privilegi. Questa richiesta viene negata dai criteri di sicurezza del Pod dei *privilegi* predefiniti, pertanto non è possibile pianificare il pod. Provare ora a eseguire lo stesso pod NGINX senza la richiesta di escalation dei privilegi.

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

Creare il pod usando il comando [kubectl Apply][kubectl-apply] e specificare il nome del manifesto YAML:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Il Pod non viene pianificato, come illustrato nell'output di esempio seguente:

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged.yaml": pods "nginx-unprivileged" is forbidden: unable to validate against any pod security policy: []
```

Il Pod non raggiunge la fase di pianificazione, quindi non sono presenti risorse da eliminare prima di procedere.

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>Testare la creazione di un pod con un contesto utente specifico

Nell'esempio precedente, l'immagine del contenitore ha provato automaticamente a usare la radice per associare NGINX alla porta 80. Questa richiesta è stata negata dai criteri di sicurezza del Pod dei *privilegi* predefiniti, quindi il Pod non viene avviato. Provare ora a eseguire lo stesso pod NGINX con un contesto utente specifico, ad esempio `runAsUser: 2000` .

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

Creare il pod usando il comando [kubectl Apply][kubectl-apply] e specificare il nome del manifesto YAML:

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

Il Pod non viene pianificato, come illustrato nell'output di esempio seguente:

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged-nonroot.yaml": pods "nginx-unprivileged-nonroot" is forbidden: unable to validate against any pod security policy: []
```

Il Pod non raggiunge la fase di pianificazione, quindi non sono presenti risorse da eliminare prima di procedere.

## <a name="create-a-custom-pod-security-policy"></a>Creare un criterio di sicurezza Pod personalizzato

Ora che è stato visto il comportamento dei criteri di sicurezza di Pod predefiniti, è possibile consentire all'utente non *amministratore* di pianificare correttamente i pod.

Creiamo un criterio per rifiutare i pod che richiedono l'accesso con privilegi. Altre opzioni, ad esempio *runAsUser* o *volumi* consentiti, non sono esplicitamente limitate. Questo tipo di Criteri nega una richiesta di accesso con privilegi, ma in caso contrario consente al cluster di eseguire i pod richiesti.

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

Creare il criterio usando il comando [kubectl Apply][kubectl-apply] e specificare il nome del manifesto YAML:

```console
kubectl apply -f psp-deny-privileged.yaml
```

Per visualizzare i criteri disponibili, usare il comando [kubectl Get PSP][kubectl-get] , come illustrato nell'esempio seguente. Confrontare il criterio con *privilegi PSP-Deny* con i criteri dei *privilegi* predefiniti applicati negli esempi precedenti per creare un pod. Solo l'uso dell'escalation di *priv* viene negato dal criterio. Non sono previste restrizioni per l'utente o il gruppo per i criteri con *privilegi PSP-Deny-* .

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *          
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>Consenti all'account utente di usare i criteri di sicurezza Pod personalizzati

Nel passaggio precedente sono stati creati criteri di sicurezza pod per rifiutare i pod che richiedono l'accesso con privilegi. Per consentire l'utilizzo del criterio, è necessario creare un *ruolo* o un *ClusterRole*. Quindi, associare uno di questi ruoli utilizzando un oggetto *roleName* o *ClusterRoleBinding*.

Per questo esempio, creare un ClusterRole che consenta di *usare* il criterio con *privilegi PSP-Deny* creato nel passaggio precedente. Creare un file denominato `psp-deny-privileged-clusterrole.yaml` e incollare il manifesto YAML seguente:

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

Creare il ClusterRole usando il comando [kubectl Apply][kubectl-apply] e specificare il nome del manifesto YAML:

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

A questo punto, creare un ClusterRoleBinding per usare il ClusterRole creato nel passaggio precedente. Creare un file denominato `psp-deny-privileged-clusterrolebinding.yaml` e incollare il manifesto YAML seguente:

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

Creare una ClusterRoleBinding usando il comando [kubectl Apply][kubectl-apply] e specificare il nome del manifesto YAML:

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> Nel primo passaggio di questo articolo, la funzionalità dei criteri di sicurezza Pod è stata abilitata nel cluster AKS. La procedura consigliata consiste nell'abilitare la funzionalità dei criteri di sicurezza pod solo dopo aver definito i propri criteri. Questa è la fase in cui è possibile abilitare la funzionalità dei criteri di sicurezza pod. Sono stati definiti uno o più criteri personalizzati e gli account utente sono stati associati a tali criteri. A questo punto è possibile abilitare la funzionalità dei criteri di sicurezza di Pod e ridurre al minimo i problemi causati dai criteri predefiniti.

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>Testare di nuovo la creazione di un pod senza privilegi

Con i criteri di sicurezza personalizzati di Pod applicati e un binding per l'account utente per l'uso del criterio, provare a creare di nuovo un pod senza privilegi. Usare lo stesso `nginx-privileged.yaml` manifesto per creare il pod usando il comando [kubectl Apply][kubectl-apply] :

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

La pianificazione del Pod è stata completata. Quando si controlla lo stato del pod usando il comando [kubectl Get][kubectl-get] pods, il Pod è *in esecuzione*:

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

Questo esempio illustra come creare criteri di sicurezza Pod personalizzati per definire l'accesso al cluster AKS per utenti o gruppi diversi. I criteri AKS predefiniti forniscono controlli rigidi sui pod che è possibile eseguire, quindi creare criteri personalizzati per definire correttamente le restrizioni necessarie.

Eliminare il Pod senza privilegi NGINX usando il comando [kubectl Delete][kubectl-delete] e specificare il nome del manifesto YAML:

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>Pulire le risorse

Per disabilitare i criteri di sicurezza di Pod, usare di nuovo il comando [AZ AKS Update][az-aks-update] . Nell'esempio seguente vengono disabilitati i criteri di sicurezza Pod nel nome del cluster *myAKSCluster* nel gruppo di risorse denominato *myResourceGroup*:

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

Eliminare i criteri di sicurezza usando il comando [kubectl Delete][kubectl-delete] e specificare il nome del manifesto YAML:

```console
kubectl delete -f psp-deny-privileged.yaml
```

Eliminare infine lo spazio dei nomi *PSP-AKS* :

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha illustrato come creare un criterio di sicurezza pod per impedire l'uso dell'accesso con privilegi. È possibile applicare un criterio a numerose funzionalità, ad esempio il tipo di volume o l'utente RunAs. Per altre informazioni sulle opzioni disponibili, vedere la documentazione di riferimento per i [criteri di sicurezza Pod Kubernetes][kubernetes-policy-reference].

Per altre informazioni sulla limitazione del traffico di rete Pod, vedere [proteggere il traffico tra i pod usando i criteri di rete in AKS][network-policies].

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
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-extension-add]: /cli/azure/extension#az-extension-add
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[policy-samples]: ./policy-reference.md#microsoftcontainerservice
