---
title: Sicurezza per l'aggiornamento del dispositivo per hub IoT di Azure | Microsoft Docs
description: Informazioni su come l'aggiornamento dei dispositivi per l'hub IoT garantisce che i dispositivi siano aggiornati in modo sicuro.
author: lichris
ms.author: lichris
ms.date: 4/15/2021
ms.topic: conceptual
ms.service: iot-hub
ms.openlocfilehash: b10049e03e26cfe8da2bd57cc9f69dd933af706b
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567299"
---
# <a name="device-update-security-model"></a>Modello di sicurezza dell'aggiornamento del dispositivo

L'aggiornamento dei dispositivi per l'hub IoT offre un metodo sicuro per distribuire gli aggiornamenti per il firmware, le immagini e le applicazioni dei dispositivi IoT. Il flusso di lavoro fornisce un canale sicuro end-to-end con un modello di catena di custodia completo che un dispositivo può usare per dimostrare che un aggiornamento è attendibile, non modificato e intenzionale.

Ogni passaggio del flusso di lavoro di aggiornamento del dispositivo è protetto tramite varie funzionalità e processi di sicurezza per garantire che ogni passaggio nella pipeline esegua un passaggio protetto a quello successivo. Il client di aggiornamento del dispositivo identifica e gestisce correttamente tutte le richieste di aggiornamento illegittimo. Il client controlla anche ogni download per assicurarsi che il contenuto sia attendibile, non modificato e intenzionale.

## <a name="for-solution-operators"></a>Per gli operatori della soluzione

Quando Solution Operators importa gli aggiornamenti nell'istanza di Aggiornamento dispositivo, il servizio carica e controlla i file binari di aggiornamento per assicurarsi che non siano stati modificati o scambiati da un utente malintenzionato. Dopo la verifica, il servizio Aggiornamento dispositivo genera un manifesto [di aggiornamento](./update-manifest.md) interno con hash di file dal manifesto di importazione e da altri metadati. Questo manifesto di aggiornamento viene quindi firmato dal servizio Aggiornamento dispositivo.

Dopo l'inserimento nel servizio e l'archiviazione in Azure, i file binari di aggiornamento e i metadati dei clienti associati vengono crittografati automaticamente quando sono in pausa dal servizio di archiviazione di Azure. Il servizio Aggiornamento dispositivo non fornisce automaticamente crittografia aggiuntiva, ma consente agli sviluppatori di crittografare il contenuto prima che il contenuto raggiunga il servizio Aggiornamento dispositivo.

Quando l'operatore della soluzione richiede di aggiornare un dispositivo, al dispositivo viene inviato un messaggio firmato tramite il canale dell'hub IoT protetto. La firma della richiesta viene convalidata dall'agente di aggiornamento del dispositivo come autentica. 

Qualsiasi download binario risultante viene protetto tramite la convalida della firma del manifesto dell'aggiornamento. Il manifesto di aggiornamento contiene gli hash dei file binari, quindi, una volta che il manifesto è considerato attendibile, l'agente di aggiornamento dispositivi considera attendibili gli hash e li trova in corrispondenza dei file binari. Dopo che il file binario di aggiornamento è stato scaricato e verificato, viene quindi passato in modo sicuro al programma di installazione nel dispositivo.

## <a name="for-device-builders"></a>Per i generatori di dispositivi

Per garantire che il servizio Aggiornamento dispositivi sia ridotto a dispositivi semplici e a prestazioni basse, il modello di sicurezza usa chiavi asimmetriche non elaborati e firme non elaborati. Usano formati basati su JSON, ad esempio token Web JSON & JSON Web Keys.

### <a name="securing-update-content-via-the-update-manifest"></a>Protezione del contenuto degli aggiornamenti tramite il manifesto dell'aggiornamento

Il manifesto di aggiornamento viene convalidato usando due firme. Le firme vengono create usando una struttura costituita da chiavi *di firma* e *chiavi* radice.

L'agente di aggiornamento dispositivi ha chiavi pubbliche incorporate che vengono usate per tutti i dispositivi compatibili con l'aggiornamento del dispositivo. Queste sono le *chiavi* radice. Le chiavi private corrispondenti sono controllate da Microsoft.

Microsoft genera anche una coppia di chiavi pubblica/privata che non è inclusa nell'agente di aggiornamento dispositivi o archiviata nel dispositivo. Si tratta della *chiave di* firma.

Quando un aggiornamento viene importato nell'aggiornamento del dispositivo per l'hub IoT e il manifesto dell'aggiornamento viene generato dal servizio, il servizio firma il manifesto usando la chiave di firma e include la chiave di firma stessa, firmata da una chiave radice. Quando il manifesto di aggiornamento viene inviato al dispositivo, l'agente di aggiornamento dispositivi riceve i dati di firma seguenti:

1. Valore della firma stesso.
2. Algoritmo usato per la generazione di #1.
3. Informazioni sulla chiave pubblica della chiave di firma usata per generare #1.
4. Firma della chiave di firma pubblica in #3.
5. ID della chiave pubblica della chiave radice usata per generare #3.
6. Algoritmo utilizzato per la generazione di #4.

L'agente di aggiornamento dispositivi usa le informazioni definite in precedenza per convalidare che la firma della chiave di firma pubblica sia firmata dalla chiave radice. L'agente di aggiornamento dispositivi verifica quindi che la firma del manifesto dell'aggiornamento sia firmata dalla chiave di firma. Se tutte le firme sono corrette, il manifesto di aggiornamento viene considerato attendibile dall'agente di aggiornamento dispositivi. Poiché il manifesto di aggiornamento include gli hash dei file che corrispondono ai file di aggiornamento stessi, i file di aggiornamento possono essere considerati attendibili anche se gli hash corrispondono.

La presenza di chiavi radice e di firma consente a Microsoft di eseguire periodicamente il roll-to-roll della chiave di firma, una procedura consigliata per la sicurezza.

### <a name="json-web-signature-jws"></a>Firma Web JSON (JWS, JSON Web Signature)

L'oggetto viene utilizzato per garantire che le informazioni contenute in non `updateManifestSignature` `updateManifest` siano state manomissionate. Viene `updateManifestSignature` generato usando una firma JSON Web con chiavi JSON Web, consentendo la verifica dell'origine. La firma è una stringa con codifica Base64Url con tre sezioni delimitate da ".".  Fare riferimento ai [metodi helper jws_util.h per](https://github.com/Azure/iot-hub-device-update/tree/main/src/utils/jws_utils) l'analisi e la verifica di chiavi e token JSON.

JSON Web Signature è uno [standard IETF](https://tools.ietf.org/html/rfc7515) proposto ampiamente usato per firmare il contenuto usando strutture di dati basate su JSON. Si tratta di un modo per garantire l'integrità dei dati verificando la firma dei dati. Altre informazioni sono disponibili in JSON Web Signature (JWS) [RFC 7515](https://www.rfc-editor.org/info/rfc7515).

### <a name="json-web-token"></a>token JSON Web

I token JSON Web sono un metodo standard aperto [e di](https://tools.ietf.org/html/rfc7519) settore per rappresentare le attestazioni in modo sicuro tra due parti.

### <a name="root-keys"></a>Chiavi radice

Ogni dispositivo di aggiornamento del dispositivo contiene un set di chiavi radice. Queste chiavi sono la radice di attendibilità per tutte le firme di Aggiornamento dispositivo. Qualsiasi firma deve essere concatenata tramite una di queste chiavi radice per essere considerata legittima.

Il set di chiavi radice cambierà nel tempo perché è appropriato ruotare periodicamente le chiavi di firma per motivi di sicurezza. Di conseguenza, il software dell'agente di aggiornamento dispositivi dovrà aggiornarsi con il set più recente di chiavi radice. 

### <a name="signatures"></a>Firme

Tutte le firme verranno ospitate da una chiave di firma (pubblica) firmata da una delle chiavi radice. La firma identificherà la chiave radice usata per firmare la chiave di firma. 

Un agente di aggiornamento dispositivi deve convalidare le firme convalidando innanzitutto che la firma della chiave di firma (pubblica) sia corretta, valida e firmata da una delle chiavi radice approvate. Dopo la convalida della chiave di firma, la firma stessa può essere convalidata usando la chiave pubblica di firma ora attendibile.

Le chiavi di firma vengono ruotate in base a una cadenza molto più rapida rispetto alle chiavi radice, pertanto si prevede che i messaggi firmati da diverse chiavi di firma diverse siano firmati. 

La revoca di una chiave di firma viene gestita dal servizio Aggiornamento dispositivi, pertanto gli utenti non devono tentare di memorizzare nella cache le chiavi di firma. Usare sempre la chiave di firma che accompagna una firma.

### <a name="receiving-updates"></a>Ricezione di aggiornamenti

Le richieste di aggiornamento ricevute da un agente di aggiornamento dispositivi conterranno un documento del manifesto di aggiornamento firmato. L'agente deve convalidare che la firma della messaggistica unificata sia corretta e intatta. Questa operazione viene eseguita convalidando che la chiave di firma della firma di messaggistica unificata sia stata firmata da una chiave radice appropriata. Al termine, l'agente convalida la firma di messaggistica unificata rispetto alla chiave di firma.

Dopo che la firma di messaggistica unificata è stata convalidata, l'agente di aggiornamento dei dispositivi può considerarla attendibile come "fonte di verità". Tutti gli altri trust di sicurezza derivano da questa origine. 

La messaggistica unificata contiene URL e hash di file del contenuto da scaricare e installare. Dopo che l'agente ha scaricato un file binario di aggiornamento, deve convalidare l'aggiornamento rispetto all'hash del file trovato nella messaggistica unificata. In questo modo viene fornito un modello di attendibilità transitiva per la convalida del download. Non solo garantisce che il contenuto sia intatto (non modificato), ma anche che ciò che è stato scaricato sia effettivamente ciò che era previsto per il download. 

### <a name="securing-the-device"></a>Protezione del dispositivo

È importante assicurarsi che gli asset di sicurezza correlati all'aggiornamento del dispositivo siano protetti correttamente nel dispositivo. Gli asset, ad esempio le chiavi radice, devono essere protetti dalla modifica. Esistono diversi modi per eseguire questa operazione, ad esempio usando dispositivi di sicurezza (TPM, SGX, HSM, altri dispositivi di sicurezza) o persino hard-coding nell'agente di aggiornamento del dispositivo. Quest'ultimo richiede che il codice dell'agente di aggiornamento del dispositivo sia firmato digitalmente e che il supporto dell'integrità del codice del sistema sia abilitato per la protezione da modifiche dannose del codice dell'agente.

Possono essere garantite misure di sicurezza aggiuntive, ad esempio per garantire che la consegna da componente a componente venga eseguita in modo sicuro. Ad esempio, la registrazione di un account isolato specifico per l'esecuzione dei vari componenti. E limitando le comunicazioni basate sulla rete (ad esempio, le chiamate API REST) solo a localhost.

**[Passaggio successivo: Altre informazioni su come Aggiornamento dispositivi usa il controllo degli accessi in base al ruolo di Azure](.\device-update-control-access.md)**