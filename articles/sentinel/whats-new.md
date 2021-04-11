---
title: Novità di Azure Sentinel
description: Questo articolo descrive le nuove funzionalità di Azure Sentinel risalenti agli ultimi mesi.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 03/31/2021
ms.openlocfilehash: 74cd5ce5912e5a656342a43b7c2b165fdf3c32d4
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490301"
---
# <a name="whats-new-in-azure-sentinel"></a>Novità di Azure Sentinel

Questo articolo elenca le funzionalità recenti aggiunte a Sentinel di Azure e le nuove funzionalità dei servizi correlati che offrono un'esperienza utente migliorata in Sentinel di Azure.

Per informazioni sulle funzionalità precedenti fornite, vedere i [Blog della community tecnica](https://techcommunity.microsoft.com/t5/azure-sentinel/bg-p/AzureSentinelBlog/label-name/What's%20New).

Le funzionalità indicate sono attualmente in anteprima. Le [condizioni aggiuntive per l'anteprima di Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) includono termini legali aggiuntivi che si applicano a funzionalità di Azure in versione beta, anteprima o diversamente non ancora disponibili a livello generale.


> [!TIP]
> I nostri team di caccia alle minacce di Microsoft contribuiscono a eseguire query, PlayBook, cartelle di lavoro e notebook nella [community di Azure Sentinel](https://github.com/Azure/Azure-Sentinel), incluse [query di caccia](https://github.com/Azure/Azure-Sentinel) specifiche che i team possono adattare e usare. 
>
> È anche possibile contribuire. Partecipa alla [community GitHub di Azure Sentinel Threat Hunters](https://github.com/Azure/Azure-Sentinel/wiki).
> 

## <a name="march-2021"></a>Marzo 2021

- [Impostazione delle cartelle di lavoro per l'aggiornamento automatico in modalità di visualizzazione](#set-workbooks-to-automatically-refresh-while-in-view-mode)
- [Nuovi rilevamenti per il firewall di Azure](#new-detections-for-azure-firewall)
- [Regole di automazione e PlayBook attivati dagli eventi imprevisti](#automation-rules-and-incident-triggered-playbooks) (inclusa la documentazione di tutti i nuovi PlayBook)
- [Nuovi miglioramenti degli avvisi: mapping di entità migliorato e dettagli personalizzati](#new-alert-enrichments-enhanced-entity-mapping-and-custom-details)
- [Stampa le cartelle di lavoro di Azure Sentinel o Salva come PDF](#print-your-azure-sentinel-workbooks-or-save-as-pdf)
- [Filtri per gli eventi imprevisti e preferenze di ordinamento ora salvati nella sessione (anteprima pubblica)](#incident-filters-and-sort-preferences-now-saved-in-your-session-public-preview)
- [Integrazione di Microsoft 365 Defender Incident (anteprima pubblica)](#microsoft-365-defender-incident-integration-public-preview)
- [Nuovi connettori del servizio Microsoft con criteri di Azure](#new-microsoft-service-connectors-using-azure-policy)

### <a name="set-workbooks-to-automatically-refresh-while-in-view-mode"></a>Impostazione delle cartelle di lavoro per l'aggiornamento automatico in modalità di visualizzazione

Gli utenti di Sentinel di Azure possono ora usare la nuova [capacità di monitoraggio di Azure](https://techcommunity.microsoft.com/t5/azure-monitor/azure-workbooks-set-it-to-auto-refresh/ba-p/2228555) per aggiornare automaticamente i dati della cartella di lavoro durante una sessione di visualizzazione.

In ogni cartella di lavoro o modello di cartella di lavoro selezionare :::image type="icon" source="media/whats-new/auto-refresh-workbook.png" border="false"::: **aggiornamento automatico** per visualizzare le opzioni di intervallo. Selezionare l'opzione che si desidera utilizzare per la sessione di visualizzazione corrente e selezionare **applica**.

- Gli intervalli di aggiornamento supportati sono compresi tra **5 minuti** e **1 giorno**.
- Per impostazione predefinita, l'aggiornamento automatico è disattivato. Per ottimizzare le prestazioni, l'aggiornamento automatico viene disattivato ogni volta che si chiude una cartella di lavoro e non viene eseguita in background. Riattivare l'aggiornamento automatico in base alle esigenze alla successiva apertura della cartella di lavoro.
- L'aggiornamento automatico viene sospeso durante la modifica di una cartella di lavoro e gli intervalli di aggiornamento automatico vengono riavviati ogni volta che si passa di nuovo alla modalità di visualizzazione dalla modalità di modifica.

    Gli intervalli vengono riavviati anche se si aggiorna manualmente la cartella di lavoro selezionando il :::image type="icon" source="media/whats-new/manual-refresh-button.png" border="false"::: pulsante **Aggiorna** .

Per altre informazioni, vedere [esercitazione: visualizzare e monitorare i dati](tutorial-monitor-your-data.md) e la [documentazione di monitoraggio di Azure](../azure-monitor/visualize/workbooks-overview.md).

### <a name="new-detections-for-azure-firewall"></a>Nuovi rilevamenti per il firewall di Azure

Sono stati aggiunti diversi rilevamenti predefiniti per il firewall di Azure all'area [Analytics](import-threat-intelligence.md#analytics-puts-your-threat-indicators-to-work-detecting-potential-threats) in Sentinel di Azure. Questi nuovi rilevamenti consentono ai team di sicurezza di ottenere avvisi se i computer nella rete interna tentano di eseguire query o connettersi a nomi di dominio Internet o indirizzi IP associati a IOC noti, come definito nella query della regola di rilevamento.

I nuovi rilevamenti includono:

- [Beacon di rete Solorigate](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/Solorigate-Network-Beacon.yaml)
- [Domini e hash del gallio noti](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/GalliumIOCs.yaml)
- [IP IRIDIUM noto](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/IridiumIOCs.yaml)
- [Domini/IP del gruppo fosforo noti](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/PHOSPHORUSMarch2019IOCs.yaml)
- [Domini di TALLio inclusi in Takedown Takedown](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ThalliumIOCs.yaml)
- [Hash maldoc correlato allo ZINCo noto](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ZincJan272021IOCs.yaml)
- [Domini del gruppo di STRONZi noti](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/STRONTIUMJuly2019IOCs.yaml)
- [Nobelium-Domain e IP IOC-marzo 2021](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/NOBELIUM_DomainIOCsMarch2021.yaml)


I rilevamenti per i firewall di Azure vengono aggiunti continuamente alla raccolta di modelli incorporata. Per ottenere i rilevamenti più recenti per il firewall di Azure, in **modelli di regole** filtrare le **origini dati** in base al **firewall di Azure**:

:::image type="content" source="media/whats-new/new-detections-analytics-efficiency-workbook.jpg" alt-text="Nuovi rilevamenti nella cartella di lavoro di analisi dell'efficienza":::

Per altre informazioni, vedere [nuovi rilevamenti per il firewall di Azure in Sentinel di Azure](https://techcommunity.microsoft.com/t5/azure-network-security/new-detections-for-azure-firewall-in-azure-sentinel/ba-p/2244958).

### <a name="automation-rules-and-incident-triggered-playbooks"></a>Regole di automazione e PlayBook attivati dagli eventi imprevisti

Le regole di automazione sono un nuovo concetto di Azure Sentinel, che consente di gestire centralmente l'automazione della gestione degli eventi imprevisti. Oltre a consentire l'assegnazione di PlayBook a eventi imprevisti (non solo agli avvisi come prima), le regole di automazione consentono anche di automatizzare le risposte per più regole di analisi in una sola volta, contrassegnare, assegnare o chiudere automaticamente gli eventi imprevisti senza la necessità di PlayBook e controllare l'ordine delle azioni eseguite. Le regole di automazione semplificano l'uso dell'automazione in Sentinel di Azure e consentono di semplificare i flussi di lavoro complessi per i processi di orchestrazione degli eventi imprevisti.

Scopri di più con questa [spiegazione completa delle regole di automazione](automate-incident-handling-with-automation-rules.md).

Come indicato in precedenza, è ora possibile attivare i PlayBook con il trigger di evento imprevisto oltre al trigger di avviso. Il trigger di evento imprevisto fornisce ai PlayBook un set di input più ampio da usare (poiché l'evento imprevisto include anche tutti i dati di avviso e di entità), garantendo una maggiore potenza e flessibilità nei flussi di lavoro di risposta. I PlayBook attivati dagli eventi imprevisti vengono attivati chiamando dalle regole di automazione.

Scopri di più sulle [funzionalità avanzate dei PlayBook](automate-responses-with-playbooks.md)e su come [creare un flusso di lavoro di risposta](tutorial-respond-threats-playbook.md) usando i PlayBook insieme alle regole di automazione.

### <a name="new-alert-enrichments-enhanced-entity-mapping-and-custom-details"></a>Nuovi miglioramenti degli avvisi: mapping di entità migliorato e dettagli personalizzati

Arricchisci gli avvisi in due nuovi modi per renderli più utilizzabili e informativi.

Per iniziare, eseguire il mapping dell'entità al livello successivo. È ora possibile eseguire il mapping di quasi 20 tipi di entità, da utenti, host e indirizzi IP, a file e processi, alle cassette postali, alle risorse di Azure e ai dispositivi Internet. È anche possibile usare più identificatori per ciascuna entità, per rafforzare la propria identificazione univoca. In questo modo si ottiene un set di dati molto più completo per gli eventi imprevisti, che fornisce una correlazione più ampia e un'analisi più potente. [Informazioni sul nuovo modo per eseguire il mapping delle entità](map-data-fields-to-entities.md) negli avvisi.

[Per altre informazioni sulle entità](entities-in-azure-sentinel.md) , vedere l' [elenco completo delle entità disponibili e i relativi identificatori](entities-reference.md).

Fornire le funzionalità di analisi e risposta a un miglioramento ancora maggiore personalizzando gli avvisi per la superficie dei dettagli degli eventi non elaborati. Offri la visibilità dei contenuti degli eventi agli eventi imprevisti, offrendo sempre più potenza e flessibilità per rispondere e analizzare le minacce per la sicurezza. [Informazioni su come visualizzare i dettagli personalizzati](surface-custom-details-in-alerts.md) negli avvisi.



### <a name="print-your-azure-sentinel-workbooks-or-save-as-pdf"></a>Stampa le cartelle di lavoro di Azure Sentinel o Salva come PDF

È ora possibile stampare cartelle di lavoro di Azure Sentinel, che consentono anche di esportarle in file PDF e di salvarle localmente o condividerle.

Nella cartella di lavoro di selezionare il menu opzioni > :::image type="icon" source="media/whats-new/print-icon.png" border="false"::: **stampa contenuto**. Selezionare quindi la stampante oppure selezionare **Salva con nome PDF** in base alle esigenze.

:::image type="content" source="media/whats-new/print-workbook.png" alt-text="Stampare la cartella di lavoro o salvarla in formato PDF.":::

Per altre informazioni, vedere [esercitazione: visualizzare e monitorare i dati](tutorial-monitor-your-data.md).

### <a name="incident-filters-and-sort-preferences-now-saved-in-your-session-public-preview"></a>Filtri per gli eventi imprevisti e preferenze di ordinamento ora salvati nella sessione (anteprima pubblica)

A questo punto, i filtri e l'ordinamento degli eventi imprevisti vengono salvati in tutta la sessione di Azure Sentinel, anche durante l'esplorazione ad altre aree del prodotto.
Fino a quando ci si trova ancora nella stessa sessione, tornando all'area [eventi imprevisti](tutorial-investigate-cases.md) in Sentinel di Azure vengono visualizzati i filtri e l'ordinamento proprio come è stato lasciato.

> [!NOTE]
> I filtri e l'ordinamento degli eventi imprevisti non vengono salvati dopo l'uscita da Azure Sentinel o l'aggiornamento del browser.

### <a name="microsoft-365-defender-incident-integration-public-preview"></a>Integrazione di Microsoft 365 Defender Incident (anteprima pubblica)

L'integrazione degli eventi imprevisti [Microsoft 365 Defender (M365D)](/microsoft-365/security/mtp/microsoft-threat-protection) di Azure Sentinel consente di trasmettere tutti gli eventi imprevisti M365D in Sentinel di Azure e di mantenerli sincronizzati tra entrambi i portali. Gli eventi imprevisti di M365D (precedentemente noto come Microsoft Threat Protection o MTP) includono tutti gli avvisi, le entità e le informazioni rilevanti associate, fornendo un contesto sufficiente per eseguire la valutazione e l'analisi preliminare in Sentinel di Azure. Una volta in Sentinel, gli eventi imprevisti rimarranno sincronizzati in modo bidirezionale con M365D, consentendo di sfruttare i vantaggi di entrambi i portali nell'analisi degli eventi imprevisti.

L'uso di Azure Sentinel e Microsoft 365 Defender insieme ti offre il meglio di entrambi i mondi. Si ottiene l'ampia gamma di informazioni che un SIEM offre all'intera organizzazione le risorse di informazioni, oltre alla profondità della potenza investigativa personalizzata e personalizzata offerta da un XDR per proteggere le risorse di Microsoft 365, entrambe coordinate e sincronizzate per un'operazione SOC senza problemi.

Per altre informazioni, vedere [Microsoft 365 Defender Integration with Azure Sentinel](microsoft-365-defender-sentinel-integration.md).

### <a name="new-microsoft-service-connectors-using-azure-policy"></a>Nuovi connettori del servizio Microsoft con criteri di Azure

[Criteri di Azure](../governance/policy/overview.md) è un servizio di Azure che consente di usare i criteri per applicare e controllare le proprietà di una risorsa. L'uso dei criteri garantisce che le risorse siano conformi agli standard di governance IT.

Tra le proprietà delle risorse che possono essere controllate dai criteri sono la creazione e la gestione dei log di diagnostica e di controllo. Azure Sentinel ora usa i criteri di Azure per consentire all'utente di applicare un set comune di impostazioni dei log di diagnostica a tutte le risorse (correnti e future) di un determinato tipo di cui si vuole inserire i log in Sentinel di Azure. Grazie ai criteri di Azure, non sarà più necessario impostare la risorsa Impostazioni log di diagnostica per risorsa.

I connettori basati su criteri di Azure sono ora disponibili per i servizi di Azure seguenti:
- [Azure Key Vault](connect-azure-key-vault.md) (anteprima pubblica)
- [Servizio Azure Kubernetes](connect-azure-kubernetes-service.md) (anteprima pubblica)
- Server/database SQL di Azure (GA)

I clienti potranno comunque inviare i log manualmente per le istanze specifiche e non devono usare il motore dei criteri.

## <a name="february-2021"></a>2021 febbraio

- [Cartella di lavoro Cybersecurity Maturity Model Certification (CMMC)](#cybersecurity-maturity-model-certification-cmmc-workbook)
- [Connettori dati di terze parti](#third-party-data-connectors)
- [OFFRONO dati Insights nella pagina entità (anteprima pubblica)](#ueba-insights-in-the-entity-page-public-preview)
- [Ricerca di eventi imprevisti migliorata (anteprima pubblica)](#improved-incident-search-public-preview)

### <a name="cybersecurity-maturity-model-certification-cmmc-workbook"></a>Cartella di lavoro Cybersecurity Maturity Model Certification (CMMC)

La cartella di lavoro CMMC di Azure Sentinel fornisce un meccanismo per visualizzare le query di log allineate ai controlli CMMC nel portfolio Microsoft, incluse le offerte di sicurezza Microsoft, Office 365, teams, Intune, desktop virtuale di Windows e molto altro ancora.

La cartella di lavoro di CMMC consente a architetti, tecnici, analisti di sicurezza, Manager e professionisti IT di ottenere visibilità sulla sicurezza dei carichi di lavoro cloud. Sono inoltre disponibili suggerimenti per la selezione, la progettazione, la distribuzione e la configurazione delle offerte Microsoft per l'allineamento con i requisiti e le procedure di CMMC rispettivi.

Anche se non è necessario conformarsi a CMMC, la cartella di lavoro di CMMC è utile per la creazione di centri operativi per la sicurezza, lo sviluppo di avvisi, la visualizzazione di minacce e la consapevolezza situazionale dei carichi di lavoro.

Accedere alla cartella di lavoro di CMMC nell'area **cartelle di lavoro** di Azure Sentinel. Selezionare **modello**, quindi cercare **CMMC**.

:::image type="content" source="media/whats-new/cmmc-guide-toggle.gif" alt-text="Attivare e disattivare la guida alla cartella di lavoro CMMC" lightbox="media/whats-new/cmmc-guide-toggle.gif":::


Per altre informazioni, vedere:

- [Cartella di lavoro di Azure Sentinel Cybersecurity SCAD Model Certification (CMMC)](https://techcommunity.microsoft.com/t5/public-sector-blog/azure-sentinel-cybersecurity-maturity-model-certification-cmmc/ba-p/2110524)
- [Esercitazione: Visualizzare e monitorare i dati](tutorial-monitor-your-data.md)


### <a name="third-party-data-connectors"></a>Connettori dati di terze parti

La nostra raccolta di integrazioni di terze parti continua a crescere, con trenta connettori aggiunti negli ultimi due mesi. Ecco un elenco:

- [Difesa del phishing di Agari e protezione del marchio](connect-agari-phishing-defense.md)
- [Eventi di sicurezza Akamai](connect-akamai-security-events.md)
- [Alsid per Active Directory](connect-alsid-active-directory.md)
- [Apache HTTP Server](connect-apache-http-server.md)
- [Aruba ClearPass](connect-aruba-clearpass.md)
- [CylancePROTECT BlackBerry](connect-data-sources.md)
- [Broadcom Symantec DLP](connect-broadcom-symantec-dlp.md)
- [Potenza di eStreamer Cisco](connect-data-sources.md)
- [Cisco Meraki](connect-cisco-meraki.md)
- [Cisco Umbrella](connect-cisco-umbrella.md)
- [Cisco Unified Computing System (UCS)](connect-cisco-ucs.md)
- [Controllo Enterprise di ESET](connect-data-sources.md)
- [Centro di gestione della sicurezza di ESET](connect-data-sources.md)
- [Area di lavoro Google (in precedenza G Suite)](connect-google-workspace.md)
- [WAF gateway impervertito](connect-imperva-waf-gateway.md)
- [Juniper SRX](connect-juniper-srx.md)
- [Netskope](connect-data-sources.md)
- [Log DNS di NXLog](connect-nxlog-dns.md)
- [Controllo di NXLog Linux](connect-nxlog-linuxaudit.md)
- [Piattaforma onside](connect-data-sources.md)
- [Sicurezza della posta elettronica su richiesta di Proofpoint (POD)](connect-proofpoint-pod.md)
- [Knowledge base sulla gestione delle vulnerabilità Qualys](connect-data-sources.md)
- [Cloud del servizio Salesforce](connect-salesforce-service-cloud.md)
- [Firewall SonicWall](connect-data-sources.md)
- [Sophos cloud Optix](connect-sophos-cloud-optix.md)
- [Squid Proxy](connect-squid-proxy.md)
- [Endpoint Protection Symantec](connect-data-sources.md)
- [Thycotic Secret Server](connect-thycotic-secret-server.md)
- [Micro XDR tendenza](connect-data-sources.md)
- [VMware ESXi](connect-vmware-esxi.md)

### <a name="ueba-insights-in-the-entity-page-public-preview"></a>OFFRONO dati Insights nella pagina entità (anteprima pubblica)

Le pagine dei dettagli dell'entità Sentinel di Azure forniscono un [riquadro Insights](identify-threats-with-entity-behavior-analytics.md#entity-insights), che consente di visualizzare informazioni sul comportamento sull'entità e di identificare rapidamente le anomalie e le minacce per la sicurezza.

Se la [offrono dati è abilitata](ueba-enrichments.md)e si è selezionato un intervallo di tempo di almeno quattro giorni, nel riquadro Insights verranno incluse anche le nuove sezioni seguenti per offrono dati Insights:

|Sezione  |Descrizione  |
|---------|---------|
|**Informazioni dettagliate su offrono dati**     | Riepiloga le attività utente anomale: <br>-In posizioni geografiche, dispositivi e ambienti<br>-Tra gli orizzonti di tempo e frequenza, rispetto alla cronologia dell'utente <br>-Confronto con il comportamento dei peer <br>-Rispetto al comportamento dell'organizzazione     |
|**Peer utente in base all'appartenenza al gruppo di sicurezza**     |   Elenca i peer degli utenti in base all'appartenenza Azure AD gruppi di sicurezza, fornendo ai team delle operazioni di sicurezza un elenco di altri utenti che condividono autorizzazioni simili.  |
|**Autorizzazioni di accesso utente per la sottoscrizione di Azure**     |     Mostra le autorizzazioni di accesso dell'utente per le sottoscrizioni di Azure accessibili direttamente oppure tramite Azure AD gruppi o entità servizio.   |
|**Indicatori di minaccia correlati all'utente**     |  Elenca una raccolta di minacce note relative agli indirizzi IP rappresentati nelle attività dell'utente. Le minacce sono elencate in base al tipo di minaccia e alla famiglia e sono arricchite dal servizio di intelligence per le minacce di Microsoft.       |
|     |         |

### <a name="improved-incident-search-public-preview"></a>Ricerca di eventi imprevisti migliorata (anteprima pubblica)

È stata migliorata l'esperienza di ricerca degli eventi imprevisti di Azure Sentinel, che consente di spostarsi più velocemente attraverso gli eventi imprevisti quando si esamina una minaccia specifica.

Quando si esegue la ricerca di eventi imprevisti in Sentinel di Azure, è ora possibile eseguire la ricerca in base ai dettagli dell'evento imprevisto seguente:

- ID
- Titolo
- Prodotto
- Proprietario
- Tag

## <a name="january-2021"></a>Gennaio 2021

- [Procedura guidata per le regole di analisi: miglioramento dell'esperienza di modifica delle query (anteprima pubblica)](#analytics-rule-wizard-improved-query-editing-experience-public-preview)
- [AZ. SecurityInsights PowerShell Module (anteprima pubblica)](#azsecurityinsights-powershell-module-public-preview)
- [Connettore database SQL](#sql-database-connector)
- [Connettore Dynamics 365 (anteprima pubblica)](#dynamics-365-connector-public-preview)
- [Commenti per gli eventi imprevisti migliorati](#improved-incident-comments)
- [Cluster Log Analytics dedicati](#dedicated-log-analytics-clusters)
- [Identità gestite di app per la logica](#logic-apps-managed-identities)
- [Ottimizzazione della regola migliorata con i grafici di anteprima della regola di analisi](#improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview)


### <a name="analytics-rule-wizard-improved-query-editing-experience-public-preview"></a>Procedura guidata per le regole di analisi: miglioramento dell'esperienza di modifica delle query (anteprima pubblica)

La creazione guidata regola di analisi pianificata di Azure Sentinel fornisce ora i miglioramenti seguenti per la scrittura e la modifica delle query:

-   Una finestra di modifica espandibile che consente di visualizzare più spazio sullo schermo per la query.
-   Evidenziazione della parola chiave nel codice della query.
-   Supporto esteso per il completamento automatico.
-   Convalide di query in tempo reale. Gli errori nella query sono ora visualizzati come un blocco rosso nella barra di scorrimento e come un punto rosso nel nome della scheda della **logica del set di regole** . Inoltre, non è possibile salvare una query con errori.

Per altre informazioni, vedere [esercitazione: creare regole di analisi personalizzate per rilevare le minacce](tutorial-detect-threats-custom.md).
### <a name="azsecurityinsights-powershell-module-public-preview"></a>AZ. SecurityInsights PowerShell Module (anteprima pubblica)

Azure Sentinel supporta ora il nuovo modulo di PowerShell [AZ. SecurityInsights](https://www.powershellgallery.com/packages/Az.SecurityInsights/) .

Il modulo **AZ. SecurityInsights** supporta i casi d'uso comuni di Azure Sentinel, ad esempio l'interazione con gli eventi imprevisti per modificare le statue, la gravità, il proprietario e così via, l'aggiunta di commenti ed etichette agli eventi imprevisti e la creazione di segnalibri.

Sebbene sia consigliabile usare i modelli di [Azure Resource Manager (ARM)](../azure-resource-manager/templates/index.yml) per la pipeline ci/CD, il modulo **AZ. SecurityInsights** è utile per le attività post-distribuzione ed è destinato all'automazione Soc.  Ad esempio, l'automazione SOC potrebbe includere passaggi per configurare i connettori di dati, creare regole di analisi o aggiungere azioni di automazione alle regole di analisi.

Per ulteriori informazioni, tra cui un elenco completo e una descrizione dei cmdlet disponibili, delle descrizioni dei parametri e degli esempi, vedere la [documentazione di PowerShell AZ. SecurityInsights](/powershell/module/az.securityinsights/).

### <a name="sql-database-connector"></a>Connettore database SQL

Azure Sentinel offre ora un connettore del database SQL di Azure, in cui è possibile trasmettere in streaming i log di controllo e di diagnostica dei database in Sentinel di Azure e monitorare continuamente l'attività in tutte le istanze.

Azure SQL è un motore di database PaaS (Platform-as-a-Service) completamente gestito che gestisce la maggior parte delle funzioni di gestione di database, ad esempio l'aggiornamento, l'applicazione di patch, i backup e il monitoraggio, senza coinvolgimento degli utenti.

Per altre informazioni, vedere [connettere i log di diagnostica e di controllo del database SQL di Azure](connect-azure-sql-logs.md).

### <a name="dynamics-365-connector-public-preview"></a>Connettore Dynamics 365 (anteprima pubblica)

Azure Sentinel offre ora un connettore per Microsoft Dynamics 365, che consente di raccogliere i log delle attività di utenti, amministratori e di supporto delle applicazioni di Dynamics 365 in Sentinel di Azure. È possibile utilizzare questi dati per controllare l'intera azione di elaborazione dei dati e analizzarla per individuare possibili violazioni della sicurezza.

Per altre informazioni, vedere [connettere i log attività di Dynamics 365 ad Azure Sentinel](connect-dynamics-365.md).

### <a name="improved-incident-comments"></a>Commenti per gli eventi imprevisti migliorati

Gli analisti usano i commenti per gli eventi imprevisti per collaborare agli eventi imprevisti, documentare i processi e i passaggi manualmente o come parte di un PlayBook. 

L'esperienza migliorata per i commenti degli eventi imprevisti consente di formattare i commenti e modificare o eliminare i commenti esistenti.

Per ulteriori informazioni, vedere [creazione automatica degli eventi imprevisti da avvisi di sicurezza Microsoft](create-incidents-from-alerts.md).
### <a name="dedicated-log-analytics-clusters"></a>Cluster Log Analytics dedicati

Azure Sentinel supporta ora cluster di Log Analytics dedicati come opzione di distribuzione. Si consiglia di prendere in considerazione un cluster dedicato se:

- Inserire **oltre 1 TB al giorno** nell'area di lavoro di Azure Sentinel
- **Avere più aree di lavoro di Azure Sentinel** nella registrazione di Azure

I cluster dedicati consentono di usare funzionalità quali chiavi gestite dal cliente, archivio protetto, crittografia doppia e query tra aree di lavoro più veloci quando si dispone di più aree di lavoro nello stesso cluster.

Per ulteriori informazioni, vedere [log di monitoraggio di Azure cluster dedicati](../azure-monitor/logs/logs-dedicated-clusters.md).

### <a name="logic-apps-managed-identities"></a>Identità gestite di app per la logica

Azure Sentinel supporta ora le identità gestite per il connettore Azure Sentinel per le app per la logica, consentendo di concedere le autorizzazioni direttamente a un PlayBook specifico per operare su Azure Sentinel anziché creare identità aggiuntive.

- **Senza un'identità gestita**, il connettore app per la logica richiede un'identità separata con un ruolo controllo degli accessi in base al ruolo di Azure Sentinel per l'esecuzione in Sentinel di Azure. L'identità separata può essere un utente Azure AD o un'entità servizio, ad esempio un'applicazione Azure AD registrata.

- L' **attivazione del supporto di identità gestite nell'app per la logica** registra l'app per la logica con Azure ad e fornisce un ID oggetto. Usare l'ID oggetto in Sentinel di Azure per assegnare l'app per la logica con un ruolo di controllo degli accessi in base al ruolo di Azure. 

Per altre informazioni, vedere:

- [Autenticazione con identità gestita in app per la logica di Azure](../logic-apps/create-managed-service-identity.md)
- [Documentazione del connettore app per la logica di Azure Sentinel](/connectors/azuresentinel) 

### <a name="improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview"></a>Ottimizzazione della regola migliorata con i grafici di anteprima della regola di analisi (anteprima pubblica)

Azure Sentinel consente ora di ottimizzare meglio le regole di analisi, in modo da aumentare la precisione e ridurre il rumore.

Dopo aver modificato una regola di analisi nella scheda **impostare la logica della regola** , trovare l'area **simulazione risultati** a destra. 

Selezionare **test con dati correnti** per fare in modo che Sentinel di Azure esegua una simulazione delle ultime 50 esecuzioni della regola di analisi. Viene generato un grafico per mostrare il numero medio di avvisi generati dalla regola, in base ai dati degli eventi non elaborati valutati. 

Per altre informazioni, vedere [definire la logica di query della regola e configurare le impostazioni](tutorial-detect-threats-custom.md#define-the-rule-query-logic-and-configure-settings).

## <a name="december-2020"></a>Dicembre 2020

- [80 nuove query di caccia predefinite](#80-new-built-in-hunting-queries)
- [Miglioramenti di Log Analytics Agent](#log-analytics-agent-improvements)

### <a name="80-new-built-in-hunting-queries"></a>80 nuove query di caccia predefinite
 
Le query di ricerca predefinite di Azure Sentinel consentono agli analisti SOC di ridurre i gap nella copertura di rilevamento corrente e di accendere nuovi lead di caccia.

Questo aggiornamento per il servizio Sentinel di Azure include nuove query di caccia che forniscono la copertura tra la matrice del Framework&CK:

- **Raccolta**
- **Comando e controllo**
- **Accesso alle credenziali**
- **Individuazione**
- **Esecuzione**
- **Esfiltrazione**
- **Impatto**
- **Accesso iniziale**
- **Persistenza**
- **Escalation dei privilegi**

Le query di ricerca aggiunte sono progettate per facilitare l'individuazione di attività sospette nell'ambiente in uso. Sebbene possano restituire attività legittime e attività potenzialmente dannose, possono essere utili per guidare la caccia. 

Se dopo l'esecuzione di queste query si è certi dei risultati, è possibile convertirli in regole di analisi o aggiungere i risultati della ricerca a eventi imprevisti nuovi o esistenti.

Tutte le query aggiunte sono disponibili tramite la pagina di ricerca di Azure Sentinel. Per altre informazioni, vedere [Hunt for Threats with Azure Sentinel](hunting.md).

### <a name="log-analytics-agent-improvements"></a>Miglioramenti di Log Analytics Agent

Gli utenti di Sentinel di Azure traggono vantaggio dai miglioramenti Log Analytics agenti seguenti:

- **Supporto per più sistemi operativi**, tra cui CentOS 8, RedHat 8 e SUSE Linux 15.
- **Supporto per Python 3** , oltre a Python 2

Azure Sentinel usa l'agente di Log Analytics per inviare eventi all'area di lavoro, inclusi eventi di sicurezza di Windows, eventi syslog, log CEF e altro ancora.

> [!NOTE]
> L'agente di Log Analytics viene talvolta definito agente OMS o Microsoft Monitoring Agent (MMA). 
> 

Per ulteriori informazioni, vedere la [documentazione di log Analytics](../azure-monitor/agents/log-analytics-agent.md) e le [Note sulla versione di log Analytics Agent](https://github.com/microsoft/OMS-Agent-for-Linux/releases).
## <a name="november-2020"></a>Novembre 2020

- [Monitora l'integrità dei PlayBook in Sentinel di Azure](#monitor-your-playbooks-health-in-azure-sentinel)
- [Connettore Microsoft 365 Defender (anteprima pubblica)](#microsoft-365-defender-connector-public-preview)

### <a name="monitor-your-playbooks-health-in-azure-sentinel"></a>Monitora l'integrità dei PlayBook in Sentinel di Azure

I PlayBook di Sentinel di Azure si basano sui flussi di lavoro compilati in [app log di Azure](../logic-apps/index.yml), un servizio cloud che consente di pianificare, automatizzare e orchestrare attività, processi aziendali e flussi di lavoro. I PlayBook possono essere richiamati automaticamente quando viene creato un evento imprevisto o durante la valutazione e l'utilizzo di eventi imprevisti. 

Per fornire informazioni dettagliate sull'integrità, sulle prestazioni e sull'utilizzo dei PlayBook, è stata aggiunta una [cartella di lavoro](../azure-monitor/visualize/workbooks-overview.md) denominata **Playbooks Health Monitoring**. 

Usare la cartella di lavoro di **monitoraggio dello stato dei PlayBook** per monitorare l'integrità dei PlayBook o cercare anomalie nella quantità di esecuzioni riuscite o non riuscite. 

La cartella di lavoro di **monitoraggio dell'integrità dei PlayBook** è ora disponibile nella raccolta di modelli di Sentinel di Azure:

:::image type="content" source="media/whats-new/playbook-monitoring-workbook.gif" alt-text="Cartella di lavoro di monitoraggio dello stato di PlayBook di esempio":::

Per altre informazioni, vedere:

- [Documentazione di app per la logica](../logic-apps/monitor-logic-apps-log-analytics.md#set-up-azure-monitor-logs)

- [Documentazione di Monitoraggio di Azure](../azure-monitor/essentials/activity-log.md#send-to-log-analytics-workspace)

### <a name="microsoft-365-defender-connector-public-preview"></a>Connettore Microsoft 365 Defender (anteprima pubblica)
 
Il connettore Microsoft 365 Defender per Azure Sentinel consente di eseguire lo streaming di log di caccia avanzati, ovvero un tipo di dati di eventi non elaborati, da Microsoft 365 Defender ad Azure Sentinel. 

Con l'integrazione di [Microsoft Defender for endpoint (MDATP)](/windows/security/threat-protection/) nel [Microsoft 365 Defender](/microsoft-365/security/mtp/microsoft-threat-protection) Security Umbrella, è ora possibile raccogliere gli eventi di caccia avanzati di Microsoft Defender per l'endpoint usando il connettore Microsoft 365 Defender e trasmetterli direttamente nelle nuove tabelle create per lo scopo nell'area di lavoro di Sentinel di Azure. 

Le tabelle di Azure Sentinel sono basate sullo stesso schema usato nel portale di Microsoft 365 Defender e offrono l'accesso completo al set completo di log di caccia avanzati. 

Per altre informazioni, vedere [connettere i dati da Microsoft 365 Defender ad Azure Sentinel](connect-microsoft-365-defender.md).

> [!NOTE]
> Microsoft 365 Defender era noto in precedenza come Microsoft Threat Protection o MTP. Microsoft Defender for endpoint era noto in precedenza come Microsoft Defender Advanced Threat Protection o MDATP.
> 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
>[Sentinella di Azure a bordo](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[Ottenere visibilità sugli avvisi](quickstart-get-visibility.md)
