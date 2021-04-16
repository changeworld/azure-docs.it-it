---
title: Servizio di attestazione FPGFA di Azure
description: Servizio di attestazione per le macchine virtuali serie NP.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: vikancha
ms.openlocfilehash: a3408d30a9caa24355cf3976235c3a9b8061b95f
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531231"
---
# <a name="fpga-attestation-for-azure-np-series-vms-preview"></a>Attestazione FPGA per macchine virtuali NP-Series Azure (anteprima)

Il servizio di attestazione FPGA esegue una serie di convalide in un file di checkpoint di progettazione (denominato "netlist") generato dal set di strumenti Xilinx e produce un file contenente l'immagine convalidata (denominata "bitstream") che può essere caricata nella scheda FPGA Xilinx U250 in una macchina virtuale della serie NP.  

## <a name="prerequisites"></a>Prerequisiti  

Saranno necessari una sottoscrizione di Azure e un account Archiviazione di Azure azure. La sottoscrizione consente di accedere ad Azure e l'account di archiviazione viene usato per contenere i file netlist e di output del servizio di attestazione.  

Vengono forniti script di PowerShell e Bash per inviare richieste di attestazione.   Gli script usano l'interfaccia della riga di comando di Azure, che può essere eseguita in Windows e Linux. PowerShell può essere eseguito in Windows, Linux e macOS.  

Download dell'interfaccia della riga di comando di Azure (obbligatorio):  

https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest  

Download di PowerShell per Windows, Linux e macOS (solo per gli script di PowerShell):  

https://docs.microsoft.com/powershell/scripting/install/installing-powershell?view=powershell-7  

È necessario avere il tenant e l'ID sottoscrizione autorizzati a inviare al servizio di attestazione. Visitare https://aka.ms/AzureFPGAAttestationPreview per richiedere l'accesso. 

## <a name="building-your-design-for-attestation"></a>Creazione della progettazione per l'attestazione  

Il set di strumenti Xilinx preferito per la creazione di progetti è Vitis 2020.2. È possibile usare i file netlist creati con una versione precedente del set di strumenti e che sono ancora compatibili con la versione 2020.2. Assicurarsi di aver caricato la shell corretta per la compilazione. La versione attualmente supportata è xilinx_u250_gen3x16_xdma_2_1_202010_1. I file di supporto possono essere scaricati dalla sala Xilinx Alveo. 

È necessario includere l'argomento seguente in Vitis (riga di comando v++) per compilare un file xclbin che contiene un netlist anziché un bitstream.   

```--advanced.param compiler.acceleratorBinaryContent=dcp  ```

## <a name="logging-into-azure"></a>Accesso ad Azure  

Prima di eseguire qualsiasi operazione con Azure, è necessario accedere ad Azure e impostare la sottoscrizione autorizzata a chiamare il servizio. A questo ```az login``` ```az account set –s <Sub ID or Name>``` scopo, usare i comandi e . Altre informazioni su questo processo sono documentate qui:  

https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest. Usare l'opzione "accedi in modo interattivo" o "accedi con credenziali" nella riga di comando.  

## <a name="creating-a-storage-account-and-blob-container"></a>Creazione di un account di archiviazione e di un contenitore BLOB  

Il file netlist deve essere caricato in un contenitore BLOB di archiviazione di Azure per l'accesso da parte del servizio di attestazione.  

Fare riferimento a questa pagina per altre informazioni sulla creazione dell'account, un contenitore e sul caricamento di netlist come BLOB in tale contenitore: [https://docs.microsoft.com/en-us/azure/storage/blobs/storage-quickstart-blobs-cli](/azure/storage/blobs/storage-quickstart-blobs-cli) .  

È anche possibile usare il portale di Azure anche a questo scopo.  

## <a name="upload-your-netlist-file-to-azure-blob-storage"></a>Caricare il file netlist nell'archivio BLOB di Azure  

Esistono diversi modi per copiare il file. Di seguito è riportato un esempio di uso del cmdlet az storage upload. I comandi az vengono eseguiti sia in Linux che in Windows. È possibile scegliere qualsiasi nome per il nome "blob", ma assicurarsi di mantenere l'estensione xclbin. 

```az storage blob upload --account-name <storage account to receive netlist> --container-name <blob container name> --name <blob filename> --file <local file with netlist>  ```

## <a name="download-the-attestation-scripts"></a>Scaricare gli script di attestazione  

Gli script di convalida possono essere scaricati dal contenitore BLOB di archiviazione di Azure seguente:  

https://fpgaattestation.blob.core.windows.net/validationscripts/validate.zip  

Il file ZIP ha due script di PowerShell, uno da inviare e l'altro da monitorare, mentre il terzo è uno script bash che esegue entrambe le funzioni.  

## <a name="running-the-attestation-scripts"></a>Esecuzione degli script di attestazione  

Per eseguire gli script, è necessario specificare il nome dell'account di archiviazione, il nome del contenitore BLOB in cui è archiviato il file netlist e il nome del file netlist. Sarà anche necessario creare una firma di accesso condiviso del servizio che concedi l'accesso in lettura/scrittura al contenitore (non netlist). Questa firma di accesso condiviso viene usata dal servizio di attestazione per creare una copia locale del file netlist e per eseguire il write back dei file di output risultanti del processo di convalida nel contenitore.  

Una panoramica delle firme di accesso condiviso è disponibile qui con informazioni specifiche sulla firma di accesso condiviso del servizio disponibile qui. La pagina Firma di accesso condiviso del servizio include un'importante attenzione per la protezione della firma di accesso condiviso generata.  Leggere l'attenzione per comprendere la necessità di proteggere la firma di accesso condiviso da un uso dannoso o non intenzionale.  

È possibile generare una firma di accesso condiviso per il contenitore usando il cmdlet az storage container generate-sas. Specificare un'ora di scadenza in formato UTC che sia almeno qualche ora dopo l'ora di invio. circa 6 ore dovrebbero essere più che adeguate.  

Se si desidera usare le directory virtuali, è necessario includere la gerarchia di directory come parte dell'argomento contenitore. Ad esempio, se si dispone di un contenitore denominato "netlists" e si dispone di una directory virtuale denominata "image1" che contiene il BLOB netlist, è necessario specificare "netlists/image1" come nome del contenitore. Aggiungere eventuali nomi di directory aggiuntivi per specificare una gerarchia più profonda. 

### <a name="powershell"></a>PowerShell   

```$sas=$(az storage container generate-sas --account-name <storage acct name> --name <blob container name> --https-only --permissions rwc --expiry <e.g., 2021-01-07T17:00Z> --output tsv)  ```

```.\Validate-FPGAImage.ps1 -StorageAccountName <storage acct name> -Container <blob container name> -BlobContainerSAS $sas -NetlistName <netlist blob filename>  ```

### <a name="bash"></a>Bash  

``` sas=az storage container generate-sas --account-name <storage acct name> --name <blob container name> --https-only --permissions rwc --expiry <2021-01-07T17:00Z> --output tsv  ```

```validate-fpgaimage.sh --storage-account <storage acct name> --container <blob container name> --netlist-name <netlist blob filename> --blob-container-sas $sas ``` 

## <a name="checking-on-the-status-of-your-submission"></a>Controllo dello stato dell'invio  

Il servizio attestazione restituirà l'ID orchestrazione dell'invio. Gli script di invio avviano automaticamente il monitoraggio dell'invio effettuando il polling per il completamento. L'ID orchestrazione è il modo principale per esaminare cosa è successo all'invio, quindi tenere presente che in caso di problemi. Come punti di riferimento, il completamento dell'attestazione richiede circa 30 minuti per un file netlist di piccole dimensioni (dimensioni di 300 MB). un file da 1,6 GB ha richiesto un'ora. 

È possibile chiamare lo script Monitor-Validation.ps1 in qualsiasi momento per ottenere lo stato e i risultati dell'attestazione, fornendo l'ID orchestrazione come argomento:  

```.\Monitor-Validation.ps1 -OrchestrationId < Orchestration ID>  ```

In alternativa, è possibile inviare una richiesta POST HTTP all'endpoint del servizio di attestazione:  

https://fpga-attestation.azurewebsites.net/api/ComputeFPGA_HttpGetStatus  

Il corpo della richiesta deve contenere l'ID sottoscrizione, l'ID tenant e l'ID orchestrazione della richiesta di attestazione:  

```
{  

  "OrchestrationId": ”< orchestration ID>”,  

  "ClientSubscriptionId": “<your subscription ID>”,  

  "ClientTenantId": “<your tenant ID>”  

}
```

## <a name="post-validation-steps"></a>Passaggi di post-convalida

Il servizio scriverà l'output nel contenitore. Se il passaggio di convalida ha esito positivo, il contenitore avrà il file netlist originale (abc.xclbin), un file con bitstream (abc.bit.xclbin), un file che identifica il percorso privato del bitstream archiviato (abc.azure.xclbin) e quattro file di log: uno per il processo di avvio (abc-log.txt) e uno per le tre fasi parallele che eseguono la convalida. Questi sono denominati *logPhaseX.txt dove X è un numero per la fase. Azure.xclbin viene usato nella macchina virtuale per segnalare il caricamento dell'immagine convalidata nell'U250. 

Se la convalida non è riuscita, viene scritto un file error-*.txt che indica il passaggio non riuscito. Controllare anche i file di log se il log degli errori indica che l'attestazione non è riuscita. Quando si contatta Microsoft per il supporto, assicurarsi di includere tutti questi file come parte della richiesta di supporto insieme all'ID orchestrazione.  

È possibile usare il portale di Azure per creare il contenitore, nonché caricare netlist e scaricare i file bitstream e di log. L'invio di una richiesta di attestazione e il monitoraggio dello stato di avanzamento tramite il portale non sono attualmente supportati e devono essere eseguiti tramite script come descritto in precedenza. 

