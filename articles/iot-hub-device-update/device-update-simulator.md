---
title: Esercitazione sull'aggiornamento del dispositivo per l'hub Azure. uso dell'agente di riferimento del simulatore Ubuntu (18,04 x64) | Microsoft Docs
description: Introduzione all'aggiornamento del dispositivo per l'hub di Azure. con l'agente di riferimento del simulatore Ubuntu (18,04 x64).
author: valls
ms.author: valls
ms.date: 2/11/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 4740bf02c33314dd7c887356f2ef1ed12bea44cf
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102443812"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-ubuntu-1804-x64-simulator-reference-agent"></a>Esercitazione sull'aggiornamento del dispositivo per l'hub Azure. uso dell'agente di riferimento del simulatore Ubuntu (18,04 x64)

L'aggiornamento del dispositivo per l'hub Internet supporta due forme di aggiornamenti, basati su immagini e basati su pacchetti.

Gli aggiornamenti delle immagini forniscono un livello di confidenza maggiore nello stato finale del dispositivo. È in genere più semplice replicare i risultati di un aggiornamento di immagine tra un ambiente di pre-produzione e un ambiente di produzione, poiché non comporta le stesse esigenze dei pacchetti e delle relative dipendenze. A causa della natura atomica, è anche possibile adottare facilmente un modello di failover A/B.

Questa esercitazione illustra la procedura per completare un aggiornamento end-to-end basato su immagini con l'aggiornamento del dispositivo per l'hub Internet. 

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Scaricare e installare l'immagine
> * Aggiungere un tag al dispositivo Internet delle cose
> * Importazione di un aggiornamento
> * Creare un gruppo di dispositivi
> * Distribuire un aggiornamento dell'immagine
> * Monitorare la distribuzione degli aggiornamenti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti
* Accesso a un hub. Si consiglia di usare un livello S1 (standard) o superiore.

### <a name="download-and-install"></a>Scaricare e installare

* Cmdlet AZ (Azure CLI) per PowerShell:
  * Aprire PowerShell > installare l'interfaccia della riga di comando di Azure ("Y" per richiedere l'installazione dall'origine "non attendibile")

```powershell
PS> Install-Module Az -Scope CurrentUser
```

### <a name="enable-wsl-on-your-windows-device-windows-subsystem-for-linux"></a>Abilitare WSL sul dispositivo Windows (sottosistema Windows per Linux)

1. Aprire PowerShell come amministratore nel computer ed eseguire il comando seguente. potrebbe essere richiesto di riavviare dopo ogni passaggio. riavviare quando richiesto:

```powershell
PS> Enable-WindowsOptionalFeature -Online -FeatureName VirtualMachinePlatform
PS> Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
```

(*Potrebbe essere richiesto di riavviare dopo questo passaggio*)

2. Passare alla Microsoft Store sul Web e installare [Ubuntu 18,04 LTS](https://www.microsoft.com/p/ubuntu-1804-lts/9n9tngvndl3q?activetab=pivot:overviewtab`).

3. Avviare "Ubuntu 18,04 LTS" e installare.

4. Al termine dell'installazione, verrà richiesto di impostare il nome della radice (username) e la password. Assicurarsi di usare una password di nome radice memorabile.

5. In PowerShell eseguire il comando seguente per impostare Ubuntu come distribuzione predefinita di Linux:

```powershell
PS> wsl --setdefault Ubuntu-18.04
```

6. Elencare tutte le distribuzioni di Linux, assicurarsi che Ubuntu sia quello predefinito.

```powershell
PS> wsl --list
```

7. Dovrebbe essere visualizzato: **Ubuntu-18,04 (impostazione predefinita)**

## <a name="download-device-update-ubuntu-1804-x64-simulator-reference-agent"></a>Scaricare l'agente di riferimento del simulatore per l'aggiornamento del dispositivo Ubuntu (18,04 x64)

L'immagine di aggiornamento Ubuntu può essere scaricata dalla sezione *Asset* dalle note sulla versione [qui](https://github.com/Azure/iot-hub-device-update/releases).

Sono disponibili due versioni dell'agente. Se si sta eseguendo uno scenario basato su immagine, usare AducIotAgentSim-Microsoft-swupdate e, se si sta eseguendo uno scenario basato su pacchetti, usare AducIotAgentSim-Microsoft-apt.

## <a name="install-device-update-agent-simulator"></a>Installare il simulatore dell'agente di aggiornamento del dispositivo

1. Avviare Ubuntu WSL e immettere il comando seguente. si noti che lo spazio aggiuntivo e il punto alla fine.

  ```shell
  explorer.exe .
  ```

2. Copiare AducIotAgentSim-Microsoft-swupdate (o AducIotAgentSim-Microsoft-APT) dalla cartella locale in cui è stato scaricato in/mnt nella cartella Home in WSL.

3. Eseguire il comando seguente per rendere il file eseguibile dei file binari.

  ```shell
  sudo chmod u+x AducIotAgentSim-microsoft-swupdate
  ```

  oppure

  ```shell
  sudo chmod u+x AducIotAgentSim-microsoft-apt
  ```
L'aggiornamento del dispositivo per il software hub Azure Internet è soggetto alle condizioni di licenza seguenti:
   * [Aggiornamento del dispositivo per la licenza dell'hub Internet](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
   * [Licenza client di ottimizzazione recapito](https://github.com/microsoft/do-client/blob/main/LICENSE.md)
   
Leggere le condizioni di licenza prima di utilizzare l'agente. L'installazione e l'utilizzo costituiscono l'accettazione di tali condizioni. Se non si accettano le condizioni di licenza, non usare l'aggiornamento del dispositivo per l'agente hub Internet delle cose.

## <a name="add-device-to-azure-iot-hub"></a>Aggiungere un dispositivo all'hub di Azure

Una volta che l'agente di aggiornamento del dispositivo è in esecuzione su un dispositivo, il dispositivo deve essere aggiunto all'hub Azure.  Dall'hub Azure, verrà generata una stringa di connessione per un determinato dispositivo.

1. Dal portale di Azure avviare l'hub di aggiornamento del dispositivo.
2. Creare un nuovo dispositivo.
3. Sul lato sinistro della pagina passare a "esploratori" > "dispositivi Internet" > selezionare "nuovo".
4. Specificare un nome per il dispositivo in "ID dispositivo". Verificare che sia selezionata l'opzione "genera chiavi automaticamente".
5. Selezionare "Salva".
6. A questo punto, verrà visualizzata la pagina "dispositivi" e il dispositivo creato sarà presente nell'elenco. Selezionare il dispositivo.
7. In visualizzazione dispositivo selezionare l'icona "copia" accanto a "stringa di connessione primaria".
8. Incollare i caratteri copiati in un punto qualsiasi per un uso successivo nei passaggi seguenti. **Questa stringa copiata è la stringa di connessione del dispositivo**.

## <a name="add-connection-string-to-simulator"></a>Aggiungi stringa di connessione al simulatore

Avviare l'agente di aggiornamento dispositivi nei nuovi dispositivi software.

1. Avviare Ubuntu.
2. Eseguire l'agente di aggiornamento del dispositivo e specificare la stringa di connessione del dispositivo della sezione precedente di cui è stato eseguito il wrapper con apostrofi:

Sostituire `<device connection string>` con la stringa di connessione
```shell
./AducIotAgentSim-microsoft-swupdate -c '<device connection string>'
```

oppure

```shell
./AducIotAgentSim-microsoft-apt -c '<device connection string>'
```

3. Scorrere verso l'alto e cercare la stringa che indica che il dispositivo è in stato "inattivo". Uno stato di "inattività" indica che il dispositivo è pronto per i comandi del servizio:

```markdown
Agent running. [main]
```

## <a name="add-a-tag-to-your-device"></a>Aggiungere un tag al dispositivo

1. Accedere a [portale di Azure](https://portal.azure.com) e passare all'hub Internet delle cose.

2. Da "dispositivi Internet o" IoT Edge "nel riquadro di spostamento a sinistra trova il tuo dispositivo e passa al dispositivo gemello.

3. Nel dispositivo gemello eliminare qualsiasi valore del tag di aggiornamento del dispositivo esistente impostando il valore su null.

4. Aggiungere un nuovo valore del tag di aggiornamento del dispositivo come illustrato di seguito.

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

## <a name="import-update"></a>Importa aggiornamento

1. Creare un manifesto di importazione seguendo queste [istruzioni](import-update.md).
2. Selezionare l'opzione aggiornamenti del dispositivo in gestione automatica dispositivi dalla barra di spostamento a sinistra.

3. Selezionare la scheda aggiornamenti.

4. Selezionare "+ Importa nuovo aggiornamento".

5. Selezionare l'icona della cartella o la casella di testo in "selezionare un file manifesto di importazione". Viene visualizzata una finestra di dialogo di selezione file. Selezionare il manifesto di importazione creato in precedenza.  Selezionare quindi l'icona della cartella o la casella di testo in "selezionare uno o più file di aggiornamento". Viene visualizzata una finestra di dialogo di selezione file. Selezionare l'immagine di aggiornamento Ubuntu scaricata in precedenza. 

   :::image type="content" source="media/import-update/select-update-files.png" alt-text="Screenshot che mostra la selezione del file di aggiornamento." lightbox="media/import-update/select-update-files.png":::

6. Selezionare l'icona della cartella o la casella di testo in "selezionare un contenitore di archiviazione". Selezionare quindi l'account di archiviazione appropriato.

7. Se è già stato creato un contenitore, è possibile riusarlo. In caso contrario, selezionare "+ contenitore" per creare un nuovo contenitore di archiviazione per gli aggiornamenti.  Selezionare il contenitore che si vuole usare e fare clic su "Seleziona".
  
  :::image type="content" source="media/import-update/container.png" alt-text="Screenshot che mostra la selezione del contenitore." lightbox="media/import-update/container.png":::

8. Selezionare "Invia" per avviare il processo di importazione.

9. Viene avviato il processo di importazione e la schermata passa alla sezione "cronologia di importazione". Selezionare "Aggiorna" per visualizzare lo stato di avanzamento fino al completamento del processo di importazione. A seconda delle dimensioni dell'aggiornamento, l'operazione può essere completata in pochi minuti, ma potrebbe richiedere più tempo.
   
   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="Screenshot che mostra la sequenza di importazione degli aggiornamenti." lightbox="media/import-update/update-publishing-sequence-2.png":::

10. Quando la colonna stato indica che l'importazione è riuscita, selezionare l'intestazione "pronto per la distribuzione". Nell'elenco verrà visualizzato l'aggiornamento importato.

[Altre](import-update.md) informazioni sull'importazione di aggiornamenti.

## <a name="create-update-group"></a>Crea gruppo di aggiornamento

1. Passare all'hub Internet delle cose precedentemente connesso all'istanza di aggiornamento del dispositivo.

2. Selezionare l'opzione aggiornamenti del dispositivo in gestione automatica dispositivi dalla barra di spostamento a sinistra.

3. Selezionare la scheda gruppi nella parte superiore della pagina. 

4. Selezionare il pulsante Aggiungi per creare un nuovo gruppo.

5. Selezionare il tag dell'hub Internet delle cose creato nel passaggio precedente dall'elenco. Selezionare Crea gruppo di aggiornamento.

   :::image type="content" source="media/create-update-group/select-tag.PNG" alt-text="Screenshot che mostra la selezione di tag." lightbox="media/create-update-group/select-tag.PNG":::

[Altre](create-update-group.md) informazioni sull'aggiunta di tag e la creazione di gruppi di aggiornamento


## <a name="deploy-update"></a>Distribuisci aggiornamento

1. Una volta creato il gruppo, verrà visualizzato un nuovo aggiornamento per il gruppo di dispositivi, con un collegamento all'aggiornamento in aggiornamenti in sospeso. Potrebbe essere necessario eseguire l'aggiornamento una sola volta. 

2. Fare clic sull'aggiornamento disponibile.

3. Verificare che sia selezionato il gruppo corretto come gruppo di destinazione. Pianificare la distribuzione, quindi selezionare Distribuisci aggiornamento.

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="Seleziona aggiornamento" lightbox="media/deploy-update/select-update.png":::

4. Visualizzare il grafico di conformità. Si noterà che l'aggiornamento è in corso. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="Aggiornamento in corso" lightbox="media/deploy-update/update-in-progress.png":::

5. Al termine dell'aggiornamento del dispositivo, si noterà che l'aggiornamento del grafico di conformità e dei dettagli della distribuzione è identico. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="Aggiornamento completato" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>Monitorare una distribuzione degli aggiornamenti

1. Selezionare la scheda distribuzioni nella parte superiore della pagina.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="Scheda distribuzioni" lightbox="media/deploy-update/deployments-tab.png":::

2. Selezionare la distribuzione creata per visualizzare i dettagli della distribuzione.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Dettagli di distribuzione" lightbox="media/deploy-update/deployment-details.png":::

3. Selezionare Aggiorna per visualizzare i dettagli più recenti sullo stato. Continuare questo processo fino a quando lo stato diventa succeeded.

A questo punto è stato completato un aggiornamento di immagine end-to-end corretto usando l'aggiornamento del dispositivo per l'hub Internet con l'agente di riferimento del simulatore Ubuntu (18,04 x64).

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessario, pulire l'account di aggiornamento del dispositivo, l'istanza, l'hub e il dispositivo Internet delle cose. 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Risoluzione dei problemi](troubleshoot-device-update.md)

