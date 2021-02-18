---
title: Usare l'analisi delle modifiche dell'applicazione in monitoraggio di Azure per trovare i problemi delle app Web | Microsoft Docs
description: Usare l'analisi delle modifiche dell'applicazione in monitoraggio di Azure per risolvere i problemi dell'applicazione nei siti Live nel servizio app Azure.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/04/2020
ms.openlocfilehash: 0f541df091733c081c77e41ebff4d0d0d93dca96
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100573924"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>Usare l'analisi delle modifiche dell'applicazione (anteprima) in monitoraggio di Azure

Quando si verifica un problema o un'interruzione di un sito Live, determinare rapidamente la causa principale è fondamentale. Le soluzioni di monitoraggio standard potrebbero segnalare un problema. Potrebbero anche indicare quale componente ha avuto esito negativo. Tuttavia, questo avviso non descriverà mai immediatamente la cause dell'errore. Si sa che il sito ha funzionato cinque minuti fa e ora è danneggiato. Cosa è cambiato negli ultimi cinque minuti? Questa è la domanda che l'analisi delle modifiche dell'applicazione è progettata per rispondere in monitoraggio di Azure.

Basandosi sulle potenzialità di [Azure Resource Graph](../../governance/resource-graph/overview.md), l'analisi delle modifiche fornisce informazioni approfondite sulle modifiche apportate alle applicazioni Azure per aumentare l'osservabilità e ridurre MTTR (tempo medio per il ripristino).

> [!IMPORTANT]
> L'analisi delle modifiche è attualmente in anteprima. Questa versione di anteprima viene fornita senza un contratto di servizio. Questa versione non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero avere funzionalità vincolate. Per altre informazioni, vedere le [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview"></a>Panoramica

L'analisi delle modifiche rileva vari tipi di modifiche, dal livello dell'infrastruttura fino alla distribuzione delle applicazioni. Si tratta di un provider di risorse di Azure a livello di sottoscrizione che controlla le modifiche alle risorse nella sottoscrizione. L'analisi delle modifiche fornisce dati per diversi strumenti di diagnostica che consentono agli utenti di comprendere quali modifiche potrebbero avere causato problemi.

Il diagramma seguente illustra l'architettura dell'analisi delle modifiche:

![Diagramma dell'architettura del modo in cui l'analisi delle modifiche ottiene i dati delle modifiche e li fornisce agli strumenti client](./media/change-analysis/overview.png)

## <a name="supported-resource-types"></a>Tipi di risorsa supportati

Il servizio di analisi delle modifiche dell'applicazione supporta le modifiche a livello di proprietà delle risorse in tutti i tipi di risorse di Azure, incluse risorse comuni come
- Macchina virtuale
- Set di scalabilità di macchine virtuali
- Servizio app
- Servizio Azure Kubernetes
- Funzione di Azure
- Risorse di rete: gruppo di sicurezza di rete, rete virtuale, gateway applicazione e così via.
- Servizi dati: archiviazione, SQL, cache Redis, Cosmos DB e così via.

## <a name="data-sources"></a>Origini dati

Query di analisi delle modifiche dell'applicazione per Azure Resource Manager proprietà rilevate, configurazioni con proxy e modifiche all'app Web in-Guest. Inoltre, il servizio tiene traccia delle modifiche delle dipendenze delle risorse per diagnosticare e monitorare un'applicazione end-to-end.

### <a name="azure-resource-manager-tracked-properties-changes"></a>Modifiche alle proprietà rilevate Azure Resource Manager

Usando il [grafo delle risorse di Azure](../../governance/resource-graph/overview.md), l'analisi delle modifiche fornisce un record cronologico del modo in cui le risorse di Azure che ospitano l'applicazione sono state modificate nel tempo. È possibile rilevare le impostazioni rilevate, ad esempio le identità gestite, l'aggiornamento del sistema operativo della piattaforma e i nomi host.

### <a name="azure-resource-manager-proxied-setting-changes"></a>Azure Resource Manager modifiche alle impostazioni con proxy

Le impostazioni, ad esempio la regola di configurazione IP, le impostazioni TLS e le versioni di estensione, non sono ancora disponibili in Azure Resource Graph, quindi le query di analisi delle modifiche e calcolano queste modifiche in modo sicuro per fornire altri dettagli sulle modifiche apportate all'app.

### <a name="changes-in-web-app-deployment-and-configuration-in-guest-changes"></a>Modifiche alla distribuzione e alla configurazione dell'app Web (modifiche nel guest)

L'analisi delle modifiche acquisisce lo stato di distribuzione e configurazione di un'applicazione ogni 4 ore. Può rilevare, ad esempio, le modifiche nelle variabili di ambiente dell'applicazione. Lo strumento calcola le differenze e presenta le modifiche apportate. Diversamente dalle modifiche apportate Gestione risorse, le informazioni sulle modifiche alla distribuzione del codice potrebbero non essere immediatamente disponibili nello strumento. Per visualizzare le ultime modifiche apportate all'analisi delle modifiche, selezionare **Aggiorna**.

![Schermata del pulsante "analizza modifiche ora"](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Modifiche delle dipendenze

Anche le modifiche alle dipendenze delle risorse possono causare problemi in una risorsa. Se, ad esempio, un'app Web chiama in una cache Redis, lo SKU della cache Redis potrebbe influire sulle prestazioni dell'app Web. Un altro esempio è se la porta 22 è stata chiusa nel gruppo di sicurezza di rete di una macchina virtuale, causerà errori di connettività.

#### <a name="web-app-diagnose-and-solve-problems-navigator-preview"></a>Strumento di esplorazione della diagnostica e risoluzione dei problemi dell'app Web (anteprima)

Per rilevare le modifiche nelle dipendenze, l'analisi delle modifiche controlla il record DNS dell'app Web. In questo modo, identifica le modifiche in tutti i componenti dell'app che possono causare problemi.
Attualmente le dipendenze seguenti sono supportate in **diagnostica e risoluzione dei problemi delle app Web | Strumento di navigazione (anteprima)**:

- App Web
- Archiviazione di Azure
- SQL di Azure

#### <a name="related-resources"></a>Risorse correlate

L'analisi delle modifiche dell'applicazione rileva le risorse correlate. Esempi comuni sono il gruppo di sicurezza di rete, la rete virtuale, il gateway applicazione e Load Balancer correlati a una macchina virtuale.
Il provisioning delle risorse di rete viene in genere eseguito automaticamente nello stesso gruppo di risorse delle risorse che lo usano, quindi il filtraggio delle modifiche per gruppo di risorse mostrerà tutte le modifiche per la macchina virtuale e le risorse di rete correlate.

![Screenshot delle modifiche di rete](./media/change-analysis/network-changes.png)

## <a name="application-change-analysis-service-enablement"></a>Abilitazione del servizio di analisi delle modifiche dell'applicazione

Il servizio di analisi delle modifiche dell'applicazione calcola e aggrega i dati delle modifiche dalle origini dati indicate in precedenza. Fornisce un set di analisi per consentire agli utenti di spostarsi agevolmente in tutte le modifiche alle risorse e identificare le modifiche rilevanti nel contesto di risoluzione dei problemi o di monitoraggio.
Il provider di risorse "Microsoft. ChangeAnalysis" deve essere registrato con una sottoscrizione per il Azure Resource Manager proprietà rilevate e le impostazioni con proxy modificare i dati in modo che siano disponibili. Quando si immette lo strumento per la diagnosi e la risoluzione dei problemi dell'app Web o si apre la scheda autonoma Change Analysis, questo provider di risorse viene registrato automaticamente.
Per le modifiche all'app Web in-Guest, è necessario abilitare separatamente per la scansione dei file di codice all'interno di un'app Web. Per ulteriori informazioni, vedere l'articolo relativo all' [analisi delle modifiche nella sezione strumento di diagnostica e risoluzione dei problemi](change-analysis-visualizations.md#application-change-analysis-in-the-diagnose-and-solve-problems-tool) più avanti in questo articolo.

## <a name="cost"></a>Costo
L'analisi delle modifiche dell'applicazione è un servizio gratuito che non comporta costi di fatturazione per le sottoscrizioni abilitate. Anche il servizio non ha alcun effetto sulle prestazioni per l'analisi delle modifiche delle proprietà delle risorse di Azure. Quando si Abilita l'analisi delle modifiche per le app Web nelle modifiche dei file Guest (o si Abilita lo strumento diagnostica e Risolvi i problemi), questo avrà un impatto trascurabile sulle prestazioni dell'app Web e nessun costo per la fatturazione.

## <a name="visualizations-for-application-change-analysis"></a>Visualizzazioni per l'analisi delle modifiche dell'applicazione

### <a name="standalone-ui"></a>Interfaccia utente autonoma

In monitoraggio di Azure è disponibile un riquadro autonomo per l'analisi delle modifiche che consente di visualizzare tutte le modifiche con informazioni dettagliate sulle dipendenze dell'applicazione e sulle risorse.

Cercare Change Analysis nella barra di ricerca portale di Azure per avviare l'esperienza.

![Screenshot della ricerca dell'analisi delle modifiche in portale di Azure](./media/change-analysis/search-change-analysis.png)

Tutte le risorse in una sottoscrizione selezionata vengono visualizzate con le modifiche apportate nelle ultime 24 ore. Per ottimizzare le prestazioni di caricamento della pagina, il servizio Visualizza 10 risorse alla volta. Fare clic su pagine successive per visualizzare altre risorse. Si sta lavorando per rimuovere questa limitazione.

![Screenshot del pannello di analisi delle modifiche in portale di Azure](./media/change-analysis/change-analysis-standalone-blade.png)

Fare clic su una risorsa per visualizzare tutte le modifiche apportate. Se necessario, eseguire il drill-down in una modifica per visualizzare i dettagli delle modifiche in formato JSON e le informazioni dettagliate.

![Schermata dei dettagli delle modifiche](./media/change-analysis/change-details.png)

Per commenti e suggerimenti, usare il pulsante Invia commenti e suggerimenti nel pannello o nel messaggio di posta elettronica changeanalysisteam@microsoft.com .

![Screenshot del pulsante feedback nel pannello Change Analysis](./media/change-analysis/change-analysis-feedback.png)

#### <a name="multiple-subscription-support"></a>Supporto per più sottoscrizioni
L'interfaccia utente supporta la selezione di più sottoscrizioni per visualizzare le modifiche alle risorse. Usare il filtro della sottoscrizione:

![Screenshot del filtro di sottoscrizione che supporta la selezione di più sottoscrizioni](./media/change-analysis/multiple-subscriptions-support.png)

### <a name="web-app-diagnose-and-solve-problems"></a>Diagnostica e risoluzione dei problemi delle app Web

In monitoraggio di Azure, l'analisi delle modifiche è incorporata anche nell'esperienza di **diagnostica e risoluzione dei problemi** in modalità self-service. Accedere a questa esperienza dalla pagina **Panoramica** dell'applicazione del servizio app.

![Screenshot del pulsante "panoramica" e del pulsante "diagnostica e risoluzione dei problemi"](./media/change-analysis/change-analysis.png)

### <a name="application-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>Analisi delle modifiche dell'applicazione nello strumento diagnostica e risoluzione dei problemi

L'analisi delle modifiche dell'applicazione è un detector autonomo negli strumenti per la diagnosi e la risoluzione dei problemi dell'app Web. Viene anche aggregato in **arresti anomali dell'applicazione** e **rilevatori di app Web**. Quando si immette lo strumento diagnostica e Risolvi i problemi, il provider di risorse **Microsoft. ChangeAnalysis** verrà registrato automaticamente. Seguire queste istruzioni per abilitare il rilevamento delle modifiche in-Guest per l'app Web.

1. Selezionare **disponibilità e prestazioni**.

    ![Screenshot delle opzioni di risoluzione dei problemi di disponibilità e prestazioni](./media/change-analysis/availability-and-performance.png)

2. Selezionare **le modifiche apportate all'applicazione**. La funzionalità è disponibile anche negli **arresti anomali dell'applicazione**.

   ![Screenshot del pulsante "arresti anomali dell'applicazione"](./media/change-analysis/application-changes.png)

3. Il collegamento consente di modificare l'applicazione dell'interfaccia utente di Aalysis nell'ambito dell'app Web. Se il rilevamento delle modifiche nell'app Web in-Guest non è abilitato, seguire il banner per ottenere le modifiche alle impostazioni di file e app.

   ![Screenshot delle opzioni "arresti anomali dell'applicazione"](./media/change-analysis/enable-changeanalysis.png)

4. Attivare l' **analisi delle modifiche** e selezionare **Salva**. Lo strumento Visualizza tutte le app Web in un piano di servizio app. È possibile usare l'opzione livello piano per attivare l'analisi delle modifiche per tutte le app Web in un piano.

    ![Screenshot dell'interfaccia utente "Enable Change Analysis"](./media/change-analysis/change-analysis-on.png)

5. I dati delle modifiche sono disponibili anche in selezione **app Web** e rilevamenti **arresti anomali dell'applicazione** . Verrà visualizzato un grafico che riepiloga il tipo di modifiche nel tempo insieme ai dettagli relativi a tali modifiche. Per impostazione predefinita, le modifiche apportate nelle ultime 24 ore vengono visualizzate per facilitare i problemi immediatamente.

     ![Screenshot della visualizzazione delle differenze delle modifiche](./media/change-analysis/change-view.png)



### <a name="virtual-machine-diagnose-and-solve-problems"></a>Diagnostica e risoluzione dei problemi della macchina virtuale

Passare allo strumento diagnostica e risoluzione dei problemi per una macchina virtuale.  Passare a **strumenti per la risoluzione dei problemi**, esplorare la pagina e selezionare **analizza modifiche recenti** per visualizzare le modifiche apportate alla macchina virtuale.

![Screenshot della macchina virtuale diagnosticare e risolvere i problemi](./media/change-analysis/vm-dnsp-troubleshootingtools.png)

![Analizzatore modifiche in strumenti di risoluzione dei problemi](./media/change-analysis/analyze-recent-changes.png)

### <a name="activity-log-change-history"></a>Cronologia modifiche log attività
La funzionalità [Visualizza cronologia modifiche](../essentials/activity-log.md#view-change-history) nel log attività chiama il back-end del servizio di analisi delle modifiche dell'applicazione per ottenere le modifiche associate a un'operazione. **Cronologia delle modifiche** usata per chiamare direttamente il [grafo delle risorse di Azure](../../governance/resource-graph/overview.md) , ma lo scambio del back-end per chiamare l'analisi delle modifiche dell'applicazione in modo che le modifiche restituite includano le modifiche a livello di risorsa da [Azure Resource Graph](../../governance/resource-graph/overview.md), le proprietà delle risorse da [Azure Resource Manager](../../azure-resource-manager/management/overview.md)e le modifiche in-Guest dai servizi di PaaS, ad esempio app Web Affinché il servizio di analisi delle modifiche dell'applicazione sia in grado di analizzare le modifiche apportate alle sottoscrizioni degli utenti, è necessario registrare un provider di risorse. La prima volta che si immette la scheda **cronologia modifiche** , lo strumento inizierà automaticamente a registrare il provider di risorse **Microsoft. ChangeAnalysis** . Dopo la registrazione, le modifiche da **Azure Resource Graph** saranno disponibili immediatamente e copriranno gli ultimi 14 giorni. Le modifiche apportate da altre origini saranno disponibili dopo circa 4 ore dopo l'onboarding della sottoscrizione.

![Integrazione cronologia modifiche log attività](./media/change-analysis/activity-log-change-history.png)

### <a name="vm-insights-integration"></a>Integrazione di VM Insights
Gli utenti che hanno abilitato [VM Insights](../vm/vminsights-overview.md) possono visualizzare le modifiche apportate alle macchine virtuali che potrebbero causare picchi in un grafico delle metriche, ad esempio CPU o memoria e chiedersi cosa ha causato. I dati delle modifiche sono integrati nella barra di spostamento sul lato di VM Insights. L'utente può visualizzare se sono state apportate modifiche alla macchina virtuale e fare clic su verifica **modifiche** per visualizzare i dettagli delle modifiche nell'interfaccia utente autonoma di analisi modifiche applicazione.

[![Integrazione di VM Insights](./media/change-analysis/vm-insights.png)](./media/change-analysis/vm-insights.png#lightbox)


## <a name="enable-change-analysis-at-scale"></a>Abilitare l'analisi delle modifiche su larga scala

Se la sottoscrizione include numerose app Web, l'abilitazione del servizio al livello dell'app Web risulterebbe inefficiente. Eseguire lo script seguente per abilitare tutte le app Web nella sottoscrizione.

Prerequisiti:

- PowerShell AZ Module. Seguire le istruzioni in [installare il modulo Azure PowerShell](/powershell/azure/install-az-ps)

Eseguire lo script riportato di seguito:

```PowerShell
# Log in to your Azure subscription
Connect-AzAccount

# Get subscription Id
$SubscriptionId = Read-Host -Prompt 'Input your subscription Id'

# Make Feature Flag visible to the subscription
Set-AzContext -SubscriptionId $SubscriptionId

# Register resource provider
Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"

# Enable each web app
$webapp_list = Get-AzWebApp | Where-Object {$_.kind -eq 'app'}
foreach ($webapp in $webapp_list)
{
    $tags = $webapp.Tags
    $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
    Set-AzResource -ResourceId $webapp.Id -Tag $tags -Force
}

```

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle [visualizzazioni nell'analisi delle modifiche](change-analysis-visualizations.md)
- Informazioni su come [risolvere i problemi nell'analisi delle modifiche](change-analysis-troubleshoot.md)
- Abilitare Application Insights per le [app di app Azure Services](azure-web-apps.md).
- Abilitare Application Insights per [le macchine virtuali di Azure e i set di scalabilità di macchine virtuali di Azure con IIS](azure-vm-vmss-apps.md).
