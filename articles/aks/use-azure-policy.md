---
title: Usare i criteri di Azure per proteggere il cluster
description: Usare criteri di Azure per proteggere un cluster Azure Kubernetes Service (AKS).
ms.service: container-service
ms.topic: how-to
ms.date: 02/17/2021
ms.custom: template-how-to
ms.openlocfilehash: 46e92e6842204cd323992a2561e71302bb9cc722
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102193441"
---
# <a name="secure-your-cluster-with-azure-policy"></a>Proteggere il cluster con criteri di Azure

Per migliorare la sicurezza del cluster Azure Kubernetes Service (AKS), è possibile applicare e applicare i criteri di sicurezza predefiniti nel cluster usando criteri di Azure. [Criteri di Azure][azure-policy] consente di applicare gli standard aziendali e di valutare la conformità su larga scala. Dopo aver installato il [componente aggiuntivo criteri di Azure per][kubernetes-policy-reference]il servizio contenitore di Azure, è possibile applicare singole definizioni di criteri o gruppi di definizioni denominate iniziative (talvolta denominate policysets) al cluster. Per un elenco completo delle definizioni dei criteri e delle iniziative di AKS, vedere [definizioni predefinite di criteri di Azure per AKS][aks-policies] .

Questo articolo illustra come applicare le definizioni dei criteri al cluster e verificare che tali assegnazioni siano applicate.

## <a name="prerequisites"></a>Prerequisiti

- Un cluster AKS esistente. Se è necessario un cluster del servizio Azure Kubernetes, vedere la guida di avvio rapido sul servizio Azure Kubernetes [Uso dell'interfaccia della riga di comando di Azure][aks-quickstart-cli] oppure [Uso del portale di Azure][aks-quickstart-portal].
- Componente aggiuntivo criteri di Azure per AKS installato in un cluster AKS. Seguire questa [procedura per installare il componente aggiuntivo criteri di Azure][azure-policy-addon].

## <a name="assign-a-built-in-policy-definition-or-initiative"></a>Assegnare un'iniziativa o una definizione dei criteri predefinita

Per applicare una definizione dei criteri o un'iniziativa, utilizzare il portale di Azure.

1. Passare al servizio criteri di Azure in portale di Azure.
1. Nel riquadro a sinistra della pagina Criteri di Azure selezionare **Definizioni**.
1. In **categorie** selezionare `Kubernetes` .
1. Scegliere la definizione di criteri o l'iniziativa che si desidera applicare. Per questo esempio, selezionare l' `Kubernetes cluster pod security baseline standards for Linux-based workloads` iniziativa.
1. Selezionare **Assegna**.
1. Impostare l' **ambito** sul gruppo di risorse del cluster AKS con il componente aggiuntivo criteri di Azure abilitato.
1. Selezionare la pagina **parametri** e aggiornare l' **effetto** da `audit` a `deny` per bloccare le nuove distribuzioni che violano l'iniziativa Baseline. È anche possibile aggiungere altri spazi dei nomi da escludere dalla valutazione. Per questo esempio, Mantieni i valori predefiniti.
1. Selezionare **Verifica + crea** e quindi **Crea** per inviare l'assegnazione dei criteri.

## <a name="validate-a-azure-policy-is-running"></a>Convalida di un criterio di Azure in esecuzione

Verificare che le assegnazioni di criteri vengano applicate al cluster eseguendo le operazioni seguenti:

```azurecli-interactive
kubectl get constrainttemplates
```

> [!NOTE]
> Le assegnazioni di criteri possono richiedere [fino a 20 minuti per la sincronizzazione][azure-policy-assign-policy] in ogni cluster.

L'output dovrebbe essere simile al seguente:

```console
$ kubectl get constrainttemplate
NAME                                     AGE
k8sazureallowedcapabilities              23m
k8sazureallowedusersgroups               23m
k8sazureblockhostnamespace               23m
k8sazurecontainerallowedimages           23m
k8sazurecontainerallowedports            23m
k8sazurecontainerlimits                  23m
k8sazurecontainernoprivilege             23m
k8sazurecontainernoprivilegeescalation   23m
k8sazureenforceapparmor                  23m
k8sazurehostfilesystem                   23m
k8sazurehostnetworkingports              23m
k8sazurereadonlyrootfilesystem           23m
k8sazureserviceallowedports              23m
```

### <a name="validate-rejection-of-a-privileged-pod"></a>Convalidare il rifiuto di un pod con privilegi

Si contesterà innanzitutto cosa accade quando si pianifica un pod con il contesto di sicurezza di `privileged: true` . Questo contesto di sicurezza inoltra i privilegi del Pod. L'iniziativa non consente i pod con privilegi, quindi la richiesta verrà negata, causando il rifiuto della distribuzione.

Creare un file denominato `nginx-privileged.yaml` e incollare il manifesto YAML seguente:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
      securityContext:
        privileged: true
```

Creare il pod con [kubectl Apply][kubectl-apply] comando e specificare il nome del manifesto YAML:

```console
kubectl apply -f nginx-privileged.yaml
```

Come previsto, il Pod non viene pianificato, come illustrato nell'output di esempio seguente:

```console
$ kubectl apply -f privileged.yaml

Error from server ([denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}): error when creating "privileged.yaml": admission webhook "validation.gatekeeper.sh" denied the request: [denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}
```

Il Pod non raggiunge la fase di pianificazione, quindi non sono presenti risorse da eliminare prima di procedere.

### <a name="test-creation-of-an-unprivileged-pod"></a>Creazione di test di un pod senza privilegi

Nell'esempio precedente, l'immagine del contenitore ha provato automaticamente a usare la radice per associare NGINX alla porta 80. Questa richiesta è stata negata dall'iniziativa Policy, quindi il Pod non viene avviato. Provare ora a eseguire lo stesso pod NGINX senza accesso con privilegi.

Creare un file denominato `nginx-unprivileged.yaml` e incollare il manifesto YAML seguente:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
```

Creare il pod usando il comando [kubectl Apply][kubectl-apply] e specificare il nome del manifesto YAML:

```console
kubectl apply -f nginx-unprivileged.yaml
```

La pianificazione del Pod è stata completata. Quando si controlla lo stato del pod usando il comando [kubectl Get][kubectl-get] pods, il Pod è *in esecuzione*:

```console
$ kubectl get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          18s
```

Questo esempio illustra l'iniziativa baseline che influisce solo sulle distribuzioni che violano i criteri della raccolta. Le distribuzioni consentite continuano a funzionare.

Eliminare il Pod senza privilegi NGINX usando il comando [kubectl Delete][kubectl-delete] e specificare il nome del manifesto YAML:

```console
kubectl delete -f nginx-unprivileged.yaml
```

## <a name="disable-a-policy-or-initiative"></a>Disabilitare un criterio o un'iniziativa

Per rimuovere l'iniziativa Baseline:

1. Passare al riquadro criteri nella portale di Azure.
1. Selezionare **assegnazioni** dal riquadro sinistro.
1. Fare clic sul pulsante **...** accanto all' `Kubernetes cluster pod security baseline standards for Linux-based workloads` iniziativa.
1. Selezionare **Elimina assegnazione**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul funzionamento dei criteri di Azure:

- [Panoramica dei criteri di Azure][azure-policy]
- [Iniziative e criteri di criteri di Azure per AKS][aks-policies]
- Rimuovere il [componente aggiuntivo criteri di Azure][azure-policy-addon-remove].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs

<!-- LINKS - internal -->
[aks-policies]: policy-reference.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[azure-policy]: ../governance/policy/overview.md
[azure-policy-addon]: ../governance/policy/concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-aks
[azure-policy-addon-remove]: ../governance/policy/concepts/policy-for-kubernetes.md#remove-the-add-on-from-aks
[azure-policy-assign-policy]: ../governance/policy/concepts/policy-for-kubernetes.md#assign-a-built-in-policy-definition
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[kubernetes-policy-reference]: ../governance/policy/concepts/policy-for-kubernetes.md
