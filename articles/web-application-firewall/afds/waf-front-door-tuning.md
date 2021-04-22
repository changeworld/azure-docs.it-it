---
title: Ottimizzazione di Web Application Firewall (WAF) per Frontdoor di Azure
description: Questo articolo illustra come ottimizzare waf per Front door.
services: web-application-firewall
author: mohitkusecurity
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: mohitku
ms.reviewer: tyao
ms.openlocfilehash: c0879edc0e3fbd6cf6bcadc26dd862f95ecf4fd4
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872354"
---
# <a name="tuning-web-application-firewall-waf-for-azure-front-door"></a>Ottimizzazione di Web Application Firewall (WAF) per Frontdoor di Azure
 
Il set di regole predefinite gestito da Azure è basato sul set di regole di [base OWASP ed](https://github.com/SpiderLabs/owasp-modsecurity-crs/tree/v3.1/dev) è progettato per essere rigorosamente predefinito. Spesso è previsto che le regole WAF siano ottimizzate in base alle esigenze specifiche dell'applicazione o dell'organizzazione che usano WAF. Questo risultato viene comunemente ottenuto definendo le esclusioni delle regole, creando regole personalizzate e persino disabilitando le regole che possono causare problemi o falsi positivi. Esistono alcune operazioni che è possibile eseguire se le richieste che devono passare attraverso web application firewall (WAF) vengono bloccate.

Prima di tutto, assicurarsi di aver letto la panoramica [di WAF di Front door](afds-overview.md) e i documenti dei criteri [WAF per Front door.](waf-front-door-create-portal.md) Assicurarsi inoltre di aver abilitato il monitoraggio e la [registrazione di WAF.](waf-front-door-monitor.md) Questi articoli illustrano il funzionamento del WAF, il funzionamento dei set di regole WAF e l'accesso ai log di WAF.
 
## <a name="understanding-waf-logs"></a>Informazioni sui log di WAF
 
Lo scopo dei log WAF è mostrare ogni richiesta corrispondente o bloccata dal WAF. Si tratta di una raccolta di tutte le richieste valutate corrispondenti o bloccate. Se si nota che waf blocca una richiesta che non dovrebbe essere (falso positivo), è possibile eseguire alcune operazioni. Per prima cosa, restringere e trovare la richiesta specifica. Se lo si desidera, è possibile [configurare un messaggio](./waf-front-door-configure-custom-response-code.md) di risposta personalizzato in modo da includere il campo per identificare facilmente l'evento ed eseguire una query di log su tale valore `trackingReference` specifico. Esaminare i log per trovare l'URI specifico, il timestamp o l'INDIRIZZO IP client della richiesta. Quando si trovano le voci di log correlate, è possibile iniziare ad agire sui falsi positivi. 
 
Ad esempio, si supponga di avere un traffico legittimo contenente la stringa `1=1` che si vuole passare attraverso il WAF. Ecco l'aspetto della richiesta:

```
POST http://afdwafdemosite.azurefd.net/api/Feedbacks HTTP/1.1
Host: afdwafdemosite.azurefd.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 55

UserId=20&captchaId=7&captchaId=15&comment="1=1"&rating=3
```

Se si prova la richiesta, il WAF blocca il traffico che contiene la stringa *1=1* in qualsiasi parametro o campo. Si tratta di una stringa spesso associata a un attacco SQL injection. È possibile esaminare i log e visualizzare il timestamp della richiesta e le regole bloccate/corrispondenti.
 
Nell'esempio seguente viene esplorato un `FrontdoorWebApplicationFirewallLog` log generato a causa di una corrispondenza di regola. La query di Log Analytics seguente può essere usata per trovare le richieste bloccate nelle ultime 24 ore:

```kusto
AzureDiagnostics
| where Category == 'FrontdoorWebApplicationFirewallLog'
| where TimeGenerated > ago(1d)
| where action_s == 'Block'

```
 
Nel campo `requestUri` è possibile vedere che la richiesta è stata effettuata in modo `/api/Feedbacks/` specifico. Più avanti, l'ID regola viene `942110` trovato nel `ruleName` campo . Conoscendo l'ID regola, è possibile passare al repository ufficiale del set di regole [OWASP ModSecurity Core](https://github.com/coreruleset/coreruleset) ed eseguire una ricerca in base a tale [ID](https://github.com/coreruleset/coreruleset/blob/v3.1/dev/rules/REQUEST-942-APPLICATION-ATTACK-SQLI.conf) regola per esaminarne il codice e comprendere esattamente la corrispondenza di questa regola. 
 
Quindi, controllando il campo , si può vedere che questa regola è impostata per bloccare le richieste in caso di corrispondenza e si conferma che la richiesta è stata di fatto bloccata dal WAF perché è `action` `policyMode` impostato su `prevention` . 
 
A questo punto, è possibile controllare le informazioni nel `details` campo . Qui è possibile visualizzare le `matchVariableName` informazioni e `matchVariableValue` . Si apprenderà che questa regola è stata attivata perché un utente ha immesso *1=1* `comment` nel campo dell'app Web.
 
```json
{
    "time": "2020-09-24T16:43:04.5422943Z",
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.NETWORK/FRONTDOORS/AFDWAFDEMOSITE",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoor/WebApplicationFirewallLog/Write",
    "properties": {
        "clientIP": "1.1.1.1",
        "clientPort": "53566",
        "socketIP": "1.1.1.1",
        "requestUri": "http://afdwafdemosite.azurefd.net:80/api/Feedbacks/",
        "ruleName": "DefaultRuleSet-1.0-SQLI-942110",
        "policy": "AFDWAFDemoPolicy",
        "action": "Block",
        "host": "afdwafdemosite.azurefd.net",
        "trackingReference": "0mMxsXwAAAABEalekYeI4S55qpi5R7R0/V1NURURHRTA4MTIAZGI4NGQzZDgtNWQ5Ny00ZWRkLTg2ZGYtZDJjNThlMzI2N2I4",
        "policyMode": "prevention",
        "details": {
            "matches": [
                {
                    "matchVariableName": "PostParamValue:comment",
                    "matchVariableValue": "\"1=1\""
                }
            ],
            "msg": "SQL Injection Attack: Common Injection Testing Detected",
            "data": "Matched Data: \"1=1\" found within PostParamValue:comment: \"1=1\""
        }
    }
}
```
 
È anche importante controllare i log di accesso per espandere le proprie conoscenze su un determinato evento WAF. Di seguito viene esaminato `FrontdoorAccessLog` il log generato come risposta all'evento precedente.
 
È possibile vedere che si tratta di log correlati in base `trackingReference` al valore che corrisponde. Tra i vari campi che forniscono informazioni generali, ad esempio e , si `userAgent` `clientIP` richiama l'attenzione sui `httpStatusCode` campi e `httpStatusDetails` . In questo caso, è possibile verificare che il client abbia ricevuto una risposta HTTP 403, che conferma assolutamente che la richiesta è stata negata e bloccata. 
 
```json
{
    "time": "2020-09-24T16:43:04.5430764Z",
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.NETWORK/FRONTDOORS/AFDWAFDEMOSITE",
    "category": "FrontdoorAccessLog",
    "operationName": "Microsoft.Network/FrontDoor/AccessLog/Write",
    "properties": {
        "trackingReference": "0mMxsXwAAAABEalekYeI4S55qpi5R7R0/V1NURURHRTA4MTIAZGI4NGQzZDgtNWQ5Ny00ZWRkLTg2ZGYtZDJjNThlMzI2N2I4",
        "httpMethod": "POST",
        "httpVersion": "1.1",
        "requestUri": "http://afdwafdemosite.azurefd.net:80/api/Feedbacks/",
        "requestBytes": "2160",
        "responseBytes": "324",
        "userAgent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/85.0.4183.83 Safari/537.36",
        "clientIp": "1.1.1.1",
        "socketIp": "1.1.1.1",
        "clientPort": "53566",
        "timeToFirstByte": "0.01",
        "timeTaken": "0.011",
        "securityProtocol": "",
        "routingRuleName": "DemoBERoutingRule",
        "rulesEngineMatchNames": [],
        "backendHostname": "13.88.65.130:3000",
        "isReceivedFromClient": true,
        "httpStatusCode": "403",
        "httpStatusDetails": "403",
        "pop": "WST",
        "cacheStatus": "CONFIG_NOCACHE"
    }
}
```

## <a name="resolving-false-positives"></a>Risoluzione di falsi positivi
 
Per prendere una decisione informata sulla gestione di un falso positivo, è importante acquisire familiarità con le tecnologie utilizzate dall'applicazione. Ad esempio, si supponga che non sia presente un server SQL nello stack di tecnologie e che si riceveranno falsi positivi correlati a tali regole. La disabilitazione di queste regole non comporta necessariamente un calo della sicurezza. 

Con queste informazioni e la conoscenza che la regola 942110 è quella che corrisponde alla stringa nell'esempio, è possibile eseguire alcune operazioni per impedire il blocco di questa richiesta `1=1` legittima:
 
* Usare gli elenchi di esclusione
  * Per [altre informazioni sugli elenchi di esclusione, vedere Web Application Firewall (WAF) con elenchi di](waf-front-door-exclusion.md) esclusione del servizio Front door. 
* Modificare le azioni waf
  * Per altre informazioni sulle azioni che è possibile eseguire quando una richiesta corrisponde alle condizioni di una regola, vedere Azioni [WAF.](afds-overview.md#waf-actions)
* Usare regole personalizzate
  * Vedere [Regole personalizzate per Web Application Firewall con Frontdoor di Azure](waf-front-door-custom-rules.md) per altre informazioni sulle regole personalizzate.
* Disabilitare le regole 

> [!TIP]
> Quando si seleziona un approccio per consentire richieste legittime tramite waf, provare a restringere il più possibile. Ad esempio, è meglio usare un elenco di esclusione anziché disabilitare completamente una regola.

### <a name="using-exclusion-lists"></a>Uso degli elenchi di esclusione

Un vantaggio dell'uso di un elenco di esclusione è che solo la variabile di corrispondenza selezionata per l'esclusione non verrà più esaminata per la richiesta specificata. In altri casi, è possibile scegliere tra intestazioni di richiesta specifiche, cookie di richiesta, argomenti della stringa di query o argomenti post del corpo della richiesta da escludere se viene soddisfatta una determinata condizione, anziché escludere l'intera richiesta dal controllo. Le altre variabili non specificate della richiesta verranno comunque controllate normalmente.
 
È importante considerare che le esclusioni sono un'impostazione globale. Ciò significa che l'esclusione configurata verrà applicata a tutto il traffico che passa attraverso il WAF, non solo a un'app Web o a un URI specifico. Ad esempio, questo potrebbe essere un problema se *1=1* è una richiesta valida nel corpo per una determinata app Web, ma non per altri utenti con gli stessi criteri WAF. Se è opportuno usare elenchi di esclusione diversi per applicazioni diverse, è consigliabile usare criteri WAF diversi per ogni applicazione e applicarli al front-end di ogni applicazione.
 
Quando si configurano gli elenchi di esclusione per le regole gestite, è possibile scegliere di escludere tutte le regole all'interno di un set di regole, tutte le regole all'interno di un gruppo di regole o una singola regola. È possibile configurare un elenco di esclusione usando [PowerShell, l'interfaccia](/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject)della riga di comando [di Azure,](/cli/azure/network/front-door/waf-policy/managed-rules/exclusion#az_network_front_door_waf_policy_managed_rules_exclusion_add) [l'API REST](/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)o il portale di Azure.

* Esclusioni a livello di regola
  * L'applicazione di esclusioni a livello di regola significa che le esclusioni specificate non verranno analizzate solo in base a tale singola regola, mentre verranno comunque analizzate da tutte le altre regole nel set di regole. Si tratta del livello più granulare per le esclusioni e può essere usato per ottimizzare il set di regole gestite in base alle informazioni disponibili nei log di WAF durante la risoluzione dei problemi relativi a un evento.
* Esclusioni a livello di gruppo di regole
  * L'applicazione di esclusioni a livello di gruppo di regole significa che le esclusioni specificate non verranno analizzate rispetto a quel set specifico di tipi di regole. Se ad esempio si seleziona *SQLI* come gruppo di regole escluse, le esclusioni delle richieste definite non verranno controllate da nessuna delle regole specifiche di SQLI, ma verranno comunque controllate dalle regole di altri gruppi, ad esempio *PHP,* *RFI* o *XSS.* Questo tipo di esclusione può essere utile quando si è certi che l'applicazione non sia soggetta a tipi specifici di attacchi. Ad esempio, un'applicazione senza database SQL potrebbe avere tutte le regole *SQLI* escluse senza compromettere il livello di sicurezza.
* Esclusioni a livello di set di regole 
  * L'applicazione di esclusioni a livello di set di regole significa che le esclusioni specificate non verranno analizzate rispetto alle regole di sicurezza disponibili in tale set di regole. Si tratta di un'esclusione completa, quindi deve essere usata con attenzione.

In questo esempio verrà eseguita un'esclusione al livello più granulare (applicando l'esclusione a una singola regola) e si sta cercando di escludere la variabile di corrispondenza **Request body post args name** che contiene `comment` . Questo è evidente perché è possibile visualizzare i dettagli della variabile di corrispondenza nel log del firewall: `"matchVariableName": "PostParamValue:comment"` . L'attributo è `comment`. È anche possibile trovare questo nome di attributo in altri modi, vedere [Ricerca dei nomi degli attributi della richiesta](#finding-request-attribute-names).

![Regole di esclusione](../media/waf-front-door-tuning/exclusion-rules.png)

![Esclusione delle regole per una regola specifica](../media/waf-front-door-tuning/exclusion-rule.png)

In alcuni casi, in alcuni casi parametri specifici vengono passati al WAF in modo che non siano intuitivi. Ad esempio, è presente un token che viene passato durante l'autenticazione usando Azure Active Directory. Questo token, `__RequestVerificationToken` , viene in genere passato come cookie di richiesta. Tuttavia, in alcuni casi in cui i cookie sono disabilitati, questo token viene passato anche come argomento post richiesta. Per questo motivo, per risolvere Azure AD token falsi positivi, è necessario assicurarsi che sia aggiunto all'elenco di esclusione `__RequestVerificationToken` sia per che per `RequestCookieNames` `RequestBodyPostArgsNames` .

Le esclusioni per un nome di campo (*selettore*) significa che il valore non verrà più valutato dal WAF. Tuttavia, il nome del campo stesso continua a essere valutato e in rari casi può corrispondere a una regola WAF e attivare un'azione.

![Esclusione delle regole per il set di regole](../media/waf-front-door-tuning/exclusion-rule-selector.png)

### <a name="changing-waf-actions"></a>Modifica delle azioni waf

Un altro modo per gestire il comportamento delle regole WAF è scegliere l'azione che verrà eseguita quando una richiesta corrisponde alle condizioni di una regola. Le azioni disponibili sono: [Consenti, Blocca, Log e Reindirizza.](afds-overview.md#waf-actions)

In questo esempio l'azione predefinita *Blocca è* stata modificata in *Azione* log per la regola 942110. In questo modo waf logerà la richiesta e continuerà a valutare la stessa richiesta rispetto alle regole di priorità inferiore rimanenti.

![Azioni di WAF](../media/waf-front-door-tuning/actions.png)

Dopo aver eseguito la stessa richiesta, è possibile fare riferimento ai log e si potrà vedere che la richiesta corrisponde all'ID regola 942110 e che il campo ora indica Log anziché `action_s` *Blocca*.  È stata quindi espansa la query di log per includere `trackingReference_s` le informazioni e vedere che cosa è successo con questa richiesta.

![Log che mostra più corrispondenze di regole](../media/waf-front-door-tuning/actions-log.png)

È interessante notare che dopo l'elaborazione dell'ID regola 942110 si verifica una corrispondenza diversa tra regole SQLI e millisecondi. La stessa richiesta corrispondeva all'ID regola 942310 e questa volta è stata attivata l'azione predefinita *Block.*

Un altro vantaggio  dell'uso dell'azione Log durante l'ottimizzazione o la risoluzione dei problemi di WAF è che è possibile identificare se più regole all'interno di un gruppo di regole specifico corrispondono e bloccano una determinata richiesta. È quindi possibile creare le esclusioni al livello appropriato, ad esempio a livello di regola o di gruppo di regole. 

### <a name="using-custom-rules"></a>Uso di regole personalizzate

Dopo aver identificato la causa della corrispondenza di una regola WAF, è possibile usare regole personalizzate per modificare la modalità di risposta del WAF all'evento. Le regole personalizzate vengono elaborate prima delle regole gestite, possono contenere più di una condizione e le azioni possono essere [Consenti, Nega, Log o Reindirizzamento.](afds-overview.md#waf-actions) Quando è presente una corrispondenza di regola, l'elaborazione del motore WAF viene arrestata. Ciò significa che altre regole personalizzate con priorità più bassa e regole gestite non vengono più eseguite.

Nell'esempio seguente è stata creata una regola personalizzata con due condizioni. La prima condizione cerca il `comment` valore nel corpo della richiesta. La seconda condizione cerca il valore `/api/Feedbacks/` nell'URI della richiesta.

L'uso di una regola personalizzata consente di essere il più granulare durante l'ottimizzazione delle regole waf e per la gestione di falsi positivi. In questo caso, non viene eseguita alcuna azione solo in base al valore del corpo della richiesta, che potrebbe esistere in più siti o app con gli stessi criteri `comment` WAF. Includendo un'altra condizione per la corrispondenza anche in un URI di richiesta specifico, si garantisce che questa regola personalizzata si applitti realmente a questo caso d'uso esplicito `/api/Feedbacks/` che è stato illustrato. In questo modo si garantisce che lo stesso attacco, se eseguito in condizioni diverse, sia ancora controllato e impedito dal motore WAF.

![Log](../media/waf-front-door-tuning/custom-rule.png)

Quando si esplora il log, è possibile vedere che il campo contiene il nome `ruleName_s` assegnato alla regola personalizzata creata: `redirectcomment` . Nel campo `action_s` è possibile vedere che è stata eseguita l'azione *di* reindirizzamento per questo evento. Nel campo `details_matches_s` è possibile visualizzare i dettagli per entrambe le condizioni corrispondenti.

### <a name="disabling-rules"></a>Disabilitazione delle regole

Un altro modo per aggirare un falso positivo è disabilitare la regola corrispondente all'input che waf ha ritenuto dannoso. Poiché i log waf sono stati analizzati e la regola è stata ridotta a 942110, è possibile disabilitarla nel portale di Azure. Vedere [Personalizzare le regole di Web Application Firewall usando portale di Azure](../ag/application-gateway-customize-waf-rules-portal.md#disable-rule-groups-and-rules).
 
La disabilitazione di una regola è un vantaggio quando si è certi che tutte le richieste che sodalino tale condizione siano in realtà richieste legittime o quando si è certi che la regola semplicemente non si applica all'ambiente (ad esempio, disabilitando una regola SQL injection perché si dispone di back-end non SQL). 
 
Tuttavia, la disabilitazione di una regola è un'impostazione globale che si applica a tutti gli host front-end associati ai criteri WAF. Quando si sceglie di disabilitare una regola, è possibile che le vulnerabilità siano esposte senza protezione o rilevamento per qualsiasi altro host front-end associato ai criteri WAF.
 
Se si vuole usare Azure PowerShell per disabilitare una regola gestita, vedere la documentazione [`PSAzureManagedRuleOverride`](/powershell/module/az.frontdoor/new-azfrontdoorwafmanagedruleoverrideobject) dell'oggetto. Se si vuole usare l'interfaccia della riga di comando di Azure, vedere la [`az network front-door waf-policy managed-rules override`](/cli/azure/network/front-door/waf-policy/managed-rules/override) documentazione.

![Regole di WAF](../media/waf-front-door-tuning/waf-rules.png)

> [!TIP]
> È buona idea documentare le modifiche apportate ai criteri waf. Includere richieste di esempio per illustrare il rilevamento di falsi positivi e spiegare chiaramente perché è stata aggiunta una regola personalizzata, è stata disabilitata una regola o un set di regole o è stata aggiunta un'eccezione. Questa documentazione può essere utile se si riprogetta l'applicazione in futuro ed è necessario verificare che le modifiche siano ancora valide. Può anche essere utile se si è mai controllati o è necessario giustificare il motivo per cui i criteri WAF sono stati riconfigurati dalle impostazioni predefinite.

## <a name="finding-request-fields"></a>Ricerca di campi di richiesta

Usando un proxy del browser come [Fiddler,](https://www.telerik.com/fiddler)è possibile esaminare le singole richieste e determinare quali campi specifici di una pagina Web vengono chiamati. Ciò è utile quando è necessario escludere determinati campi dall'ispezione usando gli elenchi di esclusione in WAF.

### <a name="finding-request-attribute-names"></a>Ricerca dei nomi degli attributi della richiesta
 
In questo esempio è possibile vedere che il campo in cui è `1=1` stata immessa la stringa è denominato `comment` . Questi dati sono stati passati nel corpo di una richiesta POST.

![Richiesta di Fiddler che mostra il corpo](../media/waf-front-door-tuning/fiddler-request-attribute-name.png)

Si tratta di un campo che è possibile escludere. Per altre informazioni sugli elenchi di esclusione, vedere [Elenchi di esclusione di Web application firewall.](./waf-front-door-exclusion.md) In questo caso è possibile escludere la valutazione configurando l'esclusione seguente:

![Regola di esclusione](../media/waf-front-door-tuning/fiddler-request-attribute-name-exclusion.png)

È anche possibile esaminare i log del firewall per ottenere le informazioni necessarie per visualizzare gli elementi da aggiungere all'elenco di esclusione. Per abilitare la registrazione, vedere [Monitoraggio di metriche e log in Frontdoor di Azure](./waf-front-door-monitor.md).

Esaminare il log del firewall nel `PT1H.json` file per l'ora in cui si è verificata la richiesta da esaminare. `PT1H.json` I file sono disponibili nei contenitori dell'account di archiviazione in cui sono archiviati i log `FrontDoorWebApplicationFirewallLog` di diagnostica e `FrontDoorAccessLog` .

In questo esempio è possibile visualizzare la regola che ha bloccato la richiesta (con lo stesso riferimento alla transazione) e si è verificata esattamente nello stesso momento:

```json
{
    "time": "2020-09-24T16:43:04.5422943Z",
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.NETWORK/FRONTDOORS/AFDWAFDEMOSITE",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoor/WebApplicationFirewallLog/Write",
    "properties": {
        "clientIP": "1.1.1.1",
        "clientPort": "53566",
        "socketIP": "1.1.1.1",
        "requestUri": "http://afdwafdemosite.azurefd.net:80/api/Feedbacks/",
        "ruleName": "DefaultRuleSet-1.0-SQLI-942110",
        "policy": "AFDWAFDemoPolicy",
        "action": "Block",
        "host": "afdwafdemosite.azurefd.net",
        "trackingReference": "0mMxsXwAAAABEalekYeI4S55qpi5R7R0/V1NURURHRTA4MTIAZGI4NGQzZDgtNWQ5Ny00ZWRkLTg2ZGYtZDJjNThlMzI2N2I4",
        "policyMode": "prevention",
        "details": {
            "matches": [
                {
                    "matchVariableName": "PostParamValue:comment",
                    "matchVariableValue": "\"1=1\""
                }
            ],
            "msg": "SQL Injection Attack: Common Injection Testing Detected",
            "data": "Matched Data: \"1=1\" found within PostParamValue:comment: \"1=1\""
        }
    }
}
```

Conoscendo il funzionamento dei set di regole gestiti da Azure (vedere Web Application Firewall in [Frontdoor di Azure),](afds-overview.md)si sa che la regola con l'azione: Blocca blocca la proprietà in base ai dati corrispondenti nel corpo della richiesta.  È possibile vedere nei dettagli che corrisponde a un criterio ( `1=1` ) e che il campo è denominato `comment` . Seguire gli stessi passaggi precedenti per escludere il nome post-argomenti del corpo della richiesta che contiene `comment` .

### <a name="finding-request-header-names"></a>Ricerca dei nomi delle intestazioni delle richieste

Fiddler è ancora una volta uno strumento utile per trovare i nomi delle intestazioni delle richieste. Nello screenshot seguente è possibile visualizzare le intestazioni per questa richiesta GET, che includono Content-Type, User-Agent e così via. È anche possibile usare le intestazioni di richiesta per creare esclusioni e regole personalizzate in WAF.

![Richiesta Fiddler che mostra l'intestazione](../media/waf-front-door-tuning/fiddler-request-header-name.png)

Un altro modo per visualizzare le intestazioni di richiesta e risposta è cercare all'interno degli strumenti di sviluppo del browser, ad esempio Edge o Chrome. È possibile premere F12 o fare clic con il pulsante destro del mouse su -> **Inspect** Strumenti di sviluppo e selezionare la scheda  ->   **Rete.** Caricare una pagina Web e fare clic sulla richiesta da esaminare.

![Richiesta di controllo di rete](../media/waf-front-door-tuning/network-inspector-request.png)

### <a name="finding-request-cookie-names"></a>Ricerca dei nomi dei cookie delle richieste

Se la richiesta contiene cookie, è possibile selezionare la scheda Cookie per visualizzarli in Fiddler. Le informazioni sui cookie possono essere usate anche per creare esclusioni o regole personalizzate in WAF.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [web application firewall di Azure](../overview.md).
- Informazioni su come [creare una Frontdoor](../../frontdoor/quickstart-create-front-door.md).
