---
title: Accesso ai dati basati su identità per i servizi di archiviazione in Azure
titleSuffix: Machine Learning
description: Informazioni su come usare l'accesso ai dati basato su identità per connettersi ai servizi di archiviazione in Azure con Azure Machine Learning archivi dati e Python SDK di Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 02/22/2021
ms.custom: how-to, contperf-fy21q1, devx-track-python, data4ml
ms.openlocfilehash: a46f54bd037dcf8d71ba3fbafb2ba0fd961a32cc
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107210653"
---
# <a name="connect-to-storage-by-using-identity-based-data-access-preview"></a>Connettersi all'archiviazione usando l'accesso ai dati basato su identità (anteprima)

>[!IMPORTANT]
> Le funzionalità descritte in questo articolo sono disponibili in anteprima. Devono essere considerati funzionalità di anteprima [sperimentale](/python/api/overview/azure/ml/#stable-vs-experimental) che potrebbero cambiare in qualsiasi momento.

In questo articolo si apprenderà come connettersi ai servizi di archiviazione in Azure usando l'accesso ai dati basato su identità e Azure Machine Learning archivi dati tramite [Azure Machine Learning SDK per Python](/python/api/overview/azure/ml/intro).  

In genere, gli archivi dati utilizzano l'accesso ai dati basato su credenziali per verificare di disporre dell'autorizzazione per accedere al servizio di archiviazione. Mantengono le informazioni di connessione, ad esempio l'ID sottoscrizione e l'autorizzazione del token, nell'insieme di credenziali delle [chiavi](https://azure.microsoft.com/services/key-vault/) associato all'area di lavoro. Quando si crea un archivio dati che usa l'accesso ai dati basato su identità, viene usato l'account di Azure ([token Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)) per confermare di avere l'autorizzazione per accedere al servizio di archiviazione. In questo scenario non viene salvata alcuna credenziale di autenticazione. Solo le informazioni sull'account di archiviazione vengono archiviate nell'archivio dati. 

Per creare archivi dati che usano l'autenticazione basata su credenziali, come le chiavi di accesso o le entità servizio, vedere [connettersi ai servizi di archiviazione in Azure](how-to-access-data.md).

## <a name="identity-based-data-access-in-azure-machine-learning"></a>Accesso ai dati basato su identità in Azure Machine Learning

Esistono due scenari in cui è possibile applicare l'accesso ai dati basato su identità in Azure Machine Learning. Questi scenari sono ideali per l'accesso basato su identità quando si lavora con dati riservati e si necessita di una gestione di accesso ai dati più granulare:

- Accesso ai servizi di archiviazione
- Training di modelli di Machine Learning con dati privati

### <a name="accessing-storage-services"></a>Accesso ai servizi di archiviazione

È possibile connettersi ai servizi di archiviazione tramite l'accesso ai dati basato su identità con Azure Machine Learning archivi dati o set di dati di [Azure Machine Learning](how-to-create-register-datasets.md). 

Le credenziali di autenticazione vengono in genere mantenute in un archivio dati, che viene usato per assicurarsi di disporre dell'autorizzazione per accedere al servizio di archiviazione. Quando queste credenziali vengono registrate tramite archivi dati, qualsiasi utente con il ruolo di lettore dell'area di lavoro può recuperarle. Tale scala di accesso può costituire un problema di sicurezza per alcune organizzazioni. [Ulteriori informazioni sul ruolo Lettore area di lavoro.](how-to-assign-roles.md#default-roles) 

Quando si usa l'accesso ai dati basato su identità, Azure Machine Learning richiede il token Azure Active Directory per l'autenticazione dell'accesso ai dati invece di mantenere le credenziali nell'archivio dati. Questo approccio consente la gestione dell'accesso ai dati a livello di archiviazione e mantiene riservate le credenziali. 

Lo stesso comportamento si applica quando si:

* [Creare un set di dati direttamente dagli URL di archiviazione](#use-data-in-storage). 
* Usare i dati in modo interattivo tramite un Jupyter Notebook nel computer locale o nell' [istanza di calcolo](concept-compute-instance.md).

> [!NOTE]
> Le credenziali archiviate tramite l'autenticazione basata sulle credenziali includono gli ID sottoscrizione, i token di firma di accesso condiviso e le informazioni sull'entità servizio e la chiave di accesso alle archiviazioni, come ID client e ID tenant.

### <a name="model-training-on-private-data"></a>Training di modelli per dati privati

Alcuni scenari di apprendimento automatico coinvolgono modelli di training con dati privati. In questi casi, i data scientist devono eseguire flussi di lavoro di training senza essere esposti ai dati di input riservati. In questo scenario, per l'autenticazione dell'accesso ai dati viene utilizzata un'identità gestita del calcolo di training. Questo approccio consente agli amministratori di archiviazione di concedere l'accesso in lettura ai dati BLOB di archiviazione all'identità gestita usata dal calcolo di training per eseguire il processo di training. Ai singoli data scientist non è necessario concedere l'accesso. Per altre informazioni, vedere [configurare l'identità gestita in un cluster di elaborazione](how-to-create-attach-compute-cluster.md#managed-identity).


## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

- Un account di archiviazione di Azure con un tipo di archiviazione supportato. Questi tipi di archiviazione sono supportati in anteprima: 
    - [Archiviazione BLOB di Azure](../storage/blobs/storage-blobs-overview.md)
    - [Azure Data Lake Storage Gen1](../data-lake-store/index.yml)
    - [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
    - [Database SQL di Azure](../azure-sql/database/sql-database-paas-overview.md)

- [SDK Azure Machine Learning per Python](/python/api/overview/azure/ml/install).

- Un'area di lavoro di Azure Machine Learning.
  
  [Creare un'area di lavoro Azure Machine Learning](how-to-manage-workspace.md) o utilizzarne una [esistente tramite Python SDK](how-to-manage-workspace.md#connect-to-a-workspace). 

## <a name="storage-access-permissions"></a>Autorizzazioni di accesso alle archiviazione

Per garantire la connessione sicura al servizio di archiviazione in Azure, Azure Machine Learning richiede l'autorizzazione ad accedere all'archivio dati corrispondente.

L'accesso ai dati basato su identità supporta le connessioni solo ai servizi di archiviazione seguenti:

* Archiviazione BLOB di Azure
* Azure Data Lake Storage Gen1
* Azure Data Lake Storage Gen2
* Database SQL di Azure

Per accedere a questi servizi di archiviazione, è necessario disporre almeno dell'accesso in [lettura dati BLOB di archiviazione](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) . Solo i proprietari dell'account [di archiviazione possono modificare il livello di accesso tramite il portale di Azure](../storage/common/storage-auth-aad-rbac-portal.md).

Se si sta eseguendo il training di un modello in una destinazione di calcolo remota, è necessario concedere all'identità di calcolo almeno il ruolo lettore dati BLOB di archiviazione dal servizio di archiviazione. Informazioni su come [configurare l'identità gestita in un cluster di elaborazione](how-to-create-attach-compute-cluster.md#managed-identity).

## <a name="work-with-virtual-networks"></a>Usare le reti virtuali

Per impostazione predefinita, Azure Machine Learning non è in grado di comunicare con un account di archiviazione che si trova dietro un firewall o in una rete virtuale.

È possibile configurare gli account di archiviazione per consentire l'accesso solo da reti virtuali specifiche. Questa configurazione richiede passaggi aggiuntivi per garantire che i dati non vengano persi all'esterno della rete. Questo comportamento è identico per l'accesso ai dati basato sulle credenziali. Per ulteriori informazioni, vedere [How to Configure Virtual Network Scenarios](how-to-access-data.md#virtual-network). 

## <a name="create-and-register-datastores"></a>Creare e registrare archivi dati

Quando si registra un servizio di archiviazione in Azure come archivio dati, l'archivio dati viene creato e registrato automaticamente in un'area di lavoro specifica. Vedere [autorizzazioni di accesso](#storage-access-permissions) alla risorsa di archiviazione per indicazioni sui tipi di autorizzazioni richiesti. Per informazioni dettagliate su come connettersi all'archiviazione dei dati dietro le reti virtuali, vedere [usare le reti virtuali](#work-with-virtual-networks) .

Nel codice seguente si noti l'assenza di parametri di autenticazione come `sas_token` , `account_key` , `subscription_id` e l'entità servizio `client_id` . Questa omissione indica che Azure Machine Learning utilizzerà l'accesso ai dati basato su identità per l'autenticazione. La creazione di archivi dati avviene in genere in modo interattivo in un notebook o tramite Studio. Il token di Azure Active Directory viene quindi usato per l'autenticazione dell'accesso ai dati.

> [!NOTE]
> I nomi degli archivi dati devono essere costituiti solo da lettere minuscole, numeri e caratteri di sottolineatura. 

### <a name="azure-blob-container"></a>Contenitore BLOB di Azure

Per registrare un contenitore BLOB di Azure come archivio dati, usare [`register_azure_blob_container()`](/python/api/azureml-core/azureml.core.datastore%28class%29#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-).

Il codice seguente consente di creare l' `credentialless_blob` archivio dati, di registrarlo nell' `ws` area di lavoro e di assegnarlo alla `blob_datastore` variabile. Questo archivio dati accede al `my_container_name` contenitore BLOB nell'account di `my-account-name` archiviazione.

```Python
# Create blob datastore without credentials.
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,
                                                      datastore_name='credentialless_blob',
                                                      container_name='my_container_name',
                                                      account_name='my_account_name')
```

### <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

Usare [register_azure_data_lake ()](/python/api/azureml-core/azureml.core.datastore.datastore#register-azure-data-lake-workspace--datastore-name--store-name--tenant-id-none--client-id-none--client-secret-none--resource-url-none--authority-url-none--subscription-id-none--resource-group-none--overwrite-false--grant-workspace-access-false-) per registrare un archivio dati che si connette a Azure Data Lake storage Gen1.

Il codice seguente consente di creare l' `credentialless_adls1` archivio dati, di registrarlo nell' `workspace` area di lavoro e di assegnarlo alla `adls_dstore` variabile. Questo archivio dati accede all' `adls_storage` account Azure Data Lake storage.

```Python
# Create Azure Data Lake Storage Gen1 datastore without credentials.
adls_dstore = Datastore.register_azure_data_lake(workspace = workspace,
                                                 datastore_name='credentialless_adls1',
                                                 store_name='adls_storage')

```

### <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

Usare [register_azure_data_lake_gen2 ()](/python/api/azureml-core/azureml.core.datastore.datastore#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) per registrare un archivio dati che si connette a Azure Data Lake storage Gen2.

Il codice seguente consente di creare l' `credentialless_adls2` archivio dati, di registrarlo nell' `ws` area di lavoro e di assegnarlo alla `adls2_dstore` variabile. Questo archivio dati accede al file system `tabular` nell'account di `myadls2` archiviazione.  

```python
# Create Azure Data Lake Storage Gen2 datastore without credentials.
adls2_dstore = Datastore.register_azure_data_lake_gen2(workspace=ws, 
                                                       datastore_name='credentialless_adls2', 
                                                       filesystem='tabular', 
                                                       account_name='myadls2')
```

## <a name="use-data-in-storage"></a>Usare i dati nell'archiviazione

Si consiglia di usare [Azure Machine Learning set](how-to-create-register-datasets.md) di dati quando si interagisce con i dati nell'archiviazione con Azure Machine Learning. 

I set di dati impacchettano i dati in un oggetto utilizzabile differito per le attività di Machine Learning, ad esempio il training. Inoltre, con i set di dati è possibile [scaricare o montare](how-to-train-with-datasets.md#mount-vs-download) file di qualsiasi formato da servizi di archiviazione di Azure, ad esempio archiviazione BLOB di azure e Azure Data Lake storage a una destinazione di calcolo.


Per creare set di dati con accesso ai dati basato su identità, sono disponibili le opzioni seguenti. Questo tipo di creazione del set di dati usa il token di Azure Active Directory per l'autenticazione di accesso ai dati. 

*  Percorsi di riferimento da archivi dati che utilizzano anche l'accesso ai dati basato su identità. 
<br>Nell'esempio seguente, `blob_datastore` esiste già e usa l'accesso ai dati basato su identità.   

    ```python
    blob_dataset = Dataset.Tabular.from_delimited_files(blob_datastore,'test.csv') 
    ```

* Ignorare la creazione dell'archivio dati e creare set di dati direttamente da URL di archiviazione. Questa funzionalità supporta attualmente solo i BLOB di Azure e Azure Data Lake Storage Gen1 e Gen2.

    ```python
    blob_dset = Dataset.File.from_files('https://myblob.blob.core.windows.net/may/keras-mnist-fashion/')
    ```

Quando si invia un processo di training che utilizza un set di dati creato con accesso ai dati basato su identità, l'identità gestita del calcolo di training viene utilizzata per l'autenticazione di accesso ai dati. Il token di Azure Active Directory non viene usato. Per questo scenario, assicurarsi che all'identità gestita del calcolo sia concesso almeno il ruolo lettore dati BLOB di archiviazione dal servizio di archiviazione. Per altre informazioni, vedere [configurare l'identità gestita nei cluster di calcolo](how-to-create-attach-compute-cluster.md#managed-identity). 

## <a name="next-steps"></a>Passaggi successivi

* [Creare un set di dati Azure Machine Learning](how-to-create-register-datasets.md)
* [Eseguire il training con set di dati](how-to-train-with-datasets.md)
* [Creare un archivio dati con accesso ai dati basato su chiavi](how-to-access-data.md)
