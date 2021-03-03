---
title: Risolvere i problemi relativi all'aggiornamento del dispositivo comune per i problemi dell'hub Azure Microsoft Docs
description: Questo documento fornisce un elenco di suggerimenti e consigli per risolvere molti possibili problemi che possono verificarsi con l'aggiornamento dei dispositivi per l'hub Internet.
author: lichris
ms.author: lichris
ms.date: 2/17/2021
ms.topic: troubleshooting
ms.service: iot-hub-device-update
ms.openlocfilehash: 6329e93bb5e628d68afbb2700ce0b9e3a2a711ad
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679494"
---
# <a name="device-update-for-iot-hub-troubleshooting-guide"></a>Guida alla risoluzione dei problemi dell'hub degli aggiornamenti del dispositivo

Questo documento elenca alcune domande frequenti e i problemi segnalati dagli utenti degli aggiornamenti dei dispositivi. Quando l'aggiornamento del dispositivo avanza attraverso l'anteprima pubblica, questa guida alla risoluzione dei problemi verrà aggiornata periodicamente con nuove domande e soluzioni. Se si verifica un problema che non viene visualizzato in questa guida alla risoluzione dei problemi, fare riferimento alla sezione relativa all' [supporto tecnico Microsoft di contatto](#contact) per documentare la situazione.

## <a name="importing-updates"></a><a name="import"></a>Importazione di aggiornamenti

### <a name="q-im-having-trouble-connecting-my-device-update-instance-to-my-iot-hub-instance"></a>D: si sono verificati problemi durante la connessione dell'istanza di aggiornamento del dispositivo all'istanza dell'hub Internet.
_Verificare che le route dei messaggi dell'hub Internet siano configurate correttamente, in base alla documentazione relativa alle risorse per l' [aggiornamento del dispositivo](./device-update-resources.md) ._

### <a name="q-im-encountering-a-role-related-error-error-message-in-azure-portal-or-a-403-api-error"></a>D: si verifica un errore correlato al ruolo (messaggio di errore in portale di Azure o errore dell'API 403).
_È possibile che non si disponga delle autorizzazioni di accesso configurate correttamente. Verificare di aver configurato correttamente le autorizzazioni di accesso in base alla documentazione relativa al controllo di accesso per l' [aggiornamento dei dispositivi](./device-update-control-access.md) ._

### <a name="q-im-encountering-a-500-type-error-when-importing-content-to-the-device-update-service"></a>D: si verifica un errore di tipo 500 durante l'importazione del contenuto nel servizio di aggiornamento del dispositivo.
_Un codice di errore nell'intervallo 500 può indicare un problema con il servizio di aggiornamento del dispositivo. Attendere 5 minuti, quindi riprovare. Se lo stesso errore viene mantenuto, seguire le istruzioni nella sezione [contatto supporto tecnico Microsoft](#contact) per inviare una richiesta di supporto con Microsoft._

### <a name="q-im-encountering-an-error-code-when-importing-content-and-would-like-to-parse-it"></a>D: si verifica un codice di errore durante l'importazione del contenuto e si desidera analizzarlo.
_Per informazioni sull'analisi dei codici di errore, vedere la documentazione relativa ai codici di errore per l' [aggiornamento del dispositivo](./device-update-error-codes.md) ._

## <a name="device-failures"></a><a name="device-failure"></a>Errori del dispositivo

### <a name="q-how-can-i-ensure-my-device-is-connected-to-device-update-for-iot-hub"></a>D: come è possibile verificare che il dispositivo sia connesso all'aggiornamento del dispositivo per l'hub Internet?
_È possibile verificare che il dispositivo sia connesso all'aggiornamento del dispositivo controllando se viene visualizzato nella sezione "dispositivi non raggruppati" nella visualizzazione conformità del portale di Azure._

### <a name="q-one-or-more-of-my-devices-is-failing-to-update"></a>D: non è possibile aggiornare uno o più dispositivi personali.
_Esistono molte possibili cause principali per un errore di aggiornamento del dispositivo. Verificare che il dispositivo sia: 1) connesso all'istanza dell'hub Internet, 2) connesso all'istanza di aggiornamento del dispositivo e 3) il servizio ottimizzazione recapito è in esecuzione. Se tutte e tre le condizioni sono vere per il dispositivo, seguire le istruzioni nella sezione [contatto supporto tecnico Microsoft](#contact) per inviare una richiesta di supporto con Microsoft._

## <a name="deploying-an-update"></a><a name="deploy"></a> Distribuzione di un aggiornamento

### <a name="q-ive-deployed-an-update-to-my-devices-but-the-compliance-status-says-it-isnt-on-the-latest-update-what-should-i-do"></a>D: è stato distribuito un aggiornamento ai dispositivi personali, ma lo stato di conformità indica che non si tratta dell'aggiornamento più recente. Cosa devo fare?
_Lo stato di conformità del dispositivo può richiedere fino a 5 minuti per l'aggiornamento. Attendere, quindi controllare di nuovo._
### <a name="q-my-devices-deployment-status-shows-incompatible-what-should-i-do"></a>D: lo stato di distribuzione del dispositivo Mostra incompatibile, cosa devo fare?
_È possibile che le proprietà del produttore e del modello di un dispositivo di destinazione siano state modificate dopo la connessione del dispositivo all'hub Internet, facendo in modo che il dispositivo sia ora considerato incompatibile con il contenuto dell'aggiornamento della distribuzione corrente._

_Controllare l' [interfaccia Adu Core](./device-update-plug-and-play) per visualizzare il produttore e il modello che il dispositivo sta segnalando al servizio di aggiornamento del dispositivo e verificare che corrisponda al produttore e al modello specificati nel [manifesto di importazione](./import-concepts.md) del contenuto di aggiornamento da distribuire. È possibile modificare queste proprietà per un determinato dispositivo usando il [file di configurazione dell'aggiornamento del dispositivo](./device-update-cofiguration-file.md)._

### <a name="q-i-see-my-deployment-is-in-active-stage-but-none-of-my-devices-are-in-progress-with-the-update-what-should-i-do"></a>D: la distribuzione è in fase "attiva", ma nessuno dei miei dispositivi è "in corso" con l'aggiornamento. Cosa devo fare?
_Assicurarsi che la data di inizio della distribuzione non sia impostata in futuro. Quando si crea una nuova distribuzione, la data di inizio della distribuzione viene impostata automaticamente sul giorno successivo come protezione, a meno che non venga modificata in modo esplicito. È possibile attendere l'arrivo della data di inizio della distribuzione o annullare la distribuzione in corso e creare una nuova distribuzione con la data di inizio desiderata._

### <a name="q-im-trying-to-group-my-devices-but-i-dont-see-the-tag-in-the-drop-down-when-creating-a-group"></a>D: si sta provando a raggruppare i dispositivi, ma non viene visualizzato il tag nell'elenco a discesa durante la creazione di un gruppo.
_Assicurarsi di aver configurato correttamente le route dei messaggi nell'hub Internet in base alla documentazione relativa alle risorse per l' [aggiornamento del dispositivo](./device-update-resources.md) . Dopo aver configurato la route, sarà necessario contrassegnare nuovamente il dispositivo._

_Un'altra causa principale potrebbe essere che il tag è stato applicato prima di connettere il dispositivo all'aggiornamento del dispositivo per l'hub Internet. Verificare che il dispositivo sia già connesso all'aggiornamento del dispositivo. È possibile verificare che il dispositivo sia connesso all'aggiornamento del dispositivo per l'hub Internet controllando se viene visualizzato in dispositivi "non raggruppati" nella visualizzazione conformità. Aggiungere temporaneamente un tag di un valore diverso e quindi aggiungere di nuovo il tag previsto dopo la connessione del dispositivo._

_Se si usa il servizio Device provisioning (DPS), assicurarsi di contrassegnare i dispositivi dopo averli sottoposti a provisioning e non durante il processo di creazione del dispositivo. Se il dispositivo è già stato contrassegnato durante il passaggio di creazione del dispositivo, sarà necessario contrassegnare temporaneamente il dispositivo con un valore diverso dopo il provisioning e quindi aggiungere di nuovo il tag desiderato._

### <a name="q-my-deployment-completed-successfully-but-some-devices-failed-to-update"></a>D: la distribuzione è stata completata correttamente, ma alcuni dispositivi non sono stati aggiornati.
_Il problema potrebbe essere causato da un errore sul lato client nei dispositivi in cui si è verificato l'errore. Vedere la sezione Errori del dispositivo in questa guida alla risoluzione dei problemi._

### <a name="q-i-encountered-an-error-in-the-ux-when-trying-to-initiate-a-deployment"></a>D: si è verificato un errore nell'esperienza utente durante il tentativo di avviare una distribuzione.
_Il problema potrebbe essere causato da un bug di servizio/UX o da un problema relativo alle autorizzazioni per le API. Seguire le istruzioni nella sezione [contatto supporto tecnico Microsoft](#contact) per inviare una richiesta di supporto con Microsoft._

### <a name="q-i-started-a-deployment-but-it-isnt-reaching-an-end-state"></a>D: è stata avviata una distribuzione, ma non raggiunge uno stato finale.
_Il problema potrebbe essere causato da un problema di prestazioni del servizio, da un bug del servizio o da un bug del client. Ripetere la distribuzione dopo 10 minuti. Se si verifica lo stesso problema, effettuare il pull dei log del dispositivo e fare riferimento alla sezione Errori del dispositivo in questa guida alla risoluzione dei problemi. Se lo stesso problema persiste, seguire le istruzioni nella sezione [contatto supporto tecnico Microsoft](#contact) per inviare una richiesta di supporto con Microsoft._

## <a name="downloading-updates-onto-devices"></a><a name="download"></a> Download degli aggiornamenti sui dispositivi

### <a name="q-how-do-i-resume-a-download-when-a-device-has-reconnected-after-a-period-of-disconnection"></a>D: Ricerca per categorie riprendere un download quando un dispositivo si è riconnesso dopo un periodo di disconnessione?
_Il download riprenderà autonomamente quando la connettività viene ripristinata entro un periodo di 24 ore. Dopo 24 ore, il download dovrà essere riavviato dall'utente._
## <a name="using-microsoft-connected-cache-mcc"></a><a name="mcc"></a> Uso di Microsoft Connected cache (MCC)

### <a name="q-i-am-encountering-an-issue-when-attempting-to-deploy-the-mcc-module-on-my-iot-edge-device"></a>D: si è verificato un problema durante il tentativo di distribuire il modulo MCC nel dispositivo IoT Edge.
_Vedere la [documentazione di IOT Edge]() per la distribuzione di moduli perimetrali per IOT Edge dispositivi. È possibile verificare se il modulo MCC viene eseguito correttamente nel dispositivo IoT Edge passando a http://localhost:5100/Summary ._
### <a name="q-one-of-my-iot-devices-is-attempting-to-download-an-update-through-mcc-but-is-failing"></a>D: uno dei miei dispositivi Internet sta provando a scaricare un aggiornamento tramite MCC, ma ha esito negativo.
_Esistono diversi problemi che potrebbero causare un errore del dispositivo Internet per la connessione a MCC. Per diagnosticare il problema, raccogliere i registri DO client e nginx dal dispositivo che ha generato l'errore (vedere la sezione [contatto supporto tecnico Microsoft](#contact) per istruzioni sulla raccolta dei log client)._

_Il dispositivo potrebbe non riuscire a eseguire il pull del contenuto da Internet per passare al relativo modulo MCC perché l'URL usato non è consentito. Per determinare in tal caso, sarà necessario verificare le variabili di ambiente IoT Edge in portale di Azure._
## <a name="contacting-microsoft-support"></a><a name="contact"></a> supporto tecnico Microsoft di contatto

Se si verificano problemi che non possono essere risolti usando le domande frequenti precedenti, è possibile archiviare una richiesta di supporto con supporto tecnico Microsoft tramite l'interfaccia portale di Azure. A seconda della categoria a cui si indica il problema, è possibile che venga richiesto di raccogliere e condividere dati aggiuntivi per aiutare supporto tecnico Microsoft analizzare il problema. 

Per istruzioni su come raccogliere ogni tipo di dati, vedere di seguito. È possibile usare [GetDevices]() per verificare la presenza di informazioni aggiuntive nella risposta del payload dell'API.

Inoltre, le informazioni seguenti possono essere utili per limitare la causa principale del problema:
* Tipo di dispositivo che si sta tentando di aggiornare (Azure Percept, gateway di IoT Edge, altro)
* Quale tipo di client di aggiornamento dispositivo si usa (basato su immagine, basato su pacchetti e simulatore)
* Quale sistema operativo è in esecuzione nel dispositivo
* Dettagli relativi all'architettura del dispositivo
* Se è stato usato correttamente l'aggiornamento del dispositivo per aggiornare un dispositivo prima

Se è disponibile una delle informazioni precedenti, includerla nella descrizione del problema.

### <a name="collecting-client-logs"></a>Raccolta dei log del client

* Nel dispositivo Raspberry Pi sono disponibili due set di log:

    ```markdown
    /adu/logs
    ```

    ```markdown
    /var/cache/do-client-lite/log
    ```

* Per il client in pacchetto, i log sono disponibili qui:

    ```markdown
    /var/log/adu
    ```

    ```markdown
    /var/cache/do-client-lite/log
    ```

* Per il simulatore, i log sono disponibili qui:

    ```markdown
    /tmp/aduc-logs
    ```

### <a name="error-codes"></a>Codici di errore
È possibile che venga richiesto di fornire codici di errore quando si segnala un problema relativo all'importazione di un aggiornamento, un errore del dispositivo o la distribuzione di un aggiornamento.

I codici di errore possono essere ottenuti osservando l'interfaccia [ADUCoreInterface](./device-update-plug-and-play.md) . Per informazioni su come analizzare i codici di errore per la diagnosi automatica e la risoluzione dei problemi, vedere la documentazione sui codici di errore per l' [aggiornamento dei dispositivi](./device-update-error-codes.md) .

### <a name="trace-id"></a>ID traccia
Potrebbe essere richiesto di fornire un ID traccia quando si segnala un problema relativo all'importazione o distribuzione di un aggiornamento.

L'ID di traccia per una determinata azione utente è disponibile nella risposta API o nella sezione cronologia importazione dell'interfaccia utente di portale di Azure. 

Attualmente, gli ID di traccia per le azioni di distribuzione sono accessibili solo tramite la risposta API.

### <a name="deployment-id"></a>ID distribuzione
È possibile che venga richiesto di fornire un ID distribuzione quando si segnala un problema relativo alla distribuzione di un aggiornamento.

L'ID distribuzione viene creato dall'utente quando si chiama l'API per avviare una distribuzione.

Attualmente, gli ID di distribuzione per le distribuzioni avviate dall'interfaccia utente di portale di Azure vengono generati automaticamente e non vengono esposti all'utente.

### <a name="iot-hub-instance-name"></a>Nome dell'istanza dell'hub Internet
È possibile che venga richiesto di fornire il nome dell'istanza dell'hub Internet quando si segnala un problema relativo agli errori del dispositivo o alla distribuzione di un aggiornamento.

Il nome dell'hub Internet viene scelto dall'utente quando ne viene effettuato il provisioning per la prima volta.

### <a name="device-update-account-name"></a>Nome dell'account di aggiornamento del dispositivo
Potrebbe essere richiesto di fornire il nome dell'account di aggiornamento del dispositivo quando si segnala un problema relativo all'importazione di un aggiornamento, errori del dispositivo o distribuzione di un aggiornamento.

Il nome dell'account di aggiornamento del dispositivo viene scelto dall'utente al momento dell'iscrizione al servizio. Altre informazioni sono reperibili nella documentazione relativa alle risorse per l' [aggiornamento dei dispositivi](./device-update-resources.md) .

### <a name="device-update-instance-name"></a>Nome istanza aggiornamento dispositivo
Potrebbe essere richiesto di fornire il nome dell'istanza di aggiornamento del dispositivo quando si segnala un problema relativo all'importazione di un aggiornamento, errori del dispositivo o distribuzione di un aggiornamento.

Il nome dell'istanza di aggiornamento del dispositivo viene scelto dall'utente quando viene eseguito il provisioning per la prima volta. Altre informazioni sono reperibili nella documentazione relativa alle risorse per l' [aggiornamento dei dispositivi](./device-update-resources.md) .

### <a name="device-id"></a>ID dispositivo
Potrebbe essere richiesto di fornire un ID dispositivo quando si segnala un problema relativo agli errori del dispositivo o alla distribuzione di un aggiornamento.

L'ID del dispositivo viene definito dal cliente quando viene effettuato il primo provisioning del dispositivo. Può anche essere recuperato dal dispositivo gemello del dispositivo.

### <a name="update-id"></a>ID aggiornamento
Potrebbe essere richiesto di fornire un ID aggiornamento quando si segnala un problema relativo alla distribuzione di un aggiornamento.

L'ID aggiornamento viene definito dal cliente quando si avvia una distribuzione.

### <a name="nginx-logs"></a>Log nginx
Potrebbe essere richiesto di fornire i log nginx quando si segnala un problema relativo alla cache connessa a Microsoft.

### <a name="adu-conftxt"></a>ADU-conf.txt
È possibile che venga richiesto di fornire il file di configurazione dell'aggiornamento del dispositivo ("adu-conf.txt") quando si segnala un problema relativo alla distribuzione di un aggiornamento.

Il file di configurazione è facoltativo e creato dall'utente seguendo le istruzioni riportate nella documentazione relativa alla [configurazione dell'aggiornamento del dispositivo](./device-update-configuration-file.md) .

### <a name="import-manifest"></a>Importa manifesto
È possibile che venga richiesto di fornire il file manifesto di importazione quando si segnala un problema relativo all'importazione o distribuzione di un aggiornamento.

Il manifesto di importazione è un file creato dal cliente durante l'importazione del contenuto di aggiornamento nel servizio di aggiornamento del dispositivo.

**[Passaggio successivo: altre informazioni sui codici di errore di aggiornamento del dispositivo](.\device-update-error-codes.md)**