---
title: Risolvere i problemi relativi ai test di disponibilità di applicazione Azure Insights
description: Risolvere i problemi dei test Web in applicazione Azure Insights. Ottenere avvisi se un sito Web diventa non disponibile o risponde lentamente.
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 02/14/2021
ms.reviewer: sdash
ms.openlocfilehash: 940a70de81df60f7b6c1545c992e1ee10e69fa9f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101728927"
---
# <a name="troubleshooting"></a>Risoluzione dei problemi

Questo articolo consente di risolvere i problemi comuni che possono verificarsi quando si usa il monitoraggio della disponibilità.

## <a name="troubleshooting-report-steps-for-ping-tests"></a>Risoluzione dei problemi relativi ai passaggi del report per i test ping

Il report per la risoluzione dei problemi consente di diagnosticare facilmente i problemi comuni che provocano un errore nei **test ping** .

![Animazione dell'esplorazione dalla scheda disponibilità selezionando un errore nei dettagli della transazione end-to-end per visualizzare il report sulla risoluzione dei problemi](./media/troubleshoot-availability/availability-to-troubleshooter.gif)

1. Nella scheda disponibilità della risorsa di Application Insights selezionare complessiva o uno dei test di disponibilità.
2. Selezionare **non riuscito** , quindi eseguire un test in "drill into" a sinistra o selezionare uno dei punti nel grafico a dispersione.
3. Nella pagina dettagli transazione end-to-end selezionare un evento in "risoluzione dei problemi di riepilogo report" selezionare **[Vai al passaggio]** per visualizzare il report sulla risoluzione dei problemi.

> [!NOTE]
>  Se è presente il passaggio riutilizzo connessione, non saranno presenti i passaggi di risoluzione DNS, creazione connessione e trasporto TLS.

|Passaggio | Messaggio di errore | Possibile causa |
|-----|---------------|----------------|
| Riutilizzo connessione | n/d | In genere dipende da una connessione stabilita in precedenza, ovvero il passaggio del test Web dipende. Quindi, non è necessario alcun passaggio DNS, Connection o SSL. |
| Risoluzione DNS | Non è stato possibile risolvere il nome remoto: "URL" | Il processo di risoluzione DNS ha avuto esito negativo, probabilmente a causa di record DNS non configurati correttamente o errori temporanei del server DNS. |
| Attivazione della connessione | Tentativo di connessione non riuscito. risposta non corretta della parte connessa dopo un periodo di tempo. | In generale, significa che il server non risponde alla richiesta HTTP. Una cause comune è che gli agenti di test sono bloccati da un firewall nel server. Se si vuole eseguire il test in una rete virtuale di Azure, è necessario aggiungere il tag del servizio di disponibilità all'ambiente.|
| Trasporto TLS  | Il client e il server non sono in grado di comunicare perché non dispongono di un algoritmo comune.| Sono supportati solo TLS 1,0, 1,1 e 1,2. SSL non è supportato. Questo passaggio non convalida i certificati SSL e stabilisce una connessione protetta. Questo passaggio viene visualizzato solo quando si verifica un errore. |
| Ricezione dell'intestazione della risposta | Impossibile leggere i dati dalla connessione di trasporto. La connessione è stata chiusa. | Il server ha eseguito il commit di un errore di protocollo nell'intestazione della risposta. Ad esempio, la connessione chiusa dal server quando la risposta non è completa. |
| Ricezione del corpo della risposta | Impossibile leggere i dati dalla connessione di trasporto: la connessione è stata chiusa. | Il server ha eseguito il commit di un errore di protocollo nel corpo della risposta. Ad esempio, la connessione è stata chiusa dal server quando la risposta non è completamente letta o la dimensione del blocco non è corretta nel corpo della risposta in blocco. |
| Convalida limite di Reindirizzamento | Questa pagina Web contiene troppi reindirizzamenti. Questo ciclo verrà terminato qui perché questa richiesta ha superato il limite per i reindirizzamenti automatici. | È previsto un limite di 10 reindirizzamenti per test. |
| Convalida del codice di stato | `200 - OK` non corrisponde allo stato previsto `400 - BadRequest` . | codice di stato restituito che indica un'operazione riuscita. 200 è il codice che indica che è stata restituita una normale pagina Web. |
| Convalida contenuto | Il testo obbligatorio ' Hello ' non è stato visualizzato nella risposta. | La stringa non è una corrispondenza esatta con distinzione tra maiuscole e minuscole nella risposta, ad esempio la stringa "Welcome!". Deve essere una stringa semplice, senza caratteri jolly, ad esempio un asterisco. Se il contenuto della pagina cambia, potrebbe essere necessario aggiornare la stringa. Con la corrispondenza del contenuto sono supportati solo i caratteri inglesi. |
  
## <a name="common-troubleshooting-questions"></a>Domande comuni sulla risoluzione dei problemi

### <a name="site-looks-okay-but-i-see-test-failures-why-is-application-insights-alerting-me"></a>Il sito sembra funzionare correttamente, ma i test segnalano errori. Perché Application Insights invia avvisi?

   * Il test ha **richieste di analisi dipendenti** abilitate? Ciò comporta un controllo rigoroso delle risorse, ad esempio script, immagini e così via. Questi tipi di errori potrebbero non essere evidenti in un browser. Controllare tutte le immagini, gli script, i fogli di stile e qualsiasi altro file caricato dalla pagina. Se uno di essi ha esito negativo, il test viene segnalato come non superato, anche se la pagina HTML principale viene caricata senza problemi. Per desensibilizzare il test a errori di questo tipo, è sufficiente deselezionare l'opzione analizza richieste dipendenti dalla configurazione di test.

   * Per ridurre le probabilità di rumore dai blip di rete temporanei e così via, verificare che sia selezionata l'opzione Abilita tentativi per la configurazione degli errori dei test. È anche possibile eseguire test da più posizioni e gestire la soglia delle regole di avviso di conseguenza per evitare che problemi specifici della posizione causino avvisi non dovuti.

   * Fare clic su uno dei punti rossi dall'esperienza esperienza del tracciato a dispersione di disponibilità o su qualsiasi errore di disponibilità da Esplora ricerche per visualizzare i dettagli del motivo per cui è stato segnalato l'errore. Il risultato del test, insieme ai dati di telemetria lato server correlati (se abilitati), dovrebbe consentire di comprendere perché il test non è riuscito. Le cause più comuni dei problemi temporanei sono problemi di connessione o di rete.

   * Controllare se si è verificato il timeout del test. I test vengono interrotti dopo 2 minuti. Se il ping o il test in più passaggi richiede più di 2 minuti, verrà segnalato come errore. È consigliabile suddividere il test in più test di durata più breve.

   * Valutare se sono stati segnalati errori in tutte le località e solo in alcune. Se gli errori sono stati segnalati solo in alcune località, potrebbero essere causati da errori di rete/CDN. Anche in questo caso, facendo clic sui puntini rossi dovrebbe essere possibile comprendere perché nella località sono stati segnalati errori.

### <a name="i-did-not-get-an-email-when-the-alert-triggered-or-resolved-or-both"></a>Non si è ricevuto un messaggio di posta elettronica quando l'avviso è stato attivato o risolto o in entrambi i casi?

Controllare la configurazione del gruppo di azioni degli avvisi per confermare che la posta elettronica sia elencata in modo diretto oppure che sia stata configurata una lista di distribuzione per la ricezione delle notifiche. In quest'ultimo caso, controllare la configurazione della lista di distribuzione per verificare che possa ricevere messaggi di posta elettronica esterni. Controllare anche se l'amministratore di posta elettronica ha eventualmente configurato criteri che possono causare questo problema.

### <a name="i-did-not-receive-the-webhook-notification"></a>Non si è ricevuto la notifica webhook?

Verificare che l'applicazione che riceve la notifica webhook sia disponibile e che riesca a elaborare le richieste di un webhook. Per altre informazioni, vedere [qui](../alerts/alerts-log-webhook.md).

### <a name="i-am-getting--403-forbidden-errors-what-does-this-mean"></a>Si ricevono 403 errori non consentiti, che cosa significa?

Questo errore indica che è necessario aggiungere eccezioni del firewall per consentire agli agenti di disponibilità di testare l'URL di destinazione. Per un elenco completo degli indirizzi IP di Agent da consentire, vedere l' [articolo eccezione IP](./ip-addresses.md#availability-tests).

### <a name="intermittent-test-failure-with-a-protocol-violation-error"></a>Esito negativo intermittente dei test con un errore di violazione del protocollo?

Un errore di tipo "Violazione del protocollo... CR deve essere seguito da LF" indica un problema relativo al server o alle dipendenze. Questa situazione si verifica quando nella risposta vengono impostate intestazioni in formato non valido e può essere causata da servizi di bilanciamento del carico o reti per la distribuzione di contenuti. In particolare, è possibile che alcune intestazioni non usino CRLF per indicare la fine della riga, che viola la specifica HTTP e pertanto non riesce a eseguire la convalida a livello di WebRequest .NET. Controllare la risposta per individuare le intestazioni, che potrebbero essere in violazione.

> [!NOTE]
> l'errore dell'URL potrebbe non verificarsi in browser con una convalida delle intestazioni HTTP meno rigida. Per una spiegazione dettagliata del problema, vedere questo post di blog: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  

### <a name="i-dont-see-any-related-server-side-telemetry-to-diagnose-test-failures"></a>Non vengono visualizzati dati di telemetria sul lato server correlati per diagnosticare gli errori dei test? *

Se Application Insights è configurato per l'applicazione lato server, il motivo può essere l'esecuzione del [campionamento](./sampling.md). Selezionare un risultato di disponibilità diverso.

### <a name="can-i-call-code-from-my-web-test"></a>È possibile chiamare codice da un test Web?

No. I passaggi del test devono essere nel file con estensione webtest. Inoltre non è possibile chiamare altri test web o utilizzare cicli. Esistono diversi plug-in che potrebbero risultare utili.


### <a name="is-there-a-difference-between-web-tests-and-availability-tests"></a>Esiste una differenza tra "test Web" e "test di disponibilità"?

I due termini vengono usati in modo intercambiabile. Test di disponibilità è un termine più generico che include i singoli test di ping URL oltre ai test Web in più passaggi.

### <a name="id-like-to-use-availability-tests-on-our-internal-server-that-runs-behind-a-firewall"></a>È possibile usare test di disponibilità nel server interno protetto da un firewall?

   Esistono due possibili soluzioni:

   * Configurare il firewall per consentire richieste in ingresso dagli [indirizzi IP degli agenti di test Web](./ip-addresses.md).
   * Scrivere il proprio codice per testare periodicamente il server interno. Eseguire il codice come processo in background in un server di prova protetto da firewall. Il processo di test può inviare i risultati ad Application Insights tramite l'API [TrackAvailability()](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) nel pacchetto SDK di base. In questo modo è necessario testare il server per avere un accesso in uscita per l'endpoint di inserimento di Application Insights, ma questo rappresenta un minore rischio per la sicurezza rispetto all'alternativa di consentire le richieste in ingresso. I risultati verranno visualizzati nei pannelli dei test Web di disponibilità, anche se l'esperienza sarà leggermente semplificata rispetto a quanto disponibile per i test creati tramite il portale. I test di disponibilità personalizzati verranno visualizzati anche come risultati della disponibilità in analisi, ricerca e metriche.

### <a name="uploading-a-multi-step-web-test-fails"></a>Non è possibile caricare un test Web in più passi

Di seguito sono riportate alcune delle possibili cause:
   * È previsto un limite di dimensioni pari a 300 KB.
   * I cicli non sono supportati.
   * I riferimenti ad altri test Web non sono supportati.
   * Le origini dati non sono supportate.

### <a name="my-multi-step-test-doesnt-complete"></a>Il test in più passi non viene completato

È previsto un limite di 100 richieste per ogni test. Inoltre, il test viene arrestato se la durata dell'esecuzione è superiore a due minuti.

### <a name="how-can-i-run-a-test-with-client-certificates"></a>È possibile eseguire un test con certificati client?

Non supportato attualmente.

## <a name="next-steps"></a>Passaggi successivi

* [Test Web in più passaggi](availability-multistep.md)
* [Test ping URL](monitor-web-app-availability.md)