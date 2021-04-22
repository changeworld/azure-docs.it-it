---
title: Configurare lo spazio Cache HPC di Azure dello spazio dei nomi aggregato
description: Come creare percorsi rivolti al client per l'archiviazione back-end con Cache HPC di Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/11/2021
ms.author: v-erkel
ms.openlocfilehash: 2cb8db4e73a8f4fa299031070bffc15a2b754d7e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870374"
---
# <a name="set-up-the-aggregated-namespace"></a>Configurare lo spazio dei nomi aggregato

Dopo aver creato le destinazioni di archiviazione, è necessario creare anche i percorsi degli spazi dei nomi. I computer client usano questi percorsi virtuali per accedere ai file tramite la cache anziché connettersi direttamente all'archiviazione back-end. Questo sistema consente agli amministratori della cache di modificare i sistemi di archiviazione back-end senza dover riscrivere le istruzioni client.

Per [altre informazioni su questa funzionalità,](hpc-cache-namespace.md) vedere Pianificare lo spazio dei nomi aggregato.

La **pagina Spazio** dei nomi portale di Azure i percorsi che i client usano per accedere ai dati tramite la cache. Usare questa pagina per creare, rimuovere o modificare i percorsi degli spazi dei nomi. È anche possibile configurare i percorsi degli spazi dei nomi usando l'interfaccia della riga di comando di Azure.

Tutti i percorsi per il client definiti per questa cache sono elencati nella pagina **Spazio dei** nomi . Le destinazioni di archiviazione per cui non sono stati definiti percorsi dello spazio dei nomi non vengono ancora visualizzate nella tabella.

È possibile ordinare le colonne della tabella per comprendere meglio lo spazio dei nomi aggregato della cache. Fare clic sulle frecce nelle intestazioni di colonna per ordinare i percorsi.

[![Screenshot della pagina dello spazio dei nomi del portale con due percorsi in una tabella. Intestazioni di colonna: percorso dello spazio dei nomi, destinazione di archiviazione, percorso di esportazione e sottodirectory di esportazione e criteri di accesso client. I nomi dei percorsi nella prima colonna sono collegamenti selezionabili. Pulsanti in alto: Aggiungere il percorso dello spazio dei nomi, aggiornare, eliminare ](media/namespace-page.png)](media/namespace-page.png#lightbox)

## <a name="add-or-edit-namespace-paths"></a>Aggiungere o modificare i percorsi degli spazi dei nomi

È necessario creare almeno un percorso dello spazio dei nomi prima che i client possano accedere alla destinazione di archiviazione. Per altre [informazioni sull'accesso client, vedere Montare](hpc-cache-mount.md) il Cache HPC di Azure.

Se di recente è stata aggiunta una destinazione di archiviazione o è stato personalizzato un criterio di accesso, potrebbe essere necessario un minuto o due prima di poter creare un percorso dello spazio dei nomi.

### <a name="blob-namespace-paths"></a>Percorsi dello spazio dei nomi BLOB

Una destinazione di archiviazione BLOB di Azure può avere un solo percorso dello spazio dei nomi.

Seguire le istruzioni seguenti per impostare o modificare il percorso con l'interfaccia della riga portale di Azure o l'interfaccia della riga di comando di Azure.

### <a name="portal"></a>[Portale](#tab/azure-portal)

Dal portale di Azure, caricare la pagina **Delle impostazioni dello** spazio dei nomi. È possibile aggiungere, modificare o eliminare percorsi dello spazio dei nomi da questa pagina.

* **Aggiungere un nuovo percorso:** Fare clic **sul pulsante +** Aggiungi nella parte superiore e immettere le informazioni nel pannello di modifica.

  ![Screenshot dell'aggiunta dei campi di modifica dello spazio dei nomi con una destinazione di archiviazione BLOB selezionata. I percorsi di esportazione e sottodirectory sono impostati su / e non sono modificabili.](media/namespace-add-blob.png)

  * Immettere il percorso che i client useranno per accedere a questa destinazione di archiviazione.

  * Selezionare i criteri di accesso da usare per questo percorso. Per altre informazioni sulla personalizzazione dell'accesso client, vedere [Usare i criteri di accesso client.](access-policies.md)

  * Selezionare la destinazione di archiviazione dall'elenco a discesa. Se una destinazione di archiviazione BLOB ha già un percorso dello spazio dei nomi, non può essere selezionata.

  * Per una destinazione di archiviazione BLOB di Azure, i percorsi di esportazione e sottodirectory vengono impostati automaticamente su ``/`` .

* **Modificare un percorso esistente:** Fare clic sul percorso dello spazio dei nomi. Viene aperto il pannello di modifica. È possibile modificare il percorso e i criteri di accesso, ma non è possibile passare a una destinazione di archiviazione diversa.

* **Eliminare un percorso dello spazio dei nomi:** Selezionare la casella di controllo a sinistra del percorso e fare clic sul **pulsante** Elimina.

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

[Configurare l'interfaccia della riga di comando di Azure Cache HPC di Azure](./az-cli-prerequisites.md).

Quando si usa l'interfaccia della riga di comando di Azure, è necessario aggiungere un percorso dello spazio dei nomi quando si crea la destinazione di archiviazione. Per [informazioni dettagliate, vedere Aggiungere una nuova destinazione di archiviazione BLOB](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-azure-blob-storage-target) di Azure.

Per aggiornare il percorso dello spazio dei nomi della destinazione, usare [il comando az hpc-cache blob-storage-target update.](/cli/azure/hpc-cache/blob-storage-target#az_hpc_cache_blob_storage_target_update) Gli argomenti per il comando update sono simili agli argomenti nel comando create, ad eccezione del fatto che non si passa il nome del contenitore o l'account di archiviazione.

Non è possibile eliminare un percorso dello spazio dei nomi da una destinazione di archiviazione BLOB con l'interfaccia della riga di comando di Azure, ma è possibile sovrascrivere il percorso con un valore diverso.

---

### <a name="nfs-namespace-paths"></a>Percorsi dello spazio dei nomi NFS

Una destinazione di archiviazione NFS può avere più percorsi virtuali, purché ogni percorso rappresenti un'esportazione o una sottodirectory diversa nello stesso sistema di archiviazione.

Quando si pianifica lo spazio dei nomi per una destinazione di archiviazione NFS, tenere presente che ogni percorso deve essere univoco e non può essere una sottodirectory di un altro percorso dello spazio dei nomi. Ad esempio, se si dispone di un percorso dello spazio dei nomi denominato , non è possibile creare anche percorsi dello spazio dei nomi ``/parent-a`` come ``/parent-a/user1`` e ``/parent-a/user2`` . Tali percorsi di directory sono già accessibili nello spazio dei nomi come sottodirectory di ``/parent-a`` .

Tutti i percorsi dello spazio dei nomi per un sistema di archiviazione NFS vengono creati in un'unica destinazione di archiviazione. La maggior parte delle configurazioni della cache può supportare fino a dieci percorsi dello spazio dei nomi per ogni destinazione di archiviazione, ma configurazioni più grandi possono supportare fino a 20.

Questo elenco mostra il numero massimo di percorsi dello spazio dei nomi per ogni configurazione.

* Velocità effettiva fino a 2 GB/s:

  * 3 TB di cache - 10 percorsi dello spazio dei nomi
  * 6 TB di cache - 10 percorsi dello spazio dei nomi
  * 12 TB di cache - 20 percorsi dello spazio dei nomi

* Velocità effettiva fino a 4 GB/s:

  * 6 TB di cache - 10 percorsi dello spazio dei nomi
  * 12 TB di cache - 10 percorsi dello spazio dei nomi
  * 24 TB di cache -20 percorsi dello spazio dei nomi

* Velocità effettiva fino a 8 GB/s:

  * Cache da 12 TB - 10 percorsi dello spazio dei nomi
  * Cache da 24 TB - 10 percorsi dello spazio dei nomi
  * Cache da 48 TB - 20 percorsi dello spazio dei nomi

Per ogni percorso dello spazio dei nomi NFS, specificare il percorso lato client, l'esportazione del sistema di archiviazione e, facoltativamente, una sottodirectory di esportazione.

### <a name="portal"></a>[Portale](#tab/azure-portal)

Dal portale di Azure, caricare la pagina **Delle impostazioni dello** spazio dei nomi. È possibile aggiungere, modificare o eliminare percorsi dello spazio dei nomi da questa pagina.

* **Per aggiungere un nuovo percorso:** Fare clic **sul pulsante +** Aggiungi nella parte superiore e immettere le informazioni nel pannello di modifica.
* **Per modificare un percorso esistente:** Fare clic sul percorso dello spazio dei nomi. Si apre il pannello di modifica ed è possibile modificare il percorso.
* **Per eliminare un percorso dello spazio dei nomi:** Selezionare la casella di controllo a sinistra del percorso e fare clic sul **pulsante** Elimina.

Compilare questi valori per ogni percorso dello spazio dei nomi:

* **Percorso dello spazio** dei nomi: percorso del file lato client.

* **Criteri di accesso client:** selezionare i criteri di accesso da usare per questo percorso. Per altre informazioni sulla personalizzazione dell'accesso client, vedere [Usare i criteri di accesso client.](access-policies.md)

* **Destinazione di archiviazione:** se si crea un nuovo percorso dello spazio dei nomi, selezionare una destinazione di archiviazione dal menu a discesa.

* **Percorso di** esportazione: immettere il percorso dell'esportazione NFS. Assicurarsi di digitare correttamente il nome dell'esportazione. Il portale convalida la sintassi per questo campo, ma non controlla l'esportazione fino a quando non si invia la modifica.

* Export subdirectory (Esporta **sottodirectory)** - Se si vuole che questo percorso monta una sottodirectory specifica dell'esportazione, immetterla qui. In caso contrario, lasciare vuoto questo campo.

![screenshot della pagina dello spazio dei nomi del portale con la pagina di modifica aperta a destra. Il modulo di modifica mostra le impostazioni per un percorso dello spazio dei nomi di destinazione di archiviazione nfs](media/namespace-edit-nfs.png)

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

[Configurare l'interfaccia della riga di comando di Azure Cache HPC di Azure](./az-cli-prerequisites.md).

Quando si usa l'interfaccia della riga di comando di Azure, è necessario aggiungere almeno un percorso dello spazio dei nomi quando si crea la destinazione di archiviazione. Per [informazioni dettagliate, vedere Aggiungere una nuova destinazione di archiviazione NFS.](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-nfs-storage-target)

Per aggiornare il percorso dello spazio dei nomi della destinazione o per aggiungere altri percorsi, usare il [comando az hpc-cache nfs-storage-target update.](/cli/azure/hpc-cache/nfs-storage-target#az_hpc_cache_nfs_storage_target_update) Usare ``--junction`` l'opzione per specificare tutti i percorsi dello spazio dei nomi desiderati.

Le opzioni usate per il comando update sono simili al comando "create", ad eccezione del fatto che non si passano le informazioni sul sistema di archiviazione (indirizzo IP o nome host) e il modello di utilizzo è facoltativo. Per [altri dettagli sulla sintassi dell'opzione,](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-nfs-storage-target) vedere Aggiungere una nuova destinazione di archiviazione ``--junction`` NFS.

---

### <a name="adls-nfs-namespace-paths-preview"></a>Percorsi dello spazio dei nomi ADLS-NFS (ANTEPRIMA)

Analogamente a una normale destinazione di archiviazione BLOB, una destinazione di archiviazione ADLS-NFS ha una sola esportazione, quindi può avere un solo percorso dello spazio dei nomi.

Seguire le istruzioni seguenti per impostare o modificare il percorso con il portale di Azure.

Caricare la pagina **Delle impostazioni** dello spazio dei nomi.

* **Aggiungere un nuovo percorso:** Fare clic **sul pulsante +** Aggiungi nella parte superiore e immettere le informazioni nel pannello di modifica.

  ![Screenshot dell'aggiunta di campi di modifica dello spazio dei nomi con una destinazione di archiviazione ADLS-NFS selezionata. I percorsi di esportazione e sottodirectory sono impostati su / e non possono essere modificati.](media/namespace-add-adls.png)

  * Immettere il percorso che i client useranno per accedere a questa destinazione di archiviazione.

  * Selezionare i criteri di accesso da usare per questo percorso. Per altre informazioni sulla personalizzazione dell'accesso client, [vedere Usare i criteri di accesso client.](access-policies.md)

  * Selezionare la destinazione di archiviazione dall'elenco a discesa. Se una destinazione di archiviazione ADLS-NFS ha già un percorso dello spazio dei nomi, non può essere selezionata.

  * Per una destinazione di archiviazione ADLS-NFS, i percorsi di esportazione e sottodirectory vengono impostati automaticamente su ``/`` .

* **Modificare un percorso esistente:** Fare clic sul percorso dello spazio dei nomi. Viene aperto il pannello di modifica. È possibile modificare il percorso e i criteri di accesso, ma non è possibile passare a una destinazione di archiviazione diversa.

* **Eliminare un percorso dello spazio dei nomi:** Selezionare la casella di controllo a sinistra del percorso e fare clic sul **pulsante** Elimina.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato lo spazio dei nomi aggregato per le destinazioni di archiviazione, è possibile montare i client nella cache. Per altre informazioni, leggere questi articoli.

* [Montare la cache HPC di Azure](hpc-cache-mount.md)
* [Spostare i dati in Archiviazione BLOB di Azure](hpc-cache-ingest.md)
