---
title: Concetti - Accesso e identità nei servizi Azure Kubernetes
description: Informazioni su accesso e identità in Azure Kubernetes Service (AKS), tra cui l'integrazione Azure Active Directory, il controllo degli accessi in base al ruolo Kubernetes (Kubernetes RBAC) e i ruoli e le associazioni.
services: container-service
ms.topic: conceptual
ms.date: 03/24/2021
author: palma21
ms.author: jpalma
ms.openlocfilehash: 76871565e0bb4ca1811d46531d07b89181d07e19
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105919"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Opzioni di accesso e identità per il servizio Azure Kubernetes

È possibile autenticare, autorizzare, proteggere e controllare l'accesso ai cluster Kubernetes in diversi modi. 
* Usando il controllo degli accessi in base al ruolo Kubernetes (Kubernetes RBAC), è possibile concedere a utenti, gruppi e account del servizio l'accesso solo alle risorse necessarie. 
* Azure Kubernetes Service (AKS) consente di migliorare ulteriormente la struttura di sicurezza e autorizzazioni tramite Azure Active Directory e il controllo degli accessi in base al ruolo di Azure. 

Kubernetes RBAC e AKS consentono di proteggere l'accesso al cluster e forniscono solo le autorizzazioni minime necessarie per gli sviluppatori e gli operatori.

Questo articolo presenta i concetti di base che consentono di autenticare e assegnare le autorizzazioni in AKS.

## <a name="aks-service-permissions"></a>Autorizzazioni del servizio AKS

Quando si crea un cluster, AKS genera o modifica le risorse necessarie, ad esempio macchine virtuali e NIC, per creare ed eseguire il cluster per conto dell'utente. Questa identità è diversa dall'autorizzazione di identità del cluster, creata durante la creazione del cluster.

### <a name="identity-creating-and-operating-the-cluster-permissions"></a>Identità creazione e gestione delle autorizzazioni del cluster

Le autorizzazioni seguenti sono necessarie per l'identità che crea e gestisce il cluster.

| Autorizzazione | Motivo |
|---|---|
| `Microsoft.Compute/diskEncryptionSets/read` | Obbligatorio per leggere l'ID del set di crittografia del disco. |
| `Microsoft.Compute/proximityPlacementGroups/write` | Obbligatorio per l'aggiornamento dei gruppi di posizionamento di prossimità. |
| `Microsoft.Network/applicationGateways/read` <br/> `Microsoft.Network/applicationGateways/write` <br/> `Microsoft.Network/virtualNetworks/subnets/join/action` | Necessario per configurare i gateway applicazione e aggiungere la subnet. |
| `Microsoft.Network/virtualNetworks/subnets/join/action` | Necessario per configurare il gruppo di sicurezza di rete per la subnet quando si usa un VNET personalizzato.|
| `Microsoft.Network/publicIPAddresses/join/action` <br/> `Microsoft.Network/publicIPPrefixes/join/action` | Necessario per configurare gli indirizzi IP pubblici in uscita nella Load Balancer Standard. |
| `Microsoft.OperationalInsights/workspaces/sharedkeys/read` <br/> `Microsoft.OperationalInsights/workspaces/read` <br/> `Microsoft.OperationsManagement/solutions/write` <br/> `Microsoft.OperationsManagement/solutions/read` <br/> `Microsoft.ManagedIdentity/userAssignedIdentities/assign/action` | Necessaria per creare e aggiornare le aree di lavoro Log Analytics e il monitoraggio di Azure per i contenitori. |

### <a name="aks-cluster-identity-permissions"></a>Autorizzazioni di identità del cluster AKS

Le autorizzazioni seguenti vengono usate dall'identità del cluster AKS, che viene creata e associata al cluster AKS. Ogni autorizzazione viene usata per i motivi seguenti:

| Autorizzazione | Motivo |
|---|---|
| `Microsoft.ContainerService/managedClusters/*`  <br/> | Obbligatorio per la creazione di utenti e il funzionamento del cluster
| `Microsoft.Network/loadBalancers/delete` <br/> `Microsoft.Network/loadBalancers/read` <br/> `Microsoft.Network/loadBalancers/write` | Necessario per configurare il servizio di bilanciamento del carico per un servizio LoadBalancer. |
| `Microsoft.Network/publicIPAddresses/delete` <br/> `Microsoft.Network/publicIPAddresses/read` <br/> `Microsoft.Network/publicIPAddresses/write` | Necessario per trovare e configurare indirizzi IP pubblici per un servizio LoadBalancer. |
| `Microsoft.Network/publicIPAddresses/join/action` | Obbligatorio per la configurazione di indirizzi IP pubblici per un servizio LoadBalancer. |
| `Microsoft.Network/networkSecurityGroups/read` <br/> `Microsoft.Network/networkSecurityGroups/write` | Obbligatorio per creare o eliminare regole di sicurezza per un servizio LoadBalancer. |
| `Microsoft.Compute/disks/delete` <br/> `Microsoft.Compute/disks/read` <br/> `Microsoft.Compute/disks/write` <br/> `Microsoft.Compute/locations/DiskOperations/read` | Obbligatorio per configurare AzureDisks. |
| `Microsoft.Storage/storageAccounts/delete` <br/> `Microsoft.Storage/storageAccounts/listKeys/action` <br/> `Microsoft.Storage/storageAccounts/read` <br/> `Microsoft.Storage/storageAccounts/write` <br/> `Microsoft.Storage/operations/read` | Obbligatorio per configurare gli account di archiviazione per AzureFile o AzureDisk. |
| `Microsoft.Network/routeTables/read` <br/> `Microsoft.Network/routeTables/routes/delete` <br/> `Microsoft.Network/routeTables/routes/read` <br/> `Microsoft.Network/routeTables/routes/write` <br/> `Microsoft.Network/routeTables/write` | Obbligatorio per configurare le route e le tabelle di route per i nodi. |
| `Microsoft.Compute/virtualMachines/read` | Obbligatorio per trovare informazioni per le macchine virtuali in una VMAS, ad esempio zone, dominio di errore, dimensioni e dischi dati. |
| `Microsoft.Compute/virtualMachines/write` | Obbligatorio per alleghire AzureDisks a una macchina virtuale in un VMAS. |
| `Microsoft.Compute/virtualMachineScaleSets/read` <br/> `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read` <br/> `Microsoft.Compute/virtualMachineScaleSets/virtualmachines/instanceView/read` | Obbligatorio per trovare informazioni per le macchine virtuali in un set di scalabilità di macchine virtuali, ad esempio zone, dominio di errore, dimensioni e dischi dati. |
| `Microsoft.Network/networkInterfaces/write` | Obbligatorio per aggiungere una macchina virtuale in una VMAS a un pool di indirizzi back-end del servizio di bilanciamento del carico. |
| `Microsoft.Compute/virtualMachineScaleSets/write` | Richiesto per aggiungere un set di scalabilità di macchine virtuali a un pool di indirizzi back-end del servizio di bilanciamento del carico e a nodi di scalabilità orizzontale in un set di scalabilità |
| `Microsoft.Compute/virtualMachineScaleSets/virtualmachines/write` | Obbligatorio per alleghire AzureDisks e aggiungere una macchina virtuale da un set di scalabilità di macchine virtuali al servizio di bilanciamento del carico. |
| `Microsoft.Network/networkInterfaces/read` | Obbligatorio per la ricerca di indirizzi IP interni e pool di indirizzi back-end di bilanciamento del carico per le macchine virtuali in una VMAS. |
| `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read` | Obbligatorio per la ricerca di indirizzi IP interni e pool di indirizzi back-end di bilanciamento del carico per una macchina virtuale in un set di scalabilità di macchine virtuali. |
| `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipconfigurations/publicipaddresses/read` | Necessaria per trovare gli indirizzi IP pubblici per una macchina virtuale in un set di scalabilità di macchine virtuali. |
| `Microsoft.Network/virtualNetworks/read` <br/> `Microsoft.Network/virtualNetworks/subnets/read` | Necessaria per verificare se esiste una subnet per il servizio di bilanciamento del carico interno in un altro gruppo di risorse. |
| `Microsoft.Compute/snapshots/delete` <br/> `Microsoft.Compute/snapshots/read` <br/> `Microsoft.Compute/snapshots/write` | Obbligatorio per configurare gli snapshot per AzureDisk. |
| `Microsoft.Compute/locations/vmSizes/read` <br/> `Microsoft.Compute/locations/operations/read` | Necessaria per trovare le dimensioni delle macchine virtuali per trovare i limiti del volume AzureDisk. |

### <a name="additional-cluster-identity-permissions"></a>Autorizzazioni di identità del cluster aggiuntive

Quando si crea un cluster con attributi specifici, sono necessarie le autorizzazioni aggiuntive seguenti per l'identità del cluster. Poiché queste autorizzazioni non vengono assegnate automaticamente, è necessario aggiungerle all'identità del cluster dopo la creazione.

| Autorizzazione | Motivo |
|---|---|
| `Microsoft.Network/networkSecurityGroups/write` <br/> `Microsoft.Network/networkSecurityGroups/read` | Obbligatorio se si usa un gruppo di sicurezza di rete in un altro gruppo di risorse. Obbligatorio per configurare le regole di sicurezza per un servizio LoadBalancer. |
| `Microsoft.Network/virtualNetworks/subnets/read` <br/> `Microsoft.Network/virtualNetworks/subnets/join/action` | Obbligatorio se si usa una subnet in un altro gruppo di risorse, ad esempio un VNET personalizzato. |
| `Microsoft.Network/routeTables/routes/read` <br/> `Microsoft.Network/routeTables/routes/write` | Obbligatorio se si usa una subnet associata a una tabella di route in un altro gruppo di risorse, ad esempio un VNET personalizzato con una tabella di route personalizzata. Necessaria per verificare se esiste già una subnet per la subnet nell'altro gruppo di risorse. |
| `Microsoft.Network/virtualNetworks/subnets/read` | Obbligatorio se si usa un servizio di bilanciamento del carico interno in un altro gruppo di risorse. Necessaria per verificare se una subnet esiste già per il servizio di bilanciamento del carico interno nel gruppo di risorse. |
| `Microsoft.Network/privatednszones/*` | Obbligatorio se si usa una zona DNS privata in un altro gruppo di risorse, ad esempio un privateDNSZone personalizzato. |

## <a name="kubernetes-rbac"></a>Controllo degli accessi in base al ruolo di Kubernetes

Il controllo degli accessi in base al ruolo Kubernetes fornisce filtri granulari delle azioni Con questo meccanismo di controllo:
* È possibile assegnare utenti o gruppi di utenti per creare e modificare le risorse o visualizzare i log dai carichi di lavoro delle applicazioni in esecuzione. 
* È possibile definire l'ambito delle autorizzazioni per un singolo spazio dei nomi o nell'intero cluster AKS. 
* È possibile creare *ruoli* per definire le autorizzazioni e quindi assegnare tali ruoli agli utenti con *associazioni di ruolo*.

Per ulteriori informazioni, vedere [utilizzo dell'autorizzazione RBAC Kubernetes][kubernetes-rbac].

### <a name="roles-and-clusterroles"></a>Role e ClusterRole

#### <a name="roles"></a>Ruoli
Prima di assegnare le autorizzazioni agli utenti con il controllo degli accessi in base al ruolo Kubernetes, verranno definite le autorizzazioni utente come *ruolo*. Concedere le autorizzazioni all'interno di uno spazio dei nomi utilizzando i ruoli. 

> [!NOTE]
> I ruoli Kubernetes *concedono* le autorizzazioni. non *negano* le autorizzazioni.

Per concedere le autorizzazioni per l'intero cluster o per le risorse del cluster al di fuori di uno spazio dei nomi specifico, è possibile usare *ClusterRoles*.

#### <a name="clusterroles"></a>ClusterRoles

Un ClusterRole concede e applica le autorizzazioni alle risorse nell'intero cluster, non in uno spazio dei nomi specifico.

### <a name="rolebindings-and-clusterrolebindings"></a>RoleBinding e ClusterRoleBinding

Una volta definiti i ruoli per concedere le autorizzazioni alle risorse, si assegnano le autorizzazioni RBAC Kubernetes con un oggetto *Role*. Se il cluster AKS si [integra con Azure Active Directory (Azure ad)](#azure-ad-integration), RoleBindings concede le autorizzazioni per Azure ad utenti per eseguire azioni all'interno del cluster. Vedere come [controllare l'accesso alle risorse del cluster usando il controllo degli accessi in base al ruolo Kubernetes e le identità del Azure Active Directory](azure-ad-rbac.md).

#### <a name="rolebindings"></a>RoleBindings

Assegnare i ruoli agli utenti per uno spazio dei nomi specifico usando RoleBindings. Con RoleBindings, è possibile separare logicamente un singolo cluster AKS, consentendo agli utenti di accedere alle risorse dell'applicazione solo nello spazio dei nomi assegnato. 

Per associare i ruoli nell'intero cluster o per eseguire il clustering di risorse esterne a un determinato spazio dei nomi, è invece necessario usare *ClusterRoleBindings*.

#### <a name="clusterrolebinding"></a>ClusterRoleBinding

Con un ClusterRoleBinding, è possibile associare i ruoli agli utenti e applicarli alle risorse nell'intero cluster, non in uno spazio dei nomi specifico. Questo approccio consente di concedere agli amministratori o ai tecnici del supporto l'accesso a tutte le risorse nel cluster del servizio Azure Kubernetes.


> [!NOTE]
> Microsoft/AKS esegue le azioni del cluster con il consenso dell'utente in un ruolo predefinito di Kubernetes `aks-service` e nell'associazione di ruoli incorporata `aks-service-rolebinding` . 
> 
> Questo ruolo consente ad AKS di risolvere e diagnosticare i problemi del cluster, ma non di modificare le autorizzazioni né di creare ruoli o associazioni di ruolo o altre azioni con privilegi elevati. L'accesso ai ruoli viene abilitato solo in ticket di supporto attivi con accesso just-in-time (JIT). Altre informazioni sui [criteri di supporto di AKS](support-policies.md).


### <a name="kubernetes-service-accounts"></a>Account del servizio Kubernetes

Gli *account del servizio* sono uno dei tipi di utente principali in Kubernetes. L'API di Kubernetes include e gestisce gli account del servizio. Le credenziali dell'account del servizio vengono archiviate come segreti Kubernetes, consentendo l'uso da parte di Pod autorizzati per la comunicazione con il server API. La maggior parte delle richieste dell'API fornisce un token di autenticazione per un account del servizio o un account utente normale.

Gli account utente normali consentono un accesso più tradizionale per amministratori o sviluppatori umani, non solo per servizi e processi. Sebbene Kubernetes non fornisca una soluzione di gestione delle identità per archiviare gli account utente e le password normali, è possibile integrare le soluzioni di identità esterne in Kubernetes. Per i cluster AKS, questa soluzione di identità integrata è Azure AD.

Per altre informazioni sulle opzioni di gestione delle identità in Kubernetes, vedere [Kubernetes authentication][kubernetes-authentication] (Autenticazione di Kubernetes).

## <a name="azure-ad-integration"></a>Integrazione di Azure AD

Migliorare la sicurezza del cluster AKS con Azure AD l'integrazione. Basato su decenni di gestione delle identità aziendali, Azure AD è un servizio multi-tenant basato sul cloud e di gestione delle identità che combina i servizi directory principali, la gestione dell'accesso alle applicazioni e la protezione delle identità. Con Azure AD, è possibile integrare le identità locali nei cluster del servizio Azure Kubernetes per offrire un'unica origine per la sicurezza e la gestione degli account.

![Integrazione di Azure Active Directory con i cluster del servizio Azure Kubernetes](media/concepts-identity/aad-integration.png)

Con i cluster del servizio Azure Kubernetes integrati in AD Azure, è possibile concedere agli utenti o ai gruppi l'accesso alle risorse di Kubernetes all'interno di uno spazio dei nomi o nel cluster. 

1. Per ottenere un `kubectl` contesto di configurazione, un utente esegue il comando [AZ AKS Get-credentials][az-aks-get-credentials] . 
1. Quando un utente interagisce con il cluster AKS con `kubectl` , viene richiesto di eseguire l'accesso con le credenziali Azure ad. 

Questo approccio fornisce un'unica origine per la gestione degli account utente e le credenziali della password. L'utente può accedere solo risorse definite dall'amministratore del cluster.

L'autenticazione di Azure AD è disponibile per i cluster di servizio Azure Kubernetes con OpenID Connect. OpenID Connect è un livello di gestione delle identità basato sul protocollo OAuth 2.0. Per ulteriori informazioni su OpenID Connect, vedere la [documentazione relativa a Open ID Connect][openid-connect]. Dall'interno del cluster Kubernetes, [l'autenticazione del token webhook][webhook-token-docs] viene usata per verificare i token di autenticazione. L'autenticazione del token del webhook viene configurata e gestita come parte del cluster servizio Azure Kubernetes.

### <a name="webhook-and-api-server"></a>Webhook e Server API

![Flusso di autenticazione del webhook e del server API](media/concepts-identity/auth-flow.png)

Come illustrato nell'immagine precedente, il server API chiama il server del webhook AKS ed esegue i passaggi seguenti:

1. `kubectl` Usa l'applicazione client Azure AD per accedere agli utenti con il [flusso di concessione dell'autorizzazione del dispositivo OAuth 2,0](../active-directory/develop/v2-oauth2-device-code.md).
2. Azure AD fornisce un access_token, id_token e un refresh_token.
3. L'utente effettua una richiesta a `kubectl` con un access_token da `kubeconfig` .
4. `kubectl` Invia il access_token al server API.
5. Il server API viene configurato con il server webhook di autenticazione per eseguire la convalida.
6. Il server del webhook di autenticazione conferma che la firma del token Web JSON è valida controllando la chiave di firma pubblica Azure AD.
7. L'applicazione server utilizza le credenziali fornite dall'utente per eseguire query sulle appartenenze ai gruppi dell'utente che ha eseguito l'accesso da MS API Graph.
8. Viene inviata una risposta al server API con le informazioni sull'utente, ad esempio l'attestazione del nome dell'entità utente (UPN) del token di accesso e l'appartenenza al gruppo dell'utente in base all'ID oggetto.
9. L'API esegue una decisione di autorizzazione basata sul ruolo/ruolo di Kubernetes.
10. Una volta autorizzato, il server API restituisce una risposta a `kubectl` .
11. `kubectl` fornisce commenti e suggerimenti all'utente.
 
Informazioni su come integrare AKS con Azure AD con la [Guida alle procedure di integrazione Azure ad gestita da AKS](managed-aad.md).

## <a name="azure-role-based-access-control"></a>Controllo degli accessi in base al ruolo di Azure

Il controllo degli accessi in base al ruolo (RBAC) di Azure è un sistema di autorizzazione basato su [Azure Resource Manager](../azure-resource-manager/management/overview.md) che offre una gestione degli accessi con granularità fine delle risorse di Azure.

| Sistema RBAC | Descrizione |
|---|---|
| Controllo degli accessi in base al ruolo di Kubernetes | Progettato per funzionare con risorse Kubernetes all'interno del cluster AKS. |
| Controllo degli accessi in base al ruolo di Azure | Progettato per lavorare sulle risorse all'interno della sottoscrizione di Azure. |

Con il controllo degli accessi in base al ruolo di Azure si crea una *definizione del ruolo* che determina le autorizzazioni da applicare. Si assegna quindi a un utente o a un gruppo questa definizione di ruolo tramite un' *assegnazione di ruolo* per un *ambito* specifico. L'ambito può essere una singola risorsa, un gruppo di risorse o tutta la sottoscrizione.

Per altre informazioni, vedere informazioni [sul controllo degli accessi in base al ruolo di Azure (RBAC di Azure).][azure-rbac]

Per il funzionamento completo di un cluster AKS sono necessari due livelli di accesso: 
* [Accedere alla risorsa AKS nella sottoscrizione di Azure](#azure-rbac-to-authorize-access-to-the-aks-resource). 
  * Controllare il ridimensionamento o l'aggiornamento del cluster usando le API AKS.
  * Estrarre il `kubeconfig` .
* Accesso all'API Kubernetes. Questo accesso è controllato da uno dei seguenti:
  * [KUBERNETES RBAC](#kubernetes-rbac) (tradizionalmente).
  * [Integrazione di Azure RBAC con AKS per l'autorizzazione Kubernetes](#azure-rbac-for-kubernetes-authorization-preview).

### <a name="azure-rbac-to-authorize-access-to-the-aks-resource"></a>Controllo RBAC di Azure per autorizzare l'accesso alla risorsa AKS

Con il controllo degli accessi in base al ruolo di Azure, è possibile fornire agli utenti (o identità) un accesso granulare alle risorse AKS in una o più sottoscrizioni. Ad esempio, è possibile usare il [ruolo Collaboratore servizio di Azure Kubernetes](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-contributor-role) per ridimensionare e aggiornare il cluster. Nel frattempo, un altro utente con il [ruolo di amministratore del cluster di servizi Kubernetes di Azure](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role) dispone solo dell'autorizzazione per eseguire il pull dell'amministratore `kubeconfig` .

In alternativa, è possibile assegnare all'utente il ruolo di [collaboratore](../role-based-access-control/built-in-roles.md#contributor) generale. Con il ruolo Collaboratore generale, gli utenti possono eseguire le autorizzazioni sopra indicate e ogni azione possibile sulla risorsa AKS, tranne la gestione delle autorizzazioni.

[Usare il controllo degli accessi in base al ruolo per definire l'accesso al file di configurazione Kubernetes in AKS](control-kubeconfig-access.md).

### <a name="azure-rbac-for-kubernetes-authorization-preview"></a>Controllo RBAC di Azure per l'autorizzazione Kubernetes (anteprima)

Con l'integrazione con controllo degli accessi in base al ruolo di Azure, AKS userà un server webhook di autorizzazione Kubernetes per gestire le autorizzazioni e le assegnazioni delle risorse cluster Kubernetes integrate Azure AD usando la definizione di ruolo e le assegnazioni di ruolo di Azure

![Controllo degli accessi in base al ruolo di Azure per Kubernetes](media/concepts-identity/azure-rbac-k8s-authz-flow.png)

Come illustrato nel diagramma precedente, quando si usa l'integrazione con il controllo degli accessi in base al ruolo di Azure, tutte le richieste all'API Kubernetes seguiranno lo stesso flusso di autenticazione illustrato nella sezione relativa all' [integrazione Azure Active Directory](#azure-ad-integration). 

Se l'identità che sta effettuando la richiesta è presente in Azure AD, Azure creerà il team con Kubernetes RBAC per autorizzare la richiesta. Se l'identità esiste al di fuori del Azure AD (ad esempio, un account del servizio Kubernetes), l'autorizzazione non riuscirà a eseguire il controllo RBAC Kubernetes normale.

In questo scenario vengono usati i meccanismi e le API RBAC di Azure per assegnare gli utenti ai ruoli predefiniti o creare ruoli personalizzati, come si farebbe con i ruoli Kubernetes. 

Questa funzionalità consente non solo di concedere agli utenti le autorizzazioni per la risorsa AKS tra le sottoscrizioni, ma anche di configurare il ruolo e le autorizzazioni per ogni cluster che controlla l'accesso all'API Kubernetes. È ad esempio possibile concedere il `Azure Kubernetes Service RBAC Viewer` ruolo nell'ambito della sottoscrizione. Il destinatario del ruolo sarà in grado di elencare e ottenere tutti gli oggetti Kubernetes da tutti i cluster senza modificarli.

> [!IMPORTANT]
> Prima di usare questa funzionalità, è necessario abilitare il controllo degli accessi in base al ruolo di Azure. Per informazioni dettagliate e istruzioni dettagliate, seguire la guida procedure per l'uso di controllo degli accessi in base al ruolo [di Azure per l'autorizzazione Kubernetes](manage-azure-rbac.md) .

#### <a name="built-in-roles"></a>Ruoli predefiniti

AKS fornisce i quattro ruoli predefiniti seguenti. Sono simili ai [ruoli predefiniti di Kubernetes](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#user-facing-roles) con alcune differenze, ad esempio il supporto di CRD. Vedere l'elenco completo delle azioni consentite da ogni [ruolo predefinito di Azure](../role-based-access-control/built-in-roles.md).

| Ruolo                                | Descrizione  |
|-------------------------------------|--------------|
| Visualizzatore RBAC del servizio Kubernetes di Azure  | Consente l'accesso in sola lettura per visualizzare la maggior parte degli oggetti in uno spazio dei nomi. <br> Non consente la visualizzazione di ruoli o associazioni di ruolo.<br> Non consente la visualizzazione `Secrets` . La lettura del `Secrets` contenuto consente l'accesso alle `ServiceAccount` credenziali nello spazio dei nomi, che consente l'accesso all'API come qualsiasi `ServiceAccount` nello spazio dei nomi (un tipo di escalation dei privilegi).  |
| Writer RBAC del servizio Kubernetes di Azure | Consente l'accesso in lettura/scrittura alla maggior parte degli oggetti in uno spazio dei nomi. <br> Non consente la visualizzazione o la modifica di ruoli o associazioni di ruolo. <br> Consente l'accesso `Secrets` e l'esecuzione di pod come qualsiasi ServiceAccount nello spazio dei nomi, in modo che possa essere usato per ottenere i livelli di accesso all'API di qualsiasi ServiceAccount nello spazio dei nomi. |
| Amministratore RBAC del servizio Kubernetes di Azure  | Consente l'accesso dell'amministratore, che deve essere concesso all'interno di uno spazio dei nomi. <br> Consente l'accesso in lettura/scrittura alla maggior parte delle risorse in uno spazio dei nomi (o ambito del cluster), inclusa la possibilità di creare ruoli e associazioni di ruolo all'interno dello spazio dei nomi. <br> Non consente l'accesso in scrittura alla quota di risorse o allo spazio dei nomi stesso. |
| Amministrazione del cluster RBAC del servizio Kubernetes di Azure  | Consente l'accesso con privilegi avanzati per eseguire qualsiasi azione su qualsiasi risorsa. <br> Fornisce il controllo completo su tutte le risorse nel cluster e in tutti gli spazi dei nomi. |


## <a name="summary"></a>Riepilogo

Visualizzare la tabella per un breve riepilogo del modo in cui gli utenti possono eseguire l'autenticazione a Kubernetes quando è abilitata l'integrazione Azure AD. In tutti i casi, la sequenza di comandi dell'utente è:
1. Eseguire `az login` per l'autenticazione in Azure.
1. Eseguire `az aks get-credentials` per scaricare le credenziali del cluster in `.kube/config` .
1. Eseguire i `kubectl` comandi. 
   * Il primo comando può attivare l'autenticazione basata su browser per l'autenticazione nel cluster, come descritto nella tabella seguente.

Nella portale di Azure è possibile trovare:
* La *concessione del ruolo* (concessione del ruolo RBAC di Azure) a cui viene fatto riferimento nella seconda colonna viene visualizzata nella scheda **controllo di accesso** . 
* Il gruppo Amministrazione cluster Azure AD viene visualizzato nella scheda **configurazione** .
  * Trovato anche con il nome del parametro `--aad-admin-group-object-ids` nell'interfaccia della riga di comando di Azure.


| Descrizione        | Concessione ruolo obbligatoria| Gruppo/i Azure AD amministrazione cluster | Utilizzo |
| -------------------|------------|----------------------------|-------------|
| Accesso amministratore legacy con certificato client| **Ruolo di amministratore di Azure Kubernetes**. Questo ruolo consente `az aks get-credentials` di usare con il `--admin` flag, che Scarica un [certificato di amministrazione del cluster legacy (non Azure ad)](control-kubeconfig-access.md) negli utenti `.kube/config` . Si tratta dell'unico scopo del ruolo di amministratore di Azure Kubernetes.|n/d|Se non si dispone dell'accesso a un gruppo di Azure AD valido con accesso al cluster.| 
| Azure AD con RoleBindings manuale (cluster)| **Ruolo utente di Azure Kubernetes**. Il ruolo "User" consente `az aks get-credentials` di usare senza il `--admin` flag. (Questo è l'unico scopo del "ruolo utente di Azure Kubernetes"). Il risultato, in un cluster abilitato per Azure AD, è il download di [una voce vuota](control-kubeconfig-access.md) in `.kube/config` , che attiva l'autenticazione basata su browser quando viene usata per la prima volta da `kubectl` .| L'utente non si trova in nessuno di questi gruppi. Poiché l'utente non è presente in alcun gruppo di amministratori del cluster, i relativi diritti verranno controllati interamente da qualsiasi RoleBindings o ClusterRoleBindings impostati dagli amministratori del cluster. Il RoleBindings (cluster) [designa Azure ad utenti o gruppi di Azure ad](azure-ad-rbac.md) come `subjects` . Se non sono state configurate associazioni di questo tipo, l'utente non sarà in grado di excute alcun `kubectl` comando.|Se si vuole un controllo di accesso con granularità fine e non si usa il controllo degli accessi in base al ruolo di Azure per l'autorizzazione Kubernetes. Si noti che l'utente che imposta i binding deve accedere da uno degli altri metodi elencati in questa tabella.|
| Azure AD dal membro del gruppo di amministratori| Vedere sopra.|L'utente è un membro di uno dei gruppi elencati di seguito. AKS genera automaticamente un ClusterRoleBinding che associa tutti i gruppi elencati al `cluster-admin` ruolo Kubernetes. In modo che gli utenti di questi gruppi possano eseguire tutti i `kubectl` comandi come `cluster-admin` .|Se si desidera concedere agli utenti i diritti di amministratore completi e _non_ si usa il controllo degli accessi in base al ruolo di Azure per l'autorizzazione Kubernetes.|
| Azure AD con l'autorizzazione RBAC di Azure per Kubernetes|Due ruoli: <br> Per prima cosa, il **ruolo utente Azure Kubernetes** (come sopra). <br> In secondo luogo, uno dei "servizi **RBAC** di Azure Kubernetes..." ruoli elencati sopra o un'alternativa personalizzata.|Il campo ruoli di amministratore nella scheda configurazione è irrilevante quando è abilitata l'autorizzazione RBAC di Azure per Kubernetes.|Si usa RBAC di Azure per l'autorizzazione Kubernetes. Questo approccio offre un controllo con granularità fine, senza la necessità di configurare RoleBindings o ClusterRoleBindings.|

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare a usare Azure AD e il controllo degli accessi in base al ruolo di Kubernetes, vedere [Integrare Azure Active Directory con il servizio Azure Kubernetes][aks-aad].
- Per le procedure consigliate associate, vedere procedure consigliate [per l'autenticazione e l'autorizzazione in AKS][operator-best-practices-identity].
- Per iniziare a usare il controllo degli accessi in base al ruolo di Azure per l'autorizzazione Kubernetes, vedere [usare il controllo degli accessi](manage-azure-rbac.md)in base al ruolo di Azure
- Per iniziare a proteggere il `kubeconfig` file, vedere [limitare l'accesso al file di configurazione del cluster](control-kubeconfig-access.md)

Per altre informazioni sui concetti fondamentali di Kubernetes e del servizio Azure Kubernetes, vedere gli articoli seguenti:

- [Kubernetes/Cluster e carichi di lavoro del servizio Azure Kubernetes][aks-concepts-clusters-workloads]
- [Sicurezza di Kubernetes/servizio Azure Kubernetes][aks-concepts-security]
- [Kubernetes/Reti virtuali nel servizio Azure Kubernetes][aks-concepts-network]
- [Kubernetes/Archiviazione nel servizio Azure Kubernetes][aks-concepts-storage]
- [Kubernetes/Ridimensionamento nel servizio Azure Kubernetes][aks-concepts-scale]

<!-- LINKS - External -->
[kubernetes-authentication]: https://kubernetes.io/docs/reference/access-authn-authz/authentication
[webhook-token-docs]: https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - Internal -->
[openid-connect]: ../active-directory/develop/v2-protocols-oidc.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[aks-aad]: managed-aad.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-identity]: operator-best-practices-identity.md
[upgrade-per-cluster]: ../azure-monitor/containers/container-insights-update-metrics.md#upgrade-per-cluster-using-azure-cli
