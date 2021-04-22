---
title: Configurare la regola WAF di restrizione IP per Frontdoor di Azure
description: Informazioni su come configurare una regola di Web Application Firewall per limitare gli indirizzi IP per un endpoint Frontdoor di Azure esistente.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 12/22/2020
ms.author: tyao
ms.openlocfilehash: 32bf7a5ecc93fa23c8c704dc346048c26c086121
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107860852"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door"></a>Configurare una regola di restrizione IP con un Web application firewall per Frontdoor di Azure

Questo articolo illustra come configurare le regole di restrizione IP in un web application firewall (WAF) per Frontdoor di Azure usando l'interfaccia della riga di comando di portale di Azure, l'interfaccia della riga di comando di Azure, Azure PowerShell o un modello Azure Resource Manager.

Una regola di controllo di accesso basata su indirizzo IP è una regola WAF personalizzata che consente di controllare l'accesso alle applicazioni Web. A tale scopo, specifica un elenco di indirizzi IP o intervalli di indirizzi IP in formato CIDR (Classless Inter-Domain Routing). Esistono due tipi di variabili di corrispondenza nella corrispondenza degli indirizzi IP, **RemoteAddr** **e SocketAddr.** RemoteAddr è l'indirizzo IP del client originale che viene in genere inviato tramite l'intestazione della richiesta X-Forwarded-For. SocketAddr è l'indirizzo IP di origine visualizzato da WAF. Se l'utente è dietro un proxy, SocketAddr è spesso l'indirizzo del server proxy.

Per impostazione predefinita, l'applicazione Web è accessibile da Internet. Se si vuole limitare l'accesso ai client da un elenco di indirizzi IP noti o intervalli di indirizzi IP, è possibile creare una regola di corrispondenza IP che contiene l'elenco di indirizzi IP come valori corrispondenti e imposta l'operatore su "Not" (negazione true) e l'azione **su Blocca**. Dopo l'applicazione di una regola di restrizione IP, le richieste provenienti da indirizzi esterni a questo elenco di indirizzi consentiti ricevono una risposta 403 - Accesso negato.

## <a name="configure-a-waf-policy-with-the-azure-portal"></a>Configurare un criterio WAF con il portale di Azure

### <a name="prerequisites"></a>Prerequisiti

Creare un profilo Frontdoor di Azure seguendo le istruzioni descritte in Avvio rapido: Creare una front door per [un'applicazione Web globale a disponibilità elevata.](../../frontdoor/quickstart-create-front-door.md)

### <a name="create-a-waf-policy"></a>Creare un criterio WAF

1. Nella portale di Azure selezionare **Crea** una risorsa, digitare Web **application firewall** nella casella di ricerca e quindi selezionare Web Application **Firewall (WAF).**
2. Selezionare **Crea**.
3. Nella pagina **Crea un criterio WAF** usare i valori seguenti per completare la **scheda Informazioni di** base:
   
   |Impostazione  |Valore  |
   |---------|---------|
   |Criteri per     |WAF globale (front door)|
   |Subscription     |Selezionare la propria sottoscrizione|
   |Resource group     |Selezionare il gruppo di risorse in cui si trova front door.|
   |Nome criteri     |Digitare un nome per i criteri|
   |Stato criteri     |Abilitato|

   Selezionare **Avanti: Impostazioni dei criteri**

1. Nella scheda **Impostazioni criteri** selezionare **Prevenzione**. Per Il **corpo della risposta Blocca** digitare *You've been blocked!* in modo da vedere che la regola personalizzata è in vigore.
2. Selezionare **Avanti: Regole gestite**.
3. Selezionare **Avanti: Regole personalizzate**.
4. Selezionare **Aggiungi regola personalizzata.**
5. Nella pagina **Aggiungi regola personalizzata** usare i valori di test seguenti per creare una regola personalizzata:

   |Impostazione  |Valore  |
   |---------|---------|
   |Nome regola personalizzata     |FdWafCustRule|
   |Stato     |Abilitato|
   |Tipo regola     |Corrispondenza|
   |Priorità    |100|
   |Tipo di corrispondenza     |indirizzo IP|
   |Variabile di corrispondenza|RemoteAddr|
   |Operazione|Non contiene|
   |Indirizzo IP o intervallo|10.10.10.0/24|
   |Risultato|Negare il traffico|

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/custom-rule.png" alt-text="Regola personalizzata":::

   Selezionare **Aggiungi**.
6. Selezionare **Avanti: Associazione**.
7. Selezionare **Aggiungi host front-end.**
8. Per **Host front-end** selezionare l'host front-end e selezionare **Aggiungi.**
9. Selezionare **Rivedi e crea**.
10. Dopo aver superato la convalida dei criteri, selezionare **Crea.**

### <a name="test-your-waf-policy"></a>Testare i criteri WAF

1. Al termine della distribuzione dei criteri WAF, passare al nome host front-end front-end di Front door.
2. Verrà visualizzato il messaggio di blocco personalizzato.

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/waf-rule-test.png" alt-text="Test delle regole WAF":::

   > [!NOTE]
   > Nella regola personalizzata è stato intenzionalmente usato un indirizzo IP privato per garantire l'attivazione della regola. In una distribuzione effettiva creare regole *di autorizzazione* *e negazione* usando gli indirizzi IP per una situazione specifica.

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>Configurare un criterio WAF con l'interfaccia della riga di comando di Azure

### <a name="prerequisites"></a>Prerequisiti
Prima di iniziare a configurare un criterio di restrizione IP, configurare l'ambiente dell'interfaccia della riga di comando e creare un profilo Frontdoor di Azure ip.

#### <a name="set-up-the-azure-cli-environment"></a>Configurare l'ambiente dell'interfaccia della riga di comando di Azure
1. Installare [l'interfaccia della riga di](/cli/azure/install-azure-cli)comando di Azure o Azure Cloud Shell. Azure Cloud Shell è una shell Bash gratuita che è possibile eseguire direttamente nel portale di Azure. Include l'interfaccia della riga di comando di Azure preinstallata e configurata per l'uso con l'account. Selezionare il **pulsante Prova nei** comandi dell'interfaccia della riga di comando seguenti e quindi accedere all'account Azure nella sessione Cloud Shell visualizzata. Dopo l'avvio della sessione, immettere `az extension add --name front-door` per aggiungere l'Frontdoor di Azure predefinita.
 2. Se si usa l'interfaccia della riga di comando in locale in Bash, accedere ad Azure usando `az login` .

#### <a name="create-an-azure-front-door-profile"></a>Creare un profilo Frontdoor di Azure personalizzato
Creare un profilo Frontdoor di Azure seguendo le istruzioni descritte in Avvio rapido: Creare una front door per [un'applicazione Web globale a disponibilità elevata.](../../frontdoor/quickstart-create-front-door.md)

### <a name="create-a-waf-policy"></a>Creare un criterio WAF

Creare un criterio WAF usando il [comando az network front-door waf-policy create.](/cli/azure/network/front-door/waf-policy#az_network_front_door_waf_policy_create) Nell'esempio seguente sostituire il nome del criterio *IPAllowPolicyExampleCLI* con un nome di criterio univoco.

```azurecli-interactive 
az network front-door waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>Aggiungere una regola di controllo di accesso IP personalizzata

Usare [il comando az network front-door waf-policy custom-rule create](/cli/azure/network/front-door/waf-policy/rule#az_network_front_door_waf_policy_rule_create) per aggiungere una regola di controllo di accesso IP personalizzata per i criteri WAF appena creati.

Negli esempi seguenti:
-  Sostituire *IPAllowPolicyExampleCLI* con i criteri univoci creati in precedenza.
-  Sostituire *ip-address-range-1*, *ip-address-range-2* con il proprio intervallo.

Creare prima di tutto una regola di autorizzazione IP per i criteri creati nel passaggio precedente. 
> [!NOTE]
> **--defer** è obbligatorio perché una regola deve avere una condizione di corrispondenza da aggiungere nel passaggio successivo.

```azurecli
az network front-door waf-policy rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI --defer
```
Aggiungere quindi la condizione di corrispondenza alla regola:

```azurecli
az network front-door waf-policy rule match-condition add \
--match-variable RemoteAddr \
--operator IPMatch \
--values "ip-address-range-1" "ip-address-range-2" \
--negate true \
--name IPAllowListRule \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI 
  ```
                                                   
### <a name="find-the-id-of-a-waf-policy"></a>Trovare l'ID di un criterio WAF 
Trovare l'ID di un criterio WAF usando il [comando az network front-door waf-policy show.](/cli/azure/network/front-door/waf-policy#az_network_front_door_waf_policy_show) Sostituire *IPAllowPolicyExampleCLI* nell'esempio seguente con i criteri univoci creati in precedenza.

   ```azurecli
   az network front-door  waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>Collegare un criterio WAF a un Frontdoor di Azure front-end

Impostare l'ID *Frontdoor di Azure WebApplicationFirewallPolicyLink* sull'ID criteri usando il [comando az network front-door update.](/cli/azure/network/front-door#az_network_front_door_update) Sostituire *IPAllowPolicyExampleCLI* con i criteri univoci creati in precedenza.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/resource-group-name/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
In questo esempio i criteri WAF vengono applicati **a FrontendEndpoints[0]**. È possibile collegare i criteri WAF a qualsiasi front-end.
> [!Note]
> È necessario impostare la proprietà **WebApplicationFirewallPolicyLink** una sola volta per collegare un criterio WAF a Frontdoor di Azure front-end. Gli aggiornamenti successivi dei criteri vengono applicati automaticamente al front-end.

## <a name="configure-a-waf-policy-with-azure-powershell"></a>Configurare un criterio WAF con Azure PowerShell

### <a name="prerequisites"></a>Prerequisiti
Prima di iniziare a configurare i criteri di restrizione IP, configurare l'ambiente di PowerShell e creare un profilo Frontdoor di Azure ip.

#### <a name="set-up-your-powershell-environment"></a>Configurare l'ambiente PowerShell
Azure PowerShell un set di cmdlet che usano il modello Azure Resource Manager [per](../../azure-resource-manager/management/overview.md) la gestione delle risorse di Azure.

È possibile installare [Azure PowerShell](/powershell/azure/) nel computer locale e usarlo in qualsiasi sessione di PowerShell. Seguire le istruzioni nella pagina per accedere a PowerShell usando le credenziali di Azure e quindi installare il modulo Az.

1. Connettersi ad Azure usando il comando seguente e quindi usare una finestra di dialogo interattiva per accedere.
    ```
    Connect-AzAccount
    ```
 2. Prima di installare un Frontdoor di Azure, assicurarsi di avere installato la versione corrente del modulo PowerShellGet. Eseguire il comando seguente e quindi riaprire PowerShell.

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. Installare il modulo Az.FrontDoor usando il comando seguente. 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-profile"></a>Creare un profilo Frontdoor di Azure personalizzato
Creare un profilo Frontdoor di Azure seguendo le istruzioni descritte in Avvio rapido: Creare una front door per [un'applicazione Web globale a disponibilità elevata.](../../frontdoor/quickstart-create-front-door.md)

### <a name="define-an-ip-match-condition"></a>Definire una condizione di corrispondenza IP
Usare il [comando New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) per definire una condizione di corrispondenza IP.
Nell'esempio seguente sostituire *ip-address-range-1*, *ip-address-range-2* con il proprio intervallo.    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue "ip-address-range-1", "ip-address-range-2"
-NegateCondition 1
```
     
### <a name="create-a-custom-ip-allow-rule"></a>Creare una regola di autorizzazione IP personalizzata

Usare il [comando New-AzFrontDoorWafCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) per definire un'azione e impostare una priorità. Nell'esempio seguente le richieste non provenienti da INDIRIZZI IP client che corrispondono all'elenco verranno bloccate.

```azurepowershell
$IPAllowRule = New-AzFrontDoorWafCustomRuleObject `
-Name "IPAllowRule" `
-RuleType MatchRule `
-MatchCondition $IPMatchCondition `
-Action Block -Priority 1
```

### <a name="configure-a-waf-policy"></a>Configurare un criterio WAF
Trovare il nome del gruppo di risorse che contiene il Frontdoor di Azure usando `Get-AzResourceGroup` . Configurare quindi un criterio WAF con la regola IP usando [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```azurepowershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule`
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>Collegare un criterio WAF a un host Frontdoor di Azure front-end

Collegare un oggetto criteri WAF a un host front-end esistente e aggiornare Frontdoor di Azure proprietà. Recuperare prima di tutto Frontdoor di Azure o object usando [Get-AzFrontDoor.](/powershell/module/Az.FrontDoor/Get-AzFrontDoor) Impostare quindi la **proprietà WebApplicationFirewallPolicyLink** sull'ID risorsa di *$IPAllowPolicyExamplePS*, creato nel passaggio precedente, usando il [comando Set-AzFrontDoor.](/powershell/module/Az.FrontDoor/Set-AzFrontDoor)

```azurepowershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> In questo esempio i criteri WAF vengono applicati **a FrontendEndpoints[0]**. È possibile collegare un criterio WAF a qualsiasi front-end. È necessario impostare la proprietà **WebApplicationFirewallPolicyLink** una sola volta per collegare un criterio WAF a Frontdoor di Azure front-end. Gli aggiornamenti successivi dei criteri vengono applicati automaticamente al front-end.


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>Configurare un criterio WAF con un Resource Manager predefinito
Per visualizzare il modello che crea un criterio Frontdoor di Azure e un criterio WAF con regole di restrizione IP personalizzate, passare a [GitHub.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip)


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare un profilo Frontdoor di Azure.](../../frontdoor/quickstart-create-front-door.md)
