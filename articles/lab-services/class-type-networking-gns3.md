---
title: Configurare un Lab di rete con Azure Lab Services e GNS3 | Microsoft Docs
description: Informazioni su come configurare un Lab usando Azure Lab Services per insegnare la rete con GNS3.
ms.topic: article
ms.date: 01/19/2021
ms.openlocfilehash: dec5dea13d5a89536a06da45fc57d33881a9b3ad
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99500513"
---
# <a name="set-up-a-lab-to-teach-a-networking-class"></a>Configurare un Lab per insegnare una classe di rete 
Questo articolo illustra come configurare una classe incentrata su come consentire agli studenti di emulare, configurare, testare e risolvere i problemi relativi alle reti virtuali e reali usando il software [GNS3](https://www.gns3.com/) . 

Questo articolo è costituito da due sezioni principali. La prima sezione illustra come creare il Lab della classe. La seconda sezione illustra come creare il computer modello con la virtualizzazione annidata abilitata e con GNS3 installato e configurato.

## <a name="lab-configuration"></a>Configurazione del lab
Per configurare questo lab, è necessaria una sottoscrizione di Azure per iniziare. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare. Una volta ottenuto una sottoscrizione di Azure, è possibile creare un nuovo account Lab in Azure Lab Services o usare un account esistente. Vedere l'esercitazione seguente per la creazione di un nuovo account Lab: [esercitazione per la configurazione di un account Lab](tutorial-setup-lab-account.md).

Seguire [questa esercitazione](tutorial-setup-classroom-lab.md) per creare un nuovo Lab e quindi applicare le impostazioni seguenti:

| Dimensioni della macchina virtuale | Immagine |
| -------------------- | ----- | 
| Grande (virtualizzazione annidata) | Windows 10 Pro, versione 1909 |

## <a name="template-machine"></a>Computer modello 

Una volta creato il modello, avviare il computer e connettersi al computer per completare le tre attività principali seguenti. 
 
1. Preparare il computer modello per la virtualizzazione nidificata.
2. Installare GNS3.
3. Creare una VM GNS3 nidificata in Hyper-V.
4. Configurare GNS3 per l'uso della VM Windows Hyper-V.
5. Aggiungere dispositivi appropriati.
6. Pubblicare il modello.


### <a name="prepare-template-machine-for-nested-virtualization"></a>Preparare il computer modello per la virtualizzazione nidificata
- Seguire le istruzioni riportate in [questo articolo](how-to-enable-nested-virtualization-template-vm.md) per preparare la macchina virtuale modello per la virtualizzazione nidificata. 

### <a name="install-gns3"></a>Installare GNS3
- Seguire le istruzioni per l' [installazione di GNS3 in Windows](https://docs.gns3.com/docs/getting-started/installation/windows).  Assicurarsi di includere l'installazione della **VM GNS3** nella finestra di dialogo componente, vedere di seguito.

![SelectGNS3vm](./media/class-type-networking-gns3/gns3-select-vm.png)

Alla fine si raggiungerà la selezione della macchina virtuale GNS3. Assicurarsi di selezionare l'opzione **Hyper-V** .

![SelectHyperV](./media/class-type-networking-gns3/gns3-vm-hyper-v.png)

  Questa opzione consente di scaricare lo script di PowerShell e i file VHD per creare la macchina virtuale GNS3 nella console di gestione di Hyper-V. Continuare l'installazione usando i valori predefiniti. **Al termine dell'installazione, non avviare GNS3**.

### <a name="create-gns3-vm"></a>Creare una VM GNS3
Al termine dell'installazione, un file zip **"GNS3.VM.Hyper-V.2.2.17.zip"** viene scaricato nella stessa cartella del file di installazione, contenente le unità e lo script di PowerShell per creare la macchina virtuale Hyper-V.
- **Estrarre tutti** i GNS3.VM.Hyper-V.2.2.17.zip.  Questa azione consente di estrarre le unità e lo script di PowerShell per creare la macchina virtuale.
- **Eseguire con PowerShell** nello script di powershell "create-vm.ps1" facendo clic con il pulsante destro del mouse sul file.
- È possibile che venga visualizzata una richiesta di modifica dei criteri di esecuzione. Immettere "Y" per eseguire lo script.

![PSExecutionPolicy](./media/class-type-networking-gns3/powershell-execution-policy-change.png)

- Una volta completato lo script, è possibile verificare che la macchina virtuale "GNS3 VM" sia stata creata nella console di gestione di Hyper-V.

### <a name="configure-gns3-to-use-hyper-v-vm"></a>Configurare GNS3 per l'uso di macchine virtuali Hyper-V
Ora che GNS3 è installato e la macchina virtuale GNS3 è stata aggiunta, avviare GNS3 per collegare i due.  L' [installazione guidata di GNS3 verrà avviata automaticamente.](https://docs.gns3.com/docs/getting-started/setup-wizard-gns3-vm#local-gns3-vm-setup-wizard)  
- Usare le **appliance di esecuzione dalla macchina virtuale.** opzione.  Usare le impostazioni predefinite per il resto della procedura guidata fino a quando non si raggiunge lo **strumento VMware vmrun.** errore.

![VMWareError](./media/class-type-networking-gns3/gns3-vmware-vmrun-tool-not-found.png)

- Scegliere **OK** e **annullare** la procedura guidata.
- Per completare la connessione alla macchina virtuale Hyper-v, aprire la   ->    ->  **VM GNS3** di modifica preferenze e selezionare **Abilita la VM GNS3** e selezionare l'opzione **Hyper-v** .
 
![EnableGNS3VMs](./media/class-type-networking-gns3/gns3-preference-vm.png)

### <a name="add-appropriate-appliances"></a>Aggiungere Appliance appropriate

A questo punto, è necessario aggiungere le [Appliance appropriate per la classe.](https://docs.gns3.com/docs/using-gns3/beginners/install-from-marketplace)

### <a name="publish-template"></a>Pubblicare il modello

Ora che la macchina virtuale modello è configurata correttamente e pronta per la pubblicazione, è necessario verificare alcuni punti chiave.
- Assicurarsi che la macchina virtuale GNS3 sia spenta o spenta.  Se la macchina virtuale è ancora in esecuzione, la macchina virtuale verrà danneggiata.
- Chiudere GNS3, la pubblicazione mentre è in esecuzione può causare effetti collaterali imprevisti.
- Pulire tutti i file di installazione o altri file non necessari.

## <a name="cost"></a>Costi  

Se si vuole stimare il costo di questo lab, è possibile usare l'esempio seguente: 
 
Per una classe di 25 studenti con 20 ore di lezioni pianificate e 10 ore di quota per compiti o assegnazioni, il prezzo del lab sarà dato dalla formula seguente: 

25 studenti * (20 + 10) ore * 84 unità Lab * 0,01 USD all'ora = 630 USD. 

**Importante:** La stima dei costi è solo a scopo esemplificativo.  Per informazioni aggiornate sui prezzi, vedere [Azure Lab Services prezzi](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusione
Questo articolo illustra la procedura per creare un Lab per la configurazione di rete con GNS3.

## <a name="next-steps"></a>Passaggi successivi
I passaggi successivi sono comuni alla configurazione di qualsiasi lab:

- [Aggiungere utenti](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Impostare la quota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Impostare una pianificazione](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Inviare i collegamenti di registrazione agli studenti tramite posta elettronica](how-to-configure-student-usage.md#send-invitations-to-users).