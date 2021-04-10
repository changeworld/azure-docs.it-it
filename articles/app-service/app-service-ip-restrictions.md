---
title: Restrizioni di accesso al servizio app Azure
description: Informazioni su come proteggere l'app nel servizio app Azure impostando restrizioni di accesso.
author: ccompy
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.topic: article
ms.date: 12/17/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 420dade645d1a4ee32bb888aecb76b033d5756e1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105731301"
---
# <a name="set-up-azure-app-service-access-restrictions"></a>Configurare le restrizioni di accesso al servizio app Azure

Se si configurano restrizioni di accesso, è possibile definire un elenco Consenti/Nega ordinato per priorità che controlla l'accesso alla rete per l'app. L'elenco può includere indirizzi IP o subnet di rete virtuale di Azure. Quando sono presenti una o più voci, alla fine dell'elenco esiste una negazione implicita *All* .

La funzionalità di restrizione dell'accesso funziona con tutti i carichi di lavoro ospitati dal servizio app Azure. I carichi di lavoro possono includere app Web, app per le API, app Linux, app contenitore Linux e funzioni.

Quando viene effettuata una richiesta all'app, l'indirizzo FROM viene valutato in base alle regole nell'elenco di restrizioni di accesso. Se l'indirizzo FROM si trova in una subnet configurata con gli endpoint di servizio in Microsoft. Web, la subnet di origine viene confrontata con le regole della rete virtuale nell'elenco di restrizioni di accesso. Se l'indirizzo non è consentito per l'accesso in base alle regole nell'elenco, il servizio risponde con un codice di stato [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) .

La funzionalità di restrizione dell'accesso è implementata nei ruoli front-end del servizio app, che sono upstream degli host di lavoro in cui viene eseguito il codice. Pertanto, le restrizioni di accesso sono in effetti elenchi di controllo di accesso di rete (ACL).

La possibilità di limitare l'accesso all'app Web da una rete virtuale di Azure è abilitata dagli [endpoint del servizio][serviceendpoints]. Con gli endpoint di servizio, è possibile limitare l'accesso a un servizio multi-tenant dalle subnet selezionate. Non funziona per limitare il traffico alle app ospitate in un ambiente del servizio app. Se ci si trova in una ambiente del servizio app, è possibile controllare l'accesso all'app applicando le regole degli indirizzi IP.

> [!NOTE]
> Gli endpoint di servizio devono essere abilitati sia sul lato rete sia per il servizio di Azure con cui sono abilitati. Per un elenco dei servizi di Azure che supportano gli endpoint di servizio, vedere [endpoint del servizio di rete virtuale](../virtual-network/virtual-network-service-endpoints-overview.md).
>

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-flow.png" alt-text="Diagramma del flusso di restrizioni di accesso.":::

## <a name="manage-access-restriction-rules-in-the-portal"></a>Gestire le regole di restrizione dell'accesso nel portale

Per aggiungere una regola di restrizione di accesso all'app, seguire questa procedura:

1. Accedere al portale di Azure.

1. Nel riquadro sinistro selezionare **rete**.

1. Nel riquadro **rete** , in **restrizioni di accesso**, selezionare **configura restrizioni di accesso**.

    :::image type="content" source="media/app-service-ip-restrictions/access-restrictions.png" alt-text="Screenshot del riquadro Opzioni di rete del servizio app nel portale di Azure.":::

1. Nella pagina **restrizioni di accesso** esaminare l'elenco di regole di restrizione di accesso definite per l'app.

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-browse.png" alt-text="Screenshot della pagina restrizioni di accesso nella portale di Azure, che mostra l'elenco delle regole di restrizione di accesso definite per l'app selezionata.":::

   L'elenco Visualizza tutte le restrizioni correnti applicate all'app. Se si dispone di una restrizione della rete virtuale nell'app, nella tabella viene indicato se gli endpoint di servizio sono abilitati per Microsoft. Web. Se non è stata definita alcuna restrizione nell'app, l'app è accessibile da qualsiasi posizione.

### <a name="add-an-access-restriction-rule"></a>Aggiungere una regola di restrizione di accesso

Per aggiungere una regola di restrizione di accesso all'app, nel riquadro **restrizioni di accesso** selezionare **Aggiungi regola**. Dopo aver aggiunto una regola, questa diventa effettiva immediatamente. 

Le regole vengono applicate in ordine di priorità, a partire dal numero più basso nella colonna **priorità** . Un'istruzione *Deny* implicita All è attiva dopo aver aggiunto anche una singola regola.

Quando si crea una regola nel riquadro **Aggiungi restrizione di accesso** , procedere come segue:

1. In **azione** selezionare **Consenti** o **Nega**.  

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-ip-add.png?v2" alt-text="Screenshot del riquadro ' Aggiungi restrizione di accesso '.":::

1. Facoltativamente, immettere un nome e una descrizione per la regola.
1. Nella casella **priorità** immettere un valore di priorità.
1. Nell'elenco a discesa **tipo** selezionare il tipo di regola.

Le sezioni seguenti illustrano i diversi tipi di regole.

> [!NOTE]
> - È previsto un limite di 512 regole di restrizione dell'accesso. Se sono necessarie più di 512 regole di restrizione dell'accesso, si consiglia di prendere in considerazione l'installazione di un prodotto di sicurezza autonomo, ad esempio front-end di Azure, app Azure gateway o una WAF alternativa.
>
#### <a name="set-an-ip-address-based-rule"></a>Impostare una regola basata sull'indirizzo IP

Attenersi alla procedura illustrata nella sezione precedente, ma con l'aggiunta seguente:
* Per il passaggio 4, nell'elenco a discesa **tipo** selezionare **IPv4** o **IPv6**. 

Specificare il **blocco di indirizzi IP** nella notazione CIDR (classy Inter-Domain routing) per gli indirizzi IPv4 e IPv6. Per specificare un indirizzo, è possibile usare qualcosa come *1.2.3.4/32*, in cui i primi quattro ottetti rappresentano l'indirizzo IP e */32* è la maschera. La notazione CIDR IPv4 per tutti gli indirizzi è 0.0.0.0/0. Per ulteriori informazioni sulla notazione CIDR, vedere [Routing Inter-Domain di classi](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). 

#### <a name="set-a-service-endpoint-based-rule"></a>Impostare una regola basata sull'endpoint di servizio

* Per il passaggio 4, nell'elenco a discesa **tipo** selezionare **rete virtuale**.

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-vnet-add.png?v2" alt-text="Screenshot del riquadro ' Aggiungi restrizione ' con il tipo di rete virtuale selezionato.":::

Specificare gli elenchi a discesa **sottoscrizione**, **rete virtuale** e **subnet** , corrispondenti a ciò a cui si vuole limitare l'accesso.

Usando gli endpoint di servizio, è possibile limitare l'accesso alle subnet della rete virtuale di Azure selezionate. Se gli endpoint di servizio non sono già abilitati con Microsoft. Web per la subnet selezionata, verranno abilitati automaticamente a meno che non si selezioni la casella di controllo **Ignora endpoint dei servizi Microsoft. Web mancanti** . Lo scenario in cui potrebbe essere necessario abilitare gli endpoint di servizio nell'app, ma non la subnet, dipende principalmente dal fatto che siano disponibili le autorizzazioni per abilitarle nella subnet. 

Se è necessario un altro utente per abilitare gli endpoint di servizio nella subnet, selezionare la casella di controllo **Ignora endpoint dei servizi Microsoft. Web mancanti** . L'app verrà configurata per gli endpoint di servizio in previsione di essere abilitata in un secondo momento nella subnet. 

Non è possibile usare gli endpoint di servizio per limitare l'accesso alle app eseguite in un ambiente del servizio app. Quando l'app si trova in una ambiente del servizio app, è possibile controllarne l'accesso applicando regole di accesso IP. 

Con gli endpoint di servizio è possibile configurare l'app con gateway applicazione o altri dispositivi web application firewall (WAF). È anche possibile configurare applicazioni multilivello con back-end protetti. Per altre informazioni, vedere [funzionalità di rete e integrazione del servizio app](networking-features.md) e del [gateway applicazione con gli endpoint di servizio](networking/app-gateway-with-service-endpoints.md).

> [!NOTE]
> - Gli endpoint di servizio non sono attualmente supportati per le app Web che usano IP virtuale (VIP) IP Secure Sockets Layer (SSL).
>
#### <a name="set-a-service-tag-based-rule"></a>Impostare una regola basata su tag di servizio

* Nel passaggio 4 Selezionare **tag servizio** nell'elenco a discesa **tipo** .

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-service-tag-add.png?v2" alt-text="Screenshot del riquadro ' Aggiungi restrizione ' con il tipo di tag del servizio selezionato.":::

Ogni tag di servizio rappresenta un elenco di intervalli di indirizzi IP dai servizi di Azure. Un elenco di questi servizi e collegamenti a intervalli specifici è disponibile nella [documentazione relativa ai tag del servizio][servicetags].

Tutti i tag di servizio disponibili sono supportati nelle regole di restrizione di accesso. Per semplicità, solo un elenco dei tag più comuni è disponibile tramite il portale di Azure. Usare modelli di Azure Resource Manager o script per configurare regole più avanzate come le regole con ambito regionale. Questi sono i tag disponibili tramite portale di Azure:

* ActionGroup
* ApplicationInsightsAvailability
* AzureCloud
* AzureCognitiveSearch
* AzureEventGrid
* AzureFrontDoor.Backend
* AzureMachineLearning
* AzureTrafficManager
* LogicApps

### <a name="edit-a-rule"></a>Modificare una regola

1. Per iniziare a modificare una regola di restrizione dell'accesso esistente, nella pagina **restrizioni di accesso** selezionare la regola che si desidera modificare.

1. Nel riquadro **Modifica restrizione di accesso** apportare le modifiche e quindi selezionare **Aggiorna regola**. Le modifiche hanno effetto immediato, incluse le modifiche nell'ordine di priorità.

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-ip-edit.png?v2" alt-text="Screenshot del riquadro ' Modifica restrizione di accesso ' nel portale di Azure, che mostra i campi di una regola di restrizione dell'accesso esistente.":::

   > [!NOTE]
   > Quando si modifica una regola, non è possibile spostarsi tra i tipi di regole. 

### <a name="delete-a-rule"></a>Eliminare una regola

Per eliminare una regola, nella pagina **restrizioni di accesso** selezionare i puntini di sospensione (**...**) accanto alla regola che si desidera eliminare, quindi selezionare **Rimuovi**.

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-delete.png" alt-text="Screenshot della pagina &quot;restrizioni di accesso&quot;, che mostra i puntini di sospensione &quot;Rimuovi&quot; accanto alla regola di restrizione dell'accesso da eliminare.":::

## <a name="access-restriction-advanced-scenarios"></a>Scenari avanzati di restrizione dell'accesso
Le sezioni seguenti descrivono alcuni scenari avanzati che usano restrizioni di accesso.

### <a name="filter-by-http-header"></a>Filtra per intestazione http

Come parte di una regola, è possibile aggiungere altri filtri di intestazione HTTP. Sono supportati i nomi di intestazione HTTP seguenti:
* X-Forwarded-For
* X-Forwarded-Host
* X-Azure-FDID
* X-FD-HealthProbe

Per ogni nome di intestazione è possibile aggiungere fino a 8 valori separati da virgola. I filtri di intestazione HTTP vengono valutati dopo la regola stessa ed è necessario che entrambe le condizioni siano vere affinché la regola venga applicata.

### <a name="multi-source-rules"></a>Regole multiorigine

Le regole di più origini consentono di combinare fino a 8 intervalli IP o 8 tag del servizio in una singola regola. Questa operazione può essere utilizzata se si dispone di più di 512 intervalli di indirizzi IP o si desidera creare regole logiche in cui più intervalli IP vengono combinati con un singolo filtro di intestazione HTTP.

Le regole di più origini sono definite nello stesso modo in cui si definiscono le regole di origine singola, ma con ogni intervallo separato con la virgola.

Esempio di PowerShell:

  ```azurepowershell-interactive
  Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName" `
    -Name "Multi-source rule" -IpAddress "192.168.1.0/24,192.168.10.0/24,192.168.100.0/24" `
    -Priority 100 -Action Allow
  ```

### <a name="block-a-single-ip-address"></a>Blocca un singolo indirizzo IP

Quando si aggiunge la prima regola di restrizione dell'accesso, il servizio aggiunge una regola esplicita *Deny All* con una priorità di 2147483647. In pratica, la regola esplicita *Deny All* è la regola finale da eseguire e blocca l'accesso a qualsiasi indirizzo IP non consentito in modo esplicito da una regola di *autorizzazione* .

Per uno scenario in cui si desidera bloccare in modo esplicito un singolo indirizzo IP o un blocco di indirizzi IP, ma consentire l'accesso a tutti gli altri elementi, aggiungere una regola *Consenti tutto* esplicitamente.

:::image type="content" source="media/app-service-ip-restrictions/block-single-address.png" alt-text="Screenshot della pagina &quot;restrizioni di accesso&quot; nella portale di Azure, che mostra un singolo indirizzo IP bloccato.":::

### <a name="restrict-access-to-an-scm-site"></a>Limitazione dell'accesso a un sito SCM 

Oltre a essere in grado di controllare l'accesso all'app, è possibile limitare l'accesso al sito SCM usato dall'app. Il sito SCM è sia l'endpoint di distribuzione Web che la console Kudu. È possibile assegnare restrizioni di accesso al sito SCM dall'app separatamente oppure usare lo stesso set di restrizioni sia per l'app che per il sito SCM. Quando si selezionano le **stesse restrizioni \<app name>** della casella di controllo, tutti gli elementi sono vuoti. Se si deseleziona la casella di controllo, le impostazioni del sito SCM verranno riapplicate. 

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-scm-browse.png" alt-text="Screenshot della pagina &quot;restrizioni di accesso&quot; nella portale di Azure, che indica che non è stata impostata alcuna restrizione di accesso per il sito SCM o l'app.":::

### <a name="restrict-access-to-a-specific-azure-front-door-instance"></a>Limitare l'accesso a un'istanza di Azure front door specifica
Il traffico dalla porta anteriore di Azure all'applicazione ha origine da un set noto di intervalli di indirizzi IP definiti nel tag del servizio AzureFrontDoor. backend. Usando una regola di restrizione dei tag del servizio, è possibile limitare il traffico solo a originare da Azure front door. Per garantire che il traffico provenga solo da un'istanza specifica, sarà necessario filtrare ulteriormente le richieste in ingresso in base all'intestazione HTTP univoca inviata da Azure front door.

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-frontdoor.png?v2" alt-text="Screenshot della pagina &quot;restrizioni di accesso&quot; nella portale di Azure, che Mostra come aggiungere una restrizione di Azure front door.":::

Esempio di PowerShell:

  ```azurepowershell-interactive
  $afd = Get-AzFrontDoor -Name "MyFrontDoorInstanceName"
  Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName" `
    -Name "Front Door example rule" -Priority 100 -Action Allow -ServiceTag AzureFrontDoor.Backend `
    -HttpHeader @{'x-azure-fdid' = $afd.FrontDoorId}
  ```

## <a name="manage-access-restriction-rules-programmatically"></a>Gestire le regole di restrizione dell'accesso a livello di codice

È possibile aggiungere restrizioni di accesso a livello di codice effettuando una delle operazioni seguenti: 

* Usare [l'interfaccia della riga di comando di Azure](/cli/azure/webapp/config/access-restriction). Ad esempio:
   
  ```azurecli-interactive
  az webapp config access-restriction add --resource-group ResourceGroup --name AppName \
    --rule-name 'IP example rule' --action Allow --ip-address 122.133.144.0/24 --priority 100
  ```

* Usare [Azure PowerShell](/powershell/module/Az.Websites/Add-AzWebAppAccessRestrictionRule). Ad esempio:


  ```azurepowershell-interactive
  Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName"
      -Name "Ip example rule" -Priority 100 -Action Allow -IpAddress 122.133.144.0/24
  ```
   > [!NOTE]
   > L'uso di tag di servizio, intestazioni HTTP o regole di più origini richiede almeno la versione 5.7.0. È possibile verificare la versione del modulo installato con: **Get-InstalledModule-Name AZ**

È anche possibile impostare manualmente i valori eseguendo una delle operazioni seguenti:

* Usare un'operazione di inserimento dell' [API REST di Azure](/rest/api/azure/) nella configurazione dell'app in Azure Resource Manager. Il percorso di queste informazioni in Azure Resource Manager è:

  management.azure.com/subscriptions/**ID sottoscrizione**/resourceGroups/**gruppi di risorse**/Providers/Microsoft.Web/sites/**nome app Web**/config/Web? API-Version = 2020-06-01

* Usare un modello di Gestione risorse. Come esempio, è possibile usare resources.azure.com e modificare il blocco ipSecurityRestrictions per aggiungere il JSON necessario.

  La sintassi JSON per l'esempio precedente è:

  ```json
  {
    "properties": {
      "ipSecurityRestrictions": [
        {
          "ipAddress": "122.133.144.0/24",
          "action": "Allow",
          "priority": 100,
          "name": "IP example rule"
        }
      ]
    }
  }
  ```
  La sintassi JSON per un esempio avanzato usando il tag del servizio e la restrizione dell'intestazione HTTP è:
  ```json
  {
    "properties": {
      "ipSecurityRestrictions": [
        {
          "ipAddress": "AzureFrontDoor.Backend",
          "tag": "ServiceTag",
          "action": "Allow",
          "priority": 100,
          "name": "Azure Front Door example",
          "headers": {
            "x-azure-fdid": [
              "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
            ]
          }
        }
      ]
    }
  }
  ```
## <a name="set-up-azure-functions-access-restrictions"></a>Configurare le restrizioni di accesso alle funzioni di Azure

Le restrizioni di accesso sono disponibili anche per le app per le funzioni con le stesse funzionalità dei piani di servizio app. Quando si abilitano le restrizioni di accesso, si disabilita anche l'editor di codice portale di Azure per gli indirizzi IP non consentiti.

## <a name="next-steps"></a>Passaggi successivi
[Limitazioni di accesso per funzioni di Azure](../azure-functions/functions-networking-options.md#inbound-access-restrictions)  
[Integrazione del gateway applicazione con gli endpoint di servizio](networking/app-gateway-with-service-endpoints.md)

<!--Links-->
[serviceendpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[servicetags]: ../virtual-network/service-tags-overview.md