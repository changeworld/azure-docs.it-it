---
title: Guida alla limitazione delle richieste per Azure Key Vault
description: La limitazione delle richieste di Key Vault consente di limitare il numero di chiamate simultanee per evitare l'uso eccessivo delle risorse.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: 7a215b53f673a7414f1b3662f519de5c26faaa9d
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749535"
---
# <a name="azure-key-vault-throttling-guidance"></a>Guida alla limitazione delle richieste per Azure Key Vault

La limitazione delle richieste è un processo che consente di limitare il numero di chiamate simultanee al servizio di Azure per prevenire l'uso eccessivo delle risorse. Azure Key Vault è progettato per gestire un volume di richieste elevato. In caso di un numero eccessivamente elevato di richieste, la limitazione delle richieste del client aiuta a mantenere le prestazioni ottimali e l'affidabilità del servizio di Azure Key Vault.

I limiti delle richieste variano in base allo scenario. Ad esempio, se si esegue un volume elevato di operazioni di scrittura, la possibilità di limitare le richieste è maggiore rispetto a quando si eseguono solo operazioni di lettura.

## <a name="how-does-key-vault-handle-its-limits"></a>Gestione dei limiti da parte di Key Vault

I limiti dei Key Vault impediscono l'uso improprio delle risorse e garantiscono la qualità del servizio per tutti Key Vault client. Quando viene superata una soglia del servizio, Key Vault limita eventuali altre richieste da tale client per un periodo di tempo, restituisce il codice di stato HTTP 429 (troppe richieste) e la richiesta ha esito negativo. Le richieste non riuscite che restituiscono un valore 429 non vengono conteggiate verso i limiti di limitazione monitorati Key Vault. 

Key Vault è stato originariamente progettato per essere usato per archiviare e recuperare i segreti in fase di distribuzione.  Il mondo si è evoluto e Key Vault viene usato in fase di esecuzione per archiviare e recuperare i segreti e spesso app e servizi vogliono usare Key Vault come un database.  I limiti correnti non supportano velocità effettiva elevate.

Key Vault creato in origine con i limiti specificati in Azure Key Vault [del servizio](service-limits.md).  Per ottimizzare Key Vault velocità effettiva, ecco alcune linee guida/procedure consigliate per ottimizzare la velocità effettiva:
1. Assicurarsi di avere la limitazione in atto.  Il client deve rispettare i criteri di back-off esponenziali per i 429 e assicurarsi di eseguire tentativi in base alle indicazioni riportate di seguito.
1. Dividere il Key Vault traffico tra più insiemi di credenziali e aree diverse.   Usare un insieme di credenziali separato per ogni dominio di sicurezza/disponibilità.   Se si hanno cinque app, ognuna in due aree, è consigliabile usare 10 insiemi di credenziali contenenti ognuno i segreti univoci per app e area.  Un limite a livello di sottoscrizione per tutti i tipi di transazione è cinque volte superiore al limite dei singoli insiemi di credenziali delle chiavi. Ad esempio, le transazioni diverse da HSM hanno un limite di 5.000 transazioni ogni 10 secondi per sottoscrizione. Prendere in considerazione la memorizzazione nella cache del segreto all'interno del servizio o dell'app per ridurre il numero di richieste al secondo direttamente nell'insieme di credenziali delle chiavi e/o gestire il traffico basato su burst.  È anche possibile dividere il traffico tra aree diverse per ridurre al minimo la latenza e usare una sottoscrizione o un insieme di credenziali diverso.  Non inviare più del limite di sottoscrizione al servizio Key Vault in una singola area di Azure.
1. Memorizzare nella cache i segreti recuperati dal Azure Key Vault in memoria e riutilizzarli quando possibile.  Ri read from Azure Key Vault solo quando la copia memorizzata nella cache smette di funzionare (ad esempio perché è stata ruotata nell'origine). 
1. Key Vault è progettato per i segreti dei propri servizi.   Se si archiviano i segreti dei clienti (in particolare per scenari di archiviazione delle chiavi con velocità effettiva elevata), è consigliabile inserire le chiavi in un database o in un account di archiviazione con crittografia e archiviare solo la chiave master in Azure Key Vault.
1. Le operazioni di crittografia, wrapping e verifica della chiave pubblica possono essere eseguite senza accesso a Key Vault, il che non solo riduce il rischio di limitazione delle richieste, ma migliora anche l'affidabilità (purché il materiale della chiave pubblica venga memorizzato correttamente nella cache).
1. Se si usa Key Vault per archiviare le credenziali per un servizio, verificare se il servizio supporta l Autenticazione di Azure AD per l'autenticazione diretta. In questo modo si riduce il carico Key Vault, si migliora l'affidabilità e si semplifica il codice perché Key Vault ora è possibile usare il token Azure AD.  Molti servizi sono passati all'uso Azure AD Auth.  Vedere l'elenco corrente in [Servizi che supportano le identità gestite per le risorse di Azure.](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)
1. Valutare la possibilità di sfalsare il carico o la distribuzione in un periodo di tempo più lungo per rimanere al di sotto dei limiti di RPS correnti.
1. Se l'app è costituita da più nodi che devono leggere gli stessi segreti, è consigliabile usare un modello di fan-out, in cui un'entità legge il segreto da Key Vault e fa riferimento a tutti i nodi.   Memorizzare nella cache i segreti recuperati solo in memoria.
Se si nota che il codice precedente non soddisfa ancora le proprie esigenze, compilare la tabella seguente e contattare Microsoft per determinare quale capacità aggiuntiva può essere aggiunta (esempio riportato di seguito solo a scopo illustrativo).

| Nome dell'insieme di credenziali | Area dell'insieme di credenziali | Tipo di oggetto (segreto, chiave o certificato) | Operazioni* | Tipo chiave | Lunghezza chiave o curva | Chiave HSM?| RPS con stato stabile necessario | Picco di richieste al secondo necessario |
|--|--|--|--|--|--|--|--|--|
| https://mykeyvault.vault.azure.net/ | | Chiave | Sign | EC | P-256 | No | 200 | 1000 |

\* Per un elenco completo dei valori possibili, vedere Azure Key Vault [operazioni](/rest/api/keyvault/key-operations).

Se viene approvata una capacità aggiuntiva, tenere presente quanto segue in seguito all'aumento della capacità:
1. Modifiche al modello di coerenza dei dati. Dopo che un insieme di credenziali è stato consentito nell'elenco con capacità di velocità effettiva aggiuntiva, la garanzia di coerenza dei dati del servizio Key Vault cambia (necessaria per soddisfare un volume RPS superiore perché il servizio Archiviazione di Azure sottostante non è in grado di mantenere il passo).  In pratica
  1. **Senza consentire l'inserzione:** Key Vault servizio rifletterà i risultati di un'operazione di scrittura (ad esempio. SecretSet, CreateKey) immediatamente nelle chiamate successive (ad esempio SecretGet, KeySign).
  1. **Con allow listing**: il Key Vault rifletterà i risultati di un'operazione di scrittura ,ad esempio SecretSet, CreateKey) entro 60 secondi nelle chiamate successive (ad esempio SecretGet, KeySign).
1. Il codice client deve rispettare i criteri di back-off per 429 tentativi. Il codice client che chiama il Key Vault non deve ripetere immediatamente Key Vault richieste quando riceve un codice di risposta 429.  Le Azure Key Vault di limitazione delle richieste pubblicate qui consigliano di applicare il backoff esponenziale quando si riceve un codice di risposta HTTP 429.

Nel caso l'utente abbia delle esigenze aziendali per cui sono necessarie delle limitazioni maggiori, contattare Microsoft.

## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>Come limitare le richieste per l'app in risposta ai limiti del servizio

Di seguito sono **riportate le procedure consigliate** da implementare quando il servizio è limitato:
- Ridurre il numero di operazioni per ogni richiesta.
- Ridurre la frequenza delle richieste.
- Evitare tentativi immediati. 
    - Tutte le richieste si accumulano per i limiti di consumo.

Quando si implementa la gestione degli errori dell'app, usare il codice di errore HTTP 429 per rilevare la necessità di limitazione delle richieste lato client. Se la richiesta ha di nuovo esito negativo con un codice di errore HTTP 429, vuol dire che ci sono ancora limiti nel servizio di Azure. Continuare a usare il metodo di limitazione delle richieste lato client consigliato, provando a inviare nuovamente la richiesta fino a quando non ha esito positivo.

Di seguito è riportato il codice che implementa il backoff esponenziale. 
```
SecretClientOptions options = new SecretClientOptions()
    {
        Retry =
        {
            Delay= TimeSpan.FromSeconds(2),
            MaxDelay = TimeSpan.FromSeconds(16),
            MaxRetries = 5,
            Mode = RetryMode.Exponential
         }
    };
    var client = new SecretClient(new Uri("https://keyVaultName.vault.azure.net"), new DefaultAzureCredential(),options);
                                 
    //Retrieve Secret
    secret = client.GetSecret(secretName);
```


L'uso di questo codice in un'applicazione C# client è semplice. 

### <a name="recommended-client-side-throttling-method"></a>Metodo consigliato per la limitazione delle richieste lato client

Quando si genera il codice di errore HTTP 429, iniziare a limitare le richieste del client con un approccio di backoff esponenziale:

1. Attendere 1 secondo e ripetere la richiesta
2. Se viene ancora limitata, attendere 2 secondi e ripetere la richiesta
3. Se viene ancora limitata, attendere 4 secondi e ripetere la richiesta
4. Se viene ancora limitata, attendere 8 secondi e ripetere la richiesta
5. Se viene ancora limitata, attendere 16 secondi e ripetere la richiesta

A questo punto, il codice di risposta HTTP 429 dovrebbe non essere più visualizzato.

## <a name="see-also"></a>Vedi anche

Per un approfondimento sulla limitazione delle richieste nel cloud di Microsoft, vedere [Throttling Pattern](/azure/architecture/patterns/throttling) (Modello di limitazione delle richieste).
