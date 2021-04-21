---
title: Usare Azure AD in servizio Azure Kubernetes
description: Informazioni su come usare i Azure AD in servizio Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 02/1/2021
ms.author: miwithro
ms.openlocfilehash: 3db9f8d895b4c13b5f969859f422e7b566722ffc
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783072"
---
# <a name="aks-managed-azure-active-directory-integration"></a>Integrazione dei servizi gestiti dal servizio Azure Active Directory Servizio Azure Active Directory

L'integrazione Azure AD gestita dal servizio Azure AD è progettata per semplificare l'esperienza di integrazione di Azure AD, in cui gli utenti dovevano in precedenza creare un'app client e un'app server e richiedevano al tenant di Azure AD di concedere le autorizzazioni di lettura nella directory. Nella nuova versione, il provider di risorse del servizio Web AKS gestisce le app client e server per l'utente.

## <a name="azure-ad-authentication-overview"></a>Azure AD panoramica dell'autenticazione

Gli amministratori del cluster possono configurare il controllo degli accessi in base al ruolo di Kubernetes in base all'identità di un utente o all'appartenenza a gruppi di directory. L'autenticazione di Azure AD è disponibile per i cluster di servizio Azure Kubernetes con OpenID Connect. OpenID Connect è un livello di gestione delle identità basato sul protocollo OAuth 2.0. Per altre informazioni sui OpenID Connect, vedere la [documentazione di Open ID Connect.][open-id-connect]

Per altre informazioni sul flusso Azure AD integrazione dei dati, vedere la [documentazione Azure Active Directory concetti relativi all'integrazione.](concepts-identity.md#azure-ad-integration)

## <a name="limitations"></a>Limitazioni 

* Non è possibile Azure AD'integrazione di servizi gestiti dal servizio Azure AD servizio Web
* La modifica di un cluster integrato Azure AD servizio Azure AD AKS in AAD legacy non è supportata
* I cluster non abilitati per il controllo degli accessi in base al ruolo di Kubernetes non sono supportati per l'integrazione Azure AD servizio Kubernetes
* La modifica Azure AD tenant associato all'integrazione Azure AD gestita dal servizio Azure AD non è supportata

## <a name="prerequisites"></a>Prerequisiti

* Interfaccia della riga di comando di Azure versione 2.11.0 o successiva
* Kubectl con versione minima [1.18.1](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#v1181) o [kubelogin](https://github.com/Azure/kubelogin)
* Se si usa [Helm](https://github.com/helm/helm), versione minima di Helm 3.3.

> [!Important]
> È necessario usare Kubectl con una versione minima 1.18.1 o kubelogin. La differenza tra le versioni secondarie di Kubernetes e kubectl non deve essere maggiore di 1 versione. Se non si usa la versione corretta, si noteranno problemi di autenticazione.

Per installare kubectl e kubelogin, usare i comandi seguenti:

```azurecli-interactive
sudo az aks install-cli
kubectl version --client
kubelogin --version
```

Usare [queste istruzioni](https://kubernetes.io/docs/tasks/tools/install-kubectl/) per altri sistemi operativi.

## <a name="before-you-begin"></a>Prima di iniziare

Per il cluster è necessario un gruppo Azure AD locale. Questo gruppo è necessario come gruppo amministratore per consentire al cluster di concedere le autorizzazioni di amministratore del cluster. È possibile usare un gruppo Azure AD esistente o crearne uno nuovo. Registrare l'ID oggetto del Azure AD gruppo.

```azurecli-interactive
# List existing groups in the directory
az ad group list --filter "displayname eq '<group-name>'" -o table
```

Per creare un nuovo gruppo Azure AD per gli amministratori del cluster, usare il comando seguente:

```azurecli-interactive
# Create an Azure AD group
az ad group create --display-name myAKSAdminGroup --mail-nickname myAKSAdminGroup
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Creare un cluster del servizio AKS con Azure AD abilitata

Creare un cluster del servizio Web Del servizio Gestione servizio Web usando i comandi dell'interfaccia della riga di comando seguenti.

Creare un gruppo di risorse di Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

Creare un cluster del servizio AKS e abilitare l'accesso di amministrazione per il Azure AD locale

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

La creazione di un cluster gestito dal servizio Azure AD del servizio Azure AD ha la sezione seguente nel corpo della risposta
```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Dopo aver creato il cluster, è possibile iniziare ad accedervi.

## <a name="access-an-azure-ad-enabled-cluster"></a>Accedere a un cluster Azure AD abilitato

Per eseguire la procedura [seguente è servizio Azure Kubernetes ruolo](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role) predefinito Utente cluster.

Ottenere le credenziali utente per accedere al cluster:
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```
Seguire le istruzioni per accedere.

Usare il comando kubectl get nodes per visualizzare i nodi nel cluster:

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```
Configurare [il controllo degli accessi in base al ruolo di Azure](./azure-ad-rbac.md) per configurare gruppi di sicurezza aggiuntivi per i cluster.

## <a name="troubleshooting-access-issues-with-azure-ad"></a>Risoluzione dei problemi di accesso con Azure AD

> [!Important]
> I passaggi descritti di seguito ignorano la normale autenticazione Azure AD gruppo. Usarli solo in caso di emergenza.

Se si è bloccati definitivamente non avendo accesso a un gruppo Azure AD valido con accesso al cluster, è comunque possibile ottenere le credenziali di amministratore per accedere direttamente al cluster.

Per eseguire questa procedura, è necessario avere accesso al ruolo predefinito [amministratore servizio Azure Kubernetes](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role) cluster.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster --admin
```

## <a name="enable-aks-managed-azure-ad-integration-on-your-existing-cluster"></a>Abilitare l'integrazione dei servizi gestiti Azure AD servizio Web nel cluster esistente

È possibile abilitare l'integrazione dei servizi gestiti dal servizio Azure AD nel cluster esistente abilitato per il controllo degli accessi in base al ruolo di Kubernetes. Assicurarsi di impostare il gruppo di amministratori per mantenere l'accesso al cluster.

```azurecli-interactive
az aks update -g MyResourceGroup -n MyManagedCluster --enable-aad --aad-admin-group-object-ids <id-1> [--aad-tenant-id <id>]
```

Una corretta attivazione di un cluster gestito dal servizio Azure AD servizio Azure AD ha la sezione seguente nel corpo della risposta

```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Scaricare di nuovo le credenziali utente per accedere al cluster seguendo la procedura [riportata qui.][access-cluster]

## <a name="upgrading-to-aks-managed-azure-ad-integration"></a>Aggiornamento all'integrazione dei servizi gestiti dal servizio Azure AD Servizio Azure AD Servizio Web

Se il cluster usa l'integrazione Azure AD legacy, è possibile eseguire l'aggiornamento all'integrazione Azure AD servizio Azure AD Servizio Web.

```azurecli-interactive
az aks update -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

Una migrazione corretta di un cluster gestito dal servizio Azure AD servizio Azure AD ha la sezione seguente nel corpo della risposta

```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Se si vuole accedere al cluster, seguire questa [procedura.][access-cluster]

## <a name="non-interactive-sign-in-with-kubelogin"></a>Accesso non interattivo con kubelogin

Esistono alcuni scenari non interattivi, ad esempio le pipeline di integrazione continua, che non sono attualmente disponibili con kubectl. È possibile usare [`kubelogin`](https://github.com/Azure/kubelogin) per accedere al cluster con l'accesso all'entità servizio non interattiva.

## <a name="use-conditional-access-with-azure-ad-and-aks"></a>Usare l'accesso condizionale con il Azure AD servizio Azure AD Servizio Web Dia

Quando si integrano Azure AD con il cluster del servizio Web Diaks, è anche possibile usare l'accesso [condizionale][aad-conditional-access] per controllare l'accesso al cluster.

> [!NOTE]
> Azure AD'accesso condizionale è Azure AD Premium funzionalità.

Per creare un criterio di accesso condizionale di esempio da usare con il servizio Servizio Web di gestione degli accessi, seguire questa procedura:

1. Nella parte superiore della portale di Azure cercare e selezionare Azure Active Directory.
1. Nel menu per Azure Active Directory sul lato sinistro selezionare *Applicazioni aziendali.*
1. Nel menu per le applicazioni aziendali sul lato sinistro selezionare *Accesso condizionale*.
1. Nel menu per Accesso condizionale sul lato sinistro selezionare *Criteri* e *quindi Nuovo criterio.*
    :::image type="content" source="./media/managed-aad/conditional-access-new-policy.png" alt-text="Aggiunta di criteri di accesso condizionale":::
1. Immettere un nome per i criteri, ad esempio *aks-policy*.
1. Selezionare *Utenti e gruppi* e quindi in *Includi* selezionare Seleziona utenti *e gruppi*. Scegliere gli utenti e i gruppi a cui applicare i criteri. Per questo esempio, scegliere lo stesso gruppo Azure AD con accesso amministrativo al cluster.
    :::image type="content" source="./media/managed-aad/conditional-access-users-groups.png" alt-text="Selezione di utenti o gruppi per applicare i criteri di accesso condizionale":::
1. Selezionare *App o azioni cloud,* quindi in *Includi* selezionare *Seleziona app.* Cercare *servizio Azure Kubernetes* e selezionare servizio Azure Kubernetes *AAD Server*.
    :::image type="content" source="./media/managed-aad/conditional-access-apps.png" alt-text="Selezione servizio Azure Kubernetes server AD per l'applicazione dei criteri di accesso condizionale":::
1. In *Controlli di accesso* selezionare *Concedi*. Selezionare *Concedi accesso* e *quindi Richiedi che il dispositivo sia contrassegnato come conforme.*
    :::image type="content" source="./media/managed-aad/conditional-access-grant-compliant.png" alt-text="Selezione per consentire solo i dispositivi conformi per i criteri di accesso condizionale":::
1. In *Enable policy*(Abilita criterio) selezionare On *(Attiva)* e quindi Create *(Crea).*
    :::image type="content" source="./media/managed-aad/conditional-access-enable-policy.png" alt-text="Abilitazione dei criteri di accesso condizionale":::

Ottenere le credenziali utente per accedere al cluster, ad esempio:

```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

Seguire le istruzioni per accedere.

Usare il `kubectl get nodes` comando per visualizzare i nodi nel cluster:

```azurecli-interactive
kubectl get nodes
```

Seguire le istruzioni per accedere di nuovo. Si noti che viene visualizzato un messaggio di errore che indica che l'accesso è stato eseguito correttamente, ma l'amministratore richiede che il dispositivo che richiede l'accesso sia gestito dal Azure AD per accedere alla risorsa.

Nel portale di Azure passare a Azure Active Directory, selezionare Applicazioni  aziendali e quindi in *Attività* selezionare *Sign-ins (Accedi).* Si noti una voce nella parte superiore con *lo stato* *Non* riuscito e l'accesso *condizionale* *riuscito*. Selezionare la voce e quindi selezionare *Accesso condizionale* in *Dettagli*. Si noti che i criteri di accesso condizionale sono elencati.

:::image type="content" source="./media/managed-aad/conditional-access-sign-in-activity.png" alt-text="Voce di accesso non riuscita a causa di criteri di accesso condizionale":::

## <a name="configure-just-in-time-cluster-access-with-azure-ad-and-aks"></a>Configurare l'accesso JUST-In-Time al cluster con il Azure AD servizio Azure AD Servizio Web di gestione degli accessi

Un'altra opzione per il controllo di accesso del cluster è usare Privileged Identity Management (PIM) per le richieste JUST-In-Time.

>[!NOTE]
> PIM è una Azure AD Premium che richiede uno SKU Premium P2. Per altre informazioni sugli SKU Azure AD, vedere la guida [ai prezzi.][aad-pricing]

Per integrare le richieste di accesso JUST-In-Time con un cluster del servizio Azure AD, seguire questa procedura:

1. Nella parte superiore della portale di Azure cercare e selezionare Azure Active Directory.
1. Prendere nota dell'ID tenant, indicato per il resto di queste istruzioni come In un Web browser, viene visualizzata la schermata portale di Azure per Azure Active Directory con `<tenant-id>` :::image type="content" source="./media/managed-aad/jit-get-tenant-id.png" alt-text="l'ID del tenant evidenziato.":::
1. Nel menu per Azure Active Directory sul lato sinistro, in *Gestisci* selezionare *Gruppi e* quindi Nuovo *gruppo.*
    :::image type="content" source="./media/managed-aad/jit-create-new-group.png" alt-text="Mostra la portale di Azure gruppi di Active Directory con l'opzione &quot;Nuovo gruppo&quot; evidenziata.":::
1. Assicurarsi che sia selezionato un tipo di *gruppo di* sicurezza e immettere un nome di gruppo, ad esempio *myJITGroup*. In *Azure AD i ruoli possono essere assegnati a questo gruppo (anteprima)*, selezionare *Sì.* Infine, selezionare *Crea*.
    :::image type="content" source="./media/managed-aad/jit-new-group-created.png" alt-text="Mostra la portale di Azure di creazione del nuovo gruppo del gruppo.":::
1. Si tornerà alla *pagina* Gruppi. Selezionare il gruppo appena creato e prendere nota dell'ID oggetto, a cui si fa riferimento per il resto di queste istruzioni come `<object-id>` .
    :::image type="content" source="./media/managed-aad/jit-get-object-id.png" alt-text="Mostra la portale di Azure per il gruppo appena creato, evidenziando l'ID oggetto":::
1. Distribuire un cluster del servizio AKS con l'integrazione Azure AD servizio AKS usando i `<tenant-id>` valori e delle versioni `<object-id>` precedenti:
    ```azurecli-interactive
    az aks create -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <object-id> --aad-tenant-id <tenant-id>
    ```
1. Di nuovo nel portale di Azure, nel menu  attività sul lato sinistro selezionare Accesso con privilegi *(anteprima)* e selezionare *Abilita accesso con privilegi.*
    :::image type="content" source="./media/managed-aad/jit-enabling-priv-access.png" alt-text="Viene portale di Azure visualizzata la pagina Accesso con privilegi (anteprima) con l'opzione &quot;Abilita accesso con privilegi&quot; evidenziata":::
1. Selezionare *Aggiungi assegnazioni per* iniziare a concedere l'accesso.
    :::image type="content" source="./media/managed-aad/jit-add-active-assignment.png" alt-text="Viene portale di Azure la schermata Accesso con privilegi (anteprima) dopo l'abilitazione. L'opzione &quot;Aggiungi assegnazioni&quot; è evidenziata.":::
1. Selezionare un ruolo membro *e* selezionare gli utenti e i gruppi a cui si vuole concedere l'accesso al cluster. Queste assegnazioni possono essere modificate in qualsiasi momento da un amministratore di gruppo. Quando si è pronti per andare avanti, selezionare *Avanti.*
    :::image type="content" source="./media/managed-aad/jit-adding-assignment.png" alt-text="Viene portale di Azure la schermata Aggiungi assegnazioni Appartenenza, con un utente di esempio selezionato per l'aggiunta come membro. L'opzione &quot;Avanti&quot; è evidenziata.":::
1. Scegliere un tipo di assegnazione *Attivo*, la durata desiderata e fornire una giustificazione. Quando si è pronti per continuare, selezionare *Assegna*. Per altre informazioni sui tipi di assegnazione, vedere Assegnare l'idoneità per un gruppo di accesso con [privilegi (anteprima) in Privileged Identity Management][aad-assignments].
    :::image type="content" source="./media/managed-aad/jit-set-active-assignment-details.png" alt-text="Viene portale di Azure la schermata Aggiungi impostazione assegnazioni. È stato selezionato un tipo di assegnazione &quot;Attivo&quot; ed è stata specificata una giustificazione di esempio. L'opzione &quot;Assegna&quot; è evidenziata.":::

Dopo aver effettuato le assegnazioni, verificare che l'accesso just-in-time funzioni accedendo al cluster. Ad esempio:

```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

Seguire la procedura per accedere.

Usare il `kubectl get nodes` comando per visualizzare i nodi nel cluster:

```azurecli-interactive
kubectl get nodes
```

Prendere nota del requisito di autenticazione e seguire la procedura per l'autenticazione. Se l'operazione riesce, verrà visualizzato un output simile al seguente:

```output
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.
NAME                                STATUS   ROLES   AGE     VERSION
aks-nodepool1-61156405-vmss000000   Ready    agent   6m36s   v1.18.14
aks-nodepool1-61156405-vmss000001   Ready    agent   6m42s   v1.18.14
aks-nodepool1-61156405-vmss000002   Ready    agent   6m33s   v1.18.14
```

### <a name="troubleshooting"></a>Risoluzione dei problemi

Se `kubectl get nodes` restituisce un errore simile al seguente:

```output
Error from server (Forbidden): nodes is forbidden: User "aaaa11111-11aa-aa11-a1a1-111111aaaaa" cannot list resource "nodes" in API group "" at the cluster scope
```

Assicurarsi che l'amministratore del gruppo di sicurezza abbia assegnato all'account *un'assegnazione* Attiva.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni [sull'integrazione del controllo degli accessi in base al ruolo di Azure per l'autorizzazione kubernetes][azure-rbac-integration]
* Informazioni sull'integrazione Azure AD controllo degli accessi in base al ruolo [di Kubernetes.][azure-ad-rbac]
* Usare [kubelogin per](https://github.com/Azure/kubelogin) accedere alle funzionalità per l'autenticazione di Azure che non sono disponibili in kubectl.
* Altre informazioni sui concetti [relativi all'identità del servizio Kubernetes e del servizio Kubernetes.][aks-concepts-identity]
* Usare [i Azure Resource Manager (ARM) ][aks-arm-template] per creare cluster abilitati per l'Azure AD servizio Azure AD servizio Web.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters
[aad-pricing]: https://azure.microsoft.com/pricing/details/active-directory/

<!-- LINKS - Internal -->
[aad-conditional-access]: ../active-directory/conditional-access/overview.md
[azure-rbac-integration]: manage-azure-rbac.md
[aks-concepts-identity]: concepts-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-group-create]: /cli/azure/group#az_group_create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az_ad_user_show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
[access-cluster]: #access-an-azure-ad-enabled-cluster
[aad-migrate]: #upgrading-to-aks-managed-azure-ad-integration
[aad-assignments]: ../active-directory/privileged-identity-management/groups-assign-member-owner.md#assign-an-owner-or-member-of-a-group
