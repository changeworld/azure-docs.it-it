---
title: Usare monitoraggio monitoraggio desktop virtuale di Windows-Azure
description: Come usare monitoraggio di Azure per desktop virtuale di Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 03/29/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 1b93e0c0d61eaa390eda66da2a583a4f429ddd79
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105709549"
---
# <a name="use-azure-monitor-for-windows-virtual-desktop-to-monitor-your-deployment"></a>Usare monitoraggio di Azure per desktop virtuale Windows per monitorare la distribuzione

Monitoraggio di Azure per desktop virtuale di Windows è un dashboard basato sulle cartelle di lavoro di monitoraggio di Azure che consente ai professionisti IT di comprendere gli ambienti desktop virtuali Windows. Questo argomento descrive come configurare monitoraggio di Azure per desktop virtuale Windows per monitorare gli ambienti desktop virtuali Windows.

## <a name="requirements"></a>Requisiti

Prima di iniziare a usare monitoraggio di Azure per desktop virtuale Windows, è necessario configurare gli elementi seguenti:

- Tutti gli ambienti desktop virtuali Windows monitorati devono essere basati sulla versione più recente di desktop virtuale di Windows compatibile con Azure Resource Manager.
- Almeno un'area di lavoro configurata Log Analytics. Utilizzare un'area di lavoro Log Analytics designata per gli host sessione desktop virtuale di Windows per garantire che i contatori delle prestazioni e gli eventi vengano raccolti solo dagli host sessione nella distribuzione di desktop virtuali Windows.
- Abilitare la raccolta dei dati per gli elementi seguenti nell'area di lavoro Log Analytics:
    - Diagnostica da un ambiente desktop virtuale Windows
    - Contatori delle prestazioni consigliati dagli host sessione desktop virtuale Windows
    - Registri eventi di Windows consigliati dagli host sessione desktop virtuale Windows

 Il processo di configurazione dei dati descritto in questo articolo è l'unico che è necessario monitorare desktop virtuale di Windows. Per ridurre i costi, è possibile disabilitare tutti gli altri elementi che inviano dati all'area di lavoro Log Analytics.

Chiunque monitora monitoraggio di Azure per desktop virtuale Windows per l'ambiente richiederà anche le seguenti autorizzazioni di accesso in lettura:

- Accesso in lettura alle sottoscrizioni di Azure che contengono le risorse del desktop virtuale Windows
- Accesso in lettura ai gruppi di risorse della sottoscrizione che contengono gli host sessione desktop virtuale Windows
- Accesso in lettura all'area di lavoro o alle aree di lavoro Log Analytics

>[!NOTE]
> L'accesso in lettura consente agli amministratori di visualizzare i dati. Sono necessarie autorizzazioni diverse per gestire le risorse nel portale di desktop virtuale di Windows.

## <a name="open-azure-monitor-for-windows-virtual-desktop"></a>Apri monitoraggio di Azure per desktop virtuale Windows

È possibile aprire Monitoraggio di Azure per desktop virtuale Windows con uno dei metodi seguenti:

- Passare a [aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks).
- Cercare e selezionare **desktop virtuale di Windows** dal portale di Azure, quindi selezionare **Insights (informazioni dettagliate**).
- Cercare e selezionare **monitoraggio di Azure** dalla portale di Azure. Selezionare **Hub Insights** in **Insights**, quindi selezionare **desktop virtuale di Windows**.
Una volta aperta la pagina, immettere la **sottoscrizione**, il **gruppo di risorse**, il **pool di host** e l'intervallo di **tempo** dell'ambiente che si desidera monitorare.

>[!NOTE]
>Desktop virtuale di Windows attualmente supporta solo il monitoraggio di una sottoscrizione, di un gruppo di risorse e di un pool host alla volta. Se non si riesce a trovare l'ambiente che si vuole monitorare, vedere [la documentazione per la risoluzione dei](troubleshoot-azure-monitor.md) problemi per richieste di funzionalità note e problemi.

## <a name="log-analytics-settings"></a>Impostazioni Log Analytics

Per iniziare a usare monitoraggio di Azure per desktop virtuale di Windows, è necessaria almeno un'area di lavoro Log Analytics. Utilizzare un'area di lavoro Log Analytics designata per gli host sessione desktop virtuale di Windows per garantire che i contatori delle prestazioni e gli eventi vengano raccolti solo da host sessione modulo nella distribuzione di desktop virtuali Windows. Se è già stata configurata un'area di lavoro, andare avanti per [configurare usando la cartella di lavoro configurazione](#set-up-using-the-configuration-workbook). Per configurarne uno, vedere [creare un'area di lavoro log Analytics nel portale di Azure](../azure-monitor/logs/quick-create-workspace.md).

>[!NOTE]
>Verranno applicati gli addebiti per l'archiviazione dei dati standard per Log Analytics. Per iniziare, è consigliabile scegliere il modello con pagamento in base al consumo e adattarlo quando si ridimensiona la distribuzione e si accettano più dati. Per altre informazioni, vedere [prezzi di monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="set-up-using-the-configuration-workbook"></a>Configurare utilizzando la cartella di lavoro di configurazione

Se è la prima volta che si apre Monitoraggio di Azure per desktop virtuale Windows, è necessario configurare monitoraggio di Azure per l'ambiente di desktop virtuale di Windows. Per configurare le risorse:

1. Aprire Monitoraggio di Azure per desktop virtuale Windows nel portale di Azure in [aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks), quindi selezionare **cartella di lavoro configurazione**.
2. Selezionare un ambiente da configurare in **sottoscrizione**, **gruppo di risorse** e **pool di host**.

La cartella di lavoro configurazione configura l'ambiente di monitoraggio e consente di controllare la configurazione al termine del processo di installazione. È importante controllare la configurazione se gli elementi nel dashboard non vengono visualizzati correttamente oppure quando il gruppo di prodotti pubblica gli aggiornamenti che richiedono nuove impostazioni.

### <a name="resource-diagnostic-settings"></a>Impostazioni di diagnostica di risorsa

Per raccogliere informazioni sull'infrastruttura di desktop virtuali Windows, è necessario abilitare diverse impostazioni di diagnostica nei pool di host e nelle aree di lavoro di desktop virtuale Windows (si tratta dell'area di lavoro desktop virtuale di Windows, non dell'area di lavoro Log Analytics). Per ulteriori informazioni sui pool host, le aree di lavoro e altri oggetti risorsa Desktop virtuale di Windows, vedere la [Guida dell'ambiente](environment-setup.md).

Per ulteriori informazioni su diagnostica desktop virtuali Windows e sulle tabelle di diagnostica supportate, vedere l'articolo relativo [all'invio di diagnostica desktop virtuali Windows a log Analytics](diagnostics-log-analytics.md).

Per impostare le impostazioni di diagnostica delle risorse nella cartella di lavoro di configurazione: 

1. Selezionare la scheda **impostazioni di diagnostica della risorsa** nella cartella di lavoro configurazione. 
2. Selezionare **log Analytics area di lavoro** per inviare diagnostica desktop virtuale di Windows. 

#### <a name="host-pool-diagnostic-settings"></a>Impostazioni di diagnostica del pool di host

Per configurare la diagnostica del pool host usando la sezione impostazioni di diagnostica della risorsa nella cartella di lavoro di configurazione:

1. In **pool host** verificare se diagnostica desktop virtuale di Windows è abilitata. In caso affermativo, verrà visualizzato un messaggio di errore che indica che non è stata trovata alcuna configurazione di diagnostica esistente per il pool host selezionato. È necessario abilitare le tabelle di diagnostica supportate seguenti:

    - Checkpoint
    - Errore
    - Gestione
    - Connessioni
    - HostRegistration
    - AgentHealthStatus
    
    >[!NOTE]
    > Se il messaggio di errore non viene visualizzato, non è necessario eseguire i passaggi da 2 a 4.

2. Selezionare **Configura pool host**.
3. Selezionare **Distribuisci**.
4. Aggiornare la cartella di lavoro configurazione.

#### <a name="workspace-diagnostic-settings"></a>Impostazioni di diagnostica dell'area di lavoro 

Per configurare la diagnostica dell'area di lavoro usando la sezione impostazioni di diagnostica delle risorse nella cartella di lavoro di configurazione:

 1. In **area di lavoro** verificare se diagnostica desktop virtuale di Windows è abilitata per l'area di lavoro desktop virtuale di Windows. In caso affermativo, verrà visualizzato un messaggio di errore che indica che non è stata trovata alcuna configurazione di diagnostica esistente per l'area di lavoro selezionata. È necessario abilitare le tabelle di diagnostica supportate seguenti:
 
    - Checkpoint
    - Errore
    - Gestione
    - Feed
    
    >[!NOTE]
    > Se il messaggio di errore non viene visualizzato, non è necessario eseguire i passaggi 2-4.

2. Selezionare **Configura area di lavoro**.
3. Selezionare **Distribuisci**.
4. Aggiornare la cartella di lavoro configurazione.

### <a name="session-host-data-settings"></a>Impostazioni dati host sessione

Per raccogliere informazioni sugli host sessione desktop virtuale Windows, è necessario installare l'agente di Log Analytics in tutti gli host di sessione nel pool host, verificare che gli host di sessione inviino a un'area di lavoro Log Analytics e configurare le impostazioni dell'agente di Log Analytics per raccogliere i dati sulle prestazioni e i registri eventi di Windows.

L'area di lavoro Log Analytics a cui si inviano i dati dell'host della sessione non deve corrispondere a quella in cui si inviano i dati di diagnostica. Se si dispone di host sessione di Azure all'esterno dell'ambiente desktop virtuale di Windows, è consigliabile disporre di un'area di lavoro Log Analytics designata per gli host sessione desktop virtuali di Windows. 

Per impostare l'area di lavoro Log Analytics in cui si desidera raccogliere i dati dell'host sessione: 

1. Selezionare la scheda **Impostazioni dati host sessione** nella cartella di lavoro configurazione. 
2. Selezionare l' **area di lavoro log Analytics** a cui si desidera inviare i dati dell'host sessione. 

#### <a name="session-hosts"></a>Host di sessione

È necessario installare l'agente di Log Analytics in tutti gli host di sessione nel pool host e inviare i dati da tali host all'area di lavoro Log Analytics selezionata. Se Log Analytics non è configurato per tutti gli host di sessione nel pool di host, verrà visualizzata una sezione **host sessione** nella parte superiore delle **Impostazioni dati host sessione** con il messaggio "alcuni host nel pool host non inviano dati all'area di lavoro log Analytics selezionata".

>[!NOTE]
> Se la sezione host della **sessione** o il messaggio di errore non viene visualizzato, tutti gli host di sessione sono configurati correttamente. Passare a configurare le istruzioni per i [contatori delle prestazioni dell'area di lavoro](#workspace-performance-counters).

Per configurare gli host di sessione rimanenti utilizzando la cartella di lavoro di configurazione:

1. Selezionare **Aggiungi host all'area di lavoro**. 
2. Aggiornare la cartella di lavoro configurazione.

>[!NOTE]
>Per installare l'estensione Log Analytics, è necessario che il computer host sia in esecuzione. Se la distribuzione automatica non funziona, è possibile installare manualmente l'estensione in un host. Per informazioni su come installare l'estensione manualmente, vedere [log Analytics estensione della macchina virtuale per Windows](../virtual-machines/extensions/oms-windows.md).

#### <a name="workspace-performance-counters"></a>Contatori delle prestazioni dell'area di lavoro

È necessario abilitare specifici contatori delle prestazioni per raccogliere informazioni sulle prestazioni dagli host della sessione e inviarle all'area di lavoro Log Analytics.

Se sono già stati abilitati contatori delle prestazioni e si desidera rimuoverli, seguire le istruzioni riportate in [configurazione dei contatori delle prestazioni](../azure-monitor/agents/data-sources-performance-counters.md). È possibile aggiungere e rimuovere i contatori delle prestazioni nella stessa posizione.

Per configurare i contatori delle prestazioni utilizzando la cartella di lavoro di configurazione: 

1. In **contatori delle prestazioni dell'area di lavoro** nella cartella di lavoro configurazione selezionare **contatori configurati** per visualizzare i contatori già abilitati per l'invio all'area di lavoro log Analytics. Controllare i **contatori mancanti** per assicurarsi di aver abilitato tutti i contatori necessari.
2. Se sono presenti contatori mancanti, selezionare **configura contatori delle prestazioni**.
3. Selezionare **Applica configurazione**.
4. Aggiornare la cartella di lavoro configurazione.
5. Verificare che tutti i contatori necessari siano abilitati controllando l'elenco dei **contatori mancanti** . 

#### <a name="configure-windows-event-logs"></a>Configurare i registri eventi di Windows

Sarà inoltre necessario abilitare i registri eventi di Windows specifici per raccogliere errori, avvisi e informazioni dagli host della sessione e inviarli all'area di lavoro Log Analytics.

Se sono già stati abilitati i registri eventi di Windows e si desidera rimuoverli, seguire le istruzioni riportate in [configurazione dei registri eventi di Windows](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs).  È possibile aggiungere e rimuovere i registri eventi di Windows nella stessa posizione.

Per configurare i registri eventi di Windows utilizzando la cartella di lavoro di configurazione:

1. In **configurazione registri eventi di Windows** selezionare **registri eventi configurati** per visualizzare i registri eventi già abilitati per l'invio all'area di lavoro log Analytics. Controllare i **registri eventi mancanti** per assicurarsi di aver abilitato tutti i registri eventi di Windows.
2. Se mancano i registri eventi di Windows, selezionare **Configura eventi**.
3. Selezionare **Distribuisci**.
4. Aggiornare la cartella di lavoro configurazione.
5. Verificare che tutti i registri eventi di Windows necessari siano abilitati controllando l'elenco dei **registri eventi mancanti** . 

>[!NOTE]
>Se la distribuzione automatica degli eventi non riesce, selezionare **Apri configurazione agente** nella cartella di lavoro configurazione per aggiungere manualmente eventuali registri eventi di Windows mancanti. 

## <a name="optional-configure-alerts"></a>Facoltativo: configurare gli avvisi

Monitoraggio di Azure per desktop virtuale Windows consente di monitorare gli avvisi di monitoraggio di Azure che si verificano all'interno della sottoscrizione selezionata nel contesto dei dati del desktop virtuale di Windows. Gli avvisi di monitoraggio di Azure sono una funzionalità facoltativa per le sottoscrizioni di Azure ed è necessario configurarli separatamente da monitoraggio di Azure per desktop virtuale di Windows. È possibile usare il Framework avvisi di monitoraggio di Azure per impostare avvisi personalizzati su eventi, diagnostica e risorse del desktop virtuale di Windows. Per altre informazioni sugli avvisi di monitoraggio di Azure, vedere [rispondere agli eventi con gli avvisi di monitoraggio di Azure](../azure-monitor/alerts/tutorial-response.md).

## <a name="diagnostic-and-usage-data"></a>Dati di diagnostica e di utilizzo

Microsoft raccoglie automaticamente i dati relativi a utilizzo e prestazioni tramite l'uso del servizio Monitoraggio di Azure. Microsoft usa questi dati per migliorare la qualità, la sicurezza e l'integrità del servizio.

Per fornire funzionalità di risoluzione dei problemi accurate ed efficienti, i dati raccolti includono l'ID di sessione del portale, Azure Active Directory ID utente e il nome della scheda del portale in cui si è verificato l'evento. Microsoft non raccoglie nomi, indirizzi o altre informazioni di contatto.

Per altre informazioni sulla raccolta e sull'uso dei dati , vedere l'[Informativa sulla privacy Servizi online Microsoft ](https://privacy.microsoft.com/privacystatement).

>[!NOTE]
>Per informazioni sulla visualizzazione o l'eliminazione dei dati personali raccolti dal servizio, vedere [richieste di soggetto dati di Azure per GDPR](/microsoft-365/compliance/gdpr-dsr-azure). Per ulteriori informazioni su GDPR, vedere [la sezione GDPR del portale di attendibilità del servizio](https://servicetrust.microsoft.com/ViewPage/GDPRGetStarted).

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato configurato monitoraggio di Azure per l'ambiente di desktop virtuale Windows, di seguito sono riportate alcune risorse che potrebbero essere utili per iniziare a monitorare l'ambiente:

- Consulta il [Glossario](azure-monitor-glossary.md) per scoprire di più su termini e concetti correlati a monitoraggio di Azure per desktop virtuale di Windows.
- Se si verifica un problema, consultare la [Guida alla risoluzione dei](troubleshoot-azure-monitor.md) problemi per informazioni e problemi noti.
