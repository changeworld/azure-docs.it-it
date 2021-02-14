---
title: Guida alla migrazione per i dispositivi fisici Azure Stack Edge Pro da FPGA a GPU
description: Questa guida contiene le istruzioni per eseguire la migrazione di carichi di lavoro da un dispositivo FPGA Pro Azure Stack Edge a un dispositivo GPU Pro Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: alkohli
ms.openlocfilehash: ce063e27620c3b0ccec9336e7a45ebb2ee1ad10d
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/10/2021
ms.locfileid: "100101717"
---
# <a name="migrate-workloads-from-an-azure-stack-edge-pro-fpga-to-an-azure-stack-edge-pro-gpu"></a>Eseguire la migrazione di carichi di lavoro da un FPGA Azure Stack Edge Pro a una GPU Pro Azure Stack Edge

Questo articolo descrive come eseguire la migrazione di carichi di lavoro e dati da un dispositivo FPGA Pro Azure Stack Edge a un dispositivo GPU Pro Azure Stack Edge. La procedura di migrazione prevede una panoramica della migrazione, incluso un confronto tra i due dispositivi, le considerazioni sulla migrazione, i passaggi dettagliati e la verifica seguita dalla pulizia.

<!--Azure Stack Edge Pro FPGA devices will reach end-of-life in February 2024. If you are considering new deployments, we recommend that you explore Azure Stack Edge Pro GPU devices for your workloads.-->

## <a name="about-migration"></a>Informazioni sulla migrazione

La migrazione è il processo di spostamento dei carichi di lavoro e dei dati delle applicazioni da una posizione di archiviazione a un'altra. Questa operazione comporta l'esecuzione di una copia esatta dei dati correnti di un'organizzazione da un dispositivo di archiviazione a un altro, preferibilmente senza compromettere o disabilitare le applicazioni attive, quindi reindirizzare tutte le attività di I/O (input/output) al nuovo dispositivo. 

Questa guida alla migrazione fornisce una procedura dettagliata dei passaggi necessari per eseguire la migrazione dei dati da un dispositivo FPGA Pro Azure Stack Edge a un dispositivo GPU Pro Azure Stack Edge. Questo documento è destinato ai professionisti IT e ai knowledge worker responsabili del funzionamento, della distribuzione e della gestione di dispositivi Azure Stack Edge nel Data Center. 

In questo articolo, il dispositivo FPGA Pro Azure Stack Edge viene definito dispositivo di *origine* e il dispositivo GPU Pro Azure stack Edge è il dispositivo di *destinazione* . 

## <a name="comparison-summary"></a>Riepilogo del confronto

Questa sezione fornisce un riepilogo comparativa delle funzionalità tra la GPU Pro Azure Stack Edge Pro e i dispositivi FPGA Pro Azure Stack Edge. L'hardware sia nel dispositivo di origine che in quello di destinazione è sostanzialmente identico e differisce solo per quanto riguarda la scheda di accelerazione hardware e la capacità di archiviazione. 

|    Funzionalità  | Azure Stack GPU Pro Edge (dispositivo di destinazione)  | Azure Stack Edge Pro FPGA (dispositivo di origine)|
|----------------|-----------------------|------------------------|
| Hardware       | Accelerazione hardware: 1 o 2 GPU NVIDIA T4 <br> Calcolo, memoria, interfaccia di rete, unità di alimentazione, specifiche del cavo di alimentazione sono identiche al dispositivo con FPGA.  | Accelerazione hardware: Intel Arria 10 FPGA <br> Calcolo, memoria, interfaccia di rete, unità di alimentazione, specifiche del cavo di alimentazione sono identiche al dispositivo con GPU.          |
| Archiviazione utilizzabile | 4,19 TB <br> Dopo aver riservato spazio per la resilienza di parità e l'uso interno | 12,5 TB <br> Dopo la riserva dello spazio per l'uso interno |
| Sicurezza       | Certificati |                                                     |
| Carichi di lavoro      | Carichi di lavoro IoT Edge <br> Carichi di lavoro della VM <br> Carichi di lavoro Kubernetes| Carichi di lavoro IoT Edge |
| Prezzi        | [Prezzi](https://azure.microsoft.com/pricing/details/azure-stack/edge/) | [Prezzi](https://azure.microsoft.com/pricing/details/azure-stack/edge/)|

## <a name="migration-plan"></a>Piano di migrazione

Per creare il piano di migrazione, tenere presenti le seguenti informazioni:

- Sviluppare una pianificazione per la migrazione. 
- Quando si esegue la migrazione dei dati, potrebbe verificarsi un tempo di inattività. È consigliabile pianificare la migrazione durante una finestra di manutenzione del tempo di inattività perché il processo comporta interruzioni. Le configurazioni vengono impostate e ripristinate in questo periodo di inattività, come descritto più avanti in questo documento.
- Comprendere la durata totale del tempo di inattività e comunicarla a tutte le parti interessate.
- Identificare i dati locali di cui è necessario eseguire la migrazione dal dispositivo di origine. Per precauzione, assicurarsi che tutti i dati presenti nell'archiviazione esistente dispongano di un backup recente. 


## <a name="migration-considerations"></a>Considerazioni sulla migrazione 

Prima di procedere con la migrazione, tenere presenti le seguenti informazioni: 

- Non è possibile attivare un dispositivo GPU Pro Azure Stack Edge con una risorsa FPGA Pro Azure Stack Edge. Per il dispositivo GPU Pro Azure Stack Edge è necessario creare una nuova risorsa, come descritto nell' [ordine di creazione di una GPU pro Azure stack Edge](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource).
- È necessario modificare i modelli di Machine Learning distribuiti nel dispositivo di origine che hanno usato l'FPGA per il dispositivo di destinazione con GPU. Per informazioni sui modelli, è possibile contattare supporto tecnico Microsoft. I modelli personalizzati distribuiti nel dispositivo di origine che non usa l'FPGA (solo CPU utilizzata) dovrebbero funzionare così come sono nel dispositivo di destinazione (usando CPU).
- I moduli IoT Edge distribuiti nel dispositivo di origine possono richiedere modifiche prima di poter essere distribuiti correttamente nel dispositivo di destinazione. 
- Il dispositivo di origine supporta i protocolli NFS 3,0 e 4,1. Il dispositivo di destinazione supporta solo il protocollo NFS 3,0.
- Il dispositivo di origine supporta i protocolli SMB e NFS. Il dispositivo di destinazione supporta l'archiviazione tramite il protocollo REST usando gli account di archiviazione, oltre ai protocolli SMB e NFS per le condivisioni.
- L'accesso alla condivisione sul dispositivo di origine avviene tramite l'indirizzo IP, mentre l'accesso alla condivisione sul dispositivo di destinazione viene tramite il nome del dispositivo.

## <a name="migration-steps-at-a-glance"></a>Procedura rapida per la migrazione

Questa tabella riepiloga il flusso generale per la migrazione, descrivendo i passaggi necessari per la migrazione e il percorso in cui eseguire questi passaggi.

| In questa fase | Eseguire questo passaggio| Sul dispositivo |
|---------------|-------------|----------------|
| Preparare il dispositivo di origine *       | 1. registrare i dati di configurazione <br>2. backup dei dati di condivisione <br>3. preparare i carichi di lavoro IoT Edge| Dispositivo di origine  |
| Preparare il dispositivo di destinazione *       |1. creare un nuovo ordine <br>2. configurare e attivare| Dispositivo di destinazione  |
| Eseguire la migrazione dei dati       | 1. migrare i dati dalle condivisioni <br>2. ridistribuire i carichi di lavoro IoT Edge| Dispositivo di destinazione  |
| Verificare i dati            |Verificare i dati migrati |Dispositivo di destinazione  |
| Pulisci, restituisci              |Cancella dati e restituisci| Dispositivo di origine  |

**I dispositivi di origine e di destinazione possono essere preparati in parallelo.*

## <a name="prepare-source-device"></a>Preparare il dispositivo di origine

La preparazione include l'identificazione delle condivisioni cloud perimetrali, le condivisioni locali perimetrali e i moduli IoT Edge distribuiti nel dispositivo. 

### <a name="1-record-configuration-data"></a>1. registrare i dati di configurazione

Eseguire questi passaggi sul dispositivo di origine tramite l'interfaccia utente locale.

Registrare i dati di configurazione nel dispositivo di *origine* . Usare l' [elenco di controllo della distribuzione](azure-stack-edge-gpu-deploy-checklist.md) per registrare la configurazione del dispositivo. Durante la migrazione, queste informazioni di configurazione verranno usate per configurare il nuovo dispositivo di destinazione. 

### <a name="2-back-up-share-data"></a>2. backup dei dati di condivisione

I dati del dispositivo possono essere di uno dei tipi seguenti:

- Dati in condivisioni cloud perimetrali
- Dati nelle condivisioni locali

#### <a name="data-in-edge-cloud-shares"></a>Dati in condivisioni cloud perimetrali

Il cloud perimetrale condivide i dati di livello dal dispositivo ad Azure. Eseguire questi passaggi sul dispositivo di *origine* tramite il portale di Azure. 

- Creare un elenco di tutti gli utenti e le condivisioni cloud perimetrali presenti nel dispositivo di origine.
- Creare un elenco di tutte le pianificazioni della larghezza di banda disponibili. Le pianificazioni della larghezza di banda vengono ricreate sul dispositivo di destinazione.
- A seconda della larghezza di banda di rete disponibile, configurare le pianificazioni della larghezza di banda nel dispositivo in modo da ottimizzare i dati a livelli nel cloud. Questo consente di ridurre al minimo i dati locali nel dispositivo.
- Verificare che le condivisioni siano completamente a livelli nel cloud. Questo può essere confermato controllando lo stato della condivisione nella portale di Azure.  

#### <a name="data-in-edge-local-shares"></a>Dati nelle condivisioni locali perimetrali

I dati nelle condivisioni locali perimetrali rimangono nel dispositivo. Eseguire questi passaggi sul dispositivo di *origine* tramite il portale di Azure. 

- Creare un elenco delle condivisioni locali perimetrali presenti nel dispositivo.
- Dato che si tratta di una migrazione unica dei dati, creare una copia dei dati della condivisione locale perimetrale in un altro server locale. Per copiare i dati, è possibile usare strumenti di copia, ad esempio `robocopy` (SMB) o `rsync` (NFS). Facoltativamente, è possibile che sia già stata distribuita una soluzione di protezione dei dati di terze parti per eseguire il backup dei dati nelle condivisioni locali. Le soluzioni di terze parti seguenti sono supportate per l'uso con i dispositivi FPGA Azure Stack Edge Pro:

    | Software di terze parti           | Riferimento alla soluzione                               |
    |--------------------------------|---------------------------------------------------------|
    | Cohesity                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> Per informazioni dettagliate, contattare Cohesity.          |
    | CommVault                      | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> Per informazioni dettagliate, contattare CommVault.          |
    | Veritas                        | [http://veritas.com/azure](http://veritas.com/azure) <br> Per informazioni dettagliate, contattare Veritas.   |
    | Veeam                          | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> Per informazioni dettagliate, contattare Veeam. |


### <a name="3-prepare-iot-edge-workloads"></a>3. preparare i carichi di lavoro IoT Edge

- Se sono stati distribuiti moduli IoT Edge e si usa l'accelerazione FPGA, potrebbe essere necessario modificare i moduli prima che questi vengano eseguiti sul dispositivo GPU. Seguire le istruzioni riportate in [modificare IOT Edge moduli](azure-stack-edge-gpu-modify-fpga-modules-gpu.md). 

<!--- If you have deployed IoT Edge workloads, the configuration data is shared on a share on the device. Back up the data in these shares.-->


## <a name="prepare-target-device"></a>Preparare il dispositivo di destinazione

### <a name="1-create-new-order"></a>1. creare un nuovo ordine

È necessario creare un nuovo ordine (e una nuova risorsa) per il dispositivo di *destinazione* . Il dispositivo di destinazione deve essere attivato sulla risorsa GPU e non sulla risorsa FPGA.

Per inserire un ordine, [creare una nuova risorsa Azure stack Edge](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource) nel portale di Azure.


### <a name="2-set-up-activate"></a>2. configurazione, attivazione

È necessario configurare e attivare il dispositivo di *destinazione* con la nuova risorsa creata in precedenza. 

Seguire questa procedura per configurare il dispositivo di *destinazione* tramite la portale di Azure:

1. Raccogliere le informazioni necessarie nell' [elenco di controllo della distribuzione](azure-stack-edge-gpu-deploy-checklist.md). È possibile usare le informazioni salvate dalla configurazione del dispositivo di origine. 
1. [Decomprimere](azure-stack-edge-gpu-deploy-install.md#unpack-the-device), [montare su rack](azure-stack-edge-gpu-deploy-install.md#rack-the-device) e [cablare il dispositivo](azure-stack-edge-gpu-deploy-install.md#cable-the-device). 
1. [Connettersi all'interfaccia utente locale del dispositivo](azure-stack-edge-gpu-deploy-connect.md).
1. Configurare la rete usando un diverso set di indirizzi IP (se si usano IP statici) rispetto a quelli usati per il dispositivo precedente. Vedere come [configurare le impostazioni di rete](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).
1. Assegnare lo stesso nome di dispositivo del dispositivo precedente e fornire un dominio DNS. Vedere come [configurare l'impostazione del dispositivo](azure-stack-edge-gpu-deploy-set-up-device-update-time.md).
1. Configurare i certificati nel nuovo dispositivo. Vedere come [configurare i certificati](azure-stack-edge-gpu-deploy-configure-certificates.md).
1. Ottenere la chiave di attivazione dalla portale di Azure e attivare il nuovo dispositivo. Vedere come [attivare il dispositivo](azure-stack-edge-gpu-deploy-activate.md).

A questo punto è possibile ripristinare i dati di condivisione e distribuire i carichi di lavoro in esecuzione nel dispositivo precedente.

## <a name="migrate-data"></a>Eseguire la migrazione dei dati

Verranno ora copiati i dati dal dispositivo di origine alle condivisioni cloud perimetrali e le condivisioni locali perimetrali sul dispositivo di *destinazione* .

### <a name="1-from-edge-cloud-shares"></a>1. da condivisioni cloud perimetrali

Seguire questa procedura per sincronizzare i dati nelle condivisioni cloud perimetrali nel dispositivo di destinazione:

1. [Aggiungere condivisioni](azure-stack-edge-j-series-manage-shares.md#add-a-share) corrispondenti ai nomi di condivisione creati nel dispositivo di origine. Assicurarsi che durante la creazione delle condivisioni, **selezionare il contenitore BLOB** sia impostato su **Usa opzione esistente** e quindi selezionare il contenitore usato con il dispositivo precedente.
1. [Aggiungere gli utenti](azure-stack-edge-j-series-manage-users.md#add-a-user) che hanno avuto accesso al dispositivo precedente.
1. [Aggiornare i](azure-stack-edge-j-series-manage-shares.md#refresh-shares) dati di condivisione da Azure. Questa operazione estrae tutti i dati cloud dal contenitore esistente alle condivisioni.
1. Ricreare le pianificazioni della larghezza di banda da associare alle condivisioni. Vedere [aggiungere una pianificazione della larghezza di banda](azure-stack-edge-j-series-manage-bandwidth-schedules.md#add-a-schedule) per i passaggi dettagliati.


### <a name="2-from-edge-local-shares"></a>2. dalle condivisioni locali perimetrali

È possibile che sia stata distribuita una soluzione di backup di terze parti per proteggere i dati delle condivisioni locali per i carichi di lavoro Internet. A questo punto sarà necessario ripristinare i dati.

Dopo che il dispositivo sostitutivo è stato completamente configurato, abilitare il dispositivo per l'archiviazione locale. 

Per ripristinare i dati dalle condivisioni locali, attenersi alla procedura seguente:

1. [Configurare il calcolo nel dispositivo](azure-stack-edge-gpu-deploy-configure-compute.md).
1. Aggiungere tutte le condivisioni locali nel dispositivo di destinazione. Vedere i passaggi dettagliati in [aggiungere una condivisione locale](azure-stack-edge-j-series-manage-shares.md#add-a-local-share).
1. L'accesso alle condivisioni SMB nel dispositivo di origine utilizzerà gli indirizzi IP, mentre nel dispositivo di destinazione verrà usato il nome del dispositivo. Vedere [connettersi a una condivisione SMB su una GPU Pro Azure stack Edge](azure-stack-edge-j-series-deploy-add-shares.md#connect-to-an-smb-share). Per connettersi alle condivisioni NFS sul dispositivo di destinazione, è necessario usare i nuovi indirizzi IP associati al dispositivo. Vedere [connettersi a una condivisione NFS sulla GPU di Azure stack Edge Pro](azure-stack-edge-j-series-deploy-add-shares.md#connect-to-an-nfs-share). 

    Se è stata eseguita la copia sui dati della condivisione in un server intermedio tramite SMB/NFS, è possibile copiare i dati in condivisioni sul dispositivo di destinazione. È anche possibile copiare i dati direttamente dal dispositivo di origine, se il dispositivo di origine e quello di destinazione sono *online*.

    Se è stato usato un software di terze parti per eseguire il backup dei dati nelle condivisioni locali, sarà necessario eseguire la procedura di ripristino fornita dalla soluzione di protezione dei dati preferita. Vedere i riferimenti nella tabella seguente.

    | Software di terze parti           | Riferimento alla soluzione                               |
    |--------------------------------|---------------------------------------------------------|
    | Cohesity                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> Per informazioni dettagliate, contattare Cohesity.          |
    | CommVault                      | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> Per informazioni dettagliate, contattare CommVault. |
    | Veritas                        | [http://veritas.com/azure](http://veritas.com/azure) <br> Per informazioni dettagliate, contattare Veritas.   |
    | Veeam                          | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> Per informazioni dettagliate, contattare Veeam. |

### <a name="3-redeploy-iot-edge-workloads"></a>3. ridistribuire i carichi di lavoro IoT Edge

Una volta preparati i moduli IoT Edge, sarà necessario distribuire i carichi di lavoro IoT Edge sul dispositivo di destinazione. Se si verificano errori durante la distribuzione di IoT Edge moduli, vedere:

- [Problemi comuni e soluzioni per Azure IOT Edge](../iot-edge/troubleshoot-common-errors.md)e 
- [Errori di runtime IoT Edge] [Gestire un dispositivo GPU Pro Azure stack Edge tramite Windows PowerShell](azure-stack-edge-gpu-troubleshoot.md#troubleshoot-iot-edge-errors).

## <a name="verify-data"></a>Verificare i dati

Dopo la migrazione, verificare che tutti i dati siano stati migrati e che i carichi di lavoro siano stati distribuiti nel dispositivo di destinazione.

## <a name="erase-data-return"></a>Cancella dati, restituisci

Al termine della migrazione dei dati, cancellare i dati locali e restituire il dispositivo di origine. Seguire i passaggi in [restituire il dispositivo Azure stack Edge Pro](azure-stack-edge-return-device.md).


## <a name="next-steps"></a>Passaggi successivi

[Informazioni su come distribuire carichi di lavoro IoT Edge nel dispositivo GPU Pro Azure Stack Edge](azure-stack-edge-gpu-deploy-compute-module-simple.md)
