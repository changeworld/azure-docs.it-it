---
title: Accesso ai dati basati su identità per i servizi di archiviazione in Azure
titleSuffix: Azure Machine Learning
description: Informazioni su come usare l'accesso ai dati basato su identità per connettersi ai servizi di archiviazione in Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 02/22/2021
ms.custom: how-to, contperf-fy21q1, devx-track-python, data4ml
ms.openlocfilehash: 68d07481e228b1d1b2f4571a783f925add261cff
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102520014"
---
# <a name="connect-to-storage-with-identity-based-data-access-preview"></a>Connettersi all'archiviazione con accesso ai dati basato su identità (anteprima)

>[!IMPORTANT]
> Le funzionalità presentate in questo articolo sono in anteprima e devono essere considerate funzionalità di anteprima [sperimentali](/python/api/overview/azure/ml/#stable-vs-experimental) che possono cambiare in qualsiasi momento.

In questo articolo si apprenderà come connettersi ai servizi di archiviazione in Azure con l'accesso ai dati basato su identità e Azure Machine Learning archivi dati tramite l' [SDK Azure Machine Learning Python](/python/api/overview/azure/ml/intro).  

In genere, gli archivi dati utilizzano l'accesso ai dati basato su credenziali per verificare di disporre dell'autorizzazione per accedere al servizio di archiviazione. Mantengono le informazioni di connessione, ad esempio l'ID sottoscrizione e l'autorizzazione del token, nel [Key Vault](https://azure.microsoft.com/services/key-vault/) associato all'area di lavoro. Quando si crea un archivio dati che usa l'accesso ai dati basato su identità, viene usato l'account di accesso di Azure ([token Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)) per confermare di avere l'autorizzazione per accedere al servizio di archiviazione. In questo scenario non viene salvata alcuna credenziale di autenticazione e solo le informazioni sull'account di archiviazione vengono archiviate nell'archivio dati. 

Per creare archivi dati che usano l'autenticazione basata su credenziali, ad esempio con chiavi di accesso o entità servizio, vedere [connettersi ai servizi di archiviazione in Azure](how-to-access-data.md).

## <a name="identity-based-data-access-in-azure-machine-learning"></a>Accesso ai dati basato su identità in Azure Machine Learning

Esistono due aree per applicare l'accesso ai dati basato su identità in Azure Machine Learning. In particolare, quando si utilizzano dati riservati e si necessita di una maggiore granularità di gestione dell'accesso ai dati. 

1. Accesso ai servizi di archiviazione.
1. Training di modelli di Machine Learning con dati privati.

### <a name="accessing-storage-services"></a>Accesso ai servizi di archiviazione

È possibile connettersi ai servizi di archiviazione tramite l'accesso ai dati basato su identità con Azure Machine Learning archivi dati o set di dati di [Azure Machine Learning](how-to-create-register-datasets.md). 

In genere, le credenziali di autenticazione vengono mantenute in un archivio dati, che viene usato per assicurarsi di disporre dell'autorizzazione per accedere al servizio di archiviazione. Quando queste credenziali vengono registrate con gli archivi dati, qualsiasi utente con il ruolo di *lettore* dell'area di lavoro è in grado di recuperarle, che può costituire un problema di sicurezza per alcune organizzazioni. Ulteriori informazioni [sul ruolo *lettore* area di lavoro](how-to-assign-roles.md#default-roles). 

Quando si usa l'accesso ai dati basato su identità, Azure Machine Learning richiede il token Azure Active Directory per l'autenticazione dell'accesso ai dati, anziché mantenere le credenziali nell'archivio dati. Che consente la gestione dell'accesso ai dati a livello di archiviazione e mantiene riservate le credenziali. 

Lo stesso comportamento si applica quando,

* [Creare un set di dati direttamente dagli URL di archiviazione](#use-data-in-storage). 
* Usare i dati in modo interattivo tramite un notebook di Jupyter nel computer locale o nell' [istanza di calcolo](concept-compute-instance.md).

> [!NOTE]
> Le credenziali archiviate con l'autenticazione basata sulle credenziali includono: ID sottoscrizione, token di firma di accesso condiviso, chiavi di accesso di archiviazione e informazioni sull'entità servizio, ad esempio ID client e ID tenant.

### <a name="model-training-on-private-data"></a>Training di modelli per dati privati

Alcuni scenari di apprendimento automatico coinvolgono modelli di training con dati privati. In questi casi, i data scientist devono eseguire flussi di lavoro di training senza alcuna esposizione ai dati di input riservati. In questo scenario, per l'autenticazione dell'accesso ai dati viene utilizzata un'identità gestita del calcolo di training. In questo modo, gli amministratori di archiviazione possono concedere al **lettore di dati BLOB di archiviazione** l'accesso all'identità gestita usata dal calcolo di training per eseguire il processo di training, invece dei singoli data scientist. Informazioni su come [configurare l'identità gestita in un calcolo](how-to-create-attach-compute-cluster.md#managed-identity).


## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

- Un account di archiviazione di Azure con un tipo di archiviazione supportato. I tipi di archiviazione seguenti sono supportati nella versione di anteprima. 
    - [Archiviazione BLOB di Azure](../storage/blobs/storage-blobs-overview.md)
    - [Azure Data Lake generazione 1](../data-lake-store/index.yml)
    - [Azure Data Lake generazione 2](../storage/blobs/data-lake-storage-introduction.md)
    - [Database SQL di Azure](../azure-sql/database/sql-database-paas-overview.md)

- [SDK Azure Machine Learning per Python](/python/api/overview/azure/ml/install).

- Un'area di lavoro di Azure Machine Learning.
  
  [Creare un'area di lavoro Azure Machine Learning](how-to-manage-workspace.md) o utilizzarne una [esistente tramite Python SDK](how-to-manage-workspace.md#connect-to-a-workspace). 

## <a name="storage-access-permissions"></a>Autorizzazioni di accesso alle archiviazione

Per garantire la connessione sicura al servizio di archiviazione in Azure, Azure Machine Learning richiede l'autorizzazione ad accedere all'archivio dati corrispondente.

L'accesso ai dati basato su identità supporta solo connessioni ai servizi di archiviazione seguenti:

* Archiviazione BLOB di Azure
* Azure Data Lake generazione 1
* Azure Data Lake generazione 2
* Database SQL di Azure

Per accedere a questi servizi di archiviazione, è necessario disporre dell'accesso al **lettore dati BLOB di archiviazione** minimo. Altre informazioni sul [lettore di dati BLOB di archiviazione](../role-based-access-control/built-in-roles.md#storage-blob-data-reader). Solo i proprietari dell'account [di archiviazione possono modificare il livello di accesso tramite il portale di Azure](../storage/common/storage-auth-aad-rbac-portal.md).

Se si esegue il training di un modello in una destinazione di calcolo remota, è necessario concedere all'identità di calcolo almeno il ruolo **lettore dati BLOB di archiviazione** dal servizio di archiviazione. Informazioni su come [configurare l'identità gestita per il calcolo](how-to-create-attach-compute-cluster.md#managed-identity).

## <a name="work-with-virtual-networks"></a>Usare le reti virtuali

Per impostazione predefinita, Azure Machine Learning non è in grado di comunicare con un account di archiviazione che si trova dietro un firewall o in una rete virtuale.

Gli account di archiviazione possono essere configurati per consentire l'accesso solo da reti virtuali specifiche, che richiedono configurazioni aggiuntive per garantire che i dati non vengano persi all'esterno della rete. Questo comportamento è identico per l'accesso ai dati basato su credenziali, vedere [quali configurazioni sono necessarie e come applicarle per gli scenari di rete virtuale](how-to-access-data.md#virtual-network). 

## <a name="create-and-register-datastores"></a>Creare e registrare archivi dati

Quando si registra un servizio di archiviazione in Azure come archivio dati, l'archivio dati viene creato e registrato automaticamente in un'area di lavoro specifica. Per informazioni dettagliate su come connettersi all'archiviazione dei dati dietro le reti virtuali, vedere le sezioni relative alle [autorizzazioni di accesso](#storage-access-permissions) alla risorsa di archiviazione per le linee guida sui tipi di autorizzazioni necessari e all' [uso della rete virtuale](#work-with-virtual-networks) .

Nel codice seguente si noti l'assenza di parametri di autenticazione, ad esempio `sas_token` ,, `account_key` o l' `subscription_id` entità servizio `client_id` . Questa omissione indica che Azure Machine Learning prevede l'uso dell'accesso ai dati basato su identità a per l'autenticazione. Poiché la creazione di archivi dati avviene in genere in modo interattivo in un notebook o tramite studio, il token Azure Active Directory viene usato per l'autenticazione di accesso ai dati.

> [!NOTE]
> I nomi degli archivi dati devono essere costituiti solo da lettere minuscole, cifre e caratteri di sottolineatura. 

### <a name="azure-blob-container"></a>Contenitore BLOB di Azure

Per registrare un contenitore BLOB di Azure come archivio dati, usare [`register_azure_blob_container()`](/python/api/azureml-core/azureml.core.datastore%28class%29#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-).

Il codice seguente crea e registra l' `credentialless_blob` archivio dati nell' `ws` area di lavoro e lo assegna alla variabile, `blob_datastore` . Questo archivio dati accede al `my_container_name` contenitore BLOB nell'account di `my-account-name` archiviazione.

```Python
# create blob datastore without credentials
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,
                                                      datastore_name='credentialless_blob',
                                                      container_name='my_container_name',
                                                      account_name='my_account_name')
```

### <a name="azure-data-lake-storage-generation-1"></a>Azure Data Lake Storage generazione 1

Per un archivio dati Azure Data Lake Storage generazione 1 (ADLS gen 1), usare [register_azure_data_lake ()](/python/api/azureml-core/azureml.core.datastore.datastore#register-azure-data-lake-workspace--datastore-name--store-name--tenant-id-none--client-id-none--client-secret-none--resource-url-none--authority-url-none--subscription-id-none--resource-group-none--overwrite-false--grant-workspace-access-false-) per registrare un archivio dati che si connette a una risorsa di archiviazione di Azure datalake di generazione 1.

Il codice seguente crea e registra l' `credentialless_adls1` archivio dati nell' `workspace` area di lavoro e lo assegna alla variabile, `adls_dstore` . Questo archivio dati accede all' `adls_storage` account di archiviazione Azure Data Lake Store.

```Python
# create adls gen1 without credentials
adls_dstore = Datastore.register_azure_data_lake(workspace = workspace,
                                                 datastore_name='credentialless_adls1',
                                                 store_name='adls_storage')

```

### <a name="azure-data-lake-storage-generation-2"></a>Azure Data Lake Storage Gen2

Per un archivio dati di Azure Data Lake Storage generazione 2 (ADLS gen 2), usare [register_azure_data_lake_gen2 ()](/python/api/azureml-core/azureml.core.datastore.datastore#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) per registrare un archivio dati che si connette a una risorsa di archiviazione di Azure datalake gen 2.

Il codice seguente crea e registra l' `credentialless_adls2` archivio dati nell' `ws` area di lavoro e lo assegna alla variabile, `adls2_dstore` . Questo archivio dati accede al file system `tabular` nell'account di `myadls2` archiviazione.  

```python
# createn adls2 datastore without credentials
adls2_dstore = Datastore.register_azure_data_lake_gen2(workspace=ws, 
                                                       datastore_name='credentialless_adls2', 
                                                       filesystem='tabular', 
                                                       account_name='myadls2')
```

## <a name="use-data-in-storage"></a>Usare i dati nell'archiviazione

[Azure Machine Learning set](how-to-create-register-datasets.md) di dati rappresentano la modalità consigliata per interagire con i dati nell'archiviazione con Azure Machine Learning. 

I set di dati impacchettano i dati in un oggetto utilizzabile differito per le attività di Machine Learning, ad esempio il training. Inoltre, con i set di dati è possibile [scaricare o montare](how-to-train-with-datasets.md#mount-vs-download) file di qualsiasi formato da servizi di archiviazione di Azure, ad esempio archiviazione BLOB di Azure e Azure Data Lake, a una destinazione di calcolo.


**Per creare set di dati con accesso ai dati basato su identità**, sono disponibili le opzioni seguenti. Questo tipo di creazione del set di dati usa il token di Azure Active Directory per l'autenticazione dell'accesso ai dati. 

*  Percorsi di riferimento da archivi dati che utilizzano anche l'accesso ai dati basato su identità. 
<br>Nell'esempio seguente, `blob_datastore` è stato creato in precedenza usando l'accesso ai dati basato su identità.   

    ```python
    blob_dataset = Dataset.Tabular.from_delimited_files(blob_datastore,'test.csv') 
    ```

* Ignorare la creazione dell'archivio dati e creare set di dati direttamente da URL di archiviazione. Attualmente questa funzionalità supporta solo BLOB di Azure e Azure Data Lake Storage generazioni 1 e 2.

    ```python
    blob_dset = Dataset.File.from_files('https://myblob.blob.core.windows.net/may/keras-mnist-fashion/')
    ```

**Tuttavia, quando si invia un processo di training che utilizza un set di dati creato con accesso ai dati basato su identità**, l'identità gestita del calcolo di training viene utilizzata per l'autenticazione di accesso ai dati, anziché il token Azure Active Directory. Per questo scenario, assicurarsi che l'identità gestita del calcolo venga concessa con almeno il ruolo **lettore dati BLOB di archiviazione** dal servizio di archiviazione. Informazioni su come [configurare l'identità gestita per il calcolo](how-to-create-attach-compute-cluster.md#managed-identity). 

## <a name="next-steps"></a>Passaggi successivi

* [Creare un set di dati di Azure Machine Learning](how-to-create-register-datasets.md).
* Eseguire il [training con i set di impostazioni](how-to-train-with-datasets.md).
* [Creare un archivio dati con accesso ai dati basato su chiave](how-to-access-data.md).
