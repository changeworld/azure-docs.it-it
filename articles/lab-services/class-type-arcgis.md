---
title: Configurare un Lab per ArcMap\ArcGIS desktop con Azure Lab Services | Microsoft Docs
description: Informazioni su come configurare un Lab per le classi usando ArcGIS.
author: nicolela
ms.topic: article
ms.date: 02/04/2021
ms.author: nicolela
ms.openlocfilehash: dbe4191b64773b71bc1ae04842d824fbfead8e55
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/06/2021
ms.locfileid: "99628153"
---
# <a name="set-up-a-lab-for-arcmaparcgis-desktop"></a>Configurare un Lab per ArcMap\ArcGIS desktop

[ArcGIS](https://www.esri.com/en-us/arcgis/products/arcgis-solutions/overview) è un tipo di sistema di informazione geografica (GIS).  ArcGIS viene usato per make\analyze mappe e per lavorare con i dati geografici forniti da [Environmental Systems Research Institute](https://www.esri.com/en-us/home) (ESRI).  Sebbene ArcGIS Desktop includa diverse applicazioni, in questo articolo viene illustrato come configurare i Lab per l'uso di ArcMap.  [ArcMap](https://desktop.arcgis.com/en/arcmap/latest/map/main/what-is-arcmap-.htm) viene usato per creare, modificare e analizzare le mappe 2D.

## <a name="lab-configuration"></a>Configurazione del lab

Per iniziare a configurare un Lab per l'uso di ArcMap, è necessario disporre di una sottoscrizione di Azure e di un account Lab.  Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

Dopo aver ottenuto una sottoscrizione di Azure, è possibile creare un nuovo account Lab in Azure Lab Services.  Per altre informazioni sulla creazione di un nuovo account Lab, vedere [configurare un account Lab](tutorial-setup-lab-account.md).  È anche possibile usare un account lab esistente.

### <a name="lab-account-settings"></a>Impostazioni dell'account Lab

Abilitare le impostazioni dell'account Lab come descritto nella tabella seguente.  Per altre informazioni su come abilitare le immagini di Azure Marketplace, vedere [specificare le immagini di Azure Marketplace disponibili per gli autori di Lab](https://docs.microsoft.com/azure/lab-services/specify-marketplace-images).

| Impostazione dell'account lab | Istruzioni |
| ------------------- | ------------ |
|Immagine del Marketplace| Abilitare l'immagine Windows 10 Pro o Windows 10 Pro N da usare nell'account Lab.|

### <a name="licensing-server"></a>Server licenze

Un tipo di licenza offerto da ArcGIS Desktop è costituito da [licenze d'uso simultanee](https://desktop.arcgis.com/en/license-manager/latest/license-manager-basics.htm).  A tale scopo, è necessario installare ArcGIS License Manager nel server licenze.  Il gestore delle licenze tiene traccia del numero di copie del software che possono essere eseguite contemporaneamente.  Per ulteriori informazioni su come configurare Gestione licenze nel server, vedere la [Guida di License Manager](https://desktop.arcgis.com/en/license-manager/latest/welcome.htm).

Il server licenze si trova in genere nella rete locale o ospitata in una macchina virtuale di Azure all'interno di una rete virtuale di Azure.  Dopo aver configurato il server licenze, sarà necessario eseguire [il peering della rete virtuale](https://docs.microsoft.com/azure/lab-services/how-to-connect-peer-virtual-network) con l' [account Lab](https://docs.microsoft.com/azure/lab-services/tutorial-setup-lab-account).  È necessario eseguire il peering di rete prima di creare il Lab in modo che le macchine virtuali del Lab possano accedere al server licenze e viceversa.

Per altre informazioni, vedere [configurare un server licenze come risorsa condivisa](how-to-create-a-lab-with-shared-resource.md).

### <a name="lab-settings"></a>Impostazioni del lab

La dimensione della macchina virtuale (VM) consigliata per ArcGIS Desktop dipende dalle applicazioni, dalle estensioni e dalle versioni specifiche che gli studenti utilizzeranno.  Anche le dimensioni della macchina virtuale dipendono dai carichi di lavoro che gli studenti dovranno eseguire.  Per informazioni sull'identificazione delle dimensioni della macchina virtuale, vedere [requisiti di sistema per desktop ArcGIS](https://desktop.arcgis.com/en/system-requirements/latest/arcgis-desktop-system-requirements.htm) .  Una volta identificate le dimensioni potenziali della macchina virtuale, è consigliabile testare i carichi di lavoro degli studenti per garantire prestazioni adeguate.

In questo articolo si consiglia di usare le [dimensioni **medie** della macchina virtuale](administrator-guide.md#vm-sizing) per la versione [10.7.1 di ArcMap](https://desktop.arcgis.com/en/system-requirements/10.7/arcgis-desktop-system-requirements.htm), presupponendo che non vengano usate altre estensioni per desktop ArcGIS.  Tuttavia, a seconda delle esigenze della classe, potrebbe essere necessaria una macchina virtuale di **grandi** dimensioni o anche una **Small\Medium GPU (visualizzazione)** .  Ad esempio, l' [estensione dell'analista spaziale](https://desktop.arcgis.com/en/arcmap/latest/tools/spatial-analyst-toolbox/gpu-processing-with-spatial-analyst.htm) inclusa in ArcGIS Desktop supporta una GPU per migliorare le prestazioni, ma non richiede l'uso di una GPU.

| Impostazione Lab | Valore e descrizione |
| ------------ | ------------------ |
|Dimensioni della macchina virtuale| **Media**.  Ideale per database relazionali, Caching in memoria e analisi.|  

### <a name="template-machine"></a>Computer modello

I passaggi descritti in questa sezione illustrano come configurare la macchina virtuale modello:

1.  Avviare la VM del modello e connettersi al computer tramite RDP.

2.  Scaricare e installare i componenti di ArcGIS Desktop usando le istruzioni da ESRI.  Questi passaggi includono l'assegnazione del gestore licenze per le licenze d'uso simultanee: 
    - [Introduzione all'installazione e alla configurazione di ArcGIS Desktop](https://desktop.arcgis.com/arcmap/latest/get-started/installation-guide/introduction.htm)

3.  Configurare l'archiviazione di backup esterno per gli studenti.  Gli studenti possono salvare i file direttamente nella macchina virtuale assegnata, perché tutte le modifiche apportate vengono salvate tra le sessioni.  Tuttavia, è consigliabile che gli studenti riportino il proprio lavoro in un archivio esterno rispetto alla macchina virtuale per alcuni motivi:
    - Per consentire agli studenti di accedere al proprio lavoro al termine della classe e del Lab.  
    - Nel caso in cui lo studente ottenga la macchina virtuale in uno stato non valido e la relativa immagine debba essere [reimpostata](how-to-set-virtual-machine-passwords.md#reset-vms).

    Con ArcGIS, ogni studente deve eseguire il backup dei file seguenti alla fine di ogni sessione di lavoro:

    - file MXD, in cui sono archiviate le informazioni sul layout per un progetto.
    - File geodatabase, che archiviano tutti i dati prodotti da ArcGIS.
    - Tutti gli altri dati che lo studente può usare, ad esempio file raster, forma, GeoTIFF e così via.

    Si consiglia di usare OneDrive per l'archiviazione di backup.  Per configurare OneDrive nella macchina virtuale modello, seguire i passaggi descritti nell'articolo [installare e configurare OneDrive](how-to-prepare-windows-template.md#install-and-configure-onedrive). 

4.  Infine, [pubblicare](how-to-create-manage-template.md#publish-the-template-vm) la macchina virtuale modello per creare la macchina virtuale students.

### <a name="auto-shutdown-and-disconnect-settings"></a>Impostazioni di arresto automatico e disconnessione

[Le impostazioni di arresto automatico e disconnessione](cost-management-guide.md#automatic-shutdown-settings-for-cost-control) di un Lab consentono di assicurarsi che la macchina virtuale di uno studente venga arrestata quando non è in uso.  Queste impostazioni devono essere impostate in base ai tipi di carichi di lavoro che gli studenti eseguiranno per evitare che la macchina virtuale venga arrestata durante il lavoro.  Ad esempio, l'impostazione **Disconnetti utenti quando macchine virtuali è inattiva** disconnette lo studente dalla sessione RDP dopo che non sono stati rilevati input di mouse o tastiera per un periodo di tempo specificato.  Questa impostazione deve consentire un tempo sufficiente per i carichi di lavoro in cui lo studente non usa attivamente il mouse o la tastiera, ad esempio per eseguire query lunghe o attendere il rendering.

Per ArcGIS è consigliabile usare i valori seguenti per queste impostazioni:
- Disconnettere gli utenti quando le macchine virtuali sono inattive
    - 30 minuti dopo il rilevamento dello stato di inattività
- Arrestare le macchine virtuali quando gli utenti si disconnettono
    - 15 minuti dopo la disconnessione dell'utente

## <a name="cost"></a>Costo

Verrà ora trattata una possibile stima dei costi per questa classe. Questa stima non include il costo di esecuzione del server licenze. Verrà usata una classe di 25 studenti. Sono previste 20 ore di tempo della classe pianificata. Ogni studente riceve inoltre una quota di 10 ore per il lavoro o assegnazioni al di fuori dell'orario di classe pianificato. Le dimensioni della macchina virtuale scelte sono **medie**, ovvero 42 unità Lab.

25 studenti \* (20 ore pianificate + 10 ore di quota) \* 42 unità Lab * 0,01 USD all'ora = 315,00 USD

>[!IMPORTANT]
> La stima dei costi è solo a scopo esemplificativo.  Per informazioni aggiornate sui prezzi, vedere [Azure Lab Services prezzi](https://azure.microsoft.com/pricing/details/lab-services/).  

## <a name="next-steps"></a>Passaggi successivi

I passaggi successivi sono comuni per la configurazione di qualsiasi Lab.

- [Creare e gestire un modello](how-to-create-manage-template.md)
- [Aggiungere utenti](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Impostare la quota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Impostare una pianificazione](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Collegamenti per la registrazione tramite posta elettronica agli studenti](how-to-configure-student-usage.md#send-invitations-to-users)