---
title: Abilitare Azure Machine Learning Studio in una rete virtuale
titleSuffix: Azure Machine Learning
description: Informazioni su come configurare Azure Machine Learning Studio per accedere ai dati archiviati all'interno di una rete virtuale.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 10/21/2020
ms.custom: contperfq4, tracking-python
ms.openlocfilehash: a90b98e8be976da9ee2669ab3b5fed4a890f0fb2
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576621"
---
# <a name="use-azure-machine-learning-studio-in-an-azure-virtual-network"></a>Usare Azure Machine Learning Studio in una rete virtuale di Azure

Questo articolo illustra come usare Azure Machine Learning Studio in una rete virtuale. In studio sono incluse funzionalità come AutoML, la finestra di progettazione e l'assegnazione di etichette ai dati. Per usare queste funzionalità in una rete virtuale, è necessario seguire la procedura descritta in questo articolo.

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> - Consentire a studio di accedere ai dati archiviati all'interno di una rete virtuale.
> - Accedere a Studio da una risorsa all'interno di una rete virtuale.
> - Informazioni sul modo in cui lo studio influisca sulla sicurezza dell'archiviazione.

Questo articolo è la parte cinque di una serie in cinque parti che illustra come proteggere un flusso di lavoro Azure Machine Learning. Si consiglia vivamente di leggere le parti precedenti per configurare un ambiente di rete virtuale.

Vedere gli altri articoli di questa serie:

[1. Panoramica di VNet](how-to-network-security-overview.md)  >  [2. Proteggere l'area di lavoro](how-to-secure-workspace-vnet.md)  >  [3. Proteggere l'ambiente di training](how-to-secure-training-vnet.md)  >  [4. Proteggere l'ambiente di inferenza](how-to-secure-inferencing-vnet.md)  >  **5. Abilitare la funzionalità di studio**


> [!IMPORTANT]
> Se l'area di lavoro si trova in un __cloud sovrano__, ad esempio Azure per enti pubblici o Azure Cina 21ViaNet, i notebook integrati _non_ supportano l'uso dell'archiviazione che si trova in una rete virtuale. È invece possibile usare Jupyter Notebook da un'istanza di calcolo. Per altre informazioni, vedere la sezione [accedere ai dati in un notebook dell'istanza di calcolo](how-to-secure-training-vnet.md#access-data-in-a-compute-instance-notebook) .


## <a name="prerequisites"></a>Prerequisiti

+ Leggere la [Panoramica della sicurezza di rete](how-to-network-security-overview.md) per comprendere gli scenari e l'architettura comuni della rete virtuale.

+ Una rete virtuale e una subnet preesistenti da usare.

+ Un' [area di lavoro Azure Machine Learning esistente con collegamento privato abilitato](how-to-secure-workspace-vnet.md#secure-the-workspace-with-private-endpoint).

+ Un account di archiviazione di Azure esistente ha [aggiunto la rete virtuale](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints).

## <a name="configure-data-access-in-the-studio"></a>Configurare l'accesso ai dati in studio

Alcune delle funzionalità di studio sono disabilitate per impostazione predefinita in una rete virtuale. Per abilitare di nuovo queste funzionalità, è necessario abilitare l'identità gestita per gli account di archiviazione che si intende usare in studio. 

Le operazioni seguenti sono disabilitate per impostazione predefinita in una rete virtuale:

* Visualizzare in anteprima i dati in studio.
* Visualizza i dati nella finestra di progettazione.
* Distribuire un modello nella finestra di progettazione ([account di archiviazione predefinito](#enable-managed-identity-authentication-for-default-storage-accounts)).
* Inviare un esperimento AutoML ([account di archiviazione predefinito](#enable-managed-identity-authentication-for-default-storage-accounts)).
* Avviare un progetto di assegnazione di etichette.

Studio supporta la lettura dei dati dai seguenti tipi di archivio dati in una rete virtuale:

* BLOB Azure
* Azure Data Lake Storage Gen1
* Azure Data Lake Storage Gen2
* Database SQL di Azure

### <a name="configure-datastores-to-use-workspace-managed-identity"></a>Configurare gli archivi dati per usare l'identità gestita dall'area di lavoro

Dopo aver aggiunto un account di archiviazione di Azure alla rete virtuale con un [endpoint del servizio](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints) o un [endpoint privato](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-private-endpoints), è necessario configurare l'archivio dati per l'uso dell'autenticazione basata sull' [identità gestita](../active-directory/managed-identities-azure-resources/overview.md) . Questa operazione consente di accedere ai dati dell'account di archiviazione in studio.

Azure Machine Learning usa gli [archivi dati](concept-data.md#datastores) per connettersi agli account di archiviazione. Usare la procedura seguente per configurare un archivio dati per usare l'identità gestita:

1. In Studio selezionare __datastores__.

1. Per aggiornare un archivio dati esistente, selezionare l'archivio dati e selezionare __Aggiorna credenziali__.

    Per creare un nuovo archivio dati, selezionare __+ nuovo archivio dati__.

1. Nelle impostazioni dell'archivio dati selezionare __Sì__ per  __Usa identità gestita dell'area di lavoro per l'anteprima dati e la profilatura in Azure Machine Learning Studio__.

    ![Screenshot che illustra come abilitare l'identità dell'area di lavoro gestita](./media/how-to-enable-studio-virtual-network/enable-managed-identity.png)

Questi passaggi aggiungono l'identità gestita dell'area di lavoro come __lettore__ al servizio di archiviazione usando il controllo degli accessi in base al ruolo Accesso __Reader__ consente all'area di lavoro di recuperare le impostazioni del firewall per assicurarsi che i dati non lascino la rete virtuale. Per rendere effettive le modifiche potrebbero essere necessari fino a 10 minuti.

### <a name="enable-managed-identity-authentication-for-default-storage-accounts"></a>Abilitare l'autenticazione dell'identità gestita per gli account di archiviazione predefiniti

Ogni area di lavoro Azure Machine Learning viene fornita con due account di archiviazione predefiniti, che vengono definiti quando si crea l'area di lavoro. Lo studio usa gli account di archiviazione predefiniti per archiviare gli artefatti dell'esperimento e del modello, che sono fondamentali per determinate funzionalità in studio.

La tabella seguente descrive il motivo per cui è necessario abilitare l'autenticazione dell'identità gestita per gli account di archiviazione predefiniti dell'area di lavoro.

|Account di archiviazione  | Note  |
|---------|---------|
|Archivio BLOB predefinito dell'area di lavoro| Archivia gli asset del modello dalla finestra di progettazione. Per distribuire i modelli nella finestra di progettazione, è necessario abilitare l'autenticazione dell'identità gestita in questo account di archiviazione. <br> <br> È possibile visualizzare ed eseguire una pipeline di progettazione se utilizza un archivio dati non predefinito configurato per l'utilizzo dell'identità gestita. Tuttavia, se si tenta di distribuire un modello sottoposto a training senza l'identità gestita abilitata nell'archivio dati predefinito, la distribuzione avrà esito negativo indipendentemente da qualsiasi altro archivio dati in uso.|
|Archivio file predefinito dell'area di lavoro| Archivia le risorse dell'esperimento AutoML. Per inviare esperimenti AutoML, è necessario abilitare l'autenticazione dell'identità gestita in questo account di archiviazione. |


![Screenshot che mostra la posizione in cui è possibile trovare gli archivi dati predefiniti](./media/how-to-enable-studio-virtual-network/default-datastores.png)


### <a name="grant-workspace-managed-identity-__reader__-access-to-storage-private-link"></a>Concessione dell'accesso al __lettore__ di identità gestito dell'area di lavoro al collegamento privato di archiviazione

Se l'account di archiviazione di Azure usa un endpoint privato, è necessario concedere l'accesso del **lettore** di identità gestito dall'area di lavoro al collegamento privato. Per ulteriori informazioni, vedere ruolo predefinito [Reader](../role-based-access-control/built-in-roles.md#reader) . 

Se l'account di archiviazione usa un endpoint del servizio, è possibile ignorare questo passaggio.

## <a name="access-the-studio-from-a-resource-inside-the-vnet"></a>Accedere a Studio da una risorsa all'interno di VNet

Se si accede a Studio da una risorsa all'interno di una rete virtuale, ad esempio un'istanza di calcolo o una macchina virtuale, è necessario consentire il traffico in uscita dalla rete virtuale a Studio. 

Ad esempio, se si usano gruppi di sicurezza di rete (NSG) per limitare il traffico in uscita, aggiungere una regola a una destinazione di __tag del servizio__ di __AzureFrontDoor. frontend__.

## <a name="technical-notes-for-managed-identity"></a>Note tecniche per l'identità gestita

L'uso dell'identità gestita per accedere ai servizi di archiviazione influisca sulle considerazioni sulla sicurezza. Questa sezione descrive le modifiche per ogni tipo di account di archiviazione. 

Queste considerazioni sono univoche per il __tipo di account di archiviazione__ a cui si accede.

### <a name="azure-blob-storage"></a>Archiviazione BLOB di Azure

Per l' __archiviazione BLOB di Azure__, l'identità gestita dall'area di lavoro viene aggiunta anche come [lettore di dati BLOB](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) in modo che possa leggere i dati dall'archivio BLOB.

### <a name="azure-data-lake-storage-gen2-access-control"></a>Controllo di accesso Azure Data Lake Storage Gen2

Per controllare l'accesso ai dati all'interno di una rete virtuale, è possibile usare gli elenchi di controllo di accesso (ACL) e gli elenchi di controllo di accesso di tipo POSIX.

Per usare il controllo degli accessi in base al ruolo di Azure, aggiungere l'identità gestita dall'area di lavoro al ruolo [lettore dati BLOB](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) Per altre informazioni, vedere [controllo degli accessi in base al ruolo di Azure](../storage/blobs/data-lake-storage-access-control-model.md#role-based-access-control).

Per usare gli ACL, è possibile assegnare l'accesso all'identità gestita dall'area di lavoro in modo analogo a qualsiasi altro principio di sicurezza. Per altre informazioni, vedere [elenchi di controllo di accesso per file e directory](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

### <a name="azure-data-lake-storage-gen1-access-control"></a>Controllo di accesso Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 supporta solo gli elenchi di controllo di accesso in stile POSIX. È possibile assegnare l'accesso di identità gestito dall'area di lavoro alle risorse come qualsiasi altro principio di sicurezza. Per ulteriori informazioni, vedere [controllo di accesso in Azure Data Lake storage Gen1](../data-lake-store/data-lake-store-access-control.md).

### <a name="azure-sql-database-contained-user"></a>Utente indipendente del database SQL di Azure

Per accedere ai dati archiviati in un database SQL di Azure tramite identità gestita, è necessario creare un utente indipendente di SQL che esegue il mapping all'identità gestita. Per altre informazioni sulla creazione di un utente da un provider esterno, vedere [creare utenti indipendenti con mapping a identità Azure ad](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities).

Dopo aver creato un utente indipendente di SQL, concedere le autorizzazioni tramite il [comando T-SQL Grant](/sql/t-sql/statements/grant-object-permissions-transact-sql).

### <a name="azure-machine-learning-designer-intermediate-module-output"></a>Output del modulo intermedio di Azure Machine Learning Designer

È possibile specificare il percorso di output per qualsiasi modulo nella finestra di progettazione. Usare questa funzionalità per archiviare i set di dati intermedi in un percorso separato a scopo di sicurezza, registrazione o controllo. Per specificare l'output:

1. Selezionare il modulo di cui si vuole specificare l'output.
1. Nel riquadro Impostazioni modulo visualizzato a destra selezionare **impostazioni di output**.
1. Specificare l'archivio dati che si vuole usare per ogni output del modulo.
 
Assicurarsi di avere accesso agli account di archiviazione intermedi nella rete virtuale. In caso contrario, la pipeline avrà esito negativo.

È anche necessario [abilitare l'autenticazione di identità gestita](#configure-datastores-to-use-workspace-managed-identity) per gli account di archiviazione intermedi per visualizzare i dati di output.

## <a name="next-steps"></a>Passaggi successivi

Questo articolo è una parte facoltativa di una serie di reti virtuali in quattro parti. Vedere il resto degli articoli per informazioni su come proteggere una rete virtuale:

* [Parte 1: Panoramica di rete virtuale](how-to-network-security-overview.md)
* [Parte 2: proteggere le risorse dell'area di lavoro](how-to-secure-workspace-vnet.md)
* [Parte 3: proteggere l'ambiente di training](how-to-secure-training-vnet.md)
* [Parte 4: proteggere l'ambiente di inferenza](how-to-secure-inferencing-vnet.md)