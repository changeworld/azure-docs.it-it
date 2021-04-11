---
title: Esercitazione sull'aggiornamento del dispositivo per l'hub Azure. uso di Raspberry Pi 3 B + riferimento Yocto image | Microsoft Docs
description: Introduzione all'aggiornamento del dispositivo per l'hub di Azure Internet con l'immagine Yocto di Raspberry Pi 3 B + Reference.
author: valls
ms.author: valls
ms.date: 2/11/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: b6a9fa3ac85460a46653c171198a2dfea8580f3a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105644488"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-raspberry-pi-3-b-reference-image"></a>Esercitazione sull'aggiornamento del dispositivo per l'hub Azure. uso dell'immagine di riferimento di Raspberry Pi 3 B +

L'aggiornamento del dispositivo per l'hub Internet supporta due forme di aggiornamenti, basati su immagini e basati su pacchetti.

Gli aggiornamenti delle immagini forniscono un livello di confidenza maggiore nello stato finale del dispositivo. È in genere più semplice replicare i risultati di un aggiornamento di immagine tra un ambiente di pre-produzione e un ambiente di produzione, poiché non comporta le stesse esigenze dei pacchetti e delle relative dipendenze. A causa della natura atomica, è anche possibile adottare facilmente un modello di failover A/B.

Questa esercitazione illustra la procedura per completare un aggiornamento end-to-end basato su immagini con l'aggiornamento del dispositivo per l'hub Internet. 

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Scarica immagine
> * Aggiungere un tag al dispositivo Internet delle cose
> * Importazione di un aggiornamento
> * Creare un gruppo di dispositivi
> * Distribuire un aggiornamento dell'immagine
> * Monitorare la distribuzione degli aggiornamenti

## <a name="prerequisites"></a>Prerequisiti
* Se non è già stato fatto, creare un' [istanza e un account di aggiornamento del dispositivo](create-device-update-account.md), inclusa la configurazione di un hub.

## <a name="download-image"></a>Scarica immagine

Sono disponibili tre immagini come parte degli "asset" in una determinata versione di GitHub per l' [aggiornamento del dispositivo](https://github.com/Azure/iot-hub-device-update/releases). Vengono fornite l'immagine di base (Adu-base-image) e un'immagine di aggiornamento (Adu-Update-image), in modo da poter provare rollout a versioni diverse senza dover eseguire il flashing della scheda SD sul dispositivo. A tale scopo, è necessario caricare le immagini di aggiornamento nell'aggiornamento del dispositivo per il servizio hub Internet, come parte dell'importazione.

## <a name="flash-sd-card-with-image"></a>Scheda flash SD con immagine

Usando lo strumento di lampeggiamento del sistema operativo preferito, installare l'immagine di base dell'aggiornamento del dispositivo (Adu-base-image) nella scheda SD che verrà usata nel dispositivo Raspberry Pi 3 B +.

### <a name="using-bmaptool-to-flash-sd-card"></a>Uso di bmaptool per la scheda SD flash

1. Se non è già stato fatto, installare l' `bmaptool` utilità.

   ```shell
   sudo apt-get install bmap-tools
   ```

2. Individuare il percorso per la scheda SD in `/dev` . Il percorso dovrebbe essere simile a `/dev/sd*` o `/dev/mmcblk*` . `dmesg`Per individuare il percorso corretto, è possibile utilizzare l'utilità.

3. Prima di lampeggiare, sarà necessario smontare tutte le partizioni montate.

   ```shell
   sudo umount /dev/<device>
   ```

4. Assicurarsi di disporre delle autorizzazioni di scrittura per il dispositivo.

   ```shell
   sudo chmod a+rw /dev/<device>
   ```

5. facoltativo. Per un lampeggio più veloce, scaricare il file Bimap insieme al file di immagine e posizionarlo nella stessa directory.

6. Lampeggia la scheda SD.

   ```shell
   sudo bmaptool copy <path to image> /dev/<device>
   ```
   
L'aggiornamento del dispositivo per il software hub Azure Internet è soggetto alle condizioni di licenza seguenti:
   * [Aggiornamento del dispositivo per la licenza dell'hub Internet](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
   * [Licenza client di ottimizzazione recapito](https://github.com/microsoft/do-client/blob/main/LICENSE)
   
Leggere le condizioni di licenza prima di utilizzare l'agente. L'installazione e l'utilizzo costituiscono l'accettazione di tali condizioni. Se non si accettano le condizioni di licenza, non usare l'aggiornamento del dispositivo per l'agente hub Internet delle cose.

## <a name="create-device-in-iot-hub-and-get-connection-string"></a>Creare un dispositivo nell'hub Internet e ottenere la stringa di connessione

A questo punto, il dispositivo deve essere aggiunto all'hub Azure.  Dall'hub Azure, verrà generata una stringa di connessione per il dispositivo.

1. Dal portale di Azure avviare l'hub di aggiornamento del dispositivo.
2. Creare un nuovo dispositivo.
3. Sul lato sinistro della pagina passare a "esploratori" > "dispositivi Internet" > selezionare "nuovo".
4. Specificare un nome per il dispositivo in "ID dispositivo". Verificare che sia selezionata l'opzione "genera chiavi automaticamente".
5. Selezionare "Salva".
6. A questo punto verrà visualizzata la pagina "dispositivi" e il dispositivo creato dovrebbe essere presente nell'elenco. Selezionare il dispositivo.
7. In visualizzazione dispositivo selezionare l'icona "copia" accanto a "stringa di connessione primaria".
8. Incollare i caratteri copiati in un punto qualsiasi per un uso successivo nei passaggi seguenti.
   **Questa stringa copiata è la stringa di connessione del dispositivo**.

## <a name="provision-connection-string-on-sd-card"></a>Eseguire il provisioning della stringa di connessione sulla scheda SD

1. Verificare che il PI3 di Raspberry sia connesso alla rete.
2. In PowerShell usare il comando seguente per SSH nel dispositivo
   ```markdown
   ssh raspberrypi3 -l root
      ```
4. Immettere login come ' root ' e la password deve essere lasciata vuota.
5. Dopo aver eseguito correttamente la connessione SSH al dispositivo, eseguire i comandi seguenti
 
Sostituire `<device connection string>` con la stringa di connessione
 ```markdown
    echo "connection_string=<device connection string>" > adu-conf.txt  
    echo "aduc_manufacturer=ADUTeam" >> adu-conf.txt
    echo "aduc_model=RefDevice" >> adu-conf.txt
   ```

## <a name="connect-the-device-in-device-update-iot-hub"></a>Connettere il dispositivo nell'hub degli aggiornamenti del dispositivo

1. Sul lato sinistro della pagina selezionare "dispositivi Internet" in "esploratori".
2. Selezionare il collegamento con il nome del dispositivo.
3. Nella parte superiore della pagina selezionare "dispositivo gemello".
4. Nella sezione "segnalata" delle proprietà del dispositivo gemello, cercare la versione del kernel Linux.
Per un nuovo dispositivo, che non ha ricevuto un aggiornamento dall'aggiornamento del dispositivo, il valore [DeviceManagement: DeviceInformation: 1. swVersion](device-update-plug-and-play.md) rappresenterà la versione del firmware in esecuzione nel dispositivo.  Dopo che un aggiornamento è stato applicato a un dispositivo, l'aggiornamento del dispositivo userà il valore della proprietà [AzureDeviceUpdateCore: ClientMetadata: 4. installedUpdateId](device-update-plug-and-play.md) per rappresentare la versione del firmware in esecuzione nel dispositivo.
5. I file di immagine di base e di aggiornamento hanno un numero di versione nel nome file.

   ```markdown
   adu-<image type>-image-<machine>-<version number>.<extension>
   ```

Usare tale numero di versione nel passaggio importa aggiornamento seguente.

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
5. Selezionare l'icona della cartella o la casella di testo in "selezionare un file manifesto di importazione". Viene visualizzata una finestra di dialogo di selezione file. Selezionare il manifesto di importazione creato in precedenza.  Selezionare quindi l'icona della cartella o la casella di testo in "selezionare uno o più file di aggiornamento". Viene visualizzata una finestra di dialogo di selezione file. Selezionare il file di aggiornamento che si desidera distribuire nei dispositivi Internet delle cose.
   
   :::image type="content" source="media/import-update/select-update-files.png" alt-text="Screenshot che mostra la selezione del file di aggiornamento." lightbox="media/import-update/select-update-files.png":::

5. Selezionare l'icona della cartella o la casella di testo in "selezionare un contenitore di archiviazione". Selezionare quindi l'account di archiviazione appropriato.

6. Se è già stato creato un contenitore, è possibile riusarlo. In caso contrario, selezionare "+ contenitore" per creare un nuovo contenitore di archiviazione per gli aggiornamenti.  Selezionare il contenitore che si vuole usare e fare clic su "Seleziona".
  
  :::image type="content" source="media/import-update/container.png" alt-text="Screenshot che mostra la selezione del contenitore." lightbox="media/import-update/container.png":::

7. Selezionare "Invia" per avviare il processo di importazione.

8. Viene avviato il processo di importazione e la schermata passa alla sezione "cronologia di importazione". Selezionare "Aggiorna" per visualizzare lo stato di avanzamento fino al completamento del processo di importazione. A seconda delle dimensioni dell'aggiornamento, l'operazione può essere completata in pochi minuti, ma potrebbe richiedere più tempo.
   
   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="Screenshot che mostra la sequenza di importazione degli aggiornamenti." lightbox="media/import-update/update-publishing-sequence-2.png":::

9. Quando la colonna stato indica che l'importazione è riuscita, selezionare l'intestazione "pronto per la distribuzione". Nell'elenco verrà visualizzato l'aggiornamento importato.

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

A questo punto è stato completato un aggiornamento dell'immagine end-to-end corretto usando l'aggiornamento del dispositivo per l'hub Internet in un dispositivo Raspberry Pi 3 B +. 

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessario, pulire l'account di aggiornamento del dispositivo, l'istanza, l'hub e il dispositivo Internet delle cose. 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Agente di riferimento del simulatore](device-update-simulator.md)
