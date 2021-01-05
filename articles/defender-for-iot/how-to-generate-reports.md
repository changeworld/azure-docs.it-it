---
title: Generare report
description: Ottenere informazioni sull'attività di rete, rischi, attacchi e tendenze usando Defender per gli strumenti per la creazione di report.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/17/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: d0c3f531ede0a590a6ba7bb21c6edbd768c08069
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/31/2020
ms.locfileid: "97840344"
---
# <a name="generate-reports"></a>Generare report

Ottenere informazioni sull'attività di rete, rischi, attacchi e tendenze usando Azure Defender per gli strumenti per la creazione di report. Sono disponibili strumenti per la generazione di report:

- In base all'attività rilevata dai singoli sensori.
- In base all'attività rilevata da tutti i sensori. 

Questi report vengono generati dalla console di gestione locale.

## <a name="reports-for-sensor-risk-assessment"></a>Report per la valutazione del rischio dei sensori

La creazione di report di valutazione dei rischi consente di generare un punteggio di sicurezza per ogni dispositivo di rete, nonché un punteggio di sicurezza di rete generale. Il Punteggio complessivo rappresenta la percentuale di sicurezza del 100%.

Questo punteggio è basato sui risultati ottenuti dall'ispezione dei pacchetti, dai motori di modellazione comportamentale e da un progetto di macchina a stati specifico di SCADA. È disponibile una vasta gamma di altre informazioni, ad esempio:

- Problemi di configurazione

- Vulnerabilità del dispositivo classificata in base al livello di sicurezza

- Problemi di sicurezza di rete

- Problemi operativi della rete

- Attack vectors (Vettori di attacco)

- Connessioni alle reti ICS

- connessioni Internet

- Indicatori di malware industriali

- Problemi relativi al protocollo

  - Dispositivi protetti: dispositivi con un punteggio di sicurezza superiore al 90%.

- **Dispositivi protetti**: dispositivi con un punteggio di sicurezza superiore al 90%.

- **Dispositivi vulnerabili**: dispositivi con un punteggio di sicurezza inferiore al 70%.

- **Dispositivi che richiedono un miglioramento**: i dispositivi con un punteggio di sicurezza compreso tra il 70 e il 89%.

Per creare un report:

1. Selezionare **valutazione dei rischi** nel menu laterale.
2. Selezionare un sensore nell'elenco a discesa **Seleziona sensore** .
3. Selezionare **Genera rapporto**.
4. Selezionare **download** dalla sezione report archiviati.

:::image type="content" source="media/how-to-generate-reports/risk-assessment.png" alt-text="Visualizzazione della valutazione dei rischi.":::

Per importare un logo aziendale:

Per importare un logo aziendale:

- Selezionare **Importa logo**.

## <a name="reports-for-sensor-attack-vector"></a>Report per il vettore di attacco dei sensori

I report vettoriali di attacco forniscono una rappresentazione grafica di una catena di vulnerabilità di dispositivi sfruttabili. Queste vulnerabilità possono consentire a un utente malintenzionato di accedere ai dispositivi di rete principali. Il simulatore di vettori di attacco calcola i vettori di attacco in tempo reale e analizza tutti i vettori di attacco per una destinazione specifica.

L'uso del vettore di attacco consente di valutare l'effetto delle attività di mitigazione nella sequenza di attacco. È quindi possibile determinare, ad esempio, se un aggiornamento del sistema interrompe il percorso del pirata informatico suddividendo la catena di attacchi o se un percorso di attacco alternativo rimane. Queste informazioni consentono di assegnare priorità alle attività di correzione e mitigazione.

:::image type="content" source="media/how-to-generate-reports/control-center.png" alt-text="Visualizzare gli avvisi nel centro di controllo.":::

> [!NOTE]
> Gli amministratori e gli analisti della sicurezza possono eseguire le procedure descritte in questa sezione.

Per creare una simulazione vettoriale di attacco:

1. Selezionare :::image type="content" source="media/how-to-generate-reports/plus.png" alt-text="segno più":::nel menu laterale per aggiungere una simulazione.

 :::image type="content" source="media/how-to-generate-reports/vector.png" alt-text="Simulazione del vettore di attacco.":::

2. Immettere le proprietà di simulazione:

   - **Nome**: nome della simulazione.

   - Numero **massimo** di vettori: il numero massimo di vettori in una singola simulazione.

   - **Mostra in mappa dei dispositivi**: Mostra il vettore di attacco come filtro sulla mappa del dispositivo.

   - **Tutti i dispositivi di origine**: il vettore di attacco prenderà in considerazione tutti i dispositivi come origine di attacco.

   - **Origine attacco**: il vettore di attacco prende in considerazione solo i dispositivi specificati come origine di attacco.

   - **Tutti i dispositivi di destinazione**: il vettore di attacco prenderà in considerazione tutti i dispositivi come destinazione di attacco.

   - **Destinazione attacco**: il vettore di attacco considererà solo i dispositivi specificati come destinazione di attacco.

   - **Escludi i dispositivi**: i dispositivi specificati verranno esclusi dalla simulazione del vettore di attacco.

   - **Escludi subnet**: le subnet specificate verranno escluse dalla simulazione del vettore di attacco.

3. Selezionare **Aggiungi simulazione**. La simulazione verrà aggiunta all'elenco simulazioni.

   :::image type="content" source="media/how-to-generate-reports/new-simulation.png" alt-text="Aggiungere una nuova simulazione.":::

4. Selezionare :::image type="icon" source="media/how-to-generate-reports/edit-a-simulation-icon.png" border="false"::: se si vuole modificare la simulazione.

   Selezionare :::image type="icon" source="media/how-to-generate-reports/delete-simulation-icon.png" border="false"::: se si desidera eliminare la simulazione.

   Selezionare :::image type="icon" source="media/how-to-generate-reports/make-a-favorite-icon.png" border="false"::: se si vuole contrassegnare la simulazione come preferita.

5. Viene visualizzato un elenco di vettori di attacco che include il Punteggio vettoriale (su 100), il dispositivo di origine di attacco e il dispositivo di destinazione attacco. Selezionare un attacco specifico per la rappresentazione grafica dei vettori di attacco.

   :::image type="content" source="media/how-to-generate-reports/sample-attack-vectors.png" alt-text="Vettori di attacco.":::

## <a name="sensor-data-mining-queries"></a>Dati del sensore-query di data mining

Gli strumenti di data mining consentono di generare informazioni complete e granulari sui dispositivi di rete a vari livelli. Ad esempio, è possibile creare una query basata su:

- Periodi di tempo

- Connessioni a Internet

- Porte

- Protocolli

- Versioni del firmware

- Comandi di programmazione

- Inattività del dispositivo

Il report può essere ottimizzato in base ai filtri. Ad esempio, è possibile eseguire una query su una subnet specifica in cui il firmware è stato aggiornato.

:::image type="content" source="media/how-to-generate-reports/active-device-list-v2.png" alt-text="Elenco di dispositivi attivi.":::

Per gestire le query sono disponibili diversi strumenti. Ad esempio, è possibile esportare e salvare.

> [!NOTE]
> Gli amministratori e gli analisti della sicurezza possono accedere alle opzioni di data mining.

### <a name="dynamic-updates"></a>Aggiornamenti dinamici

Le query di data mining create vengono aggiornate dinamicamente ogni volta che vengono aperte. Ad esempio:

- Se si crea un report per le versioni del firmware nei dispositivi del 1 ° giugno e si apre nuovamente il report il 10 giugno, il report verrà aggiornato con informazioni accurate per il 10 giugno.

- Se si crea un report per rilevare i nuovi dispositivi individuati negli ultimi 30 giorni del 1 ° giugno e si apre il report il 30 giugno, i risultati verranno visualizzati per gli ultimi 30 giorni.

### <a name="data-mining-use-cases"></a>Casi d'uso di data mining

È possibile usare le query per gestire un'ampia gamma di esigenze di sicurezza per i diversi team di sicurezza:

- **Risposta agli eventi imprevisti SOC**: consente di generare un report in tempo reale per gestire la risposta immediata agli eventi imprevisti. Ad esempio, generare un report per un elenco di dispositivi che potrebbero richiedere l'applicazione di patch.

- **Forensics**: genera un report basato sui dati cronologici per i report investigativi.

- **Integrità della rete it**: genera un report che consente di migliorare la sicurezza complessiva della rete. Ad esempio, generare un report che elenca i dispositivi con credenziali di autenticazione vulnerabili.

- **Visibilità**: genera un report che copre tutti gli elementi della query per visualizzare tutti i parametri di base della rete.

### <a name="data-mining-storage"></a>Archiviazione di data mining

Le informazioni di data mining vengono salvate e archiviate in modo continuo, tranne quando viene eliminato un dispositivo. I risultati di data mining possono essere esportati e archiviati esternamente in un server protetto. Inoltre, il sensore esegue backup giornalieri automatici per garantire la continuità del sistema e la conservazione dei dati.

### <a name="data-mining-and-reports"></a>Data mining e report 

I report regolari, accessibili dall'opzione **report** , sono report di data mining predefiniti. Non sono query dinamiche come sono disponibili in data mining, ma una rappresentazione statica dei risultati della query di data mining.

I risultati delle query di data mining non sono disponibili per gli utenti RO. Gli amministratori e gli analisti della sicurezza che desiderano consentire agli utenti RO di accedere alle informazioni generate da data mining query devono salvare le informazioni come report.

### <a name="predefined-queries"></a>Query predefinite

Sono disponibili le query predefinite seguenti. Queste query vengono generate in tempo reale.

- **CVES**: elenco di dispositivi rilevati con vulnerabilità note nelle ultime 24 ore.

- **Escluso CVES**: elenco di tutti i CVES che sono stati esclusi manualmente. Per ottenere risultati più accurati nei report VA e nei vettori di attacco, è possibile personalizzare l'elenco CVE manualmente includendo ed escludendo CVEs.

- **Attività Internet**: dispositivi connessi a Internet.

- **Dispositivi non attivi**: dispositivi che non hanno comunicato negli ultimi sette giorni.

- **Dispositivi attivi**: dispositivi di rete attivi nelle ultime 24 ore.

- **Accesso remoto**: dispositivi che comunicano tramite protocolli di sessione remota.

- **Comandi di programmazione**: dispositivi che inviano la programmazione industriale.

Questi report sono accessibili automaticamente dalla schermata **report** , in cui gli utenti ro e gli altri utenti possono visualizzarli. Gli utenti RO non possono accedere ai report di data mining.

:::image type="content" source="media/how-to-generate-reports/data-mining-screeshot-v2.png" alt-text="Schermata data mining.":::

### <a name="create-a-data-mining-report"></a>Creazione di un report di data mining

Per creare un report di data mining:

1. Scegliere **data mining** dal menu laterale. I report suggeriti predefiniti vengono visualizzati automaticamente.

 :::image type="content" source="media/how-to-generate-reports/data-mining-screeshot-v2.png" alt-text="Selezionare data mining dal riquadro laterale.":::

2. Selezionare :::image type="icon" source="media/how-to-generate-reports/plus-icon.png" border="false":::.

3. Selezionare **nuovo report** e definire il report.

   :::image type="content" source="media/how-to-generate-reports/create-new-report-screen.png" alt-text="Per creare un nuovo report, compilare questa schermata.":::

   Sono disponibili i parametri seguenti:

   - Specificare un nome e una descrizione per il report.

   - Per le categorie, selezionare una delle seguenti operazioni:

      - **Categorie (tutti)** per visualizzare tutti i risultati dei report relativi a tutti i dispositivi della rete.

      - **Generico** per scegliere le categorie standard.

   - Selezionare specifici parametri di report di interesse.

   - Scegliere un ordinamento (**Order by**). Ordina i risultati in base all'attività o alla categoria.

   - Selezionare **Salva nelle pagine del report** per salvare i risultati del report come report accessibile dalla pagina del **report** . Questa operazione consentirà agli utenti RO di eseguire il report creato.

   - Selezionare **filtri (Aggiungi)** per aggiungere altri filtri. Sono supportate le richieste con caratteri jolly.

   - Specificare un gruppo di dispositivi (definito nella mappa dei dispositivi).

   - Specificare un indirizzo IP.

   - Specificare una porta.

   - Specificare un indirizzo MAC.

4. Selezionare **Salva**. I risultati del report vengono aperti nella pagina di **data mining** .

:::image type="content" source="media/how-to-generate-reports/data-mining-page.png" alt-text="Segnala i risultati visualizzati nella pagina di data mining.":::

### <a name="manage-data-mining-reports"></a>Gestire i report di data mining

Nella tabella seguente vengono descritte le opzioni di gestione per data mining:

| Immagine icona | Descrizione |
|--|--|
| :::image type="icon" source="media/how-to-generate-reports/edit-a-simulation-icon.png" border="false"::: | Modificare i parametri del report. |
| :::image type="icon" source="media/how-to-generate-reports/export-as-pdf-icon.png" border="false"::: | Esporta in formato PDF. |
| :::image type="icon" source="media/how-to-generate-reports/csv-export-icon.png" border="false"::: |Esporta come CSV. |
| :::image type="icon" source="media/how-to-generate-reports/information-icon.png" border="false"::: | Mostra informazioni aggiuntive, ad esempio la data dell'Ultima modifica. Utilizzare questa funzionalità per creare uno snapshot del risultato della query. Potrebbe essere necessario eseguire questa operazione per un'ulteriore analisi con i responsabili del team o gli analisti SOC, ad esempio. |
| :::image type="icon" source="media/how-to-generate-reports/pin-icon.png" border="false"::: | Visualizzato nella pagina **report** o Nascondi nella pagina **report** . :::image type="content" source="media/how-to-generate-reports/hide-reports-page.png" alt-text="Nascondere o rivelare i report."::: |
| :::image type="icon" source="media/how-to-generate-reports/delete-simulation-icon.png" border="false"::: | Eliminare il report. |

#### <a name="create-customized-directories"></a>Creare directory personalizzate 

È possibile organizzare le informazioni estese per le query di data mining creando directory per le categorie. Ad esempio, è possibile creare directory per protocolli o percorsi.

Per creare una nuova directory:

1. Selezionare questa :::image type="icon" source="media/how-to-generate-reports/plus-icon.png" border="false"::: per aggiungere una nuova directory.

2. Selezionare **nuova directory** per visualizzare il nuovo modulo di directory.

3. Assegnare un nome alla nuova directory.

4. Trascinare i report necessari nella nuova directory. In qualsiasi momento, è possibile trascinare nuovamente il report nella visualizzazione principale.

5. Fare clic con il pulsante destro del mouse sulla nuova directory per aprirla, modificarla o eliminarla.

#### <a name="create-snapshots-of-report-results"></a>Creazione di snapshot dei risultati del report

Potrebbe essere necessario salvare determinati risultati della query per un'ulteriore analisi. Ad esempio, potrebbe essere necessario condividere i risultati con diversi team di sicurezza.

Gli snapshot vengono salvati nei risultati del report e non generano query dinamiche.

:::image type="content" source="media/how-to-generate-reports/report-results-report.png" alt-text="Istantanee.":::

Per creare uno snapshot:

1. Aprire il report richiesto.

2. Selezionare l'icona informazioni :::image type="icon" source="media/how-to-generate-reports/information-icon.png" border="false"::: .

3. Selezionare **Take New (nuovo**).

4. Immettere un nome per lo snapshot e selezionare **Salva**.

:::image type="content" source="media/how-to-generate-reports/take-a-snapshot.png" alt-text="Creare uno snapshot.":::

#### <a name="customize-the-cve-list"></a>Personalizzare l'elenco CVE

È possibile personalizzare manualmente l'elenco CVE come indicato di seguito:

  - Includi/Escludi CVEs

  - Modificare il Punteggio CVE

Per eseguire modifiche manuali nel rapporto CVE:

1.  Scegliere **data mining** dal menu laterale.

2. Selezionare :::image type="icon" source="media/how-to-generate-reports/plus-icon.png" border="false"::: nell'angolo superiore sinistro della finestra di **data mining** . Quindi selezionare **nuovo rapporto**.

   :::image type="content" source="media/how-to-generate-reports/create-a-new-report-screen.png" alt-text="Creazione di un nuovo report.":::

3. Nel riquadro sinistro selezionare una delle opzioni seguenti:

   - **Vulnerabilità note**: seleziona entrambe le opzioni e Visualizza i risultati nelle due tabelle del report, una con CVES e l'altra con CVES escluso.

   - **CVES**: selezionare questa opzione per presentare un elenco di tutti i CVES.

   - **Escluso CVES**: selezionare questa opzione per presentare un elenco di tutti i CVES esclusi.

4. Immettere le informazioni relative al **nome** e alla **Descrizione** e selezionare **Salva**. Il nuovo report verrà visualizzato nella finestra di **data mining** .

5. Per escludere CVEs, aprire il report di data mining per CVEs. Viene visualizzato l'elenco di tutti i CVEs.

   :::image type="content" source="media/how-to-generate-reports/cves.png" alt-text="Report C V E.":::

6. Per abilitare la selezione di elementi nell'elenco, selezionare :::image type="icon" source="media/how-to-generate-reports/enable-selecting-icon.png" border="false"::: e selezionare il CVES che si desidera personalizzare. La barra **delle operazioni** viene visualizzata in basso.

   :::image type="content" source="media/how-to-generate-reports/operations-bar-appears.png" alt-text="Screenshot della barra delle operazioni di data mining.":::

7. Selezionare il CVEs che si vuole escludere e quindi selezionare **Elimina record**. Il CVEs selezionato non viene visualizzato nell'elenco di CVEs e verrà visualizzato nell'elenco dei CVEs esclusi quando ne viene generato uno.

8. Per includere i CVEs esclusi nell'elenco di CVEs, generare il report per CVEs escluso ed eliminare dall'elenco gli elementi che si desidera includere nuovamente nell'elenco di CVEs.

9. Selezionare il CVEs in cui si vuole modificare il punteggio, quindi selezionare **Aggiorna punteggio CVE**.

   :::image type="content" source="media/how-to-generate-reports/set-new-score-screen.png" alt-text="Aggiornare il Punteggio CVE.":::

10. Immettere il nuovo punteggio e selezionare **OK**. Il Punteggio aggiornato viene visualizzato nella CVEs selezionata.

### <a name="reports-based-on-data-mining"></a>Report basati su data mining

I report riflettono le informazioni generate da data mining risultati della query. Sono inclusi i report data mining predefiniti, disponibili nella visualizzazione report. Gli analisti di amministrazione e sicurezza possono inoltre generare query data mining personalizzate e salvarle come report. Questi report sono disponibili anche per gli utenti RO.

Per generare un report

1. Selezionare **report** dal menu laterale.

2. Scegliere il report necessario da visualizzare. La scelta può essere costituita da report **personalizzati** o **generati automaticamente** , ad esempio **comandi di programmazione** e **accesso remoto**.

3. È possibile esportare il report selezionando una delle icone in alto a destra nella schermata:

   :::image type="icon" source="media/how-to-generate-reports/export-to-pdf-icon.png" border="false"::: Esportare in un file PDF.

   :::image type="icon" source="media/how-to-generate-reports/export-to-csv-icon.png" border="false"::: Esportare in un file CSV.

> [!NOTE] 
> L'utente RO può visualizzare solo i report creati per essi.

:::image type="content" source="media/how-to-generate-reports/select-a-report-screen.png" alt-text="Consente di selezionare il report da generare.":::

:::image type="content" source="media/how-to-generate-reports/remote-access-report.png" alt-text="Il report di accesso remoto è stato generato.":::

## <a name="reports-for-sensor-trends-and-statistics"></a>Report relativi alle tendenze e alle statistiche dei sensori

È possibile creare grafici di widget e grafici a torta per ottenere informazioni sulle tendenze e le statistiche di rete. I widget possono essere raggruppati in dashboard definiti dall'utente.

> [!NOTE]
> Solo gli amministratori e gli analisti della sicurezza possono eseguire le procedure descritte in questa sezione.

Per visualizzare i dashboard e i widget, selezionare **tendenze & statistiche** dal menu laterale.

:::image type="content" source="media/how-to-generate-reports/investigation-screenshot.png" alt-text="Screenshot di un'indagine.":::

Il dashboard è costituito da widget che descrivono graficamente i tipi di informazioni seguenti:

- Traffico per porta
- Larghezza di banda del canale
- Larghezza di banda totale
- Connessione TCP attiva
- Dispositivi:
  - Nuovi dispositivi
  - Dispositivi occupati
  - Dispositivi per fornitore
  - Dispositivi per sistema operativo
  - Dispositivi disconnessi
- Riduzione della connettività per ore
- Avvisi per eventi imprevisti per tipo
- Accesso alla tabella di database
- Widget di dissezione del protocollo
- Ethernet e indirizzo IP:
  - Traffico di indirizzi IP e Ethernet per servizio CIP
  - Traffico di indirizzi IP e Ethernet per classe CIP
  - Ethernet e traffico indirizzi IP per comando
- OPC
  - Operazioni di gestione principali OPC
  - Operazioni di I/O principali OPC
- S7 Siemens:
  - Traffico S7 per funzione di controllo
  - Traffico S7 per sottofunzione
- SRTP
  - Traffico SRTP per codice del servizio
  - Errori di SRTP per giorno
- SuiteLink:
  - SuiteLink primi tag con query
  - Comportamento dei tag numerici SuiteLink
- Traffico IEC-60870 per ASDU
- DNP3 il traffico per funzione
- Traffico MMS per servizio
- Traffico Modbus per funzione
- Traffico OPC-UA per servizio

> [!NOTE]
>  Il tempo nei widget è impostato in base al tempo del sensore.

## <a name="reports-for-the-on-premises-management-console"></a>Report per la console di gestione locale

La console di gestione locale consente di generare report per ogni sensore connesso. I report si basano sulle query di data mining dei sensori eseguite.

È possibile generare i report seguenti:

- **Dispositivi attivi (ultime 24 ore)**: presenta un elenco di dispositivi che mostrano l'attività di rete in un periodo di 24 ore.

- **Dispositivi non attivi (ultimi 7 giorni)**: presenta un elenco di dispositivi che non mostrano attività di rete negli ultimi sette giorni.

- **Programming Commands**: Visualizza un elenco di dispositivi che hanno inviato i comandi di programmazione nelle ultime 24 ore.

- **Accesso remoto**: Visualizza un elenco di dispositivi a cui si accede da origini remote nelle ultime 24 ore.

:::image type="content" source="media/how-to-generate-reports/reports-view.png" alt-text="Screenshot della visualizzazione report.":::

Quando si sceglie il sensore dalla console di gestione locale, tutti i report personalizzati configurati su tale sensore vengono visualizzati nell'elenco dei report. Per ogni sensore, è possibile generare un report predefinito o un report personalizzato configurato sul sensore.

Per generare un report

1. Nel riquadro sinistro selezionare **report**. Verrà visualizzata la finestra **report** .

2. Dall'elenco a discesa **sensori** selezionare il sensore per il quale si desidera generare il report.

   :::image type="content" source="media/how-to-generate-reports/sensor-drop-down-list.png" alt-text="Screenshot della visualizzazione sensori.":::

3. Nell'elenco a discesa a destra selezionare il report che si desidera generare.

4. Per creare un file PDF dei risultati del report, selezionare :::image type="icon" source="media/how-to-generate-reports/pdf-report-icon.png" border="false"::: .

## <a name="risk-assessment-reports-for-the-on-premises-management-console"></a>Report di valutazione dei rischi per la console di gestione locale

Generare un report di valutazione dei rischi per ogni sensore connesso alla console di gestione locale. Questo report fornisce informazioni dettagliate su ognuna delle reti monitorate dai sensori.

I report di valutazione dei rischi consentono di generare un punteggio di sicurezza per ogni dispositivo di rete, nonché un punteggio di sicurezza di rete generale. Il Punteggio complessivo è basato sull'ispezione completa dei pacchetti, sui motori di modellazione comportamentale e sulla progettazione di una macchina a stati specifica del SCADA. È disponibile una vasta gamma di altre informazioni. Ad esempio:

- Problemi di configurazione

- Vulnerabilità del dispositivo classificata in base al livello di sicurezza

- Problemi di sicurezza di rete

- Problemi operativi della rete

- Attack vectors (Vettori di attacco)

- Connessioni alle reti ICS

- connessioni Internet

- Indicatori di malware industriali

- Problemi relativi al protocollo

Il report fornisce consigli per la mitigazione che consentono di migliorare il Punteggio di sicurezza.

- Dispositivi protetti: dispositivi con un punteggio di sicurezza superiore al 90%.

- **Dispositivi vulnerabili**: dispositivi con un punteggio di sicurezza inferiore al 70%.

- **Dispositivi che richiedono un miglioramento**: i dispositivi con un punteggio di sicurezza compreso tra il 70 e il 89%.

Per creare un report:

1. Selezionare **valutazione dei rischi** nel menu laterale.

2. Selezionare un sensore nell'elenco a discesa **Seleziona sensore** .

3. Selezionare **Genera rapporto**.

4. Selezionare **download** dalla sezione **report archiviati** .

Per importare un logo aziendale:

- Selezionare **Importa logo**.

:::image type="content" source="media/how-to-generate-reports/import-logo-screenshot.png" alt-text="Importare il logo tramite la vista valutazione dei rischi.":::

## <a name="see-also"></a>Vedere anche
[Utilizzare le viste della mappa del sito](how-to-gain-insight-into-global-regional-and-local-threats.md#work-with-site-map-views)
