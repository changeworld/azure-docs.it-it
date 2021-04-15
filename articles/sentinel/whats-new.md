---
title: Novità di Azure Sentinel
description: Questo articolo descrive le nuove funzionalità Azure Sentinel degli ultimi mesi.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: 7f9a8cb54458999d8f20a258bc36241dfdbd0de8
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376036"
---
# <a name="whats-new-in-azure-sentinel"></a>Novità di Azure Sentinel

Questo articolo elenca le funzionalità recenti aggiunte per Azure Sentinel e le nuove funzionalità nei servizi correlati che offrono un'esperienza utente avanzata in Azure Sentinel.

Per informazioni sulle funzionalità precedenti disponibili, vedere i [blog della community tecnica](https://techcommunity.microsoft.com/t5/azure-sentinel/bg-p/AzureSentinelBlog/label-name/What's%20New).

Le funzionalità notate sono attualmente in anteprima. Le [condizioni aggiuntive per l'anteprima di Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) includono termini legali aggiuntivi che si applicano a funzionalità di Azure in versione beta, anteprima o diversamente non ancora disponibili a livello generale.


> [!TIP]
> I team di ricerca delle minacce di Microsoft contribuiscono a query, playbook, cartelle [](https://github.com/Azure/Azure-Sentinel) di lavoro e notebook alla community [di Azure Sentinel,](https://github.com/Azure/Azure-Sentinel)incluse query di ricerca specifiche che i team possono adattare e usare.
>
> È anche possibile contribuire. Partecipare alla community di [GitHub Azure Sentinel Threat Threat.](https://github.com/Azure/Azure-Sentinel/wiki)
>

## <a name="april-2021"></a>Aprile 2021

- [Sequenza temporale evento imprevisto (anteprima pubblica)](#incident-timeline-public-preview)

### <a name="incident-timeline-public-preview"></a>Sequenza temporale evento imprevisto (anteprima pubblica)

La prima scheda in una pagina dei dettagli dell'evento imprevisto è ora **Sequenza temporale,** che mostra una sequenza temporale di avvisi e segnalibri nell'evento imprevisto. La sequenza temporale di un evento imprevisto consente di comprendere meglio l'evento imprevisto e ricostruire la sequenza temporale delle attività degli utenti malintenzionati tra gli avvisi e i segnalibri correlati.

- Selezionare un elemento nella sequenza temporale per visualizzarne i dettagli, senza uscire dal contesto dell'evento imprevisto
- Filtrare il contenuto della sequenza temporale per visualizzare solo gli avvisi o i segnalibri o gli elementi con una gravità o una tattica MITRE specifici.
- È possibile selezionare il **collegamento ID avviso** di  sistema per visualizzare l'intero record o il collegamento Eventi per visualizzare gli eventi correlati **nell'area** Log.

Ad esempio:

:::image type="content" source="media/tutorial-investigate-cases/incident-timeline.png" alt-text="Scheda Sequenza temporale evento imprevisto":::

Per altre informazioni, vedere [Esercitazione: Analizzare gli eventi imprevisti con Azure Sentinel](tutorial-investigate-cases.md).

## <a name="march-2021"></a>Marzo 2021

- [Impostare le cartelle di lavoro per l'aggiornamento automatico in modalità di visualizzazione](#set-workbooks-to-automatically-refresh-while-in-view-mode)
- [Nuovi rilevamenti per Firewall di Azure](#new-detections-for-azure-firewall)
- [Regole di automazione e playbook attivati da eventi imprevisti](#automation-rules-and-incident-triggered-playbooks) (inclusa la nuova documentazione dei playbook)
- [Nuovi arricchimenti degli avvisi: mapping di entità avanzato e dettagli personalizzati](#new-alert-enrichments-enhanced-entity-mapping-and-custom-details)
- [Stampare le Azure Sentinel cartelle di lavoro o salvarle in formato PDF](#print-your-azure-sentinel-workbooks-or-save-as-pdf)
- [Filtri e preferenze di ordinamento degli eventi imprevisti ora salvati nella sessione (anteprima pubblica)](#incident-filters-and-sort-preferences-now-saved-in-your-session-public-preview)
- [Microsoft 365'integrazione degli eventi imprevisti di Defender (anteprima pubblica)](#microsoft-365-defender-incident-integration-public-preview)
- [Nuovi connettori di servizio Microsoft con Criteri di Azure](#new-microsoft-service-connectors-using-azure-policy)

### <a name="set-workbooks-to-automatically-refresh-while-in-view-mode"></a>Impostare le cartelle di lavoro per l'aggiornamento automatico in modalità di visualizzazione

Azure Sentinel gli utenti possono ora usare la nuova Monitoraggio di Azure [di aggiornare](https://techcommunity.microsoft.com/t5/azure-monitor/azure-workbooks-set-it-to-auto-refresh/ba-p/2228555) automaticamente i dati della cartella di lavoro durante una sessione di visualizzazione.

In ogni cartella di lavoro o modello di cartella di lavoro selezionare :::image type="icon" source="media/whats-new/auto-refresh-workbook.png" border="false"::: **Aggiornamento automatico** per visualizzare le opzioni dell'intervallo. Selezionare l'opzione che si vuole usare per la sessione di visualizzazione corrente e selezionare **Applica**.

- Gli intervalli di aggiornamento supportati vanno **da 5 minuti** a **1 giorno.**
- Per impostazione predefinita, l'aggiornamento automatico è disattivato. Per ottimizzare le prestazioni, l'aggiornamento automatico viene disattivato anche ogni volta che si chiude una cartella di lavoro e non viene eseguito in background. Riattivare l'aggiornamento automatico in base alle esigenze alla successiva apertura della cartella di lavoro.
- L'aggiornamento automatico viene sospeso durante la modifica di una cartella di lavoro e gli intervalli di aggiornamento automatico vengono riavviati ogni volta che si torna alla modalità di visualizzazione dalla modalità di modifica.

    Gli intervalli vengono riavviati anche se si aggiorna manualmente la cartella di lavoro selezionando il :::image type="icon" source="media/whats-new/manual-refresh-button.png" border="false"::: **pulsante** Aggiorna.

Per altre informazioni, vedere [Tutorial: Visualize and monitor your data](tutorial-monitor-your-data.md) and the Monitoraggio di Azure [documentation](../azure-monitor/visualize/workbooks-overview.md).

### <a name="new-detections-for-azure-firewall"></a>Nuovi rilevamenti per Firewall di Azure

Sono stati aggiunti diversi rilevamenti Firewall di Azure all'area [Analisi](import-threat-intelligence.md#analytics-puts-your-threat-indicators-to-work-detecting-potential-threats) in Azure Sentinel. Questi nuovi rilevamenti consentono ai team di sicurezza di ricevere avvisi se i computer nella rete interna tentano di eseguire query o di connettersi a nomi di dominio Internet o indirizzi IP associati a I/O noti, come definito nella query della regola di rilevamento.

I nuovi rilevamenti includono:

- [Solorigate Network Beacon](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/Solorigate-Network-Beacon.yaml)
- [Domini e hash PIÙ NOTI](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/GalliumIOCs.yaml)
- [IP IRIDIUM noto](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/IridiumIOCs.yaml)
- [Domini/IP del gruppo di Phosphorus noti](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/PHOSPHORUSMarch2019IOCs.yaml)
- [Domini THALLIUM inclusi nella rimozione della DCU](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ThalliumIOCs.yaml)
- [Hash maldoc correlato a RELATED noto](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ZincJan272021IOCs.yaml)
- [Domini di gruppo STRONTIUM noti](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/STRONTIUMJuly2019IOCs.yaml)
- [KPIIUM - I/O di dominio e IP - Marzo 2021](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/NOBELIUM_DomainIOCsMarch2021.yaml)


I rilevamenti per Firewall di Azure vengono aggiunti continuamente alla raccolta di modelli predefiniti. Per ottenere i rilevamenti più recenti per Firewall di Azure, in Modelli **di regola** filtrare le origini **dati** in base **Firewall di Azure**:

:::image type="content" source="media/whats-new/new-detections-analytics-efficiency-workbook.jpg" alt-text="Nuovi rilevamenti nella cartella di lavoro Efficienza analisi":::

Per altre informazioni, vedere [New detections for Firewall di Azure in Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-network-security/new-detections-for-azure-firewall-in-azure-sentinel/ba-p/2244958).

### <a name="automation-rules-and-incident-triggered-playbooks"></a>Regole di automazione e playbook attivati da eventi imprevisti

Le regole di automazione sono un nuovo concetto in Azure Sentinel, consentendo di gestire centralmente l'automazione della gestione degli eventi imprevisti. Oltre ad assegnare playbook agli eventi imprevisti (non solo agli avvisi come in precedenza), le regole di automazione consentono anche di automatizzare le risposte per più regole di analisi contemporaneamente, contrassegnare automaticamente, assegnare o chiudere eventi imprevisti senza la necessità di playbook e controllare l'ordine delle azioni eseguite. Le regole di automazione semplificano l'uso dell'automazione Azure Sentinel e consentono di semplificare flussi di lavoro complessi per i processi di orchestrazione degli eventi imprevisti.

Per altre informazioni, vedere questa [spiegazione completa delle regole di automazione.](automate-incident-handling-with-automation-rules.md)

Come accennato in precedenza, i playbook possono ora essere attivati con il trigger di evento imprevisto oltre al trigger di avviso. Il trigger dell'evento imprevisto offre ai playbook un set più ampio di input da usare(poiché l'evento imprevisto include anche tutti i dati di avviso e entità), offrendo maggiore potenza e flessibilità nei flussi di lavoro di risposta. I playbook attivati da eventi imprevisti vengono attivati mediante la chiamata da regole di automazione.

Altre informazioni sulle [funzionalità avanzate dei playbook](automate-responses-with-playbooks.md)e su come creare un flusso di lavoro di risposta usando i playbook insieme alle regole di automazione. [](tutorial-respond-threats-playbook.md)

### <a name="new-alert-enrichments-enhanced-entity-mapping-and-custom-details"></a>Nuovi arricchimenti degli avvisi: mapping di entità avanzato e dettagli personalizzati

Arricchire gli avvisi in due nuovi modi per renderli più utilizzabili e più informativi.

Per iniziare, portare il mapping dell'entità al livello successivo. È ora possibile eseguire il mapping di quasi 20 tipi di entità, da utenti, host e indirizzi IP, a file e processi, a cassette postali, risorse di Azure e dispositivi IoT. È anche possibile usare più identificatori per ogni entità per rafforzare l'identificazione univoca. Questo offre un set di dati molto più ricco negli eventi imprevisti, offrendo una correlazione più ampia e un'analisi più efficace. [Informazioni sul nuovo modo per eseguire il mapping delle](map-data-fields-to-entities.md) entità negli avvisi.

[Per altre informazioni sulle entità,](entities-in-azure-sentinel.md) vedere l'elenco completo delle [entità disponibili e i relativi identificatori.](entities-reference.md)

Offrire alle funzionalità di indagine e risposta un incremento ancora maggiore personalizzando gli avvisi per visualizzare i dettagli degli eventi non elaborati. La visibilità dei contenuti degli eventi offre maggiore potenza e flessibilità per rispondere e analizzare le minacce alla sicurezza. [Informazioni su come visualizzare i dettagli personalizzati](surface-custom-details-in-alerts.md) negli avvisi.



### <a name="print-your-azure-sentinel-workbooks-or-save-as-pdf"></a>Stampare le Azure Sentinel cartelle di lavoro o salvarle in formato PDF

È ora possibile stampare Azure Sentinel cartelle di lavoro, che consentono anche di esportarle in PDF e salvare in locale o condividere.

Nella cartella di lavoro selezionare il menu opzioni > :::image type="icon" source="media/whats-new/print-icon.png" border="false"::: **Stampa contenuto**. Selezionare quindi la stampante o selezionare **Salva come PDF in** base alle esigenze.

:::image type="content" source="media/whats-new/print-workbook.png" alt-text="Stampare la cartella di lavoro o salvarla in formato PDF.":::

Per altre informazioni, vedere [Esercitazione: Visualizzare e monitorare i dati.](tutorial-monitor-your-data.md)

### <a name="incident-filters-and-sort-preferences-now-saved-in-your-session-public-preview"></a>Filtri e preferenze di ordinamento degli eventi imprevisti ora salvati nella sessione (anteprima pubblica)

Ora i filtri e l'ordinamento degli eventi imprevisti vengono salvati Azure Sentinel sessione, anche durante il passaggio ad altre aree del prodotto.
Se si è ancora nella stessa sessione, tornando [all'area](tutorial-investigate-cases.md) Eventi imprevisti in Azure Sentinel mostra i filtri e l'ordinamento così come sono stati lasciati.

> [!NOTE]
> I filtri e l'ordinamento degli eventi imprevisti non vengono salvati dopo Azure Sentinel o dopo l'aggiornamento del browser.

### <a name="microsoft-365-defender-incident-integration-public-preview"></a>Microsoft 365'integrazione degli eventi imprevisti di Defender (anteprima pubblica)

l'integrazione degli eventi imprevisti di [Microsoft 365 Azure Sentinel Defender (M365D)](/microsoft-365/security/mtp/microsoft-threat-protection) di Azure Sentinel consente di trasmettere tutti gli eventi imprevisti M365D in Azure Sentinel e mantenerli sincronizzati tra entrambi i portali. Gli eventi imprevisti di M365D (noti in precedenza come Microsoft Threat Protection o MTP) includono tutti gli avvisi, le entità e le informazioni rilevanti associati, fornendo un contesto sufficiente per eseguire la valutazione e l'analisi preliminare in Azure Sentinel. In Sentinel gli eventi imprevisti rimarranno sincronizzati bidirezionale con M365D, consentendo di sfruttare i vantaggi di entrambi i portali nell'analisi degli eventi imprevisti.

L'Azure Sentinel e Microsoft 365 Defender insieme offre il meglio di entrambi i mondo. Si ottengono tutte le informazioni dettagliate che un SISTEMA SIEM offre nell'intero ambito delle risorse di informazioni dell'organizzazione e anche la profondità della potenza di attacco personalizzata e personalizzata che un XDR offre per proteggere le risorse di Microsoft 365, entrambe coordinate e sincronizzate per un funzionamento SOC trasparente.

Per altre informazioni, vedere [Integrazione Microsoft 365 Defender con Azure Sentinel](microsoft-365-defender-sentinel-integration.md).

### <a name="new-microsoft-service-connectors-using-azure-policy"></a>Nuovi connettori di servizi Microsoft che Criteri di Azure

[Criteri di Azure](../governance/policy/overview.md) è un servizio di Azure che consente di usare i criteri per applicare e controllare le proprietà di una risorsa. L'uso dei criteri garantisce che le risorse rimangano conformi agli standard di governance IT.

Tra le proprietà delle risorse che possono essere controllate dai criteri sono la creazione e la gestione dei log di diagnostica e di controllo. Azure Sentinel usa ora Criteri di Azure per consentire di applicare un set comune di impostazioni dei log di diagnostica a tutte le risorse (correnti e future) di un particolare tipo di cui si desidera inserire i log Azure Sentinel. Grazie a Criteri di Azure, non sarà più necessario impostare le impostazioni dei log di diagnostica per risorsa.

Criteri di Azure i connettori basati su Criteri di Azure sono ora disponibili per i servizi di Azure seguenti:
- [Azure Key Vault](connect-azure-key-vault.md) (anteprima pubblica)
- [servizio Azure Kubernetes](connect-azure-kubernetes-service.md) (anteprima pubblica)
- Azure SQL database/server (GA)

I clienti potranno comunque inviare i log manualmente per istanze specifiche e non devono usare il motore dei criteri.

## <a name="february-2021"></a>Febbraio 2021

- [Cartella di lavoro Cybersecurity Maturity Model Certification (CMMC)](#cybersecurity-maturity-model-certification-cmmc-workbook)
- [Connettori dati di terze parti](#third-party-data-connectors)
- [Informazioni dettagliate su UEBA nella pagina dell'entità (anteprima pubblica)](#ueba-insights-in-the-entity-page-public-preview)
- [Ricerca degli eventi imprevisti migliorata (anteprima pubblica)](#improved-incident-search-public-preview)

### <a name="cybersecurity-maturity-model-certification-cmmc-workbook"></a>Cartella di lavoro Cybersecurity Maturity Model Certification (CMMC)

La cartella di lavoro di CMMC di Azure Sentinel offre un meccanismo per la visualizzazione delle query di log allineate ai controlli CMMC nel portfolio Microsoft, incluse le offerte di sicurezza Microsoft, Office 365, Teams, Intune, Desktop virtuale Windows e molti altri.

La cartella di lavoro CMMC consente agli architetti della sicurezza, ai tecnici, agli analisti delle operazioni di sicurezza, ai responsabili e ai professionisti IT di acquisire visibilità di consapevolezza della situazione per la posizione di sicurezza dei carichi di lavoro cloud. Sono inoltre disponibili raccomandazioni per la selezione, la progettazione, la distribuzione e la configurazione delle offerte Microsoft per l'allineamento con i rispettivi requisiti e procedure di CMMC.

Anche se non è necessario essere conformi a CMMC, la cartella di lavoro cmmc è utile per la creazione di centri operazioni di sicurezza, lo sviluppo di avvisi, la visualizzazione delle minacce e la consapevolezza della situazione dei carichi di lavoro.

Accedere alla cartella di lavoro di CMMC nell'area Azure Sentinel cartelle **di lavoro.** Selezionare **Modello** e quindi cercare **CMMC.**

:::image type="content" source="media/whats-new/cmmc-guide-toggle.gif" alt-text="Attivare e disattivare la guida della cartella di lavoro di CMMC" lightbox="media/whats-new/cmmc-guide-toggle.gif":::


Per altre informazioni, vedere:

- [Azure Sentinel di lavoro di Cybersecurity Maturity Model Certification (CMMC)](https://techcommunity.microsoft.com/t5/public-sector-blog/azure-sentinel-cybersecurity-maturity-model-certification-cmmc/ba-p/2110524)
- [Esercitazione: Visualizzare e monitorare i dati](tutorial-monitor-your-data.md)


### <a name="third-party-data-connectors"></a>Connettori dati di terze parti

La raccolta di integrazioni di terze parti continua a crescere, con l'aggiunta di 30 connettori negli ultimi due mesi. Ecco un elenco:

- [Agari Phishing Defense e Protezione del marchio](connect-agari-phishing-defense.md)
- [Eventi di sicurezza di Akamai](connect-akamai-security-events.md)
- [Alsid per Active Directory](connect-alsid-active-directory.md)
- [Apache HTTP Server](connect-apache-http-server.md)
- [Aruba ClearPass](connect-aruba-clearpass.md)
- [Blackberry CylancePROTECT](connect-data-sources.md)
- [Broadcom Symantec DLP](connect-broadcom-symantec-dlp.md)
- [Cisco Firepower eStreamer](connect-data-sources.md)
- [Cisco Meraki](connect-cisco-meraki.md)
- [Cisco Umbrella](connect-cisco-umbrella.md)
- [Cisco Unified Computing System (UCS)](connect-cisco-ucs.md)
- [ESET Enterprise Inspector](connect-data-sources.md)
- [Centro gestione sicurezza ESET](connect-data-sources.md)
- [Google Workspace (in precedenza G Suite)](connect-google-workspace.md)
- [Imperva WAF Gateway](connect-imperva-waf-gateway.md)
- [Juniper SRX](connect-juniper-srx.md)
- [Netskope](connect-data-sources.md)
- [Log DNS NXLog](connect-nxlog-dns.md)
- [NXLog Linux Audit](connect-nxlog-linuxaudit.md)
- [Piattaforma Onapsis](connect-data-sources.md)
- [Proofpoint On Demand Email Security (POD)](connect-proofpoint-pod.md)
- [Qualys Vulnerability Management Knowledge Base](connect-data-sources.md)
- [Cloud del servizio Salesforce](connect-salesforce-service-cloud.md)
- [SonicWall Firewall](connect-data-sources.md)
- [Sophos Cloud Optix](connect-sophos-cloud-optix.md)
- [Squid Proxy](connect-squid-proxy.md)
- [Symantec Endpoint Protection](connect-data-sources.md)
- [Thycotic Secret Server](connect-thycotic-secret-server.md)
- [Trend Micro XDR](connect-data-sources.md)
- [VMware ESXi](connect-vmware-esxi.md)

### <a name="ueba-insights-in-the-entity-page-public-preview"></a>Informazioni dettagliate su UEBA nella pagina dell'entità (anteprima pubblica)

Le Azure Sentinel dettagli dell'entità [](identify-threats-with-entity-behavior-analytics.md#entity-insights)forniscono un riquadro Informazioni dettagliate che visualizza informazioni dettagliate sul comportamento sull'entità e consente di identificare rapidamente le anomalie e le minacce alla sicurezza.

Se [UEBA](ueba-enrichments.md)è abilitato e è stato selezionato un intervallo di tempo di almeno quattro giorni, questo riquadro Informazioni dettagliate includerà ora anche le nuove sezioni seguenti per informazioni dettagliate su UEBA:

|Sezione  |Descrizione  |
|---------|---------|
|**Informazioni dettagliate su UEBA**     | Riepiloga le attività degli utenti anomali: <br>- In posizioni geografiche, dispositivi e ambienti diversi<br>- Tra gli orizzonti di tempo e frequenza, rispetto alla cronologia dell'utente <br>- Rispetto al comportamento dei peer <br>- Rispetto al comportamento dell'organizzazione     |
|**Peer utente in base all'appartenenza al gruppo di sicurezza**     |   Elenca i peer dell'utente in base all'appartenenza Azure AD gruppi di sicurezza, fornendo ai team delle operazioni di sicurezza un elenco di altri utenti che condividono autorizzazioni simili.  |
|**Autorizzazioni di accesso utente per la sottoscrizione di Azure**     |     Mostra le autorizzazioni di accesso dell'utente alle sottoscrizioni di Azure accessibili direttamente o tramite Azure AD gruppi/entità servizio.   |
|**Indicatori di minaccia correlati all'utente**     |  Elenca una raccolta di minacce note relative agli indirizzi IP rappresentati nelle attività dell'utente. Le minacce sono elencate in base al tipo e alla famiglia di minacce e sono arricchite dal servizio di intelligence per le minacce di Microsoft.       |
|     |         |

### <a name="improved-incident-search-public-preview"></a>Ricerca degli eventi imprevisti migliorata (anteprima pubblica)

È stata migliorata l'esperienza Azure Sentinel ricerca di eventi imprevisti, consentendo di spostarsi più velocemente tra gli eventi imprevisti durante l'analisi di una minaccia specifica.

Quando si cercano eventi imprevisti in Azure Sentinel, è ora possibile eseguire la ricerca in base ai dettagli degli eventi imprevisti seguenti:

- ID
- Titolo
- Prodotto
- Proprietario
- Tag

## <a name="january-2021"></a>Gennaio 2021

- [Creazione guidata regola di Analisi: esperienza di modifica delle query migliorata (anteprima pubblica)](#analytics-rule-wizard-improved-query-editing-experience-public-preview)
- [Modulo Az.SecurityInsights di PowerShell (anteprima pubblica)](#azsecurityinsights-powershell-module-public-preview)
- [Connettore database SQL](#sql-database-connector)
- [Connettore Dynamics 365 (anteprima pubblica)](#dynamics-365-connector-public-preview)
- [Commenti migliorati per gli eventi imprevisti](#improved-incident-comments)
- [Cluster log analytics dedicati](#dedicated-log-analytics-clusters)
- [Identità gestite delle app per la logica](#logic-apps-managed-identities)
- [Miglioramento dell'ottimizzazione delle regole con i grafici di anteprima delle regole di analisi](#improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview)


### <a name="analytics-rule-wizard-improved-query-editing-experience-public-preview"></a>Creazione guidata regola di Analisi: esperienza di modifica delle query migliorata (anteprima pubblica)

La Azure Sentinel guidata regole di analisi pianificata offre ora i miglioramenti seguenti per la scrittura e la modifica di query:

-   Una finestra di modifica espandibile, che offre più spazio sullo schermo per visualizzare la query.
-   Evidenziazione delle parole chiave nel codice della query.
-   Supporto del completamento automatico esteso.
-   Convalide di query in tempo reale. Gli errori nella query vengono ora visualizzati come un blocco rosso nella barra di scorrimento e come un punto rosso nella **scheda Imposta nome della logica** della regola. Inoltre, non è possibile salvare una query con errori.

Per altre informazioni, vedere [Esercitazione: Creare regole di analisi personalizzate per rilevare le minacce.](tutorial-detect-threats-custom.md)
### <a name="azsecurityinsights-powershell-module-public-preview"></a>Modulo Az.SecurityInsights di PowerShell (anteprima pubblica)

Azure Sentinel ora supporta il nuovo [modulo Az.SecurityInsights](https://www.powershellgallery.com/packages/Az.SecurityInsights/) di PowerShell.

Il modulo **Az.SecurityInsights** supporta casi d'uso Azure Sentinel comuni, ad esempio l'interazione con gli eventi imprevisti per modificare il livello di gravità, la gravità, il proprietario e così via, l'aggiunta di commenti ed etichette agli eventi imprevisti e la creazione di segnalibri.

Sebbene sia consigliabile usare modelli [Azure Resource Manager (ARM)](../azure-resource-manager/templates/index.yml) per la pipeline CI/CD, il modulo **Az.SecurityInsights** è utile per le attività post-distribuzione ed è destinato all'automazione SOC.  Ad esempio, l'automazione SOC può includere passaggi per configurare i connettori dati, creare regole di analisi o aggiungere azioni di automazione alle regole di analisi.

Per altre informazioni, inclusi un elenco completo e una descrizione dei cmdlet disponibili, le descrizioni dei parametri e gli esempi, vedere la documentazione di [Az.SecurityInsights di PowerShell.](/powershell/module/az.securityinsights/)

### <a name="sql-database-connector"></a>Connettore di database SQL

Azure Sentinel ora fornisce un connettore di database Azure SQL, che consente di trasmettere i log di controllo e di diagnostica dei database Azure Sentinel e monitorare continuamente l'attività in tutte le istanze.

Azure SQL è un motore di database PaaS (Platform-as-a-Service) completamente gestito che gestisce la maggior parte delle funzioni di gestione del database, ad esempio l'aggiornamento, l'applicazione di patch, i backup e il monitoraggio, senza il coinvolgimento degli utenti.

Per altre informazioni, vedere [Connettersi Azure SQL log di diagnostica](connect-azure-sql-logs.md)e controllo del database .

### <a name="dynamics-365-connector-public-preview"></a>Connettore Dynamics 365 (anteprima pubblica)

Azure Sentinel ora fornisce un connettore per Microsoft Dynamics 365, che consente di raccogliere i log attività utente, amministratore e supporto delle applicazioni Dynamics 365 in Azure Sentinel. È possibile usare questi dati per controllare l'intera esecuzione di azioni di elaborazione dei dati e analizzarli alla ricerca di possibili violazioni della sicurezza.

Per altre informazioni, vedere Connettere i log attività di [Dynamics 365 Azure Sentinel](connect-dynamics-365.md).

### <a name="improved-incident-comments"></a>Commenti migliorati per gli eventi imprevisti

Gli analisti usano i commenti degli eventi imprevisti per collaborare agli eventi imprevisti, documentando i processi e i passaggi manualmente o come parte di un playbook. 

L'esperienza migliorata di commento agli eventi imprevisti consente di formattare i commenti e di modificare o eliminare i commenti esistenti.

Per altre informazioni, vedere [Creare automaticamente eventi imprevisti dagli avvisi di sicurezza Microsoft.](create-incidents-from-alerts.md)
### <a name="dedicated-log-analytics-clusters"></a>Cluster log analytics dedicati

Azure Sentinel ora supporta cluster log analytics dedicati come opzione di distribuzione. È consigliabile prendere in considerazione un cluster dedicato se:

- **Inserire più di 1 TB al giorno nell'area** di lavoro Azure Sentinel lavoro
- **Avere più Azure Sentinel di lavoro nella** registrazione di Azure

I cluster dedicati consentono di usare funzionalità come chiavi gestite dal cliente, lockbox, crittografia doppia e query più veloci tra aree di lavoro quando sono presenti più aree di lavoro nello stesso cluster.

Per altre informazioni, vedere [Monitoraggio di Azure di log dedicati.](../azure-monitor/logs/logs-dedicated-clusters.md)

### <a name="logic-apps-managed-identities"></a>Identità gestite delle app per la logica

Azure Sentinel ora supporta le identità gestite per il connettore app per la logica di Azure Sentinel, consentendo di concedere le autorizzazioni direttamente a un playbook specifico per operare su Azure Sentinel invece di creare identità aggiuntive.

- **Senza un'identità gestita,** il connettore app per la logica richiede un'identità separata con un Azure Sentinel di controllo degli accessi in base al ruolo per l'esecuzione in Azure Sentinel. L'identità separata può essere un Azure AD utente o un'entità servizio, ad esempio un'Azure AD un'applicazione registrata.

- **L'attivazione del supporto delle identità gestite nell'app per la logica** registra l'app per la logica con Azure AD e fornisce un ID oggetto. Usare l'ID oggetto in Azure Sentinel per assegnare l'app per la logica con un ruolo Controllo degli accessi in base al ruolo di Azure nell'area Azure Sentinel lavoro. 

Per altre informazioni, vedere:

- [Autenticazione con identità gestita in App per la logica di Azure](../logic-apps/create-managed-service-identity.md)
- [Azure Sentinel documentazione del connettore app per la logica](/connectors/azuresentinel) 

### <a name="improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview"></a>Miglioramento dell'ottimizzazione delle regole con i grafici di anteprima delle regole di analisi (anteprima pubblica)

Azure Sentinel ora consente di ottimizzare meglio le regole di analisi, consentendo di aumentarne l'accuratezza e ridurre il rumore.

Dopo aver modificato una regola di analisi nella scheda Imposta logica **regola,** trovare l'area **Simulazione** risultati a destra. 

Selezionare **Test con i dati correnti** per Azure Sentinel eseguire una simulazione delle ultime 50 esecuzioni della regola di analisi. Viene generato un grafico per mostrare il numero medio di avvisi generati dalla regola, in base ai dati degli eventi non elaborati valutati. 

Per altre informazioni, vedere [Definire la logica di query delle regole e configurare le impostazioni](tutorial-detect-threats-custom.md#define-the-rule-query-logic-and-configure-settings).

## <a name="december-2020"></a>Dicembre 2020

- [80 nuove query di ricerca incorporate](#80-new-built-in-hunting-queries)
- [Miglioramenti dell'agente di Log Analytics](#log-analytics-agent-improvements)

### <a name="80-new-built-in-hunting-queries"></a>80 nuove query di ricerca incorporate
 
Azure Sentinel query di ricerca incorporate consentono agli analisti SOC di ridurre le lacune nella copertura di rilevamento corrente e di accendere nuovi lead di ricerca.

Questo aggiornamento per Azure Sentinel include nuove query di ricerca che forniscono copertura nella matrice del framework MITRE ATT&CK:

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

Le query di ricerca aggiunte sono progettate per facilitare l'individuazione di attività sospette nell'ambiente. Anche se possono restituire attività legittime e potenzialmente dannose, possono essere utili per guidare la ricerca. 

Se dopo aver eseguito queste query si è certi dei risultati, è possibile convertirli in regole di analisi o aggiungere risultati di ricerca a eventi imprevisti nuovi o esistenti.

Tutte le query aggiunte sono disponibili tramite la Azure Sentinel Ricerca. Per altre informazioni, vedere [Cercare minacce con Azure Sentinel](hunting.md).

### <a name="log-analytics-agent-improvements"></a>Miglioramenti dell'agente di Log Analytics

Azure Sentinel utenti possono trarre vantaggio dai miglioramenti dell'agente di Log Analytics seguenti:

- **Supporto per più sistemi operativi,** tra cui CentOS 8, RedHat 8 e SUSE Linux 15.
- **Supporto per Python 3** oltre a Python 2

Azure Sentinel usa l'agente di Log Analytics per inviare eventi all'area di lavoro, inclusi eventi Sicurezza di Windows, eventi Syslog, log CEF e altro ancora.

> [!NOTE]
> L'agente di Log Analytics viene talvolta definito agente OMS o Microsoft Monitoring Agent (MMA). 
> 

Per altre informazioni, vedere la documentazione di [Log Analytics e](../azure-monitor/agents/log-analytics-agent.md) le note sulla versione [dell'agente di Log Analytics.](https://github.com/microsoft/OMS-Agent-for-Linux/releases)
## <a name="november-2020"></a>Novembre 2020

- [Monitorare l'integrità dei playbook in Azure Sentinel](#monitor-your-playbooks-health-in-azure-sentinel)
- [Microsoft 365 Defender Connector (anteprima pubblica)](#microsoft-365-defender-connector-public-preview)

### <a name="monitor-your-playbooks-health-in-azure-sentinel"></a>Monitorare l'integrità dei playbook in Azure Sentinel

Azure Sentinel playbook sono basati su flussi di lavoro creati in App log di [Azure,](../logic-apps/index.yml)un servizio cloud che consente di pianificare, automatizzare e orchestrare attività, processi aziendali e flussi di lavoro. I playbook possono essere richiamati automaticamente quando viene creato un evento imprevisto o quando si valutano e si lavora con gli eventi imprevisti. 

Per fornire informazioni dettagliate sull'integrità, le prestazioni e l'utilizzo dei playbook, è stata aggiunta una cartella di lavoro [denominata](../azure-monitor/visualize/workbooks-overview.md) Monitoraggio dell'integrità **dei playbook.** 

Usare la cartella di lavoro Di monitoraggio dell'integrità dei **playbook** per monitorare l'integrità dei playbook o cercare anomalie nella quantità di esecuzioni riuscite o non riuscite. 

La **cartella di lavoro Di monitoraggio dell'integrità** dei playbook è ora disponibile nella raccolta Azure Sentinel modelli:

:::image type="content" source="media/whats-new/playbook-monitoring-workbook.gif" alt-text="Cartella di lavoro di monitoraggio dell'integrità dei playbook di esempio":::

Per altre informazioni, vedere:

- [Documentazione di App per la logica](../logic-apps/monitor-logic-apps-log-analytics.md#set-up-azure-monitor-logs)

- [Documentazione di Monitoraggio di Azure](../azure-monitor/essentials/activity-log.md#send-to-log-analytics-workspace)

### <a name="microsoft-365-defender-connector-public-preview"></a>Microsoft 365 Defender Connector (anteprima pubblica)
 
Il connettore Microsoft 365 Defender per Azure Sentinel consente di trasmettere i log di ricerca avanzati (un tipo di dati di eventi non elaborati) da Microsoft 365 Defender a Azure Sentinel. 

Con l'integrazione di [Microsoft Defender for Endpoint (MDATP)](/windows/security/threat-protection/) nell'ombrello di sicurezza di Microsoft 365 [Defender,](/microsoft-365/security/mtp/microsoft-threat-protection) è ora possibile raccogliere gli eventi di ricerca avanzata di Microsoft Defender for Endpoint usando il connettore Microsoft 365 Defender e trasmettere i dati direttamente nelle nuove tabelle appositamente create nell'area di lavoro di Azure Sentinel. 

Le Azure Sentinel sono compilate sullo stesso schema usato nel portale di Microsoft 365 Defender e forniscono l'accesso completo al set completo di log di ricerca avanzati. 

Per altre informazioni, vedere [Connettere i dati da Microsoft 365 Defender a Azure Sentinel](connect-microsoft-365-defender.md).

> [!NOTE]
> Microsoft 365 Defender era precedentemente noto come Microsoft Threat Protection o MTP. Microsoft Defender per Endpoint era precedentemente noto come Microsoft Defender Advanced Threat Protection o MDATP.
> 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
>[On-board Azure Sentinel](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[Ottenere visibilità sugli avvisi](quickstart-get-visibility.md)
