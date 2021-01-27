---
title: Creare e caricare un VHD Oracle Linux
description: Informazioni su come creare e caricare un disco rigido virtuale (VHD) di Azure che contiene un sistema operativo Oracle Linux.
author: danielsollondon
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 12/01/2020
ms.author: danis
ms.openlocfilehash: 34ebc126ba4a1ae725325f8d888899af2dd72a59
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98875669"
---
# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>Preparare una macchina virtuale Oracle Linux per Azure

In questo articolo si presuppone che l'utente abbia già installato un sistema operativo Oracle Linux in un disco rigido virtuale. Sono disponibili vari strumenti per creare file con estensione vhd, ad esempio una soluzione di virtualizzazione come Hyper-V. Per istruzioni, vedere [Installare il ruolo Hyper-V e configurare una macchina virtuale](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

## <a name="oracle-linux-installation-notes"></a>Note generali sull'installazione di Oracle Linux
* Vedere anche [Note generali sull'installazione di Linux](create-upload-generic.md#general-linux-installation-notes) per altri suggerimenti sulla preparazione di Linux per Azure.
* Hyper-V e il supporto di Azure Oracle Linux con il kernel UEK (Unbreakable Enterprise kernel) o con il kernel compatibile Red Hat.
* UEK2 di Oracle non è supportato su Hyper-V e Azure perché non include i driver necessari.
* Il formato VHDX non è supportato in Azure, solo nei **VHD fissi**.  È possibile convertire il disco in formato VHD tramite la console di gestione di Hyper-V o il cmdlet convert-vhd.
* **Per l'installazione di file system UDF è necessario il supporto di kernel.** Al primo avvio in Azure la configurazione del provisioning viene passata alla macchina virtuale Linux tramite supporti di memorizzazione formattati con UDF, collegati al guest. È necessario che l'agente Linux di Azure possa montare il file system UDF per leggere la relativa configurazione ed eseguire il provisioning della macchina virtuale.
* Durante l'installazione del sistema operativo Linux è consigliabile usare partizioni standard anziché LVM, che spesso è la scelta predefinita per numerose installazioni. In questo modo sarà possibile evitare conflitti di nome LVM con le macchine virtuali clonate, in particolare se fosse necessario collegare un disco del sistema operativo a un'altra macchina virtuale per la risoluzione dei problemi. Se si preferisce, su dischi di dati si può usare [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) o [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid).
* I kernel Linux con versione precedente a 2.6.37 non supportano NUMA in Hyper-V con macchine virtuali di dimensioni maggiori. Questo problema incide principalmente sulle distribuzioni precedenti che usavano il kernel upstream Red Hat 2.6.32 ed è stato risolto in Oracle Linux 6.6
* Non configurare una partizione swap nel disco del sistema operativo. Altre informazioni su questo argomento sono disponibili nei passaggi seguenti.
* Le dimensioni virtuali di tutti i dischi rigidi virtuali su Azure devono essere allineate a 1 MB. Quando si converte un disco non formattato in un disco rigido virtuale, prima della conversione è necessario assicurarsi che le dimensioni del disco non formattato siano un multiplo di 1 MB. Per altre informazioni, vedere [Note sull'installazione di Linux](create-upload-generic.md#general-linux-installation-notes).
* Verificare che il repository `Addons` sia abilitato. Modificare il file `/etc/yum.repos.d/public-yum-ol6.repo`(Oracle Linux 6) o `/etc/yum.repos.d/public-yum-ol7.repo`(Oracle Linux 7) e modificare la riga `enabled=0` in `enabled=1` in **[ol6_addons]** o **[ol7_addons]** in questo file.

## <a name="oracle-linux-64-and-later"></a>Oracle Linux 6.4 e successive
Per l'esecuzione della macchina virtuale in Azure è necessario eseguire specifici passaggi di configurazione nel sistema operativo.

1. Nel riquadro centrale della console di gestione di Hyper-V selezionare la macchina virtuale.
2. Fare clic su **Connect** per aprire la finestra della macchina virtuale.
3. Disinstallare NetworkManager attivando il seguente comando:

    ```console
    # sudo rpm -e --nodeps NetworkManager
    ```

    **Nota:** se il pacchetto non è già installato, questo comando avrà esito negativo e verrà visualizzato un messaggio di errore. Si tratta di un comportamento previsto.
4. Creare un file denominato **network** in the `/etc/sysconfig/` contenente il testo seguente:

    ```config   
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

5. Creare un file denominato **ifcfg-eth0** in the `/etc/sysconfig/network-scripts/` contenente il testo seguente:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

6. Modificare le regole udev per evitare la generazione di regole statiche per l'interfaccia Ethernet. Le regole seguenti possono provocare problemi quando si clona una macchina virtuale in Microsoft Azure o Hyper-V:

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

7. Assicurarsi che il servizio di rete venga eseguito all'avvio attivando il seguente comando:

    ```console
    # chkconfig network on
    ```

8. Installare python-pyasn1 eseguendo il comando seguente:

    ```console
    # sudo yum install python-pyasn1
    ```

9. Modificare la riga di avvio del kernel nella configurazione GRUB per includere ulteriori parametri del kernel per Azure. A questo scopo, aprire "/boot/grub/menu.lst" in un editor di testo e verificare che il kernel includa i parametri seguenti:

    ```config-grub
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

   In questo modo si garantisce che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug.
   
   Inoltre, è consigliabile *rimuovere* i parametri seguenti:

    ```config-grub
    rhgb quiet crashkernel=auto
    ```

   L'avvio grafico e l'avvio silenzioso non sono utili in un ambiente cloud se tutti i log devono essere inviati alla porta seriale.
   
   L'opzione `crashkernel` può essere configurata, ma si tenga presente che questo parametro ridurrà la quantità di memoria disponibile nella macchina virtuale di almeno 128 MB, il che potrebbe causare problemi con le macchine virtuali di dimensioni inferiori.
10. Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio.  Questo è in genere il valore predefinito.
11. Installare l'agente Linux di Azure eseguendo il comando seguente: La versione più recente è la 2.0.15.

    ```console
    # sudo yum install WALinuxAgent
    ```

    Si noti che, installando il pacchetto WALinuxAgent, i pacchetti NetworkManager e NetworkManager-gnome verranno rimossi, se l'operazione non è già stata eseguita come descritto nel passaggio 2.
12. Non creare l'area di swap sul disco del sistema operativo.
    
    L'agente Linux di Azure può configurare automaticamente l'area di swap utilizzando il disco risorse locale collegato alla VM dopo il provisioning in Azure. Si noti che il disco risorse locale è un disco *temporaneo* e potrebbe essere svuotato in seguito al deprovisioning della macchina virtuale. Dopo aver installato l'agente Linux di Azure come illustrato nel passaggio precedente, modificare i parametri seguenti in /etc/waagent.conf in modo appropriato:

    ```config-conf
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

13. Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure:

    ```console
    # sudo waagent -force -deprovision
    # export HISTSIZE=0
    # logout
    ```

14. Fare clic su **Azione -> Arresta** nella console di gestione di Hyper-V. Il file VHD Linux è ora pronto per il caricamento in Azure.

---
## <a name="oracle-linux-70-and-later"></a>Oracle Linux 7.0 e successive
**Modifiche in Oracle Linux 7**

La preparazione di una macchina virtuale Oracle Linux 7 per Azure è molto simile a Oracle Linux 6, tuttavia vi sono alcune importanti differenze da notare:

* Il supporto di Azure Oracle Linux con il kernel UEK (Unbreakable Enterprise kernel) o con il kernel compatibile Red Hat. È consigliabile Oracle Linux con UEK.
* Il pacchetto NetworkManager e l'agente Linux di Azure non sono più in conflitto. Questo pacchetto viene installato per impostazione predefinita ed è consigliabile non rimuoverlo.
* GRUB2 viene ora usato come bootloader predefinito, quindi la procedura per la modifica dei parametri kernel è cambiata (vedere di seguito).
* XFS è ora il file system predefinito. Se si vuole, è ancora possibile usare il file system ext4.

**Passaggi di configurazione**

1. Nella console di gestione di Hyper-V selezionare la macchina virtuale.
2. Fare clic su **Connetti** per aprire una finestra della console per la macchina virtuale.
3. Creare un file denominato **network** in the `/etc/sysconfig/` contenente il testo seguente:

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

4. Creare un file denominato **ifcfg-eth0** in the `/etc/sysconfig/network-scripts/` contenente il testo seguente:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

5. Modificare le regole udev per evitare la generazione di regole statiche per l'interfaccia Ethernet. Le regole seguenti possono provocare problemi quando si clona una macchina virtuale in Microsoft Azure o Hyper-V:

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    ```

6. Assicurarsi che il servizio di rete venga eseguito all'avvio attivando il seguente comando:

    ```console
    # sudo chkconfig network on
    ```

7. Installare il pacchetto python-pyasn1 eseguendo il comando seguente:

    ```console
    # sudo yum install python-pyasn1
    ```

8. Eseguire il comando seguente per cancellare i metadati yum correnti e installare eventuali aggiornamenti:

    ```console 
    # sudo yum clean all
    # sudo yum -y update
    ```

9. Modificare la riga di avvio del kernel nella configurazione GRUB per includere ulteriori parametri del kernel per Azure. A tale scopo, aprire "/etc/default/grub" in un editor di testo e modificare il parametro `GRUB_CMDLINE_LINUX` , ad esempio:

    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   In questo modo si garantisce inoltre che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug. Disattiva inoltre le convenzioni di denominazione per le schede di rete in Oracle Linux 7 con il kernel Enterprise Unbreakable. Inoltre, è consigliabile *rimuovere* i parametri seguenti:

    ```config-grub
       rhgb quiet crashkernel=auto
    ```
 
   L'avvio grafico e l'avvio silenzioso non sono utili in un ambiente cloud se tutti i log devono essere inviati alla porta seriale.
   
   L'opzione `crashkernel` può essere configurata, ma si tenga presente che questo parametro ridurrà la quantità di memoria disponibile nella macchina virtuale di almeno 128 MB, il che potrebbe causare problemi con le macchine virtuali di dimensioni inferiori.
10. Dopo aver terminato di modificare "/etc/default/grub" come sopra illustrato, eseguire il comando seguente per ricompilare la configurazione GRUB:

    ```console
    # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

11. Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio.  Questo è in genere il valore predefinito.
12. Installare l'agente Linux di Azure e le dipendenze:

    ```bash
    sudo yum install WALinuxAgent
    sudo systemctl enable waagent
    ```

13. Installare cloud-init per gestire il provisioning

    ```console
    yum install -y cloud-init cloud-utils-growpart gdisk hyperv-daemons

    # Configure waagent for cloud-init
    sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
    sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf


    echo "Adding mounts and disk_setup to init stage"
    sed -i '/ - mounts/d' /etc/cloud/cloud.cfg
    sed -i '/ - disk_setup/d' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - mounts' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - disk_setup' /etc/cloud/cloud.cfg

    echo "Allow only Azure datasource, disable fetching network setting via IMDS"
    cat > /etc/cloud/cloud.cfg.d/91-azure_datasource.cfg <<EOF
    datasource_list: [ Azure ]
    datasource:
    Azure:
        apply_network_config: False
    EOF

    if [[ -f /mnt/resource/swapfile ]]; then
    echo Removing swapfile - RHEL uses a swapfile by default
    swapoff /mnt/resource/swapfile
    rm /mnt/resource/swapfile -f
    fi

    echo "Add console log file"
    cat >> /etc/cloud/cloud.cfg.d/05_logging.cfg <<EOF

    # This tells cloud-init to redirect its stdout and stderr to
    # 'tee -a /var/log/cloud-init-output.log' so the user can see output
    # there without needing to look on the console.
    output: {all: '| tee -a /var/log/cloud-init-output.log'}
    EOF

    ```

14. La configurazione dello scambio non crea spazio di swapping sul disco del sistema operativo.

    In precedenza, l'agente Linux di Azure è stato usato per configurare automaticamente lo spazio di swapping usando il disco risorse locale collegato alla macchina virtuale dopo il provisioning della macchina virtuale in Azure. Tuttavia, questo è ora gestito da cloud-init, **non è necessario** usare l'agente Linux per formattare il disco risorse creare il file di scambio, modificare i parametri seguenti in `/etc/waagent.conf` modo appropriato:

    ```console
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
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


15. Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure:

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

16. Fare clic su **Azione -> Arresta** nella console di gestione di Hyper-V. Il file VHD Linux è ora pronto per il caricamento in Azure.

## <a name="next-steps"></a>Passaggi successivi
È ora possibile usare il file con estensione vhd Oracle Linux per creare nuove macchine virtuali in Azure. Se è la prima volta che si carica il file VHD in Azure, vedere [Creare una macchina virtuale Linux da un disco personalizzato](upload-vhd.md#option-1-upload-a-vhd).