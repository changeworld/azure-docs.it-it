---
title: Creare deserializzatori .NET personalizzati per i processi cloud di analisi di flusso di Azure usando Visual Studio Code
description: Questa esercitazione illustra come creare un deserializzatore .NET personalizzato per un processo cloud di analisi di flusso di Azure usando Visual Studio Code.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/22/2020
ms.openlocfilehash: 1813fb222bca74f355fec52252ce3d77fef06e5d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98013924"
---
# <a name="create-custom-net-deserializers-for-azure-stream-analytics-in-visual-studio-code"></a>Creare deserializzatori .NET personalizzati per analisi di flusso di Azure in Visual Studio Code

Analisi di flusso di Azure offre [supporto incorporato per tre formati di dati](stream-analytics-parsing-json.md): JSON, CSV e Avro. Con i deserializzatori .NET personalizzati, è possibile leggere dati da altri formati, ad esempio [buffer del protocollo](https://developers.google.com/protocol-buffers/), [Bond](https://github.com/Microsoft/bond) e altri formati definiti dall'utente per i processi cloud.

## <a name="custom-net-deserializers-in-visual-studio-code"></a>Deserializzatori .NET personalizzati in Visual Studio Code

È possibile creare, testare ed eseguire il debug di un deserializzatore .NET personalizzato per un processo cloud di analisi di flusso di Azure usando Visual Studio Code.

### <a name="prerequisites"></a>Prerequisiti

* Installare [.NET Core SDK](https://dotnet.microsoft.com/download) e riavviare Visual Studio Code.

* Usare questa [Guida introduttiva](quick-create-visual-studio-code.md) per informazioni su come creare un processo di analisi di flusso usando Visual Studio Code.

### <a name="create-a-custom-deserializer"></a>Creare un deserializzatore personalizzato

1. Aprire un terminale ed eseguire il comando seguente per creare una libreria di classi .NET in Visual Studio Code per il deserializzatore personalizzato chiamato **ProtobufDeserializer**.

   ```dotnetcli
   dotnet new classlib -o ProtobufDeserializer
   ```

2. Passare alla directory del progetto **ProtobufDeserializer** e installare i pacchetti NuGet [Microsoft. Azure. StreamAnalytics](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics/) e [Google. protobuf](https://www.nuget.org/packages/Google.Protobuf/) .

   ```dotnetcli
   dotnet add package Microsoft.Azure.StreamAnalytics
   ```

   ```dotnetcli
   dotnet add package Google.Protobuf
   ```

3. Aggiungere la [classe MessageBodyProto](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyProto.cs) e la [classe MessageBodyDeserializer](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyDeserializer.cs) al progetto.

4. Compilare il progetto **ProtobufDeserializer** .

### <a name="add-an-azure-stream-analytics-project"></a>Aggiungere un progetto di Analisi di flusso di Azure

1. Aprire Visual Studio Code e premere **CTRL + MAIUSC + P** per aprire il riquadro comandi. Immettere quindi ASA e selezionare **ASA: Create New Project**(ASA: Crea nuovo progetto). Denominarlo **ProtobufCloudDeserializer**.

### <a name="configure-a-stream-analytics-job"></a>Configurare un processo di analisi di flusso

1. Fare doppio clic su **JobConfig.json**. Usare le configurazioni predefinite, tranne per le impostazioni seguenti:

   |Impostazione|Valore consigliato|
   |-------|---------------|
   |Global Storage Settings Resource (Risorsa impostazioni di archiviazione globali)|Scegliere l'origine dati dall'account corrente|
   |Global Storage Settings Subscription (Sottoscrizione impostazioni di archiviazione globali)| < sottoscrizione >|
   |Global Storage Settings Storage Account (Account di archiviazione impostazioni di archiviazione globali)| < account di archiviazione >|
   |Impostazioni CustomCodeStorage account di archiviazione|< account di archiviazione >|
   |Contenitore Impostazioni CustomCodeStorage|< contenitore di archiviazione >|

2. Nella cartella **input** aprire **input.js**. Selezionare **Aggiungi input Live** e aggiungere un input da Azure Data Lake storage Gen2/archiviazione BLOB, scegliere **Seleziona dalla sottoscrizione di Azure**. Usare le configurazioni predefinite, tranne per le impostazioni seguenti:

   |Impostazione|Valore consigliato|
   |-------|---------------|
   |Nome|Input|
   |Sottoscrizione|< sottoscrizione >|
   |Account di archiviazione|< account di archiviazione >|
   |Contenitore|< contenitore di archiviazione >|
   |Tipo di serializzazione|Scegliere **personalizzata**|
   |SerializationProjectPath|Selezionare **Scegli percorso progetto libreria** da CodeLens e selezionare il progetto di libreria **ProtobufDeserializer** creato nella sezione precedente. Selezionare **Compila progetto** per compilare il progetto|
   |SerializationClassName|Selezionare **Seleziona classe di deserializzazione** da CodeLens per popolare automaticamente il nome della classe e il percorso della dll|
   |Nome della classe|MessageBodyProto.MessageBodyDeserializer|

   :::image type="content" source="./media/custom-deserializer/create-input-vscode.png" alt-text="Aggiungere un input del deserializzatore personalizzato.":::

3. Aggiungere la query seguente al file **ProtobufCloudDeserializer. asaql** .

   ```sql
   SELECT * FROM Input
   ```

4. Scaricare il [file di input protobuf di esempio](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/SimulatedTemperatureEvents.protobuf). Nella cartella **input** fare clic con il pulsante destro del mouse **suinput.json** e scegliere **Aggiungi input locale**. Quindi, fare doppio clic **sulocal_input1.json** e utilizzare le configurazioni predefinite, eccetto le impostazioni seguenti.

   |Impostazione|Valore consigliato|
   |-------|---------------|
   |Selezionare il percorso del file locale|Fare clic su CodeLens per selezionare < il percorso del file di input protobuf di esempio scaricato>|

### <a name="execute-the-stream-analytics-job"></a>Eseguire il processo di analisi di flusso

1. Aprire **ProtobufCloudDeserializer. asaql** e selezionare **Esegui localmente** da CodeLens, quindi scegliere **Usa input locale** dall'elenco a discesa.

2. Osservare la scheda risultati nella visualizzazione **diagramma dei processi** a destra. È anche possibile fare clic sui passaggi del diagramma del processo per visualizzare il risultato intermedio. Per altri dettagli, vedere [debug in locale usando il diagramma dei processi](debug-locally-using-job-diagram-vs-code.md).

   :::image type="content" source="./media/custom-deserializer/check-local-run-result-vscode.png" alt-text="Verificare il risultato dell'esecuzione locale.":::

Il deserializzatore personalizzato per il processo di analisi di flusso è stato implementato correttamente. In questa esercitazione è stato testato il deserializzatore personalizzato localmente con i dati di input locali. È anche possibile testarla [usando input di dati dinamici nel cloud](visual-studio-code-local-run-live-input.md). Per eseguire il processo nel cloud, è necessario configurare correttamente l'input e l'output. Inviare quindi il processo ad Azure da Visual Studio Code per eseguire il processo nel cloud usando il deserializzatore personalizzato appena implementato.

### <a name="debug-your-deserializer"></a>Eseguire il debug del deserializzatore

È possibile eseguire il debug del deserializzatore .NET in locale esattamente come si esegue il debug di codice .NET standard. 

1. Aggiungere punti di interruzione nella funzione .NET.

2. Fare clic su **Esegui** da Visual Studio Code barra attività e selezionare **Crea una launch.jssu file**.
   :::image type="content" source="./media/custom-deserializer/create-launch-file-vscode.png" alt-text="Creare il file di avvio.":::

   Scegliere **ProtobufCloudDeserializer** e quindi **analisi di flusso di Azure** dall'elenco a discesa.
   :::image type="content" source="./media/custom-deserializer/create-launch-file-vscode-2.png" alt-text="Creare il file di avvio 2.":::

   Modificare il **launch.jsnel** file per sostituire <ASAScript> . asaql con ProtobufCloudDeserializer. asaql.
   :::image type="content" source="./media/custom-deserializer/configure-launch-file-vscode.png" alt-text="Configurare il file di avvio.":::

3. Premere **F5** per avviare il debug. Il programma verrà interrotto in corrispondenza dei punti di interruzione nel modo previsto. Questa operazione può essere eseguita sia per i dati di input locali che per quelli Live.

   :::image type="content" source="./media/custom-deserializer/debug-vscode.png" alt-text="Debug del deserializzatore personalizzato.":::

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare il gruppo di risorse, il processo di streaming e tutte le risorse correlate. Eliminando il processo si evita di pagare per le unità di streaming usate dal processo. Se si prevede di usare il processo in futuro, è possibile arrestarlo e riavviarlo in un secondo momento, quando è necessario. Se non si intende continuare a usare il processo, eliminare tutte le risorse create tramite questa esercitazione seguendo questa procedura:

1. Scegliere **Gruppi di risorse** dal menu a sinistra del portale di Azure e quindi selezionare il nome della risorsa creata.  

2. Nella pagina del gruppo di risorse selezionare **Elimina**, digitare il nome della risorsa da eliminare nella casella di testo e quindi selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato illustrato come implementare un deserializzatore .NET personalizzato per la serializzazione dell'input del buffer di protocollo. Per altre informazioni sulla creazione di deserializzatori personalizzati, continuare con l'articolo seguente:

> [!div class="nextstepaction"]
> * [Creare deserializzatori .NET diversi per i processi di Analisi di flusso di Azure](custom-deserializer-examples.md)
> * [Testare i processi di analisi di flusso di Azure localmente con input Live con Visual Studio Code](visual-studio-code-local-run-live-input.md)