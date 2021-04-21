---
title: Connettere computer ibridi ad Azure tramite PowerShell
description: Questo articolo illustra come installare l'agente e connettere un computer ad Azure usando Azure Arc abilitati. È possibile farlo con PowerShell.
ms.date: 10/28/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d6963a53ac14c9d6727a8d53e781bc8b8389b76e
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831618"
---
# <a name="connect-hybrid-machines-to-azure-by-using-powershell"></a>Connettere computer ibridi ad Azure tramite PowerShell

Per i server abilitati Azure Arc, è possibile eseguire passaggi manuali per abilitarli per uno o più computer Windows o Linux nell'ambiente in uso. In alternativa, è possibile usare il cmdlet di PowerShell [Connect-AzConnectedMachine](/powershell/module/az.connectedmachine/remove-azconnectedmachine) per scaricare l'agente Connected Machine, installare l'agente e registrare il computer con Azure Arc. Il cmdlet scarica il pacchetto dell'agente Windows (Windows Installer) dall'Area download Microsoft e il pacchetto dell'agente Linux dal repository di pacchetti Microsoft.

Con questo metodo sono necessarie le autorizzazioni di amministratore sulla macchina virtuale per installare e configurare l'agente. In Linux, tramite l'account radice, e in Windows, si è membri del gruppo Administrators locale. È possibile completare questo processo in modo interattivo o remoto in un server Windows usando la comunicazione [remota di PowerShell.](/powershell/scripting/learn/ps101/08-powershell-remoting)

Prima di iniziare, esaminare i [prerequisiti e](agent-overview.md#prerequisites) verificare che la sottoscrizione e le risorse soddisfino i requisiti. Per informazioni sulle aree supportate e altre considerazioni correlate, vedere [Aree di Azure supportate.](overview.md#supported-regions)

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

- Un computer con Azure PowerShell. Per istruzioni, vedere [Installare e configurare Azure PowerShell](/powershell/azure/).

Usare PowerShell per gestire le estensioni macchina virtuale nei server ibridi gestiti Azure Arc server abilitati. Prima di usare PowerShell, installare il `Az.ConnectedMachine` modulo. Eseguire il comando seguente nel server abilitato con Azure Arc:

```powershell
Install-Module -Name Az.ConnectedMachine
```

Al termine dell'installazione, viene visualizzato il messaggio seguente:

`The installed extension ``Az.ConnectedMachine`` is experimental and not covered by customer support. Please use with discretion.`

## <a name="install-the-agent-and-connect-to-azure"></a>Installare l'agente e connettersi ad Azure

1. Aprire una console di PowerShell con privilegi elevati.

2. Accedere ad Azure eseguendo il comando `Connect-AzAccount` .

3. Per installare l'agente Connected Machine, `Connect-AzConnectedMachine` usare con i parametri , e `-Name` `-ResourceGroupName` `-Location` . Usare il `-SubscriptionId` parametro per eseguire l'override della sottoscrizione predefinita in seguito al contesto di Azure creato dopo l'accesso. Eseguire uno dei comandi seguenti:

    * Per installare l'agente Connected Machine nel computer di destinazione in grado di comunicare direttamente con Azure, eseguire:

        ```azurepowershell
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region>
        ```
    
    * Per installare l'agente Connected Machine nel computer di destinazione che comunica tramite un server proxy, eseguire:
        
        ```azurepowershell
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -Proxy http://<proxyURL>:<proxyport>
        ```

Se l'agente non si avvia dopo l'installazione, controllare i log per vedere le informazioni dettagliate sull'errore. In Windows controllare questo file: *%ProgramData%\AzureConnectedMachineAgent\Log\himds.log*. In Linux controllare questo file: */var/opt/azcmagent/log/himds.log*.

## <a name="install-and-connect-by-using-powershell-remoting"></a>Installare e connettersi usando la comunicazione remota di PowerShell

Ecco come configurare uno o più server Windows con server abilitati con Azure Arc. È necessario abilitare la comunicazione remota di PowerShell nel computer remoto. A tale scopo, utilizzare il cmdlet `Enable-PSRemoting`.

1. Aprire una console di PowerShell come amministratore.

2. Accedere ad Azure eseguendo il comando `Connect-AzAccount` .

3. Per installare l'agente Connected Machine, `Connect-AzConnectedMachine` usare con i parametri e `-ResourceGroupName` `-Location` . I nomi delle risorse di Azure useranno automaticamente il nome host di ogni server. Usare il `-SubscriptionId` parametro per eseguire l'override della sottoscrizione predefinita come risultato del contesto di Azure creato dopo l'accesso.

    * Per installare l'agente Connected Machine nel computer di destinazione che può comunicare direttamente con Azure, eseguire il comando seguente:
    
        ```azurepowershell
        $sessions = New-PSSession -ComputerName myMachineName
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Location <region> -PSSession $sessions
        ```
    
    * Per installare l'agente Connected Machine in più computer remoti contemporaneamente, aggiungere un elenco di nomi di computer remoti, ognuno separato da una virgola.

        ```azurepowershell
        $sessions = New-PSSession -ComputerName myMachineName1, myMachineName2, myMachineName3
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Location <region> -PSSession $sessions
        ```

    L'esempio seguente mostra i risultati del comando che ha come destinazione un singolo computer:
    
    ```azurepowershell
    time="2020-08-07T13:13:25-07:00" level=info msg="Onboarding Machine. It usually takes a few minutes to complete. Sometimes it may take longer depending on network and server load status."
    time="2020-08-07T13:13:25-07:00" level=info msg="Check network connectivity to all endpoints..."
    time="2020-08-07T13:13:29-07:00" level=info msg="All endpoints are available... continue onboarding"
    time="2020-08-07T13:13:50-07:00" level=info msg="Successfully Onboarded Resource to Azure" VM Id=f65bffc7-4734-483e-b3ca-3164bfa42941
    
    Name           Location OSName   Status     ProvisioningState
    ----           -------- ------   ------     -----------------
    myMachineName  eastus   windows  Connected  Succeeded
    ```

## <a name="verify-the-connection-with-azure-arc"></a>Verificare la connessione con Azure Arc

Dopo aver installato e configurato l'agente per la registrazione Azure Arc server abilitati, passare al portale di Azure per verificare che il server sia stato connesso correttamente. Visualizzare la propria macchina virtuale nel [portale di Azure](https://portal.azure.com).

![Screenshot del dashboard Server, che mostra una connessione server riuscita.](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Passaggi successivi

* Se necessario, vedere la guida [all'agente Risoluzione dei problemi relativi alla macchina connessa](troubleshoot-agent-onboard.md).

* Informazioni su come gestire il computer [usando](../../governance/policy/overview.md)Criteri di Azure . È possibile usare la configurazione [guest](../../governance/policy/concepts/guest-configuration.md)della macchina virtuale, verificare che il computer sia in grado di creare report nell'area di lavoro Log Analytics prevista e abilitare il monitoraggio con Monitoraggio di Azure [con le macchine virtuali](../../azure-monitor/vm/vminsights-enable-policy.md).

* Altre informazioni sull'[agente Log Analytics](../../azure-monitor/agents/log-analytics-agent.md). L'agente di Log Analytics per Windows e Linux è necessario quando si vogliono raccogliere dati di monitoraggio del sistema operativo e del carico di lavoro o gestirlo usando runbook o funzionalità Automazione di Azure come Gestione aggiornamenti. Questo agente è necessario anche per usare altri servizi di Azure, ad esempio [Centro sicurezza di Azure](../../security-center/security-center-introduction.md).
