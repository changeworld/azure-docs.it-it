---
title: Modelli di utilizzo della cache HPC di Azure
description: Vengono descritti i diversi modelli di utilizzo della cache e viene illustrato come scegliere tra di essi per impostare la memorizzazione nella cache di sola lettura o di lettura/scrittura e controllare altre impostazioni di memorizzazione nella cache.
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/15/2021
ms.author: v-erkel
ms.openlocfilehash: 3ad252520ca0cf7acdb3c84ef1da87c8076f3172
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104775715"
---
# <a name="understand-cache-usage-models"></a>Informazioni sui modelli di utilizzo della cache

I modelli di utilizzo della cache consentono di personalizzare la modalità di archiviazione dei file nella cache HPC di Azure per velocizzare il flusso di lavoro.

## <a name="basic-file-caching-concepts"></a>Concetti di base sulla memorizzazione nella cache di file

La memorizzazione nella cache di file è il modo in cui cache HPC di Azure accelera le richieste del client. Usa queste procedure di base:

* **Caching di lettura** : la cache HPC di Azure conserva una copia dei file richiesti dai client dal sistema di archiviazione. Alla successiva richiesta da parte di un client dello stesso file, la cache HPC può fornire la versione nella propria cache anziché recuperarla nuovamente dal sistema di archiviazione back-end.

* **Caching in scrittura** : facoltativamente, la cache HPC di Azure può archiviare una copia di tutti i file modificati inviati dai computer client. Se più client apportano modifiche allo stesso file in un breve periodo di tempo, la cache può raccogliere tutte le modifiche nella cache anziché dover scrivere singolarmente ogni modifica nel sistema di archiviazione back-end.

  Dopo un periodo di tempo specificato senza alcuna modifica, la cache sposta il file nel sistema di archiviazione a lungo termine.

  Se la memorizzazione nella cache in scrittura è disabilitata, la cache non archivia il file modificato e lo scrive immediatamente nel sistema di archiviazione back-end.

* **Ritardo writeback** : per una cache con memorizzazione nella cache in scrittura attivata, il ritardo write-back corrisponde alla quantità di tempo durante il quale la cache resta in attesa di ulteriori modifiche ai file prima di copiare il file nel sistema di archiviazione back-end.

* **Verifica back-end** : l'impostazione di verifica del back-end determina la frequenza con cui la cache confronta la copia locale di un file con la versione remota sul sistema di archiviazione back-end. Se la copia back-end è più recente della copia memorizzata nella cache, la cache recupera la copia remota e la archivia per richieste future.

  L'impostazione di verifica back-end indica quando la cache confronta *automaticamente* i propri file con i file di origine nell'archiviazione remota. Tuttavia, è possibile forzare la cache HPC di Azure per confrontare i file eseguendo un'operazione di directory che include una richiesta READDIRPLUS. READDIRPLUS è un'API NFS standard (detta anche lettura estesa) che restituisce i metadati della directory, che determina il confronto e l'aggiornamento dei file nella cache.

I modelli di utilizzo incorporati nella cache HPC di Azure hanno valori diversi per queste impostazioni, in modo che sia possibile scegliere la combinazione migliore per la situazione.

## <a name="choose-the-right-usage-model-for-your-workflow"></a>Scegliere il modello di utilizzo corretto per il flusso di lavoro

È necessario scegliere un modello di utilizzo per ogni destinazione di archiviazione montata da NFS in uso. Le destinazioni di archiviazione BLOB di Azure hanno un modello di utilizzo incorporato che non può essere personalizzato.

I modelli di utilizzo della cache HPC consentono di scegliere come bilanciare la risposta rapida con il rischio di recuperare i dati obsoleti. Se si desidera ottimizzare la velocità per la lettura dei file, è possibile che non si sia interessati a verificare se i file nella cache vengono confrontati con i file back-end. D'altra parte, se si desidera assicurarsi che i file siano sempre aggiornati con l'archiviazione remota, scegliere un modello che controlli di frequente.

Queste sono le opzioni del modello di utilizzo:

* **Read Heavy, scritture rare** : usare questa opzione se si vuole velocizzare l'accesso in lettura ai file statici o modificati raramente.

  Questa opzione consente di memorizzare nella cache le letture client ma non le Scritture. Passa immediatamente le scritture all'archiviazione back-end.
  
  I file archiviati nella cache non vengono confrontati automaticamente con i file nel volume di archiviazione NFS. (Leggere la descrizione della verifica del back-end precedente per informazioni su come confrontarli manualmente).

  Non usare questa opzione se esiste il rischio che un file venga modificato direttamente nel sistema di archiviazione senza prima scriverlo nella cache. In tal caso, la versione memorizzata nella cache del file non sarà sincronizzata con il file back-end.

* **Scritture superiori al 15%** : questa opzione consente di velocizzare le prestazioni di lettura e scrittura. Quando si usa questa opzione, tutti i client devono accedere ai file tramite la cache HPC di Azure anziché montare direttamente l'archiviazione back-end. I file memorizzati nella cache avranno modifiche recenti che non sono ancora state copiate nel back-end.

  In questo modello di utilizzo, i file nella cache vengono verificati solo in base ai file nell'archiviazione back-end ogni otto ore. Si presuppone che la versione memorizzata nella cache del file sia più aggiornata. Un file modificato nella cache viene scritto nel sistema di archiviazione back-end dopo che è stato inserito nella cache per 20 minuti<!-- an hour --> senza ulteriori modifiche.

* I **client scrivono nella destinazione NFS, ignorando la cache** . scegliere questa opzione se i client nel flusso di lavoro scrivono i dati direttamente nel sistema di archiviazione senza prima scrivere nella cache o se si vuole ottimizzare la coerenza dei dati. I file richiesti dai client vengono memorizzati nella cache (letture), ma tutte le modifiche apportate a tali file dal client (scritture) non vengono memorizzate nella cache. Vengono passati direttamente al sistema di archiviazione back-end.

  Con questo modello di utilizzo, i file nella cache vengono spesso controllati rispetto alle versioni back-end per gli aggiornamenti, ogni 30 secondi. Questa verifica consente di modificare i file all'esterno della cache mantenendo la coerenza dei dati.

  > [!TIP]
  > I primi tre modelli di utilizzo di base possono essere usati per gestire la maggior parte dei flussi di lavoro della cache HPC di Azure. Le opzioni successive sono per gli scenari meno comuni.

* **Più del 15% di Scritture, il controllo delle modifiche del server di supporto ogni 30 secondi** e **più del 15%, il controllo delle modifiche del server di backup ogni 60 secondi** . queste opzioni sono progettate per i flussi di lavoro in cui si desidera velocizzare le operazioni di lettura e scrittura, ma è possibile che un altro utente scriva direttamente nel sistema di archiviazione back-end. Se, ad esempio, più set di client lavorano sugli stessi file di posizioni diverse, questi modelli di utilizzo potrebbero avere senso bilanciare la necessità di accesso rapido ai file con tolleranza bassa per il contenuto non aggiornato dall'origine.

* **Scritture maggiori del 15%, writeback sul server ogni 30 secondi** : questa opzione è progettata per lo scenario in cui più client stanno modificando attivamente gli stessi file o se alcuni client accedono direttamente all'archiviazione back-end anziché montare la cache.

  Le Scritture back-end frequenti influiscono sulle prestazioni della cache, pertanto è consigliabile utilizzare il modello di utilizzo **Scritture maggiore del 15%** se è presente un rischio basso di conflitti di file, ad esempio se si è certi che client diversi funzionino in aree diverse dello stesso set di file.

* Leggere con grande frequenza **, controllare il server di backup ogni 3 ore** . questa opzione consente di definire la priorità delle letture rapide sul lato client, ma anche di aggiornare regolarmente i file memorizzati nella cache dal sistema di archiviazione back-end, a differenza del modello di utilizzo di **Scritture pesanti in lettura** .

In questa tabella vengono riepilogate le differenze del modello di utilizzo:

[!INCLUDE [usage-models-table.md](includes/usage-models-table.md)]

Per domande sul modello di utilizzo ottimale per il flusso di lavoro della cache HPC di Azure, contattare il rappresentante Azure o aprire una richiesta di supporto per assistenza.

## <a name="next-steps"></a>Passaggi successivi

* [Aggiungere destinazioni di archiviazione](hpc-cache-add-storage.md) alla cache HPC di Azure
