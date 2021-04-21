---
title: Concetti - Accesso e identità nei servizi Azure Kubernetes
description: Informazioni sull'accesso e sull'identità in servizio Azure Kubernetes (AKS), tra cui l'integrazione Azure Active Directory, il controllo degli accessi in base al ruolo di Kubernetes e i ruoli e le associazioni.
services: container-service
ms.topic: conceptual
ms.date: 03/24/2021
author: palma21
ms.author: jpalma
ms.openlocfilehash: b10d31cf069bc4f28a1597ec12160fa6ed98b8ce
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789556"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Opzioni di accesso e identità per il servizio Azure Kubernetes

È possibile autenticare, autorizzare, proteggere e controllare l'accesso ai cluster Kubernetes in diversi modi. 
* Usando il controllo degli accessi in base al ruolo di Kubernetes, è possibile concedere a utenti, gruppi e account del servizio l'accesso solo alle risorse necessarie. 
* Con servizio Azure Kubernetes (AKS), è possibile migliorare ulteriormente la struttura di sicurezza e autorizzazioni tramite il controllo degli accessi in base al ruolo Azure Active Directory Azure. 

Il controllo degli accessi in base al ruolo di Kubernetes e il servizio Kubernetes consentono di proteggere l'accesso al cluster e forniscono solo le autorizzazioni minime necessarie a sviluppatori e operatori.

Questo articolo presenta i concetti di base che consentono di autenticare e assegnare le autorizzazioni nel servizio Web Diaks.

## <a name="aks-service-permissions"></a>Autorizzazioni del servizio AKS

Quando si crea un cluster, il servizio AKS genera o modifica le risorse necessarie (ad esempio vm e schede di interfaccia di rete) per creare ed eseguire il cluster per conto dell'utente. Questa identità è distinta dall'autorizzazione di identità del cluster, che viene creata durante la creazione del cluster.

### <a name="identity-creating-and-operating-the-cluster-permissions"></a>Creazione e gestione delle autorizzazioni del cluster da parte dell'identità

Le autorizzazioni seguenti sono necessarie per la creazione e il funzionamento del cluster da parte dell'identità.

| Autorizzazione | Motivo |
|---|---|
| `Microsoft.Compute/diskEncryptionSets/read` | Obbligatorio per leggere l'ID del set di crittografia del disco. |
| `Microsoft.Compute/proximityPlacementGroups/write` | Obbligatorio per l'aggiornamento dei gruppi di posizionamento di prossimità. |
| `Microsoft.Network/applicationGateways/read` <br/> `Microsoft.Network/applicationGateways/write` <br/> `Microsoft.Network/virtualNetworks/subnets/join/action` | Necessario per configurare i gateway applicazione e aggiungere la subnet. |
| `Microsoft.Network/virtualNetworks/subnets/join/action` | Necessario per configurare il gruppo di sicurezza di rete per la subnet quando si usa una rete virtuale personalizzata.|
| `Microsoft.Network/publicIPAddresses/join/action` <br/> `Microsoft.Network/publicIPPrefixes/join/action` | Obbligatorio per configurare gli indirizzi IP pubblici in uscita nel Load Balancer Standard. |
| `Microsoft.OperationalInsights/workspaces/sharedkeys/read` <br/> `Microsoft.OperationalInsights/workspaces/read` <br/> `Microsoft.OperationsManagement/solutions/write` <br/> `Microsoft.OperationsManagement/solutions/read` <br/> `Microsoft.ManagedIdentity/userAssignedIdentities/assign/action` | Necessario per creare e aggiornare le aree di lavoro Log Analytics e il monitoraggio di Azure per i contenitori. |

### <a name="aks-cluster-identity-permissions"></a>Autorizzazioni per l'identità del cluster del servizio Servizio Web Diaks

Le autorizzazioni seguenti vengono usate dall'identità del cluster del servizio Web Diaks, che viene creata e associata al cluster del servizio Web Diaks. Ogni autorizzazione viene usata per i motivi seguenti:

| Autorizzazione | Motivo |
|---|---|
| `Microsoft.ContainerService/managedClusters/*`  <br/> | Obbligatorio per la creazione di utenti e il funzionamento del cluster
| `Microsoft.Network/loadBalancers/delete` <br/> `Microsoft.Network/loadBalancers/read` <br/> `Microsoft.Network/loadBalancers/write` | Obbligatorio per configurare il servizio di bilanciamento del carico per un servizio LoadBalancer. |
| `Microsoft.Network/publicIPAddresses/delete` <br/> `Microsoft.Network/publicIPAddresses/read` <br/> `Microsoft.Network/publicIPAddresses/write` | Obbligatorio per trovare e configurare gli IP pubblici per un servizio LoadBalancer. |
| `Microsoft.Network/publicIPAddresses/join/action` | Obbligatorio per la configurazione di IP pubblici per un servizio LoadBalancer. |
| `Microsoft.Network/networkSecurityGroups/read` <br/> `Microsoft.Network/networkSecurityGroups/write` | Obbligatorio per creare o eliminare regole di sicurezza per un servizio LoadBalancer. |
| `Microsoft.Compute/disks/delete` <br/> `Microsoft.Compute/disks/read` <br/> `Microsoft.Compute/disks/write` <br/> `Microsoft.Compute/locations/DiskOperations/read` | Obbligatorio per configurare AzureDisks. |
| `Microsoft.Storage/storageAccounts/delete` <br/> `Microsoft.Storage/storageAccounts/listKeys/action` <br/> `Microsoft.Storage/storageAccounts/read` <br/> `Microsoft.Storage/storageAccounts/write` <br/> `Microsoft.Storage/operations/read` | Obbligatorio per configurare gli account di archiviazione per AzureFile o AzureDisk. |
| `Microsoft.Network/routeTables/read` <br/> `Microsoft.Network/routeTables/routes/delete` <br/> `Microsoft.Network/routeTables/routes/read` <br/> `Microsoft.Network/routeTables/routes/write` <br/> `Microsoft.Network/routeTables/write` | Obbligatorio per configurare le tabelle di route e le route per i nodi. |
| `Microsoft.Compute/virtualMachines/read` | Obbligatorio per trovare informazioni per le macchine virtuali in un VMAS, ad esempio zone, dominio di errore, dimensioni e dischi dati. |
| `Microsoft.Compute/virtualMachines/write` | Necessario per collegare AzureDisks a una macchina virtuale in un VMAS. |
| `Microsoft.Compute/virtualMachineScaleSets/read` <br/> `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read` <br/> `Microsoft.Compute/virtualMachineScaleSets/virtualmachines/instanceView/read` | Obbligatorio per trovare informazioni per le macchine virtuali in un set di scalabilità di macchine virtuali, ad esempio zone, dominio di errore, dimensioni e dischi dati. |
| `Microsoft.Network/networkInterfaces/write` | Obbligatorio per aggiungere una macchina virtuale in una VMAS a un pool di indirizzi back-end del servizio di bilanciamento del carico. |
| `Microsoft.Compute/virtualMachineScaleSets/write` | Obbligatorio per aggiungere un set di scalabilità di macchine virtuali a pool di indirizzi back-end del servizio di bilanciamento del carico e aumentare il numero di nodi in un set di scalabilità di macchine virtuali. |
| `Microsoft.Compute/virtualMachineScaleSets/virtualmachines/write` | Necessario per collegare AzureDisks e aggiungere una macchina virtuale da un set di scalabilità di macchine virtuali al servizio di bilanciamento del carico. |
| `Microsoft.Network/networkInterfaces/read` | Obbligatorio per la ricerca di indirizzi IP interni e pool di indirizzi back-end del servizio di bilanciamento del carico per le macchine virtuali in VMAS. |
| `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read` | Obbligatorio per cercare indirizzi IP interni e pool di indirizzi back-end del servizio di bilanciamento del carico per una macchina virtuale in un set di scalabilità di macchine virtuali. |
| `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipconfigurations/publicipaddresses/read` | Obbligatorio per trovare gli IP pubblici per una macchina virtuale in un set di scalabilità di macchine virtuali. |
| `Microsoft.Network/virtualNetworks/read` <br/> `Microsoft.Network/virtualNetworks/subnets/read` | Obbligatorio per verificare se esiste una subnet per il servizio di bilanciamento del carico interno in un altro gruppo di risorse. |
| `Microsoft.Compute/snapshots/delete` <br/> `Microsoft.Compute/snapshots/read` <br/> `Microsoft.Compute/snapshots/write` | Obbligatorio per configurare gli snapshot per AzureDisk. |
| `Microsoft.Compute/locations/vmSizes/read` <br/> `Microsoft.Compute/locations/operations/read` | Obbligatorio per trovare le dimensioni delle macchine virtuali per trovare i limiti di volume di AzureDisk. |

### <a name="additional-cluster-identity-permissions"></a>Autorizzazioni aggiuntive per l'identità del cluster

Quando si crea un cluster con attributi specifici, sono necessarie le autorizzazioni aggiuntive seguenti per l'identità del cluster. Poiché queste autorizzazioni non vengono assegnate automaticamente, è necessario aggiungerle all'identità del cluster dopo la creazione.

| Autorizzazione | Motivo |
|---|---|
| `Microsoft.Network/networkSecurityGroups/write` <br/> `Microsoft.Network/networkSecurityGroups/read` | Obbligatorio se si usa un gruppo di sicurezza di rete in un altro gruppo di risorse. Obbligatorio per configurare le regole di sicurezza per un servizio LoadBalancer. |
| `Microsoft.Network/virtualNetworks/subnets/read` <br/> `Microsoft.Network/virtualNetworks/subnets/join/action` | Obbligatorio se si usa una subnet in un altro gruppo di risorse, ad esempio una rete virtuale personalizzata. |
| `Microsoft.Network/routeTables/routes/read` <br/> `Microsoft.Network/routeTables/routes/write` | Obbligatorio se si usa una subnet associata a una tabella di route in un altro gruppo di risorse, ad esempio una rete virtuale personalizzata con una tabella di route personalizzata. Obbligatorio per verificare se esiste già una subnet per la subnet nell'altro gruppo di risorse. |
| `Microsoft.Network/virtualNetworks/subnets/read` | Obbligatorio se si usa un servizio di bilanciamento del carico interno in un altro gruppo di risorse. Obbligatorio per verificare se esiste già una subnet per il servizio di bilanciamento del carico interno nel gruppo di risorse. |
| `Microsoft.Network/privatednszones/*` | Obbligatorio se si usa una zona DNS privata in un altro gruppo di risorse, ad esempio un elemento privateDNSZone personalizzato. |

## <a name="kubernetes-rbac"></a>Controllo degli accessi in base al ruolo di Kubernetes

Il controllo degli accessi in base al ruolo di Kubernetes offre un filtro granulare delle azioni dell'utente. Con questo meccanismo di controllo:
* Assegnare a utenti o gruppi di utenti l'autorizzazione per creare e modificare le risorse o visualizzare i log dai carichi di lavoro dell'applicazione in esecuzione. 
* È possibile impostare come ambito le autorizzazioni per un singolo spazio dei nomi o per l'intero cluster del servizio AKS. 
* È possibile *creare ruoli* per definire le autorizzazioni e quindi assegnarli agli utenti con associazioni *di ruolo*.

Per altre informazioni, vedere [Uso dell'autorizzazione per il controllo degli accessi in base al ruolo di Kubernetes.][kubernetes-rbac]

### <a name="roles-and-clusterroles"></a>Role e ClusterRole

#### <a name="roles"></a>Ruoli
Prima di assegnare le autorizzazioni agli utenti con il controllo degli accessi in base al ruolo di Kubernetes, si definiranno le autorizzazioni utente come *ruolo*. Concedere autorizzazioni all'interno di uno spazio dei nomi usando i ruoli. 

> [!NOTE]
> I ruoli kubernetes *concedono le* autorizzazioni; non *negano le* autorizzazioni.

Per concedere autorizzazioni all'intero cluster o alle risorse del cluster all'esterno di uno spazio dei nomi specificato, è invece possibile usare *ClusterRoles.*

#### <a name="clusterroles"></a>ClusterRoles

Un ClusterRole concede e applica le autorizzazioni alle risorse nell'intero cluster, non a uno spazio dei nomi specifico.

### <a name="rolebindings-and-clusterrolebindings"></a>RoleBinding e ClusterRoleBinding

Dopo aver definito i ruoli per concedere le autorizzazioni alle risorse, assegnare le autorizzazioni di controllo degli accessi in base al ruolo di Kubernetes con *un RoleBinding*. Se il cluster del servizio Azure Active Directory si integra con [Azure Active Directory (Azure AD),](#azure-ad-integration)RoleBindings concede le autorizzazioni agli utenti Azure AD eseguire azioni all'interno del cluster. Per informazioni, vedere Controllare l'accesso alle risorse del cluster usando il controllo degli accessi in base al ruolo di [Kubernetes Azure Active Directory identità.](azure-ad-rbac.md)

#### <a name="rolebindings"></a>RoleBindings

Assegnare ruoli agli utenti per un determinato spazio dei nomi usando RoleBindings. Con RoleBindings, è possibile separare logicamente un singolo cluster del servizio Web Disassociato di servizio, consentendo solo agli utenti di accedere alle risorse dell'applicazione nello spazio dei nomi assegnato. 

Per associare i ruoli nell'intero cluster o alle risorse del cluster all'esterno di uno spazio dei nomi specificato, usare *invece ClusterRoleBindings*.

#### <a name="clusterrolebinding"></a>ClusterRoleBinding

Con ClusterRoleBinding, si associano i ruoli agli utenti e si applicano alle risorse nell'intero cluster, non a uno spazio dei nomi specifico. Questo approccio consente di concedere agli amministratori o ai tecnici del supporto l'accesso a tutte le risorse nel cluster del servizio Azure Kubernetes.


> [!NOTE]
> Microsoft o servizio Kubernetes esegue qualsiasi azione del cluster con il consenso dell'utente con un ruolo Kubernetes predefinito e `aks-service` un'associazione di ruolo `aks-service-rolebinding` incorporata. 
> 
> Questo ruolo consente al servizio AKS di risolvere e diagnosticare i problemi del cluster, ma non può modificare le autorizzazioni, creare ruoli o associazioni di ruolo o altre azioni con privilegi elevati. L'accesso ai ruoli è abilitato solo con ticket di supporto attivi con accesso JIT (Just-In-Time). Altre informazioni sui criteri [di supporto del servizio Web Diaks.](support-policies.md)


### <a name="kubernetes-service-accounts"></a>Account del servizio Kubernetes

*Gli account* del servizio sono uno dei tipi di utente principali in Kubernetes. L'API Kubernetes contiene e gestisce gli account del servizio. Le credenziali dell'account del servizio vengono archiviate come segreti Kubernetes, consentendone l'uso da parte di pod autorizzati per comunicare con il server API. La maggior parte delle richieste dell'API fornisce un token di autenticazione per un account del servizio o un account utente normale.

Gli account utente normali consentono un accesso più tradizionale per amministratori o sviluppatori umani, non solo per servizi e processi. Anche se Kubernetes non offre una soluzione di gestione delle identità per archiviare account utente e password normali, è possibile integrare soluzioni di gestione delle identità esterne in Kubernetes. Per i cluster del servizio Web Diaks, questa soluzione di gestione delle identità integrata è Azure AD.

Per altre informazioni sulle opzioni di gestione delle identità in Kubernetes, vedere [Kubernetes authentication][kubernetes-authentication] (Autenticazione di Kubernetes).

## <a name="azure-ad-integration"></a>Integrazione di Azure AD

Migliorare la sicurezza del cluster del servizio Servizio Web Di Azure AD'integrazione. Basato su decenni di gestione delle identità aziendali, Azure AD è un servizio di gestione di identità e directory multi-tenant basato sul cloud che combina i principali servizi directory, la gestione dell'accesso alle applicazioni e la protezione delle identità. Con Azure AD, è possibile integrare le identità locali nei cluster del servizio Azure Kubernetes per offrire un'unica origine per la sicurezza e la gestione degli account.

![Integrazione di Azure Active Directory con i cluster del servizio Azure Kubernetes](media/concepts-identity/aad-integration.png)

Con i cluster del servizio Azure Kubernetes integrati in AD Azure, è possibile concedere agli utenti o ai gruppi l'accesso alle risorse di Kubernetes all'interno di uno spazio dei nomi o nel cluster. 

1. Per ottenere un contesto `kubectl` di configurazione, un utente esegue il [comando az aks get-credentials.][az-aks-get-credentials] 
1. Quando un utente interagisce con il cluster del servizio Web Diaks con , gli viene richiesto di accedere con le `kubectl` proprie Azure AD credenziali. 

Questo approccio fornisce un'unica origine per la gestione degli account utente e le credenziali della password. L'utente può accedere solo risorse definite dall'amministratore del cluster.

L'autenticazione di Azure AD è disponibile per i cluster di servizio Azure Kubernetes con OpenID Connect. OpenID Connect è un livello di gestione delle identità basato sul protocollo OAuth 2.0. Per altre informazioni sui OpenID Connect, vedere la [documentazione di Open ID Connect.][openid-connect] All'interno del cluster Kubernetes viene usata [l'autenticazione basata su token webhook][webhook-token-docs] per verificare i token di autenticazione. L'autenticazione del token del webhook viene configurata e gestita come parte del cluster servizio Azure Kubernetes.

### <a name="webhook-and-api-server"></a>Webhook e server API

![Flusso di autenticazione del webhook e del server API](media/concepts-identity/auth-flow.png)

Come illustrato nell'immagine precedente, il server API chiama il server webhook del servizio Web AKS ed esegue i passaggi seguenti:

1. `kubectl`usa l'Azure AD client per consentire agli utenti di accedere con il flusso di concessione dell'autorizzazione del dispositivo [OAuth 2.0.](../active-directory/develop/v2-oauth2-device-code.md)
2. Azure AD fornisce un access_token, id_token e un refresh_token.
3. L'utente effettua una richiesta a `kubectl` con un access_token da `kubeconfig` .
4. `kubectl` invia il access_token al server API.
5. Il server API è configurato con il server webhook di autenticazione per eseguire la convalida.
6. Il server webhook di autenticazione conferma che token JSON Web firma è valida controllando Azure AD chiave di firma pubblica.
7. L'applicazione server usa le credenziali fornite dall'utente per eseguire query sulle appartenenze ai gruppi dell'utente connesso dal API Graph.
8. Viene inviata una risposta al server API con informazioni sull'utente, ad esempio l'attestazione del nome dell'entità utente (UPN) del token di accesso e l'appartenenza al gruppo dell'utente in base all'ID oggetto.
9. L'API esegue una decisione di autorizzazione basata sul ruolo Kubernetes/RoleBinding.
10. Dopo l'autorizzazione, il server API restituisce una risposta a `kubectl` .
11. `kubectl` fornisce commenti e suggerimenti all'utente.
 
Informazioni su come integrare il servizio AKS con Azure AD con la guida alla procedura di integrazione Azure AD gestione del servizio Azure AD servizio [AKS.](managed-aad.md)

## <a name="azure-role-based-access-control"></a>Controllo degli accessi in base al ruolo di Azure

Il controllo degli accessi in base al ruolo [](../azure-resource-manager/management/overview.md) di Azure è un sistema di autorizzazione basato su Azure Resource Manager che offre una gestione granulare degli accessi delle risorse di Azure.

| Sistema di controllo degli accessi in base al ruolo | Descrizione |
|---|---|
| Controllo degli accessi in base al ruolo di Kubernetes | Progettato per funzionare sulle risorse Kubernetes all'interno del cluster del servizio Kubernetes. |
| Controllo degli accessi in base al ruolo di Azure | Progettato per funzionare sulle risorse all'interno della sottoscrizione di Azure. |

Con il controllo degli accessi in base al ruolo di Azure si crea una *definizione del ruolo* che determina le autorizzazioni da applicare. Assegnare quindi a un utente o a un gruppo questa definizione di ruolo tramite *un'assegnazione di ruolo* per un ambito *specifico.* L'ambito può essere una singola risorsa, un gruppo di risorse o una sottoscrizione.

Per altre informazioni, vedere Informazioni sul controllo degli [accessi in][azure-rbac] base al ruolo di Azure

Esistono due livelli di accesso necessari per il funzionamento completo di un cluster del servizio AKS: 
* [Accedere alla risorsa servizio AzureKs nella sottoscrizione di Azure.](#azure-rbac-to-authorize-access-to-the-aks-resource) 
  * Controllare il ridimensionamento o l'aggiornamento del cluster usando le API del servizio Web Disatteso.
  * Eseguire il pull di `kubeconfig` .
* Accesso all'API Kubernetes. Questo accesso è controllato da:
  * [Controllo degli accessi in base al ruolo di Kubernetes](#kubernetes-rbac) (tradizionalmente).
  * [Integrazione del controllo degli accessi in base al ruolo di Azure con il servizio Azure Kubernetes per l'autorizzazione](#azure-rbac-for-kubernetes-authorization-preview).

### <a name="azure-rbac-to-authorize-access-to-the-aks-resource"></a>Controllo degli accessi in base al ruolo di Azure per autorizzare l'accesso alla risorsa servizio AzureKs

Con il controllo degli accessi in base al ruolo di Azure, è possibile fornire agli utenti (o alle identità) l'accesso granulare alle risorse del servizio AzureKs in una o più sottoscrizioni. Ad esempio, è possibile usare il ruolo [servizio Azure Kubernetes collaboratore per](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-contributor-role) ridimensionare e aggiornare il cluster. Nel frattempo, un altro utente con il [servizio Azure Kubernetes amministratore del cluster](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role) ha solo l'autorizzazione per eseguire il pull di `kubeconfig` Admin.

In alternativa, è possibile assegnare all'utente il ruolo [Collaboratore](../role-based-access-control/built-in-roles.md#contributor) generale. Con il ruolo Collaboratore generale, gli utenti possono eseguire le autorizzazioni precedenti e tutte le azioni possibili nella risorsa del servizio Web Disatteso, ad eccezione della gestione delle autorizzazioni.

[Usare il controllo degli accessi in base al ruolo di Azure per definire l'accesso al file di configurazione di Kubernetes nel servizio Azure Kubernetes.](control-kubeconfig-access.md)

### <a name="azure-rbac-for-kubernetes-authorization-preview"></a>Controllo degli accessi in base al ruolo di Azure per l'autorizzazione kubernetes (anteprima)

Con l'integrazione del controllo degli accessi in base al ruolo di Azure, il servizio Azure Kubernetes userà un server webhook di autorizzazione Kubernetes per poter gestire le autorizzazioni e le assegnazioni delle risorse cluster Kubernetes integrate in Azure AD usando la definizione del ruolo di Azure e le assegnazioni di ruolo.

![Flusso di autorizzazione di Controllo degli accessi in base al ruolo di Azure per Kubernetes](media/concepts-identity/azure-rbac-k8s-authz-flow.png)

Come illustrato nel diagramma precedente, quando si usa l'integrazione del controllo degli accessi in base al ruolo di Azure, tutte le richieste all'API Kubernetes seguiranno lo stesso flusso di autenticazione illustrato nella sezione Azure Active Directory [di integrazione.](#azure-ad-integration) 

Se l'identità che effettua la richiesta esiste in Azure AD, Azure si unirà al controllo degli accessi in base al ruolo di Kubernetes per autorizzare la richiesta. Se l'identità esiste al di Azure AD (ad esempio, un account del servizio Kubernetes), l'autorizzazione impedirà il normale controllo degli accessi in base al ruolo di Kubernetes.

In questo scenario si usano i meccanismi e le API di Controllo degli accessi in base al ruolo di Azure per assegnare agli utenti ruoli predefiniti o creare ruoli personalizzati, come si farebbe con i ruoli Kubernetes. 

Con questa funzionalità, non solo si connettono agli utenti le autorizzazioni per la risorsa servizio Azure Kubernetes tra le sottoscrizioni, ma si configurano anche il ruolo e le autorizzazioni per all'interno di ognuno di questi cluster che controllano l'accesso all'API Kubernetes. Ad esempio, è possibile concedere il `Azure Kubernetes Service RBAC Viewer` ruolo nell'ambito della sottoscrizione. Il destinatario del ruolo sarà in grado di elencare e ottenere tutti gli oggetti Kubernetes da tutti i cluster senza modificarli.

> [!IMPORTANT]
> È necessario abilitare l'autorizzazione del controllo degli accessi in base al ruolo di Azure per Kubernetes prima di usare questa funzionalità. Per altre informazioni dettagliate e istruzioni dettagliate, seguire la guida all'uso del controllo degli accessi in base al ruolo di Azure per [l'autorizzazione kubernetes.](manage-azure-rbac.md)

#### <a name="built-in-roles"></a>Ruoli predefiniti

Il servizio AKS offre i quattro ruoli predefiniti seguenti. Sono simili ai ruoli predefiniti di [Kubernetes](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#user-facing-roles) con alcune differenze, ad esempio il supporto dei CRD. Vedere l'elenco completo delle azioni consentite da ogni [ruolo predefinito di Azure.](../role-based-access-control/built-in-roles.md)

| Ruolo                                | Descrizione  |
|-------------------------------------|--------------|
| servizio Azure Kubernetes visualizzatore controllo degli accessi in base al ruolo  | Consente l'accesso in sola lettura per visualizzare la maggior parte degli oggetti in uno spazio dei nomi. <br> Non consente la visualizzazione di ruoli o associazioni di ruolo.<br> Non consente la visualizzazione di `Secrets` . La lettura del contenuto consente l'accesso alle credenziali nello spazio dei nomi, che consentirebbe l'accesso alle API come qualsiasi nello spazio dei nomi (una forma di `Secrets` `ServiceAccount` escalation dei `ServiceAccount` privilegi).  |
| servizio Azure Kubernetes RBAC Writer | Consente l'accesso in lettura/scrittura alla maggior parte degli oggetti in uno spazio dei nomi. <br> Non consente la visualizzazione o la modifica di ruoli o associazioni di ruolo. <br> Consente l'accesso e l'esecuzione di pod come qualsiasi ServiceAccount nello spazio dei nomi, in modo che possa essere usato per ottenere i livelli di accesso API di `Secrets` qualsiasi ServiceAccount nello spazio dei nomi. |
| servizio Azure Kubernetes amministratore del controllo degli accessi in base al ruolo  | Consente l'accesso amministratore, destinato a essere concesso all'interno di uno spazio dei nomi. <br> Consente l'accesso in lettura/scrittura alla maggior parte delle risorse in uno spazio dei nomi (o nell'ambito del cluster), inclusa la possibilità di creare ruoli e associazioni di ruolo all'interno dello spazio dei nomi. <br> Non consente l'accesso in scrittura alla quota di risorse o allo spazio dei nomi stesso. |
| servizio Azure Kubernetes amministratore del cluster controllo degli accessi in base al ruolo  | Consente l'accesso utente con privilegi super per eseguire qualsiasi azione su qualsiasi risorsa. <br> Offre il controllo completo su ogni risorsa nel cluster e in tutti gli spazi dei nomi. |


## <a name="summary"></a>Riepilogo

Visualizzare la tabella per un breve riepilogo di come gli utenti possono eseguire l'autenticazione a Kubernetes quando Azure AD'integrazione è abilitata. In tutti i casi, la sequenza di comandi dell'utente è:
1. Eseguire `az login` per eseguire l'autenticazione in Azure.
1. Eseguire `az aks get-credentials` per scaricare le credenziali per il cluster in `.kube/config` .
1. Eseguire `kubectl` i comandi. 
   * Il primo comando può attivare l'autenticazione basata su browser per l'autenticazione nel cluster, come descritto nella tabella seguente.

Nella portale di Azure è possibile trovare:
* La *concessione di ruolo* (concessione del ruolo Controllo degli accessi in base al ruolo di Azure) a cui si fa riferimento nella seconda colonna viene visualizzata nella scheda Controllo **di** accesso. 
* Il gruppo di Azure AD cluster viene visualizzato nella **scheda** Configurazione.
  * Disponibile anche con il nome del parametro `--aad-admin-group-object-ids` nell'interfaccia della riga di comando di Azure.


| Descrizione        | Concessione di ruolo obbligatoria| Gruppi di Azure AD cluster | Utilizzo |
| -------------------|------------|----------------------------|-------------|
| Accesso amministratore legacy con certificato client| **Ruolo di amministratore di Azure Kubernetes**. Questo ruolo consente di essere usato con il flag , che scarica un certificato di amministrazione `az aks get-credentials` `--admin` del cluster legacy [(non Azure AD)](control-kubeconfig-access.md) nell'oggetto dell'utente. `.kube/config` Questo è l'unico scopo del "ruolo di amministratore di Azure Kubernetes".|n/d|Se si è bloccati definitivamente perché non si ha accesso a un gruppo di Azure AD valido con accesso al cluster.| 
| Azure AD con manuale (Cluster)RoleBindings| **Ruolo utente di Azure Kubernetes**. Il ruolo "User" `az aks get-credentials` consente di essere usato senza il flag `--admin` . Questo è l'unico scopo di "Ruolo utente di Azure Kubernetes". Il risultato, in un cluster abilitato Azure AD, è il download di una voce vuota [in](control-kubeconfig-access.md) , che attiva l'autenticazione basata su browser quando viene usata per la prima `.kube/config` volta da `kubectl` .| L'utente non si trova in nessuno di questi gruppi. Poiché l'utente non si trova in alcun gruppo di amministratori del cluster, i relativi diritti verranno controllati interamente da roleBindings o ClusterRoleBindings che sono stati impostati dagli amministratori del cluster. (Cluster)RoleBindings [designa Azure AD utenti](azure-ad-rbac.md) o Azure AD gruppi come `subjects` . Se non sono state impostate associazioni di questo tipo, l'utente non sarà in grado di eseguire alcun `kubectl` comando.|Se si vuole un controllo degli accessi con granularità fine e non si usa il controllo degli accessi in base al ruolo di Azure per l'autorizzazione kubernetes. Si noti che l'utente che configura le associazioni deve accedere con uno degli altri metodi elencati in questa tabella.|
| Azure AD per membro del gruppo di amministratori| Vedere sopra.|L'utente è membro di uno dei gruppi elencati di seguito. Il servizio Kubernetes genera automaticamente un clusterRoleBinding che associa tutti i gruppi elencati al `cluster-admin` ruolo Kubernetes. Gli utenti di questi gruppi possono quindi eseguire tutti `kubectl` i comandi come `cluster-admin` .|Se si vogliono concedere facilmente agli utenti diritti di amministratore completi e non si _usa_ l'autorizzazione del controllo degli accessi in base al ruolo di Azure per Kubernetes.|
| Azure AD con il controllo degli accessi in base al ruolo di Azure per l'autorizzazione kubernetes|Due ruoli: <br> In primo **luogo, ruolo utente di Azure Kubernetes** (come sopra). <br> In secondo momento, uno dei "servizio Azure Kubernetes **controllo degli accessi in base al ruolo..."** ruoli elencati in precedenza o un'alternativa personalizzata.|Il campo ruoli di amministratore nella scheda Configurazione è irrilevante quando è abilitato il controllo degli accessi in base al ruolo di Azure per l'autorizzazione kubernetes.|Si usa il controllo degli accessi in base al ruolo di Azure per l'autorizzazione kubernetes. Questo approccio offre un controllo con granularità fine, senza la necessità di configurare RoleBindings o ClusterRoleBindings.|

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare a usare Azure AD e il controllo degli accessi in base al ruolo di Kubernetes, vedere [Integrare Azure Active Directory con il servizio Azure Kubernetes][aks-aad].
- Per le procedure consigliate associate, vedere [Best practices for authentication and authorization in AKS][operator-best-practices-identity].
- Per iniziare a usare il controllo degli accessi in base al ruolo di Azure per l'autorizzazione kubernetes, vedere Usare il controllo degli accessi in base al ruolo di Azure per autorizzare l'accesso all'interno [del cluster servizio Azure Kubernetes (AKS).](manage-azure-rbac.md)
- Per iniziare a proteggere il `kubeconfig` file, vedere Limitare [l'accesso al file di configurazione del cluster](control-kubeconfig-access.md)

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
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[azure-rbac]: ../role-based-access-control/overview.md
[aks-aad]: managed-aad.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-identity]: operator-best-practices-identity.md
[upgrade-per-cluster]: ../azure-monitor/containers/container-insights-update-metrics.md#upgrade-per-cluster-using-azure-cli
