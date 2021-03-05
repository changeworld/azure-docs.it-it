---
title: Concetti - Accesso e identità nei servizi Azure Kubernetes
description: Informazioni su accesso e identità in Azure Kubernetes Service (AKS), tra cui l'integrazione Azure Active Directory, il controllo degli accessi in base al ruolo Kubernetes (Kubernetes RBAC) e i ruoli e le associazioni.
services: container-service
ms.topic: conceptual
ms.date: 07/07/2020
author: palma21
ms.author: jpalma
ms.openlocfilehash: 98044f6ff6311241717cb66a6e26a72702d749e6
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181449"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Opzioni di accesso e identità per il servizio Azure Kubernetes

Esistono diversi modi per eseguire l'autenticazione, controllare l'accesso/autorizzazione e proteggere i cluster Kubernetes. Usando il controllo degli accessi in base al ruolo Kubernetes (Kubernetes RBAC), è possibile concedere a utenti, gruppi e account del servizio l'accesso solo alle risorse necessarie. Con Azure Kubernetes Service (AKS) è possibile migliorare ulteriormente la struttura di sicurezza e autorizzazioni usando Azure Active Directory e il controllo degli accessi in base al ruolo di Azure. Questi approcci consentono di proteggere l'accesso al cluster e forniscono solo le autorizzazioni minime necessarie per gli sviluppatori e gli operatori.

Questo articolo presenta i concetti di base che consentono di autenticare e assegnare le autorizzazioni in AKS.

## <a name="aks-service-permissions"></a>Autorizzazioni del servizio AKS

Quando si crea un cluster, AKS crea o modifica le risorse necessarie per creare ed eseguire il cluster, ad esempio macchine virtuali e NIC, per conto dell'utente che crea il cluster. Questa identità è diversa dall'autorizzazione di identità del cluster, creata durante la creazione del cluster.

### <a name="identity-creating-and-operating-the-cluster-permissions"></a>Identità creazione e gestione delle autorizzazioni del cluster

Le autorizzazioni seguenti sono necessarie per l'identità che crea e gestisce il cluster.

| Autorizzazione | Motivo |
|---|---|
| Microsoft. Compute/diskEncryptionSets/Read | Obbligatorio per leggere l'ID del set di crittografia del disco. |
| Microsoft. Compute/proximityPlacementGroups/Write | Obbligatorio per l'aggiornamento dei gruppi di posizionamento di prossimità. |
| Microsoft.Network/applicationGateways/read <br/> Microsoft.Network/applicationGateways/write <br/> Microsoft.Network/virtualNetworks/subnets/join/action | Necessario per configurare i gateway applicazione e aggiungere la subnet. |
| Microsoft.Network/virtualNetworks/subnets/join/action | Necessario per configurare il gruppo di sicurezza di rete per la subnet quando si usa un VNET personalizzato.|
| Microsoft.Network/publicIPAddresses/join/action <br/> Microsoft.Network/publicIPPrefixes/join/action | Necessario per configurare gli indirizzi IP pubblici in uscita nella Load Balancer Standard. |
| Microsoft. OperationalInsights/Workspaces/sharedkeys/Read <br/> Microsoft.OperationalInsights/workspaces/read <br/> Microsoft.OperationsManagement/solutions/write <br/> Microsoft.OperationsManagement/solutions/read <br/> Microsoft.ManagedIdentity/userAssignedIdentities/assign/action | Necessaria per creare e aggiornare le aree di lavoro Log Analytics e il monitoraggio di Azure per i contenitori. |

### <a name="aks-cluster-identity-permissions"></a>Autorizzazioni di identità del cluster AKS

Le autorizzazioni seguenti vengono usate dall'identità del cluster AKS, che viene creata e associata al cluster AKS quando viene creato il cluster. Ogni autorizzazione viene usata per i motivi seguenti:

| Autorizzazione | Motivo |
|---|---|
| Microsoft. servizio contenitore/managedClusters/*  <br/> | Obbligatorio per la creazione di utenti e il funzionamento del cluster
| Microsoft.Network/loadBalancers/delete <br/> Microsoft.Network/loadBalancers/read <br/> Microsoft.Network/loadBalancers/write | Necessario per configurare il servizio di bilanciamento del carico per un servizio LoadBalancer. |
| Microsoft.Network/publicIPAddresses/delete <br/> Microsoft.Network/publicIPAddresses/read <br/> Microsoft.Network/publicIPAddresses/write | Necessario per trovare e configurare indirizzi IP pubblici per un servizio LoadBalancer. |
| Microsoft.Network/publicIPAddresses/join/action | Obbligatorio per la configurazione di indirizzi IP pubblici per un servizio LoadBalancer. |
| Microsoft.Network/networkSecurityGroups/read <br/> Microsoft.Network/networkSecurityGroups/write | Obbligatorio per creare o eliminare regole di sicurezza per un servizio LoadBalancer. |
| Microsoft.Compute/disks/delete <br/> Microsoft.Compute/disks/read <br/> Microsoft.Compute/disks/write <br/> Microsoft. Compute/locations/decapodistrias/Read | Obbligatorio per configurare AzureDisks. |
| Microsoft.Storage/storageAccounts/delete <br/> Microsoft.Storage/storageAccounts/listKeys/action <br/> Microsoft.Storage/storageAccounts/read <br/> Microsoft.Storage/storageAccounts/write <br/> Microsoft.Storage/operations/read | Obbligatorio per configurare gli account di archiviazione per AzureFile o AzureDisk. |
| Microsoft.Network/routeTables/read <br/> Microsoft.Network/routeTables/routes/delete <br/> Microsoft.Network/routeTables/routes/read <br/> Microsoft.Network/routeTables/routes/write <br/> Microsoft.Network/routeTables/write | Obbligatorio per configurare le route e le tabelle di route per i nodi. |
| Microsoft.Compute/virtualMachines/read | Obbligatorio per trovare informazioni per le macchine virtuali in una VMAS, ad esempio zone, dominio di errore, dimensioni e dischi dati. |
| Microsoft.Compute/virtualMachines/write | Obbligatorio per alleghire AzureDisks a una macchina virtuale in un VMAS. |
| Microsoft.Compute/virtualMachineScaleSets/read <br/> Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read <br/> Microsoft. Compute/virtualMachineScaleSets/VirtualMachines/instanceView/Read | Obbligatorio per trovare informazioni per le macchine virtuali in un set di scalabilità di macchine virtuali, ad esempio zone, dominio di errore, dimensioni e dischi dati. |
| Microsoft.Network/networkInterfaces/write | Obbligatorio per aggiungere una macchina virtuale in una VMAS a un pool di indirizzi back-end del servizio di bilanciamento del carico. |
| Microsoft.Compute/virtualMachineScaleSets/write | Richiesto per aggiungere un set di scalabilità di macchine virtuali a un pool di indirizzi back-end del servizio di bilanciamento del carico e a nodi di scalabilità orizzontale in un set di scalabilità |
| Microsoft. Compute/virtualMachineScaleSets/VirtualMachines/Write | Obbligatorio per alleghire AzureDisks e aggiungere una macchina virtuale da un set di scalabilità di macchine virtuali al servizio di bilanciamento del carico. |
| Microsoft.Network/networkInterfaces/read | Obbligatorio per la ricerca di indirizzi IP interni e pool di indirizzi back-end di bilanciamento del carico per le macchine virtuali in una VMAS. |
| Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read | Obbligatorio per la ricerca di indirizzi IP interni e pool di indirizzi back-end di bilanciamento del carico per una macchina virtuale in un set di scalabilità di macchine virtuali. |
| Microsoft. Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipconfigurations/publicipaddresses/Read | Necessaria per trovare gli indirizzi IP pubblici per una macchina virtuale in un set di scalabilità di macchine virtuali. |
| Microsoft.Network/virtualNetworks/read <br/> Microsoft.Network/virtualNetworks/subnets/read | Necessaria per verificare se esiste una subnet per il servizio di bilanciamento del carico interno in un altro gruppo di risorse. |
| Microsoft.Compute/snapshots/delete <br/> Microsoft.Compute/snapshots/read <br/> Microsoft.Compute/snapshots/write | Obbligatorio per configurare gli snapshot per AzureDisk. |
| Microsoft.Compute/locations/vmSizes/read <br/> Microsoft.Compute/locations/operations/read | Necessaria per trovare le dimensioni delle macchine virtuali per trovare i limiti del volume AzureDisk. |

### <a name="additional-cluster-identity-permissions"></a>Autorizzazioni di identità del cluster aggiuntive

Le autorizzazioni aggiuntive seguenti sono necessarie per l'identità del cluster quando si crea un cluster con attributi specifici. Queste autorizzazioni non vengono assegnate automaticamente, quindi è necessario aggiungere queste autorizzazioni all'identità del cluster dopo la relativa creazione.

| Autorizzazione | Motivo |
|---|---|
| Microsoft.Network/networkSecurityGroups/write <br/> Microsoft.Network/networkSecurityGroups/read | Obbligatorio se si usa un gruppo di sicurezza di rete in un altro gruppo di risorse. Obbligatorio per configurare le regole di sicurezza per un servizio LoadBalancer. |
| Microsoft.Network/virtualNetworks/subnets/read <br/> Microsoft.Network/virtualNetworks/subnets/join/action | Obbligatorio se si usa una subnet in un altro gruppo di risorse, ad esempio un VNET personalizzato. |
| Microsoft.Network/routeTables/routes/read <br/> Microsoft.Network/routeTables/routes/write | Obbligatorio se si usa una subnet associata a una tabella di route in un altro gruppo di risorse, ad esempio un VNET personalizzato con una tabella di route personalizzata. Necessaria per verificare se esiste già una subnet per la subnet nell'altro gruppo di risorse. |
| Microsoft.Network/virtualNetworks/subnets/read | Obbligatorio se si usa un servizio di bilanciamento del carico interno in un altro gruppo di risorse. Necessaria per verificare se una subnet esiste già per il servizio di bilanciamento del carico interno nel gruppo di risorse. |

## <a name="kubernetes-role-based-access-control-kubernetes-rbac"></a>Controllo degli accessi in base al ruolo Kubernetes (Kubernetes RBAC)

Per fornire filtri granulari delle azioni che gli utenti possono eseguire, Kubernetes usa il controllo degli accessi in base al ruolo Kubernetes (Kubernetes RBAC). Questo meccanismo di controllo consente di assegnare agli utenti o ai gruppi di utenti l'autorizzazione per eseguire operazioni come la creazione o la modifica di risorse oppure la visualizzazione di log generati dall'esecuzione dei carichi di lavoro dell'applicazione. È possibile definire l'ambito di queste autorizzazioni in un singolo spazio dei nomi o concederle nell'intero cluster del servizio Azure Kubernetes. Con il controllo degli accessi in base al ruolo, si creano i *ruoli* per definire le autorizzazioni e quindi si assegnano tali ruoli agli utenti con le *associazioni dei ruoli*.

Per ulteriori informazioni, vedere [utilizzo dell'autorizzazione RBAC Kubernetes][kubernetes-rbac].

### <a name="roles-and-clusterroles"></a>Role e ClusterRole

Prima di assegnare le autorizzazioni agli utenti con il controllo degli accessi in base al ruolo di Kubernetes, si definiscono tali autorizzazioni come *Role*. I ruoli di Kubernetes *concedono* le autorizzazioni. Non esiste alcun concetto di autorizzazione *Deny* .

I ruoli vengono usati per concedere le autorizzazioni all'interno di uno spazio dei nomi. Se è necessario concedere le autorizzazioni nell'intero cluster o alle risorse cluster all'esterno di un determinato spazio dei nomi, è invece possibile usare i *ClusterRole*.

Un ClusterRole concede le autorizzazioni alle risorse nello stesso modo, ma può essere applicato alle risorse di tutto il cluster, non a uno specifico spazio dei nomi.

### <a name="rolebindings-and-clusterrolebindings"></a>RoleBinding e ClusterRoleBinding

Dopo aver definito i ruoli per concedere le autorizzazioni alle risorse, si assegnano tali autorizzazioni di controllo degli accessi in base al ruolo di Kubernetes con un *RoleBinding*. Se il cluster AKS si [integra con Azure Active Directory (Azure ad)](#azure-active-directory-integration), le associazioni rappresentano il modo in cui gli utenti Azure ad ricevono le autorizzazioni per eseguire azioni all'interno del cluster, vedere come [controllare l'accesso alle risorse del cluster usando il controllo degli accessi in base al ruolo di Kubernetes e le identità di Azure Active Directory](azure-ad-rbac.md).

Le associazioni dei ruoli vengono usate per assegnare i ruoli per un determinato spazio dei nomi. Questo approccio consente di separare logicamente un singolo cluster del servizio Azure Kubernetes e gli utenti potranno accedere solo alle risorse dell'applicazione nello spazio dei nomi assegnato. Se è necessario associare i ruoli nell'intero cluster o alle risorse cluster all'esterno di un determinato spazio dei nomi, è invece possibile usare i *ClusterRoleBinding*.

Un ClusterRoleBinding associa i ruoli agli utenti nello stesso modo, ma può essere applicato alle risorse di tutto il cluster, non a uno specifico spazio dei nomi. Questo approccio consente di concedere agli amministratori o ai tecnici del supporto l'accesso a tutte le risorse nel cluster del servizio Azure Kubernetes.


> [!NOTE]
> Qualsiasi azione del cluster eseguita da Microsoft/AKS viene effettuata con il consenso dell'utente con un ruolo predefinito di Kubernetes `aks-service` e un'associazione di ruolo incorporata `aks-service-rolebinding` . Questo ruolo consente ad AKS di risolvere e diagnosticare i problemi del cluster, ma non di modificare le autorizzazioni né di creare ruoli o associazioni di ruolo o altre azioni con privilegi elevati. L'accesso ai ruoli viene abilitato solo in ticket di supporto attivi con accesso just-in-time (JIT). Altre informazioni sui [criteri di supporto di AKS](support-policies.md).


### <a name="kubernetes-service-accounts"></a>Account del servizio Kubernetes

Uno dei tipi di utenti primari in Kubernetes è un *account del servizio*. Un account del servizio esiste ed è gestito nell'API Kubernetes. Le credenziali per gli account del servizio vengono archiviate come segreti di Kubernetes e possono quindi essere usate da pod autorizzati a comunicare con il server dell'API. La maggior parte delle richieste dell'API fornisce un token di autenticazione per un account del servizio o un account utente normale.

Gli account utente normali consentono un accesso più tradizionale per gli amministratori o gli sviluppatori umani, non solo per i servizi e i processi. Kubernetes stesso non fornisce una soluzione di gestione delle identità in cui vengono archiviati gli account utente e le password normali. ma è possibile integrare in Kubernetes soluzioni di gestione delle identità esterne. Per i cluster del servizio Azure Kubernetes, la soluzione integrata di gestione delle identità è Azure Active Directory.

Per altre informazioni sulle opzioni di gestione delle identità in Kubernetes, vedere [Kubernetes authentication][kubernetes-authentication] (Autenticazione di Kubernetes).

## <a name="azure-active-directory-integration"></a>Integrazione di Azure Active Directory

È possibile migliorare la sicurezza dei cluster del servizio Azure Kubernetes con l'integrazione di Azure Active Directory (AD). Azure Active Directory (Azure AD), nato da decenni di esperienza nella gestione delle identità aziendali, è un servizio directory e di gestione delle identità basato sul cloud multi-tenant, che combina i principali servizi directory, la gestione dell'accesso alle applicazioni e la protezione delle identità. Con Azure AD, è possibile integrare le identità locali nei cluster del servizio Azure Kubernetes per offrire un'unica origine per la sicurezza e la gestione degli account.

![Integrazione di Azure Active Directory con i cluster del servizio Azure Kubernetes](media/concepts-identity/aad-integration.png)

Con i cluster del servizio Azure Kubernetes integrati in AD Azure, è possibile concedere agli utenti o ai gruppi l'accesso alle risorse di Kubernetes all'interno di uno spazio dei nomi o nel cluster. Per ottenere un contesto di configurazione `kubectl`, un utente può eseguire il comando [az servizio Azure Kubernetes get-credentials][az-aks-get-credentials]. Quando un utente interagisce con il cluster AKS con `kubectl` , viene richiesto di eseguire l'accesso con le credenziali Azure ad. Questo approccio fornisce un'unica origine per la gestione degli account utente e le credenziali della password. L'utente può accedere solo risorse definite dall'amministratore del cluster.

L'autenticazione di Azure AD è disponibile per i cluster di servizio Azure Kubernetes con OpenID Connect. OpenID Connect è un livello di gestione delle identità basato sul protocollo OAuth 2.0. Per ulteriori informazioni su OpenID Connect, vedere la [documentazione relativa a Open ID Connect][openid-connect]. Dall'interno del cluster Kubernetes, [l'autenticazione del token webhook][webhook-token-docs] viene usata per verificare i token di autenticazione. L'autenticazione del token del webhook viene configurata e gestita come parte del cluster servizio Azure Kubernetes.

### <a name="webhook-and-api-server"></a>Webhook e Server API

![Flusso di autenticazione del webhook e del server API](media/concepts-identity/auth-flow.png)

Come illustrato nell'immagine precedente, il server API chiama il server del webhook AKS ed esegue i passaggi seguenti:

1. L'applicazione client Azure AD viene usata da kubectl per l'accesso degli utenti con il [flusso di concessione dell'autorizzazione del dispositivo OAuth 2,0](../active-directory/develop/v2-oauth2-device-code.md).
2. Azure AD fornisce un access_token, id_token e un refresh_token.
3. L'utente effettua una richiesta a kubectl con un access_token di kubeconfig.
4. Kubectl invia il access_token al server API.
5. Il server API viene configurato con il server webhook di autenticazione per eseguire la convalida.
6. Il server del webhook di autenticazione conferma che la firma del token Web JSON è valida controllando la chiave di firma pubblica Azure AD.
7. L'applicazione server utilizza le credenziali fornite dall'utente per eseguire query sulle appartenenze ai gruppi dell'utente che ha eseguito l'accesso da MS API Graph.
8. Viene inviata una risposta al server API con le informazioni sull'utente, ad esempio l'attestazione del nome dell'entità utente (UPN) del token di accesso e l'appartenenza al gruppo dell'utente in base all'ID oggetto.
9. L'API esegue una decisione di autorizzazione basata sul ruolo/ruolo di Kubernetes.
10. Una volta autorizzato, il server API restituisce una risposta a kubectl.
11. Kubectl fornisce commenti e suggerimenti all'utente.
 
**Informazioni su come integrare AKS con AAD [qui](managed-aad.md).**

## <a name="azure-role-based-access-control-azure-rbac"></a>Controllo degli accessi in base al ruolo di Azure

Il controllo degli accessi in base al ruolo di Azure è un sistema di autorizzazione basato su [Azure Resource Manager](../azure-resource-manager/management/overview.md) che garantisce una gestione con granularità fine degli accessi delle risorse di Azure.

 Il controllo degli accessi in base al ruolo di Azure è progettato per funzionare con le risorse all'interno della sottoscrizione di Azure, mentre Kubernetes RBAC è progettato per funzionare con risorse Kubernetes nel cluster AKS 

Con il controllo degli accessi in base al ruolo di Azure si crea una *definizione del ruolo* che determina le autorizzazioni da applicare. A un utente o a un gruppo viene quindi assegnata questa definizione di ruolo tramite un' *assegnazione di ruolo* per un determinato *ambito*, che può essere una singola risorsa, un gruppo di risorse o nella sottoscrizione.

Per altre informazioni, vedere informazioni [sul controllo degli accessi in base al ruolo di Azure (RBAC di Azure).][azure-rbac]

Per il funzionamento completo di un cluster AKS sono necessari due livelli di accesso: 
1. [Accedere alla risorsa AKS nella sottoscrizione di Azure](#azure-rbac-to-authorize-access-to-the-aks-resource). Questo processo consente di controllare le operazioni di ridimensionamento o di aggiornamento del cluster usando le API AKS, oltre a eseguire il pull dei kubeconfig.
2. Accesso all'API Kubernetes. Questo accesso è controllato dal controllo degli accessi in base al ruolo (tradizionalmente) [Kubernetes](#kubernetes-role-based-access-control-kubernetes-rbac) o dall' [integrazione di Azure RBAC con AKS per l'autorizzazione Kubernetes](#azure-rbac-for-kubernetes-authorization-preview)

### <a name="azure-rbac-to-authorize-access-to-the-aks-resource"></a>Controllo RBAC di Azure per autorizzare l'accesso alla risorsa AKS

Con il controllo degli accessi in base al ruolo di Azure, è possibile fornire agli utenti (o identità) un accesso granulare alle risorse AKS in una o più sottoscrizioni. Ad esempio, è possibile avere il [ruolo di collaboratore del servizio Azure Kubernetes](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-contributor-role) che consente di eseguire azioni come la scalabilità e l'aggiornamento del cluster. Un altro utente potrebbe avere il [ruolo di amministratore del cluster del servizio Azure Kubernetes](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role) che concede solo l'autorizzazione per il pull dell'amministratore kubeconfig.

In alternativa, è possibile assegnare all'utente il ruolo di [collaboratore](../role-based-access-control/built-in-roles.md#contributor) generale, che include le autorizzazioni indicate sopra e tutte le azioni possibili sulla risorsa AKS, ad eccezione della gestione delle autorizzazioni.

Vedere altre informazioni su come usare il controllo degli accessi in base al ruolo di Azure per proteggere l'accesso al file kubeconfig che fornisce l'accesso all'API Kubernetes [qui](control-kubeconfig-access.md).

### <a name="azure-rbac-for-kubernetes-authorization-preview"></a>Controllo RBAC di Azure per l'autorizzazione Kubernetes (anteprima)

Con l'integrazione con controllo degli accessi in base al ruolo di Azure, AKS userà un server webhook di autorizzazione Kubernetes per consentire di gestire le autorizzazioni e le assegnazioni di risorse cluster K8s integrate Azure AD usando la definizione di ruolo e le assegnazioni di ruolo di Azure.

![Controllo degli accessi in base al ruolo di Azure per Kubernetes](media/concepts-identity/azure-rbac-k8s-authz-flow.png)

Come illustrato nel diagramma precedente, quando si usa l'integrazione del controllo degli accessi in base al ruolo di Azure, tutte le richieste all'API Kubernetes seguiranno lo stesso flusso di autenticazione illustrato nella sezione relativa all' [integrazione Azure Active Directory](#azure-active-directory-integration). 

In seguito, invece di basarsi esclusivamente su Kubernetes RBAC per l'autorizzazione, la richiesta verrà effettivamente autorizzata da Azure, purché l'identità che ha effettuato la richiesta sia presente in AAD. Se l'identità non esiste in AAD, ad esempio un account del servizio Kubernetes, il controllo degli accessi in base al ruolo di Azure non viene avviata e sarà il Kubernetes RBAC normale.

In questo scenario è possibile assegnare agli utenti uno dei quattro ruoli predefiniti oppure creare ruoli personalizzati come si farebbe con i ruoli Kubernetes, ma in questo caso usando i meccanismi e le API di Azure RBAC. 

Questa funzionalità consentirà, ad esempio, di concedere agli utenti solo le autorizzazioni per la risorsa AKS tra le sottoscrizioni, ma di configurare e assegnare loro il ruolo e le autorizzazioni che avranno all'interno di ognuno di questi cluster che controlla l'accesso all'API Kubernetes. Ad esempio, è possibile concedere il `Azure Kubernetes Service RBAC Viewer` ruolo nell'ambito della sottoscrizione e il destinatario sarà in grado di elencare e ottenere tutti gli oggetti Kubernetes da tutti i cluster, ma non di modificarli.

> [!IMPORTANT]
> Prima di usare questa funzionalità, è necessario abilitare il controllo degli accessi in base al ruolo di Azure per l'autorizzazione Kubernetes. Per informazioni dettagliate e istruzioni dettagliate, [vedere qui](manage-azure-rbac.md).

#### <a name="built-in-roles"></a>Ruoli predefiniti

AKS fornisce i quattro ruoli predefiniti seguenti. Sono simili ai [ruoli predefiniti di Kubernetes](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#user-facing-roles) , ma con alcune differenze, ad esempio il supporto di CRD. Per l'elenco completo delle azioni consentite da ogni ruolo predefinito, vedere [qui](../role-based-access-control/built-in-roles.md).

| Ruolo                                | Descrizione  |
|-------------------------------------|--------------|
| Visualizzatore RBAC del servizio Kubernetes di Azure  | Consente l'accesso in sola lettura per visualizzare la maggior parte degli oggetti in uno spazio dei nomi. Non consente la visualizzazione di ruoli o associazioni di ruolo. Questo ruolo non consente la visualizzazione `Secrets` perché la lettura del contenuto dei segreti consente l'accesso alle `ServiceAccount` credenziali nello spazio dei nomi, che consente l'accesso all'API come qualsiasi `ServiceAccount` nello spazio dei nomi (un tipo di escalation dei privilegi)  |
| Writer RBAC del servizio Kubernetes di Azure | Consente l'accesso in lettura/scrittura alla maggior parte degli oggetti in uno spazio dei nomi. Questo ruolo non consente la visualizzazione o la modifica di ruoli o associazioni di ruolo. Tuttavia, questo ruolo consente l'accesso `Secrets` e l'esecuzione di pod come qualsiasi ServiceAccount nello spazio dei nomi, quindi può essere usato per ottenere i livelli di accesso all'API di qualsiasi ServiceAccount nello spazio dei nomi. |
| Amministratore RBAC del servizio Kubernetes di Azure  | Consente l'accesso dell'amministratore, che deve essere concesso all'interno di uno spazio dei nomi. Consente l'accesso in lettura/scrittura alla maggior parte delle risorse in uno spazio dei nomi (o ambito del cluster), inclusa la possibilità di creare ruoli e associazioni di ruolo all'interno dello spazio dei nomi. Questo ruolo non consente l'accesso in scrittura alla quota di risorse o allo spazio dei nomi stesso. |
| Amministrazione del cluster RBAC del servizio Kubernetes di Azure  | Consente l'accesso con privilegi avanzati per eseguire qualsiasi azione su qualsiasi risorsa. Fornisce il controllo completo su tutte le risorse nel cluster e in tutti gli spazi dei nomi. |


## <a name="summary"></a>Riepilogo

Questa tabella riepiloga i modi in cui gli utenti possono eseguire l'autenticazione a Kubernetes quando è abilitata l'integrazione Azure AD.  In tutti i casi, la sequenza di comandi dell'utente è:
1. Eseguire `az login` per l'autenticazione in Azure.
1. Eseguire `az aks get-credentials` per scaricare le credenziali del cluster in `.kube/config` .
1. Eseguire `kubectl` i comandi, il primo dei quali può attivare l'autenticazione basata su browser per l'autenticazione nel cluster, come descritto nella tabella seguente.

La concessione di ruolo a cui si fa riferimento nella seconda colonna è la concessione del ruolo **controllo degli accessi** in base al ruolo di Azure visualizzata nella scheda controllo di accesso nel portale di Azure. Il gruppo Amministrazione cluster Azure AD viene visualizzato nella scheda **configurazione** del portale (o con il nome del parametro `--aad-admin-group-object-ids` nell'interfaccia della riga di comando di Azure).

| Descrizione        | Concessione ruolo obbligatoria| Gruppo/i Azure AD amministrazione cluster | Utilizzo |
| -------------------|------------|----------------------------|-------------|
| Accesso amministratore legacy con certificato client| **Ruolo di amministratore di Azure Kubernetes**. Questo ruolo consente `az aks get-credentials` di usare con il `--admin` flag, che Scarica un [certificato di amministrazione del cluster legacy (non Azure ad)](control-kubeconfig-access.md) negli utenti `.kube/config` . Si tratta dell'unico scopo del ruolo di amministratore di Azure Kubernetes.|n/d|Se non si dispone dell'accesso a un gruppo di Azure AD valido con accesso al cluster.| 
| Azure AD con RoleBindings manuale (cluster)| **Ruolo utente di Azure Kubernetes**. Il ruolo "User" consente `az aks get-credentials` di usare senza il `--admin` flag. (Questo è l'unico scopo del "ruolo utente di Azure Kubernetes"). Il risultato, in un cluster abilitato per Azure AD, è il download di [una voce vuota](control-kubeconfig-access.md) in `.kube/config` , che attiva l'autenticazione basata su browser quando viene usata per la prima volta da `kubectl` .| L'utente non si trova in nessuno di questi gruppi. Poiché l'utente non è presente in alcun gruppo di amministratori del cluster, i relativi diritti verranno controllati interamente da qualsiasi RoleBindings o ClusterRoleBindings impostati dagli amministratori del cluster. Il RoleBindings (cluster) [designa Azure ad utenti o gruppi di Azure ad](azure-ad-rbac.md) come `subjects` . Se non sono state configurate associazioni di questo tipo, l'utente non sarà in grado di excute alcun `kubectl` comando.|Se si vuole un controllo di accesso con granularità fine e non si usa il controllo degli accessi in base al ruolo di Azure per l'autorizzazione Kubernetes. Si noti che l'utente che imposta i binding deve accedere da uno degli altri metodi elencati in questa tabella.|
| Azure AD dal membro del gruppo di amministratori| Vedere sopra.|L'utente è un membro di uno dei gruppi elencati di seguito. AKS genera automaticamente un ClusterRoleBinding che associa tutti i gruppi elencati al `cluster-admin` ruolo Kubernetes. In modo che gli utenti di questi gruppi possano eseguire tutti i `kubectl` comandi come `cluster-admin` .|Se si desidera concedere agli utenti i diritti di amministratore completi e _non_ si usa il controllo degli accessi in base al ruolo di Azure per l'autorizzazione Kubernetes.|
| Azure AD con l'autorizzazione RBAC di Azure per Kubernetes|Due ruoli: primo, **ruolo utente Azure Kubernetes** (come sopra). In secondo luogo, uno dei "servizi **RBAC** di Azure Kubernetes..." ruoli elencati sopra o un'alternativa personalizzata.|Il campo ruoli di amministratore nella scheda configurazione è irrilevante quando è abilitata l'autorizzazione RBAC di Azure per Kubernetes.|Si usa RBAC di Azure per l'autorizzazione Kubernetes. Questo approccio offre un controllo con granularità fine, senza la necessità di configurare RoleBindings o ClusterRoleBindings.|

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare a usare Azure AD e il controllo degli accessi in base al ruolo di Kubernetes, vedere [Integrare Azure Active Directory con il servizio Azure Kubernetes][aks-aad].
- Per le procedure consigliate associate, vedere procedure consigliate [per l'autenticazione e l'autorizzazione in AKS][operator-best-practices-identity].
- Per iniziare a usare il controllo degli accessi in base al ruolo di Azure per l'autorizzazione Kubernetes, vedere [usare il controllo degli accessi](manage-azure-rbac.md)in base al ruolo di Azure
- Per iniziare a proteggere il file kubeconfig, vedere [limitare l'accesso al file di configurazione del cluster](control-kubeconfig-access.md)

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
