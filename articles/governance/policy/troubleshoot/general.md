---
title: Risoluzione dei problemi comuni
description: Informazioni su come risolvere i problemi relativi alla creazione di definizioni di criteri, ai vari SDK e al componente aggiuntivo per Kubernetes.
ms.date: 04/19/2021
ms.topic: troubleshooting
ms.openlocfilehash: c4feae11c6d8d78a43bae9882405e292a18e90bd
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725063"
---
# <a name="troubleshoot-errors-with-using-azure-policy"></a>Risolvere gli errori relativi all'uso Criteri di Azure

Quando si creano definizioni di criteri, si lavora con gli SDK o si configura il componente aggiuntivo [Criteri di Azure per Kubernetes,](../concepts/policy-for-kubernetes.md) si potrebbero verificarsi errori. Questo articolo descrive vari errori generali che potrebbero verificarsi e suggerisce come risolverli.

## <a name="find-error-details"></a>Trovare i dettagli dell'errore

La posizione dei dettagli dell'errore dipende dall'aspetto Criteri di Azure si sta lavorando.

- Se si lavora con criteri personalizzati, passare al portale di Azure per ottenere commenti e suggerimenti [](../how-to/get-compliance-data.md) sullo schema oppure esaminare i dati di conformità risultanti per vedere come sono state valutate le risorse.
- Se si lavora con uno dei vari SDK, l'SDK fornisce informazioni dettagliate sul motivo per cui la funzione ha avuto esito negativo.
- Se si sta lavorando con il componente aggiuntivo per Kubernetes, iniziare con la [registrazione](../concepts/policy-for-kubernetes.md#logging) nel cluster.

## <a name="general-errors"></a>Errori generali

### <a name="scenario-alias-not-found"></a>Scenario: Alias non trovato

#### <a name="issue"></a>Problema

In una definizione di criteri viene usato un alias non corretto o inesistente. Criteri di Azure usa [gli alias per](../concepts/definition-structure.md#aliases) eseguire il mapping Azure Resource Manager proprietà.

#### <a name="cause"></a>Causa

In una definizione di criteri viene usato un alias non corretto o inesistente.

#### <a name="resolution"></a>Soluzione

Verificare innanzitutto che la proprietà Resource Manager abbia un alias. Per cercare gli alias disponibili, passare all'estensione Criteri di Azure [per Visual Studio Code](../how-to/extension-for-vscode.md) o l'SDK.
Se l'alias per Resource Manager proprietà non esiste, creare un ticket di supporto.

### <a name="scenario-evaluation-details-arent-up-to-date"></a>Scenario: i dettagli della valutazione non sono aggiornati

#### <a name="issue"></a>Problema

Una risorsa è nello _stato Non_ avviato o i dettagli di conformità non sono correnti.

#### <a name="cause"></a>Causa

L'applicazione di un nuovo criterio o di un'assegnazione di iniziativa richiede circa 30 minuti. Le risorse nuove o aggiornate nell'ambito di un'assegnazione esistente diventano disponibili in circa 15 minuti. Un'analisi di conformità standard viene eseguita ogni 24 ore. Per altre informazioni, vedere [Trigger di valutazione.](../how-to/get-compliance-data.md#evaluation-triggers)

#### <a name="resolution"></a>Soluzione

Prima di tutto, attendere il completamento di una valutazione e la disponibilità dei risultati di conformità nel portale di Azure o nell'SDK. Per avviare una nuova analisi di valutazione con Azure PowerShell o l'API REST, vedere [Analisi di valutazione su richiesta.](../how-to/get-compliance-data.md#on-demand-evaluation-scan)

### <a name="scenario-compliance-isnt-as-expected"></a>Scenario: la conformità non è come previsto

#### <a name="issue"></a>Problema

Una risorsa non è nello stato di _valutazione_ _Conforme_ o Non conforme previsto per la risorsa.

#### <a name="cause"></a>Causa

La risorsa non è nell'ambito corretto per l'assegnazione dei criteri o la definizione dei criteri non funziona come previsto.

#### <a name="resolution"></a>Soluzione

Per risolvere i problemi relativi alla definizione dei criteri, eseguire le operazioni seguenti:

1. Prima di tutto, attendere il periodo di tempo appropriato per il completamento di una valutazione e la disponibilità dei risultati di conformità in portale di Azure o SDK. 

1. Per avviare una nuova analisi di valutazione con Azure PowerShell o l'API REST, vedere [Analisi di valutazione su richiesta.](../how-to/get-compliance-data.md#on-demand-evaluation-scan)
1. Assicurarsi che i parametri di assegnazione e l'ambito di assegnazione siano impostati correttamente.
1. Controllare la [modalità di definizione dei criteri](../concepts/definition-structure.md#mode):
   - La modalità deve essere per `all` tutti i tipi di risorse.
   - La modalità deve essere se `indexed` la definizione dei criteri verifica la presenza di tag o posizione.
1. Assicurarsi che l'ambito della risorsa non sia [escluso o](../concepts/assignment-structure.md#excluded-scopes) [esente.](../concepts/exemption-structure.md)
1. Se la conformità per un'assegnazione di criteri mostra risorse, nessuna risorsa è stata determinata come `0/0` applicabile all'interno dell'ambito di assegnazione. Controllare sia la definizione dei criteri che l'ambito di assegnazione.
1. Per una risorsa non conforme che dovrebbe essere conforme, vedere determinare [i motivi della non conformità.](../how-to/determine-non-compliance.md) Il confronto tra la definizione e il valore della proprietà valutata indica il motivo per cui una risorsa non era conforme.
   - Se il **valore di destinazione** è errato, rivedere la definizione dei criteri.
   - Se il **valore corrente è** errato, convalidare il payload della risorsa tramite `resources.azure.com` .
1. Per altri problemi e soluzioni comuni, vedere [Risoluzione dei problemi: Imposizione non come previsto.](#scenario-enforcement-not-as-expected)

Se si verifica ancora un problema con la definizione di criteri predefiniti duplicata e personalizzata o la definizione personalizzata, creare un ticket di supporto in **Creazione** di criteri per indirizzare correttamente il problema.

### <a name="scenario-enforcement-not-as-expected"></a>Scenario: imposizione non come previsto

#### <a name="issue"></a>Problema

Una risorsa su cui Criteri di Azure agire non viene agita e non è presente alcuna voce nel [log attività di Azure.](../../../azure-monitor/essentials/platform-logs-overview.md)

#### <a name="cause"></a>Causa

L'assegnazione di criteri è stata configurata per [**un'impostazione enforcementMode**](../concepts/assignment-structure.md#enforcement-mode) _disabilitata._
Mentre **enforcementMode** è disabilitato, l'effetto dei criteri non viene applicato e non è presente alcuna voce nel log attività.

#### <a name="resolution"></a>Soluzione

Risolvere i problemi di imposizione dell'assegnazione di criteri eseguendo le operazioni seguenti:

1. Prima di tutto, attendere il periodo di tempo appropriato per il completamento di una valutazione e la disponibilità dei risultati di conformità nel portale di Azure o nell'SDK. 

1. Per avviare una nuova analisi di valutazione Azure PowerShell o l'API REST, vedere [Analisi di valutazione su richiesta](../how-to/get-compliance-data.md#on-demand-evaluation-scan).
1. Assicurarsi che i parametri di assegnazione e l'ambito di assegnazione siano impostati correttamente e che **enforcementMode sia** _Abilitato_.
1. Controllare la [modalità di definizione dei criteri](../concepts/definition-structure.md#mode):
   - La modalità deve essere per `all` tutti i tipi di risorse.
   - La modalità deve essere se `indexed` la definizione dei criteri verifica la presenza di tag o percorso.
1. Assicurarsi che l'ambito della risorsa non sia [escluso](../concepts/assignment-structure.md#excluded-scopes) o [esente.](../concepts/exemption-structure.md)
1. Verificare che il payload della risorsa corrisponda alla logica dei criteri. Questa operazione può essere eseguita [acquisendo una traccia HTTP Archive (HAR)](../../../azure-portal/capture-browser-trace.md) o esaminando le proprietà del modello Azure Resource Manager (modello ARM).
1. Per altri problemi e soluzioni comuni, vedere [Risoluzione dei problemi: Conformità non come previsto.](#scenario-compliance-isnt-as-expected)

Se si verifica ancora un problema con la definizione di criteri predefiniti duplicata e personalizzata o la definizione personalizzata, creare un ticket di supporto in **Creazione** di criteri per indirizzare correttamente il problema.

### <a name="scenario-denied-by-azure-policy"></a>Scenario: Negato da Criteri di Azure

#### <a name="issue"></a>Problema

La creazione o l'aggiornamento di una risorsa viene negato.

#### <a name="cause"></a>Causa

Un'assegnazione di criteri all'ambito della risorsa nuova o aggiornata soddisfa i criteri di una definizione di criteri con un [effetto Nega.](../concepts/effects.md#deny) Le risorse che soddisfano queste definizioni non possono essere create o aggiornate.

#### <a name="resolution"></a>Soluzione

Il messaggio di errore da un'assegnazione di criteri di rifiuto include la definizione dei criteri e gli ID di assegnazione dei criteri. Se le informazioni sull'errore nel messaggio non vengono visualizzate, sono disponibili anche nel [log attività](../../../azure-monitor/essentials/activity-log.md#view-the-activity-log). Usare queste informazioni per ottenere altri dettagli per comprendere le restrizioni delle risorse e modificare le proprietà delle risorse nella richiesta in modo che corrispondano ai valori consentiti.

### <a name="scenario-definition-targets-multiple-resource-types"></a>Scenario: la definizione è destinata a più tipi di risorse

#### <a name="issue"></a>Problema

Una definizione di criteri che include più tipi di risorse non supera la convalida durante la creazione o l'aggiornamento con l'errore seguente:

```error
The policy definition '{0}' targets multiple resource types, but the policy rule is authored in a way that makes the policy not applicable to the target resource types '{1}'.
```

#### <a name="cause"></a>Causa

La regola di definizione dei criteri ha una o più condizioni che non vengono valutate dai tipi di risorse di destinazione.

#### <a name="resolution"></a>Soluzione

Se viene usato un alias, assicurarsi che l'alias venga valutato rispetto solo al tipo di risorsa a cui appartiene aggiungendo una condizione del tipo prima di esso. Un'alternativa consiste nel suddividere la definizione dei criteri in più definizioni per evitare di avere come destinazione più tipi di risorse.

## <a name="template-errors"></a>Errori del modello

### <a name="scenario-policy-supported-functions-processed-by-template"></a>Scenario: funzioni supportate da criteri elaborate dal modello

#### <a name="issue"></a>Problema

Criteri di Azure supporta una serie di funzioni e funzioni del modello arm disponibili solo in una definizione di criteri. Resource Manager elabora queste funzioni come parte di una distribuzione anziché come parte di una definizione di criteri.

#### <a name="cause"></a>Causa

L'uso di funzioni supportate, ad esempio o , comporta l'elaborazione del risultato della funzione in fase di distribuzione anziché consentire l'elaborazione della funzione per la definizione dei criteri e Criteri di Azure `parameter()` `resourceGroup()` motore di elaborazione.

#### <a name="resolution"></a>Soluzione

Per passare una funzione tramite come parte di una definizione di criteri, eseguire l'escape dell'intera stringa con in modo che `[` la proprietà sia simile a `[[resourceGroup().tags.myTag]` . Il carattere di escape Resource Manager considera il valore come stringa quando elabora il modello. Criteri di Azure quindi inserisce la funzione nella definizione dei criteri, che consente di essere dinamica come previsto. Per altre informazioni, vedere [Sintassi ed espressioni in Azure Resource Manager modelli.](../../../azure-resource-manager/templates/template-expressions.md)

## <a name="add-on-for-kubernetes-installation-errors"></a>Errori di installazione del componente aggiuntivo per Kubernetes

### <a name="scenario-installation-by-using-a-helm-chart-fails-because-of-a-password-error"></a>Scenario: l'installazione tramite un grafico Helm non riesce a causa di un errore di password

#### <a name="issue"></a>Problema

Il `helm install azure-policy-addon` comando ha esito negativo e restituisce uno degli errori seguenti:

- `!: event not found`
- `Error: failed parsing --set data: key "<key>" has no value (cannot end with ,)`

#### <a name="cause"></a>Causa

La password generata include una virgola ( `,` ), in cui il grafico Helm viene suddiviso.

#### <a name="resolution"></a>Soluzione

Quando si esegue `helm install azure-policy-addon` , eseguire l'escape della virgola ( ) nel valore della password con una barra `,` rovesciata ( `\` ).

### <a name="scenario-installation-by-using-a-helm-chart-fails-because-the-name-already-exists"></a>Scenario: l'installazione tramite un grafico Helm non riesce perché il nome esiste già

#### <a name="issue"></a>Problema

Il `helm install azure-policy-addon` comando ha esito negativo e restituisce l'errore seguente:

- `Error: cannot re-use a name that is still in use`

#### <a name="cause"></a>Causa

Helm Chart con il nome `azure-policy-addon` è già stato installato o parzialmente installato.

#### <a name="resolution"></a>Soluzione

Seguire le istruzioni per rimuovere il Criteri di Azure per il componente aggiuntivo [Kubernetes,](../concepts/policy-for-kubernetes.md#remove-the-add-on)quindi eseguire di nuovo il `helm install azure-policy-addon` comando.

### <a name="scenario-azure-virtual-machine-user-assigned-identities-are-replaced-by-system-assigned-managed-identities"></a>Scenario: le identità assegnate dall'utente delle macchine virtuali di Azure vengono sostituite da identità gestite assegnate dal sistema

#### <a name="issue"></a>Problema

Dopo aver assegnato le iniziative dei criteri di configurazione guest per controllare le impostazioni all'interno di un computer, le identità gestite assegnate dall'utente assegnate al computer non vengono più assegnate. Viene assegnata solo un'identità gestita assegnata dal sistema.

#### <a name="cause"></a>Causa

Le definizioni dei criteri usate in precedenza nelle definizioni DeployIfNotExists della configurazione guest assicuravano che al computer fosse assegnata un'identità assegnata dal sistema, ma rimosse anche le assegnazioni di identità assegnate dall'utente.

#### <a name="resolution"></a>Soluzione

Le definizioni che in precedenza causavano questo problema vengono visualizzate come _\[ Deprecate \]_ e vengono sostituite dalle definizioni dei criteri che gestiscono i prerequisiti senza rimuovere le identità gestite assegnate dall'utente. È necessario un passaggio manuale. Eliminare tutte le assegnazioni di criteri esistenti contrassegnate come _\[ Deprecate \]_ e sostituirle con l'iniziativa dei criteri dei prerequisiti aggiornata e le definizioni dei criteri con lo stesso nome dell'originale.

Per una descrizione dettagliata, vedere il post di blog [Modifica importante rilasciata per i criteri di controllo della configurazione guest](https://techcommunity.microsoft.com/t5/azure-governance-and-management/important-change-released-for-guest-configuration-audit-policies/ba-p/1655316).

## <a name="add-on-for-kubernetes-general-errors"></a>Errori generali del componente aggiuntivo per Kubernetes

### <a name="scenario-the-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-because-of-egress-restrictions"></a>Scenario: il componente aggiuntivo non riesce a raggiungere l'endpoint Criteri di Azure servizio a causa di restrizioni in uscita

#### <a name="issue"></a>Problema

Il componente aggiuntivo non può raggiungere l'endpoint Criteri di Azure servizio e restituisce uno degli errori seguenti:

- `failed to fetch token, service not reachable`
- `Error getting file "Get https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/template.yaml: dial tcp 151.101.228.133.443: connect: connection refused`

#### <a name="cause"></a>Causa

Questo problema si verifica quando un cluster in uscita viene bloccato.

#### <a name="resolution"></a>Soluzione

Assicurarsi che i domini e le porte indicati negli articoli seguenti siano aperti:

- [Regole di rete in uscita obbligatorie e nomi di dominio completi (FQDN) per i cluster del servizio Active Directory](../../../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
- [Installare il Criteri di Azure per Kubernetes Azure Arc abilitato per l'installazione (anteprima)](../concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)

### <a name="scenario-the-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-because-of-the-aad-pod-identity-configuration"></a>Scenario: il componente aggiuntivo non riesce a raggiungere l'endpoint Criteri di Azure servizio a causa della configurazione aad-pod-identity

#### <a name="issue"></a>Problema

Il componente aggiuntivo non può raggiungere l'endpoint Criteri di Azure servizio e restituisce uno degli errori seguenti:

- `azure.BearerAuthorizer#WithAuthorization: Failed to refresh the Token for request to https://gov-prod-policy-data.trafficmanager.net/checkDataPolicyCompliance?api-version=2019-01-01-preview: StatusCode=404`
- `adal: Refresh request failed. Status Code = '404'. Response body: getting assigned identities for pod kube-system/azure-policy-8c785548f-r882p in CREATED state failed after 16 attempts, retry duration [5]s, error: <nil>`

#### <a name="cause"></a>Causa

Questo errore si verifica _quando add-pod-identity_ viene installato nel cluster e i pod _kube-system_ non vengono esclusi in _aad-pod-identity._

I pod _NMI (aad-pod-identity_ component identità gestita del nodo) modificano gli iptable dei nodi per intercettare le chiamate all'endpoint dei metadati dell'istanza di Azure. Questa configurazione significa che qualsiasi richiesta effettuata all'endpoint dei metadati viene intercettata da NMI, anche se il pod non usa _aad-pod-identity._
_AzurePodIdentityException_ CustomResourceDefinition (CRD) può essere configurato per informare _aad-pod-identity_ che qualsiasi richiesta a un endpoint di metadati proveniente da un pod corrispondente alle etichette definite nel CRD deve essere proxy senza alcuna elaborazione in NMI.

#### <a name="resolution"></a>Soluzione

Escludere i pod di sistema che hanno l'etichetta nello spazio dei nomi `kubernetes.azure.com/managedby: aks` _kube-system_ in _aad-pod-identity_ configurando il CRD _AzurePodIdentityException._

Per altre informazioni, vedere [Disabilitare l'identità del pod Azure Active Directory (Azure AD) per un pod o un'applicazione specifica.](https://azure.github.io/aad-pod-identity/docs/configure/application_exception)

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

Il componente aggiuntivo può raggiungere l'endpoint Criteri di Azure servizio, ma i log del componente aggiuntivo visualizzano uno degli errori seguenti:

- `The resource provider 'Microsoft.PolicyInsights' is not registered in subscription '{subId}'. See
  https://aka.ms/policy-register-subscription for how to register subscriptions.`

- `policyinsightsdataplane.BaseClient#CheckDataPolicyCompliance: Failure responding to request:
  StatusCode=500 -- Original Error: autorest/azure: Service returned an error. Status=500
  Code="InternalServerError" Message="Encountered an internal server error.`

#### <a name="cause"></a>Causa

Il provider di risorse "Microsoft.PolicyInsights" non è registrato. Deve essere registrato per il componente aggiuntivo per ottenere le definizioni dei criteri e restituire i dati di conformità.

#### <a name="resolution"></a>Soluzione

Registrare il provider di risorse "Microsoft.PolicyInsights" nella sottoscrizione del cluster. Per istruzioni, vedere [Registrare un provider di risorse](../../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider).

### <a name="scenario-the-subscription-is-disabled"></a>Scenario: la sottoscrizione è disabilitata

#### <a name="issue"></a>Problema

Il componente aggiuntivo può raggiungere l'endpoint Criteri di Azure servizio, ma viene visualizzato l'errore seguente:

`The subscription '{subId}' has been disabled for azure data-plane policy. Please contact support.`

#### <a name="cause"></a>Causa

Questo errore indica che la sottoscrizione è stata determinata come problematica ed è stato aggiunto il flag di funzionalità `Microsoft.PolicyInsights/DataPlaneBlocked` per bloccare la sottoscrizione.

#### <a name="resolution"></a>Soluzione

Per analizzare e risolvere questo problema, [contattare il team delle funzionalità.](mailto:azuredg@microsoft.com)

## <a name="next-steps"></a>Passaggi successivi

Se il problema non è elencato in questo articolo o non è possibile risolverlo, ottenere supporto visitando uno dei canali seguenti:

- Ottenere risposte da esperti tramite [Microsoft Q&A.](/answers/topics/azure-policy.html)
- Collegarsi a [@AzureSupport](https://twitter.com/azuresupport), Questa risorsa Microsoft Azure su Twitter consente di migliorare l'esperienza del cliente connettendo la community di Azure alle risposte, al supporto e agli esperti più esperti.
- Se è ancora necessaria assistenza, passare al sito [supporto tecnico di Azure e](https://azure.microsoft.com/support/options/) selezionare **Invia una richiesta di supporto**.
