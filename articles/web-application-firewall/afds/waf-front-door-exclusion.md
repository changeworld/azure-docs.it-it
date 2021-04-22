---
title: Elenchi di esclusione di Web application firewall in Frontdoor di Azure - portale di Azure
description: Questo articolo fornisce informazioni sulla configurazione degli elenchi di esclusione in Front di Azure con il portale di Azure.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/10/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 83baf03c414d9b0f7acb6a93db03794a539a3c58
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107860834"
---
# <a name="web-application-firewall-waf-with-front-door-service-exclusion-lists"></a>Web Application Firewall (WAF) con elenchi di esclusione del servizio Front door 

In alcuni casi Web Application Firewall (WAF) potrebbe bloccare una richiesta che si vuole consentire per l'applicazione. Ad esempio, Active Directory inserisce i token usati per l'autenticazione. Questi token possono contenere caratteri speciali che possono attivare un falso positivo dalle regole waf. Gli elenchi di esclusione di Web Application firewall consentono agli utenti di omettere determinati attributi di richiesta da una valutazione di WAF.  È possibile configurare un elenco di esclusione usando  [PowerShell, l'interfaccia](/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject)della riga di comando [di Azure,](/cli/azure/network/front-door/waf-policy/managed-rules/exclusion#az_network_front_door_waf_policy_managed_rules_exclusion_add) [l'API REST](/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)o il portale di Azure. Nell'esempio seguente viene illustrata portale di Azure configurazione. 
## <a name="configure-exclusion-lists-using-the-azure-portal"></a>Configurare gli elenchi di esclusione usando il portale di Azure
**Gestire le esclusioni** è accessibile dal portale waf in **Regole gestite**

![Gestire ](../media/waf-front-door-exclusion/exclusion1.png)
 ![ l'esclusione Gestire exclusion_add](../media/waf-front-door-exclusion/exclusion2.png)

 Un elenco di esclusione di esempio: ![ Gestire exclusion_define](../media/waf-front-door-exclusion/exclusion3.png)

Questo esempio esclude il valore nel campo *dell'intestazione* utente. Una richiesta valida può includere il *campo utente* che contiene una stringa che attiva una regola SQL injection. In questo caso è *possibile escludere* il parametro user in modo che la regola WAF non valuti nulla nel campo.

Gli attributi seguenti possono essere aggiunti agli elenchi di esclusione in base al nome. I valori dei campi in uso non vengono valutati in base alle regole waf, ma i relativi nomi vengono valutati. Gli elenchi di esclusione rimuovono l'ispezione del valore del campo.

* Nome intestazione richiesta
* Richiedere il nome del cookie
* Nome degli argomenti della stringa di query
* Nome degli argomenti post del corpo della richiesta

È possibile specificare un'esatta intestazione di richiesta, un corpo, un cookie o una corrispondenza dell'attributo stringa della query.  In alternativa, è possibile specificare facoltativamente corrispondenze parziali. Gli operatori seguenti sono i criteri di corrispondenza supportati:

- **Uguale a**: questo operatore viene usato per una corrispondenza esatta. Ad esempio, per selezionare un'intestazione denominata **bearerToken,** usare l'operatore equals con il selettore impostato **su bearerToken**.
- **Inizia con**: questo operatore corrisponde a tutti i campi che iniziano con il valore del selettore specificato.
- **Termina con**: questo operatore corrisponde a tutti i campi di richiesta che terminano con il valore del selettore specificato.
- **Contiene**: questo operatore corrisponde a tutti i campi di richiesta che contengono il valore del selettore specificato.
- **Uguale a qualsiasi**: questo operatore corrisponde a tutti i campi della richiesta. * è il valore del selettore.

Per i nomi di intestazione e cookie non viene distinzione tra maiuscole e minuscole.

Se un valore di intestazione, un valore del cookie, un valore dell'argomento post o un valore dell'argomento di query genera falsi positivi per alcune regole, è possibile escludere tale parte della richiesta dalla considerazione da parte della regola:


|matchVariableName dai log WAF  |Esclusione delle regole nel portale  |
|---------|---------|
|CookieValue:SOME_NAME        |Richiedi nome cookie Uguale SOME_NAME|
|HeaderValue:SOME_NAME        |Nome intestazione richiesta Uguale SOME_NAME|
|PostParamValue:SOME_NAME     |Nome dell'argomento post del corpo della richiesta Equals SOME_NAME|
|QueryParamValue:SOME_NAME    |Nome degli argomenti della stringa di query Equals SOME_NAME|


Attualmente sono supportate solo le esclusioni di regole per i matchVariableNames precedenti nei log WAF. Per qualsiasi altro matchVariableNames, è necessario disabilitare le regole che forniscono falsi positivi o creare una regola personalizzata che consenta in modo esplicito tali richieste. In particolare, quando matchVariableName è CookieName, HeaderName, PostParamName o QueryParamName, significa che il nome stesso attiva la regola. Al momento l'esclusione delle regole non supporta matchVariableNames.


Se si esclude un argomento post del corpo della richiesta denominato *FOO,* nessuna regola deve visualizzare PostParamValue:FOO come matchVariableName nei log waf. Tuttavia, è comunque possibile che venga visualizzata una regola con matchVariableName InitialBodyContents che corrisponde al valore dell'oggetto FOO del parametro post perché i valori dei parametri post fanno parte di InitialBodyContents.

È possibile applicare elenchi di esclusione a tutte le regole all'interno del set di regole gestite, alle regole per un gruppo di regole specifico o a una singola regola, come illustrato nell'esempio precedente.

## <a name="define-exclusion-based-on-web-application-firewall-logs"></a>Definire l'esclusione in base ai log di Web Application Firewall
 [Web application firewall di Azure monitoraggio e registrazione mostra](waf-front-door-monitor.md) i dettagli corrispondenti di una richiesta bloccata. Se un valore di intestazione, un valore del cookie, un valore dell'argomento post o un valore dell'argomento di query produce falsi positivi per alcune regole, è possibile escludere tale parte della richiesta dalla regola. La tabella seguente mostra i valori di esempio dei log waf e le condizioni di esclusione corrispondenti.

|matchVariableName dai log waf    |Esclusione di regole nel portale|
|--------|------|
|CookieValue:SOME_NAME  |Request cookie name Equals SOME_NAME|
|HeaderValue:SOME_NAME  |Nome intestazione richiesta Uguale SOME_NAME|
|PostParamValue:SOME_NAME|  Il corpo della richiesta pubblica il nome args Equals SOME_NAME|
|QueryParamValue:SOME_NAME| Nome degli argomenti della stringa di query Uguale SOME_NAME|


## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato le impostazioni di WAF, vedere come visualizzare i log di WAF. Per altre informazioni, vedere [Diagnostica di Front door.](../afds/waf-front-door-monitor.md)