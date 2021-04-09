---
title: Proteggi le VM della soluzione VMware di Azure con l'integrazione del Centro sicurezza di Azure
description: Proteggi le VM della soluzione VMware di Azure con gli strumenti di sicurezza nativi di Azure dal dashboard del Centro sicurezza di Azure.
ms.topic: how-to
ms.date: 02/12/2021
ms.openlocfilehash: 7292ea4486a61f5b0cfd8f656d2763a3ce655e79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100578248"
---
# <a name="protect-your-azure-vmware-solution-vms-with-azure-security-center-integration"></a>Proteggi le VM della soluzione VMware di Azure con l'integrazione del Centro sicurezza di Azure

Gli strumenti di sicurezza nativi di Azure offrono protezione per un ambiente ibrido di Azure, una soluzione VMware di Azure e macchine virtuali (VM) locali. Questo articolo illustra come configurare gli strumenti di Azure per la sicurezza dell'ambiente ibrido. Questi strumenti verranno usati per identificare e risolvere varie minacce.

## <a name="azure-native-services"></a>Servizi nativi di Azure

Ecco un breve riepilogo dei servizi nativi di Azure:

- **Log Analytics area di lavoro:** Log Analytics area di lavoro è un ambiente univoco per archiviare i dati di log. Ogni area di lavoro include un proprio repository di dati e una configurazione specifica. Le origini dati e le soluzioni sono configurate per archiviare i dati in un'area di lavoro specifica.
- **Centro sicurezza di Azure:** Il Centro sicurezza di Azure è un sistema di gestione della sicurezza dell'infrastruttura unificata. Rafforza la sicurezza dei data center e offre protezione avanzata dalle minacce nei carichi di lavoro ibridi nel cloud o in locale.
- **Sentinel di Azure:** Azure Sentinel è una soluzione di gestione degli eventi di sicurezza (SIEM) nativa del cloud. Fornisce analisi della sicurezza, rilevamento degli avvisi e risposta automatica alle minacce in un ambiente.

## <a name="topology"></a>Topologia

![Diagramma che illustra l'architettura della sicurezza integrata di Azure.](media/azure-security-integration/azure-integrated-security-architecture.png)

L'agente Log Analytics Abilita la raccolta di dati di log da Azure, dalla soluzione VMware di Azure e dalle macchine virtuali locali. I dati di log vengono inviati ai log di monitoraggio di Azure e vengono archiviati in un'area di lavoro Log Analytics. È possibile distribuire l'agente di Log Analytics usando i server con Arc abilitato [estensioni VM supporto](../azure-arc/servers/manage-vm-extensions.md) per macchine virtuali nuove ed esistenti. 

Una volta raccolti i log dall'area di lavoro Log Analytics, è possibile configurare l'area di lavoro Log Analytics con il Centro sicurezza di Azure. Il Centro sicurezza di Azure valuterà lo stato della vulnerabilità delle macchine virtuali della soluzione VMware di Azure e genererà un avviso per eventuali vulnerabilità critiche. Ad esempio, valuta le patch del sistema operativo mancanti, le configurazioni di sicurezza e gli [Endpoint Protection](../security-center/security-center-services.md).

È possibile configurare l'area di lavoro Log Analytics con Azure Sentinel per il rilevamento degli avvisi, la visibilità delle minacce, la ricerca e la risposta alle minacce. Nel diagramma precedente, il Centro sicurezza di Azure è connesso ad Azure Sentinel usando il connettore del Centro sicurezza di Azure. Il Centro sicurezza di Azure Invia la vulnerabilità dell'ambiente a Sentinel di Azure per creare un evento imprevisto ed eseguire il mapping con altre minacce. È inoltre possibile creare la query delle regole pianificate per rilevare attività indesiderate e convertirle in eventi imprevisti.

## <a name="benefits"></a>Vantaggi

- I servizi nativi di Azure possono essere usati per la sicurezza dell'ambiente ibrido in Azure, nella soluzione VMware di Azure e nei servizi locali.
- Usando un'area di lavoro Log Analytics, è possibile raccogliere i dati o i log in un singolo punto e presentare gli stessi dati a diversi servizi nativi di Azure.
- Il Centro sicurezza di Azure offre molte funzionalità, tra cui:
    - Monitoraggio dell'integrità dei file
    - Rilevamento di attacchi non file
    - Valutazione patch del sistema operativo 
    - Valutazione degli errori di configurazione della sicurezza
    - Valutazione della protezione endpoint
- Azure Sentinel consente di:
    - Raccogli i dati su scala cloud in tutti gli utenti, i dispositivi, le applicazioni e l'infrastruttura, sia in locale che in più cloud.
    - Rilevare le minacce precedentemente non rilevate.
    - Esamina le minacce con l'intelligenza artificiale e cerca le attività sospette su larga scala.
    - Risponde rapidamente agli eventi imprevisti con orchestrazione e automazione integrate delle attività comuni.

## <a name="create-a-log-analytics-workspace"></a>Creare un'area di lavoro Log Analytics

Per raccogliere dati da diverse origini, è necessaria un'area di lavoro Log Analytics. Per ulteriori informazioni, vedere [la pagina relativa alla creazione di un'area di lavoro log Analytics dalla portale di Azure](../azure-monitor/logs/quick-create-workspace.md). 

## <a name="deploy-security-center-and-configure-azure-vmware-solution-vms"></a>Distribuire il Centro sicurezza e configurare le macchine virtuali della soluzione VMware di Azure

Il Centro sicurezza di Azure è uno strumento preconfigurato che non richiede la distribuzione. Nella portale di Azure cercare **Centro sicurezza** e selezionarlo.

### <a name="enable-azure-defender"></a>Abilitare Azure Defender

Azure Defender estende la protezione avanzata dalle minacce del Centro sicurezza di Azure nei tuoi carichi di lavoro ibridi, sia in locale che nel cloud. Per proteggere le macchine virtuali della soluzione VMware di Azure, è necessario abilitare Azure Defender. 

1. Nel centro sicurezza selezionare **Guida introduttiva**.

2. Selezionare la scheda **aggiornamento** , quindi selezionare la sottoscrizione o l'area di lavoro. 

3. Selezionare **Aggiorna** per abilitare Azure Defender.

## <a name="add-azure-vmware-solution-vms-to-security-center"></a>Aggiungere macchine virtuali della soluzione VMware di Azure al centro sicurezza

1. Nella portale di Azure cercare **Azure Arc** e selezionarlo.

2. In risorse selezionare **Server** e quindi **+ Aggiungi**.

    :::image type="content" source="media/azure-security-integration/add-server-to-azure-arc.png" alt-text="Screenshot che mostra la pagina dei server Azure Arc per aggiungere una macchina virtuale della soluzione VMware di Azure in Azure.":::

3. Selezionare **Genera script**.
 
    :::image type="content" source="media/azure-security-integration/add-server-using-script.png" alt-text="Screenshot della pagina di Azure Arc che mostra l'opzione per l'aggiunta di un server tramite script interattivo."::: 
 
4. Nella scheda **prerequisiti** selezionare **Avanti**.

5. Nella scheda **Dettagli risorsa** compilare i dettagli seguenti: 
    - Subscription
    - Resource group
    - Region 
    - Sistema operativo
    - Dettagli server proxy
    
    Quindi selezionare **Avanti: Tag**.

6. Nella scheda **tag** selezionare **Avanti**.

7. Nella scheda **Scarica ed Esegui script** selezionare **Scarica**.

8. Specificare il sistema operativo ed eseguire lo script nella macchina virtuale della soluzione VMware di Azure.

## <a name="view-recommendations-and-passed-assessments"></a>Visualizza le raccomandazioni e le valutazioni passate

1. Nel centro sicurezza di Azure selezionare **inventario** dal riquadro sinistro.

2. Per tipo di risorsa selezionare **Server-Azure Arc**.
 
     :::image type="content" source="media/azure-security-integration/select-resource-in-security-center.png" alt-text="Screenshot della pagina di inventario del Centro sicurezza di Azure che mostra i server: Azure Arc selezionato in tipo di risorsa.":::

3. Selezionare il nome della risorsa. Viene visualizzata una pagina che mostra i dettagli di integrità della sicurezza della risorsa.

4. In **elenco** raccomandazioni selezionare le schede **raccomandazioni**, **valutazioni passate** e valutazioni non **disponibili** per visualizzare questi dettagli.

    :::image type="content" source="media/azure-security-integration/view-recommendations-assessments.png" alt-text="Screenshot del Centro sicurezza di Azure che mostra le raccomandazioni e le valutazioni sulla sicurezza.":::

## <a name="deploy-an-azure-sentinel-workspace"></a>Distribuire un'area di lavoro di Azure Sentinel

Azure Sentinel si basa su un'area di lavoro Log Analytics. Il primo passaggio per il caricamento di Azure Sentinel consiste nel selezionare l'area di lavoro Log Analytics che si vuole usare a tale scopo.

1. Nella portale di Azure cercare **Azure Sentinel** e selezionarlo.

2. Nella pagina aree di lavoro di Azure Sentinel selezionare **+ Aggiungi**.

3. Selezionare l'area di lavoro Log Analytics e selezionare **Aggiungi**.

## <a name="enable-data-collector-for-security-events-on-azure-vmware-solution-vms"></a>Abilitare l'agente di raccolta dati per gli eventi di sicurezza nelle VM della soluzione VMware di Azure

A questo punto si è pronti per connettere Sentinel di Azure con le origini dati, in questo caso, gli eventi di sicurezza.

1. Nella pagina aree di lavoro di Azure Sentinel selezionare l'area di lavoro configurata.

2. In configurazione selezionare **connettori dati**.

3. Nella colonna Nome connettore selezionare **gli eventi di sicurezza** dall'elenco e quindi fare clic su **Apri pagina connettore**.

4. Nella pagina Connector (connettore) selezionare gli eventi che si desidera trasmettere in streaming e quindi selezionare **Apply Changes (Applica modifiche**).

    :::image type="content" source="media/azure-security-integration/select-events-you-want-to-stream.png" alt-text="Screenshot della pagina degli eventi di sicurezza in Sentinel di Azure in cui è possibile selezionare gli eventi da trasmettere.":::

## <a name="connect-azure-sentinel-with-azure-security-center"></a>Connettere Sentinel di Azure al centro sicurezza di Azure  

1. Nella pagina area di lavoro di Azure Sentinel selezionare l'area di lavoro configurata.

2. In configurazione selezionare **connettori dati**.

3. Selezionare **Centro sicurezza di Azure** dall'elenco e quindi fare clic su **Apri pagina connettore**.

    :::image type="content" source="media/azure-security-integration/connect-security-center-with-azure-sentinel.png" alt-text="Screenshot della pagina dei connettori dati in Sentinel di Azure che mostra la selezione per connettere il Centro sicurezza di Azure con Sentinel di Azure.":::

4. Selezionare **Connetti** per connettere il Centro sicurezza di Azure con Sentinel di Azure.

5. Abilitare **Crea evento imprevisto** per generare un evento imprevisto per il Centro sicurezza di Azure.

## <a name="create-rules-to-identify-security-threats"></a>Creare regole per identificare le minacce per la sicurezza

Dopo aver connesso le origini dati ad Azure Sentinel, è possibile creare regole per generare avvisi per le minacce rilevate. Nell'esempio seguente viene creata una regola per i tentativi di accesso a Windows Server con la password errata.

1. Nella pagina Panoramica di Azure Sentinel, in configurazioni, selezionare **Analytics**.

2. In configurazioni selezionare **Analytics**.

3. Selezionare **+ Crea** e nell'elenco a discesa selezionare regola di **query pianificata**.

4. Nella scheda **generale** immettere le informazioni necessarie.

    - Nome
    - Descrizione
    - Tattiche
    - Gravità
    - Stato

    Selezionare **Avanti: impostare la logica della regola >**.

5. Nella scheda **impostare la logica della regola** immettere le informazioni necessarie.

    - Query di regola (di seguito viene illustrata la query di esempio)
    
        ```
        SecurityEvent
        |where Activity startswith '4625'
        |summarize count () by IpAddress,Computer
        |where count_ > 3
        ```
        
    - Esegui mapping entità
    - Pianificazione di query
    - Soglia di avviso
    - Raggruppamento di eventi
    - Eliminazione degli avvisi

    Selezionare **Avanti**.

6. Nella scheda **Impostazioni evento imprevisto** abilitare **crea eventi imprevisti da avvisi attivati da questa regola di analisi** e selezionare **Avanti: risposta automatica >**.
 
    :::image type="content" source="media/azure-security-integration/create-new-analytic-rule-wizard.png" alt-text="Screenshot della creazione guidata regola di analisi per la creazione di una nuova regola in Sentinel di Azure. Mostra gli eventi imprevisti creati dagli avvisi attivati da questa regola come abilitati.":::

7. Selezionare **Avanti: esaminare >**.

8. Nella scheda **Verifica e crea** esaminare le informazioni e selezionare **Crea**.

Dopo il terzo tentativo di accesso a Windows Server, la regola creata attiva un evento imprevisto per ogni tentativo non riuscito.

## <a name="view-alerts"></a>Visualizzare gli avvisi

È possibile visualizzare gli eventi imprevisti generati con Sentinel di Azure. È anche possibile assegnare gli eventi imprevisti e chiuderli una volta risolti, tutti dall'interno di Sentinel di Azure.

1. Passare alla pagina Panoramica di Sentinel di Azure.

2. In gestione minacce selezionare **eventi imprevisti**.

3. Selezionare un evento imprevisto. È quindi possibile assegnare l'evento imprevisto a un team per la risoluzione.

    :::image type="content" source="media/azure-security-integration/assign-incident.png" alt-text="Screenshot della pagina eventi imprevisti di Azure Sentinel con evento imprevisto selezionato e opzione per assegnare l'evento imprevisto per la risoluzione.":::

    Dopo aver risolto il problema, è possibile chiuderlo.

## <a name="hunt-security-threats-with-queries"></a>Cercare le minacce alla sicurezza con le query

È possibile creare query o usare la query predefinita disponibile in Sentinel di Azure per identificare le minacce nell'ambiente. Nei passaggi seguenti viene eseguita una query predefinita.

1. Passare alla pagina Panoramica di Sentinel di Azure.

2. In gestione minacce selezionare **Hunting**. Viene visualizzato un elenco di query predefinite.

3. Selezionare una query e quindi fare clic su **Esegui query**.

4. Selezionare **Visualizza risultati** per verificare i risultati.

### <a name="create-a-new-query"></a>Creare una nuova query

1.  In gestione minacce selezionare **Hunting** , quindi **+ nuova query**.

    :::image type="content" source="media/azure-security-integration/create-new-query.png" alt-text="Screenshot della pagina di ricerca di Azure Sentinel con + nuova query evidenziata.":::

2. Immettere le informazioni seguenti per creare una query personalizzata.

    - Nome
    - Descrizione
    - Query personalizzata
    - Immettere il mapping
    - Tattiche
    
3. Selezionare **Crea**. È quindi possibile selezionare la query creata, **eseguire query** e **visualizzare i risultati**.

## <a name="next-steps"></a>Passaggi successivi

Ora che è stata illustrata la protezione delle macchine virtuali della soluzione VMware di Azure, è possibile ottenere informazioni su:

- Uso del [dashboard di Azure Defender](../security-center/azure-defender-dashboard.md).
- [Rilevamento avanzato degli attacchi multifase in Sentinel di Azure](../azure-monitor/logs/quick-create-workspace.md).
- [Gestione del ciclo di vita delle macchine virtuali della soluzione VMware di Azure](lifecycle-management-of-azure-vmware-solution-vms.md).
