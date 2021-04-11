---
title: Servizio di attestazione di Azure FPGFA
description: Servizio di attestazione per le macchine virtuali della serie NP.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: vikancha
ms.openlocfilehash: 563155bb6559f8443f1453a65fa0b1574af106f7
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106556171"
---
# <a name="fpga-attestation-for-azure-np-series-vms-preview"></a>Attestazione FPGA per macchine virtuali NP-Series di Azure (anteprima)

Il servizio di attestazione FPGA esegue una serie di convalide in un file di checkpoint di progettazione (denominato "netlist") generato dal set di strumenti Xilinx e produce un file che contiene l'immagine convalidata, denominata "bitstream", che può essere caricata nella scheda Xilinx U250 FPGA in una VM serie NP.  

## <a name="prerequisites"></a>Prerequisiti  

Sono necessari una sottoscrizione di Azure e un account di archiviazione di Azure. La sottoscrizione consente di accedere ad Azure e l'account di archiviazione viene usato per memorizzare i file di netlist e di output del servizio di attestazione.  

Sono disponibili script di PowerShell e bash per inviare richieste di attestazione.   Gli script usano l'interfaccia della riga di comando di Azure, che può essere eseguita in Windows e Linux. PowerShell può essere eseguito in Windows, Linux e macOS.  

Download dell'interfaccia della riga di comando Azure (obbligatorio):  

https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest  

PowerShell per il download di Windows, Linux e macOS (solo per gli script di PowerShell):  

https://docs.microsoft.com/powershell/scripting/install/installing-powershell?view=powershell-7  

È necessario che il tenant e l'ID sottoscrizione siano autorizzati a inviare al servizio di attestazione. Visitare https://aka.ms/AzureFPGAAttestationPreview per richiedere l'accesso. 

## <a name="building-your-design-for-attestation"></a>Creazione della progettazione per l'attestazione  

Il set di strumenti Xilinx preferito per la compilazione di progetti è Vitis 2020,2. È possibile usare i file netlist creati con una versione precedente del set di strumenti e ancora compatibili con 2020,2. Assicurarsi di aver caricato la shell corretta per la compilazione. La versione attualmente supportata è xilinx_u250_gen3x16_xdma_2_1_202010_1. È possibile scaricare i file di supporto da Xilinx alveo lounge. 

Per compilare un file xclbin che contiene un netlist anziché un bitstream, è necessario includere il seguente argomento in Vitis (riga cmd v + +).   

```--advanced.param compiler.acceleratorBinaryContent=dcp  ```

## <a name="logging-into-azure"></a>Accesso ad Azure  

Prima di eseguire qualsiasi operazione con Azure, è necessario accedere ad Azure e impostare la sottoscrizione autorizzata a chiamare il servizio. Usare i ```az login``` ```az account set –s <Sub ID or Name>``` comandi e a questo scopo. Ulteriori informazioni su questo processo sono descritte di seguito:  

https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest. Usare l'opzione ' accedi in modo interattivo ' o ' accedi con credenziali ' nella riga di comando.  

## <a name="creating-a-storage-account-and-blob-container"></a>Creazione di un account di archiviazione e di un contenitore BLOB  

Il file netlist deve essere caricato in un contenitore BLOB di archiviazione di Azure per l'accesso da parte del servizio di attestazione.  

Vedere questa pagina per altre informazioni sulla creazione dell'account, un contenitore e il caricamento del netlist come BLOB in tale contenitore: https://docs.microsoft.com/azure/storage/blobs/storage-quickstartblobs-cli .  

È anche possibile usare il portale di Azure anche per questa operazione.  

## <a name="upload-your-netlist-file-to-azure-blob-storage"></a>Caricare il file netlist nell'archivio BLOB di Azure  

Esistono diversi modi per copiare il file; di seguito è riportato un esempio di uso del cmdlet AZ storage upload. I comandi AZ vengono eseguiti in Linux e Windows. È possibile scegliere qualsiasi nome per il nome del BLOB, ma assicurarsi di mantenere l'estensione xclbin. 

```az storage blob upload --account-name <storage account to receive netlist> container-name <blob container name> --name <blob filename> --file <local file with netlist>  ```

## <a name="download-the-attestation-scripts"></a>Scaricare gli script di attestazione  

Gli script di convalida possono essere scaricati dal seguente contenitore BLOB di archiviazione di Azure:  

https://fpgaattestation.blob.core.windows.net/validationscripts/validate.zip  

Il file zip contiene due script di PowerShell, uno da inviare e l'altro da monitorare mentre il terzo file è uno script bash che esegue entrambe le funzioni.  

## <a name="running-the-attestation-scripts"></a>Esecuzione degli script di attestazione  

Per eseguire gli script, è necessario specificare il nome dell'account di archiviazione, il nome del contenitore BLOB in cui è archiviato il file netlist e il nome del file netlist. Sarà anche necessario creare una firma di accesso condiviso (SAS) del servizio che conceda l'accesso in lettura/scrittura al contenitore (non netlist). Questa firma di accesso condiviso viene usata dal servizio di attestazione per creare una copia locale del file netlist e per eseguire il writeback dei file di output risultanti del processo di convalida nel contenitore.  

Una panoramica delle firme di accesso condiviso è disponibile qui con informazioni specifiche sulla firma di accesso condiviso del servizio disponibile qui. La pagina firma di accesso condiviso del servizio include un'importante attenzione alla protezione della firma di accesso condiviso generata.  Leggere attentamente per comprendere la necessità di proteggere la firma di accesso condiviso da un utilizzo dannoso o imprevisto.  

È possibile generare una firma di accesso condiviso per il contenitore usando il cmdlet AZ Storage container generate-SAS. Specificare un'ora di scadenza in formato UTC che è almeno alcune ore dopo l'ora di invio; circa 6 ore devono essere più che adeguate.  

Se si desidera utilizzare le directory virtuali, è necessario includere la gerarchia di directory come parte dell'argomento del contenitore. Se, ad esempio, si dispone di un contenitore denominato "netlist" e di una directory virtuale denominata "image1" che contiene il BLOB netlist, è necessario specificare "netlist/image1" come nome del contenitore. Aggiungere altri nomi di directory per specificare una gerarchia più profonda. 

### <a name="powershell"></a>PowerShell   

```$sas=$(az storage container generate-sas --account-name <storage acct name> -name <blob container name> --https-only --permissions rwc --expiry <e.g., 2021-01-07T17:00Z> --output tsv)  ```

```.\Validate-FPGAImage.ps1 -StorageAccountName <storage acct name> -Container <blob container name> -BlobContainerSAS $sas -NetlistName <netlist blob filename>  ```

### <a name="bash"></a>Bash  

``` sas=az storage container generate-sas --account-name <storage acct name> -name <blob container name> --https-only --permissions rwc --expiry <2021-01-07T17:00Z> --output tsv  ```

```validate-fpgaimage.sh --storage-account <storage acct name> --container <blob container name> --netlist-name <netlist blob filename> --blob-container-sas $sas ``` 

## <a name="checking-on-the-status-of-your-submission"></a>Verifica dello stato dell'invio  

Il servizio di attestazione restituirà l'ID dell'orchestrazione dell'invio. Gli script di invio avviano automaticamente il monitoraggio dell'invio eseguendo il polling per il completamento. L'ID orchestrazione è il modo principale per esaminare gli eventi che si sono verificati durante l'invio. tenere presente che in caso di problemi. Come punti di riferimento, l'attestazione richiede circa 30 minuti per il completamento di un file netlist di piccole dimensioni (300MB); un file da 1,6 GB ha richiesto un'ora. 

È possibile chiamare lo script di Monitor-Validation.ps1 in qualsiasi momento per ottenere lo stato e i risultati dell'attestazione, fornendo l'ID orchestrazione come argomento:  

```.\Monitor-Validation.ps1 -OrchestrationId < Orchestration ID>  ```

In alternativa, è possibile inviare una richiesta HTTP post all'endpoint del servizio di attestazione:  

https://fpga-attestation.azurewebsites.net/api/ComputeFPGA_HttpGetStatus  

Il corpo della richiesta deve contenere l'ID sottoscrizione, l'ID tenant e l'ID orchestrazione della richiesta di attestazione:  

```
{  

  "OrchestrationId": ”< orchestration ID>”,  

  "ClientSubscriptionId": “<your subscription ID>”,  

  "ClientTenantId": “<your tenant ID>”  

}
```

## <a name="post-validation-steps"></a>Passaggi successivi alla convalida

Il servizio scriverà di nuovo l'output nel contenitore. Se il passaggio di convalida ha esito positivo, il contenitore avrà il file netlist originale (ABC. xclbin), un file con Bitstream (ABC. bit. xclbin), un file che identifica la posizione privata del Bitstream archiviato (ABC. Azure. xclbin) e quattro file di log: uno per il processo di avvio (abc-log.txt) e uno per le tre fasi parallele che eseguono la convalida Questi sono denominati * logPhaseX.txt dove X è un numero per la fase. Azure. xclbin viene usato nella macchina virtuale per segnalare il caricamento dell'immagine convalidata in U250. 

Se la convalida non è riuscita, viene scritto un file Error-*. txt che indica il passaggio non riuscito. Controllare inoltre i file di log se il log degli errori indica che l'attestazione non è riuscita. Quando ci si rivolge al supporto tecnico, assicurarsi di includere tutti questi file come parte della richiesta di supporto insieme all'ID orchestrazione.  

È possibile usare la portale di Azure per creare il contenitore e caricare il netlist e scaricare i file Bitstream e log. L'invio di una richiesta di attestazione e il monitoraggio dello stato di avanzamento tramite il portale non sono al momento supportati e devono essere eseguiti tramite script come descritto in precedenza. 

