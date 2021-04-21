---
title: Usare Criteri di Azure per proteggere il cluster
description: Usare Criteri di Azure per proteggere un cluster servizio Azure Kubernetes (AKS).
ms.service: container-service
ms.topic: how-to
ms.date: 02/17/2021
ms.custom: template-how-to
ms.openlocfilehash: 6462c2987155925b7df5241d8fb6aa13c1e37b89
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777726"
---
# <a name="secure-your-cluster-with-azure-policy"></a>Proteggere il cluster con Criteri di Azure

Per migliorare la sicurezza del cluster servizio Azure Kubernetes (AKS), è possibile applicare e applicare criteri di sicurezza predefiniti nel cluster usando Criteri di Azure. [Criteri di Azure][azure-policy] consente di applicare gli standard dell'organizzazione e di valutare la conformità su larga scala. Dopo aver installato il [componente aggiuntivo Criteri di Azure][kubernetes-policy-reference]per il servizio Web Disassociazione di , è possibile applicare al cluster singole definizioni di criteri o gruppi di definizioni di criteri denominati iniziative(talvolta denominati set di criteri). Per un elenco completo Criteri di Azure definizioni di iniziative e criteri del bot AKS, vedere l'Criteri di Azure definizioni di criteri e di iniziative del componente [AKS.][aks-policies]

Questo articolo illustra come applicare le definizioni dei criteri al cluster e verificare che tali assegnazioni vengano applicate.

## <a name="prerequisites"></a>Prerequisiti

- Un cluster del servizio Web Diaks esistente. Se è necessario un cluster del servizio Azure Kubernetes, vedere la guida di avvio rapido sul servizio Azure Kubernetes [Uso dell'interfaccia della riga di comando di Azure][aks-quickstart-cli] oppure [Uso del portale di Azure][aks-quickstart-portal].
- Il Criteri di Azure componente aggiuntivo per il servizio Web Disassoce installato in un cluster del servizio Web Disassoce. Seguire questa [procedura per installare il Criteri di Azure componente aggiuntivo][azure-policy-addon].

## <a name="assign-a-built-in-policy-definition-or-initiative"></a>Assegnare una definizione di criteri o un'iniziativa incorporata

Per applicare una definizione di criteri o un'iniziativa, usare il portale di Azure.

1. Passare al servizio Criteri di Azure in portale di Azure.
1. Nel riquadro a sinistra della pagina Criteri di Azure selezionare **Definizioni**.
1. In **Categorie** selezionare `Kubernetes` .
1. Scegliere la definizione di criteri o l'iniziativa da applicare. Per questo esempio, selezionare `Kubernetes cluster pod security baseline standards for Linux-based workloads` l'iniziativa .
1. Selezionare **Assegna**.
1. Impostare **l'ambito** sul gruppo di risorse del cluster del servizio Criteri di Azure con il componente aggiuntivo abilitato.
1. Selezionare la **pagina Parametri** e aggiornare **l'effetto** da a per bloccare le `audit` nuove `deny` distribuzioni che violano l'iniziativa di base. È anche possibile aggiungere altri spazi dei nomi da escludere dalla valutazione. Per questo esempio, mantenere i valori predefiniti.
1. Selezionare **Rivedi e crea e** quindi Crea **per** inviare l'assegnazione dei criteri.

## <a name="validate-a-azure-policy-is-running"></a>Convalidare un Criteri di Azure è in esecuzione

Verificare che le assegnazioni dei criteri siano applicate al cluster eseguendo le operazioni seguenti:

```azurecli-interactive
kubectl get constrainttemplates
```

> [!NOTE]
> La sincronizzazione delle assegnazioni dei criteri in ogni cluster può richiedere fino a [20][azure-policy-assign-policy] minuti.

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

Si esamini prima cosa accade quando si pianifica un pod con il contesto di sicurezza di `privileged: true` . Questo contesto di sicurezza aumenta i privilegi del pod. L'iniziativa non consente pod con privilegi, quindi la richiesta verrà negata e la distribuzione verrà rifiutata.

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

Creare il pod con [il comando kubectl apply][kubectl-apply] e specificare il nome del manifesto YAML:

```console
kubectl apply -f nginx-privileged.yaml
```

Come previsto, il pod non viene pianificato, come illustrato nell'output di esempio seguente:

```console
$ kubectl apply -f privileged.yaml

Error from server ([denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}): error when creating "privileged.yaml": admission webhook "validation.gatekeeper.sh" denied the request: [denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}
```

Il pod non raggiunge la fase di pianificazione, quindi non ci sono risorse da eliminare prima di procedere.

### <a name="test-creation-of-an-unprivileged-pod"></a>Testare la creazione di un pod senza privilegi

Nell'esempio precedente l'immagine del contenitore ha tentato automaticamente di usare root per associare NGINX alla porta 80. Questa richiesta è stata negata dall'iniziativa dei criteri, quindi l'avvio del pod non riesce. Provare ora a eseguire lo stesso pod NGINX senza accesso con privilegi.

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

Creare il pod usando il [comando kubectl apply][kubectl-apply] e specificare il nome del manifesto YAML:

```console
kubectl apply -f nginx-unprivileged.yaml
```

Il pod è stato pianificato correttamente. Quando si controlla lo stato del pod usando il [comando kubectl get pods,][kubectl-get] il pod è *In esecuzione:*

```console
$ kubectl get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          18s
```

Questo esempio illustra l'iniziativa di base che interessa solo le distribuzioni che violano i criteri nella raccolta. Le distribuzioni consentite continuano a funzionare.

Eliminare il pod senza privilegi NGINX usando il [comando kubectl delete][kubectl-delete] e specificare il nome del manifesto YAML:

```console
kubectl delete -f nginx-unprivileged.yaml
```

## <a name="disable-a-policy-or-initiative"></a>Disabilitare un criterio o un'iniziativa

Per rimuovere l'iniziativa di base:

1. Passare al riquadro Criteri nella portale di Azure.
1. Selezionare **Assegnazioni** nel riquadro sinistro.
1. Fare clic **sul pulsante ...** accanto all'iniziativa. `Kubernetes cluster pod security baseline standards for Linux-based workloads`
1. Selezionare **Elimina assegnazione**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul funzionamento Criteri di Azure:

- [Panoramica dei criteri di Azure][azure-policy]
- [Criteri di Azure iniziative e i controlli per il server del supporto di AKS][aks-policies]
- Rimuovere il [Criteri di Azure componente aggiuntivo .][azure-policy-addon-remove]

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
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[kubernetes-policy-reference]: ../governance/policy/concepts/policy-for-kubernetes.md
