---
title: Creare un canale conda personalizzato per la gestione dei pacchetti
description: Informazioni su come creare un canale conda personalizzato per la gestione dei pacchetti
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 528ba4a1be3650a81772d78a438f03611b9bd761
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102107682"
---
# <a name="create-a-custom-conda-channel-for-package-management"></a>Creare un canale conda personalizzato per la gestione dei pacchetti 
Quando si installano pacchetti Python, gestione pacchetti conda usa i canali per cercare i pacchetti. Potrebbe essere necessario creare un canale conda personalizzato per diversi motivi. Ad esempio, è possibile trovare quanto segue:

- l'area di lavoro è exfiltration dati protetti e le connessioni in uscita sono bloccate.  
- sono presenti pacchetti che non si vogliono caricare nei repository pubblici.
- si vuole configurare un repository alternativo per gli utenti all'interno dell'area di lavoro.

In questo articolo verrà fornita una guida dettagliata per la creazione di un canale conda personalizzato all'interno dell'account Azure Data Lake Storage.

## <a name="set-up-your-local-machine"></a>Configurare il computer locale

1. Installare conda nel computer locale. È possibile fare riferimento al [Runtime Spark di Azure sinapsi](./apache-spark-version-support.md) per identificare la versione conda usata nello stesso runtime.
   
2. Per creare un canale personalizzato, installare conda-Build.
```
conda install conda-build
```
3. Organizzare tutti i pacchetti in per la piattaforma che si vuole gestire. In questo esempio verrà installato Anaconda Archive nel computer locale.

```
sudo wget https://repo.continuum.io/archive/Anaconda3-4.4.0-Linux-x86_64.sh 
sudo chmod +x Anaconda3-4.4.0-Linux-x86_64.sh  
sudo bash Anaconda3-4.4.0-Linux-x86_64.sh -b -p /usr/lib/anaconda3 
export PATH="/usr/lib/anaconda3/bin:$PATH" 
sudo chmod 777 -R /usr/lib/anaconda3a.  
```
## <a name="mount-the-storage-account-onto-your-machine"></a>Montare l'account di archiviazione nel computer
A questo punto, l'account Azure Data Lake Storage Gen2 verrà installato nel computer locale. Questo processo può essere eseguito anche con un account WASB. Tuttavia, si passerà a un esempio per l'account ADLSg2 
 
Per ulteriori informazioni su come montare l'account di archiviazione nel computer locale, è possibile visitare [Questa pagina](https://github.com/Azure/azure-storage-fuse#blobfuse ). 

1. È possibile installare blobfuse dal repository software Linux per i prodotti Microsoft.

```
wget https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb 
sudo dpkg -i packages-microsoft-prod.deb 
sudo apt-get update 
sudo apt-get install blobfuse fuse 
export AZURE_STORAGE_ACCOUNT=<<myaccountname>
export AZURE_STORAGE_ACCESS_KEY=<<myaccountkey>>
export AZURE_STORAGE_BLOB_ENDPOINT=*.dfs.core.windows.net 
```

2. Creare il mountpoint ( ```mkdir /path/to/mount``` ) e montare un contenitore BLOB con blobfuse. In questo esempio viene usato il valore **PrivateChannel** per la variabile **contenitore** .
   
```
blobfuse /path/to/mount --container-name=mycontainer --tmp-path=/mnt/blobfusetmp --use-adls=true --log-level=LOG_DEBUG 
sudo mkdir -p /mnt/blobfusetmp
sudo chown <myuser> /mnt/blobfusetmp
```
## <a name="create-the-channel"></a>Creare il canale
Nel prossimo set di passaggi verrà creato un canale conda personalizzato. 

1. Nel computer locale creare una directory per organizzare tutti i pacchetti per il canale personalizzato.
   
```
mkdir /home/trusted-service-user/privatechannel 
cd ~/privatechannel/ 
mkdir channel1/linux64 
```

2. Organizzare tutti i ```tar.bz2``` pacchetti da https://repo.anaconda.com/pkgs/main/linux-64/ nella sottodirectory. Assicurarsi di includere anche tutti i pacchetti tar. bz2 dipendenti. 

```
cd channel1 
mkdir noarch 
echo '{}' > noarch/repodata.json 
bzip2 -k noarch/repodata.json 

// Create channel 

conda index channel1/noarch 
conda index channel1/linux-64 
conda index channel1 
```

Per ulteriori informazioni, è possibile [visitare anche il manuale dell'utente conda per la](https://docs.conda.io/projects/conda/latest/user-guide/tasks/create-custom-channels.html) creazione di canali personalizzati. 

## <a name="storage-account-permissions"></a>Autorizzazioni dell'account di archiviazione
A questo punto, sarà necessario convalidare le autorizzazioni per l'account di archiviazione. Per impostare queste autorizzazioni, passare al percorso in cui verrà creato il canale personalizzato. Quindi, creare un token di firma di accesso condiviso per ```privatechannel``` che disponga di autorizzazioni di lettura, elenco ed esecuzione. 

Il nome del canale sarà ora l'URL di firma di accesso condiviso BLOB generato da questo processo.  

## <a name="create-a-sample-conda-environment-configuration-file"></a>Creare un file di configurazione dell'ambiente conda di esempio
Verificare infine il processo di installazione creando un file conda di esempio ```environment.yml``` . Se si dispone di un'area di lavoro abilitata per DEP, è necessario specificare il ``nodefaults`` canale nel file dell'ambiente.

Di seguito è riportato un esempio di file di configurazione conda:
```
name: sample 
channels: 
  - https://<<storage account name>>.blob.core.windows.net/privatechannel/channel?<<SAS Token>
  - nodefaults 
dependencies: 
  - openssl 
  - ncurses 
```
Dopo aver creato il file conda di esempio, è possibile creare un ambiente conda virtuale. 

```
conda env create –file sample.yml  
source activate env 
conda list 
```
Ora che è stato verificato il canale personalizzato, è possibile usare il processo di [gestione del pool Python](./apache-spark-manage-python-packages.md) per aggiornare le librerie nel pool di Apache Spark.

## <a name="next-steps"></a>Passaggi successivi
- Visualizzare le librerie predefinite: [supporto della versione Apache Spark](apache-spark-version-support.md)
- Gestire pacchetti Python: [Gestione pacchetti Python](./apache-spark-manage-python-packages.md)

