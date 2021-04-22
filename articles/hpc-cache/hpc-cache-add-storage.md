---
title: Aggiungere spazio di archiviazione a un Cache HPC di Azure
description: Come definire le destinazioni di archiviazione in modo che il Cache HPC di Azure possa usare il sistema NFS locale o i contenitori BLOB di Azure per l'archiviazione di file a lungo termine
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/15/2021
ms.author: v-erkel
ms.openlocfilehash: 708ad8bbfec9e3fd0176c53c111b5b5b25a5318f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862238"
---
# <a name="add-storage-targets"></a>Aggiungere destinazioni di archiviazione

*Le destinazioni di* archiviazione sono risorse di archiviazione back-end per i file a cui si accede tramite un Cache HPC di Azure. È possibile aggiungere l'archiviazione NFS (ad esempio un sistema hardware locale) o archiviare i dati nel BLOB di Azure.

È possibile definire fino a 20 destinazioni di archiviazione diverse per una cache. La cache presenta tutte le destinazioni di archiviazione in un unico spazio dei nomi aggregato.

I percorsi dello spazio dei nomi vengono configurati separatamente dopo l'aggiunta delle destinazioni di archiviazione. In generale, una destinazione di archiviazione NFS può avere fino a dieci percorsi dello spazio dei nomi o più per alcune configurazioni di grandi dimensioni. Per informazioni [dettagliate, vedere Percorsi](add-namespace-paths.md#nfs-namespace-paths) dello spazio dei nomi NFS.

Tenere presente che le esportazioni di archiviazione devono essere accessibili dalla rete virtuale della cache. Per l'archiviazione hardware locale, potrebbe essere necessario configurare un server DNS in grado di risolvere i nomi host per l'accesso all'archiviazione NFS. Per altre informazioni, vedere [Accesso DNS.](hpc-cache-prerequisites.md#dns-access)

Aggiungere le destinazioni di archiviazione dopo aver creato la cache. Seguire questa procedura:

1. [Creare la cache](hpc-cache-create.md)
1. Definire una destinazione di archiviazione (informazioni in questo articolo)
1. [Creare i percorsi lato client (per](add-namespace-paths.md) lo spazio [dei nomi aggregato](hpc-cache-namespace.md))

La procedura per aggiungere una destinazione di archiviazione è leggermente diversa a seconda del tipo di archiviazione che usa. Di seguito sono riportati i dettagli per ognuno di essi.

Fare clic sull'immagine seguente per guardare una [dimostrazione video](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/) della creazione di una cache e dell'aggiunta di una destinazione di archiviazione dal portale di Azure.

[![anteprima video: Cache HPC di Azure: Configurazione (fare clic per visitare la pagina del video)](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="add-a-new-azure-blob-storage-target"></a>Aggiungere una nuova destinazione di archiviazione BLOB di Azure

Una nuova destinazione di archiviazione BLOB richiede un contenitore BLOB vuoto o un contenitore popolato con dati nel Cache HPC di Azure cloud file system formato. Per altre informazioni sul precaricamento di un contenitore BLOB, vedere [Spostare i dati nell'archivio BLOB di Azure.](hpc-cache-ingest.md)

La portale di Azure **Aggiungi destinazione di archiviazione** include l'opzione per creare un nuovo contenitore BLOB subito prima di aggiungerlo.

> [!NOTE]
> Per l'archiviazione BLOB montata su NFS, usare il tipo di destinazione di archiviazione [ADLS-NFS.](#)

### <a name="portal"></a>[Portale](#tab/azure-portal)

Dal riquadro portale di Azure aprire l'istanza della cache e fare clic su **Destinazioni di archiviazione** sulla barra laterale sinistra.

![Screenshot delle impostazioni > destinazione di archiviazione, con due destinazioni di archiviazione esistenti in una tabella e un'evidenziazione intorno al pulsante + aggiungi destinazione di archiviazione sopra la tabella](media/add-storage-target-button.png)

La **pagina Destinazioni di** archiviazione elenca tutte le destinazioni esistenti e fornisce un collegamento per aggiungerne una nuova.

Fare clic **sul pulsante Aggiungi destinazione di** archiviazione.

![Screenshot della pagina Aggiungi destinazione di archiviazione, popolata con le informazioni per una nuova destinazione di archiviazione BLOB di Azure](media/hpc-cache-add-blob.png)

Per definire un contenitore BLOB di Azure, immettere queste informazioni.

* **Nome destinazione di archiviazione:** impostare un nome che identifichi questa destinazione di archiviazione nel Cache HPC di Azure.
* **Tipo di destinazione:** scegliere **BLOB.**
* **Account di archiviazione:** selezionare l'account che si vuole usare.

  È necessario autorizzare l'istanza della cache ad accedere all'account di archiviazione come descritto in [Aggiungere i ruoli di accesso](#add-the-access-control-roles-to-your-account).

  Per informazioni sul tipo di account di archiviazione che è possibile usare, vedere Requisiti [di archiviazione BLOB](hpc-cache-prerequisites.md#blob-storage-requirements).

* **Contenitore di** archiviazione: selezionare il contenitore BLOB per questa destinazione oppure fare clic **su Crea nuovo**.

  ![Screenshot della finestra di dialogo per specificare il nome e il livello di accesso (privato) per il nuovo contenitore](media/add-blob-new-container.png)

Al termine, fare clic **su OK** per aggiungere la destinazione di archiviazione.

> [!NOTE]
> Se il firewall dell'account di archiviazione è impostato per limitare l'accesso solo alle "reti selezionate", usare la soluzione temporanea documentata in Risolvere le impostazioni del [firewall dell'account di archiviazione BLOB.](hpc-cache-blob-firewall-fix.md)

### <a name="add-the-access-control-roles-to-your-account"></a>Aggiungere i ruoli di controllo di accesso all'account

Cache HPC di Azure usa il controllo degli accessi in base al ruolo di [Azure](../role-based-access-control/index.yml) per autorizzare il servizio cache ad accedere all'account di archiviazione per le destinazioni di archiviazione BLOB di Azure.

Il proprietario dell'account di archiviazione deve aggiungere in modo esplicito i ruoli [Collaboratore account](../role-based-access-control/built-in-roles.md#storage-account-contributor) di archiviazione e Collaboratore ai dati del BLOB di [archiviazione](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) per l'utente "Provider di risorse cache HPC".

È possibile eseguire questa operazione in anticipo oppure facendo clic su un collegamento nella pagina in cui si aggiunge una destinazione di archiviazione BLOB. Tenere presente che la propagazione delle impostazioni del ruolo nell'ambiente Azure può richiedere fino a cinque minuti, quindi è consigliabile attendere alcuni minuti dopo l'aggiunta dei ruoli prima di creare una destinazione di archiviazione.

Passaggi per aggiungere i ruoli di Azure:

1. Aprire la **pagina Controllo di accesso (IAM) per** l'account di archiviazione. Il collegamento nella pagina Aggiungi **destinazione di archiviazione** apre automaticamente questa pagina per l'account selezionato.

1. Fare clic **+** su nella parte superiore della pagina e scegliere Aggiungi **un'assegnazione di ruolo.**

1. Selezionare il ruolo "Collaboratore account di archiviazione" dall'elenco.

1. Nel campo **Assegna accesso a** lasciare selezionato il valore predefinito ("Azure AD utente, gruppo o entità servizio").  

1. Nel **campo Seleziona** cercare "hpc".  Questa stringa deve corrispondere a un'entità servizio, denominata "Provider di risorse cache HPC". Fare clic sull'entità per selezionarla.

   > [!NOTE]
   > Se la ricerca di "hpc" non funziona, provare a usare la stringa "storagecache". Gli utenti che hanno partecipato alle anteprime (prima della versione ga) potrebbero dover usare il nome precedente per l'entità servizio.

1. Fare clic **sul pulsante** Salva nella parte inferiore.

1. Ripetere questo processo per assegnare il ruolo "Collaboratore ai dati dei BLOB di archiviazione".  

![screenshot dell'interfaccia utente grafica per l'aggiunta dell'assegnazione di ruolo](media/hpc-cache-add-role.png)

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

### <a name="prerequisite-storage-account-access"></a>Prerequisito: Accesso all'account di archiviazione

[Configurare l'interfaccia della riga di comando di Azure Cache HPC di Azure](./az-cli-prerequisites.md).

Prima di aggiungere una destinazione di archiviazione BLOB, verificare che la cache abbia i ruoli corretti per accedere all'account di archiviazione e che le impostazioni del firewall consentano la creazione della destinazione di archiviazione.

Cache HPC di Azure usa il controllo degli accessi in base al ruolo di [Azure](../role-based-access-control/index.yml) per autorizzare il servizio cache ad accedere all'account di archiviazione per le destinazioni di archiviazione BLOB di Azure.

Il proprietario dell'account di archiviazione deve aggiungere in modo esplicito i ruoli [Collaboratore account](../role-based-access-control/built-in-roles.md#storage-account-contributor) di archiviazione e Collaboratore dati BLOB di archiviazione [per](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) l'utente "Provider di risorse cache HPC".

La creazione della destinazione di archiviazione avrà esito negativo se la cache non ha questi ruoli.

La propagazione delle impostazioni del ruolo nell'ambiente di Azure può richiedere fino a cinque minuti, quindi è consigliabile attendere alcuni minuti dopo l'aggiunta dei ruoli prima di creare una destinazione di archiviazione.

Per [istruzioni dettagliate, vedere Aggiungere o rimuovere assegnazioni di ruolo di Azure](../role-based-access-control/role-assignments-cli.md) usando l'interfaccia della riga di comando di Azure.

Controllare anche le impostazioni del firewall dell'account di archiviazione. Se il firewall è impostato per limitare l'accesso solo alle "reti selezionate", la creazione della destinazione di archiviazione potrebbe non riuscire. Usare la soluzione alternativa documentata in [Risolvere le impostazioni del firewall dell'account di archiviazione BLOB.](hpc-cache-blob-firewall-fix.md)

### <a name="add-a-blob-storage-target-with-azure-cli"></a>Aggiungere una destinazione di archiviazione BLOB con l'interfaccia della riga di comando di Azure

Usare [l'interfaccia az hpc-cache blob-storage-target add](/cli/azure/hpc-cache/blob-storage-target#az_hpc_cache_blob_storage_target_add) per definire una destinazione di archiviazione BLOB di Azure.

> [!NOTE]
> I comandi dell'interfaccia della riga di comando di Azure richiedono attualmente di creare un percorso dello spazio dei nomi quando si aggiunge una destinazione di archiviazione. Questo processo è diverso dal processo usato con l'portale di Azure predefinita.

Oltre ai parametri del gruppo di risorse standard e del nome della cache, è necessario specificare queste opzioni per la destinazione di archiviazione:

* ``--name`` - Impostare un nome che identifichi questa destinazione di archiviazione nel Cache HPC di Azure.

* ``--storage-account`` - Identificatore dell'account, nel formato seguente: /subscriptions/*<subscription_id>*/resourceGroups/*<storage_resource_group>*/providers/Microsoft.Storage/storageAccounts/*<account_name>*

  Per informazioni sul tipo di account di archiviazione che è possibile usare, vedere Requisiti [di archiviazione BLOB.](hpc-cache-prerequisites.md#blob-storage-requirements)

* ``--container-name`` - Specificare il nome del contenitore da usare per questa destinazione di archiviazione.

* ``--virtual-namespace-path`` - Impostare il percorso del file per il client per questa destinazione di archiviazione. Racchiudere i percorsi tra virgolette. Per [altre informazioni sulla funzionalità dello spazio dei](hpc-cache-namespace.md) nomi virtuale, vedere Pianificare lo spazio dei nomi aggregato.

Comando di esempio:

```azurecli
az hpc-cache blob-storage-target add --resource-group "hpc-cache-group" \
    --cache-name "cache1" --name "blob-target1" \
    --storage-account "/subscriptions/<subscriptionID>/resourceGroups/myrgname/providers/Microsoft.Storage/storageAccounts/myaccountname" \
    --container-name "container1" --virtual-namespace-path "/blob1"
```

---

## <a name="add-a-new-nfs-storage-target"></a>Aggiungere una nuova destinazione di archiviazione NFS

Una destinazione di archiviazione NFS ha impostazioni diverse da una destinazione di archiviazione BLOB. L'impostazione del modello di utilizzo consente alla cache di memorizzare in modo efficiente nella cache i dati di questo sistema di archiviazione.

![Screenshot della pagina aggiungi destinazione di archiviazione con la destinazione NFS definita](media/add-nfs-target.png)

> [!NOTE]
> Prima di creare una destinazione di archiviazione NFS, assicurarsi che il sistema di archiviazione sia accessibile dal Cache HPC di Azure e soddisfi i requisiti di autorizzazione. La creazione della destinazione di archiviazione avrà esito negativo se la cache non riesce ad accedere al sistema di archiviazione. Per informazioni [dettagliate, vedere](hpc-cache-prerequisites.md#nfs-storage-requirements) Requisiti di archiviazione NFS e Risolvere i problemi di [configurazione NAS e destinazione di archiviazione NFS.](troubleshoot-nas.md)

### <a name="choose-a-usage-model"></a>Scegliere un modello di utilizzo
<!-- referenced from GUI by aka.ms link -->

Quando si crea una destinazione di archiviazione che usa NFS per raggiungere il sistema di archiviazione, è necessario scegliere un modello di utilizzo per tale destinazione. Questo modello determina la modalità di memorizzazione dei dati nella cache.

Per [altre informazioni su tutte queste](cache-usage-models.md) impostazioni, vedere Informazioni sui modelli di utilizzo.

I modelli di utilizzo predefiniti consentono di scegliere come bilanciare la risposta rapida con il rischio di ottenere dati non obsoleti. Se si vuole ottimizzare la velocità di lettura dei file, potrebbe non essere necessario verificare se i file nella cache vengono verificati rispetto ai file back-end. D'altra parte, se si vuole assicurarsi che i file siano sempre aggiornati con l'archiviazione remota, scegliere un modello che controlli frequentemente.

Queste tre opzioni riguardano la maggior parte delle situazioni:

* **Operazioni di lettura pesanti e poco** frequenti: velocizza l'accesso in lettura ai file statici o modificati raramente.

  Questa opzione memorizza nella cache i file dalle operazioni di lettura del client, ma passa immediatamente le scritture client all'archiviazione back-end. I file archiviati nella cache non vengono confrontati automaticamente con i file nel volume di archiviazione NFS.

  Non usare questa opzione se esiste il rischio che un file possa essere modificato direttamente nel sistema di archiviazione senza prima scriverlo nella cache. In questo caso, la versione memorizzata nella cache del file non sarà sincronizzata con il file back-end.

* **Scritture superiori al 15%:** questa opzione velocizza le prestazioni di lettura e scrittura.

  Le operazioni di lettura e scrittura client vengono entrambe memorizzate nella cache. Si presuppone che i file nella cache siano più nuovi dei file nel sistema di archiviazione back-end. I file memorizzati nella cache vengono controllati automaticamente solo sui file nell'archiviazione back-end ogni otto ore. I file modificati nella cache vengono scritti nel sistema di archiviazione back-end dopo che sono stati memorizzati nella cache per 20 minuti senza modifiche aggiuntive.

  Non usare questa opzione se i client montano direttamente il volume di archiviazione back-end, perché esiste il rischio che abbia file obsoleti.

* I client scrivono nella destinazione **NFS,** ignorando la cache: scegliere questa opzione se i client nel flusso di lavoro scrivono i dati direttamente nel sistema di archiviazione senza prima scrivere nella cache o se si vuole ottimizzare la coerenza dei dati.

  I file che i client richiedono vengono memorizzati nella cache, ma qualsiasi modifica a tali file dal client viene passata immediatamente al sistema di archiviazione back-end. I file nella cache vengono spesso verificati in base alle versioni back-end per gli aggiornamenti. Questa verifica mantiene la coerenza dei dati quando i file vengono modificati direttamente nel sistema di archiviazione anziché tramite la cache.

Per informazioni dettagliate sulle altre opzioni, vedere [Informazioni sui modelli di utilizzo.](cache-usage-models.md)

Questa tabella riepiloga le differenze tra tutti i modelli di utilizzo:

[!INCLUDE [usage-models-table.md](includes/usage-models-table.md)]

> [!NOTE]
> Il **valore di verifica back-end** indica quando la cache confronta automaticamente i file con i file di origine nell'archiviazione remota. È tuttavia possibile attivare un confronto inviando una richiesta client che include un'operazione readdirplus nel sistema di archiviazione back-end. Readdirplus è un'API NFS standard (detta anche lettura estesa) che restituisce i metadati della directory, che fa sì che la cache confronti e aggiornesca i file.

### <a name="create-an-nfs-storage-target"></a>Creare una destinazione di archiviazione NFS

### <a name="portal"></a>[Portale](#tab/azure-portal)

Dal portale di Azure aprire l'istanza della cache e fare clic su **Destinazioni di archiviazione** nella barra laterale sinistra.

![screenshot delle impostazioni nella > di destinazione di archiviazione, con due destinazioni di archiviazione esistenti in una tabella e un'evidenziazione intorno al pulsante + Aggiungi destinazione di archiviazione sopra la tabella](media/add-storage-target-button.png)

La **pagina Destinazioni di** archiviazione elenca tutte le destinazioni esistenti e fornisce un collegamento per aggiungerne una nuova.

Fare clic sul **pulsante Aggiungi destinazione di** archiviazione.

![Screenshot della pagina aggiungi destinazione di archiviazione con la destinazione NFS definita](media/add-nfs-target.png)

Specificare queste informazioni per una destinazione di archiviazione supportata da NFS:

* **Nome destinazione di archiviazione:** impostare un nome che identifichi questa destinazione di archiviazione nel Cache HPC di Azure.

* **Tipo di destinazione:** scegliere **NFS.**

* **Nome host:** immettere l'indirizzo IP o il nome di dominio completo per il sistema di archiviazione NFS. Usare un nome di dominio solo se la cache ha accesso a un server DNS in grado di risolvere il nome.

* **Modello di utilizzo:** scegliere uno dei profili di memorizzazione nella cache dei dati in base al flusso di lavoro, descritto in [Scegliere un modello di utilizzo in precedenza.](#choose-a-usage-model)

Al termine, fare clic **su OK** per aggiungere la destinazione di archiviazione.

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

[Configurare l'interfaccia della riga di comando di Azure Cache HPC di Azure](./az-cli-prerequisites.md).

Usare il comando dell'interfaccia della riga di comando di Azure [az hpc-cache nfs-storage-target add](/cli/azure/hpc-cache/nfs-storage-target#az_hpc_cache_nfs_storage_target_add) per creare la destinazione di archiviazione.

> [!NOTE]
> I comandi dell'interfaccia della riga di comando di Azure richiedono attualmente di creare un percorso dello spazio dei nomi quando si aggiunge una destinazione di archiviazione. Questo processo è diverso dal processo usato con l'portale di Azure predefinita.

Specificare questi valori oltre al nome della cache e al gruppo di risorse della cache:

* ``--name`` - Impostare un nome che identifichi questa destinazione di archiviazione nel Cache HPC di Azure.
* ``--nfs3-target`` - Indirizzo IP del sistema di archiviazione NFS. È possibile usare un nome di dominio completo qui se la cache ha accesso a un server DNS in grado di risolvere il nome.
* ``--nfs3-usage-model`` - Uno dei profili di memorizzazione nella cache dei dati, descritto in [Scegliere un modello di utilizzo,](#choose-a-usage-model)riportato in precedenza.

  Verificare i nomi dei modelli di utilizzo con il comando [az hpc-cache usage-model list](/cli/azure/hpc-cache/usage-model#az_hpc_cache_usage_model_list).

* ``--junction`` - Il parametro di giunzione collega il percorso del file virtuale rivolto al client con un percorso di esportazione nel sistema di archiviazione.

  Una destinazione di archiviazione NFS può avere più percorsi virtuali, purché ogni percorso rappresenti un'esportazione o una sottodirectory diversa nello stesso sistema di archiviazione. Creare tutti i percorsi per un sistema di archiviazione in un'unica destinazione di archiviazione.

  È possibile [aggiungere e modificare i percorsi degli spazi dei](add-namespace-paths.md) nomi in una destinazione di archiviazione in qualsiasi momento.

  Il ``--junction`` parametro usa questi valori:

  * ``namespace-path`` - Percorso del file virtuale con connessione client
  * ``nfs-export`` - Esportazione del sistema di archiviazione da associare al percorso lato client
  * ``target-path`` (facoltativo): sottodirectory dell'esportazione, se necessario

  Esempio: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

  Per [altre informazioni sulla funzionalità dello spazio](hpc-cache-namespace.md) dei nomi virtuale, vedere Configurare lo spazio dei nomi aggregato.

Comando di esempio:

```azurecli

az hpc-cache nfs-storage-target add --resource-group "hpc-cache-group" --cache-name "doc-cache0629" \
    --name nfsd1 --nfs3-target 10.0.0.4 --nfs3-usage-model WRITE_WORKLOAD_15 \
    --junction namespace-path="/nfs1/data1" nfs-export="/datadisk1" target-path=""
```

Output:
```azurecli

{- Finished ..
  "clfs": null,
  "id": "/subscriptions/<subscriptionID>/resourceGroups/hpc-cache-group/providers/Microsoft.StorageCache/caches/doc-cache0629/storageTargets/nfsd1",
  "junctions": [
    {
      "namespacePath": "/nfs1/data1",
      "nfsExport": "/datadisk1",
      "targetPath": ""
    }
  ],
  "location": "eastus",
  "name": "nfsd1",
  "nfs3": {
    "target": "10.0.0.4",
    "usageModel": "WRITE_WORKLOAD_15"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "hpc-cache-group",
  "targetType": "nfs3",
  "type": "Microsoft.StorageCache/caches/storageTargets",
  "unknown": null
}

```

---

## <a name="add-a-new-adls-nfs-storage-target-preview"></a>Aggiungere una nuova destinazione di archiviazione ADLS-NFS (ANTEPRIMA)

Le destinazioni di archiviazione ADLS-NFS usano contenitori BLOB di Azure che supportano il protocollo NFS (Network File System) 3.0.

> [!NOTE]
> Il supporto del protocollo NFS 3.0 per l'archiviazione BLOB di Azure è disponibile in anteprima pubblica. La disponibilità è limitata e le funzionalità potrebbero cambiare tra ora e quando la funzionalità diventa disponibile a livello generale. Non usare la tecnologia di anteprima nei sistemi di produzione.
>
> Per le informazioni più recenti, vedere Supporto [del protocollo NFS 3.0.](../storage/blobs/network-file-system-protocol-support.md)

Le destinazioni di archiviazione ADLS-NFS hanno alcune analogie con le destinazioni di archiviazione BLOB e altre con le destinazioni di archiviazione NFS. Ad esempio:

* Analogamente a una destinazione di archiviazione BLOB, è necessario concedere Cache HPC di Azure'autorizzazione per [accedere all'account di archiviazione.](#add-the-access-control-roles-to-your-account)
* Analogamente a una destinazione di archiviazione NFS, è necessario impostare un modello di [utilizzo della](#choose-a-usage-model)cache .
* Poiché i contenitori BLOB abilitati per NFS hanno una struttura gerarchica compatibile con NFS, non è necessario usare la cache per inserire i dati e i contenitori sono leggibili da altri sistemi NFS. È possibile precaricare i dati in un contenitore ADLS-NFS, quindi aggiungerli a una cache HPC come destinazione di archiviazione e quindi accedere ai dati in un secondo momento dall'esterno di una cache HPC. Quando si usa un contenitore BLOB standard come destinazione di archiviazione della cache HPC, i dati vengono scritti in un formato proprietario ed è possibile accedervi solo da altri Cache HPC di Azure compatibili con Cache HPC di Azure.

Prima di poter creare una destinazione di archiviazione ADLS-NFS, è necessario creare un account di archiviazione abilitato per NFS. Seguire i suggerimenti in [Prerequisiti per Cache HPC di Azure](hpc-cache-prerequisites.md#nfs-mounted-blob-adls-nfs-storage-requirements-preview) e le istruzioni in [Montare l'archiviazione BLOB usando NFS.](../storage/blobs/network-file-system-protocol-support-how-to.md) Dopo aver configurato l'account di archiviazione, è possibile creare un nuovo contenitore quando si crea la destinazione di archiviazione.

Per altre informazioni su questa configurazione, vedere Use [NFS-mounted blob storage with Cache HPC di Azure](nfs-blob-considerations.md) (Usare l'archiviazione BLOB montata con NFS con Cache HPC di Azure informazioni su questa configurazione).

Per creare una destinazione di archiviazione ADLS-NFS, aprire la **pagina Aggiungi** destinazione di archiviazione nel portale di Azure. Altri metodi sono in fase di sviluppo.

![Screenshot della pagina aggiungi destinazione di archiviazione con la destinazione ADLS-NFS definita](media/add-adls-target.png)

Immettere queste informazioni.

* **Nome destinazione di archiviazione:** impostare un nome che identifichi questa destinazione di archiviazione nel Cache HPC di Azure.
* **Tipo di** destinazione: scegliere **ADLS-NFS.**
* **Account di** archiviazione: selezionare l'account che si vuole usare. Se l'account di archiviazione abilitato per NFS non viene visualizzato nell'elenco, verificare che sia conforme ai prerequisiti e che la cache possa accedervi.

  È necessario autorizzare l'istanza della cache ad accedere all'account di archiviazione come descritto in [Aggiungere i ruoli di accesso](#add-the-access-control-roles-to-your-account).

* **Contenitore di** archiviazione: selezionare il contenitore BLOB abilitato per NFS per questa destinazione oppure fare clic **su Crea nuovo**.

* **Modello di utilizzo:** scegliere uno dei profili di memorizzazione nella cache dei dati in base al flusso di lavoro, descritto in Scegliere un modello di utilizzo descritto in [precedenza.](#choose-a-usage-model)

Al termine, fare clic **su OK** per aggiungere la destinazione di archiviazione.

## <a name="view-storage-targets"></a>Visualizzare le destinazioni di archiviazione

È possibile usare il portale di Azure o l'interfaccia della riga di comando di Azure per visualizzare le destinazioni di archiviazione già definite per la cache.

### <a name="portal"></a>[Portale](#tab/azure-portal)

Dal riquadro portale di Azure aprire l'istanza della cache e fare clic su **Destinazioni di** archiviazione , che si trova sotto l'intestazione Impostazioni sulla barra laterale sinistra. La pagina Destinazioni di archiviazione elenca tutte le destinazioni e i controlli esistenti per l'aggiunta o l'eliminazione.

Fare clic sul nome di una destinazione di archiviazione per aprire la pagina dei dettagli.

Per altre [informazioni, vedere Modificare](hpc-cache-edit-storage.md) le destinazioni di archiviazione.

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

[Configurare l'interfaccia della riga di comando di Azure Cache HPC di Azure](./az-cli-prerequisites.md).

Usare [l'opzione az hpc-cache storage-target list](/cli/azure/hpc-cache/storage-target#az_hpc_cache_storage-target-list) per visualizzare le destinazioni di archiviazione esistenti per una cache. Specificare il nome della cache e il gruppo di risorse (a meno che non sia stato impostato a livello globale).

```azurecli
az hpc-cache storage-target list --resource-group "scgroup" --cache-name "sc1"
```

Usare [az hpc-cache storage-target show](/cli/azure/hpc-cache/storage-target#az_hpc_cache_storage-target-list) per visualizzare i dettagli su una determinata destinazione di archiviazione. Specificare la destinazione di archiviazione in base al nome.

Esempio:

```azurecli
$ az hpc-cache storage-target show --cache-name doc-cache0629 --name nfsd1

{
  "clfs": null,
  "id": "/subscriptions/<subscription_ID>/resourceGroups/scgroup/providers/Microsoft.StorageCache/caches/doc-cache0629/storageTargets/nfsd1",
  "junctions": [
    {
      "namespacePath": "/nfs1/data1",
      "nfsExport": "/datadisk1",
      "targetPath": ""
    }
  ],
  "location": "eastus",
  "name": "nfsd1",
  "nfs3": {
    "target": "10.0.0.4",
    "usageModel": "WRITE_WORKLOAD_15"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "scgroup",
  "targetType": "nfs3",
  "type": "Microsoft.StorageCache/caches/storageTargets",
  "unknown": null
}
$
```

---

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato le destinazioni di archiviazione, continuare con queste attività per ottenere la cache pronta per l'uso:

* [Configurare lo spazio dei nomi aggregato](add-namespace-paths.md)
* [Montare la cache HPC di Azure](hpc-cache-mount.md)
* [Spostare i dati nell'archiviazione BLOB di Azure](hpc-cache-ingest.md)

Se è necessario aggiornare le impostazioni, è possibile modificare [una destinazione di archiviazione](hpc-cache-edit-storage.md).
