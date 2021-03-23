---
title: Gestire gli avvisi della console di gestione locale
description: Usare la console di gestione locale per ottenere una visualizzazione aziendale delle minacce recenti nella rete e comprendere meglio il modo in cui gli utenti dei sensori li gestiscono.
ms.date: 12/06/2020
ms.topic: how-to
ms.openlocfilehash: a99c489c54b2671a463c0c3dad1909c74b4fa203
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781467"
---
# <a name="work-with-alerts-on-the-on-premises-management-console"></a>Gestire gli avvisi della console di gestione locale 

Nella finestra **avvisi** della console di gestione è possibile eseguire le operazioni seguenti:

- Usare i filtri di avviso

- Usare i contatori degli avvisi

- Visualizzare le informazioni sugli avvisi

- Gestione degli eventi di avviso

- Creare regole di esclusione degli avvisi

- Attivare regole di esclusione avvisi da sistemi esterni

- Accelera il flusso di lavoro eventi imprevisti con gruppi di avvisi

## <a name="view-alerts-in-the-on-premises-management-console"></a>Visualizzare gli avvisi nella console di gestione locale

La console di gestione locale aggrega gli avvisi di tutti i sensori connessi. In questo modo viene fornita una visualizzazione aziendale delle minacce recenti nella rete e viene fornita una migliore comprensione del modo in cui gli utenti dei sensori li gestiscono.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alerts-with-samples.png" alt-text="Screenshot della finestra degli avvisi.":::

### <a name="work-with-alert-filters"></a>Usare i filtri di avviso

La finestra **avvisi** Visualizza gli avvisi generati dai sensori connessi alla console di gestione locale. È possibile visualizzare tutti gli avvisi per i sensori connessi o presentare gli avvisi inviati da uno specifico:

- Sito

- Zona

- Dispositivo

- Sensore

Selezionare **Cancella filtri** per visualizzare tutti gli avvisi.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/clear-filters.png" alt-text="Deselezionare i filtri selezionando il pulsante Cancella filtri.":::

### <a name="work-with-alert-counters"></a>Usare i contatori degli avvisi

I contatori degli avvisi forniscono una suddivisione degli avvisi in base alla gravità e allo stato riconosciuto.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/number-of-alerts.png" alt-text="Il contatore avvisi Mostra il numero di avvisi disponibili.":::

Nel contatore degli avvisi vengono visualizzati i livelli di gravità seguenti:

- **Critico**: indica un attacco dannoso che deve essere gestito immediatamente.

- **Major**: indica una minaccia per la sicurezza che è importante affrontare.

- **Minor**: indica una deviazione rispetto al comportamento della linea di base che potrebbe contenere una minaccia per la sicurezza.

- **Avviso**: indica una deviazione dal comportamento della linea di base senza minacce per la sicurezza.

I livelli di gravità sono predefiniti.

È possibile modificare il contatore per fornire numeri in base a avvisi riconosciuti e non riconosciuti. Gli avvisi non riconosciuti sono stati attivati in Defender per i sensori Internet, ma non sono stati ancora esaminati dagli operatori sul sensore.

Quando l'opzione **Mostra avvisi riconosciuti** è selezionata, tutti gli avvisi riconosciuti vengono visualizzati nella finestra **avvisi** .

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/show-acknowledged-alerts.png" alt-text="Visualizzare gli avvisi riconosciuti.":::

### <a name="view-alert-information"></a>Visualizzare le informazioni sugli avvisi

L'avviso presenta le informazioni seguenti:

- Riepilogo dell'evento di avviso.

- Gravità dell'avviso.

- Un collegamento all'avviso nel sensore che lo ha rilevato.

- UUID di avviso. L'UUID è costituito dall'ID avviso associato all'evento di avviso rilevato sul sensore, separato da un trattino e seguito da un numero ID di sistema univoco.

**UUID avviso console di gestione locale**

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/unauthorized-internet-connectivity.png" alt-text="Un dispositivo è connesso ma non autorizzato.":::

**ID avviso sensore**

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/internet-connectivity-alert.png" alt-text="ID avviso del sensore.":::

L'uso degli UUID garantisce che ogni avviso visualizzato nella console di gestione locale sia ricercabile e identificabile da un numero univoco. Questa operazione è necessaria perché gli avvisi generati da più sensori potrebbero produrre lo stesso ID avviso.

> [!NOTE]
> Per impostazione predefinita, gli UUID vengono visualizzati nei seguenti sistemi partner quando vengono definite regole di invio: ArcSight, server syslog, QRadar, Sentinel e NetWitness. Non è necessaria alcuna installazione.

Per visualizzare le informazioni sugli avvisi:

- Selezionare un avviso dall'elenco avvisi.

  :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alert-information.png" alt-text="Screenshot delle informazioni sugli avvisi.":::

Per visualizzare l'avviso nel sensore:

- Selezionare **Apri sensore** dall'avviso.

Per visualizzare i dispositivi in una mappa di zona:

- Per visualizzare la mappa del dispositivo con lo stato attivo sul dispositivo segnalato e su tutti i dispositivi connessi, selezionare **Mostra dispositivi**.

## <a name="manage-alert-events"></a>Gestione degli eventi di avviso

Sono disponibili diverse opzioni per la gestione degli eventi di avviso dalla console di gestione locale.

- Informazioni o riconoscimento degli eventi di avviso. Selezionare **learn & confermare** per apprendere tutti gli eventi di avviso che possono essere autorizzati e per confermare tutti gli eventi di avviso attualmente non riconosciuti.

  :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/learn-and-acknowledge.png" alt-text="Selezionare Learn & confermare per apprendere tutti.":::

- Disattiva e disattiva gli eventi di avviso.

Per altre informazioni sull'apprendimento, il riconoscimento e la disattivazione degli eventi di avviso, vedere l'articolo [gestire gli eventi di avviso](how-to-manage-the-alert-event.md) per il sensore.

## <a name="export-alert-information"></a>Esporta informazioni sugli avvisi

Esportare le informazioni sugli avvisi in un file con estensione CSV. È possibile esportare le informazioni di tutti gli avvisi rilevati o esportare le informazioni in base alla visualizzazione filtrata. Vengono esportate le seguenti informazioni:

- Indirizzo di origine
- Indirizzo di destinazione
- Titolo avviso
- Gravità dell'avviso
- Messaggio di avviso
- Informazioni aggiuntive
- Stato riconosciuto
- Disponibilità PCAP

Per esportare:

1. Selezionare avvisi dal menu laterale.
1. Selezionare Esporta.
1. Selezionare Esporta avvisi estesi per esportare le informazioni sugli avvisi in righe separate per ogni avviso che copre più dispositivi. Quando si seleziona Esporta avvisi estesi, il file con estensione CSV creerà una riga duplicata dell'avviso con gli elementi univoci in ogni riga. L'utilizzo di questa opzione consente di esaminare più facilmente gli eventi di avviso esportati.  

## <a name="create-alert-exclusion-rules"></a>Creare regole di esclusione degli avvisi

Indica a Defender di ignorare i trigger di avviso in base a:

- Fusi orari e periodi di tempo

- Indirizzo del dispositivo (IP, MAC, subnet)

- Nomi degli avvisi

- Un sensore specifico

Creare regole di esclusione degli avvisi quando si desidera che Defender ignori l'attività che attiverà un avviso.

Ad esempio, se si è certi che tutti i dispositivi OT monitorati da un sensore specifico verranno sottoposti a procedure di manutenzione per due giorni, è possibile definire una regola di esclusione che indica a Defender for Internet per eliminare gli avvisi rilevati da questo sensore durante il periodo predefinito.

### <a name="alert-exclusion-logic"></a>Logica di esclusione degli avvisi

La logica della regola di avviso è `AND` basata su. Ciò significa che viene attivato un avviso solo quando vengono soddisfatte tutte le condizioni della regola.

Se non è definita una condizione della regola, la condizione includerà tutte le opzioni. Se, ad esempio, non si include il nome di un sensore nella regola, questo verrà applicato a tutti i sensori.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/create-alert-exclusion-v2.png" alt-text="Screenshot della vista Crea regola di esclusione.":::

I riepiloghi delle regole vengono visualizzati nella finestra della **regola di esclusione** .

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/exclusion-summary-v2.png" alt-text="Screenshot della visualizzazione Riepilogo della regola di esclusione.":::

Oltre a utilizzare le regole di esclusione, è possibile disattivare gli avvisi per cancellarli.

### <a name="create-exclusion-rules"></a>Creare regole di esclusione

Per creare regole di esclusione:

1. Dal riquadro sinistro della console di gestione locale selezionare **esclusione avvisi**. Definire una nuova regola di esclusione selezionando l'icona **Aggiungi** :::image type="icon" source="media/how-to-work-with-alerts-on-premises-management-console/add-icon.png" border="false"::: nell'angolo in alto a destra della finestra visualizzata. Verrà visualizzata la finestra di dialogo **Crea regola di esclusione** .

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/create-alert-exclusion-view.png" alt-text="Per creare un'esclusione di avvisi, compilare qui le informazioni.":::

2. Immettere un nome di regola nel campo **nome** . Il nome non può contenere virgolette ( `"` ).

3. Nella sezione **per fuso orario/periodo** , immettere un periodo di tempo in un fuso orario specifico. Usare questa funzionalità quando una regola di esclusione viene creata per un periodo di tempo specifico in un fuso orario, ma deve essere implementata contemporaneamente in altri fusi orari. Ad esempio, potrebbe essere necessario applicare una regola di esclusione tra 8:00 AM e 10:00 AM in tre fusi orari diversi. In questo caso, creare tre regole di esclusione separate che usano lo stesso periodo di tempo e il fuso orario pertinente.

4. Selezionare **AGGIUNGI**. Durante il periodo di esclusione, nessun avviso viene creato sui sensori connessi.

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/by-the-time-period.png" alt-text="Screenshot della visualizzazione del periodo di tempo.":::

5. Nella sezione **per indirizzo del dispositivo** definire:

  - Indirizzo IP del dispositivo, indirizzo MAC o indirizzo di subnet che si desidera escludere.

  - Direzione del traffico per i dispositivi, l'origine e la destinazione esclusi.

6. Selezionare **AGGIUNGI**.

7. Nella sezione in **base al titolo dell'avviso** , iniziare a digitare il titolo dell'avviso. Nell'elenco a discesa selezionare il titolo o i titoli degli avvisi da escludere.

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alert-title.png" alt-text="Screenshot della visualizzazione del titolo dell'avviso.":::

8. Selezionare **AGGIUNGI**.

9. Nella sezione **per nome sensore** iniziare a digitare il nome del sensore. Dall'elenco a discesa selezionare il sensore o i sensori che si desidera escludere.

10. Selezionare **AGGIUNGI**.

11. Selezionare **SAVE** (SALVA). La nuova regola verrà visualizzata nell'elenco di regole.

È possibile disattivare gli avvisi tramite il silenziamento o la creazione di regole di esclusione degli avvisi. Questa sezione descrive i potenziali casi d'uso per entrambe le funzionalità.

- **Regola di esclusione**. Scrivere una regola di esclusione nei casi seguenti:

  - Si sa in anticipo che si desidera escludere l'evento dal database. Ad esempio, si sa che lo scenario rilevato in un determinato sensore attiverà avvisi irrilevanti. Ad esempio, si eseguirà il lavoro di manutenzione sui PLC aziendali in un sito specifico e si desidera disattivare gli avvisi relativi ai PLC per questo sito.

  - Si vuole che Defender per l'utente ignori gli eventi per un intervallo di tempo specifico (per le attività di manutenzione del sistema).

  - Si desidera ignorare gli eventi in una subnet specifica.

  - Si desidera controllare gli eventi di avviso generati da diversi sensori con una sola regola.

  - Non si vuole tenere traccia dell'esclusione degli avvisi come un evento nel registro eventi.

- **Disattiva**. Disattiva un avviso quando:

  - Gli elementi che devono essere disattivati non sono pianificati. Non è possibile stabilire in anticipo quali eventi saranno irrilevanti.

  - Si vuole che l'avviso venga eliminato dalla finestra **avvisi** , ma si vuole comunque monitorarlo nel registro eventi.

  - Si desidera ignorare gli eventi su un canale specifico.

### <a name="trigger-alert-exclusion-rules-from-external-systems"></a>Attivare regole di esclusione avvisi da sistemi esterni

Attivare regole di esclusione avvisi da sistemi esterni. Ad esempio, gestire le regole di esclusione da sistemi di ticket aziendali o sistemi che gestiscono i processi di manutenzione di rete.

Definire sensori, motori, ora di inizio e ora di fine per applicare la regola. Per altre informazioni, vedere [Defender per le API del sensore API e della console di gestione](references-work-with-defender-for-iot-apis.md).

Le regole create tramite l'API vengono visualizzate nella finestra **regola di esclusione** come ro.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/edit-exclusion-rule-screen.png" alt-text="Screenshot della visualizzazione Modifica regola di esclusione.":::

## <a name="see-also"></a>Vedi anche

[Usare gli avvisi sul sensore](how-to-work-with-alerts-on-your-sensor.md)
