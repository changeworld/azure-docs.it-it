---
title: 'Avvio rapido: Libreria client di Archiviazione BLOB di Azure - Ruby'
description: Creare un account di archiviazione e un contenitore in Archiviazione BLOB di Azure. Usare la libreria client di archiviazione per Ruby per creare un BLOB, scaricare un BLOB ed elencare i BLOB presenti in un contenitore.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/04/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: ec3fc490466f5fce36b67b2f3744e4ee5cc0ae79
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96781097"
---
# <a name="quickstart-azure-blob-storage-client-library-for-ruby"></a>Avvio rapido: Libreria client di Archiviazione BLOB di Azure per Ruby

Questo argomento di avvio rapido illustra come usare Ruby per creare, scaricare ed elencare BLOB in un contenitore di Archiviazione BLOB di Microsoft Azure.

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Verificare che siano installati i prerequisiti aggiuntivi seguenti:

- [Ruby](https://www.ruby-lang.org/en/downloads/)
- [Libreria di Archiviazione di Azure per Ruby](https://github.com/azure/azure-storage-ruby) con il [pacchetto RubyGem](https://rubygems.org/gems/azure-storage-blob):

    ```console
    gem install azure-storage-blob
    ```

## <a name="download-the-sample-application"></a>Scaricare l'applicazione di esempio

L'[applicazione di esempio](https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git) usata in questa guida rapida è un'applicazione Ruby di base.

Usare [Git](https://git-scm.com/) per scaricare una copia dell'applicazione nell'ambiente di sviluppo. Questo comando clona il repository nel computer locale:

```console
git clone https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git
```

Passare alla cartella *storage-blobs-ruby-quickstart* e aprire il file *example.rb* nell'editor di codice.

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Configurare la stringa di connessione di archiviazione

Specificare il nome dell'account di archiviazione e la chiave dell'account per creare un'istanza di [BlobService](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/BlobService) per l'applicazione.

Il codice seguente nel file *example.rb* crea un'istanza di un nuovo oggetto [BlobService](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/BlobService). Sostituire i valori *accountname* e *accountkey* con il nome e la chiave dell'account.

```ruby
# Create a BlobService object
account_name = "accountname"
account_key = "accountkey"

blob_client = Azure::Storage::Blob::BlobService.create(
    storage_account_name: account_name,
    storage_access_key: account_key
)
```

## <a name="run-the-sample"></a>Eseguire l'esempio

L'esempio crea un contenitore in Archiviazione BLOB, crea un nuovo BLOB nel contenitore, elenca i BLOB presenti nel contenitore e scarica il BLOB in un file locale.

Eseguire l'esempio. Ecco un esempio dell'output risultante dall'esecuzione dell'applicazione:

```console
C:\azure-samples\storage-blobs-ruby-quickstart> ruby example.rb

Creating a container: quickstartblobs18cd9ec0-f4ac-4688-a979-75c31a70503e

Creating blob: QuickStart_6f8f29a8-879a-41fb-9db2-0b8595180728.txt

List blobs in the container following continuation token
        Blob name: QuickStart_6f8f29a8-879a-41fb-9db2-0b8595180728.txt

Downloading blob to C:/Users/azureuser/Documents/QuickStart_6f8f29a8-879a-41fb-9db2-0b8595180728.txt

Paused, press the Enter key to delete resources created by the sample and exit the application
```

Quando si preme INVIO per continuare, il programma di esempio elimina il contenitore di archiviazione e il file locale. Prima di continuare, controllare che nella cartella *Documenti* sia presente il file scaricato.

È anche possibile usare [Azure Storage Explorer](https://storageexplorer.com) per visualizzare i file nell'account di archiviazione. Azure Storage Explorer è uno strumento multipiattaforma gratuito che consente di accedere alle informazioni dell'account di archiviazione.

Dopo aver verificato i file, premere INVIO per eliminare i file di test e terminare la demo. Aprire il file *example.rb* per esaminare il codice.

## <a name="understand-the-sample-code"></a>Informazioni sul codice di esempio

Questa sezione descrive in dettaglio il funzionamento del codice di esempio.

### <a name="get-references-to-the-storage-objects"></a>Ottenere i riferimenti agli oggetti di archiviazione

La prima cosa da fare è creare istanze degli oggetti usati per accedere e gestire Archiviazione BLOB. Questi oggetti sono basati l'uno sull'altro. Ognuno viene usato da quello successivo nell'elenco.

- Creare un'istanza dell'oggetto [BlobService](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/BlobService) di Archiviazione di Azure per configurare le credenziali di connessione.
- Creare l'oggetto [Container](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Container/Container), che rappresenta il contenitore a cui si sta accedendo. I contenitori vengono usati per organizzare i BLOB, così come si usano le cartelle nel computer per organizzare i file.

Dopo aver creato l'oggetto contenitore, è possibile creare un oggetto BLOB [Block](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Block) che punta a un BLOB specifico a cui si è interessati. Usare l'oggetto [Block](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Block) per creare, scaricare e copiare BLOB.

> [!IMPORTANT]
> I nomi dei contenitori devono essere in minuscolo. Per altre informazioni sui nomi di contenitori e BLOB, vedere [Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Denominazione e riferimento a contenitori, BLOB e metadati).

Il codice di esempio seguente:

- Crea un nuovo contenitore.
- Imposta le autorizzazioni per il contenitore in modo da rendere i BLOB pubblici. Il contenitore è denominato *quickstartblobs* con l'aggiunta di un ID univoco alla fine.

```ruby
# Create a container
container_name = "quickstartblobs" + SecureRandom.uuid
puts "\nCreating a container: " + container_name
container = blob_client.create_container(container_name)

# Set the permission so the blobs are public
blob_client.set_container_acl(container_name, "container")
```

### <a name="create-a-blob-in-the-container"></a>Creare un BLOB nel contenitore

Archiviazione BLOB supporta BLOB in blocchi, BLOB di accodamento e BLOB di pagine. Per creare un BLOB, chiamare il metodo [create_block_blob](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob#create_block_blob-instance_method) passando i dati per il BLOB.

L'esempio seguente crea un BLOB denominato *QuickStart_* con un ID univoco e l'estensione file *txt* nel contenitore creato in precedenza.

```ruby
# Create a new block blob containing 'Hello, World!'
blob_name = "QuickStart_" + SecureRandom.uuid + ".txt"
blob_data = "Hello, World!"
puts "\nCreating blob: " + blob_name
blob_client.create_block_blob(container.name, blob_name, blob_data)
```

I BLOB in blocchi possono avere dimensioni fino a 4,7 TB ed essere qualsiasi tipo di file, da fogli di calcolo a file video di grandi dimensioni. I BLOB di pagine vengono usati principalmente per i file VHD su cui si basano le macchine virtuali IaaS. I BLOB di accodamento sono comunemente usati per la registrazione, ad esempio quando si vuole scrivere in un file e poi continuare ad aggiungere altre informazioni.

### <a name="list-the-blobs-in-a-container"></a>Elencare i BLOB in un contenitore

Ottenere un elenco di file nel contenitore usando il metodo [list_blobs](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Container#list_blobs-instance_method). Il codice seguente recupera l'elenco dei BLOB e ne visualizza i nomi.

```ruby
# List the blobs in the container
puts "\nList blobs in the container following continuation token"
nextMarker = nil
loop do
    blobs = blob_client.list_blobs(container_name, { marker: nextMarker })
    blobs.each do |blob|
        puts "\tBlob name: #{blob.name}"
    end
    nextMarker = blobs.continuation_token
    break unless nextMarker && !nextMarker.empty?
end
```

### <a name="download-a-blob"></a>Scaricare un BLOB

Scaricare un BLOB nel disco locale usando il metodo [get_blob](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob#get_blob-instance_method). Il codice seguente scarica il BLOB creato in una sezione precedente.

```ruby
# Download the blob

# Set the path to the local folder for downloading
if(is_windows)
    local_path = File.expand_path("~/Documents")
else 
    local_path = File.expand_path("~/")
end

# Create the full path to the downloaded file
full_path_to_file = File.join(local_path, blob_name)

puts "\nDownloading blob to " + full_path_to_file
blob, content = blob_client.get_blob(container_name, blob_name)
File.open(full_path_to_file,"wb") {|f| f.write(content)}
```

### <a name="clean-up-resources"></a>Pulire le risorse

Se un BLOB non serve più, usare [delete_blob](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob#delete_blob-instance_method) per rimuoverlo. Per eliminare un intero contenitore, usare il metodo [delete_container](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Container#delete_container-instance_method). Se si elimina un contenitore, vengono eliminati anche tutti i BLOB archiviati in tale contenitore.

```ruby
# Clean up resources, including the container and the downloaded file
blob_client.delete_container(container_name)
File.delete(full_path_to_file)
```

## <a name="resources-for-developing-ruby-applications-with-blobs"></a>Risorse per lo sviluppo di applicazioni Ruby con BLOB

Per lo sviluppo Ruby, vedere le risorse aggiuntive seguenti:

- Visualizzare e scaricare il [codice sorgente della libreria client Ruby](https://github.com/Azure/azure-storage-ruby) per Archiviazione di Azure su GitHub.
- Esplorare gli [esempi di Azure](/samples/browse/?products=azure&languages=ruby) scritti con la libreria client Ruby.
- [Esempio: Introduzione ad Archiviazione di Azure in Ruby](https://github.com/Azure-Samples/storage-blob-ruby-getting-started)

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stato illustrato come trasferire file tra Archiviazione BLOB di Azure e un disco locale con Ruby. Per altre informazioni sull'uso di Archiviazione BLOB, continuare con la panoramica dell'account di archiviazione.

> [!div class="nextstepaction"]
> [Panoramica dell'account di archiviazione](../common/storage-account-overview.md)

Per altre informazioni su Storage Explorer e i BLOB, vedere [Gestire le risorse di archiviazione BLOB di Azure con Storage Explorer](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
