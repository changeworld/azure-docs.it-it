---
title: Esercitazione sull'aggiornamento 18,04 del dispositivo per l'hub di Azure Microsoft Docs
description: Introduzione all'aggiornamento del dispositivo per l'hub di Azure Internet con l'agente di pacchetti di Ubuntu server 18,04 x64.
author: vimeht
ms.author: vimeht
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 6464ad632251053ac481fbd1f6a3e1197aa470df
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106121303"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-package-agent-on-ubuntu-server-1804-x64"></a>Esercitazione 18,04 sull'aggiornamento del dispositivo per l'hub di Azure

L'aggiornamento del dispositivo per l'hub Internet supporta due forme di aggiornamenti, basati su immagini e basati su pacchetti.

Gli aggiornamenti basati su pacchetti sono aggiornamenti mirati che modificano solo un componente o un'applicazione specifica nel dispositivo. Gli aggiornamenti basati su pacchetti comportano un consumo ridotto della larghezza di banda e consentono di ridurre il tempo necessario per scaricare e installare l'aggiornamento. Gli aggiornamenti dei pacchetti consentono in genere un minor tempo di inattività per i dispositivi quando si applica un aggiornamento ed evitano il sovraccarico della creazione di immagini.

Questa esercitazione end-to-end illustra l'aggiornamento di Azure IoT Edge in Ubuntu server 18,04 x64 con l'agente del pacchetto di aggiornamento del dispositivo. Anche se nell'esercitazione viene illustrato l'aggiornamento di IoT Edge, è possibile aggiornare altri pacchetti, ad esempio il motore di contenitori usato.

Gli strumenti e i concetti in questa esercitazione si applicano anche se si prevede di usare una configurazione della piattaforma del sistema operativo diversa. Completare questa introduzione a un processo di aggiornamento end-to-end, quindi scegliere la forma preferita di aggiornamento e piattaforma del sistema operativo per approfondire i dettagli.

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Scaricare e installare l'agente di aggiornamento del dispositivo e le relative dipendenze
> * Aggiungere un tag al dispositivo
> * Importazione di un aggiornamento
> * Creare un gruppo di dispositivi
> * Distribuire un aggiornamento del pacchetto
> * Monitorare la distribuzione degli aggiornamenti

## <a name="prerequisites"></a>Prerequisiti

* Se non è già stato fatto, creare un' [istanza e un account di aggiornamento del dispositivo](create-device-update-account.md), inclusa la configurazione di un hub.
* [Stringa di connessione per un dispositivo IOT Edge](../iot-edge/how-to-register-device.md?view=iotedge-2020-11&preserve-view=true#view-registered-devices-and-retrieve-connection-strings).

## <a name="prepare-a-device"></a>Preparare un dispositivo
### <a name="using-the-automated-deploy-to-azure-button"></a>Usare il pulsante Distribuisci automaticamente in Azure

Per praticità, in questa esercitazione viene usato un modello di [Azure Resource Manager](../azure-resource-manager/templates/overview.md) basato su [cloud-init](../virtual-machines/linux/using-cloud-init.md)che consente di configurare rapidamente una macchina virtuale Ubuntu 18,04 LTS. Installa sia il runtime di Azure IoT Edge che l'agente del pacchetto di aggiornamento del dispositivo, quindi configura automaticamente il dispositivo con le informazioni di provisioning usando la stringa di connessione del dispositivo per un dispositivo IoT Edge (prerequisito) fornito dall'utente. Il modello di Azure Resource Manager evita inoltre la necessità di avviare una sessione SSH per completare la configurazione.

1. Per iniziare, fare clic sul pulsante seguente:

   [![Pulsante Distribuisci in Azure per iotedge-vm-deploy](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fdevice-update-tutorial%2FedgeDeploy.json)

1. Nella finestra appena avviata compilare i campi del modulo disponibili:

    > [!div class="mx-imgBorder"]
    > [![Screenshot che mostra il modello iotedge-vm-deploy](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)

    **Sottoscrizione**: sottoscrizione di Azure attiva in cui distribuire la macchina virtuale.

    **Gruppo di risorse**: gruppo di risorse esistente o appena creato per contenere la macchina virtuale e le risorse associate.

    **Prefisso dell'etichetta DNS**: un valore obbligatorio a scelta usato per anteporre il nome host della macchina virtuale.

    **Nome utente amministratore**: un nome utente, a cui verranno forniti i privilegi radice per la distribuzione.

    **Stringa di connessione del dispositivo**: una [stringa di connessione del dispositivo](../iot-edge/how-to-register-device.md) per un dispositivo che è stato creato all'interno dell' [Hub](../iot-hub/about-iot-hub.md).

    **VM size (dimensioni VM**): [dimensioni](../cloud-services/cloud-services-sizes-specs.md) della macchina virtuale da distribuire

    **Versione del sistema operativo Ubuntu**: versione del sistema operativo Ubuntu da installare nella macchina virtuale di base. Lasciare invariato il valore predefinito in quanto verrà impostato su Ubuntu 18,04-LTS già.

    **Località**: l' [area geografica](https://azure.microsoft.com/global-infrastructure/locations/) in cui distribuire la macchina virtuale. per impostazione predefinita, questo valore viene impostato sul percorso del gruppo di risorse selezionato.

    **Tipo di autenticazione**: scegliere **sshPublicKey** o **password** in base alle proprie preferenze.

    **Password amministratore o chiave**: il valore della chiave pubblica SSH o il valore della password a seconda della scelta del tipo di autenticazione.

    Dopo aver compilato tutti i campi, selezionare la casella di controllo nella parte inferiore della pagina per accettare i termini e selezionare **Acquista** per iniziare la distribuzione.

1. Verificare che la distribuzione sia stata completata correttamente. Attendere alcuni minuti dopo il completamento della distribuzione perché la post-installazione e la configurazione finiscano l'installazione di IoT Edge e l'agente di aggiornamento del pacchetto del dispositivo.

   Una risorsa macchina virtuale deve essere stata distribuita nel gruppo di risorse selezionato.  Prendere nota del nome del computer nel formato `vm-0000000000000` . Prendere nota anche del valore di **Nome DNS** associato, che deve essere nel formato `<dnsLabelPrefix>`.`<location>`.cloudapp.azure.com.

    È possibile ottenere il valore di **Nome DNS** dalla sezione **Panoramica** della nuova macchina virtuale distribuita nel portale di Azure.

    > [!div class="mx-imgBorder"]
    > [![Screenshot che mostra il nome DNS della macchina virtuale iotedge](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

   > [!TIP]
   > Se si vuole eseguire SSH in questa macchina virtuale dopo l'installazione, usare il **nome DNS** associato con il comando: `ssh <adminUsername>@<DNS_Name>`

### <a name="optional-manually-prepare-a-device"></a>Opzionale Preparare manualmente un dispositivo
Analogamente ai passaggi automatizzati dallo [script cloud-init](https://github.com/Azure/iotedge-vm-deploy/blob/1.2.0-rc4/cloud-init.txt), di seguito sono riportati i passaggi manuali per installare e configurare il dispositivo. Questa procedura può essere usata per preparare un dispositivo fisico.

1. Seguire le istruzioni per [installare il runtime di Azure IOT Edge](../iot-edge/how-to-install-iot-edge.md?view=iotedge-2020-11&preserve-view=true).
   > [!NOTE]
   > L'agente del pacchetto di aggiornamento del dispositivo non dipende da IoT Edge. Tuttavia, si basa sul daemon di identità del servizio Internet delle cose installato con IoT Edge (1.2.0 e versioni successive) per ottenere un'identità e connettersi all'hub Internet.
   >
   > Sebbene non sia trattato in questa esercitazione, il daemon del servizio di identità di Internet delle cose [può essere installato autonomamente nei dispositivi](https://azure.github.io/iot-identity-service/packaging.html)Internet delle cose. La sequenza di installazione è importante. È necessario installare l'agente del pacchetto di aggiornamento del dispositivo _dopo_ il servizio di identità Internet. In caso contrario, l'agente del pacchetto non verrà registrato come componente autorizzato per stabilire una connessione all'hub di Internet.

1. Quindi, installare i pacchetti dell'agente di aggiornamento del dispositivo. deb.

   ```bash
   sudo apt-get install deviceupdate-agent deliveryoptimization-plugin-apt 
   ```

L'aggiornamento del dispositivo per i pacchetti software dell'hub Azure è soggetto alle condizioni di licenza seguenti:
  * [Aggiornamento del dispositivo per la licenza dell'hub Internet](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
  * [Licenza client di ottimizzazione recapito](https://github.com/microsoft/do-client/blob/main/LICENSE)

Leggere le condizioni di licenza prima di utilizzare un pacchetto. L'installazione e l'utilizzo di un pacchetto costituiscono l'accettazione di tali condizioni. Se non si accettano le condizioni di licenza, non utilizzare il pacchetto.

## <a name="add-a-tag-to-your-device"></a>Aggiungere un tag al dispositivo

1. Accedere a [portale di Azure](https://portal.azure.com) e passare all'hub Internet delle cose.

2. Da "IoT Edge" nel riquadro di spostamento a sinistra trovare il dispositivo IoT Edge e passare al dispositivo gemello o al modulo gemello.

3. Nel modulo gemello del modulo Device Update Agent eliminare qualsiasi valore di tag di aggiornamento del dispositivo esistente impostando il valore su null. Se si usa l'identità del dispositivo con l'agente di aggiornamento del dispositivo, apportare queste modifiche nel dispositivo gemello.

4. Aggiungere un nuovo valore del tag di aggiornamento del dispositivo come illustrato di seguito.

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            },
```

## <a name="import-update"></a>Importa aggiornamento

1. Passare alle [versioni di aggiornamento del dispositivo](https://github.com/Azure/iot-hub-device-update/releases) in GitHub e fare clic sull'elenco a discesa "asset".

3. Scaricare la facendo `Edge.package.update.samples.zip` clic su di essa.

5. Estrarre il contenuto della cartella per individuare un esempio di aggiornamento e i manifesti di importazione corrispondenti. 

2. In portale di Azure selezionare l'opzione aggiornamenti del dispositivo in gestione automatica dispositivi dalla barra di spostamento a sinistra nell'hub.

3. Selezionare la scheda aggiornamenti.

4. Selezionare "+ Importa nuovo aggiornamento".

5. Selezionare l'icona della cartella o la casella di testo in "selezionare un file manifesto di importazione". Viene visualizzata una finestra di dialogo di selezione file. Selezionare il `sample-1.0.1-aziot-edge-importManifest.json` manifesto di importazione dalla cartella scaricata in precedenza. Selezionare quindi l'icona della cartella o la casella di testo in "selezionare uno o più file di aggiornamento". Viene visualizzata una finestra di dialogo di selezione file. Selezionare il `sample-1.0.1-aziot-edge-apt-manifest.json` file di aggiornamento del manifesto APT dalla cartella scaricata in precedenza.
Con questo aggiornamento il `aziot-identity-service` e i pacchetti vengono aggiornati `aziot-edge` alla versione 1.2.0 ~ RC4-1 nel dispositivo.

   :::image type="content" source="media/import-update/select-update-files.png" alt-text="Screenshot che mostra la selezione del file di aggiornamento." lightbox="media/import-update/select-update-files.png":::

6. Selezionare l'icona della cartella o la casella di testo in "selezionare un contenitore di archiviazione". Selezionare quindi l'account di archiviazione appropriato.

7. Se è già stato creato un contenitore, è possibile riusarlo. In caso contrario, selezionare "+ contenitore" per creare un nuovo contenitore di archiviazione per gli aggiornamenti.  Selezionare il contenitore che si vuole usare e fare clic su "Seleziona".

   :::image type="content" source="media/import-update/container.png" alt-text="Screenshot che mostra la selezione del contenitore." lightbox="media/import-update/container.png":::

8. Selezionare "Invia" per avviare il processo di importazione.

9. Viene avviato il processo di importazione e la schermata passa alla sezione "cronologia di importazione". Selezionare "Aggiorna" per visualizzare lo stato di avanzamento fino al completamento del processo di importazione. A seconda delle dimensioni dell'aggiornamento, il processo di importazione può essere completato in pochi minuti, ma potrebbe richiedere più tempo.

   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="Screenshot che mostra la sequenza di importazione degli aggiornamenti." lightbox="media/import-update/update-publishing-sequence-2.png":::

10. Quando la colonna stato indica che l'importazione è riuscita, selezionare l'intestazione "pronto per la distribuzione". Nell'elenco verrà visualizzato l'aggiornamento importato.

[Altre](import-update.md) informazioni sull'importazione di aggiornamenti.

## <a name="create-update-group"></a>Crea gruppo di aggiornamento

1. Passare all'hub Internet delle cose precedentemente connesso all'istanza di aggiornamento del dispositivo.

1. Selezionare l'opzione aggiornamenti del dispositivo in gestione automatica dispositivi dalla barra di spostamento a sinistra.

1. Selezionare la scheda gruppi nella parte superiore della pagina.

1. Selezionare il pulsante Aggiungi per creare un nuovo gruppo.

1. Selezionare il tag dell'hub Internet delle cose creato nel passaggio precedente dall'elenco. Selezionare Crea gruppo di aggiornamento.

   :::image type="content" source="media/create-update-group/select-tag.PNG" alt-text="Screenshot che mostra la selezione di tag." lightbox="media/create-update-group/select-tag.PNG":::

[Altre](create-update-group.md) informazioni sull'aggiunta di tag e la creazione di gruppi di aggiornamento

## <a name="deploy-update"></a>Distribuisci aggiornamento

1. Una volta creato il gruppo, verrà visualizzato un nuovo aggiornamento per il gruppo di dispositivi, con un collegamento all'aggiornamento nella colonna _aggiornamenti disponibili_ . Potrebbe essere necessario eseguire l'aggiornamento una sola volta.

1. Fare clic sul collegamento all'aggiornamento disponibile.

1. Verificare che sia selezionato il gruppo corretto come gruppo di destinazione e pianificare la distribuzione

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="Seleziona aggiornamento" lightbox="media/deploy-update/select-update.png":::

   > [!TIP]
   > Per impostazione predefinita, la data e l'ora di inizio corrispondono a 24 ore dall'ora corrente. Assicurarsi di selezionare una data/ora diversa se si vuole che la distribuzione venga avviata in precedenza.

1. Selezionare Distribuisci aggiornamento.

1. Visualizzare il grafico di conformità. Si noterà che l'aggiornamento è in corso. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="Aggiornamento in corso" lightbox="media/deploy-update/update-in-progress.png":::

1. Al termine dell'aggiornamento del dispositivo, si noterà che l'aggiornamento del grafico di conformità e dei dettagli della distribuzione è identico. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="Aggiornamento completato" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>Monitorare una distribuzione degli aggiornamenti

1. Selezionare la scheda distribuzioni nella parte superiore della pagina.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="Scheda distribuzioni" lightbox="media/deploy-update/deployments-tab.png":::

1. Selezionare la distribuzione creata per visualizzare i dettagli della distribuzione.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Dettagli di distribuzione" lightbox="media/deploy-update/deployment-details.png":::

1. Selezionare Aggiorna per visualizzare i dettagli più recenti sullo stato. Continuare questo processo fino a quando lo stato diventa succeeded.

A questo punto è stato completato un aggiornamento end-to-end corretto con l'aggiornamento del dispositivo per l'hub Internet in un dispositivo Ubuntu server 18,04 x64. 

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessario, eseguire la pulizia dell'account di aggiornamento del dispositivo, dell'istanza, dell'hub Internet e del dispositivo IoT Edge (se la macchina virtuale è stata creata tramite il pulsante Distribuisci in Azure). A tale scopo, passare a ogni singola risorsa e selezionare "Elimina". Prima di eseguire la pulizia dell'account di aggiornamento del dispositivo, è necessario pulire un'istanza di aggiornamento del dispositivo.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiornamento dell'immagine in Raspberry Pi 3 B + esercitazione](device-update-raspberry-pi.md)
