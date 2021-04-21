---
title: Servizio app di Azure restrizioni di accesso
description: Informazioni su come proteggere l'app in Servizio app di Azure impostando restrizioni di accesso.
author: ccompy
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.topic: article
ms.date: 12/17/2020
ms.author: ccompy
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: 541af6d0051d06de5721b22616fbf1e2867b71d6
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833365"
---
# <a name="set-up-azure-app-service-access-restrictions"></a>Configurare le Servizio app di Azure di accesso

Configurando le restrizioni di accesso, è possibile definire un elenco di autorizzazioni/rifiuto ordinato con priorità che controlla l'accesso alla rete all'app. L'elenco può includere indirizzi IP o subnet di rete virtuale di Azure. Quando sono presenti una o più voci, alla fine dell'elenco esiste una negazione *implicita* di tutte le voci.

La funzionalità di restrizione dell'accesso funziona con tutti Servizio app di Azure carichi di lavoro ospitati. I carichi di lavoro possono includere app Web, app per le API, app Linux, app contenitore Linux e Funzioni.

Quando viene effettuata una richiesta all'app, l'indirizzo FROM viene valutato in base alle regole nell'elenco delle restrizioni di accesso. Se l'indirizzo FROM si trova in una subnet configurata con endpoint di servizio per Microsoft.Web, la subnet di origine viene confrontata con le regole di rete virtuale nell'elenco delle restrizioni di accesso. Se all'indirizzo non è consentito l'accesso in base alle regole nell'elenco, il servizio risponde con un [codice di stato HTTP 403.](https://en.wikipedia.org/wiki/HTTP_403)

La funzionalità di restrizione dell'accesso viene implementata nei ruoli front-end del servizio app, che sono upstream degli host di lavoro in cui viene eseguito il codice. Di conseguenza, le restrizioni di accesso sono in effetti elenchi di controllo di accesso (ACL) di rete.

La possibilità di limitare l'accesso all'app Web da una rete virtuale di Azure è abilitata dagli [endpoint di servizio][serviceendpoints]. Con gli endpoint di servizio è possibile limitare l'accesso a un servizio multi-tenant dalle subnet selezionate. Non funziona per limitare il traffico alle app ospitate in un ambiente del servizio app. Se si ha un'ambiente del servizio app, è possibile controllare l'accesso all'app applicando regole di indirizzi IP.

> [!NOTE]
> Gli endpoint di servizio devono essere abilitati sia sul lato rete che per il servizio di Azure con cui vengono abilitati. Per un elenco dei servizi di Azure che supportano gli endpoint di servizio, vedere [Endpoint di servizio di rete virtuale](../virtual-network/virtual-network-service-endpoints-overview.md).
>

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-flow.png" alt-text="Diagramma del flusso delle restrizioni di accesso.":::

## <a name="manage-access-restriction-rules-in-the-portal"></a>Gestire le regole di restrizione dell'accesso nel portale

Per aggiungere una regola di restrizione dell'accesso all'app, seguire questa procedura:

1. Accedere al portale di Azure.

1. Nel riquadro sinistro selezionare **Rete.**

1. Nel riquadro **Rete** in Restrizioni **di accesso** selezionare Configura restrizioni **di accesso.**

    :::image type="content" source="media/app-service-ip-restrictions/access-restrictions.png" alt-text="Screenshot del riquadro delle opzioni di rete del servizio app nella portale di Azure.":::

1. Nella pagina **Restrizioni di accesso** esaminare l'elenco delle regole di restrizione dell'accesso definite per l'app.

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-browse.png" alt-text="Screenshot della pagina Restrizioni di accesso nel portale di Azure, che mostra l'elenco delle regole di restrizione dell'accesso definite per l'app selezionata.":::

   L'elenco visualizza tutte le restrizioni correnti applicate all'app. Se si dispone di una restrizione di rete virtuale per l'app, la tabella indica se gli endpoint di servizio sono abilitati per Microsoft.Web. Se nell'app non sono definite restrizioni, l'app è accessibile da qualsiasi posizione.

### <a name="add-an-access-restriction-rule"></a>Aggiungere una regola di restrizione dell'accesso

Per aggiungere una regola di restrizione dell'accesso all'app, nel riquadro **Restrizioni di** accesso selezionare **Aggiungi regola.** Dopo l'aggiunta, una regola diventa effettiva immediatamente. 

Le regole vengono applicate in ordine di priorità, a partire dal numero più basso nella **colonna** Priorità . Un nega *tutto implicito* è attivo dopo aver aggiunto anche una singola regola.

Quando si **crea una** regola, nel riquadro Aggiungi restrizione di accesso eseguire le operazioni seguenti:

1. In **Azione** selezionare Consenti **o** **Nega.**  

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-ip-add.png?v2" alt-text="Screenshot del riquadro &quot;Aggiungi restrizione di accesso&quot;.":::

1. Facoltativamente, immettere un nome e una descrizione della regola.
1. Nella casella **Priorità** immettere un valore di priorità.
1. **Nell'elenco** a discesa Tipo selezionare il tipo di regola.

I diversi tipi di regole sono descritti nelle sezioni seguenti.

> [!NOTE]
> - È previsto un limite di 512 regole di restrizione dell'accesso. Se sono necessarie più di 512 regole di restrizione dell'accesso, è consigliabile installare un prodotto di sicurezza autonomo, ad esempio Frontdoor di Azure, app Azure Gateway o un WAF alternativo.
>
#### <a name="set-an-ip-address-based-rule"></a>Impostare una regola basata su indirizzi IP

Seguire la procedura descritta nella sezione precedente, ma con l'aggiunta seguente:
* Per il passaggio 4, **nell'elenco** a discesa Tipo selezionare **IPv4** o **IPv6.** 

Specificare il **blocco di indirizzi IP** nella notazione CIDR (Classless Inter-Domain Routing) per gli indirizzi IPv4 e IPv6. Per specificare un indirizzo, è possibile usare un valore simile a *1.2.3.4/32,* dove i primi quattro ottetti rappresentano l'indirizzo IP e */32* è la maschera. La notazione CIDR IPv4 per tutti gli indirizzi è 0.0.0.0/0. Per altre informazioni sulla notazione CIDR, vedere [Classless Inter-Domain Routing](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). 

#### <a name="set-a-service-endpoint-based-rule"></a>Impostare una regola basata su endpoint di servizio

* Per il passaggio 4, **nell'elenco a** discesa Tipo selezionare **Rete virtuale**.

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-vnet-add.png?v2" alt-text="Screenshot del riquadro &quot;Aggiungi restrizione&quot; con il tipo di rete virtuale selezionato.":::

Specificare gli **elenchi a** **discesa** Sottoscrizione, Rete virtuale **e Subnet,** corrispondenti agli elementi a cui si vuole limitare l'accesso.

Usando gli endpoint di servizio, è possibile limitare l'accesso alle subnet di rete virtuale di Azure selezionate. Se gli endpoint di servizio non sono già abilitati con Microsoft.Web per la subnet selezionata, verranno abilitati automaticamente a meno che non si seleziona la casella di controllo Ignora endpoint di servizio **Microsoft.Web** mancanti. Lo scenario in cui è possibile abilitare gli endpoint di servizio nell'app ma non nella subnet dipende principalmente dal fatto che si dispone delle autorizzazioni per abilitarli nella subnet. 

Se è necessario che un altro utente abiliti gli endpoint di servizio nella subnet, selezionare la casella di controllo Ignora endpoint servizio **Microsoft.Web** mancanti. L'app verrà configurata per gli endpoint di servizio in previsione che siano abilitati in un secondo momento nella subnet. 

Non è possibile usare gli endpoint di servizio per limitare l'accesso alle app eseguite in un ambiente del servizio app. Quando l'app si trova in ambiente del servizio app, è possibile controllarvi l'accesso applicando regole di accesso IP. 

Con gli endpoint di servizio è possibile configurare l'app con gateway applicazione o altri web application firewall (WAF). È anche possibile configurare applicazioni multilivello con back-end sicuri. Per altre informazioni, vedere Funzionalità [di rete e Integrazione del servizio app](networking-features.md) e del gateway applicazione con gli endpoint di [servizio.](networking/app-gateway-with-service-endpoints.md)

> [!NOTE]
> - Gli endpoint di servizio non sono attualmente supportati per le app Web che usano IP Secure Sockets Layer (IP virtuale SSL).
>
#### <a name="set-a-service-tag-based-rule"></a>Impostare una regola basata su tag di servizio

* Per il passaggio 4, **nell'elenco** a discesa Tipo selezionare **Tag di servizio**.

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-service-tag-add.png?v2" alt-text="Screenshot del riquadro &quot;Aggiungi restrizione&quot; con il tipo di tag di servizio selezionato.":::

Ogni tag di servizio rappresenta un elenco di intervalli IP dai servizi di Azure. Un elenco di questi servizi e i collegamenti agli intervalli specifici sono disponibili nella documentazione [relativa ai tag di servizio][servicetags].

Tutti i tag di servizio disponibili sono supportati nelle regole di restrizione dell'accesso. Per semplicità, è disponibile solo un elenco dei tag più comuni tramite il portale di Azure. Usare Azure Resource Manager modelli o script per configurare regole più avanzate, ad esempio le regole con ambito a livello di area. Questi sono i tag disponibili tramite portale di Azure:

* ActionGroup
* ApplicationInsightsAvailability
* AzureCloud
* AzureCognitiveSearch
* Griglia di eventi di Azure
* AzureFrontDoor.Backend
* AzureMachineLearning
* AzureTrafficManager
* LogicApps

### <a name="edit-a-rule"></a>Modificare una regola

1. Per iniziare a modificare una regola di restrizione dell'accesso esistente, nella pagina **Restrizioni di** accesso selezionare la regola da modificare.

1. Nel riquadro **Modifica restrizione di** accesso apportare le modifiche e quindi selezionare **Aggiorna regola.** Le modifiche sono effettive immediatamente, incluse le modifiche nell'ordine di priorità.

   :::image type="content" source="media/app-service-ip-restrictions/access-restrictions-ip-edit.png?v2" alt-text="Screenshot del riquadro &quot;Modifica restrizione di accesso&quot; nella portale di Azure, che mostra i campi per una regola di restrizione di accesso esistente.":::

   > [!NOTE]
   > Quando si modifica una regola, non è possibile passare da un tipo di regola all'altro. 

### <a name="delete-a-rule"></a>Eliminare una regola

Per eliminare una regola, nella pagina Restrizioni di accesso selezionare i puntini di sospensione (**...**) accanto alla regola da eliminare e quindi **selezionare Rimuovi**. 

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-delete.png" alt-text="Screenshot della pagina &quot;Restrizioni di accesso&quot;, che mostra i puntini di sospensione &quot;Rimuovi&quot; accanto alla regola di restrizione di accesso da eliminare.":::

## <a name="access-restriction-advanced-scenarios"></a>Scenari avanzati di restrizione dell'accesso
Le sezioni seguenti descrivono alcuni scenari avanzati che usano restrizioni di accesso.

### <a name="filter-by-http-header"></a>Filtrare in base all'intestazione HTTP

Come parte di qualsiasi regola, è possibile aggiungere filtri di intestazione HTTP aggiuntivi. Sono supportati i nomi di intestazione HTTP seguenti:
* X-Forwarded-For
* X-Forwarded-Host
* X-Azure-FDID
* X-FD-HealthProbe

Per ogni nome di intestazione è possibile aggiungere fino a 8 valori separati da virgola. I filtri di intestazione HTTP vengono valutati dopo la regola stessa ed entrambe le condizioni devono essere true per l'applicazione della regola.

### <a name="multi-source-rules"></a>Regole multi-origine

Le regole multi-origine consentono di combinare fino a 8 intervalli IP o 8 tag di servizio in una singola regola. È possibile usarlo se si hanno più di 512 intervalli IP o si vogliono creare regole logiche in cui più intervalli IP vengono combinati con un singolo filtro di intestazione HTTP.

Le regole multi-origine vengono definite nello stesso modo in cui si definiscono le regole a origine singola, ma con ogni intervallo separato da virgola.

Esempio di PowerShell:

  ```azurepowershell-interactive
  Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName" `
    -Name "Multi-source rule" -IpAddress "192.168.1.0/24,192.168.10.0/24,192.168.100.0/24" `
    -Priority 100 -Action Allow
  ```

### <a name="block-a-single-ip-address"></a>Bloccare un singolo indirizzo IP

Quando si aggiunge la prima regola di restrizione dell'accesso, il servizio aggiunge una regola esplicita *Nega* tutto con priorità 2147483647. In pratica, la regola *esplicita Nega* tutto è la regola finale da eseguire e blocca l'accesso a qualsiasi indirizzo IP non esplicitamente consentito da una *regola Consenti.*

Per uno scenario in cui si vuole bloccare in modo esplicito un singolo indirizzo IP o un blocco di indirizzi IP, ma consentire l'accesso a tutti gli altri elementi, aggiungere una regola *esplicita Consenti tutto.*

:::image type="content" source="media/app-service-ip-restrictions/block-single-address.png" alt-text="Screenshot della pagina &quot;Restrizioni di accesso&quot; nel portale di Azure, che mostra un singolo indirizzo IP bloccato.":::

### <a name="restrict-access-to-an-scm-site"></a>Limitare l'accesso a un sito SCM 

Oltre a poter controllare l'accesso all'app, è possibile limitare l'accesso al sito SCM usato dall'app. Il sito di Gestione controllo servizi è sia l'endpoint di distribuzione Web che la console Kudu. È possibile assegnare le restrizioni di accesso al sito SCM dall'app separatamente o usare lo stesso set di restrizioni sia per l'app che per il sito di Gestione controllo servizi. Quando si seleziona la **casella di controllo Same restrictions as \<app name>** (Stesse restrizioni di), tutti gli elementi vengono vuoti. Se si deseleziona la casella di controllo, le impostazioni del sito di Gestione controllo servizi vengono riapplicate. 

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-scm-browse.png" alt-text="Screenshot della pagina &quot;Restrizioni di accesso&quot; nel portale di Azure, che mostra che non sono impostate restrizioni di accesso per il sito di Gestione controllo servizi o l'app.":::

### <a name="restrict-access-to-a-specific-azure-front-door-instance"></a>Limitare l'accesso a un'istanza Frontdoor di Azure specifica
Il traffico Frontdoor di Azure'applicazione proviene da un set noto di intervalli IP definiti nel tag di servizio AzureFrontDoor.Backend. Usando una regola di restrizione dei tag di servizio, è possibile limitare il traffico in modo che provena solo da Frontdoor di Azure. Per assicurarsi che il traffico provena solo dall'istanza specifica, è necessario filtrare ulteriormente le richieste in ingresso in base all'intestazione HTTP univoca Frontdoor di Azure inviati.

:::image type="content" source="media/app-service-ip-restrictions/access-restrictions-frontdoor.png?v2" alt-text="Screenshot della pagina &quot;Restrizioni di accesso&quot; nel portale di Azure, che mostra come aggiungere Frontdoor di Azure restrizioni.":::

Esempio di PowerShell:

  ```azurepowershell-interactive
  $afd = Get-AzFrontDoor -Name "MyFrontDoorInstanceName"
  Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName" `
    -Name "Front Door example rule" -Priority 100 -Action Allow -ServiceTag AzureFrontDoor.Backend `
    -HttpHeader @{'x-azure-fdid' = $afd.FrontDoorId}
  ```

## <a name="manage-access-restriction-rules-programmatically"></a>Gestire le regole di restrizione dell'accesso a livello di codice

È possibile aggiungere restrizioni di accesso a livello di codice eseguendo una delle operazioni seguenti: 

* Usare [l'interfaccia della riga di comando di Azure.](/cli/azure/webapp/config/access-restriction) Ad esempio:
   
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
   > L'uso di tag di servizio, intestazioni HTTP o regole multi-origine richiede almeno la versione 5.7.0. È possibile verificare la versione del modulo installato con: **Get-InstalledModule -Name Az**

È anche possibile impostare i valori manualmente eseguendo una delle operazioni seguenti:

* Usare [un'operazione PUT dell'API REST](/rest/api/azure/) di Azure nella configurazione dell'app in Azure Resource Manager. Il percorso per queste informazioni in Azure Resource Manager è:

  management.azure.com/subscriptions/**id** sottoscrizione /resourceGroups/**gruppi** di risorse /providers/Microsoft.Web/sites/ nome **app** Web /config/web?api-version=2020-06-01

* Usare un Resource Manager modello. Come esempio, è possibile usare resources.azure.com e modificare il blocco ipSecurityRestrictions per aggiungere il JSON necessario.

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
  La sintassi JSON per un esempio avanzato che usa il tag di servizio e la restrizione dell'intestazione HTTP è:
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
## <a name="set-up-azure-functions-access-restrictions"></a>Configurare le Funzioni di Azure di accesso

Le restrizioni di accesso sono disponibili anche per le app per le funzioni con le stesse funzionalità dei piani di servizio app. Quando si abilitano le restrizioni di accesso, si disabilita anche l portale di Azure editor di codice per tutti gli IP non consentiti.

## <a name="next-steps"></a>Passaggi successivi
[Restrizioni di accesso per Funzioni di Azure](../azure-functions/functions-networking-options.md#inbound-access-restrictions)  
[Integrazione del gateway applicazione con gli endpoint di servizio](networking/app-gateway-with-service-endpoints.md)

<!--Links-->
[serviceendpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[servicetags]: ../virtual-network/service-tags-overview.md
