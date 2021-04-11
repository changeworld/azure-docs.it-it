---
title: Procedure consigliate per la gestione dell'identità
titleSuffix: Azure Kubernetes Service
description: Procedure consigliate per l'operatore del cluster per la gestione dell'autenticazione e dell'autorizzazione per i cluster nel servizio Azure Kubernetes (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: jpalma
author: palma21
ms.openlocfilehash: de84e3e2a8da3e1b5195978a8a2204fdfa2108d7
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105103"
---
# <a name="best-practices-for-authentication-and-authorization-in-azure-kubernetes-service-aks"></a>Procedure consigliate per l'autenticazione e l'autorizzazione nel servizio Azure Kubernetes (AKS)

Quando si distribuiscono e si gestiscono i cluster in Azure Kubernetes Service (AKS), si implementano modi per gestire l'accesso alle risorse e ai servizi. Senza questi controlli:
* Gli account potrebbero avere accesso a risorse e servizi non necessari. 
* Il rilevamento del set di credenziali utilizzato per apportare modifiche potrebbe essere difficile.

Questo articolo sulle procedure consigliate è incentrato su come un operatore del cluster può gestire l'accesso e l'identità per i cluster servizio Azure Kubernetes. In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
>
> * Autenticare gli utenti del cluster AKS con Azure Active Directory.
> * Controllare l'accesso alle risorse con il controllo degli accessi in base al ruolo Kubernetes (Kubernetes RBAC).
> * Usare il controllo degli accessi in base al ruolo di Azure per controllare in modo granulare l'accesso alla risorsa AKS, l'API Kubernetes su larga scala e `kubeconfig` .
> * Usare un'identità gestita per autenticare i pod stessi con altri servizi.

## <a name="use-azure-active-directory-azure-ad"></a>USA Azure Active Directory (Azure AD)

> **Indicazioni sulle procedure consigliate** 
> 
> Distribuire i cluster AKS con Azure AD l'integrazione. L'uso di Azure AD consente di centralizzare il componente di gestione delle identità. Qualsiasi modifica all'account utente o allo stato del gruppo viene aggiornata automaticamente nell'accesso al cluster servizio Azure Kubernetes. Definire l'ambito di utenti o gruppi per le autorizzazioni minime utilizzando i [ruoli, ClusterRoles o le associazioni](#use-kubernetes-role-based-access-control-kubernetes-rbac).

Gli sviluppatori e i proprietari di applicazioni del cluster Kubernetes devono accedere a risorse diverse. Kubernetes non dispone di una soluzione di gestione delle identità per controllare le risorse con cui gli utenti possono interagire. In alternativa, in genere si integra il cluster con una soluzione di gestione delle identità esistente. Immettere Azure AD: una soluzione di gestione delle identità di livello aziendale che si integra con i cluster AKS.

Con i cluster integrati in Azure AD in AKS è possibile creare *ruoli* o *ClusterRoles* che definiscono le autorizzazioni di accesso alle risorse. Si *associano* quindi i ruoli a utenti o gruppi da Azure AD. Altre informazioni su questi Kubernetes RBAC sono disponibili nella [sezione successiva](#use-kubernetes-role-based-access-control-kubernetes-rbac). Il diagramma seguente illustra l'integrazione Azure AD e il controllo dell'accesso alle risorse:

![Autenticazione a livello di cluster per l'integrazione di Azure Active Directory con servizio Azure Kubernetes](media/operator-best-practices-identity/cluster-level-authentication-flow.png)

1. Lo sviluppatore viene autenticato con Azure AD.
1. L'endpoint di emissione del token di Azure AD emette il token di accesso.
1. Lo sviluppatore esegue un'azione utilizzando il token di Azure AD, ad esempio `kubectl create pod` .
1. Kubernetes convalida il token con Azure AD e recupera le appartenenze ai gruppi dello sviluppatore.
1. Vengono applicati i criteri RBAC e cluster Kubernetes.
1. La richiesta dello sviluppatore ha avuto esito positivo in base alla convalida precedente di Azure AD appartenenza al gruppo e ai criteri e RBAC Kubernetes.

Per creare un cluster AKS che usa Azure AD, vedere [Integrare Azure Active Directory con il servizio Azure Kubernetes][aks-aad].

## <a name="use-kubernetes-role-based-access-control-kubernetes-rbac"></a>Usare il controllo degli accessi in base al ruolo Kubernetes (Kubernetes RBAC)

> **Indicazioni sulle procedure consigliate**
> 
> Definire le autorizzazioni utente o gruppo per le risorse cluster con Kubernetes RBAC. Creare ruoli e associazioni che assegnano il numero minimo di autorizzazioni richieste. È possibile eseguire l'integrazione con Azure AD per aggiornare automaticamente eventuali modifiche dello stato utente o dell'appartenenza a un gruppo e rimanere aggiornati sulle risorse del cluster.

In Kubernetes è possibile fornire il controllo di accesso granulare alle risorse del cluster. Si definiscono le autorizzazioni a livello di cluster o a spazi dei nomi specifici. Determinare quali risorse possono essere gestite e con quali autorizzazioni. Questi ruoli vengono quindi applicati a utenti o gruppi con un'associazione. Per altre informazioni su *ruoli*, *ClusterRole* e *associazioni*, vedere [Opzioni di accesso e identità per il servizio Azure Kubernetes][aks-concepts-identity].

È ad esempio possibile creare un ruolo con accesso completo alle risorse nello spazio dei nomi denominato *Finance-app*, come illustrato nel seguente manifesto YAML di esempio:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role
  namespace: finance-app
rules:
- apiGroups: [""]
  resources: ["*"]
  verbs: ["*"]
```

Si crea quindi un oggetto roleName e si associa il Azure AD utente *developer1 \@ contoso.com* al Metodo Role, come illustrato nel manifesto YAML seguente:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role-binding
  namespace: finance-app
subjects:
- kind: User
  name: developer1@contoso.com
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: finance-app-full-access-role
  apiGroup: rbac.authorization.k8s.io
```

Quando *developer1 \@ contoso.com* viene autenticato nel cluster AKS, dispone delle autorizzazioni complete per le risorse nello spazio dei nomi *Finance-app* . In questo modo, l'accesso alle risorse viene separato e controllato logicamente. Usare Kubernetes RBAC insieme a Azure AD-Integration.

Per informazioni su come usare i gruppi di Azure AD per controllare l'accesso alle risorse di Kubernetes usando il controllo degli accessi in base al ruolo, vedere [controllare l'accesso alle risorse del cluster usando il controllo degli accessi in base al ruolo e Azure Active Directory identità][azure-ad-rbac]

## <a name="use-azure-rbac"></a>Usa RBAC di Azure 
> **Indicazioni sulle procedure consigliate** 
> 
> Usare il controllo degli accessi in base al ruolo di Azure per definire le autorizzazioni minime necessarie per utenti e gruppi per le risorse AKS in una o più

Per il funzionamento completo di un cluster AKS sono necessari due livelli di accesso: 
1. Accedere alla risorsa AKS nella sottoscrizione di Azure. 

    Questo livello di accesso consente di:
      * Controllare il ridimensionamento o l'aggiornamento del cluster tramite le API AKS
      * Estrarre il `kubeconfig` .

    Per informazioni su come controllare l'accesso alla risorsa AKS e `kubeconfig` , vedere [limitare l'accesso al file di configurazione del cluster](control-kubeconfig-access.md).

2. Accesso all'API Kubernetes. 
    
    Questo livello di accesso è controllato da:
    * [KUBERNETES RBAC](#use-kubernetes-role-based-access-control-kubernetes-rbac) (tradizionalmente) o 
    * Integrando Azure RBAC con AKS per l'autorizzazione kubernetes.

    Per informazioni su come concedere in modo granulare le autorizzazioni per l'API Kubernetes usando il controllo degli accessi in base al ruolo di Azure, vedere usare il controllo [degli](manage-azure-rbac.md)accessi

## <a name="use-pod-managed-identities"></a>Usare le identità gestite da Pod

> **Indicazioni sulle procedure consigliate** 
> 
> Non usare credenziali fisse all'interno di pod o immagini del contenitore, perché sono a rischio di esposizione o abusi. Usare invece le *identità Pod* per richiedere automaticamente l'accesso tramite una soluzione di identità Azure ad centrale. 

> [!NOTE]
> Le identità pod sono destinate all'uso solo con i pod Linux e le immagini contenitore. Il supporto per le identità gestite da Pod per i contenitori Windows sarà presto disponibile.

Per accedere ad altri servizi di Azure, ad esempio Cosmos DB, Key Vault o archiviazione BLOB, il Pod necessita di credenziali di accesso. È possibile definire le credenziali di accesso con l'immagine del contenitore o inserirle come segreto Kubernetes. In entrambi i casi, è necessario crearli e assegnarli manualmente. In genere, queste credenziali vengono riutilizzate tra i pod e non vengono ruotate regolarmente.

Con le identità gestite da Pod per le risorse di Azure, si richiede automaticamente l'accesso ai servizi tramite Azure AD. Le identità gestite da Pod sono ora attualmente disponibili in anteprima per AKS. Per informazioni introduttive, vedere la [usare Azure Active Directory identità gestite da Pod in Azure Kubernetes Service (anteprima) [( https://docs.microsoft.com/azure/aks/use-azure-ad-pod-identity) documentazione. 

Anziché definire manualmente le credenziali per i pod, le identità gestite da Pod richiedono un token di accesso in tempo reale, usandolo per accedere solo ai servizi assegnati. In AKS sono disponibili due componenti che gestiscono le operazioni per consentire ai pod di usare le identità gestite:

* Il **server NMI (Node Management Identity)** è un pod che viene eseguito come DaemonSet su ogni nodo nel cluster servizio Azure Kubernetes. Il server NMI ascolta le richieste del pod ai servizi di Azure.
* **Il provider di risorse di Azure** esegue una query sul server API Kubernetes e verifica la presenza di un mapping di identità di Azure corrispondente a un pod.

Quando i pod richiedono l'accesso a un servizio di Azure, le regole di rete reindirizzano il traffico al server NMI. 
1. Server NMI:
    * Identifica i pod che richiedono l'accesso ai servizi di Azure in base al relativo indirizzo remoto.
    * Esegue una query sul provider di risorse di Azure. 
1. Il provider di risorse di Azure verifica i mapping delle identità di Azure nel cluster AKS.
1. Il server NMI richiede un token di accesso da Azure AD in base al mapping di identità del Pod. 
1. Azure AD fornisce l'accesso al server NMI, che viene restituito al pod. 
    * Questo token di accesso può essere quindi usato dal pod per richiedere l'accesso ai servizi di Azure.

Nell'esempio seguente uno sviluppatore crea un pod che usa un'identità gestita per richiedere l'accesso al database SQL di Azure:

![Le identità del pod consentono a un pod di richiedere automaticamente l'accesso ad altri servizi](media/operator-best-practices-identity/pod-identities.png)

1. Operatore cluster crea un account del servizio per eseguire il mapping delle identità quando i pod richiedono l'accesso ai servizi.
1. Il server NMI viene distribuito per inoltrare le richieste Pod, insieme al provider di risorse di Azure, per i token di accesso Azure AD.
1. Uno sviluppatore distribuisce un pod con un'identità gestita che richiede un token di accesso tramite il server NMI.
1. Il token viene restituito al Pod e usato per accedere al database SQL di Azure

> [!NOTE]
> Le identità gestite da Pod sono attualmente in stato di anteprima.

Per usare le identità gestite da Pod, vedere [usare Azure Active Directory identità gestite da Pod nel servizio Azure Kubernetes (anteprima)]( https://docs.microsoft.com/azure/aks/use-azure-ad-pod-identity).

## <a name="next-steps"></a>Passaggi successivi

Questo articolo sulle procedure consigliate ha illustrato l'autenticazione e l'autorizzazione per il cluster e le risorse. Per implementare alcune di queste procedure consigliate, vedere gli articoli seguenti:

* [Integrare Azure Active Directory con servizio Azure Kubernetes][aks-aad]
* [Usare Azure Active Directory identità gestite da Pod nel servizio Azure Kubernetes (anteprima)]( https://docs.microsoft.com/azure/aks/use-azure-ad-pod-identity)

Per altre informazioni sulle operazioni cluster in servizio Azure Kubernetes, vedere le procedure consigliate seguenti:

* [Isolamento cluster e multi-tenant][aks-best-practices-cluster-isolation]
* [Funzionalità di base dell'utilità di pianificazione di Kubernetes][aks-best-practices-scheduler]
* [Funzionalità avanzate dell'utilità di pianificazione di Kubernetes][aks-best-practices-advanced-scheduler]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-aad]: azure-ad-integration-cli.md
[managed-identities:]: ../active-directory/managed-identities-azure-resources/overview.md
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[azure-ad-rbac]: azure-ad-rbac.md
