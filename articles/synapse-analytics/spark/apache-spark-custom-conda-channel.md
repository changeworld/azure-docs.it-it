---
title: Creare un canale Conda personalizzato per la gestione dei pacchetti
description: Informazioni su come creare un canale Conda personalizzato per la gestione dei pacchetti
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 26b6adefd2d334c9fe570bfa7e63bb06b55b9d20
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588769"
---
# <a name="create-a-custom-conda-channel-for-package-management"></a>Creare un canale Conda personalizzato per la gestione dei pacchetti 
Quando si installano pacchetti Python, Gestione pacchetti Conda usa i canali per cercare i pacchetti. Potrebbe essere necessario creare un canale Conda personalizzato per vari motivi. Ad esempio, è possibile che:

- L'area di lavoro è protetta dall'esfiltrazione dei dati e le connessioni in uscita sono bloccate.  
- sono disponibili pacchetti che non si vuole caricare nei repository pubblici.
- si vuole configurare un repository alternativo per gli utenti all'interno dell'area di lavoro.

In questo articolo verrà illustrata una guida dettagliata che consente di creare il canale Conda personalizzato all'interno dell'account Azure Data Lake Storage personalizzato.

## <a name="set-up-your-local-machine"></a>Configurare il computer locale

1. Installare Conda nel computer locale. È possibile fare riferimento al [runtime Azure Synapse Spark](./apache-spark-version-support.md) per identificare la versione di Conda usata nello stesso runtime.
   
2. Per creare un canale personalizzato, installare conda-build.
```
conda install conda-build
```
3. Organizzare tutti i pacchetti in per la piattaforma che si vuole gestire. In questo esempio verrà installato l'archivio Anaconda nel computer locale.

```
sudo wget https://repo.continuum.io/archive/Anaconda3-4.4.0-Linux-x86_64.sh 
sudo chmod +x Anaconda3-4.4.0-Linux-x86_64.sh  
sudo bash Anaconda3-4.4.0-Linux-x86_64.sh -b -p /usr/lib/anaconda3 
export PATH="/usr/lib/anaconda3/bin:$PATH" 
sudo chmod 777 -R /usr/lib/anaconda3a.  
```
## <a name="mount-the-storage-account-onto-your-machine"></a>Montare l'account di archiviazione nel computer
Successivamente, l'account Azure Data Lake Storage Gen2 verrà montato nel computer locale. Questo processo può essere eseguito anche con un account WASB. Tuttavia, verrà illustrato un esempio per l'account ADLSg2 
 
Per altre informazioni su come montare l'account di archiviazione nel computer locale, visitare [questa pagina](https://github.com/Azure/azure-storage-fuse#blobfuse ). 

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

2. Creare il punto di montaggio ( ```mkdir /path/to/mount``` ) e montare un contenitore BLOB con blobfuse. In questo esempio viene utilizzato il valore **privatechannel** per la **variabile mycontainer.**
   
```
blobfuse /path/to/mount --container-name=mycontainer --tmp-path=/mnt/blobfusetmp --use-adls=true --log-level=LOG_DEBUG 
sudo mkdir -p /mnt/blobfusetmp
sudo chown <myuser> /mnt/blobfusetmp
```
## <a name="create-the-channel"></a>Creare il canale
Nel successivo set di passaggi verrà creato un canale Conda personalizzato. 

1. Nel computer locale creare una directory per organizzare tutti i pacchetti per il canale personalizzato.
   
```
mkdir /home/trusted-service-user/privatechannel 
cd ~/privatechannel/ 
mkdir channel1/linux64 
```

2. Organizzare tutti ```tar.bz2``` i pacchetti da nella https://repo.anaconda.com/pkgs/main/linux-64/ sottodirectory . Assicurarsi di includere anche tutti i pacchetti tar.bz2 dipendenti. 

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

Per altre informazioni, è anche possibile visitare il manuale dell'utente [di Conda](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/create-custom-channels.html) per la creazione di canali personalizzati. 

## <a name="storage-account-permissions"></a>Autorizzazioni dell'account di archiviazione
A questo punto, sarà necessario convalidare le autorizzazioni per l'account di archiviazione. Per impostare queste autorizzazioni, passare al percorso in cui verrà creato il canale personalizzato. Creare quindi un token di firma di accesso condiviso ```privatechannel``` per con autorizzazioni di lettura, elenco ed esecuzione. 

Il nome del canale sarà ora l'URL della firma di accesso condiviso BLOB generato da questo processo.  

## <a name="create-a-sample-conda-environment-configuration-file"></a>Creare un file di configurazione dell'ambiente Conda di esempio
Verificare infine il processo di installazione creando un file Conda di ```environment.yml``` esempio. Se si dispone di un'area di lavoro abilitata per DEP, è necessario specificare il ``nodefaults`` canale nel file di ambiente.

Di seguito è riportato un file di configurazione conda di esempio:
```
name: sample 
channels: 
  - https://<<storage account name>>.blob.core.windows.net/privatechannel/channel?<<SAS Token>
  - nodefaults 
dependencies: 
  - openssl 
  - ncurses 
```
Dopo aver creato il file Conda di esempio, è possibile creare un ambiente Conda virtuale. 

```
conda env create –file sample.yml  
source activate env 
conda list 
```
Dopo aver verificato il canale personalizzato, è possibile usare il processo di gestione del [pool Python](./apache-spark-manage-python-packages.md) per aggiornare le librerie nel pool Apache Spark dati.

## <a name="next-steps"></a>Passaggi successivi
- Visualizzare le librerie predefinite: supporto [Apache Spark versione](apache-spark-version-support.md)
- Gestire i pacchetti Python: [gestione dei pacchetti Python](./apache-spark-manage-python-packages.md)

