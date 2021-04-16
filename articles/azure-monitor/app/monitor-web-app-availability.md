---
title: Monitorare la disponibilità e la velocità di risposta di qualsiasi sito Web - Monitoraggio di Azure
description: Configurare i test ping in Application Insights. Ottenere avvisi se un sito Web diventa non disponibile o risponde lentamente.
ms.topic: conceptual
ms.date: 04/15/2021
ms.reviewer: sdash
ms.openlocfilehash: ecfd4ffee3582ff37411e59c75d8be8fca5e945f
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516623"
---
# <a name="monitor-the-availability-of-any-website"></a>Monitorare la disponibilità di un sito Web

Il nome "URL ping test" è un po' un errore. Per essere chiari, questi test non usano ICMP (Internet Control Message Protocol) per controllare la disponibilità del sito. Usano invece funzionalità di richiesta HTTP più avanzate per convalidare se un endpoint risponde. Misurano anche le prestazioni associate a tale risposta e aggiungono la possibilità di impostare criteri di esito positivo personalizzati associati a funzionalità più avanzate, ad esempio l'analisi delle richieste dipendenti e la possibilità di tentativi.

Esistono due tipi di test ping URL che è possibile creare, test ping di base e standard.

> [!NOTE]
> I test ping Basic e Standard sono attualmente in anteprima pubblica. Queste versioni di anteprima vengono fornite senza un contratto di servizio. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.

Basic e Standard:

- Basic è limitato a cinque posizioni per ogni test.
- I test standard possono avere intestazioni personalizzate o corpo della richiesta.
- I test standard possono usare qualsiasi metodo di richiesta HTTP, mentre di base può usare solo `GET` .
- Il controllo della durata del certificato SSL avvisa l'utente di un periodo di tempo impostato prima della scadenza del certificato.
- I test standard sono una funzionalità a pagamento.

> [!NOTE]
> Attualmente non sono disponibili costi aggiuntivi per i test Ping standard della funzionalità di anteprima. I prezzi per le funzionalità disponibili in anteprima verranno annunciati in futuro e verrà fornito un avviso prima dell'inizio della fatturazione. Se si sceglie di continuare a usare i test Ping Standard dopo il periodo di preavviso, verrà addebitata la tariffa applicabile.

## <a name="create-a-url-ping-test"></a>Creare un test di ping URL

Per creare un test di disponibilità, è necessario usare una risorsa di Application Insight esistente o creare una Application Insights [risorsa .](create-new-resource.md)

Per creare la prima richiesta di disponibilità, aprire il riquadro Disponibilità e selezionare Crea test & scegliere lo SKU di test.

:::image type="content" source="./media/monitor-web-app-availability/create-basic-test.png" alt-text="Screenshot della creazione di un test ping dell'URL di base nel portale di Azure":::

|Impostazione | Spiegazione |
|--------|-------------|
|**URL** |  L'URL può essere qualsiasi pagina Web che si vuole testare, ma deve essere visibile da Internet pubblico. L'URL può includere una stringa di query. In questo modo, ad esempio, è possibile esercitarsi nell'uso del database. Se l'URL comporta un reindirizzamento, l'operazione viene effettuata fino a un numero massimo di 10 reindirizzamenti.|
|**Analizzare le richieste dipendenti**| Il test richiede immagini, script, file di stile e altri file che fanno parte della pagina Web testata. Il tempo di risposta registrato include il tempo impiegato per ottenere questi file. Il test ha esito negativo se una di queste risorse non può essere scaricata correttamente entro il timeout per l'intero test. Se l'opzione non viene selezionata, il test richiede solo il file in corrispondenza dell'URL specificato. L'abilitazione di questa opzione comporta un controllo più rigoroso. Il test potrebbe non riuscire per i casi, che potrebbero non essere evidenti durante l'esplorazione manuale del sito. |
|**Abilitare i tentativi**| Quando il test ha esito negativo, viene ritentato dopo un breve intervallo. Un errore viene segnalato solo se tre tentativi successivi non riescono. I test successivi vengono quindi eseguiti in base alla frequenza di test normale. I nuovi tentativi saranno temporaneamente sospesi fino al completamento successivo. Questa regola viene applicata in modo indipendente in ogni località di test. **È consigliabile usare questa opzione.** In media, circa l'80% degli errori non si ripresenta al nuovo tentativo.|
| **Test di convalida del certificato SSL** | È possibile verificare il certificato SSL nel sito Web per assicurarsi che sia installato correttamente, valido, attendibile e non presenti errori agli utenti. |
| **Controllo proattivo della durata** | In questo modo è possibile definire un periodo di tempo impostato prima della scadenza del certificato SSL. Una volta scaduto, il test avrà esito negativo. |
|**Frequenza test**| impostare la frequenza di esecuzione del test da ogni località di test. Con una frequenza predefinita di cinque minuti e cinque località di test, il sito verrà testato in media ogni minuto.|
|**Località di test**| Sono le posizioni da cui i server inviano richieste Web all'URL indicato. **Il numero minimo di località di test consigliate è cinque** per essere certi di poter distinguere i problemi del sito Web da quelli della rete. È possibile selezionare più di cinque posizioni con test standard e fino a 16 posizioni.|

**Se l'URL non è visibile dalla** rete Internet pubblica, è possibile scegliere di aprire in modo selettivo il firewall per consentire solo le transazioni di test tramite . Per altre informazioni sulle eccezioni del firewall per gli agenti di test di disponibilità, vedere la [guida all'indirizzo IP](./ip-addresses.md#availability-tests).

> [!NOTE]
> È consigliabile eseguire test da più posizioni **con un minimo di cinque posizioni.** Questo serve a evitare falsi allarmi che possono essere dovuti a problemi temporanei di una località specifica. Inoltre, è stato rilevato che la configurazione ottimale è che il numero di posizioni di test è uguale alla soglia di posizione dell'avviso **+ 2**.

## <a name="standard-test"></a>Standard Test

:::image type="content" source="./media/monitor-web-app-availability/standard-test-post.png" alt-text="Screenshot della scheda delle informazioni di test standard." border="false":::

|Impostazione | Spiegazione |
|--------|-------------|
| **Intestazioni personalizzate** | Coppie chiave-valore che definiscono i parametri operativi. |
| **Verbo di richiesta HTTP** | Indicare l'azione da intraprendere con la richiesta. Se il verbo scelto non è disponibile nell'interfaccia utente, è possibile distribuire un test standard usando Azure Monitoraggio risorse con la scelta desiderata. |
| **Testo della richiesta** | Dati personalizzati associati alla richiesta HTTP. È possibile caricare il tipo di file nel contenuto o disabilitare questa funzionalità. Per il contenuto del corpo non elaborato sono supportati TEXT, JSON, HTML, XML e JavaScript. |

## <a name="success-criteria"></a>Criteri di superamento

|Impostazione| Spiegazione
|----|----|----|
| **Timeout test** |ridurre questo valore per ricevere avvisi in merito alle risposte lente. Il test viene conteggiato come non riuscito se le risposte dal sito non sono state ricevute entro questo periodo. Se è stata selezionata l'opzione **Analizza richieste dipendenti**, è necessario che tutti gli script, i file di stile, le immagini e le altre risorse dipendenti siano stati ricevuti entro questo periodo.|
| **Risposta HTTP** | codice di stato restituito che indica un'operazione riuscita. 200 è il codice che indica che è stata restituita una normale pagina Web.|
| **Il contenuto corrisponde a** | Una stringa, ad esempio "Benvenuto". Verifichiamo che in ogni risposta ci una corrispondenza esatta di maiuscolo e minuscolo. Deve trattarsi di una stringa di testo normale, senza caratteri jolly. È importante ricordare che, se il contenuto cambia, potrebbe essere necessario aggiornare la stringa. **Con la corrispondenza del contenuto sono supportati solo i caratteri inglesi** |

## <a name="alerts"></a>Avvisi

|Impostazione| Spiegazione
|----|----|----|
|**Near real-time (anteprima)** | Si consiglia di usare gli avvisi near real-time. La configurazione di questo tipo di avviso viene eseguita dopo la creazione del test di disponibilità.  |
|**Soglia località di avviso**|Si consiglia un minimo di 3-5 posizioni. Il rapporto ottimale tra la soglia località di avviso e il numero di località di test è dato da **soglia località di avviso** = **numero di località di test - 2, con un numero minimo pari a cinque località di test.**|

## <a name="location-population-tags"></a>Tag di popolamento della posizione

I tag di popolamento seguenti possono essere usati per l'attributo geo-location quando si distribuisce un test ping dell'URL di disponibilità usando Azure Resource Manager.

#### <a name="azure-gov"></a>Azure gov

| Nome visualizzato   | Nome del popolamento     |
|----------------|---------------------|
| USGov Virginia | usgov-va-azr        |
| USGov Arizona  | usgov-phx-azr       |
| USGov Texas    | usgov-tx-azr        |
| Stati uniti orientali DoD     | usgov-ddeast-azr    |
| Stati Uniti centrali DoD  | usgov-ddcentral-azr |

#### <a name="azure"></a>Azure

| Nome visualizzato                           | Nome del popolamento   |
|----------------------------------------|-------------------|
| Australia orientale                         | emea-au-syd-edge  |
| Brasile meridionale                           | latam-br-gru-edge |
| Stati Uniti centrali                             | us-fl-mia-edge    |
| Asia orientale                              | apac-hk-hkn-azr   |
| Stati Uniti orientali                                | us-va-ash-azr     |
| Francia meridionale (in precedenza Francia centrale) | emea-ch-zrh-edge  |
| Francia centrale                         | emea-fr-pra-edge  |
| Giappone orientale                             | apac-jp-kaw-edge  |
| Europa settentrionale                           | emea-gb-db3-azr   |
| Stati Uniti centro-settentrionali                       | us-il-ch1-azr     |
| Stati Uniti centro-meridionali                       | us-tx-sn1-azr     |
| Asia sud-orientale                         | apac-sg-sin-azr   |
| Regno Unito occidentale                                | emea-se-sto-edge  |
| Europa occidentale                            | emea-nl-ams-azr   |
| Stati Uniti occidentali                                | us-ca-sjc-azr     |
| Regno Unito meridionale                               | emea-ru-msa-edge  |

## <a name="see-your-availability-test-results"></a>Visualizzare i risultati del test di disponibilità

I risultati dei test di disponibilità possono essere visualizzati sia con le visualizzazioni dei grafici a linee che con i grafici a dispersione.

Dopo alcuni minuti, fare clic **su Aggiorna per** visualizzare i risultati del test.

:::image type="content" source="./media/monitor-web-app-availability/availability-refresh-002.png" alt-text="Screenshot che mostra la pagina Disponibilità con il pulsante Aggiorna evidenziato.":::

La visualizzazione del grafico a dispersione mostra esempi dei risultati del test che hanno i dettagli del passaggio del test di diagnostica. Il motore di test archivia i dettagli diagnostici per i test che hanno restituito errori. Per i test riusciti, vengono archiviati i dettagli diagnostici per un subset delle esecuzioni. Passare il puntatore del mouse su uno dei punti verdi/rossi per visualizzare il test, il nome e la posizione del test.

:::image type="content" source="./media/monitor-web-app-availability/availability-scatter-plot-003.png" alt-text="Visualizzazione riga." border="false":::

Selezionare una posizione o un test specifico oppure ridurre il periodo di tempo per visualizzare più risultati riguardo all'intervallo desiderato. Usare Esplora ricerche per visualizzare i risultati di tutte le esecuzioni oppure usare query di analisi per eseguire report personalizzati per i dati.

## <a name="inspect-and-edit-tests"></a> Esaminare e modificare i test

Per modificare, disabilitare temporaneamente o eliminare un test, fare clic sui puntini di sospensione accanto al nome di un test. La propagazione delle modifiche di configurazione a tutti gli agenti di test dopo una modifica può richiedere fino a 20 minuti.

:::image type="content" source="./media/monitor-web-app-availability/edit.png" alt-text="Visualizzare i dettagli del test. Modificare e disabilitare un test Web." border="false":::

Può essere necessario disabilitare i test di disponibilità o le regole di avviso associate ai test durante le operazioni di manutenzione del servizio.

## <a name="if-you-see-failures"></a>In caso di errori

Selezionare un punto rosso.

:::image type="content" source="./media/monitor-web-app-availability/end-to-end.png" alt-text="Screenshot della scheda dettagli transazione end-to-end." border="false":::

Dal risultato di un test di disponibilità è possibile visualizzare i dettagli delle transazioni in tutti i componenti. A questo punto è possibile:

* Esaminare il report sulla risoluzione dei problemi per determinare ciò che potrebbe aver causato l'esito negativo del test, ma l'applicazione è ancora disponibile.
* Controllare la risposta ricevuta dal server.
* Diagnosticare l'errore con i dati di telemetria lato server correlati, raccolti durante l'elaborazione del test di disponibilità non riuscito.
* Registrare un problema o elemento di lavoro in Git o Azure Boards per tenere traccia del problema. Il bug conterrà un collegamento a questo evento.
* Aprire il risultato del test Web in Visual Studio.

Per altre informazioni sull'esperienza di diagnostica delle transazioni end-to-end, vedere la documentazione [sulla diagnostica delle transazioni](./transaction-diagnostics.md).

Fare clic sulla riga dell'eccezione per visualizzare i dettagli dell'eccezione lato server che ha causato l'errore durante il test di disponibilità sintetico. È anche possibile ottenere lo [snapshot di debug](./snapshot-debugger.md) per una diagnostica più avanzata a livello di codice.

:::image type="content" source="./media/monitor-web-app-availability/open-instance-4.png" alt-text="Diagnostica lato server.":::

Oltre ai risultati non elaborati, è anche possibile visualizzare due metriche chiave di disponibilità in [Esplora metriche](../essentials/metrics-getting-started.md):

1. Disponibilità: percentuale dei test riusciti rispetto a tutte le esecuzioni di test.
2. Durata test: durata media dei test rispetto a tutte le esecuzioni di test.

## <a name="automation"></a>Automazione

* [Usare script di PowerShell per configurare un test di disponibilità](./powershell.md#add-an-availability-test) automaticamente.
* Configurare un [webhook](../alerts/alerts-webhooks.md) che verrà chiamato quando viene generato un avviso.


## <a name="next-steps"></a>Passaggi successivi

* [Avvisi di disponibilità](availability-alerts.md)
* [Test Web in più passaggi](availability-multistep.md)
* [Risoluzione dei problemi](troubleshoot-availability.md)
