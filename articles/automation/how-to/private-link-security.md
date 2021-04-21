---
title: Usare collegamento privato di Azure per connettere in modo sicuro le reti Automazione di Azure
description: Usare collegamento privato di Azure per connettere in modo sicuro le reti Automazione di Azure
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/11/2020
ms.subservice: ''
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 28f4c314b65a27c71c7620ff5941463b1ea68b55
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831457"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-automation"></a>Usare collegamento privato di Azure per connettere in modo sicuro le reti Automazione di Azure

L'endpoint privato di Azure è un'interfaccia di rete che connette privatamente e in modo sicuro a un servizio basato su collegamento privato di Azure. L'endpoint privato usa un indirizzo IP privato dalla rete virtuale, portando in modo efficace il servizio di Automazione nella rete virtuale. Il traffico di rete tra i computer nella rete virtuale e l'account di Automazione attraversa la rete virtuale e un collegamento privato nella rete backbone Microsoft, eliminando l'esposizione dalla rete Internet pubblica.

Ad esempio, si dispone di una rete virtuale in cui è stato disabilitato l'accesso a Internet in uscita. Tuttavia, si vuole accedere all'account di Automazione privatamente e usare funzionalità di Automazione come webhook, State Configuration e processi runbook nei processi ibridi per runbook. Si vuole inoltre che gli utenti abbia accesso all'account di Automazione solo tramite la rete virtuale.  La distribuzione di endpoint privati raggiunge questi obiettivi.

Questo articolo illustra quando usare e come configurare un endpoint privato con l'account di Automazione.

![Panoramica concettuale di Collegamento privato per Automazione di Azure](./media/private-link-security/private-endpoints-automation.png)

>[!NOTE]
> Il supporto di Collegamento privato con Automazione di Azure è disponibile solo nei cloud commerciali di Azure e azure us government.

## <a name="advantages"></a>Vantaggi

Con il collegamento privato è possibile:

- Connettersi privatamente a Automazione di Azure senza aprire alcun accesso alla rete pubblica.
- Connettersi privatamente all'Monitoraggio di Azure'area di lavoro Log Analytics senza aprire l'accesso alla rete pubblica.

    >[!NOTE]
    >È necessario un endpoint privato separato per l'area di lavoro Log Analytics se l'account di Automazione è collegato a un'area di lavoro Log Analytics per inoltrare i dati del processo e se sono state abilitate funzionalità come Gestione aggiornamenti, Rilevamento modifiche e inventario, State Configuration o Avvio/Arresto di macchine virtuali durante gli orari di minore attività. Per altre informazioni sul collegamento privato per Monitoraggio di Azure, vedere Usare collegamento privato di Azure per connettere in modo sicuro le reti [Monitoraggio di Azure](../../azure-monitor/logs/private-link-security.md).

- Assicurarsi che i dati di Automazione siano accessibili solo tramite reti private autorizzate.
- Impedire l'esfiltrazione dei dati dalle reti private definendo la risorsa Automazione di Azure che si connette tramite l'endpoint privato.
- Connettere in modo sicuro la rete locale privata al Automazione di Azure usando ExpressRoute e Collegamento privato.
- Mantenere tutto il traffico all'interno Microsoft Azure rete backbone.

Per altre informazioni, vedere [Vantaggi principali del collegamento privato](../../private-link/private-link-overview.md#key-benefits).

## <a name="limitations"></a>Limitazioni

- Nell'implementazione corrente di Collegamento privato, i processi cloud dell'account di Automazione non possono accedere alle risorse di Azure protette tramite endpoint privato. Ad esempio, Azure Key Vault, Azure SQL, Archiviazione di Azure account e così via. Per risolvere questo problema, usare [invece un ruolo di lavoro ibrido per runbook.](../automation-hybrid-runbook-worker.md)
- È necessario usare la versione più recente [dell'agente di Log Analytics](../../azure-monitor/agents/log-analytics-agent.md) per Windows o Linux.
- Il [gateway di Log Analytics](../../azure-monitor/agents/gateway.md) non supporta il collegamento privato.

## <a name="how-it-works"></a>Funzionamento

Automazione di Azure collegamento privato connette uno o più endpoint privati (e quindi le reti virtuali in cui sono contenuti) alla risorsa account di Automazione. Questi endpoint sono computer che usano webhook per avviare un runbook, computer che ospitano il ruolo di lavoro ibrido per runbook e nodi Desired State Configuration (DSC).

Dopo aver creato gli endpoint privati per Automazione, viene eseguito il mapping di ognuno degli URL di Automazione pubblici a un endpoint privato nella rete virtuale. L'utente o un computer può contattare direttamente gli URL di Automazione.

### <a name="webhook-scenario"></a>Scenario webhook

È possibile avviare i runbook eseguendo un post nell'URL del webhook. Ad esempio, l'URL è simile al seguente: `https://<automationAccountId>.webhooks.<region>.azure-automation.net/webhooks?token=gzGMz4SMpqNo8gidqPxAJ3E%3d`

### <a name="hybrid-runbook-worker-scenario"></a>Scenario di ruolo di lavoro ibrido per runbook

La funzionalità ruolo di lavoro ibrido per runbook dell'utente di Automazione di Azure consente di eseguire i runbook direttamente nel computer Azure o non Azure, inclusi i server registrati con Azure Arc server abilitati. Dal computer o dal server che ospita il ruolo, è possibile eseguire i runbook direttamente su di esso e sulle risorse nell'ambiente per gestire tali risorse locali.

Un endpoint JDS viene usato dal ruolo di lavoro ibrido per avviare/arrestare runbook, scaricare i runbook nel ruolo di lavoro e inviare di nuovo il flusso di log del processo al servizio Automazione.Dopo aver abilitato l'endpoint JDS, l'URL sarà simile al seguente: `https://<automationaccountID>.jobruntimedata.<region>.azure-automation.net` . In questo modo l'esecuzione del runbook nel ruolo di lavoro ibrido connesso alla rete virtuale di Azure sarà in grado di eseguire processi senza la necessità di aprire una connessione in uscita a Internet.  

> [!NOTE]
>Con l'implementazione corrente di Collegamenti privati per Automazione di Azure, supporta solo l'esecuzione di processi nel ruolo di lavoro ibrido per runbook connesso a una rete virtuale di Azure e non i processi cloud.

## <a name="hybrid-worker-scenario-for-update-management"></a>Scenario di lavoro ibrido per Gestione aggiornamenti  

Il ruolo di lavoro ibrido per runbook di sistema supporta un set di runbook nascosti usati dalla funzionalità Gestione aggiornamenti progettata per installare gli aggiornamenti specificati dall'utente nei computer Windows e Linux. Quando Automazione di Azure Gestione aggiornamenti abilitata, qualsiasi computer connesso all'area di lavoro Log Analytics viene configurato automaticamente come ruolo di lavoro ibrido per runbook di sistema.

Per informazioni & configurare Gestione aggiornamenti [vedere Informazioni Gestione aggiornamenti](../update-management/overview.md). La Gestione aggiornamenti ha una dipendenza da un'area di lavoro Log Analytics e pertanto richiede il collegamento dell'area di lavoro a un account di Automazione. Un'area di lavoro Log Analytics archivia i dati raccolti dalla soluzione e ospita le ricerche nei log e le visualizzazioni.

Se si vuole che i computer configurati per Gestione aggiornamenti si connettono in modo sicuro all'area di lavoro Log Analytics di Automation & tramite il canale Collegamento privato, è necessario abilitare Collegamento privato per l'area di lavoro Log Analytics collegata all'account di Automazione configurato con collegamento privato.

È possibile controllare il modo in cui un'area di lavoro Log Analytics può essere raggiunta dall'esterno degli ambiti collegamento privato seguendo la procedura descritta in [Configurare Log Analytics](../../azure-monitor/logs/private-link-security.md#configure-log-analytics). Se si imposta **Consenti l'accesso alla rete pubblica per l’inserimento** su **No**, i computer esterni agli ambiti connessi non possono caricare dati in questa area di lavoro. Se si imposta **Consenti l'accesso alla rete pubblica per le query** su **No**, i computer esterni agli ambiti non possono accedere ai dati in questa area di lavoro.

Usare la sotto-risorsa di destinazione **DSCAndHybridWorker** per abilitare il collegamento privato per i & ibridi del sistema.

> [!NOTE]
> I computer ospitati all'esterno di Azure gestiti da Gestione aggiornamenti e connessi alla rete virtuale di Azure tramite peering privato ExpressRoute, tunnel VPN e reti virtuali con peering tramite endpoint privati supportano il collegamento privato.

### <a name="state-configuration-agentsvc-scenario"></a>State Configuration (agentsvc)

State Configuration offre un servizio di gestione della configurazione di Azure che consente di scrivere, gestire e compilare configurazioni di PowerShell Desired State Configuration (DSC) per i nodi in qualsiasi data center cloud o locale.

L'agente nel computer viene registrato nel servizio DSC e quindi usa l'endpoint del servizio per eseguire il pull della configurazione DSC. L'endpoint del servizio agente è simile al seguente: `https://<automationAccountId>.agentsvc.<region>.azure-automation.net` .

L'URL per & endpoint privato sarà lo stesso, ma verrà mappato a un indirizzo IP privato quando il collegamento privato è abilitato.

## <a name="planning-based-on-your-network"></a>Pianificazione basata sulla rete

Prima di configurare la risorsa account di Automazione, prendere in considerazione i requisiti di isolamento della rete. Valutare l'accesso delle reti virtuali a Internet pubblico e le restrizioni di accesso all'account di Automazione,inclusa la configurazione di un ambito del gruppo di collegamento privato per i log di Monitoraggio di Azure se integrato con l'account di Automazione. Includere anche una revisione dei record DNS del [servizio Automazione](./automation-region-dns-records.md) come parte del piano per assicurarsi che le funzionalità supportate funzionino senza problemi.

### <a name="connect-to-a-private-endpoint"></a>Connettersi a un endpoint privato

Creare un endpoint privato per connettere la rete. È possibile crearlo nel centro [portale di Azure collegamento privato](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints). Dopo aver applicato le modifiche apportate a publicNetworkAccess e Collegamento privato, possono essere necessarie fino a 35 minuti prima che siano effettive.

In questa sezione si creerà un endpoint privato per l'account di Automazione.

1. In alto a sinistra nella schermata selezionare Crea una risorsa > **rete > Centro collegamento privato**.

2. In **Centro collegamento privato - Informazioni generali** selezionare **Avvia** per l'opzione **Crea una connessione privata a un servizio**.

3. In **Crea una macchina virtuale - Informazioni di base** immettere o selezionare le informazioni seguenti:

    | Impostazione | valore |
    | ------- | ----- |
    | **DETTAGLI DEL PROGETTO** | |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Resource group | Selezionare **myResourceGroup**. Questo gruppo è stato creato nella sezione precedente.  |
    | **DETTAGLI DELL'ISTANZA** |  |
    | Nome | Immettere *il valore di PrivateEndpoint.* |
    | Region | Selezionare **Area.** |
    |||

4. Selezionare **Avanti: Risorsa**.

5. In **Crea un endpoint privato - Risorsa** immettere o selezionare le informazioni seguenti:

    | Impostazione | valore |
    | ------- | ----- |
    |Metodo di connessione  | Selezionare Connettersi a una risorsa di Azure nella directory.|
    | Subscription| Selezionare la propria sottoscrizione. |
    | Tipo di risorsa | Selezionare **Microsoft.Automation/automationAccounts.** |
    | Risorsa |Selezionare *myAutomationAccount*|
    |Sottorisorsa di destinazione |Selezionare *Webhook* o *DSCAndHybridWorker a* seconda dello scenario.|
    |||

6. Selezionare **Avanti: Configurazione**.

7. In **Crea un endpoint privato - Configurazione** immettere o selezionare le informazioni seguenti:

    | Impostazione | valore |
    | ------- | ----- |
    |**RETE**| |
    | Rete virtuale| Selezionare *MyVirtualNetwork*. |
    | Subnet | Selezionare *mySubnet*. |
    |**INTEGRAZIONE DNS PRIVATO**||
    |Integra con la zona DNS privato |Selezionare **Sì**. |
    |Zona DNS privato |Selezionare *(Nuovo)privatelink.azure-automation.net* |
    |||

8. Selezionare **Rivedi e crea**. Si viene reindirizzati alla pagina **Rivedi e crea** dove Azure convalida la configurazione.

9. Quando viene visualizzato il messaggio **Convalida superata**, selezionare **Crea**.

**Nell'Centro collegamento privato** selezionare Endpoint **privati per** visualizzare la risorsa di collegamento privato.

![Collegamento privato della risorsa di automazione](./media/private-link-security/private-link-automation-resource.png)

Selezionare la risorsa per visualizzare tutti i dettagli. Verrà creato un nuovo endpoint privato per l'account di Automazione e verrà assegnato un indirizzo IP privato dalla rete virtuale. Lo **stato della connessione** viene visualizzato come **approvato.**

Analogamente, viene creato un nome di dominio completo univoco (FQDN) per il State Configuration (agentsvc) e per il runtime del processo di lavoro ibrido per runbook (jrds). A ognuno di essi viene assegnato un indirizzo IP separato dalla rete virtuale e lo **stato della connessione** viene visualizzato come **approvato.**

Se il consumer del servizio ha le autorizzazioni di Controllo degli accessi in base al ruolo di Azure per la risorsa di Automazione, può scegliere il metodo di approvazione automatica. In questo caso, quando la richiesta raggiunge la risorsa del provider di automazione, non è necessaria alcuna azione da parte del provider di servizi e la connessione viene approvata automaticamente.

## <a name="set-public-network-access-flags"></a>Impostare i flag di accesso alla rete pubblica

È possibile configurare un account di Automazione per negare tutte le configurazioni pubbliche e consentire solo le connessioni tramite endpoint privati per migliorare ulteriormente la sicurezza di rete. Se si vuole limitare l'accesso all'account di Automazione solo all'interno della rete virtuale e non consentire l'accesso da Internet pubblico, è possibile impostare `publicNetworkAccess` la proprietà su `$false` .

Quando  l'impostazione Accesso alla rete pubblica è impostata su , sono consentite solo le connessioni tramite endpoint privati e tutte le connessioni tramite endpoint pubblici vengono negate con un messaggio di errore non autorizzato e lo stato `$false` HTTP 401.

Lo script di PowerShell seguente illustra come e la proprietà Accesso alla rete `Get` pubblica a livello di account di `Set` Automazione: 

```powershell
$account = Get-AzResource -ResourceType Microsoft.Automation/automationAccounts -ResourceGroupName "<resourceGroupName>" -Name "<automationAccountName>" -ApiVersion "2020-01-13-preview"
$account.Properties | Add-Member -Name 'publicNetworkAccess' -Type NoteProperty -Value $false
$account | Set-AzResource -Force -ApiVersion "2020-01-13-preview"
```

È anche possibile controllare la proprietà di accesso alla rete pubblica dal portale di Azure. Nell'account di Automazione selezionare **Isolamento rete** nel riquadro a sinistra nella **sezione Impostazioni** account. Quando l'impostazione Accesso alla rete pubblica è impostata su **No,** sono consentite solo le connessioni su endpoint privati e tutte le connessioni su endpoint pubblici vengono negate.

![Impostazione accesso alla rete pubblica](./media/private-link-security/allow-public-network-access.png)

## <a name="dns-configuration"></a>Configurazione del DNS

Quando ci si connette a una risorsa di collegamento privato usando un nome di dominio completo (FQDN) come parte della stringa di connessione, è importante configurare correttamente le impostazioni DNS per la risoluzione nell'indirizzo IP privato allocato. I servizi di Azure esistenti potrebbero avere già una configurazione DNS da usare per la connessione tramite un endpoint pubblico. La configurazione DNS deve essere esaminata e aggiornata per connettersi usando l'endpoint privato.

L'interfaccia di rete associata all'endpoint privato contiene il set completo di informazioni necessarie per configurare il DNS, inclusi FQDN e indirizzi IP privati allocati per una determinata risorsa Collegamento privato.

Per configurare le impostazioni DNS per gli endpoint privati si può procedere nei modi seguenti:

* Usare il file host (consigliato solo per i test). È possibile usare il file host in una macchina virtuale per eseguire prima l'override dell'uso di DNS per la risoluzione dei nomi. La voce DNS dovrebbe essere simile all'esempio seguente: `privatelinkFQDN.jrds.sea.azure-automation.net` .

* Usare una [zona DNS privata](../../dns/private-dns-privatednszone.md). È possibile usare zone DNS private per eseguire l'override della risoluzione DNS per un determinato endpoint privato. Una zona DNS privato può essere collegata alla rete virtuale per risolvere domini specifici. Per consentire all'agente nella macchina virtuale di comunicare tramite l'endpoint privato, creare un record DNS privato come `privatelink.azure-automation.net` . Aggiungere un nuovo mapping di record *DNS A* all'indirizzo IP dell'endpoint privato.

* Usare il server d'inoltro DNS (facoltativo). È possibile usare il server d'inoltro DNS per eseguire l'override della risoluzione DNS per una determinata risorsa di collegamento privato. Se il [server DNS](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) è ospitato in una rete virtuale, si può creare una regola di invio DNS in modo da usare una zona DNS privato per semplificare la configurazione per tutte le risorse Collegamento privato.

Per altre informazioni, vedere [Configurazione DNS dell'endpoint privato di Azure](../../private-link/private-endpoint-dns.md).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'endpoint privato, vedere [Che cos'è l'endpoint privato di Azure?](../../private-link/private-endpoint-overview.md).
