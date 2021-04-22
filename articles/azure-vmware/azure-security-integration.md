---
title: Proteggere le soluzione Azure VMware virtuali con l'Centro sicurezza di Azure dati
description: Proteggere le soluzione Azure VMware virtuali con gli strumenti di sicurezza nativi di Azure dal dashboard Centro sicurezza di Azure sicurezza.
ms.topic: how-to
ms.date: 02/12/2021
ms.openlocfilehash: d04f0ac3e3934442ce5b6d5fbf4b53e18b3dff18
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877517"
---
# <a name="protect-your-azure-vmware-solution-vms-with-azure-security-center-integration"></a>Proteggere le soluzione Azure VMware virtuali con l'Centro sicurezza di Azure dati

Gli strumenti di sicurezza nativi di Azure offrono protezione per un ambiente ibrido di Azure, soluzione Azure VMware e macchine virtuali (VM) locali. Questo articolo illustra come configurare gli strumenti di Azure per la sicurezza dell'ambiente ibrido. Questi strumenti verranno utilizzati per identificare e affrontare varie minacce.

## <a name="azure-native-services"></a>Servizi nativi di Azure

Ecco un breve riepilogo dei servizi nativi di Azure:

- **Area di lavoro Log Analytics:** L'area di lavoro Log Analytics è un ambiente univoco per archiviare i dati di log. Ogni area di lavoro include un proprio repository di dati e una configurazione specifica. Le origini dati e le soluzioni sono configurate per archiviare i dati in un'area di lavoro specifica.
- **Centro sicurezza di Azure:** Centro sicurezza di Azure è un sistema unificato di gestione della sicurezza dell'infrastruttura. Potenzia la sicurezza dei data center e offre protezione avanzata dalle minacce nei carichi di lavoro ibridi nel cloud o in locale.
- **Azure Sentinel:** Azure Sentinel è una soluzione siEM (Security Information Event Management) nativa del cloud. Fornisce analisi della sicurezza, rilevamento degli avvisi e risposta automatica alle minacce in un ambiente.

## <a name="topology"></a>Topologia

![Diagramma che illustra l'architettura della sicurezza integrata di Azure.](media/azure-security-integration/azure-integrated-security-architecture.png)

L'agente di Log Analytics consente la raccolta di dati di log da azure, soluzione Azure VMware e macchine virtuali locali. I dati di log vengono inviati Monitoraggio di Azure log e vengono archiviati in un'area di lavoro Log Analytics. È possibile distribuire l'agente di Log Analytics usando il supporto delle estensioni [macchina](../azure-arc/servers/manage-vm-extensions.md) virtuale per server abilitati per Arc per macchine virtuali nuove ed esistenti. 

Dopo aver raccolto i log dall'area di lavoro Log Analytics, è possibile configurare l'area di lavoro Log Analytics con Centro sicurezza di Azure. Centro sicurezza di Azure valuterà lo stato di vulnerabilità delle soluzione Azure VMware e genererà un avviso per eventuali vulnerabilità critiche. Ad esempio, valuta le patch mancanti del sistema operativo, le configurazioni non corretta della sicurezza e [endpoint protection.](../security-center/security-center-services.md)

È possibile configurare l'area di lavoro Log Analytics con Azure Sentinel rilevamento degli avvisi, visibilità delle minacce, ricerca e risposta alle minacce. Nel diagramma precedente la Centro sicurezza di Azure è connessa a Azure Sentinel usando Centro sicurezza di Azure connettore. Centro sicurezza di Azure inoltrare la vulnerabilità dell'ambiente Azure Sentinel creare un evento imprevisto ed eseguire il mapping con altre minacce. È anche possibile creare la query delle regole pianificate per rilevare attività indesiderate e convertirla in eventi imprevisti.

## <a name="benefits"></a>Vantaggi

- I servizi nativi di Azure possono essere usati per la sicurezza dell'ambiente ibrido in Azure, soluzione Azure VMware e nei servizi locali.
- Usando un'area di lavoro Log Analytics, è possibile raccogliere i dati o i log in un singolo punto e presentare gli stessi dati a servizi nativi di Azure diversi.
- Centro sicurezza di Azure offre molte funzionalità, tra cui:
    - Monitoraggio dell'integrità dei file
    - Rilevamento di attacchi senza file
    - Valutazione delle patch del sistema operativo 
    - Valutazione degli errori di configurazione della sicurezza
    - Valutazione della protezione endpoint
- Azure Sentinel consente di:
    - Raccogliere dati su scala cloud tra tutti gli utenti, i dispositivi, le applicazioni e l'infrastruttura, sia in locale che in più cloud.
    - Rilevare minacce non rilevate in precedenza.
    - Analizzare le minacce con l'intelligenza artificiale e cercare attività sospette su larga scala.
    - Risponde rapidamente agli eventi imprevisti con orchestrazione e automazione integrate delle attività comuni.

## <a name="create-a-log-analytics-workspace"></a>Creare un'area di lavoro Log Analytics

Sarà necessaria un'area di lavoro Log Analytics per raccogliere dati da diverse origini. Per altre informazioni, vedere [Creare un'area di lavoro Log Analytics dal portale di Azure](../azure-monitor/logs/quick-create-workspace.md). 

## <a name="deploy-security-center-and-configure-azure-vmware-solution-vms"></a>Distribuire il Centro sicurezza e configurare soluzione Azure VMware virtuali

Centro sicurezza di Azure è uno strumento preconfigurato che non richiede la distribuzione. Nel riquadro portale di Azure cercare **Centro sicurezza** e selezionarlo.

### <a name="enable-azure-defender"></a>Abilitare Azure Defender

Azure Defender estende la Centro sicurezza di Azure avanzata della protezione dalle minacce nei carichi di lavoro ibridi, sia in locale che nel cloud. Per proteggere le macchine soluzione Azure VMware virtuali, è quindi necessario abilitare Azure Defender. 

1. Nel Centro sicurezza selezionare **Introduzione.**

2. Selezionare la **scheda Aggiorna** e quindi selezionare la sottoscrizione o l'area di lavoro. 

3. Selezionare **Aggiorna** per abilitare Azure Defender.

## <a name="add-azure-vmware-solution-vms-to-security-center"></a>Aggiungere soluzione Azure VMware macchine virtuali al Centro sicurezza

1. Nel portale di Azure cercare **Azure Arc** e selezionarlo.

2. In Risorse selezionare **Server e** quindi **+Aggiungi.**

    :::image type="content" source="media/azure-security-integration/add-server-to-azure-arc.png" alt-text="Screenshot che mostra la Azure Arc server per l'aggiunta di una macchina soluzione Azure VMware virtuale in Azure.":::

3. Selezionare **Genera script.**
 
    :::image type="content" source="media/azure-security-integration/add-server-using-script.png" alt-text="Screenshot della pagina Azure Arc che mostra l'opzione per l'aggiunta di un server tramite script interattivo."::: 
 
4. Nella **scheda Prerequisiti** selezionare **Avanti.**

5. Nella scheda **Dettagli risorsa** immettere i dettagli seguenti: 
    - Subscription
    - Resource group
    - Region 
    - Sistema operativo
    - Dettagli del server proxy
    
    Selezionare quindi **Avanti: Tag**.

6. Nella **scheda** Tag selezionare **Avanti.**

7. Nella scheda **Scarica ed esegui script** selezionare **Scarica**.

8. Specificare il sistema operativo ed eseguire lo script nella macchina soluzione Azure VMware virtuale.

## <a name="view-recommendations-and-passed-assessments"></a>Visualizzare le raccomandazioni e le valutazioni superate

1. Nella Centro sicurezza di Azure selezionare **Inventario** nel riquadro sinistro.

2. Per Tipo di risorsa selezionare **Server - Azure Arc**.
 
     :::image type="content" source="media/azure-security-integration/select-resource-in-security-center.png" alt-text="Screenshot della pagina Inventario Centro sicurezza di Azure server - Azure Arc selezionata in Tipo di risorsa.":::

3. Selezionare il nome della risorsa. Verrà visualizzata una pagina che mostra i dettagli sull'integrità della sicurezza della risorsa.

4. In **Elenco raccomandazioni** selezionare le **schede Raccomandazioni**, **Valutazioni superate** e Valutazioni **non disponibili** per visualizzare questi dettagli.

    :::image type="content" source="media/azure-security-integration/view-recommendations-assessments.png" alt-text="Screenshot dell'Centro sicurezza di Azure che mostra le raccomandazioni e le valutazioni di sicurezza.":::

## <a name="deploy-an-azure-sentinel-workspace"></a>Distribuire un'area Azure Sentinel lavoro

Azure Sentinel è basato su un'area di lavoro Log Analytics. Il primo passaggio nell'onboarding Azure Sentinel è selezionare l'area di lavoro Log Analytics che si vuole usare a tale scopo.

1. Nel portale di Azure cercare **Azure Sentinel** e selezionarlo.

2. Nella pagina Azure Sentinel aree di lavoro selezionare **+Aggiungi**.

3. Selezionare l'area di lavoro Log Analytics e selezionare **Aggiungi**.

## <a name="enable-data-collector-for-security-events-on-azure-vmware-solution-vms"></a>Abilitare l'agente di raccolta dati per gli eventi soluzione Azure VMware macchine virtuali

A questo punto è possibile connettersi Azure Sentinel origini dati, in questo caso eventi di sicurezza.

1. Nella pagina Azure Sentinel aree di lavoro selezionare l'area di lavoro configurata.

2. In Configurazione selezionare **Connettori dati**.

3. Nella colonna Nome connettore selezionare **Eventi di sicurezza nell'elenco** e quindi selezionare Apri **pagina connettore**.

4. Nella pagina del connettore selezionare gli eventi da trasmettere e quindi **selezionare Applica modifiche**.

    :::image type="content" source="media/azure-security-integration/select-events-you-want-to-stream.png" alt-text="Screenshot della pagina Eventi di sicurezza in Azure Sentinel in cui è possibile selezionare gli eventi da trasmettere.":::

## <a name="connect-azure-sentinel-with-azure-security-center"></a>Connettersi Azure Sentinel con Centro sicurezza di Azure  

1. Nella pagina Azure Sentinel di lavoro selezionare l'area di lavoro configurata.

2. In Configurazione selezionare **Connettori dati**.

3. Selezionare **Centro sicurezza di Azure** dall'elenco e quindi selezionare **Open connector page (Apri pagina connettore).**

    :::image type="content" source="media/azure-security-integration/connect-security-center-with-azure-sentinel.png" alt-text="Screenshot della pagina Connettori dati in Azure Sentinel che mostra la selezione per connettersi Centro sicurezza di Azure con Azure Sentinel.":::

4. Selezionare **Connetti** per connettere il Centro sicurezza di Azure con Azure Sentinel.

5. Abilitare **Crea evento imprevisto** per generare un evento imprevisto per Centro sicurezza di Azure.

## <a name="create-rules-to-identify-security-threats"></a>Creare regole per identificare le minacce alla sicurezza

Dopo aver connesso le origini dati Azure Sentinel, è possibile creare regole per generare avvisi per le minacce rilevate. Nell'esempio seguente verrà creata una regola per i tentativi di accesso a Windows Server con una password errata.

1. Nella pagina Azure Sentinel panoramica selezionare Analisi in **Configurazioni.**

2. In Configurazioni selezionare **Analisi.**

3. Selezionare **+Crea** e nell'elenco a discesa selezionare **Regola di query pianificata.**

4. Nella **scheda Generale** immettere le informazioni necessarie.

    - Nome
    - Descrizione
    - Tattiche
    - Gravità
    - Stato

    Selezionare **Avanti: Impostare la logica delle regole >**.

5. Nella scheda **Set rule logic (Imposta** logica regola) immettere le informazioni necessarie.

    - Query della regola (qui è illustrata la query di esempio)
    
        ```
        SecurityEvent
        |where Activity startswith '4625'
        |summarize count () by IpAddress,Computer
        |where count_ > 3
        ```
        
    - Esegui mapping entità
    - Pianificazione delle query
    - Soglia di avviso
    - Raggruppamento di eventi
    - Eliminazione degli avvisi

    Selezionare **Avanti**.

6. Nella scheda **Impostazioni evento imprevisto** abilitare Create **incidents from alerts triggered by this analytics rule** (Crea eventi imprevisti dagli avvisi attivati da questa regola di analisi) e selezionare **Next: Automated response >**.
 
    :::image type="content" source="media/azure-security-integration/create-new-analytic-rule-wizard.png" alt-text="Screenshot della creazione guidata delle regole analitiche per la creazione di una nuova regola in Azure Sentinel. Mostra Create incidents from alerts triggered by this rule (Crea eventi imprevisti da avvisi attivati da questa regola) come abilitato.":::

7. Selezionare **Avanti: Verifica >**.

8. Nella scheda **Rivedi e crea** esaminare le informazioni e selezionare **Crea.**

Dopo il terzo tentativo non riuscito di accesso a Windows Server, la regola creata attiva un evento imprevisto per ogni tentativo non riuscito.

## <a name="view-alerts"></a>Visualizzare gli avvisi

È possibile visualizzare gli eventi imprevisti generati con Azure Sentinel. È anche possibile assegnare eventi imprevisti e chiuderli dopo che sono stati risolti, tutti dall'interno Azure Sentinel.

1. Passare alla pagina di Azure Sentinel panoramica.

2. In Gestione minacce selezionare **Eventi imprevisti**.

3. Selezionare un evento imprevisto. È quindi possibile assegnare l'evento imprevisto a un team per la risoluzione.

    :::image type="content" source="media/azure-security-integration/assign-incident.png" alt-text="Screenshot della pagina Azure Sentinel eventi imprevisti con l'evento imprevisto selezionato e l'opzione per assegnare l'evento imprevisto per la risoluzione.":::

    Dopo aver risolto il problema, è possibile chiuderlo.

## <a name="hunt-security-threats-with-queries"></a>Eseguire la ricerca di minacce alla sicurezza con query

È possibile creare query o usare la query predefinita disponibile in Azure Sentinel per identificare le minacce nell'ambiente. La procedura seguente esegue una query predefinita.

1. Passare alla pagina di Azure Sentinel panoramica.

2. In Gestione delle minacce selezionare **Ricerca**. Viene visualizzato un elenco di query predefinite.

3. Selezionare una query e quindi **Esegui query**.

4. Selezionare **Visualizza risultati** per controllare i risultati.

### <a name="create-a-new-query"></a>Creare una nuova query

1.  In Gestione delle minacce selezionare **Ricerca** e quindi **+Nuova query.**

    :::image type="content" source="media/azure-security-integration/create-new-query.png" alt-text="Screenshot della Azure Sentinel Ricerca con + Nuova query evidenziata.":::

2. Immettere le informazioni seguenti per creare una query personalizzata.

    - Nome
    - Descrizione
    - Query personalizzata
    - Immettere il mapping
    - Tattiche
    
3. Selezionare **Crea**. È quindi possibile selezionare la query creata, **Esegui query** e **Visualizza risultati**.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver illustrato come proteggere le macchine virtuali soluzione Azure VMware, è possibile ottenere informazioni su:

- Uso del [dashboard Azure Defender](../security-center/azure-defender-dashboard.md)
- [Rilevamento avanzato di attacchi multi-installazione in Azure Sentinel](../azure-monitor/logs/quick-create-workspace.md)
- [Gestione del ciclo di vita soluzione Azure VMware macchine virtuali](lifecycle-management-of-azure-vmware-solution-vms.md)
