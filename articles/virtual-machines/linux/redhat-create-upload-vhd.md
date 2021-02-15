---
title: Creare e caricare un VHD di Red Hat Enterprise Linux per l'utilizzo in Azure
description: Informazioni su come creare e caricare un disco rigido virtuale (VHD) di Azure contenente un sistema operativo Linux RedHat.
author: danielsollondon
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: how-to
ms.date: 12/01/2020
ms.author: danis
ms.openlocfilehash: 28f82b641216dcd77b650b066c72bef4c4470dd2
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/10/2021
ms.locfileid: "100098963"
---
# <a name="prepare-a-red-hat-based-virtual-machine-for-azure"></a>Preparare una macchina virtuale basata su RedHat per Azure

In questo articolo verrà descritto come preparare una macchina virtuale Red Hat Enterprise Linux (RHEL) per l'utilizzo in Azure. Le versioni di RHEL trattate in questo articolo sono la 6.7+ e la 7.1+. Gli hypervisor per la preparazione illustrati in questo articolo sono Hyper-V, KVM (Kernel-based Virtual Machine) e VMware. Per altre informazioni sui requisiti di idoneità per partecipare al programma di accesso al cloud di Red Hat, vedere gli articoli relativi al [sito web di accesso al cloud di Red Hat](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) e all'[esecuzione di RHEL in Azure](https://access.redhat.com/ecosystem/ccsp/microsoft-azure). Per informazioni su come automatizzare la creazione di immagini RHEL, vedere [Generatore di immagini di Azure](../image-builder-overview.md).

## <a name="hyper-v-manager"></a>Console di gestione di Hyper-V

Questa sezione illustra come preparare una macchina virtuale [RHEL 6](#rhel-6-using-hyper-v-manager), [RHEL 7](#rhel-7-using-hyper-v-manager)o [RHEL 8](#rhel-8-using-hyper-v-manager) con la console di gestione di Hyper-V.

### <a name="prerequisites"></a>Prerequisiti
In questa sezione si presuppone che si sia già ottenuto un file ISO dal sito Web Red Hat e che sia già stata installata un'immagine RHEL in un disco rigido virtuale (VHD). Per altri dettagli su come usare la console di gestione di Hyper-V per installare un'immagine del sistema operativo, vedere l'articolo su come [installare il ruolo Hyper-V e configurare una macchina virtuale](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

**Note sull'installazione di RHEL**

* Azure non supporta il formato VHDX. Azure supporta solo dischi rigidi virtuali a dimensione fissa. È possibile usare la console di gestione di Hyper-V o il cmdlet convert-vhd per convertire il disco in formato VHD. Se si usa VirtualBox, durante la creazione del disco selezionare **Fixed size** (A dimensione fissa) anziché l'opzione predefinita di allocazione dinamica.
* Azure supporta le macchine virtuali Gen1 (BIOS boot) & Gen2 (UEFI Boot).
* La dimensione massima consentita per il disco rigido virtuale è 1.023 GB.
* LVM (Logical Volume Manager) è supportato e può essere usato nel disco del sistema operativo o nei dischi dati in macchine virtuali di Azure. Tuttavia, in genere è consigliabile usare partizioni standard sul disco del sistema operativo anziché LVM. Questa procedura consentirà di evitare conflitti di nome LVM con le macchine virtuali clonate, in particolare se fosse eventualmente necessario collegare un disco del sistema operativo a un'altra macchina virtuale identica per la risoluzione dei problemi. Vedere anche la documentazione di [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) e [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid).
* **È necessario il supporto del kernel per il montaggio di file System UDF (Universal Disk Format)**. Al primo avvio in Azure, i supporti con formattazione UDF collegati al guest passano la configurazione di provisioning alla macchina virtuale Linux. L'agente Linux di Azure deve essere in grado di montare la funzione definita dall'utente file system per leggere la configurazione ed effettuare il provisioning della macchina virtuale, senza questo problema, il provisioning avrà esito negativo.
* Non configurare una partizione di swapping sul disco del sistema operativo. Altre informazioni su questo argomento sono disponibili nei passaggi riportati di seguito.

* Le dimensioni virtuali di tutti i dischi rigidi virtuali su Azure devono essere allineate a 1 MB. Quando si converte un disco non formattato in un disco rigido virtuale, prima della conversione è necessario assicurarsi che le dimensioni del disco non formattato siano un multiplo di 1 MB. Altri dettagli sono disponibili nelle procedure seguenti. Per altre informazioni, vedere anche [Note sull'installazione di Linux](create-upload-generic.md#general-linux-installation-notes).

### <a name="rhel-6-using-hyper-v-manager"></a>RHEL 6 con la console di gestione di Hyper-V

1. Nella console di gestione di Hyper-V selezionare la macchina virtuale.

1. Fare clic su **Connetti** per aprire una finestra della console per la macchina virtuale.

1. In RHEL 6, NetworkManager può interferire con l'agente Linux di Azure. Disinstallare il pacchetto eseguendo questo comando:

    ```console
    # sudo rpm -e --nodeps NetworkManager
    ```

1. Creare o modificare il file `/etc/sysconfig/network` e aggiungere il testo seguente:

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Creare o modificare il file `/etc/sysconfig/network-scripts/ifcfg-eth0` e aggiungere il testo seguente:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

1. Spostare o eliminare le regole udev per evitare la generazione di regole statiche per l'interfaccia Ethernet. Le regole seguenti provocano problemi quando si clona una macchina virtuale in Microsoft Azure o Hyper-V:

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

1. Assicurarsi che il servizio di rete venga eseguito all'avvio attivando il comando seguente:

    ```console
    # sudo chkconfig network on
    ```

1. Registrare la propria sottoscrizione Red Hat per abilitare l'installazione dei pacchetti dall'archivio RHEL eseguendo questo comando:

    ```console
    # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. È stato effettuato il push del pacchetto WALinuxAgent `WALinuxAgent-<version>` nel repository di funzionalità aggiuntive di Red Hat. Abilitare il repository di funzionalità aggiuntive eseguendo questo comando:

    ```console
    # subscription-manager repos --enable=rhel-6-server-extras-rpms
    ```

1. Modificare la riga di avvio del kernel nella configurazione GRUB per includere ulteriori parametri del kernel per Azure. Per eseguire questa modifica, aprire `/boot/grub/menu.lst` in un editor di testo e verificare che il kernel predefinito includa i parametri seguenti:

    ```config-grub
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

    In questo modo si garantisce inoltre che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug.
    
    È consigliabile anche rimuovere i parametri seguenti:

    ```config-grub
    rhgb quiet crashkernel=auto
    ```
    
    L'avvio grafico e l'avvio silenzioso non sono utili in un ambiente cloud se tutti i log devono essere inviati alla porta seriale.  È possibile mantenere l'opzione `crashkernel` configurata, se necessario. Si noti che questo parametro riduce la quantità di memoria disponibile nella macchina virtuale di almeno 128 MB. Questa configurazione potrebbe causare problemi in macchine virtuali di dimensioni inferiori.


1. Verificare che il server SSH (Secure Shell) sia installato e configurato per l'esecuzione all'avvio, che è in genere l'impostazione predefinita. Modificare /etc/ssh/sshd_config per includere la riga seguente:

    ```config
    ClientAliveInterval 180
    ```

1. Installare l'agente Linux di Azure eseguendo il comando seguente:

    ```console
    # sudo yum install WALinuxAgent

    # sudo chkconfig waagent on
    ```

    L'installazione del pacchetto WALinuxAgent determina la rimozione dei pacchetti NetworkManager e NetworkManager-gnome, se non sono già stati rimossi nel passaggio 3.

1. Non creare lo spazio di swapping sul disco del sistema operativo.

    L'agente Linux di Azure può configurare automaticamente lo spazio di swapping usando il disco risorse locale collegato alla macchina virtuale dopo il provisioning della macchina virtuale in Azure. Si noti che il disco risorse locale è un disco temporaneo e potrebbe essere svuotato se viene effettuato il deprovisioning della macchina virtuale. Dopo aver installato l'agente Linux di Azure nel passaggio precedente, modificare nel modo appropriato i parametri seguenti in /etc/waagent.conf:

    ```config-cong
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

1. Annullare la sottoscrizione (se necessario) eseguendo il comando seguente:

    ```console
    # sudo subscription-manager unregister
    ```

1. Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure:

    ```console
    # Note: if you are migrating a specific virtual machine and do not wish to create a generalized image,
    # skip the deprovision step
    # sudo waagent -force -deprovision

    # export HISTSIZE=0

    # logout
    ```

1. Fare clic su **azione**  >  **Arresta** nella console di gestione di Hyper-V. Il file VHD Linux è ora pronto per il caricamento in Azure.


### <a name="rhel-7-using-hyper-v-manager"></a>RHEL 7 con la console di gestione di Hyper-V

1. Nella console di gestione di Hyper-V selezionare la macchina virtuale.

1. Fare clic su **Connetti** per aprire una finestra della console per la macchina virtuale.

1. Creare o modificare il file `/etc/sysconfig/network` e aggiungere il testo seguente:

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Creare o modificare il file `/etc/sysconfig/network-scripts/ifcfg-eth0` e aggiungere il testo seguente:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    PERSISTENT_DHCLIENT=yes
    NM_CONTROLLED=yes
    ```

1. Assicurarsi che il servizio di rete venga eseguito all'avvio attivando il comando seguente:

    ```console
    # sudo systemctl enable network
    ```

1. Registrare la propria sottoscrizione Red Hat per abilitare l'installazione dei pacchetti dall'archivio RHEL eseguendo questo comando:

    ```console
    # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Modificare la riga di avvio del kernel nella configurazione GRUB per includere ulteriori parametri del kernel per Azure. Per eseguire questa modifica, aprire `/etc/default/grub` in un editor di testo e modificare il parametro `GRUB_CMDLINE_LINUX`. Ad esempio:

    
    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 earlyprintk=ttyS0 net.ifnames=0"
    GRUB_TERMINAL_OUTPUT="serial console"
    GRUB_SERIAL_COMMAND="serial --speed=115200 --unit=0 --word=8 --parity=no --stop=1
    ```
   
    Ciò garantisce inoltre che tutti i messaggi della console vengano inviati alla prima porta seriale e consentano l'interazione con la console seriale, che può aiutare il supporto tecnico di Azure con problemi di debug. Questa configurazione disattiva anche le nuove convenzioni di denominazione di RHEL 7 per le schede di interfaccia di rete.

    ```config
    rhgb quiet crashkernel=auto
    ```
   
    L'avvio grafico e l'avvio silenzioso non sono utili in un ambiente cloud se tutti i log devono essere inviati alla porta seriale. È possibile mantenere l'opzione `crashkernel` configurata, se necessario. Si noti che questo parametro riduce la quantità di memoria disponibile nella macchina virtuale di almeno 128 MB e questo potrebbe causare problemi in macchine virtuali di dimensioni inferiori.

1. Dopo aver terminato di modificare `/etc/default/grub`, eseguire questo comando per ricompilare la configurazione GRUB:

    ```console
    # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```
    > [!NOTE]
    > Se si carica una macchina virtuale abilitata per UEFI, il comando per aggiornare GRUB è `grub2-mkconfig -o /boot/efi/EFI/redhat/grub.cfg` .

1. Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio, che è in genere l'impostazione predefinita. Modificare `/etc/ssh/sshd_config` per poter includere la riga seguente:

    ```config
    ClientAliveInterval 180
    ```

1. È stato effettuato il push del pacchetto WALinuxAgent `WALinuxAgent-<version>` nel repository di funzionalità aggiuntive di Red Hat. Abilitare il repository di funzionalità aggiuntive eseguendo questo comando:

    ```console
    # subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

1. Installare l'agente Linux di Azure, cloud-init e altre utilità necessarie eseguendo il comando seguente:

    ```console
    # sudo yum install -y WALinuxAgent cloud-init cloud-utils-growpart gdisk hyperv-daemons

    # sudo systemctl enable waagent.service
    # sudo systemctl enable cloud-init.service
    ```

1. Configurare cloud-init per gestire il provisioning:

    1. Configurare waagent per cloud-init:

    ```console
    sed -i 's/Provisioning.Agent=auto/Provisioning.Agent=cloud-init/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
    ```
    > [!NOTE]
    > Se si esegue la migrazione di una macchina virtuale specifica e non si desidera creare un'immagine generalizzata, impostare `Provisioning.Agent=disabled` sulla `/etc/waagent.conf` configurazione.
    
    1. Configura montaggi:

    ```console
    echo "Adding mounts and disk_setup to init stage"
    sed -i '/ - mounts/d' /etc/cloud/cloud.cfg
    sed -i '/ - disk_setup/d' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - mounts' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - disk_setup' /etc/cloud/cloud.cfg
    ```
    
    1. Configurare l'origine dati di Azure:

    ```console
    echo "Allow only Azure datasource, disable fetching network setting via IMDS"
    cat > /etc/cloud/cloud.cfg.d/91-azure_datasource.cfg <<EOF
    datasource_list: [ Azure ]
    datasource:
    Azure:
        apply_network_config: False
    EOF
    ```

    1. Se configurato, rimuovere il file esistente:

    ```console
    if [[ -f /mnt/resource/swapfile ]]; then
    echo "Removing swapfile" #RHEL uses a swapfile by defaul
    swapoff /mnt/resource/swapfile
    rm /mnt/resource/swapfile -f
    fi
    ```
    1. Configurare la registrazione cloud-init:
    ```console
    echo "Add console log file"
    cat >> /etc/cloud/cloud.cfg.d/05_logging.cfg <<EOF

    # This tells cloud-init to redirect its stdout and stderr to
    # 'tee -a /var/log/cloud-init-output.log' so the user can see output
    # there without needing to look on the console.
    output: {all: '| tee -a /var/log/cloud-init-output.log'}
    EOF

    ```

1. La configurazione dello scambio non crea spazio di swapping sul disco del sistema operativo.

    In precedenza, l'agente Linux di Azure è stato usato per configurare automaticamente lo spazio di swapping usando il disco risorse locale collegato alla macchina virtuale dopo il provisioning della macchina virtuale in Azure. Tuttavia, questo è ora gestito da cloud-init, **non è necessario** usare l'agente Linux per formattare il disco risorse creare il file di scambio, modificare i parametri seguenti in `/etc/waagent.conf` modo appropriato:

    ```console
    ResourceDisk.Format=n
    ResourceDisk.EnableSwap=n
    ```

    Se si desidera installare, formattare e creare uno scambio è possibile:
    * Passa come configurazione cloud-init ogni volta che crei una macchina virtuale
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
1. Se si vuole annullare la registrazione della sottoscrizione, eseguire il comando seguente:

    ```console
    # sudo subscription-manager unregister
    ```

1. Effettuare il deprovisioning

    Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure:

    > [!CAUTION]
    > Se si esegue la migrazione di una macchina virtuale specifica e non si desidera creare un'immagine generalizzata, ignorare il passaggio di deprovisioning. L'esecuzione del comando `waagent -force -deprovision` renderà il computer di origine inutilizzabile. questo passaggio è destinato solo alla creazione di un'immagine generalizzata.
    ```console
    # sudo waagent -force -deprovision

    # export HISTSIZE=0

    # logout
    ```
    

1. Fare clic su **azione**  >  **Arresta** nella console di gestione di Hyper-V. Il file VHD Linux è ora pronto per il caricamento in Azure.

### <a name="rhel-8-using-hyper-v-manager"></a>RHEL 8 con la console di gestione di Hyper-V

1. Nella console di gestione di Hyper-V selezionare la macchina virtuale.

1. Fare clic su **Connetti** per aprire una finestra della console per la macchina virtuale.

1. Verificare che il servizio gestore reti si avvii in fase di avvio eseguendo il comando seguente:

    ```console
    # sudo systemctl enable NetworkManager.service
    ```

1. Configurare l'interfaccia di rete per l'avvio automatico all'avvio e l'utilizzo di DHCP:

    ```console
    # nmcli con mod eth0 connection.autoconnect yes ipv4.method auto
    ```


1. Registrare la propria sottoscrizione Red Hat per abilitare l'installazione dei pacchetti dall'archivio RHEL eseguendo questo comando:

    ```console
    # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Modificare la riga di avvio del kernel nella configurazione di GRUB per includere altri parametri del kernel per Azure e abilitare la console seriale. 

    1. Rimuovere i parametri di GRUB correnti:
    ```console
    # grub2-editenv - unset kernelopts
    ```

    1. Modificare `/etc/default/grub` in un editor di testo e aggiungere i parametri seguenti:

    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 earlyprintk=ttyS0 net.ifnames=0"
    GRUB_TERMINAL_OUTPUT="serial console"
    GRUB_SERIAL_COMMAND="serial --speed=115200 --unit=0 --word=8 --parity=no --stop=1"
    ```
   
   Ciò garantisce inoltre che tutti i messaggi della console vengano inviati alla prima porta seriale e consentano l'interazione con la console seriale, che può aiutare il supporto tecnico di Azure con problemi di debug. Questa configurazione disattiva anche le nuove convenzioni di denominazione per le schede di rete.
   
   1. Inoltre, è consigliabile rimuovere i parametri seguenti:

    ```config
    rhgb quiet crashkernel=auto
    ```
   
    L'avvio grafico e l'avvio silenzioso non sono utili in un ambiente cloud se tutti i log devono essere inviati alla porta seriale. È possibile mantenere l'opzione `crashkernel` configurata, se necessario. Si noti che questo parametro riduce la quantità di memoria disponibile nella macchina virtuale di almeno 128 MB e questo potrebbe causare problemi in macchine virtuali di dimensioni inferiori.

1. Dopo aver terminato di modificare `/etc/default/grub`, eseguire questo comando per ricompilare la configurazione GRUB:

    ```console
    # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```
    Per una macchina virtuale abilitata per UEFI, eseguire il comando seguente:

    ```console
    # sudo grub2-mkconfig -o /boot/efi/EFI/redhat/grub.cfg
    ```

1. Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio, che è in genere l'impostazione predefinita. Modificare `/etc/ssh/sshd_config` per poter includere la riga seguente:

    ```config
    ClientAliveInterval 180
    ```

1. Installare l'agente Linux di Azure, cloud-init e altre utilità necessarie eseguendo il comando seguente:

    ```console
    # sudo yum install -y WALinuxAgent cloud-init cloud-utils-growpart gdisk hyperv-daemons

    # sudo systemctl enable waagent.service
    # sudo systemctl enable cloud-init.service
    ```

1. Configurare cloud-init per gestire il provisioning:

    1. Configurare waagent per cloud-init:

    ```console
    sed -i 's/Provisioning.Agent=auto/Provisioning.Agent=cloud-init/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
    ```
    > [!NOTE]
    > Se si esegue la migrazione di una macchina virtuale specifica e non si desidera creare un'immagine generalizzata, impostare `Provisioning.Agent=disabled` sulla `/etc/waagent.conf` configurazione.
    
    1. Configura montaggi:

    ```console
    echo "Adding mounts and disk_setup to init stage"
    sed -i '/ - mounts/d' /etc/cloud/cloud.cfg
    sed -i '/ - disk_setup/d' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - mounts' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - disk_setup' /etc/cloud/cloud.cfg
    ```
    
    1. Configurare l'origine dati di Azure:

    ```console
    echo "Allow only Azure datasource, disable fetching network setting via IMDS"
    cat > /etc/cloud/cloud.cfg.d/91-azure_datasource.cfg <<EOF
    datasource_list: [ Azure ]
    datasource:
    Azure:
        apply_network_config: False
    EOF
    ```

    1. Se configurato, rimuovere il file esistente:

    ```console
    if [[ -f /mnt/resource/swapfile ]]; then
    echo "Removing swapfile" #RHEL uses a swapfile by defaul
    swapoff /mnt/resource/swapfile
    rm /mnt/resource/swapfile -f
    fi
    ```
    1. Configurare la registrazione cloud-init:
    ```console
    echo "Add console log file"
    cat >> /etc/cloud/cloud.cfg.d/05_logging.cfg <<EOF

    # This tells cloud-init to redirect its stdout and stderr to
    # 'tee -a /var/log/cloud-init-output.log' so the user can see output
    # there without needing to look on the console.
    output: {all: '| tee -a /var/log/cloud-init-output.log'}
    EOF

    ```

1. La configurazione dello scambio non crea spazio di swapping sul disco del sistema operativo.

    In precedenza, l'agente Linux di Azure è stato usato per configurare automaticamente lo spazio di swapping usando il disco risorse locale collegato alla macchina virtuale dopo il provisioning della macchina virtuale in Azure. Tuttavia, questo è ora gestito da cloud-init, **non è necessario** usare l'agente Linux per formattare il disco risorse creare il file di scambio, modificare i parametri seguenti in `/etc/waagent.conf` modo appropriato:

    ```console
    ResourceDisk.Format=n
    ResourceDisk.EnableSwap=n
    ```

    Se si desidera installare, formattare e creare uno scambio è possibile:
    * Passa come configurazione cloud-init ogni volta che crei una macchina virtuale
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
1. Se si vuole annullare la registrazione della sottoscrizione, eseguire il comando seguente:

    ```console
    # sudo subscription-manager unregister
    ```

1. Effettuare il deprovisioning

    Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure:

    ```console
    # sudo waagent -force -deprovision

    # export HISTSIZE=0

    # logout
    ```
    > [!CAUTION]
    > Se si esegue la migrazione di una macchina virtuale specifica e non si desidera creare un'immagine generalizzata, ignorare il passaggio di deprovisioning. L'esecuzione del comando `waagent -force -deprovision` renderà il computer di origine inutilizzabile. questo passaggio è destinato solo alla creazione di un'immagine generalizzata.


1. Fare clic su **azione**  >  **Arresta** nella console di gestione di Hyper-V. Il file VHD Linux è ora pronto per il caricamento in Azure.


## <a name="kvm"></a>KVM

Questa sezione illustra come usare KVM per preparare una distribuzione [RHEL 6](#rhel-6-using-kvm) o [RHEL 7](#rhel-7-using-kvm) da caricare in Azure. 

### <a name="rhel-6-using-kvm"></a>RHEL 6 con KVM

1. Scaricare l'immagine KVM di RHEL 6 dal sito Web Red Hat.

1. Impostare una password radice.

    Generare una password crittografata e copiare l'output del comando:

    ```console
    # openssl passwd -1 changeme
    ```

    Impostare una password radice con guestfish:

    ```console
    # guestfish --rw -a <image-name>
    > <fs> run
    > <fs> list-filesystems
    > <fs> mount /dev/sda1 /
    > <fs> vi /etc/shadow
    > <fs> exit
    ```

   Modificare il secondo campo dell'utente radice da "!!" con la password crittografata.

1. Creare una macchina virtuale in KVM dall'immagine qcow2. Impostare il tipo di disco su **qcow2** e il modello del dispositivo di interfaccia di rete virtuale su **virtio**. Avviare quindi la macchina virtuale e accedere come utente ROOT.

1. Creare o modificare il file `/etc/sysconfig/network` e aggiungere il testo seguente:

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Creare o modificare il file `/etc/sysconfig/network-scripts/ifcfg-eth0` e aggiungere il testo seguente:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

1. Spostare o eliminare le regole udev per evitare la generazione di regole statiche per l'interfaccia Ethernet. Le regole seguenti causano problemi quando si clona una macchina virtuale in Azure o Hyper-V:

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

1. Assicurarsi che il servizio di rete venga eseguito all'avvio attivando il comando seguente:

    ```console
    # chkconfig network on
    ```

1. Registrare la propria sottoscrizione Red Hat per abilitare l'installazione dei pacchetti dall'archivio RHEL eseguendo questo comando:

    ```console
    # subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Modificare la riga di avvio del kernel nella configurazione GRUB per includere ulteriori parametri del kernel per Azure. Per eseguire questa configurazione, aprire `/boot/grub/menu.lst` in un editor di testo e verificare che il kernel predefinito includa i parametri seguenti:

    ```config-grub
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

    In questo modo si garantisce inoltre che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug.
    
    È consigliabile anche rimuovere i parametri seguenti:

    ```config-grub
    rhgb quiet crashkernel=auto
    ```

    L'avvio grafico e l'avvio silenzioso non sono utili in un ambiente cloud se tutti i log devono essere inviati alla porta seriale. È possibile mantenere l'opzione `crashkernel` configurata, se necessario. Si noti che questo parametro riduce la quantità di memoria disponibile nella macchina virtuale di almeno 128 MB e questo potrebbe causare problemi in macchine virtuali di dimensioni inferiori.


1. Aggiungere i moduli Hyper-V a initramfs:  

    Modificare `/etc/dracut.conf` e aggiungere il contenuto seguente:

    ```config-conf
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    Ricompilare initramfs:

    ```config-conf
    # dracut -f -v
    ```

1. Disinstallare cloud-init:

    ```console
    # yum remove cloud-init
    ```

1. Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio:

    ```console
    # chkconfig sshd on
    ```

    Modificare /etc/ssh/sshd_config per includere le righe seguenti:

    ```config
    PasswordAuthentication yes
    ClientAliveInterval 180
    ```

1. È stato effettuato il push del pacchetto WALinuxAgent `WALinuxAgent-<version>` nel repository di funzionalità aggiuntive di Red Hat. Abilitare il repository di funzionalità aggiuntive eseguendo questo comando:

    ```console
    # subscription-manager repos --enable=rhel-6-server-extras-rpms
    ```

1. Installare l'agente Linux di Azure eseguendo il comando seguente:

    ```console
    # yum install WALinuxAgent

    # chkconfig waagent on
    ```

1. L'agente Linux di Azure può configurare automaticamente lo spazio di swapping usando il disco risorse locale collegato alla macchina virtuale dopo il provisioning della macchina virtuale in Azure. Si noti che il disco risorse locale è un disco temporaneo e potrebbe essere svuotato se viene effettuato il deprovisioning della macchina virtuale. Dopo aver installato l'agente Linux di Azure nel passaggio precedente, modificare i parametri seguenti in **/etc/waagent.conf** in modo appropriato:

    ```config-conf
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

1. Annullare la sottoscrizione (se necessario) eseguendo il comando seguente:

    ```console-conf
    # subscription-manager unregister
    ```

1. Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure:

    ```console
    # Note: if you are migrating a specific virtual machine and do not wish to create a generalized image,
    # skip the deprovision step
    # sudo rm -rf /var/lib/waagent/
    # sudo rm -f /var/log/waagent.log

    # waagent -force -deprovision+user
    # rm -f ~/.bash_history
    

    # export HISTSIZE=0

    # logout
    ```

1. Arrestare la macchina virtuale in KVM.

1. Convertire l'immagine qcow2 nel formato VHD.

    > [!NOTE]
    > Esiste un bug noto nelle versioni qemu-img > = 2.2.1 risultante in un disco rigido virtuale non formattato correttamente. Il problema è stato risolto in QEMU 2.6. Si consiglia di usare qemu-img 2.2.0 o versione precedente oppure di eseguire l'aggiornamento alla versione 2.6 o successiva. Riferimento: https://bugs.launchpad.net/qemu/+bug/1490611.
    >

    Convertire innanzitutto l'immagine in formato non elaborato:

    ```console
    # qemu-img convert -f qcow2 -O raw rhel-6.9.qcow2 rhel-6.9.raw
    ```

    Verificare che le dimensioni dell'immagine non elaborata siano pari a 1 MB. In caso contrario, arrotondare le dimensioni a 1 MB:

    ```console
    # MB=$((1024*1024))
    # size=$(qemu-img info -f raw --output json "rhel-6.9.raw" | \
      gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    # rounded_size=$((($size/$MB + 1)*$MB))
    # qemu-img resize rhel-6.9.raw $rounded_size
    ```

    Convertire il disco non formattato in un disco rigido virtuale a dimensione fissa:

    ```console
    # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.9.raw rhel-6.9.vhd
    ```

    Oppure, con qemu versione **2.6+** includere l'opzione `force_size`:

    ```console
    # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-6.9.raw rhel-6.9.vhd
    ```

        
### <a name="rhel-7-using-kvm"></a>RHEL 7 con KVM

1. Scaricare l'immagine KVM di RHEL 7 dal sito Web di Red Hat. Questa procedura usa come esempio RHEL 7.

1. Impostare una password radice.

    Generare una password crittografata e copiare l'output del comando:

    ```console
    # openssl passwd -1 changeme
    ```

    Impostare una password radice con guestfish:

    ```console
    # guestfish --rw -a <image-name>
    > <fs> run
    > <fs> list-filesystems
    > <fs> mount /dev/sda1 /
    > <fs> vi /etc/shadow
    > <fs> exit
    ```

   Modificare il secondo campo dell'utente radice da "!!" con la password crittografata.

1. Creare una macchina virtuale in KVM dall'immagine qcow2. Impostare il tipo di disco su **qcow2** e il modello del dispositivo di interfaccia di rete virtuale su **virtio**. Avviare quindi la macchina virtuale e accedere come utente ROOT.

1. Creare o modificare il file `/etc/sysconfig/network` e aggiungere il testo seguente:

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Creare o modificare il file `/etc/sysconfig/network-scripts/ifcfg-eth0` e aggiungere il testo seguente:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    PERSISTENT_DHCLIENT=yes
    NM_CONTROLLED=yes
    ```

1. Assicurarsi che il servizio di rete venga eseguito all'avvio attivando il comando seguente:

    ```console
    # sudo systemctl enable network
    ```

1. Registrare la propria sottoscrizione Red Hat per abilitare l’installazione dei pacchetti dall’archivio RHEL eseguendo il seguente comando:

    ```console
    # subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Modificare la riga di avvio del kernel nella configurazione GRUB per includere ulteriori parametri del kernel per Azure. Per eseguire questa configurazione, aprire `/etc/default/grub` in un editor di testo e modificare il parametro `GRUB_CMDLINE_LINUX`. Ad esempio:

    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Questo comando, inoltre, garantisce che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug. Il comando disattiva anche le nuove convenzioni di denominazione di RHEL 7 per le schede di interfaccia di rete. È consigliabile anche rimuovere i parametri seguenti:

    ```config-grub
    rhgb quiet crashkernel=auto
    ```

    L'avvio grafico e l'avvio silenzioso non sono utili in un ambiente cloud se tutti i log devono essere inviati alla porta seriale. È possibile mantenere l'opzione `crashkernel` configurata, se necessario. Si noti che questo parametro riduce la quantità di memoria disponibile nella macchina virtuale di almeno 128 MB e questo potrebbe causare problemi in macchine virtuali di dimensioni inferiori.

1. Dopo aver terminato di modificare `/etc/default/grub`, eseguire questo comando per ricompilare la configurazione GRUB:

    ```console
    # grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. Aggiungere i moduli Hyper-V in initramfs.

    Modificare `/etc/dracut.conf` e aggiungere il contenuto:

    ```config-conf
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    Ricompilare initramfs:

    ```config-conf
    # dracut -f -v
    ```

1. Disinstallare cloud-init:

    ```console
    # yum remove cloud-init
    ```

1. Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio:

    ```console
    # systemctl enable sshd
    ```

    Modificare /etc/ssh/sshd_config per includere le righe seguenti:

    ```config
    PasswordAuthentication yes
    ClientAliveInterval 180
    ```

1. È stato effettuato il push del pacchetto WALinuxAgent `WALinuxAgent-<version>` nel repository di funzionalità aggiuntive di Red Hat. Abilitare il repository di funzionalità aggiuntive eseguendo questo comando:

    ```config
    # subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

1. Installare l'agente Linux di Azure eseguendo il comando seguente:

    ```console
    # yum install WALinuxAgent
    ```

    Abilitare il servizio waagent:

    ```console
    # systemctl enable waagent.service
    ```

1. Installare cloud-init: seguire la procedura descritta in "preparare una macchina virtuale RHEL 7 dalla console di gestione di Hyper-V", passaggio 12, "installare cloud-init per gestire il provisioning".

1. Scambia configurazione 

    Non creare lo spazio di swapping sul disco del sistema operativo.
    Seguire i passaggi in "preparare una macchina virtuale RHEL 7 dalla console di gestione di Hyper-V", passaggio 13, "swap Configuration".


1. Annullare la sottoscrizione (se necessario) eseguendo il comando seguente:

    ```console
    # subscription-manager unregister
    ```


1. Effettuare il deprovisioning

    Seguire i passaggi in "preparare una macchina virtuale RHEL 7 dalla console di gestione di Hyper-V", passaggio 15, "deprovision"

1. Arrestare la macchina virtuale in KVM.

1. Convertire l'immagine qcow2 nel formato VHD.

    > [!NOTE]
    > Esiste un bug noto nelle versioni qemu-img > = 2.2.1 risultante in un disco rigido virtuale non formattato correttamente. Il problema è stato risolto in QEMU 2.6. Si consiglia di usare qemu-img 2.2.0 o versione precedente oppure di eseguire l'aggiornamento alla versione 2.6 o successiva. Riferimento: https://bugs.launchpad.net/qemu/+bug/1490611.
    >

    Convertire innanzitutto l'immagine in formato non elaborato:

    ```console
    # qemu-img convert -f qcow2 -O raw rhel-7.4.qcow2 rhel-7.4.raw
    ```

    Verificare che le dimensioni dell'immagine non elaborata siano pari a 1 MB. In caso contrario, arrotondare le dimensioni a 1 MB:

    ```console
    # MB=$((1024*1024))
    # size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
      gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    # rounded_size=$((($size/$MB + 1)*$MB))
    # qemu-img resize rhel-7.4.raw $rounded_size
    ```

    Convertire il disco non formattato in un disco rigido virtuale a dimensione fissa:

    ```console
    # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

    Oppure, con qemu versione **2.6+** includere l'opzione `force_size`:

    ```console
    # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

## <a name="vmware"></a>VMware

Questa sezione illustra come preparare una distribuzione [RHEL 6](#rhel-6-using-vmware) o [RHEL 7](#rhel-6-using-vmware)  da VMware.

### <a name="prerequisites"></a>Prerequisiti
In questa sezione si presuppone che una macchina virtuale RHEL sia già stata installata in VMware. Per informazioni dettagliate su come installare un sistema operativo in VMware, vedere la [guida all'installazione del sistema operativo guest VMware](https://partnerweb.vmware.com/GOSIG/home.html).

* Quando si installa il sistema operativo Linux è consigliabile usare partizioni standard anziché LVM, che è spesso l'impostazione predefinita per numerose installazioni. Questo consentirà di evitare conflitti di nome LVM con la macchina virtuale clonata, in particolare se fosse eventualmente necessario collegare un disco del sistema operativo a un'altra macchina virtuale per la risoluzione dei problemi. Se si preferisce, su dischi di dati si può usare LVM o RAID.
* Non configurare una partizione di swapping sul disco del sistema operativo. È possibile configurare l'agente Linux per poter creare un file di scambio sul disco temporaneo delle risorse. Altre informazioni su questo argomento sono disponibili nei passaggi riportati di seguito.
* Quando si crea il disco rigido virtuale, selezionare **Store virtual disk as a single file**(Archivia disco virtuale come singolo file).

### <a name="rhel-6-using-vmware"></a>RHEL 6 con VMware
1. In RHEL 6, NetworkManager può interferire con l'agente Linux di Azure. Disinstallare il pacchetto eseguendo questo comando:

    ```console
    # sudo rpm -e --nodeps NetworkManager
    ```

1. Creare nella directory /etc/sysconfig/ un file denominato **network** contenente il testo seguente:

    ```console
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Creare o modificare il file `/etc/sysconfig/network-scripts/ifcfg-eth0` e aggiungere il testo seguente:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

1. Spostare o eliminare le regole udev per evitare la generazione di regole statiche per l'interfaccia Ethernet. Le regole seguenti causano problemi quando si clona una macchina virtuale in Azure o Hyper-V:

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

1. Assicurarsi che il servizio di rete venga eseguito all'avvio attivando il comando seguente:

    ```console
    # sudo chkconfig network on
    ```

1. Registrare la propria sottoscrizione Red Hat per abilitare l'installazione dei pacchetti dall'archivio RHEL eseguendo questo comando:

    ```console
    # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. È stato effettuato il push del pacchetto WALinuxAgent `WALinuxAgent-<version>` nel repository di funzionalità aggiuntive di Red Hat. Abilitare il repository di funzionalità aggiuntive eseguendo questo comando:

    ```console
    # subscription-manager repos --enable=rhel-6-server-extras-rpms
    ```

1. Modificare la riga di avvio del kernel nella configurazione GRUB per includere ulteriori parametri del kernel per Azure. A tale scopo, aprire `/etc/default/grub` in un editor di testo e modificare il parametro `GRUB_CMDLINE_LINUX`. Ad esempio:

    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"
    ```

   In questo modo si garantisce inoltre che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug. È consigliabile anche rimuovere i parametri seguenti:

    ```config-grub
    rhgb quiet crashkernel=auto
    ```
   
    L'avvio grafico e l'avvio silenzioso non sono utili in un ambiente cloud se tutti i log devono essere inviati alla porta seriale. È possibile mantenere l'opzione `crashkernel` configurata, se necessario. Si noti che questo parametro riduce la quantità di memoria disponibile nella macchina virtuale di almeno 128 MB e questo potrebbe causare problemi in macchine virtuali di dimensioni inferiori.

1. Aggiungere i moduli Hyper-V a initramfs:

    Modificare `/etc/dracut.conf` e aggiungere il contenuto seguente:

    ```config-conf
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    Ricompilare initramfs:

    ```config-cong
    # dracut -f -v
    ```

1. Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio, che è in genere l'impostazione predefinita. Modificare `/etc/ssh/sshd_config` per poter includere la riga seguente:

    ```config
    ClientAliveInterval 180
    ```

1. Installare l'agente Linux di Azure eseguendo il comando seguente:

    ```console
    # sudo yum install WALinuxAgent

    # sudo chkconfig waagent on
    ```

1. Non creare lo spazio di swapping sul disco del sistema operativo.

    L'agente Linux di Azure può configurare automaticamente lo spazio di swapping usando il disco risorse locale collegato alla macchina virtuale dopo il provisioning della macchina virtuale in Azure. Si noti che il disco risorse locale è un disco temporaneo e potrebbe essere svuotato se viene effettuato il deprovisioning della macchina virtuale. Dopo aver installato l'agente Linux di Azure nel passaggio precedente, modificare nel modo appropriato i parametri seguenti in `/etc/waagent.conf`:

    ```config-conf
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

1. Annullare la sottoscrizione (se necessario) eseguendo il comando seguente:

    ```console
    # sudo subscription-manager unregister
    ```

1. Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure:

    ```console
    # Note: if you are migrating a specific virtual machine and do not wish to create a generalized image,
    # skip the deprovision step
    # sudo rm -rf /var/lib/waagent/
    # sudo rm -f /var/log/waagent.log

    # waagent -force -deprovision+user
    # rm -f ~/.bash_history
    

    # export HISTSIZE=0

    # logout
    ```

1. Arrestare la macchina virtuale e convertire il file VMDK in un file con estensione vhd.

    > [!NOTE]
    > Esiste un bug noto nelle versioni qemu-img > = 2.2.1 risultante in un disco rigido virtuale non formattato correttamente. Il problema è stato risolto in QEMU 2.6. Si consiglia di usare qemu-img 2.2.0 o versione precedente oppure di eseguire l'aggiornamento alla versione 2.6 o successiva. Riferimento: https://bugs.launchpad.net/qemu/+bug/1490611.
    >

    Convertire innanzitutto l'immagine in formato non elaborato:

    ```console
    # qemu-img convert -f vmdk -O raw rhel-6.9.vmdk rhel-6.9.raw
    ```

    Verificare che le dimensioni dell'immagine non elaborata siano pari a 1 MB. In caso contrario, arrotondare le dimensioni a 1 MB:

    ```console
    # MB=$((1024*1024))
    # size=$(qemu-img info -f raw --output json "rhel-6.9.raw" | \
      gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    # rounded_size=$((($size/$MB + 1)*$MB))
    # qemu-img resize rhel-6.9.raw $rounded_size
    ```

    Convertire il disco non formattato in un disco rigido virtuale a dimensione fissa:

    ```console
    # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.9.raw rhel-6.9.vhd
    ```

    Oppure, con qemu versione **2.6+** includere l'opzione `force_size`:

    ```console
    # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-6.9.raw rhel-6.9.vhd
    ```

### <a name="rhel-7-using-vmware"></a>RHEL 7 con VMware
1. Creare o modificare il file `/etc/sysconfig/network` e aggiungere il testo seguente:

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Creare o modificare il file `/etc/sysconfig/network-scripts/ifcfg-eth0` e aggiungere il testo seguente:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    PERSISTENT_DHCLIENT=yes
    NM_CONTROLLED=yes
    ```

1. Assicurarsi che il servizio di rete venga eseguito all'avvio attivando il comando seguente:

    ```console
    # sudo systemctl enable network
    ```

1. Registrare la propria sottoscrizione Red Hat per abilitare l'installazione dei pacchetti dall'archivio RHEL eseguendo questo comando:

    ```console
    # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Modificare la riga di avvio del kernel nella configurazione GRUB per includere ulteriori parametri del kernel per Azure. Per eseguire questa modifica, aprire `/etc/default/grub` in un editor di testo e modificare il parametro `GRUB_CMDLINE_LINUX`. Ad esempio:

    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Questa configurazione, inoltre, garantisce che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug. Disattiva anche le nuove convenzioni di denominazione RHEL 7 per le schede di interfaccia di rete. È consigliabile anche rimuovere i parametri seguenti:

    ```config-grub
    rhgb quiet crashkernel=auto
    ```

    L'avvio grafico e l'avvio silenzioso non sono utili in un ambiente cloud se tutti i log devono essere inviati alla porta seriale. È possibile mantenere l'opzione `crashkernel` configurata, se necessario. Si noti che questo parametro riduce la quantità di memoria disponibile nella macchina virtuale di almeno 128 MB e questo potrebbe causare problemi in macchine virtuali di dimensioni inferiori.

1. Dopo aver terminato di modificare `/etc/default/grub`, eseguire questo comando per ricompilare la configurazione GRUB:

    ```console
    # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. Aggiungere i moduli Hyper-V a initramfs.

    Modificare `/etc/dracut.conf`e aggiungere il contenuto:

    ```config-conf
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    Ricompilare initramfs:

    ```console
    # dracut -f -v
    ```

1. Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio. Questa è in genere l'impostazione predefinita. Modificare `/etc/ssh/sshd_config` per poter includere la riga seguente:

    ```config
    ClientAliveInterval 180
    ```

1. È stato effettuato il push del pacchetto WALinuxAgent `WALinuxAgent-<version>` nel repository di funzionalità aggiuntive di Red Hat. Abilitare il repository di funzionalità aggiuntive eseguendo questo comando:

    ```console
    # subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

1. Installare l'agente Linux di Azure eseguendo il comando seguente:

    ```console
    # sudo yum install WALinuxAgent

    # sudo systemctl enable waagent.service
    ```

1. Installare cloud-init

    Seguire i passaggi in "preparare una macchina virtuale RHEL 7 dalla console di gestione di Hyper-V", passaggio 12, "Install cloud-init per gestire il provisioning".

1. Scambia configurazione

    Non creare lo spazio di swapping sul disco del sistema operativo.
    Seguire i passaggi in "preparare una macchina virtuale RHEL 7 dalla console di gestione di Hyper-V", passaggio 13, "swap Configuration".

1. Se si vuole annullare la registrazione della sottoscrizione, eseguire il comando seguente:

    ```console
    # sudo subscription-manager unregister
    ```

1. Effettuare il deprovisioning

    Seguire i passaggi in "preparare una macchina virtuale RHEL 7 dalla console di gestione di Hyper-V", passaggio 15, "deprovision"


1. Arrestare la macchina virtuale e convertire il file VMDK nel formato VHD.

    > [!NOTE]
    > Esiste un bug noto nelle versioni qemu-img > = 2.2.1 risultante in un disco rigido virtuale non formattato correttamente. Il problema è stato risolto in QEMU 2.6. Si consiglia di usare qemu-img 2.2.0 o versione precedente oppure di eseguire l'aggiornamento alla versione 2.6 o successiva. Riferimento: https://bugs.launchpad.net/qemu/+bug/1490611.
    >

    Convertire innanzitutto l'immagine in formato non elaborato:

    ```console
    # qemu-img convert -f vmdk -O raw rhel-7.4.vmdk rhel-7.4.raw
    ```

    Verificare che le dimensioni dell'immagine non elaborata siano pari a 1 MB. In caso contrario, arrotondare le dimensioni a 1 MB:

    ```console
    # MB=$((1024*1024))
    # size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
      gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    # rounded_size=$((($size/$MB + 1)*$MB))
    # qemu-img resize rhel-7.4.raw $rounded_size
    ```

    Convertire il disco non formattato in un disco rigido virtuale a dimensione fissa:

    ```console
    # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

    Oppure, con qemu versione **2.6+** includere l'opzione `force_size`:

    ```console
    # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

## <a name="kickstart-file"></a>File kickstart

Questa sezione illustra come preparare una distribuzione RHEL 7 da un'immagine ISO usando un file kickstart.

### <a name="rhel-7-from-a-kickstart-file"></a>RHEL 7 da un file kickstart

1.  Creare e salvare un file kickstart con il contenuto seguente. Per informazioni dettagliate sull'installazione di kickstart, vedere la [guida all'installazione di kickstart](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html).

    ```text
    # Kickstart for provisioning a RHEL 7 Azure VM

    # System authorization information
      auth --enableshadow --passalgo=sha512

    # Use graphical install
    text

    # Do not run the Setup Agent on first boot
    firstboot --disable

    # Keyboard layouts
    keyboard --vckeymap=us --xlayouts='us'

    # System language
    lang en_US.UTF-8

    # Network information
    network  --bootproto=dhcp

    # Root password
    rootpw --plaintext "to_be_disabled"

    # System services
    services --enabled="sshd,waagent,NetworkManager"

    # System timezone
    timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

    # Partition clearing information
    clearpart --all --initlabel

    # Clear the MBR
    zerombr

    # Disk partitioning information
    part /boot --fstype="xfs" --size=500
    part / --fstyp="xfs" --size=1 --grow --asprimary

    # System bootloader configuration
    bootloader --location=mbr

    # Firewall configuration
    firewall --disabled

    # Enable SELinux
    selinux --enforcing

    # Don't configure X
    skipx

    # Power down the machine after install
    poweroff

    %packages
    @base
    @console-internet
    chrony
    sudo
    parted
    -dracut-config-rescue

    %end

    %post --log=/var/log/anaconda/post-install.log

    #!/bin/bash

    # Register Red Hat Subscription
    subscription-manager register --username=XXX --password=XXX --auto-attach --force

    # Install latest repo update
    yum update -y

    # Enable extras repo
    subscription-manager repos --enable=rhel-7-server-extras-rpms

    # Install WALinuxAgent
    yum install -y WALinuxAgent

    # Unregister Red Hat subscription
    subscription-manager unregister

    # Enable waaagent at boot-up
    systemctl enable waagent

    # Install cloud-init
    yum install -y cloud-init cloud-utils-growpart gdisk hyperv-daemons

    # Configure waagent for cloud-init
    sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
    sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf

    echo "Adding mounts and disk_setup to init stage"
    sed -i '/ - mounts/d' /etc/cloud/cloud.cfg
    sed -i '/ - disk_setup/d' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - mounts' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - disk_setup' /etc/cloud/cloud.cfg

    # Disable the root account
    usermod root -p '!!'

    # Disabke swap in WALinuxAgent
    ResourceDisk.Format=n
    ResourceDisk.EnableSwap=n

    # Configure swap using cloud-init
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

    # Set the cmdline
    sed -i 's/^\(GRUB_CMDLINE_LINUX\)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

    # Enable SSH keepalive
    sed -i 's/^#\(ClientAliveInterval\).*$/\1 180/g' /etc/ssh/sshd_config

    # Build the grub cfg
    grub2-mkconfig -o /boot/grub2/grub.cfg

    # Configure network
    cat << EOF > /etc/sysconfig/network-scripts/ifcfg-eth0
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    PERSISTENT_DHCLIENT=yes
    NM_CONTROLLED=yes
    EOF

    # Deprovision and prepare for Azure if you are creating a generalized image
    sudo cloud-init clean --logs --seed
    sudo rm -rf /var/lib/cloud/
    sudo rm -rf /var/lib/waagent/
    sudo rm -f /var/log/waagent.log

    sudo waagent -force -deprovision+user
    rm -f ~/.bash_history
    export HISTSIZE=0

    %end
    ```

1. Posizionare il file kickstart in un percorso in cui sia accessibile per il sistema di installazione.

1. Creare una nuova macchina virtuale nella console di gestione di Hyper-V. Nella pagina **Connessione disco rigido virtuale** selezionare **Connetti un disco rigido virtuale successivamente** e completare la creazione guidata della macchina virtuale.

1. Aprire le impostazioni della macchina virtuale:

    a.  Collegare un nuovo disco rigido virtuale alla macchina virtuale. Accertarsi di selezionare **Formato VHD** e **A dimensione fissa**.

    b.  Collegare l'ISO di installazione all'unità DVD.

    c.  Impostare il BIOS per l'avvio da CD.

1. Avviare la macchina virtuale. Quando viene visualizzata la guida all'installazione, premere **Tab** per configurare le opzioni di avvio.

1. Inserire `inst.ks=<the location of the kickstart file>` alla fine di opzioni di avvio e premere **Invio**.

1. Attendere la fine dell'installazione. Al termine, la macchina virtuale verrà arrestata automaticamente. Il file VHD Linux è ora pronto per il caricamento in Azure.

## <a name="known-issues"></a>Problemi noti
### <a name="the-hyper-v-driver-could-not-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>Non è possibile includere il driver Hyper-V nel disco RAM iniziale quando si usa un hypervisor non Hyper-V

In alcuni casi, i programmi di installazione di Linux potrebbero non includere i driver per Hyper-V nel disco RAM iniziale (initrd o initramfs), a meno che Linux non rilevi di essere in esecuzione in un ambiente Hyper-V.

Quando si usa un sistema di virtualizzazione diverso (ovvero VirtualBox, Xen e così via) per preparare l'immagine Linux, potrebbe essere necessario ricompilare initrd per assicurarsi che almeno i moduli kernel hv_vmbus e hv_storvsc siano disponibili sul disco RAM iniziale. Questo è un problema noto, almeno nei sistemi basati sulla distribuzione upstream di Red Hat.

Per risolvere questo problema, aggiungere i moduli Hyper-V a initramfs e ricompilarlo:

Modificare `/etc/dracut.conf` e aggiungere il contenuto seguente:

```config-conf
add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
```

Ricompilare initramfs:

```console
# dracut -f -v
```

Per altri dettagli, vedere le informazioni sulla [ricompilazione di initramfs](https://access.redhat.com/solutions/1958).

## <a name="next-steps"></a>Passaggi successivi
* È ora possibile usare il disco rigido virtuale Red Hat Enterprise Linux per creare nuove macchine virtuali in Azure. Se è la prima volta che si carica il file VHD in Azure, vedere [Creare una macchina virtuale Linux da un disco personalizzato](upload-vhd.md#option-1-upload-a-vhd).
* Per altre informazioni sugli hypervisor certificati per l'esecuzione di Red Hat Enterprise Linux, visitare [il sito Web di Red Hat](https://access.redhat.com/certified-hypervisors).
* Per altre informazioni sull'uso di immagini RHEL BYOS pronte per la produzione, vedere la pagina della documentazione relativa a [BYOS](../workloads/redhat/byos.md).
