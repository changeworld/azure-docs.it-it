---
title: Note sulla versione di Azure Stack Edge Pro 2103
description: Descrive i problemi e le risoluzioni critici per il Azure Stack Edge Pro che esegue la versione 2103.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/23/2021
ms.author: alkohli
ms.openlocfilehash: 846d4a259f0fcd204bcad6c898efc999c3765fd3
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "104962684"
---
# <a name="azure-stack-edge-2103-release-notes"></a>Note sulla versione di Azure Stack Edge 2103

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Le note sulla versione seguenti identificano i problemi critici aperti e i problemi risolti per la versione 2103 per i dispositivi Azure Stack Edge. Queste note sulla versione sono valide per Azure Stack GPU Pro Edge, Azure Stack Edge Pro R e i dispositivi Mini R Azure Stack Edge. Le funzionalità e i problemi che corrispondono a un modello specifico vengono chiamati laddove applicabile.

Le note sulla versione vengono aggiornate continuamente e i problemi che richiedono una soluzione alternativa vengono aggiunti man mano che vengono individuati. Prima di distribuire il dispositivo, esaminare attentamente le informazioni contenute nelle note sulla versione.

Questo articolo si applica alla versione di **Azure stack Edge 2103** , che esegue il mapping al numero di versione del software **2.2.1540.2890**. Questo software può essere applicato al dispositivo se si esegue almeno Azure Stack software perimetrale 2010 (2.1.1377.2170).

## <a name="whats-new"></a>Novità

Nella versione di Azure Stack Edge 2103 sono disponibili le nuove funzionalità seguenti. 
 
- **Nuove funzionalità per le macchine virtuali** : a partire da questa versione, è possibile eseguire le operazioni di gestione seguenti nelle macchine virtuali tramite la portale di Azure:
    - Aggiungere o rimuovere più interfacce di rete a una macchina virtuale esistente.
    - Aggiungere o rimuovere più dischi in una macchina virtuale esistente.
    - Ridimensionare la macchina virtuale.
    - Aggiungere dati personalizzati durante la distribuzione di una macchina virtuale Windows o Linux.

  È anche possibile [connettersi alla console VM sul dispositivo](azure-stack-edge-gpu-connect-virtual-machine-console.md) e risolvere eventuali problemi della macchina virtuale.

- **Connettersi all'interfaccia di PowerShell tramite HTTPS** : a partire da questa versione, non sarà più possibile aprire una sessione remota di PowerShell in un dispositivo tramite *http*. Per impostazione predefinita, *https* verrà usato per tutte le sessioni. Per ulteriori informazioni, vedere la pagina relativa alla modalità di [connessione all'interfaccia di PowerShell](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) del dispositivo.

- Sono stati apportati **miglioramenti per** le funzionalità di calcolo: sono stati apportati miglioramenti e miglioramenti, tra cui:

    - **Qualità complessiva della piattaforma di calcolo**. Questa versione include correzioni di bug per migliorare la qualità complessiva della piattaforma di calcolo. Vedere i [problemi risolti nella versione 2103](#issues-fixed-in-2103-release). 
    - **Componenti della piattaforma di calcolo**. Gli aggiornamenti della sicurezza sono stati applicati all'immagine di macchina virtuale di calcolo. Sono state aggiornate anche IoT Edge e Azure Arc per le versioni Kubernetes.
    - **Diagnostica**. Viene rilasciata una nuova API per verificare le condizioni della rete e delle risorse. È possibile connettersi all'interfaccia di PowerShell del dispositivo e usare il `Test-HcsKubernetesStatus` comando per verificare la conformità della rete del dispositivo.
    - **Raccolta di log** che porterebbe a un debug migliorato. 
    - **Infrastruttura di avvisi** che consente di rilevare i conflitti di indirizzi IP per gli indirizzi IP di calcolo. 
    - **Combinare il carico di lavoro** di Kubernetes e Azure Resource Manager locali. 

- **Registrazione proattiva per impostazione predefinita** . a partire da questa versione, la raccolta dei log proattivi è abilitata per impostazione predefinita nel dispositivo. Questa funzionalità consente a Microsoft di raccogliere i log in modo proattivo in base agli indicatori di integrità del sistema per facilitare la risoluzione dei problemi relativi ai dispositivi. Per altre informazioni, vedere [raccolta di log proattiva nel dispositivo](azure-stack-edge-gpu-proactive-log-collection.md).

## <a name="issues-fixed-in-2103-release"></a>Problemi risolti nella versione 2103

Nella tabella seguente sono elencati i problemi rilasciati nelle versioni precedenti e corretti nella versione corrente.

| No. | Funzionalità | Problema | 
| --- | --- | --- |
|**1.**|Kubernetes |Il registro contenitori perimetrale non funziona quando il proxy Web è abilitato.|
|**2.**|Kubernetes |Il registro contenitori perimetrale non funziona con i moduli IoT Edge.| 

## <a name="known-issues-in-2103-release"></a>Problemi noti nella versione 2103

La tabella seguente fornisce un riepilogo dei problemi noti della versione 2103.

| No. | Funzionalità | Problema | Soluzione alternativa/commenti |
| --- | --- | --- | --- |
|**1.**|Funzionalità di anteprima |Per questa versione, le funzionalità seguenti: Azure Resource Manager locali, VM, gestione cloud di macchine virtuali, gestione cloud Kubernetes, Azure Arc abilitato Kubernetes, VPN per Azure Stack Edge Pro R e Azure Stack Edge Mini R, servizio multiprocesso (MP) per la GPU Pro Azure Stack Edge-sono tutti disponibili in anteprima.  |Queste funzionalità saranno disponibili a livello generale nelle versioni successive. |
|**2.**|VM GPU |Prima di questa versione, il ciclo di vita della VM GPU non è stato gestito nel flusso di aggiornamento. Pertanto, quando si esegue l'aggiornamento alla versione 2103, le macchine virtuali GPU non vengono interrotte automaticamente durante l'aggiornamento. Prima di aggiornare il dispositivo, sarà necessario arrestare manualmente le VM GPU usando un `stop-stayProvisioned` flag. Per altre informazioni, vedere [sospendere o arrestare la VM](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#suspend-or-shut-down-the-vm).<br> Tutte le VM GPU mantenute in esecuzione prima dell'aggiornamento vengono avviate dopo l'aggiornamento. In questi casi, i carichi di lavoro in esecuzione nelle macchine virtuali non vengono interrotti normalmente. È possibile che le macchine virtuali si trovino in uno stato indesiderato dopo l'aggiornamento. <br>Tutte le VM GPU interrotte tramite il `stop-stayProvisioned` prima dell'aggiornamento vengono avviate automaticamente dopo l'aggiornamento. <br>Se si arrestano le VM GPU tramite il portale di Azure, sarà necessario avviare manualmente la macchina virtuale dopo l'aggiornamento del dispositivo.| Se si eseguono macchine virtuali GPU con Kubernetes, arrestare le VM GPU immediatamente prima dell'aggiornamento. <br>Quando le VM GPU vengono arrestate, Kubernetes utilizzerà le GPU usate in origine dalle macchine virtuali. <br>Più a lungo le VM GPU si trovano nello stato interrotto, maggiori sono le probabilità che Kubernetes sostituirà le GPU. |
|**3.**|Estensione della macchina virtuale per script personalizzati |Si è verificato un problema noto nelle VM Windows create in una versione precedente e il dispositivo è stato aggiornato a 2103. <br> Se si aggiunge un'estensione di script personalizzata in queste VM, l'agente guest della macchina virtuale Windows (solo versione 2.7.41491.901) si blocca nell'aggiornamento causando il timeout della distribuzione dell'estensione. | Come soluzione alternativa a questo problema: <ul><li> Connettersi alla macchina virtuale Windows usando Remote Desktop Protocol (RDP). </li><li> Verificare che `waappagent.exe` sia in esecuzione nel computer: `Get-Process WaAppAgent` . </li><li> Se `waappagent.exe` non è in esecuzione, riavviare il `rdagent` servizio: `Get-Service RdAgent` \| `Restart-Service` . Attendere 5 minuti.</li><li> Mentre `waappagent.exe` è in esecuzione, terminare il `WindowsAzureGuest.exe` processo. </li><li>Dopo aver terminato il processo, il processo inizia a eseguire nuovamente con la versione più recente.</li><li>Verificare che la versione dell'agente guest della macchina virtuale Windows sia 2.7.41491.971 usando il comando seguente: `Get-Process WindowsAzureGuestAgent` \| `fl ProductVersion` .</li><li>[Configurare l'estensione dello script personalizzato nella macchina virtuale Windows](azure-stack-edge-gpu-deploy-virtual-machine-custom-script-extension.md). </li><ul> | 
|**4.**|Servizio a più processi (MP) |Quando il software del dispositivo e il cluster Kubernetes vengono aggiornati, l'impostazione MPS non viene mantenuta per i carichi di lavoro.   |[Riabilitare MP](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) e ridistribuire i carichi di lavoro che usavano MP. |


## <a name="known-issues-from-previous-releases"></a>Problemi noti delle versioni precedenti

Nella tabella seguente viene fornito un riepilogo dei problemi noti rilevati dalle versioni precedenti.

| No. | Funzionalità | Problema | Soluzione alternativa/commenti |
| --- | --- | --- | --- |
| **1.** |Azure Stack Edge Pro e Azure SQL | Per la creazione del database SQL è necessario l'accesso amministratore.   |Eseguire i passaggi seguenti anziché i passaggi 1-2 in [https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database](../iot-edge/tutorial-store-data-sql-server.md#create-the-sql-database) . <ul><li>Nell'interfaccia utente locale del dispositivo abilitare l'interfaccia di calcolo. Selezionare **calcolo > porta # > Abilita per calcolo > applica.**</li><li>Scarica `sqlcmd` dal computer client da https://docs.microsoft.com/sql/tools/sqlcmd-utility </li><li>Connettersi all'indirizzo IP dell'interfaccia di calcolo (la porta abilitata), aggiungendo ", 1401" alla fine dell'indirizzo.</li><li>Il comando finale sarà simile al seguente: sqlcmd-S {Interface IP}, 1401-U SA-P "Strong! Passw0rd ".</li>Al termine di questa operazione, i passaggi 3-4 dalla documentazione corrente devono essere identici. </li></ul> |
| **2.** |Aggiorna| Le modifiche incrementali apportate ai BLOB ripristinati tramite l' **aggiornamento** non sono supportate |Per gli endpoint BLOB, gli aggiornamenti parziali dei BLOB dopo un aggiornamento possono comportare il caricamento degli aggiornamenti nel cloud. Ad esempio, sequenza di azioni come:<ul><li>Creare un BLOB nel cloud. O eliminare un BLOB caricato in precedenza dal dispositivo.</li><li>Aggiornare il BLOB dal cloud al dispositivo usando la funzionalità di aggiornamento.</li><li>Aggiornare solo una parte del BLOB usando le API REST di Azure SDK.</li></ul>Queste azioni possono comportare l'aggiornamento delle sezioni aggiornate del BLOB nel cloud. <br>**Soluzione alternativa**: usare strumenti come Robocopy o una normale copia di file tramite Esplora risorse o la riga di comando per sostituire i BLOB interi.|
|**3.**|Limitazione|Durante la limitazione, se le nuove scritture nel dispositivo non sono consentite, le scritture dal client NFS hanno esito negativo con un errore di "autorizzazione negata".| L'errore verrà visualizzato come riportato di seguito:<br>`hcsuser@ubuntu-vm:~/nfstest$ mkdir test`<br>mkdir: non è possibile creare la directory ' test ': autorizzazione negata|
|**4.**|Inserimento di archiviazione BLOB|Quando si usa AzCopy versione 10 per l'inserimento dell'archiviazione BLOB, eseguire AzCopy con l'argomento seguente: `Azcopy <other arguments> --cap-mbps 2000`| Se non vengono forniti questi limiti per AzCopy, è possibile che venga inviato un numero elevato di richieste al dispositivo, causando problemi con il servizio.|
|**5.**|Account di archiviazione a livelli|Quando si usano gli account di archiviazione a livelli, si applica quanto segue:<ul><li> Sono supportati solo i BLOB in blocchi. I BLOB di pagine non sono supportati.</li><li>Non è disponibile alcun supporto per snapshot o copia API.</li><li> L'inserimento del carico di lavoro Hadoop tramite `distcp` non è supportato perché usa l'operazione di copia.</li></ul>||
|**6.**|Connessione NFS share|Se più processi eseguono la copia nella stessa condivisione e l' `nolock` attributo non viene utilizzato, è possibile che vengano visualizzati errori durante la copia.|L' `nolock` attributo deve essere passato al comando mount per copiare i file nella condivisione NFS. Ad esempio: `C:\Users\aseuser mount -o anon \\10.1.1.211\mnt\vms Z:`.|
|**7.**|Cluster Kubernetes|Quando si applica un aggiornamento sul dispositivo che esegue un cluster Kubernetes, le macchine virtuali Kubernetes vengono riavviate e riavviate. In questo caso, solo i pod distribuiti con le repliche specificate vengono ripristinati automaticamente dopo un aggiornamento.  |Se sono stati creati singoli pod all'esterno di un controller di replica senza specificare un set di repliche, questi Pod non verranno ripristinati automaticamente dopo l'aggiornamento del dispositivo. Sarà necessario ripristinare i pod.<br>Un set di repliche sostituisce i pod eliminati o interrotti per qualsiasi motivo, ad esempio un errore del nodo o un aggiornamento del nodo di interruzione. Per questo motivo, è consigliabile usare un set di repliche anche se l'applicazione richiede un singolo POD.|
|**8.**|Cluster Kubernetes|Kubernetes su Azure Stack Edge Pro è supportato solo con Helm V3 o versione successiva. Per altre informazioni, vedere [domande frequenti: rimozione di un timone](https://v3.helm.sh/docs/faq/).|
|**9.**|Kubernetes con abilitazione di Azure Arc |Per la versione GA, Azure Arc Enabled Kubernetes viene aggiornato dalla versione 0.1.18 a 0.2.9. Poiché l'aggiornamento di Kubernetes abilitato per Azure Arc non è supportato nel dispositivo Azure Stack Edge, è necessario ridistribuire Azure Arc Enabled Kubernetes.|A tale scopo, seguire questa procedura:<ol><li>[Applicare gli aggiornamenti software e Kubernetes del dispositivo](azure-stack-edge-gpu-install-update.md).</li><li>Connettersi all' [interfaccia di PowerShell del dispositivo](azure-stack-edge-gpu-connect-powershell-interface.md).</li><li>Rimuovere l'agente di Azure ARC esistente. Digitare: `Remove-HcsKubernetesAzureArcAgent`.</li><li>Distribuire [Azure Arc in una nuova risorsa](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md). Non usare una risorsa di Azure ARC esistente.</li></ol>|
|**10.**|Kubernetes con abilitazione di Azure Arc|Le distribuzioni di Azure Arc non sono supportate se il proxy Web è configurato sul dispositivo Azure Stack Edge Pro.||
|**11.**|Kubernetes |La porta 31000 è riservata per il dashboard di Kubernetes. La porta 31001 è riservata al registro contenitori perimetrale. Analogamente, nella configurazione predefinita, gli indirizzi IP 172.28.0.1 e 172.28.0.10 sono riservati rispettivamente per il servizio Kubernetes e per il servizio DNS di base.|Non usare IP riservati.|
|**12.**|Kubernetes |Kubernetes attualmente non consente i servizi LoadBalancer multiprotocollo. Ad esempio, un servizio DNS che avrebbe dovuto rimanere in attesa su TCP e UDP. |Per ovviare a questa limitazione di Kubernetes con MetalLB, è possibile creare due servizi (uno per TCP, uno per UDP) nello stesso selettore di Pod. Questi servizi usano la stessa chiave di condivisione e spec. loadBalancerIP per condividere lo stesso indirizzo IP. Gli IP possono anche essere condivisi se si dispone di più servizi rispetto agli indirizzi IP disponibili. <br> Per altre informazioni, vedere [condivisione degli indirizzi IP](https://metallb.universe.tf/usage/#ip-address-sharing).|
|**13.**|Cluster Kubernetes|I moduli esistenti di Azure IoT Edge Marketplace possono richiedere modifiche da eseguire su IoT Edge sul dispositivo Azure Stack Edge.|Per altre informazioni, vedere Modificare Azure IoT Edge moduli da Marketplace per l'esecuzione nel dispositivo Azure Stack Edge.<!-- insert link-->|
|**14.**|Kubernetes |I montaggi con binding basato su file non sono supportati con Azure IoT Edge in Kubernetes sul dispositivo Azure Stack Edge.|IoT Edge usa un livello di conversione per tradurre le `ContainerCreate` Opzioni nei costrutti Kubernetes. La creazione di `Binds` mappe per la `hostpath` Directory e quindi il montaggio di binding basato su file non possono essere associati a percorsi in IOT Edge contenitori. Se possibile, eseguire il mapping della directory padre.|
|**15.**|Kubernetes |Se si importano certificati personalizzati per IoT Edge e si aggiungono i certificati sul dispositivo Azure Stack Edge dopo che il calcolo è stato configurato nel dispositivo, i nuovi certificati non vengono prelevati.|Per risolvere questo problema, è necessario caricare i certificati prima di configurare il calcolo nel dispositivo. Se il calcolo è già configurato, [connettersi all'interfaccia di PowerShell del dispositivo ed eseguire IOT Edge comandi](azure-stack-edge-gpu-connect-powershell-interface.md#use-iotedge-commands). Riavviare `iotedged` e `edgehub` pod.|
|**16.**|Certificati |In alcuni casi, lo stato del certificato nell'interfaccia utente locale può richiedere alcuni secondi per l'aggiornamento. |Potrebbero essere interessati gli scenari seguenti nell'interfaccia utente locale.<ul><li>Colonna **stato** nella pagina **certificati** .</li><li>Riquadro **sicurezza** nella pagina attività **iniziali** .</li><li>Riquadro **configurazione** nella pagina **Panoramica** .</li></ul>  |
|**17.**|IoT Edge |I moduli distribuiti tramite IoT Edge non possono utilizzare la rete host. | |
|**18.**|Calcolo + Kubernetes |Compute/Kubernetes non supporta il proxy Web NTLM. ||
|**19.**|Kubernetes + aggiornamento |Le versioni precedenti del software, ad esempio le versioni 2008, presentano un problema race condition aggiornamento che causa l'esito negativo dell'aggiornamento con ClusterConnectionException. |L'uso delle compilazioni più recenti dovrebbe contribuire a evitare questo problema. Se si verifica questo problema, la soluzione alternativa consiste nel ritentare l'aggiornamento e dovrebbe funzionare.|
|**20**|Internet Explorer|Se sono abilitate le funzionalità di sicurezza avanzate, potrebbe non essere possibile accedere alle pagine dell'interfaccia utente Web locale. | Disabilitare la sicurezza avanzata e riavviare il browser.|
|**21.**|Dashboard di Kubernetes | L'endpoint *https* per il dashboard di Kubernetes con certificato SSL non è supportato. | |
|**22.**|Kubernetes |Kubernetes non supporta ":" nei nomi delle variabili di ambiente usati dalle applicazioni .NET. Questa operazione è necessaria anche per il funzionamento di griglia di eventi IoT Edge modulo sul dispositivo Azure Stack Edge e su altre applicazioni. Per ulteriori informazioni, vedere la [documentazione di ASP.NET Core](/aspnet/core/fundamentals/configuration/?tabs=basicconfiguration&view=aspnetcore-3.1&preserve-view=true#environment-variables).|Sostituire ":" con il doppio carattere di sottolineatura. Per ulteriori informazioni, vedere la pagina relativa al [problema Kubernetes](https://github.com/kubernetes/kubernetes/issues/53201)|
|**23.** |Azure Arc + cluster Kubernetes |Per impostazione predefinita, quando la risorsa `yamls` viene eliminata dal repository git, le risorse corrispondenti non vengono eliminate dal cluster Kubernetes.  |Per consentire l'eliminazione delle risorse quando vengono eliminate dal repository git, impostare `--sync-garbage-collection` in Arc OperatorParams. Per altre informazioni, vedere [eliminare una configurazione](../azure-arc/kubernetes/use-gitops-connected-cluster.md#additional-parameters). |
|**24.**|NFS |Le applicazioni che usano NFS Share Mounts sul dispositivo per scrivere i dati devono usare la scrittura esclusiva. Che garantisce che le Scritture vengano scritte nel disco.| |
|**25.**|Configurazione di calcolo |La configurazione di calcolo non riesce nelle configurazioni di rete in cui i gateway o i commutatori o i router rispondono alle richieste ARP (Address Resolution Protocol) per i sistemi che non esistono nella rete.| |
|**26.**|COMPUTE e Kubernetes |Se Kubernetes è impostato per primo sul dispositivo, attesta tutte le GPU disponibili. Di conseguenza, non è possibile creare macchine virtuali Azure Resource Manager usando GPU dopo la configurazione di Kubernetes. |Se il dispositivo ha 2 GPU, è possibile creare 1 VM che usa la GPU e quindi configurare Kubernetes. In questo caso, Kubernetes utilizzerà la GPU rimanente disponibile 1. |


<!--|**18.**|Azure Private Edge Zone (Preview) |There is a known issue with Virtual Network Function VM if the VM was created on Azure Stack Edge device running earlier preview builds such as 2006/2007b and then the device was updated to 2009 GA release. The issue is that the VNF information can't be retrieved or any new VNFs can't be created unless the VNF VMs are deleted before the device is updated.  |Before you update Azure Stack Edge device to 2009 release, use the PowerShell command `get-mecvnf` followed by `remove-mecvnf <VNF guid>` to remove all Virtual Network Function VMs one at a time. After the upgrade, you will need to redeploy the same VNFs.|-->


## <a name="next-steps"></a>Passaggi successivi

- [Aggiornare il dispositivo](azure-stack-edge-gpu-install-update.md)
