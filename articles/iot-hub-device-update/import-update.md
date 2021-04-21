---
title: Come aggiungere un nuovo aggiornamento | Microsoft Docs
description: How-To per l'aggiunta di un nuovo aggiornamento in Aggiornamento dispositivi per l'hub IoT.
author: andrewbrownmsft
ms.author: andbrown
ms.date: 4/19/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: ebfeee2828b3a36f9cf47891f8aea6d889db85bd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763578"
---
# <a name="add-an-update-to-device-update-for-iot-hub"></a>Aggiungere un aggiornamento all'aggiornamento del dispositivo per l'hub IoT
Informazioni su come aggiungere un nuovo aggiornamento in Aggiornamento dispositivi per l'hub IoT.

## <a name="prerequisites"></a>Prerequisiti

* [Accesso a un hub IoT con Aggiornamento dispositivi per l'hub IoT abilitato.](create-device-update-account.md) 
* Un dispositivo IoT (o simulatore) di cui è stato [effettuato il provisioning per l'aggiornamento del dispositivo all'interno](device-update-agent-provisioning.md) dell'hub IoT.
* [PowerShell 5 o](/powershell/scripting/install/installing-powershell) versione successiva (include le installazioni linux, macOS e Windows)
* Browser supportati:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

> [!NOTE]
> Alcuni dati inviati a questo servizio potrebbero essere elaborati in un'area esterna all'area in cui è stata creata l'istanza.

## <a name="obtain-an-update-for-your-devices"></a>Ottenere un aggiornamento per i dispositivi

Dopo aver configurato Aggiornamento dispositivi ed eseguito il provisioning dei dispositivi, saranno necessari i file di aggiornamento che verranno distribuiti in tali dispositivi.

Se i dispositivi sono stati acquistati da un OEM o da un integratore di soluzioni, è molto probabile che l'organizzazione fornirà automaticamente i file di aggiornamento, senza la necessità di creare gli aggiornamenti. Per informazioni su come rendere disponibili gli aggiornamenti, contattare l'OEM o l'integratore di soluzioni.

Se l'organizzazione crea già software per i dispositivi in uso, lo stesso gruppo sarà quello che crea gli aggiornamenti per tale software. Quando si crea un aggiornamento da distribuire usando Aggiornamento dispositivi [](understand-device-update.md#support-for-a-wide-range-of-update-artifacts) per l'hub IoT, iniziare con l'approccio basato su immagine o basato su pacchetto a seconda dello scenario. Nota: se si vogliono creare aggiornamenti personalizzati ma si sta iniziando, GitHub è un'ottima opzione per gestire lo sviluppo. È possibile archiviare e gestire il codice sorgente ed eseguire l'integrazione continua (CI) e la distribuzione continua (CD) [usando GitHub Actions](https://docs.github.com/en/actions/guides/about-continuous-integration).

## <a name="create-a-device-update-import-manifest"></a>Creare un manifesto di importazione di Aggiornamento dispositivi

Se non è già stato fatto, assicurarsi di acquisire familiarità con i concetti di [importazione di base.](import-concepts.md)

1. Assicurarsi che i file di aggiornamento si trovino in una directory accessibile da PowerShell.

2. Creare un file di testo **denominato AduUpdate.psm1** nella directory in cui si trova il file di immagine di aggiornamento o il file manifesto APT. Aprire quindi il cmdlet di PowerShell [AduUpdate.psm1,](https://github.com/Azure/iot-hub-device-update/tree/main/tools/AduCmdlets) copiare il contenuto nel file di testo e quindi salvare il file di testo.

3. In PowerShell passare alla directory in cui è stato creato il cmdlet di PowerShell dal passaggio 2. Usare l'opzione Copia di seguito e quindi incollarla in PowerShell per eseguire i comandi:

    ```powershell
    Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process
    Import-Module .\AduUpdate.psm1
    ```

4. Eseguire i comandi seguenti sostituendo i valori dei parametri di esempio per generare un manifesto di importazione, un file JSON che descrive l'aggiornamento:
    ```powershell
    $compat = New-AduUpdateCompatibility -DeviceManufacturer 'deviceManufacturer' -DeviceModel 'deviceModel'

    $importManifest = New-AduImportManifest -Provider 'updateProvider' -Name 'updateName' -Version 'updateVersion' `
                                            -UpdateType 'updateType' -InstalledCriteria 'installedCriteria' `
                                            -Compatibility $compat -Files 'updateFilePath(s)'

    $importManifest | Out-File '.\importManifest.json' -Encoding UTF8
    ```

    Per riferimento rapido, di seguito sono riportati alcuni valori di esempio per i parametri precedenti. Per altri dettagli, è anche possibile visualizzare [lo schema completo del manifesto](import-schema.md) di importazione.

    | Parametro | Descrizione |
    | --------- | ----------- |
    | deviceManufacturer | Produttore del dispositivo con cui è compatibile l'aggiornamento, ad esempio Contoso. Deve corrispondere alla _proprietà del_ [dispositivo produttore](./device-update-plug-and-play.md#device-properties).
    | deviceModel | Modello del dispositivo con cui l'aggiornamento è compatibile, ad esempio toaster. Deve corrispondere alla _proprietà del_ [dispositivo del modello](./device-update-plug-and-play.md#device-properties).
    | updateProvider | Entità che crea o è direttamente responsabile dell'aggiornamento. Spesso sarà il nome di una società.
    | updateName | Identificatore per una classe di aggiornamenti. La classe può essere qualsiasi elemento scelto. Spesso si tratta di un nome di dispositivo o modello.
    | updateVersion | Numero di versione che distingue questo aggiornamento da altri che hanno lo stesso provider e lo stesso nome. Non ha una versione di un singolo componente software nel dispositivo (ma può scegliere).
    | updateType | <ul><li>Specificare per `microsoft/swupdate:1` l'aggiornamento dell'immagine</li><li>Specificare per `microsoft/apt:1` l'aggiornamento del pacchetto</li></ul>
    | installedCriteria | <ul><li>Specificare il valore di SWVersion per il `microsoft/swupdate:1` tipo di aggiornamento</li><li>Specificare **name-version**, dove _nome_ è il nome del manifesto APT e _version_ è la versione del manifesto APT. Ad esempio, contoso-iot-edge-1.0.0.0.
    | updateFilePath(s) | Percorso dei file di aggiornamento nel computer


## <a name="review-the-generated-import-manifest"></a>Esaminare il manifesto di importazione generato

Esempio:
```json
{
  "updateId": {
    "provider": "Microsoft",
    "name": "Toaster",
    "version": "2.0"
  },
  "updateType": "microsoft/swupdate:1",
  "installedCriteria": "5",
  "compatibility": [
    {
      "deviceManufacturer": "Fabrikam",
      "deviceModel": "Toaster"
    },
    {
      "deviceManufacturer": "Contoso",
      "deviceModel": "Toaster"
    }
  ],
  "files": [
    {
      "filename": "file1.json",
      "sizeInBytes": 7,
      "hashes": {
        "sha256": "K2mn97qWmKSaSaM9SFdhC0QIEJ/wluXV7CoTlM8zMUo="
      }
    },
    {
      "filename": "file2.zip",
      "sizeInBytes": 11,
      "hashes": {
        "sha256": "gbG9pxCr9RMH2Pv57vBxKjm89uhUstD06wvQSioLMgU="
      }
    }
  ],
  "createdDateTime": "2020-10-08T03:32:52.477Z",
  "manifestVersion": "2.0"
}
```

## <a name="import-an-update"></a>Importare un aggiornamento

> [!NOTE]
> Le istruzioni seguenti illustrano come importare un aggiornamento tramite l'interfaccia portale di Azure interfaccia utente. È anche possibile usare [l'aggiornamento del dispositivo per le API dell'hub IoT](https://github.com/Azure/iot-hub-device-update/tree/main/docs/publish-api-reference) per importare un aggiornamento. 

1. Accedere al portale di Azure [passare](https://portal.azure.com) all'hub IoT con Aggiornamento dispositivo.

2. Sul lato sinistro della pagina selezionare "Aggiornamenti del dispositivo" in "Gestione automatica dei dispositivi".

   :::image type="content" source="media/import-update/import-updates-3.png" alt-text="Importare gli aggiornamenti" lightbox="media/import-update/import-updates-3.png":::

3. Nella parte superiore della schermata verranno visualizzate diverse schede. Selezionare la scheda Aggiornamenti.

   :::image type="content" source="media/import-update/updates-tab.png" alt-text="Aggiornamenti" lightbox="media/import-update/updates-tab.png":::

4. Selezionare "+ Importa nuovo aggiornamento" sotto l'intestazione "Pronto per la distribuzione".

   :::image type="content" source="media/import-update/import-new-update-2.png" alt-text="Importare un nuovo aggiornamento" lightbox="media/import-update/import-new-update-2.png":::

5. Selezionare l'icona della cartella o la casella di testo in "Selezionare un file manifesto di importazione". Verrà visualizzata una finestra di dialogo di selezione file. Selezionare il manifesto di importazione creato in precedenza usando il cmdlet di PowerShell. Selezionare quindi l'icona della cartella o la casella di testo in "Selezionare uno o più file di aggiornamento". Verrà visualizzata una finestra di dialogo di selezione file. Selezionare i file di aggiornamento.

   :::image type="content" source="media/import-update/select-update-files.png" alt-text="Selezionare Aggiorna file" lightbox="media/import-update/select-update-files.png":::

6. Selezionare l'icona della cartella o la casella di testo in "Selezionare un contenitore di archiviazione". Selezionare quindi l'account di archiviazione appropriato. Il contenitore di archiviazione viene usato per lo gestione temporanea dei file di aggiornamento.

   :::image type="content" source="media/import-update/storage-account.png" alt-text="Storage Account" lightbox="media/import-update/storage-account.png":::

7. Se è già stato creato un contenitore, è possibile riutilizzarlo. In caso contrario, selezionare "+ Contenitore" per creare un nuovo contenitore di archiviazione per gli aggiornamenti.  Selezionare il contenitore da usare e fare clic su "Seleziona".

   :::image type="content" source="media/import-update/container.png" alt-text="Selezionare il contenitore" lightbox="media/import-update/container.png":::

8. Selezionare "Invia" per avviare il processo di importazione.

   :::image type="content" source="media/import-update/publish-update.png" alt-text="Pubblica aggiornamento" lightbox="media/import-update/publish-update.png":::

9. Viene avviato il processo di importazione e la schermata passa alla sezione "Cronologia importazione". Selezionare "Aggiorna" per visualizzare lo stato di avanzamento fino al completamento del processo di importazione. A seconda delle dimensioni dell'aggiornamento, l'operazione potrebbe essere completata in pochi minuti, ma potrebbe richiedere più tempo.

   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="Aggiornare la sequenziazione dell'importazione" lightbox="media/import-update/update-publishing-sequence-2.png":::

10. Quando la colonna Stato indica che l'importazione è riuscita, selezionare l'intestazione "Pronto per la distribuzione". L'aggiornamento importato dovrebbe essere ora visualizzato nell'elenco.

   :::image type="content" source="media/import-update/update-ready.png" alt-text="Stato processo" lightbox="media/import-update/update-ready.png":::

## <a name="next-steps"></a>Passaggi successivi

[Creare gruppi](create-update-group.md)

[Informazioni sui concetti relativi all'importazione](import-concepts.md)