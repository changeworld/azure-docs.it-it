---
title: Creazione e caricamento di un VHD SUSE Linux in Azure
description: Informazioni su come creare e caricare un disco rigido virtuale (VHD) di Azure che contiene un sistema operativo SUSE Linux.
author: danielsollondon
ms.service: virtual-machines
ms.subservice: imaging
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 12/01/2020
ms.author: danis
ms.openlocfilehash: 276f5f4542ecea42c665764b8c4e5f66f2531126
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102552712"
---
# <a name="prepare-a-sles-or-opensuse-virtual-machine-for-azure"></a>Preparare una macchina virtuale SLES o openSUSE per Azure


In questo articolo si presuppone che l'utente abbia già installato un sistema operativo Linux SUSE od openSUSE in un disco rigido virtuale. Sono disponibili vari strumenti per creare file con estensione vhd, ad esempio una soluzione di virtualizzazione come Hyper-V. Per istruzioni, vedere [Installare il ruolo Hyper-V e configurare una macchina virtuale](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

## <a name="sles--opensuse-installation-notes"></a>Note di installazione di SLES/openSUSE
* Vedere anche [Note generali sull'installazione di Linux](create-upload-generic.md#general-linux-installation-notes) per altri suggerimenti sulla preparazione di Linux per Azure.
* Il formato VHDX non è supportato in Azure, solo nei **VHD fissi**.  È possibile convertire il disco in formato VHD tramite la console di gestione di Hyper-V o il cmdlet convert-vhd.
* Durante l'installazione del sistema operativo Linux è consigliabile usare partizioni standard anziché LVM, che spesso è la scelta predefinita per numerose installazioni. In questo modo sarà possibile evitare conflitti di nome LVM con le macchine virtuali clonate, in particolare se fosse necessario collegare un disco del sistema operativo a un'altra macchina virtuale per la risoluzione dei problemi. Se si preferisce, su dischi di dati si può usare [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) o [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid).
* Non configurare una partizione swap nel disco del sistema operativo. L'agente Linux può essere configurato in modo da creare un file swap sul disco temporaneo delle risorse.  Altre informazioni su questo argomento sono disponibili nei passaggi seguenti.
* Le dimensioni virtuali di tutti i dischi rigidi virtuali su Azure devono essere allineate a 1 MB. Quando si converte un disco non formattato in un disco rigido virtuale, prima della conversione è necessario assicurarsi che le dimensioni del disco non formattato siano un multiplo di 1 MB. Per altre informazioni, vedere [Note sull'installazione di Linux](create-upload-generic.md#general-linux-installation-notes).

## <a name="use-suse-studio"></a>Usare SUSE Studio
[SUSE Studio](https://studioexpress.opensuse.org/) consente di creare e gestire facilmente le immagini SLES e openSUSE per Azure e Hyper-V. Questo è l'approccio consigliato per personalizzare le proprie immagini SLES e openSUSE.

In alternativa alla creazione di un disco rigido virtuale personalizzato, SUSE pubblica anche immagini BYOS (Bring your own Subscription) per SLES in [VM Depot](https://www.microsoft.com/research/wp-content/uploads/2016/04/using-and-contributing-vms-to-vm-depot.pdf).

## <a name="prepare-suse-linux-enterprise-server-for-azure"></a>Preparare SUSE Linux Enterprise Server per Azure
1. Nel riquadro centrale della console di gestione di Hyper-V selezionare la macchina virtuale.
2. Fare clic su **Connect** per aprire la finestra della macchina virtuale.
3. Registrare il sistema SUSE Linux Enterprise per scaricare gli aggiornamenti e installare i pacchetti.
4. Aggiornare il sistema con tutte le patch più recenti:

    ```console
    # sudo zypper update
    ```
    
5. Installare l'agente Linux di Azure e cloud-init

    ```console
    # SUSEConnect -p sle-module-public-cloud/15.2/x86_64  (SLES 15 SP2)
    # sudo zypper refresh
    # sudo zypper install python-azure-agent
    # sudo zypper install cloud-init
    ```

6. Abilitare waagent & cloud-init per l'avvio all'avvio

    ```console
    # sudo chkconfig waagent on
    # systemctl enable cloud-init-local.service
    # systemctl enable cloud-init.service
    # systemctl enable cloud-config.service
    # systemctl enable cloud-final.service
    # systemctl daemon-reload
    # cloud-init clean
    ```

7. Aggiornare la configurazione di waagent e cloud-init

    ```console
    # sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
    # sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf

    # sudo sh -c 'printf "datasource:\n  Azure:" > /etc/cloud/cloud.cfg.d/91-azure_datasource.cfg'
    # sudo sh -c 'printf "reporting:\n  logging:\n    type: log\n  telemetry:\n    type: hyperv" > /etc/cloud/cloud.cfg.d/10-azure-kvp.cfg'
    ```

8. Modificare il file/etc/default/grub per assicurarsi che i log della console vengano inviati alla porta seriale e quindi aggiornare il file di configurazione principale con GRUB2-mkconfig-o/boot/GRUB2/grub.cfg

    ```config-grub
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```
    In questo modo si garantisce che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug.
    
9. Verificare che il file/etc/fstab faccia riferimento al disco usando il relativo UUID (by-uuid)
         
10. Modificare le regole udev per evitare la generazione di regole statiche per l'interfaccia Ethernet. Le regole seguenti possono provocare problemi quando si clona una macchina virtuale in Microsoft Azure o Hyper-V:

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```
   
11. Si consiglia di modificare il file "/etc/sysconfig/network/dhcp" e modificare il parametro `DHCLIENT_SET_HOSTNAME` come segue:

    ```config
    DHCLIENT_SET_HOSTNAME="no"
    ```

12. In "/etc/sudoers" rimuovere o impostare come commento le righe seguenti, se esistenti:

    ```text
    Defaults targetpw   # ask for the password of the target user i.e. root
    ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!
    ```

13. Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio. Questo è in genere il valore predefinito.

14. Scambia configurazione
 
    Non creare lo spazio di swapping sul disco del sistema operativo.

    In precedenza, l'agente Linux di Azure è stato usato per configurare automaticamente lo spazio di swapping usando il disco risorse locale collegato alla macchina virtuale dopo il provisioning della macchina virtuale in Azure. Tuttavia, questo è ora gestito da cloud-init, **non è necessario** usare l'agente Linux per formattare il disco risorse creare il file di scambio, modificare i parametri seguenti in `/etc/waagent.conf` modo appropriato:

    ```console
    # sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    # sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
    ```

    Se si desidera installare, formattare e creare uno scambio è possibile:
    * Passarla come configurazione cloud-init ogni volta che si crea una macchina virtuale.
    * Usare una direttiva cloud-init nell'immagine che eseguirà questa operazione ogni volta che viene creata la macchina virtuale:

        ```console
        cat > /etc/cloud/cloud.cfg.d/00-azure-swap.cfg << EOF
        #cloud-config
        # Generated by Azure cloud image build
        disk_setup:
          ephemeral0:
            table_type: mbr
            layout: [66, [33, 82]]
            overwrite: True
        fs_setup:
          - device: ephemeral0.1
            filesystem: ext4
          - device: ephemeral0.2
            filesystem: swap
        mounts:
          - ["ephemeral0.1", "/mnt"]
          - ["ephemeral0.2", "none", "swap", "sw", "0", "0"]
        EOF
        ```

15. Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure:

    ```console
    # sudo rm -rf /var/lib/waagent/
    # sudo rm -f /var/log/waagent.log

    # waagent -force -deprovision+user
    # rm -f ~/.bash_history
    

    # export HISTSIZE=0

    # logout
    ```
16. Fare clic su **Azione -> Arresta** nella console di gestione di Hyper-V. Il file VHD Linux è ora pronto per il caricamento in Azure.

---
## <a name="prepare-opensuse-131"></a>Preparare openSUSE 13.1+
1. Nel riquadro centrale della console di gestione di Hyper-V selezionare la macchina virtuale.
2. Fare clic su **Connect** per aprire la finestra della macchina virtuale.
3. Nella shell eseguire il comando '`zypper lr`'. Se questo comando restituisce un output simile al seguente, i repository vengono configurati come previsto e non sono necessarie modifiche. Si noti che i numeri di versione possono variare:

   | # | Alias                 | Nome                  | Abilitato | Aggiorna
   | - | :-------------------- | :-------------------- | :------ | :------
   | 1 | Cloud: Tools_13.1      | Cloud: Tools_13.1      | Sì     | Sì
   | 2 | openSUSE_13 openSUSE_13.1_OSS     | openSUSE_13 openSUSE_13.1_OSS     | Sì     | Sì
   | 3 | openSUSE_13 openSUSE_13.1_Updates | openSUSE_13 openSUSE_13.1_Updates | Sì     | Sì

    Se il comando restituisce un messaggio simile a "Nessun archivio definito..." usare i comandi seguenti per aggiungere gli archivi:

    ```console
    # sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1
    # sudo zypper ar -f https://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
    # sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates
    ```

    Sarà quindi possibile verificare che i repository siano stati aggiunti eseguendo nuovamente il comando '`zypper lr`' . Se uno degli archivi di aggiornamento pertinenti non è abilitato, abilitarlo con il comando seguente:

    ```console
    # sudo zypper mr -e [NUMBER OF REPOSITORY]
    ```

4. Aggiornare il kernel all'ultima versione disponibile:

    ```console
    # sudo zypper up kernel-default
    ```

    Oppure per aggiornare il sistema con tutte le patch più recenti:

    ```console
    # sudo zypper update
    ```

5. Installare l'agente Linux di Azure.

    ```console
    # sudo zypper install WALinuxAgent
    ```

6. Modificare la riga di avvio del kernel nella configurazione GRUB per includere ulteriori parametri del kernel per Azure. A questo scopo, aprire "/boot/grub/menu.lst" in un editor di testo e verificare che il kernel predefinito includa i parametri seguenti:

    ```config-grub
     console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

   In questo modo si garantisce che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug. Rimuovere inoltre i messaggi seguenti dalla riga di avvio del kernel, se presenti:

    ```config-grub
     libata.atapi_enabled=0 reserve=0x1f0,0x8
    ```

7. Si consiglia di modificare il file "/etc/sysconfig/network/dhcp" e modificare il parametro `DHCLIENT_SET_HOSTNAME` come segue:

    ```config
     DHCLIENT_SET_HOSTNAME="no"
    ```

8. **Importante:** In "/etc/sudoers" rimuovere o impostare come commento le righe seguenti, se esistenti:

    ```text
    Defaults targetpw   # ask for the password of the target user i.e. root
    ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!
    ```

9. Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio.  Questo è in genere il valore predefinito.
10. Non creare l'area di swap sul disco del sistema operativo.

    L'agente Linux di Azure può configurare automaticamente l'area di swap utilizzando il disco risorse locale collegato alla VM dopo il provisioning in Azure. Si noti che il disco risorse locale è un disco *temporaneo* e potrebbe essere svuotato in seguito al deprovisioning della macchina virtuale. Dopo aver installato l'agente Linux di Azure come illustrato nel passaggio precedente, modificare i parametri seguenti in /etc/waagent.conf in modo appropriato:

    ```config-conf
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

11. Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure:

    ```console
    # sudo waagent -force -deprovision
    # export HISTSIZE=0
    # logout
    ```

12. Verificare che l'agente Linux di Azure venga eseguito all'avvio:

    ```console
    # sudo systemctl enable waagent.service
    ```

13. Fare clic su **Azione -> Arresta** nella console di gestione di Hyper-V. Il file VHD Linux è ora pronto per il caricamento in Azure.

## <a name="next-steps"></a>Passaggi successivi
È ora possibile usare il disco rigido virtuale SUSE Linux per creare nuove macchine virtuali in Azure. Se è la prima volta che si carica il file VHD in Azure, vedere [Creare una macchina virtuale Linux da un disco personalizzato](upload-vhd.md#option-1-upload-a-vhd).