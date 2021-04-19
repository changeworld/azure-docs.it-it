---
title: Cache HPC di Azure di utilizzo
description: Descrive i diversi modelli di utilizzo della cache e come scegliere tra di essi per impostare la memorizzazione nella cache di sola lettura o di lettura/scrittura e controllare altre impostazioni di memorizzazione nella cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 04/08/2021
ms.author: v-erkel
ms.openlocfilehash: 7e1b11fd15cca9b11fc627222318f08d31743336
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719188"
---
# <a name="understand-cache-usage-models"></a>Informazioni su modelli di utilizzo della cache

I modelli di utilizzo della cache consentono di personalizzare il modo in cui il Cache HPC di Azure archivia i file per velocizzare il flusso di lavoro.

## <a name="basic-file-caching-concepts"></a>Concetti di base sulla memorizzazione nella cache dei file

La memorizzazione nella cache dei file Cache HPC di Azure velocizza le richieste del client. Usa queste procedure di base:

* **Memorizzazione nella cache** di Cache HPC di Azure mantiene una copia dei file che i client richiedono al sistema di archiviazione. La volta successiva che un client richiede lo stesso file, cache HPC può fornire la versione nella cache anziché doverla recuperare nuovamente dal sistema di archiviazione back-end.

* **Memorizzazione nella cache** in scrittura: Cache HPC di Azure possibile archiviare una copia di tutti i file modificati inviati dai computer client. Se più client apportano modifiche allo stesso file in un breve periodo di tempo, la cache può raccogliere tutte le modifiche nella cache anziché dover scrivere ogni modifica singolarmente nel sistema di archiviazione back-end.

  Dopo un periodo di tempo specificato senza modifiche, la cache sposta il file nel sistema di archiviazione a lungo termine.

  Se la memorizzazione nella cache in scrittura è disabilitata, la cache non archivia il file modificato e lo scrive immediatamente nel sistema di archiviazione back-end.

* **Ritardo write-back:** per una cache con memorizzazione nella cache in scrittura attivata, il ritardo di write-back è la quantità di tempo di attesa della cache per le modifiche aggiuntive ai file prima di copiare il file nel sistema di archiviazione back-end.

* **Verifica back-end:** l'impostazione di verifica back-end determina la frequenza con cui la cache confronta la copia locale di un file con la versione remota nel sistema di archiviazione back-end. Se la copia back-end è più recente della copia memorizzata nella cache, la cache recupera la copia remota e la archivia per le richieste future.

  L'impostazione di verifica back-end mostra quando la cache *confronta* automaticamente i file con i file di origine nell'archiviazione remota. Tuttavia, è possibile forzare Cache HPC di Azure confrontare i file eseguendo un'operazione di directory che include una richiesta readdirplus. Readdirplus è un'API NFS standard (detta anche lettura estesa) che restituisce i metadati della directory, che fa sì che la cache confronti e aggiornesca i file.

I modelli di utilizzo incorporati Cache HPC di Azure hanno valori diversi per queste impostazioni, in modo da poter scegliere la combinazione migliore per la situazione specifica.

## <a name="choose-the-right-usage-model-for-your-workflow"></a>Scegliere il modello di utilizzo giusto per il flusso di lavoro

È necessario scegliere un modello di utilizzo per ogni destinazione di archiviazione del protocollo NFS in uso. Le destinazioni di archiviazione BLOB di Azure hanno un modello di utilizzo incorporato che non può essere personalizzato.

I modelli di utilizzo della cache HPC consentono di scegliere come bilanciare la risposta rapida con il rischio di ottenere dati non obsoleti. Se si vuole ottimizzare la velocità di lettura dei file, potrebbe non essere necessario verificare se i file nella cache vengono controllati rispetto ai file back-end. D'altra parte, se si vuole assicurarsi che i file siano sempre aggiornati con l'archiviazione remota, scegliere un modello che controlli frequentemente.

Queste sono le opzioni del modello di utilizzo:

* **Operazioni di lettura frequenti: usare** questa opzione se si vuole velocizzare l'accesso in lettura ai file statici o modificati raramente.

  Questa opzione memorizza nella cache le operazioni di lettura del client, ma non le scritture nella cache. Passa immediatamente le operazioni di scrittura all'archiviazione back-end.
  
  I file archiviati nella cache non vengono confrontati automaticamente con i file nel volume di archiviazione NFS. Leggere la descrizione della verifica back-end precedente per informazioni su come confrontarle manualmente.

  Non usare questa opzione se esiste il rischio che un file possa essere modificato direttamente nel sistema di archiviazione senza prima scriverlo nella cache. In questo caso, la versione memorizzata nella cache del file non sarà sincronizzata con il file back-end.

* **Scritture superiori al 15%:** questa opzione consente di velocizzare le prestazioni di lettura e scrittura. Quando si usa questa opzione, tutti i client devono accedere ai file tramite il Cache HPC di Azure anziché montare direttamente l'archiviazione back-end. I file memorizzati nella cache avranno modifiche recenti che non sono ancora state copiate nel back-end.

  In questo modello di utilizzo, i file nella cache vengono controllati solo rispetto ai file nell'archiviazione back-end ogni otto ore. Si presuppone che la versione del file memorizzata nella cache sia più aggiornata. Un file modificato nella cache viene scritto nel sistema di archiviazione back-end dopo che è stato nella cache per 20 minuti<!-- an hour --> senza modifiche aggiuntive.

* I client scrivono nella destinazione **NFS ignorando** la cache: scegliere questa opzione se i client nel flusso di lavoro scrivono i dati direttamente nel sistema di archiviazione senza prima scrivere nella cache o se si vuole ottimizzare la coerenza dei dati. I file che i client richiedono vengono memorizzati nella cache (operazioni di lettura), ma tutte le modifiche apportate a tali file dal client (scritture) non vengono memorizzate nella cache. Vengono passati direttamente al sistema di archiviazione back-end.

  Con questo modello di utilizzo, i file nella cache vengono spesso controllati rispetto alle versioni back-end per gli aggiornamenti, ogni 30 secondi. Questa verifica consente di modificare i file all'esterno della cache mantenendo la coerenza dei dati.

  > [!TIP]
  > Questi primi tre modelli di utilizzo di base possono essere usati per gestire la maggior parte dei Cache HPC di Azure di lavoro. Le opzioni seguenti sono per scenari meno comuni.

* Scritture superiori al **15%,** controllo delle modifiche nel server di backup ogni 30 secondi e maggiore del **15%** delle scritture, controllo delle modifiche nel server di backup ogni 60 secondi: queste opzioni sono progettate per i flussi di lavoro in cui si vuole velocizzare le operazioni di lettura e scrittura, ma esiste la possibilità che un altro utente scrivo direttamente nel sistema di archiviazione back-end. Ad esempio, se più set di client lavorano sugli stessi file da posizioni diverse, questi modelli di utilizzo potrebbero avere senso bilanciare la necessità di un accesso rapido ai file con una tolleranza bassa per il contenuto non obsoleto dall'origine.

* Scritture superiori al **15%, write back-back** nel server ogni 30 secondi: questa opzione è progettata per lo scenario in cui più client modificano attivamente gli stessi file o se alcuni client accedono direttamente all'archiviazione back-end anziché montare la cache.

  Le frequenti scritture back-end influiscono sulle prestazioni della cache, pertanto è consigliabile usare il modello di utilizzo delle scritture maggiore del **15%** in caso di basso rischio di conflitto tra file, ad esempio se si è a sapere che client diversi funzionano in aree diverse dello stesso set di file.

* Lettura pesante, controllo del server di backup ogni **3** ore: questa opzione assegna la priorità alle operazioni di lettura veloci sul  lato client, ma aggiorna regolarmente i file memorizzati nella cache dal sistema di archiviazione back-end, a differenza del modello di utilizzo Operazioni di lettura con utilizzo elevato e poco frequenti.

Questa tabella riepiloga le differenze del modello di utilizzo:

[!INCLUDE [usage-models-table.md](includes/usage-models-table.md)]

Per domande sul modello di utilizzo migliore per il flusso di lavoro Cache HPC di Azure, parlare con il rappresentante di Azure o aprire una richiesta di assistenza.

## <a name="know-when-to-remount-clients-for-nlm"></a>Sapere quando rimontare i client per NLM

In alcune situazioni potrebbe essere necessario rimontare i client se si modifica il modello di utilizzo di una destinazione di archiviazione. Questa operazione è necessaria a causa del modo in cui i diversi modelli di utilizzo gestiscono le richieste NLM (Network Lock Manager).

La cache HPC si trova tra i client e il sistema di archiviazione back-end. In genere la cache passa le richieste NLM al sistema di archiviazione back-end, ma in alcune situazioni la cache stessa riconosce la richiesta NLM e restituisce un valore al client. In Cache HPC di Azure, ciò si verifica solo quando si usa il modello di utilizzo Read **heavy, infrequent writes** (o con una destinazione di archiviazione BLOB standard, che non ha modelli di utilizzo configurabili).

Esiste un piccolo rischio di conflitto di file se si cambia tra il modello di utilizzo Read **heavy, infrequent writes** usage e un modello di utilizzo diverso. Non è possibile trasferire lo stato NLM corrente dalla cache al sistema di archiviazione o viceversa. Lo stato di blocco del client non è quindi accurato.

Rimontare i client per assicurarsi di avere uno stato NLM accurato con il nuovo gestore blocchi.

Se i client inviano una richiesta NLM quando il modello di utilizzo o l'archiviazione back-end non la supporta, riceveranno un errore.

### <a name="disable-nlm-at-client-mount-time"></a>Disabilitare NLM in fase di montaggio client

Non è sempre facile sapere se i sistemi client invieranno o meno richieste NLM.

È possibile disabilitare NLM quando i client montano il cluster usando l'opzione ``-o nolock`` nel ``mount`` comando .

Il comportamento esatto dell'opzione dipende dal sistema operativo client, quindi controllare la documentazione del ``nolock`` montaggio (man 5 nfs) per il sistema operativo client. Nella maggior parte dei casi, il blocco viene spostato localmente nel client. Prestare attenzione se l'applicazione blocca i file tra più client.

> [!NOTE]
> ADLS-NFS non supporta NLM. È consigliabile disabilitare NLM con l'opzione di montaggio precedente quando si usa una destinazione di archiviazione ADLS-NFS.

## <a name="next-steps"></a>Passaggi successivi

* [Aggiungere destinazioni di](hpc-cache-add-storage.md) archiviazione al Cache HPC di Azure
