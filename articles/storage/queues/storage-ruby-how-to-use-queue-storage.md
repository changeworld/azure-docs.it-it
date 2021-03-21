---
title: Come usare l'archiviazione di Accodamento da Ruby-archiviazione di Azure
description: Informazioni su come usare l'archiviazione code di Azure per creare ed eliminare code e per inserire, ottenere ed eliminare messaggi. Gli esempi sono scritti in Ruby.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 12/08/2016
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 8558949e49bcf551c9276458d375fb9ac9636184
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97587663"
---
# <a name="how-to-use-queue-storage-from-ruby"></a>Come usare l'archiviazione di Accodamento da Ruby

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Panoramica

In questa guida viene illustrato come eseguire scenari comuni del servizio di archiviazione di accodamento di Microsoft Azure. Gli esempi sono scritti usando l'API Ruby di Azure. Gli scenari illustrati includono **inserimento**, **visualizzazione**, **recupero** ed **eliminazione** dei messaggi in coda, oltre alla **creazione ed eliminazione di code**.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Creare un'applicazione Ruby

Creare un'applicazione Ruby. Per istruzioni, vedere [creare un'applicazione Ruby nel servizio app in Linux](../../app-service/quickstart-ruby.md).

## <a name="configure-your-application-to-access-storage"></a>Configurare l'applicazione per l'accesso all'archiviazione

Per usare archiviazione di Azure, è necessario scaricare e usare il pacchetto Ruby Azure, che include un set di pratici librerie che comunicano con i servizi REST di archiviazione.

<!-- docutune:ignore Terminal -->

### <a name="use-rubygems-to-obtain-the-package"></a>Utilizzare RubyGems per ottenere il pacchetto

1. Usare un'interfaccia della riga di comando, ad esempio PowerShell (Windows), Terminal (Mac) o Bash (Unix).
2. Digitare `gem install Azure` nella finestra di comando per installare la gemma e le dipendenze.

### <a name="import-the-package"></a>Importare il pacchetto

Usando l'editor di testo preferito aggiungere quanto segue alla parte superiore del file Ruby dove si intende usare l'archiviazione:

```ruby
require "azure"
```

## <a name="setup-an-azure-storage-connection"></a>Configurare una connessione di archiviazione di Azure

Il modulo di Azure leggerà le variabili di ambiente `AZURE_STORAGE_ACCOUNT` e le `AZURE_STORAGE_ACCESS_KEY` informazioni necessarie per connettersi all'account di archiviazione di Azure. Se queste variabili di ambiente non sono impostate, è necessario specificare le informazioni sull'account prima `Azure::QueueService` di usare con il codice seguente:

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your Azure storage access key>"
```

Per ottenere questi valori da un account di archiviazione classico o di Resource Manager nel portale di Azure:

1. Accedere al [Portale di Azure](https://portal.azure.com).
2. Passare all'account di archiviazione che si desidera utilizzare.
3. Nel pannello **Impostazioni** a destra fare clic su **chiavi di accesso**.
4. Nel pannello **chiavi di accesso** visualizzato verranno visualizzati la chiave di accesso 1 e la chiave di accesso 2. È possibile usare una di queste indifferentemente.
5. Fare clic sull'icona Copia per copiare la chiave negli Appunti.

## <a name="how-to-create-a-queue"></a>Procedura: creare una coda

Il codice seguente crea un `Azure::QueueService` oggetto che consente di usare le code.

```ruby
azure_queue_service = Azure::QueueService.new
```

Utilizzare il `create_queue()` metodo per creare una coda con il nome specificato.

```ruby
begin
  azure_queue_service.create_queue("test-queue")
rescue
  puts $!
end
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Procedura: inserire un messaggio in una coda

Per inserire un messaggio in una coda, usare il `create_message()` metodo per creare un nuovo messaggio e aggiungerlo alla coda.

```ruby
azure_queue_service.create_message("test-queue", "test message")
```

## <a name="how-to-peek-at-the-next-message"></a>Procedura: visualizzare il messaggio successivo

È possibile visualizzare il messaggio nella parte anteriore di una coda senza rimuoverlo dalla coda chiamando il `peek_messages()` metodo. Per impostazione predefinita, `peek_messages()` Visualizza un singolo messaggio. È anche possibile specificare il numero di messaggi che si desidera visualizzare.

```ruby
result = azure_queue_service.peek_messages("test-queue",
  {:number_of_messages => 10})
```

## <a name="how-to-dequeue-the-next-message"></a>Procedura: rimuovere il messaggio successivo dalla coda

È possibile rimuovere un messaggio da una coda in due passaggi.

1. Quando si chiama `list_messages()` , per impostazione predefinita si ottiene il messaggio successivo in una coda. È anche possibile specificare il numero di messaggi che si desidera ottenere. I messaggi restituiti da `list_messages()` diventano invisibile a qualsiasi altro codice che legge i messaggi da questa coda. Passare il timeout di visibilità in secondi come parametro.
2. Per completare la rimozione del messaggio dalla coda, è necessario chiamare anche `delete_message()` .

Questo processo in due passaggi di rimozione di un messaggio assicura che, qualora l'elaborazione di un messaggio abbia esito negativo a causa di errori hardware o software, un'altra istanza del codice sia in grado di ottenere lo stesso messaggio e di riprovare. Il codice chiama `delete_message()` subito dopo l'elaborazione del messaggio.

```ruby
messages = azure_queue_service.list_messages("test-queue", 30)
azure_queue_service.delete_message("test-queue",
  messages[0].id, messages[0].pop_receipt)
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Procedura: cambiare il contenuto di un messaggio accodato

È possibile cambiare il contenuto di un messaggio inserito nella coda. Il codice seguente usa il `update_message()` metodo per aggiornare un messaggio. Il metodo restituirà una tupla contenente la ricezione pop del messaggio della coda e un `DateTime` valore UTC che rappresenta il momento in cui il messaggio sarà visibile nella coda.

```ruby
message = azure_queue_service.list_messages("test-queue", 30)
pop_receipt, time_next_visible = azure_queue_service.update_message(
  "test-queue", message.id, message.pop_receipt, "updated test message",
  30)
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Procedura: opzioni aggiuntive per la rimozione dalla coda dei messaggi

È possibile personalizzare il recupero di messaggi da una coda in due modi.

1. È possibile recuperare un batch di messaggi.
2. È possibile impostare un timeout di invisibilità più lungo o più breve assegnando al codice più o meno tempo per l'elaborazione completa di ogni messaggio.

Nell'esempio di codice seguente viene usato il `list_messages()` metodo per ottenere 15 messaggi in una sola chiamata. Quindi, ogni messaggio viene stampato ed eliminato. Per ogni messaggio, inoltre, il timeout di invisibilità viene impostato su cinque minuti.

```ruby
azure_queue_service.list_messages("test-queue", 300
  {:number_of_messages => 15}).each do |m|
  puts m.message_text
  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
end
```

## <a name="how-to-get-the-queue-length"></a>Procedura: recuperare la lunghezza delle code

È possibile ottenere una stima sul numero di messaggi presenti in una coda. Il `get_queue_metadata()` metodo restituisce il numero approssimativo di messaggi e altri metadati della coda.

```ruby
message_count, metadata = azure_queue_service.get_queue_metadata(
  "test-queue")
```

## <a name="how-to-delete-a-queue"></a>Procedura: eliminare una coda

Per eliminare una coda e tutti i messaggi che contiene, chiamare il `delete_queue()` metodo sull'oggetto Queue.

```ruby
azure_queue_service.delete_queue("test-queue")
```

## <a name="next-steps"></a>Passaggi successivi

Ora che sono state apprese le nozioni di base dell'archiviazione code, seguire i collegamenti seguenti per informazioni sulle attività di archiviazione più complesse.

- Blog del [team di archiviazione di Azure](/archive/blogs/windowsazurestorage/)
- Archivio [Azure SDK for Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) su GitHub

Per un confronto tra l'archiviazione code di Azure descritta in questo articolo e le code del bus di servizio di Azure illustrate in [come usare le code del bus di servizio](https://azure.microsoft.com/develop/ruby/how-to-guides/service-bus-queues/), vedere Code di [archiviazione di Azure e code del bus di servizio-confronto e contrasto](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)
