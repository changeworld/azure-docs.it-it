---
title: Risoluzione dei problemi comuni
description: Informazioni su come risolvere i problemi relativi alla creazione di definizioni di criteri, ai vari SDK e al componente aggiuntivo per Kubernetes.
ms.date: 01/26/2021
ms.topic: troubleshooting
ms.openlocfilehash: 6e0e4067f07266bae9c87fd4443d27314cc28c0b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100592604"
---
# <a name="troubleshoot-errors-with-using-azure-policy"></a>Risolvere gli errori con l'uso di criteri di Azure

Quando si creano definizioni di criteri, si usano gli SDK o si configura il componente aggiuntivo [criteri di Azure per Kubernetes](../concepts/policy-for-kubernetes.md) , è possibile che si verifichino errori. Questo articolo descrive vari errori generali che possono verificarsi e suggerisce modi per risolverli.

## <a name="find-error-details"></a>Trova Dettagli errore

Il percorso dei dettagli dell'errore dipende dall'aspetto dei criteri di Azure con cui si sta lavorando.

- Se si usano criteri personalizzati, passare alla portale di Azure per ottenere commenti e suggerimenti sullo schema o esaminare i [dati di conformità](../how-to/get-compliance-data.md) risultanti per vedere come sono state valutate le risorse.
- Se si lavora con uno dei vari SDK, l'SDK fornisce informazioni dettagliate sul motivo per cui la funzione non è riuscita.
- Se si utilizza il componente aggiuntivo per Kubernetes, iniziare con la [registrazione](../concepts/policy-for-kubernetes.md#logging) nel cluster.

## <a name="general-errors"></a>Errori generali

### <a name="scenario-alias-not-found"></a>Scenario: alias non trovato

#### <a name="issue"></a>Problema

Un alias errato o inesistente viene utilizzato in una definizione di criteri. Criteri di Azure usa [alias](../concepts/definition-structure.md#aliases) per eseguire il mapping alle proprietà Azure Resource Manager.

#### <a name="cause"></a>Causa

Un alias errato o inesistente viene utilizzato in una definizione di criteri.

#### <a name="resolution"></a>Soluzione

Prima di tutto, verificare che la proprietà Gestione risorse disponga di un alias. Per cercare gli alias disponibili, passare all'estensione di [criteri di Azure per Visual Studio Code](../how-to/extension-for-vscode.md) o SDK.
Se l'alias di una proprietà Gestione risorse non esiste, creare un ticket di supporto.

### <a name="scenario-evaluation-details-arent-up-to-date"></a>Scenario: i dettagli della valutazione non sono aggiornati

#### <a name="issue"></a>Problema

Una risorsa è nello stato _non avviato_ o i dettagli di conformità non sono aggiornati.

#### <a name="cause"></a>Causa

Per applicare un nuovo criterio o un'assegnazione di iniziativa sono necessari circa 30 minuti. Le risorse nuove o aggiornate nell'ambito di un'assegnazione esistente diventano disponibili in circa 15 minuti. Un'analisi di conformità standard viene eseguita ogni 24 ore. Per altre informazioni, vedere [trigger di valutazione](../how-to/get-compliance-data.md#evaluation-triggers).

#### <a name="resolution"></a>Soluzione

Prima di tutto, attendere un periodo di tempo appropriato per il completamento di una valutazione e che i risultati di conformità diventino disponibili nell'portale di Azure o nell'SDK. Per avviare una nuova analisi di valutazione con Azure PowerShell o l'API REST, vedere [analisi di valutazione su richiesta](../how-to/get-compliance-data.md#on-demand-evaluation-scan).

### <a name="scenario-compliance-isnt-as-expected"></a>Scenario: la conformità non è quella prevista

#### <a name="issue"></a>Problema

Una risorsa non è nello stato di valutazione _conforme_ o _non conforme_ previsto per la risorsa.

#### <a name="cause"></a>Causa

La risorsa non è nell'ambito corretto per l'assegnazione di criteri oppure la definizione dei criteri non funziona come previsto.

#### <a name="resolution"></a>Soluzione

Per risolvere i problemi della definizione dei criteri, eseguire le operazioni seguenti:

1. Per prima cosa, attendere il completamento della valutazione e ottenere risultati di conformità in portale di Azure o SDK. 

1. Per avviare una nuova analisi di valutazione con Azure PowerShell o l'API REST, vedere [analisi di valutazione su richiesta](../how-to/get-compliance-data.md#on-demand-evaluation-scan).
1. Verificare che i parametri di assegnazione e l'ambito di assegnazione siano impostati correttamente.
1. Controllare la [modalità di definizione dei criteri](../concepts/definition-structure.md#mode):
   - La modalità deve essere `all` per tutti i tipi di risorsa.
   - La modalità deve essere `indexed` se la definizione dei criteri controlla i tag o il percorso.
1. Verificare che l'ambito della risorsa non sia [escluso](../concepts/assignment-structure.md#excluded-scopes) o [esentato](../concepts/exemption-structure.md).
1. Se la conformità per un'assegnazione di criteri Mostra `0/0` risorse, non è stata determinata alcuna risorsa da applicare nell'ambito dell'assegnazione. Controllare sia la definizione dei criteri che l'ambito di assegnazione.
1. Per una risorsa non conforme che dovrebbe essere conforme, vedere [determinare i motivi](../how-to/determine-non-compliance.md)della mancata conformità. Il confronto tra la definizione e il valore della proprietà valutato indica il motivo per cui una risorsa non è conforme.
   - Se il **valore di destinazione** è errato, rivedere la definizione dei criteri.
   - Se il **valore corrente** è errato, convalidare il payload della risorsa tramite `resources.azure.com` .
1. Per altri problemi comuni e soluzioni, vedere [risolvere i problemi: imposizione non come previsto](#scenario-enforcement-not-as-expected).

Se si riscontra ancora un problema con la definizione di criteri predefinita duplicata e personalizzata, creare un ticket di supporto in creazione di **un criterio** per instradare correttamente il problema.

### <a name="scenario-enforcement-not-as-expected"></a>Scenario: imposizione non come previsto

#### <a name="issue"></a>Problema

Una risorsa su cui si prevede che i criteri di Azure agisca non viene eseguita e non è presente alcuna voce nel [log attività di Azure](../../../azure-monitor/essentials/platform-logs-overview.md).

#### <a name="cause"></a>Causa

L'assegnazione dei criteri è stata configurata per un'impostazione [**EnforcementMode**](../concepts/assignment-structure.md#enforcement-mode) _disabilitata_.
Mentre **enforcementMode** è disabilitato, l'effetto del criterio non viene applicato e non è presente alcuna voce nel log attività.

#### <a name="resolution"></a>Soluzione

Risolvere i problemi relativi all'imposizione dell'assegnazione di criteri eseguendo le operazioni seguenti:

1. In primo luogo, attendere il completamento di una valutazione e ottenere risultati di conformità nel portale di Azure o nell'SDK. 

1. Per avviare una nuova analisi di valutazione con Azure PowerShell o l'API REST, vedere [analisi di valutazione su richiesta](../how-to/get-compliance-data.md#on-demand-evaluation-scan).
1. Verificare che i parametri di assegnazione e l'ambito di assegnazione siano impostati correttamente e che **enforcementMode** sia _abilitato_.
1. Controllare la [modalità di definizione dei criteri](../concepts/definition-structure.md#mode):
   - La modalità deve essere `all` per tutti i tipi di risorsa.
   - La modalità deve essere `indexed` se la definizione dei criteri controlla i tag o il percorso.
1. Verificare che l'ambito della risorsa non sia [escluso](../concepts/assignment-structure.md#excluded-scopes) o [esentato](../concepts/exemption-structure.md).
1. Verificare che il payload della risorsa corrisponda alla logica dei criteri. Questa operazione può essere eseguita [acquisendo una traccia di archivio http (Har)](../../../azure-portal/capture-browser-trace.md) o esaminando le proprietà del modello di Azure Resource Manager (modello ARM).
1. Per altri problemi comuni e soluzioni, vedere la pagina relativa alla [risoluzione dei problemi: conformità non come previsto](#scenario-compliance-isnt-as-expected).

Se si riscontra ancora un problema con la definizione di criteri predefinita duplicata e personalizzata, creare un ticket di supporto in creazione di **un criterio** per instradare correttamente il problema.

### <a name="scenario-denied-by-azure-policy"></a>Scenario: negato da criteri di Azure

#### <a name="issue"></a>Problema

La creazione o l'aggiornamento di una risorsa è stato negato.

#### <a name="cause"></a>Causa

Un'assegnazione di criteri all'ambito della risorsa nuova o aggiornata soddisfa i criteri di una definizione di criteri con un effetto di [negazione](../concepts/effects.md#deny) . Impossibile creare o aggiornare le risorse che soddisfano queste definizioni.

#### <a name="resolution"></a>Soluzione

Il messaggio di errore di un'assegnazione di criteri Deny include gli ID di definizione dei criteri e di assegnazione dei criteri. Se le informazioni sull'errore nel messaggio vengono perse, sono disponibili anche nel [log attività](../../../azure-monitor/essentials/activity-log.md#view-the-activity-log). Usare queste informazioni per ottenere altri dettagli per comprendere le restrizioni delle risorse e modificare le proprietà delle risorse nella richiesta in modo che corrispondano ai valori consentiti.

## <a name="template-errors"></a>Errori del modello

### <a name="scenario-policy-supported-functions-processed-by-template"></a>Scenario: funzioni supportate dal criterio elaborate dal modello

#### <a name="issue"></a>Problema

Criteri di Azure supporta alcune funzioni e funzioni di modello ARM disponibili solo in una definizione di criteri. Gestione risorse elabora queste funzioni come parte di una distribuzione anziché come parte di una definizione dei criteri.

#### <a name="cause"></a>Causa

L'uso di funzioni supportate, ad esempio `parameter()` o `resourceGroup()` , comporta il risultato elaborato della funzione in fase di distribuzione anziché consentire l'elaborazione della funzione per la definizione dei criteri e il motore di criteri di Azure.

#### <a name="resolution"></a>Soluzione

Per passare una funzione tramite come parte di una definizione dei criteri, eseguire l'escape dell'intera stringa con `[` tale che la proprietà ha un aspetto simile a `[[resourceGroup().tags.myTag]` . Il carattere di escape fa in modo che Gestione risorse considerino il valore come stringa quando elabora il modello. Criteri di Azure posiziona quindi la funzione nella definizione dei criteri, che consente di renderla dinamica come previsto. Per altre informazioni, vedere [sintassi ed espressioni nei modelli Azure Resource Manager](../../../azure-resource-manager/templates/template-expressions.md).

## <a name="add-on-for-kubernetes-installation-errors"></a>Componente aggiuntivo per errori di installazione di Kubernetes

### <a name="scenario-installation-by-using-a-helm-chart-fails-because-of-a-password-error"></a>Scenario: l'installazione tramite un grafico Helm non riesce a causa di un errore di password

#### <a name="issue"></a>Problema

Il `helm install azure-policy-addon` comando ha esito negativo e restituisce uno degli errori seguenti:

- `!: event not found`
- `Error: failed parsing --set data: key "<key>" has no value (cannot end with ,)`

#### <a name="cause"></a>Causa

La password generata include una virgola ( `,` ) su cui si divide il grafico Helm.

#### <a name="resolution"></a>Soluzione

Quando si esegue `helm install azure-policy-addon` , eseguire l'escape della virgola ( `,` ) nel valore della password con una barra rovesciata ( `\` ).

### <a name="scenario-installation-by-using-a-helm-chart-fails-because-the-name-already-exists"></a>Scenario: l'installazione tramite un grafico Helm ha esito negativo perché il nome esiste già

#### <a name="issue"></a>Problema

Il `helm install azure-policy-addon` comando ha esito negativo e restituisce l'errore seguente:

- `Error: cannot re-use a name that is still in use`

#### <a name="cause"></a>Causa

Il grafico Helm con il nome `azure-policy-addon` è già stato installato o parzialmente installato.

#### <a name="resolution"></a>Soluzione

Seguire le istruzioni per [rimuovere il componente aggiuntivo criteri di Azure per Kubernetes](../concepts/policy-for-kubernetes.md#remove-the-add-on), quindi eseguire di nuovo il `helm install azure-policy-addon` comando.

### <a name="scenario-azure-virtual-machine-user-assigned-identities-are-replaced-by-system-assigned-managed-identities"></a>Scenario: le identità assegnate dall'utente di macchine virtuali di Azure vengono sostituite dalle identità gestite assegnate dal sistema

#### <a name="issue"></a>Problema

Dopo aver assegnato le iniziative dei criteri di configurazione Guest alle impostazioni di controllo all'interno di un computer, le identità gestite assegnate dall'utente assegnate al computer non vengono più assegnate. Viene assegnata solo un'identità gestita assegnata dal sistema.

#### <a name="cause"></a>Causa

Le definizioni dei criteri usate in precedenza nelle definizioni DeployIfNotExists di configurazione Guest hanno garantito che un'identità assegnata dal sistema venga assegnata al computer, ma hanno anche rimosso le assegnazioni di identità assegnate dall'utente.

#### <a name="resolution"></a>Soluzione

Le definizioni che in precedenza hanno causato il problema vengono visualizzate come _\[ deprecate \]_ e vengono sostituite dalle definizioni dei criteri che gestiscono i prerequisiti senza rimuovere le identità gestite assegnate dall'utente. È necessario eseguire un passaggio manuale. Eliminare le assegnazioni di criteri esistenti contrassegnate come _\[ deprecate \]_ e sostituirle con l'iniziativa dei criteri e le definizioni dei criteri dei prerequisiti aggiornati con lo stesso nome dell'originale.

Per una descrizione dettagliata, vedere il post di Blog [relativo alla modifica importante rilasciata per i criteri di controllo della configurazione Guest](https://techcommunity.microsoft.com/t5/azure-governance-and-management/important-change-released-for-guest-configuration-audit-policies/ba-p/1655316).

## <a name="add-on-for-kubernetes-general-errors"></a>Errori generali del componente aggiuntivo per Kubernetes

### <a name="scenario-the-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-because-of-egress-restrictions"></a>Scenario: il componente aggiuntivo non riesce a raggiungere l'endpoint del servizio criteri di Azure a causa di restrizioni in uscita

#### <a name="issue"></a>Problema

Il componente aggiuntivo non è in grado di raggiungere l'endpoint del servizio criteri di Azure e restituisce uno degli errori seguenti:

- `failed to fetch token, service not reachable`
- `Error getting file "Get https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/template.yaml: dial tcp 151.101.228.133.443: connect: connection refused`

#### <a name="cause"></a>Causa

Questo problema si verifica quando un cluster in uscita è bloccato.

#### <a name="resolution"></a>Soluzione

Assicurarsi che i domini e le porte indicati negli articoli seguenti siano aperti:

- [Regole di rete in uscita obbligatorie e nomi di dominio completi (FQDN) per i cluster AKS](../../../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
- [Installare il componente aggiuntivo criteri di Azure per Kubernetes abilitato per Azure (anteprima)](../concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)

### <a name="scenario-the-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-because-of-the-aad-pod-identity-configuration"></a>Scenario: il componente aggiuntivo non riesce a raggiungere l'endpoint del servizio criteri di Azure a causa della configurazione di AAD-Pod-Identity

#### <a name="issue"></a>Problema

Il componente aggiuntivo non è in grado di raggiungere l'endpoint del servizio criteri di Azure e restituisce uno degli errori seguenti:

- `azure.BearerAuthorizer#WithAuthorization: Failed to refresh the Token for request to https://gov-prod-policy-data.trafficmanager.net/checkDataPolicyCompliance?api-version=2019-01-01-preview: StatusCode=404`
- `adal: Refresh request failed. Status Code = '404'. Response body: getting assigned identities for pod kube-system/azure-policy-8c785548f-r882p in CREATED state failed after 16 attempts, retry duration [5]s, error: <nil>`

#### <a name="cause"></a>Causa

Questo errore si verifica quando si installa _Add-Pod-Identity_ nel cluster e i pod del _sistema Kube_ non sono esclusi in _AAD-Pod-Identity_.

I pod dell'identità gestita del nodo del componente _AAD-Pod-Identity_ consentono di modificare i iptables dei nodi per intercettare le chiamate all'endpoint dei metadati dell'istanza di Azure. Questa configurazione significa che tutte le richieste effettuate all'endpoint dei metadati vengono intercettate da NMI, anche se il Pod non usa _AAD-Pod-Identity_.
Il CustomResourceDefinition _AzurePodIdentityException_ (CRD) può essere configurato per informare _AAD-Pod-Identity_ che qualsiasi richiesta a un endpoint di metadati originato da un pod corrispondente alle etichette definite nel CRD deve essere inoltrata senza elaborazione in NMI.

#### <a name="resolution"></a>Soluzione

Escludere i pod di sistema che hanno l' `kubernetes.azure.com/managedby: aks` etichetta nello spazio dei nomi _Kube-System_ in _AAD-Pod-Identity_ configurando la _AzurePodIdentityException_ CRD.

Per altre informazioni, vedere [disabilitare l'identità pod Azure Active Directory (Azure ad) per un pod/applicazione specifico](https://azure.github.io/aad-pod-identity/docs/configure/application_exception).

Per configurare un'eccezione, seguire questo esempio:

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: mic-exception
  namespace: default
spec:
  podLabels:
    app: mic
    component: mic
---
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: aks-addon-exception
  namespace: kube-system
spec:
  podLabels:
    kubernetes.azure.com/managedby: aks
```

### <a name="scenario-the-resource-provider-isnt-registered"></a>Scenario: il provider di risorse non è registrato

#### <a name="issue"></a>Problema

Il componente aggiuntivo può raggiungere l'endpoint del servizio criteri di Azure, ma nei log del componente aggiuntivo viene visualizzato uno degli errori seguenti:

- `The resource provider 'Microsoft.PolicyInsights' is not registered in subscription '{subId}'. See
  https://aka.ms/policy-register-subscription for how to register subscriptions.`

- `policyinsightsdataplane.BaseClient#CheckDataPolicyCompliance: Failure responding to request:
  StatusCode=500 -- Original Error: autorest/azure: Service returned an error. Status=500
  Code="InternalServerError" Message="Encountered an internal server error.`

#### <a name="cause"></a>Causa

Il provider di risorse ' Microsoft. PolicyInsights ' non è registrato. Deve essere registrato per il componente aggiuntivo per ottenere le definizioni dei criteri e restituire i dati di conformità.

#### <a name="resolution"></a>Soluzione

Registrare il provider di risorse ' Microsoft. PolicyInsights ' nella sottoscrizione del cluster. Per istruzioni, vedere [registrare un provider di risorse](../../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider).

### <a name="scenario-the-subscription-is-disabled"></a>Scenario: la sottoscrizione è disabilitata

#### <a name="issue"></a>Problema

Il componente aggiuntivo può raggiungere l'endpoint del servizio criteri di Azure, ma viene visualizzato l'errore seguente:

`The subscription '{subId}' has been disabled for azure data-plane policy. Please contact support.`

#### <a name="cause"></a>Causa

Questo errore indica che la sottoscrizione è stata determinata come problematica e che `Microsoft.PolicyInsights/DataPlaneBlocked` è stato aggiunto il flag funzionalità per bloccare la sottoscrizione.

#### <a name="resolution"></a>Soluzione

Per esaminare e risolvere questo problema, [contattare il team di funzionalità](mailto:azuredg@microsoft.com).

## <a name="next-steps"></a>Passaggi successivi

Se il problema non è elencato in questo articolo o non è possibile risolverlo, ottenere assistenza visitando uno dei canali seguenti:

- Ottieni risposte dagli esperti tramite [Microsoft Q&A](/answers/topics/azure-policy.html).
- Collegarsi a [@AzureSupport](https://twitter.com/azuresupport), Questa risorsa Microsoft Azure ufficiale su Twitter aiuta a migliorare l'esperienza del cliente connettendo la community di Azure alle risposte, al supporto e agli esperti corretti.
- Se è ancora necessaria assistenza, accedere al [sito del supporto tecnico di Azure](https://azure.microsoft.com/support/options/) e selezionare **Invia una richiesta di supporto**.
