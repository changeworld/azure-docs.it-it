---
title: Installare l Microsoft Azure di Servizi di ripristino di Microsoft (MARS)
description: Informazioni su come installare l'Microsoft Azure servizi di ripristino di emergenza (MARS) per eseguire il backup di computer Windows.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 3ea48aaa6aad4a51463c4c028ead22f31163f810
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519222"
---
# <a name="install-the-azure-backup-mars-agent"></a>Installare l'Backup di Azure MARS

Questo articolo illustra come installare l'agente Microsoft Azure Servizi di ripristino di emergenza (MARS). MARS è noto anche come agente Backup di Azure servizio.

## <a name="about-the-mars-agent"></a>Informazioni sull'agente di Servizi di ripristino di Microsoft Azure

Backup di Azure usa l'agente MARS per eseguire il backup di file, cartelle e stato del sistema da computer locali e macchine virtuali di Azure. Tali backup vengono archiviati in un insieme di credenziali di Servizi di ripristino in Azure. È possibile eseguire l'agente:

* Direttamente nei computer Windows locali. Questi computer possono eseguire il backup direttamente in un insieme di credenziali di Servizi di ripristino in Azure.
* Nelle macchine virtuali di Azure che eseguono Windows side-by-side con l'estensione di backup delle macchine virtuali di Azure. L'agente backup di cartelle e file specifici nella macchina virtuale.
* In un'istanza di Backup di Microsoft Azure Server (MABS) o in un server System Center Data Protection Manager (DPM). In questo scenario, i computer e i carichi di lavoro esere il backup in MABS o Data Protection Manager. Il server di Backup di Microsoft Data Protection Manager usa quindi l'agente MARS per eseguire il backup in un insieme di credenziali in Azure.

I dati disponibili per il backup dipendono dalla posizione in cui è installato l'agente.

> [!NOTE]
> In genere, è possibile eseguire il backup di una macchina virtuale di Azure usando Backup di Azure'estensione nella macchina virtuale. Questo metodo esegue il backup dell'intera macchina virtuale. Se si vuole eseguire il backup di cartelle e file specifici nella macchina virtuale, installare e usare l'agente MARS insieme all'estensione . Per altre informazioni, vedere [Architettura di un backup di macchine virtuali di Azure incorporato.](backup-architecture.md#architecture-built-in-azure-vm-backup)

![Passaggi del processo di backup](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>Prima di iniziare

* Informazioni su [Backup di Azure l'agente MARS per eseguire il backup di computer Windows.](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)
* Informazioni [sull'architettura di backup](backup-architecture.md#architecture-back-up-to-dpmmabs) che esegue l'agente MARS in un server maBS o Data Protection Manager secondario.
* Esaminare [le funzionalità supportate e gli elementi di cui è possibile eseguire il backup tramite](backup-support-matrix-mars-agent.md) l'agente MARS.
* Assicurarsi di avere un account Azure se è necessario eseguire il backup di un server o di un client in Azure. Se non si ha un account, è possibile crearne [uno](https://azure.microsoft.com/free/) gratuito in pochi minuti.
* Verificare l'accesso a Internet nei computer di cui si vuole eseguire il backup.
* Assicurarsi che l'utente che esegue l'installazione e la configurazione dell'agente MARS abbia privilegi di amministratore locale nel server da proteggere.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>Modificare la replica di archiviazione

Per impostazione predefinita, gli insiemi di credenziali usano l'[archiviazione con ridondanza geografica](../storage/common/storage-redundancy.md#geo-redundant-storage).

* Se l'insieme di credenziali è il meccanismo di backup primario, è consigliabile usare grS.
* È possibile usare [l'archiviazione con ridondanza locale per](../storage/common/storage-redundancy.md#locally-redundant-storage) ridurre i costi di archiviazione di Azure.

Per modificare il tipo di replica di archiviazione:

1. Nel nuovo insieme di credenziali selezionare **Proprietà** nella **sezione** Impostazioni.

1. Nella pagina **Proprietà** in **Configurazione backup selezionare** **Aggiorna**.

1. Selezionare il tipo di replica di archiviazione e selezionare **Salva**.

    ![Aggiornare la configurazione di backup](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> Non è possibile modificare il tipo di replica di archiviazione dopo che l'insieme di credenziali è stato configurato e contiene elementi di backup. Per eseguire questa operazione, è necessario creare nuovamente l'insieme di credenziali.
>

### <a name="verify-internet-access"></a>Verificare l'accesso a Internet

Se il computer ha accesso a Internet limitato, assicurarsi che le impostazioni del firewall nel computer o nel proxy consentano gli URL e gli indirizzi IP seguenti:

* URL
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* Indirizzi IP
  * 20.190.128.0/18
  * 40.126.0.0/18

### <a name="use-azure-expressroute"></a>Usare Azure ExpressRoute

È possibile eseguire il backup dei dati Azure ExpressRoute usando il peering pubblico (disponibile per i circuiti vecchi) e il peering Microsoft. Il backup su peering privato non è supportato.

Per usare il peering pubblico, assicurarsi prima di tutto l'accesso ai domini e agli indirizzi seguenti:

* `http://www.msftncsi.com/ncsi.txt`
* `http://www.msftconnecttest.com/connecttest.txt`
* `microsoft.com`
* `.WindowsAzure.com`
* `.microsoftonline.com`
* `.windows.net`
* Indirizzi IP
  * 20.190.128.0/18
  * 40.126.0.0/18

Per usare il peering Microsoft, selezionare i servizi, le aree e i valori della community pertinenti seguenti:

* Azure Active Directory (12076:5060)
* Area di Azure, in base alla posizione dell'insieme di credenziali di Servizi di ripristino
* Archiviazione di Azure, in base alla posizione dell'insieme di credenziali di Servizi di ripristino

Per altre informazioni, vedere Requisiti [di routing di ExpressRoute.](../expressroute/expressroute-routing.md)

> [!NOTE]
> Il peering pubblico è deprecato per i nuovi circuiti.

Tutti gli URL e gli indirizzi IP precedenti usano il protocollo HTTPS sulla porta 443.

### <a name="private-endpoints"></a>Endpoint privati

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

## <a name="download-the-mars-agent"></a>Scaricare l'agente MARS

Scaricare l'agente MARS in modo che sia possibile installarlo nei computer di cui si vuole eseguire il backup.

Se l'agente è già stato installato in qualsiasi computer, assicurarsi di eseguire la versione più recente dell'agente. Trovare la versione più recente nel portale o passare direttamente al [download](https://aka.ms/azurebackup_agent).

1. Nell'insieme di credenziali, **in Attività iniziali** selezionare **Backup.**

    ![Aprire l'obiettivo di backup](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

1. In **Posizione di esecuzione del carico di lavoro** selezionare **Locale.** Selezionare questa opzione anche se si vuole installare l'agente MARS in una macchina virtuale di Azure.
1. In **Elementi di cui eseguire il backup selezionare** File e **cartelle.** È anche possibile selezionare **Stato del sistema.** Sono disponibili molte altre opzioni, ma queste opzioni sono supportate solo se si esegue un server di backup secondario. Selezionare **Preparare l'infrastruttura.**

    ![Configurare file e cartelle](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

1. Per **Preparare l'infrastruttura**, in **Installare l'agente di Servizi di ripristino** scaricare l'agente MARS.

    ![Preparare l'infrastruttura](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

1. Nel menu di download selezionare **Salva**. Per impostazione predefinita, il file *MARSagentinstaller.exe* viene salvato nella cartella Downloads.

1. Selezionare **Già scaricare o usare l'agente di Servizi di ripristino più recente** e quindi scaricare le credenziali dell'insieme di credenziali.

    ![Scaricare le credenziali dell’insieme di credenziali](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

1. Selezionare **Salva**. Il file viene scaricato nella cartella Download. Non è possibile aprire il file delle credenziali dell'insieme di credenziali.

## <a name="install-and-register-the-agent"></a>Installare e registrare l'agente

1. Eseguire il *MARSagentinstaller.exe* file nei computer di cui si vuole eseguire il backup.
1. Nella finestra di dialogo Agente mars Installazione guidata selezionare **Impostazioni di installazione**. Scegliere dove installare l'agente e scegliere un percorso per la cache. Selezionare quindi **Avanti**.
   * Backup di Azure usa la cache per archiviare gli snapshot dei dati prima di inviarli ad Azure.
   * Il percorso della cache deve avere spazio disponibile pari ad almeno il 5% delle dimensioni dei dati di cui verrà creato il backup.

    ![Scegliere le impostazioni di installazione nell'agente MARS Installazione guidata](./media/backup-configure-vault/mars1.png)

1. Per **Configurazione proxy** specificare il modo in cui l'agente in esecuzione nel computer Windows si connetterà a Internet. Selezionare quindi **Avanti**.

   * Se si usa un proxy personalizzato, specificare le impostazioni proxy e le credenziali necessarie.
   * Tenere presente che l'agente deve accedere [a URL specifici](#before-you-start).

    ![Configurare l'accesso a Internet nella procedura guidata MARS](./media/backup-configure-vault/mars2.png)

1. Per **Installazione** esaminare i prerequisiti e selezionare **Installa.**
1. Dopo aver installato l'agente, selezionare **Proceed to Registration (Continua con la registrazione).**
1. In **Registrazione guidata dell'insieme**  >  **di** credenziali individuare e selezionare il file di credenziali scaricato. Selezionare quindi **Avanti**.

    ![Aggiungere le credenziali dell'insieme di credenziali usando la Registrazione guidata server](./media/backup-configure-vault/register1.png)

1. Nella pagina **Impostazione crittografia** specificare una passphrase che verrà usata per crittografare e decrittografare i backup per il computer. [Per altre](backup-azure-file-folder-backup-faq.yml#what-characters-are-allowed-for-the-passphrase-) informazioni sui caratteri passphrase consentiti, vedere qui.

    * Salvare la passphrase in un percorso sicuro. È necessario per ripristinare un backup.
    * Se si perde o si dimentica la passphrase, Microsoft non può aiutare a ripristinare i dati di backup.

1. Selezionare **Fine**. L'agente è ora installato e il computer è registrato nell'insieme di credenziali. Ora è possibile configurare e pianificare il backup.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [eseguire il backup di computer Windows usando l'Backup di Azure MARS](backup-windows-with-mars-agent.md)
