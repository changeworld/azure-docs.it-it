---
title: Visualizzare e configurare gli avvisi di protezione DDoS per la protezione DDoS di Azure standard
description: Informazioni su come visualizzare e configurare gli avvisi di protezione DDoS per la protezione DDoS di Azure standard.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/28/2020
ms.author: yitoh
ms.openlocfilehash: a5639d583d9b98f6527e47bf5db213cb191ebeb7
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100575302"
---
# <a name="view-and-configure-ddos-protection-alerts"></a>Visualizzare e configurare gli avvisi di protezione DDoS

Lo standard Protezione DDoS di Azure fornisce informazioni dettagliate sugli attacchi e ne consente la visualizzazione con l'analisi degli attacchi DDoS. I clienti che proteggono le reti virtuali da attacchi DDoS hanno visibilità dettagliata sul traffico degli attacchi e sulle azioni intraprese per mitigare l'attacco tramite i report sulla mitigazione dell'attacco e i log del flusso di mitigazione. I dati di telemetria avanzati vengono esposti tramite monitoraggio di Azure, incluse le metriche dettagliate durante la durata di un attacco DDoS. Gli avvisi possono essere configurati per una delle metriche di monitoraggio di Azure esposte dalla protezione DDoS. La registrazione può essere integrata ulteriormente con [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md), Splunk (hub eventi di Azure), OMS log Analytics e archiviazione di Azure per l'analisi avanzata tramite l'interfaccia di diagnostica di monitoraggio di Azure.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Configurare gli avvisi tramite monitoraggio di Azure
> * Configurare gli avvisi tramite il portale
> * Visualizzare gli avvisi nel centro sicurezza di Azure
> * Convalidare e testare gli avvisi

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
- Prima di eseguire i passaggi di questa esercitazione, è necessario creare prima di tutto un [piano di protezione standard DDoS di Azure](manage-ddos-protection.md) e la protezione DDoS standard deve essere abilitata in una rete virtuale.
- La protezione DDoS esegue il monitoraggio degli indirizzi IP pubblici assegnati alle risorse all'interno di una rete virtuale. Se non si dispone di risorse con indirizzi IP pubblici nella rete virtuale, è innanzitutto necessario creare una risorsa con un indirizzo IP pubblico. È possibile monitorare l'indirizzo IP pubblico di tutte le risorse distribuite tramite Gestione risorse (non classico) elencate in [rete virtuale per i servizi di Azure](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) (inclusi i servizi di bilanciamento del carico di Azure in cui le macchine virtuali di back-end si trovano nella rete virtuale), ad eccezione dei app Azure ambienti del servizio. Per continuare questa esercitazione, è possibile creare rapidamente una macchina virtuale [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).     

## <a name="configure-alerts-through-azure-monitor"></a>Configurare gli avvisi tramite monitoraggio di Azure

Con questi modelli, sarà possibile configurare gli avvisi per tutti gli indirizzi IP pubblici per i quali è stata abilitata la registrazione diagnostica. Quindi, per usare questi modelli di avviso, è necessario prima di tutto un'area di lavoro Log Analytics con le impostazioni di diagnostica abilitate. Vedere [visualizzare e configurare la registrazione diagnostica DDoS](diagnostic-logging.md).

### <a name="azure-monitor-alert-rule"></a>Regola di avviso di monitoraggio di Azure

Questa [regola di avviso di monitoraggio di Azure](https://aka.ms/DDOSmitigationstatus) consente di eseguire una query semplice per rilevare quando si verifica una mitigazione DDoS attiva. Ciò indica un potenziale attacco. I gruppi di azioni possono essere usati per richiamare le azioni in seguito all'avviso.

[![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FAzure%2520Monitor%2520Alert%2520-%2520DDoS%2520Mitigation%2520Started%2FDDoSMitigationStarted.json)

### <a name="azure-monitor-alert-rule-with-logic-app"></a>Regola di avviso di monitoraggio di Azure con app per la logica

Questo [modello](https://aka.ms/ddosalert) distribuisce i componenti necessari di un avviso di mitigazione DDoS arricchito: regola di avviso di monitoraggio di Azure, gruppo di azioni e app per la logica. Il risultato del processo è un avviso di posta elettronica con i dettagli relativi all'indirizzo IP sotto attacco, incluse le informazioni sulla risorsa associata all'indirizzo IP. Il proprietario della risorsa viene aggiunto come destinatario del messaggio di posta elettronica, insieme al team di sicurezza. Viene inoltre eseguito un test di disponibilità dell'applicazione di base e i risultati vengono inclusi nell'avviso di posta elettronica.

[![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FDDoS%2520Mitigation%2520Alert%2520Enrichment%2FEnrich-DDoSAlert.json)

## <a name="configure-alerts-through-portal"></a>Configurare gli avvisi tramite il portale

Usando la configurazione degli avvisi di Monitoraggio di Azure, è possibile selezionare una delle metriche di protezione DDoS disponibili per ricevere un avviso nel caso in cui ci sia una mitigazione attiva durante un attacco. 

1. Accedere al [portale di Azure](https://portal.azure.com/) e passare al piano di protezione DDoS.
2. In **Monitoraggio** selezionare **Metrica**.
3. Nella barra di spostamento grigia selezionare **nuova regola di avviso**. 
4. Immettere o selezionare i valori personalizzati oppure immettere i valori di esempio seguenti, accettare le impostazioni predefinite rimanenti e quindi selezionare **Crea regola di avviso**:

    |Impostazione                  |Valore                                                                                               |
    |---------                |---------                                                                                           |
    | Ambito                   | Scegliere **Seleziona risorsa**. </br> Selezionare la **sottoscrizione** che contiene l'indirizzo IP pubblico che si vuole registrare, selezionare **indirizzo IP pubblico** per **tipo di risorsa**, quindi selezionare l'indirizzo IP pubblico specifico per cui si vogliono registrare le metriche. </br> Selezionare **Fine**. | 
    | Condizione | Selezionare **Seleziona condizione**. </br> In nome segnale selezionare **sotto attacco DDoS o no**. </br> In **operatore** selezionare **maggiore o uguale a**. </br> In **tipo di aggregazione** selezionare **massimo**. </br> In **valore soglia** immettere *1*. Per la metrica **attacco DDoS o no** , **0** indica che non si è sotto attacco mentre **1** indica che l'utente è sotto attacco. </br> Selezionare **Fine**. | 
    | Azioni | Selezionare **Aggiungi gruppi di azioni**. </br> Selezionare **Crea gruppo di azioni**. </br> In **notifiche**, in **tipo di notifica**, selezionare **messaggio di posta elettronica/SMS/push/Voice**. </br> In **nome** immettere _MyUnderAttackEmailAlert_. </br> Fare clic sul pulsante modifica, quindi selezionare **posta elettronica** e il numero di opzioni seguenti, quindi selezionare **OK**. </br> Selezionare **Rivedi e crea**. | 
    | Dettagli regola di avviso | In **Nome regola di avviso** immettere _MyDdosAlert_. |

Entro pochi minuti dal rilevamento degli attacchi, si dovrebbe ricevere un messaggio di posta elettronica dalle metriche di monitoraggio di Azure che hanno un aspetto simile all'immagine seguente:

![Avviso di attacco](./media/manage-ddos-protection/ddos-alert.png)

È anche possibile leggere altre informazioni relative alla [configurazione dei webhook](../azure-monitor/alerts/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e alle [app per la logica](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) per la creazione di avvisi.

## <a name="view-alerts-in-azure-security-center"></a>Visualizzare gli avvisi nel centro sicurezza di Azure

Il Centro sicurezza di Azure fornisce un elenco di [avvisi di sicurezza](../security-center/security-center-managing-and-responding-alerts.md), con informazioni che consentono di analizzare e correggere i problemi. Questa funzionalità consente di ottenere una visualizzazione unificata degli avvisi, inclusi gli avvisi relativi agli attacchi DDoS e le azioni intraprese per attenuare l'attacco in tempi più prossimi.
Esistono due avvisi specifici che verranno visualizzati per qualsiasi rilevamento e mitigazione degli attacchi DDoS:

- **Attacco DDoS rilevato per l'indirizzo IP pubblico**: questo avviso viene generato quando il servizio protezione DDoS rileva che uno degli indirizzi IP pubblici è la destinazione di un attacco DDoS.
- **Attacco DDoS mitigato per IP pubblico**: questo avviso viene generato quando è stato mitigato un attacco all'indirizzo IP pubblico.
Per visualizzare gli avvisi, aprire il **Centro sicurezza** nel portale di Azure. In **protezione dalle minacce** selezionare **avvisi di sicurezza**. Lo screenshot seguente mostra un esempio degli avvisi di attacco DDoS.

![Avviso DDoS nel centro sicurezza di Azure](./media/manage-ddos-protection/ddos-alert-asc.png)

Gli avvisi includono informazioni generali sull'indirizzo IP pubblico che si trova sotto attacco, informazioni sull'Intelligence per le minacce e sulla Geo e procedure correttive.

## <a name="validate-and-test"></a>Convalida e verifica

Per simulare un attacco DDoS per convalidare gli avvisi, vedere [Validate DDoS Detection](test-through-simulations.md).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

- Configurare gli avvisi tramite monitoraggio di Azure
- Configurare gli avvisi tramite il portale
- Visualizzare gli avvisi nel centro sicurezza di Azure
- Convalidare e testare gli avvisi

Per informazioni su come testare e simulare un attacco DDoS, vedere la guida ai test di simulazione:

> [!div class="nextstepaction"]
> [Testare con le simulazioni](test-through-simulations.md)
