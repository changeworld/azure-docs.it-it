---
title: Sicurezza per l'aggiornamento dei dispositivi per l'hub di Azure Microsoft Docs
description: Informazioni su come l'aggiornamento dei dispositivi per l'hub Internet garantisce che i dispositivi vengano aggiornati in modo sicuro.
author: lichris
ms.author: lichris
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub
ms.openlocfilehash: 86b2dbe6a28d1440f93788eb40e133d9b62d3f0c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "102489430"
---
# <a name="device-update-security-model"></a>Modello di sicurezza per l'aggiornamento del dispositivo

L'aggiornamento del dispositivo per l'hub Internet offre un metodo sicuro per distribuire gli aggiornamenti per il firmware, le immagini e le applicazioni del dispositivo nei dispositivi Internet delle cose. Il flusso di lavoro fornisce un canale sicuro end-to-end con un modello di catena di custodia completo che un dispositivo può usare per dimostrare che un aggiornamento è attendibile, non modificato e intenzionale.

Ogni passaggio nel flusso di lavoro di aggiornamento del dispositivo è protetto tramite varie funzionalità e processi di sicurezza per garantire che ogni passaggio della pipeline esegua una consegna protetta a quella successiva. Il client di aggiornamento del dispositivo identifica e gestisce correttamente le richieste di aggiornamento illegittime. Il client controlla anche ogni download per assicurarsi che il contenuto sia attendibile, non modificato e intenzionale.

## <a name="for-solution-operators"></a>Per gli operatori di soluzione

Poiché gli operatori della soluzione importano gli aggiornamenti nell'istanza di aggiornamento del dispositivo, il servizio carica e controlla i file binari di aggiornamento per assicurarsi che non siano stati modificati o scambiati da un utente malintenzionato. Una volta verificata, il servizio di aggiornamento del dispositivo genera un [manifesto di aggiornamento](./update-manifest.md) interno con hash di file dal manifesto di importazione e da altri metadati. Questo manifesto di aggiornamento viene quindi firmato dal servizio di aggiornamento del dispositivo.

Quando l'operatore della soluzione richiede l'aggiornamento di un dispositivo, viene inviato un messaggio firmato sul canale dell'hub Internet delle cose protette al dispositivo. La firma della richiesta viene convalidata dall'agente di aggiornamento dispositivo del dispositivo come autentico. 

Qualsiasi download binario risultante viene protetto tramite la convalida della firma del manifesto dell'aggiornamento. Il manifesto di aggiornamento contiene gli hash dei file binari, quindi, una volta che il manifesto è considerato attendibile, l'agente di aggiornamento del dispositivo considera attendibili gli hash e li associa ai file binari. Una volta che il file binario di aggiornamento è stato scaricato e verificato, viene quindi passato al programma di installazione nel dispositivo.

## <a name="for-device-builders"></a>Per i generatori di dispositivi

Per assicurarsi che il servizio di aggiornamento del dispositivo si Riduci a dispositivi semplici a prestazioni ridotte, il modello di sicurezza utilizza chiavi asimmetriche non elaborate e firme non elaborate. Usano formati basati su JSON, ad esempio i token Web JSON & le chiavi Web JSON.

### <a name="securing-update-content-via-the-update-manifest"></a>Protezione del contenuto degli aggiornamenti tramite il manifesto di aggiornamento

Il manifesto di aggiornamento viene convalidato utilizzando due firme. Le firme vengono create usando una struttura composta da chiavi di *firma* e chiavi *radice* .

L'agente di aggiornamento del dispositivo ha chiavi pubbliche incorporate che vengono usate per tutti i dispositivi compatibili con l'aggiornamento dei dispositivi. Si tratta delle chiavi *radice* . Le chiavi private corrispondenti sono controllate da Microsoft.

Microsoft genera anche una coppia di chiavi pubblica/privata non inclusa nell'agente di aggiornamento del dispositivo o archiviata nel dispositivo. Si tratta della chiave di *firma* .

Quando un aggiornamento viene importato nell'aggiornamento del dispositivo per l'hub Internet e il manifesto dell'aggiornamento viene generato dal servizio, il servizio firma il manifesto usando la chiave di firma e include la chiave di firma stessa, che è firmata da una chiave radice. Quando il manifesto dell'aggiornamento viene inviato al dispositivo, l'agente di aggiornamento del dispositivo riceve i dati della firma seguenti:

1. Valore della firma.
2. Algoritmo utilizzato per generare #1.
3. Informazioni sulla chiave pubblica della chiave di firma utilizzata per generare #1.
4. Firma della chiave di firma pubblica in #3.
5. ID della chiave pubblica della chiave radice utilizzata per generare #3.
6. Algoritmo utilizzato per generare #4.

L'agente di aggiornamento del dispositivo usa le informazioni definite in precedenza per convalidare che la firma della chiave di firma pubblica è firmata dalla chiave radice. L'agente di aggiornamento del dispositivo convalida quindi che la firma del manifesto di aggiornamento è firmata dalla chiave di firma. Se tutte le firme sono corrette, il manifesto dell'aggiornamento è considerato attendibile dall'agente di aggiornamento del dispositivo. Poiché il manifesto dell'aggiornamento include gli hash di file che corrispondono ai file di aggiornamento, i file di aggiornamento possono essere considerati attendibili anche se gli hash corrispondono.

La presenza di chiavi radice e firma consente a Microsoft di eseguire periodicamente il Rolling della chiave di firma, una procedura di sicurezza consigliata.

### <a name="json-web-signature-jws"></a>Firma Web JSON (JWS, JSON Web Signature)

`updateManifestSignature`Viene utilizzato per garantire che le informazioni contenute all'interno di `updateManifest` non siano state manomesse. `updateManifestSignature`Viene generato utilizzando una firma Web JSON con chiavi Web JSON, consentendo la verifica dell'origine. La firma è una stringa con codifica Base64Url con tre sezioni delineate da ".".  Fare riferimento ai [metodi helper jws_util. h](https://github.com/Azure/iot-hub-device-update/tree/main/src/utils/jws_utils) per l'analisi e la verifica di chiavi e token JSON.

La firma Web JSON è uno [standard IETF proposto](https://tools.ietf.org/html/rfc7515) ampiamente usato per la firma di contenuto con strutture di dati basate su JSON. Si tratta di un modo per garantire l'integrità dei dati verificando la firma dei dati. Per ulteriori informazioni, vedere la [RFC 7515](https://www.rfc-editor.org/info/rfc7515)relativa alla firma Web JSON (JWS).

### <a name="json-web-token"></a>Token Web JSON

I token Web JSON sono un metodo Open e [standard](https://tools.ietf.org/html/rfc7519) di settore per la rappresentazione sicura delle attestazioni tra due parti.

### <a name="root-keys"></a>Chiavi radice

Ogni dispositivo di aggiornamento del dispositivo contiene un set di chiavi radice. Queste chiavi sono la radice di attendibilità per tutte le firme degli aggiornamenti dei dispositivi. Per essere considerati legittimi, è necessario concatenare qualsiasi firma tramite una di queste chiavi radice.

Il set di chiavi radice cambierà nel tempo, in quanto è adatto per ruotare periodicamente le chiavi di firma per motivi di sicurezza. Di conseguenza, il software dell'agente di aggiornamento del dispositivo dovrà aggiornarsi con l'ultimo set di chiavi radice. 

### <a name="signatures"></a>Firme

Tutte le firme verranno gestite da una chiave di firma (pubblica) firmata da una delle chiavi radice. La firma identificherà la chiave radice usata per firmare la chiave di firma. 

Un agente di aggiornamento dispositivi deve convalidare le firme verificando innanzitutto che la firma della chiave di firma (pubblica) sia corretta, valida e firmata da una delle chiavi radice approvate. Una volta convalidata la chiave di firma, la firma può essere convalidata utilizzando la chiave pubblica di firma attendibile.

Le chiavi di firma vengono ruotate in base a una cadenza molto più rapida rispetto alle chiavi radice, quindi si prevede che i messaggi siano firmati da diverse chiavi di firma diverse. 

La revoca di una chiave di firma viene gestita dal servizio di aggiornamento del dispositivo, quindi gli utenti non devono tentare di memorizzare nella cache le chiavi di firma. Usare sempre la chiave di firma associata a una firma.

### <a name="receiving-updates"></a>Ricezione degli aggiornamenti

Le richieste di aggiornamento ricevute da un agente di aggiornamento del dispositivo conterranno un documento di aggiornamento firmato (UM). L'agente deve verificare che la firma dell'oggetto UM sia corretta e intatta. Questa operazione viene eseguita convalidando che la chiave di firma della firma di messaggistica unificata è stata firmata da una chiave radice corretta. Al termine, l'agente convalida la firma di messaggistica unificata rispetto alla chiave di firma.

Una volta convalidata la firma UM, l'agente di aggiornamento del dispositivo può considerarlo attendibile come "origine di verità". Tutti gli altri trust di sicurezza provengono da questa origine. 

La messaggistica unificata contiene URL e hash di file del contenuto da scaricare e installare. Una volta che l'agente ha scaricato un file binario di aggiornamento, deve convalidare l'aggiornamento rispetto all'hash del file trovato nell'UM. Fornisce un modello di trust transitivo per la convalida dei download. Non solo garantisce che il contenuto sia intatto (non modificato), ma conferma anche che ciò che è stato scaricato era effettivamente quello che era previsto per il download. 

### <a name="securing-the-device"></a>Sicurezza del dispositivo

È importante assicurarsi che gli asset di sicurezza correlati all'aggiornamento del dispositivo siano protetti e protetti correttamente nel dispositivo. Gli asset come le chiavi radice devono essere protetti da modifiche. Esistono diversi modi per eseguire questa operazione, ad esempio l'uso di dispositivi di sicurezza (TPM, SGX, HSM, altri dispositivi di sicurezza) o anche la loro codifica a livello di codice nell'agente di aggiornamento del dispositivo. Quest'ultimo richiede che il codice dell'agente di aggiornamento del dispositivo sia firmato digitalmente e che il supporto di integrità del codice del sistema sia abilitato per la protezione da modifiche dannose del codice dell'agente.

È possibile che siano garantite misure di sicurezza aggiuntive, ad esempio garantire che la consegna da componente a componente venga eseguita in modo sicuro. Ad esempio, la registrazione di un account isolato specifico per eseguire i vari componenti. E limitano le comunicazioni basate sulla rete, ad esempio le chiamate all'API REST, solo a localhost.

**[Passaggio successivo: altre informazioni sull'uso del controllo degli accessi in base al ruolo di Azure](.\device-update-control-access.md)**