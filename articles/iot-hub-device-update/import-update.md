---
title: Come importare un nuovo aggiornamento | Microsoft Docs
description: Guida How-To per importare un nuovo aggiornamento nell'aggiornamento del dispositivo dell'hub Internet per gli hub Internet.
author: andrewbrownmsft
ms.author: andbrown
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: b9d40848abdd85beeca592001b697e3c50b7cd59
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103008563"
---
# <a name="import-new-update"></a>Importa nuovo aggiornamento
Informazioni su come importare un nuovo aggiornamento nell'aggiornamento del dispositivo per l'hub Internet. Se non è già stato fatto, assicurarsi di acquisire familiarità con i concetti di base dell' [importazione](import-concepts.md).

## <a name="prerequisites"></a>Prerequisiti

* [Accesso a un hub Internet delle cose con l'aggiornamento del dispositivo per l'hub](create-device-update-account.md)Internet. Si consiglia di usare un livello S1 (standard) o superiore per l'hub Internet. 
* Viene eseguito il provisioning di un dispositivo o di un simulatore per l'aggiornamento del dispositivo all'interno dell'hub.
   * Se si usa un dispositivo reale, è necessario un file di immagine di aggiornamento per l'aggiornamento dell'immagine o un [file manifesto apt](device-update-apt-manifest.md) per l'aggiornamento del pacchetto.
* [PowerShell 5](https://docs.microsoft.com/powershell/scripting/install/installing-powershell) o versione successiva.
* Browser supportati:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

> [!NOTE]
> Alcuni dati inviati a questo servizio potrebbero essere elaborati in un'area esterna all'area in cui è stata creata questa istanza.

## <a name="create-device-update-import-manifest"></a>Crea manifesto di importazione aggiornamento dispositivo

1. Verificare che il file di immagine dell'aggiornamento o il file manifesto APT si trovi in una directory accessibile da PowerShell.

2. Creare un file di testo denominato **AduUpdate. psm1** nella directory in cui si trova il file dell'immagine di aggiornamento o il file manifesto apt. Aprire quindi il cmdlet di PowerShell [AduUpdate. psm1](https://github.com/Azure/iot-hub-device-update/tree/main/tools/AduCmdlets) , copiare il contenuto nel file di testo e quindi salvare il file di testo.

3. In PowerShell passare alla directory in cui è stato creato il cmdlet di PowerShell dal passaggio 2. Eseguire quindi:

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

    Per riferimento rapido, di seguito sono riportati alcuni valori di esempio per i parametri precedenti. Per ulteriori informazioni, è inoltre possibile visualizzare lo [schema del manifesto dell'importazione](import-schema.md) completa.

    | Parametro | Descrizione |
    | --------- | ----------- |
    | deviceManufacturer | Produttore del dispositivo con cui è compatibile l'aggiornamento, ad esempio contoso. Deve corrispondere alla [proprietà del dispositivo](https://docs.microsoft.com/azure/iot-hub-device-update/device-update-plug-and-play#device-properties)del _produttore_ .
    | deviceModel | Modello del dispositivo con cui è compatibile l'aggiornamento, ad esempio, tostapane. Deve corrispondere alla [proprietà del dispositivo](https://docs.microsoft.com/azure/iot-hub-device-update/device-update-plug-and-play#device-properties)del _modello_ .
    | updateProvider | Entità che sta creando o direttamente responsabile dell'aggiornamento. Si tratta spesso di un nome della società.
    | aggiornaname | Identificatore di una classe di aggiornamenti. La classe può essere qualsiasi cosa scelta. Si tratta spesso di un nome di dispositivo o modello.
    | updateVersion | Numero di versione che distingue questo aggiornamento da altri che hanno lo stesso provider e lo stesso nome. Non corrisponde a una versione di un singolo componente software nel dispositivo (ma è possibile scegliere).
    | updateType | <ul><li>Specificare `microsoft/swupdate:1` per l'aggiornamento delle immagini</li><li>Specificare `microsoft/apt:1` per l'aggiornamento del pacchetto</li></ul>
    | installedCriteria | <ul><li>Specificare il valore di SWVersion per il `microsoft/swupdate:1` tipo di aggiornamento</li><li>Specificare il valore consigliato per il `microsoft/apt:1` tipo di aggiornamento.
    | updateFilePath | Percorso dei file di aggiornamento nel computer


## <a name="review-generated-import-manifest"></a>Verifica manifesto importazione generata

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

## <a name="import-update"></a>Importa aggiornamento

[!NOTE]
Le istruzioni seguenti illustrano come importare un aggiornamento tramite l'interfaccia utente di portale di Azure. È anche possibile usare l' [aggiornamento del dispositivo per le API dell'hub](https://github.com/Azure/iot-hub-device-update/tree/main/docs/publish-api-reference) Internet per importare un aggiornamento. 

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'hub Internet con l'aggiornamento del dispositivo.

2. Sul lato sinistro della pagina selezionare "aggiornamenti del dispositivo" in "gestione automatica dispositivi".

   :::image type="content" source="media/import-update/import-updates-3.png" alt-text="Importa aggiornamenti" lightbox="media/import-update/import-updates-3.png":::

3. Nella parte superiore della schermata vengono visualizzate diverse schede. Selezionare la scheda aggiornamenti.

   :::image type="content" source="media/import-update/updates-tab.png" alt-text="Aggiornamenti" lightbox="media/import-update/updates-tab.png":::

4. Selezionare "+ Importa nuovo aggiornamento" sotto l'intestazione "pronto per la distribuzione".

   :::image type="content" source="media/import-update/import-new-update-2.png" alt-text="Importa nuovo aggiornamento" lightbox="media/import-update/import-new-update-2.png":::

5. Selezionare l'icona della cartella o la casella di testo in "selezionare un file manifesto di importazione". Viene visualizzata una finestra di dialogo di selezione file. Selezionare il manifesto di importazione creato in precedenza usando il cmdlet di PowerShell. Selezionare quindi l'icona della cartella o la casella di testo in "selezionare uno o più file di aggiornamento". Viene visualizzata una finestra di dialogo di selezione file. Selezionare i file di aggiornamento.

   :::image type="content" source="media/import-update/select-update-files.png" alt-text="Selezionare i file di aggiornamento" lightbox="media/import-update/select-update-files.png":::

6. Selezionare l'icona della cartella o la casella di testo in "selezionare un contenitore di archiviazione". Selezionare quindi l'account di archiviazione appropriato. Il contenitore di archiviazione viene usato per organizzare temporaneamente i file di aggiornamento.

   :::image type="content" source="media/import-update/storage-account.png" alt-text="Storage Account" lightbox="media/import-update/storage-account.png":::

7. Se è già stato creato un contenitore, è possibile riusarlo. In caso contrario, selezionare "+ contenitore" per creare un nuovo contenitore di archiviazione per gli aggiornamenti.  Selezionare il contenitore che si vuole usare e fare clic su "Seleziona".

   :::image type="content" source="media/import-update/container.png" alt-text="Seleziona contenitore" lightbox="media/import-update/container.png":::

8. Selezionare "Invia" per avviare il processo di importazione.

   :::image type="content" source="media/import-update/publish-update.png" alt-text="Pubblica aggiornamento" lightbox="media/import-update/publish-update.png":::

9. Viene avviato il processo di importazione e la schermata passa alla sezione "cronologia di importazione". Selezionare "Aggiorna" per visualizzare lo stato di avanzamento fino al completamento del processo di importazione (a seconda delle dimensioni dell'aggiornamento, l'operazione può essere completata in pochi minuti, ma potrebbe richiedere più tempo).

   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="Aggiornare la sequenziazione dell'importazione" lightbox="media/import-update/update-publishing-sequence-2.png":::

10. Quando la colonna stato indica che l'importazione è riuscita, selezionare l'intestazione "pronto per la distribuzione". Nell'elenco verrà visualizzato l'aggiornamento importato.

   :::image type="content" source="media/import-update/update-ready.png" alt-text="Stato processo" lightbox="media/import-update/update-ready.png":::

## <a name="next-steps"></a>Passaggi successivi

[Creazione di gruppi](create-update-group.md)

[Informazioni sui concetti di importazione](import-concepts.md)
