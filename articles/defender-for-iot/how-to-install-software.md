---
title: Defender per l'installazione di Internet.
description: Informazioni su come installare un sensore e la console di gestione locale per Azure Defender per l'it.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/2/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 175f006adbc18d21e0f0c5d859737aedb00bc457
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103574663"
---
# <a name="defender-for-iot-installation"></a>Defender per l'installazione di Internet.

Questo articolo descrive come installare gli elementi seguenti di Azure Defender per l'it:

- **Sensore**: il Defender per i sensori di Internet delle cose raccoglie il traffico di rete ICS usando il monitoraggio passivo (senza agenti). Passiva e non intrusiva, i sensori hanno un impatto zero sulle reti e sui dispositivi OT e Internet. Il sensore si connette a una porta SPAN o a un TAP di rete e inizia immediatamente a monitorare la rete. I rilevamenti vengono visualizzati nella console del sensore. Qui è possibile visualizzarli, analizzarli e analizzarli in una mappa di rete, un inventario dei dispositivi e un'ampia gamma di report. Gli esempi includono report di valutazione dei rischi, query data mining e vettori di attacco. Per altre informazioni sulle funzionalità dei sensori, vedere la [Guida dell'utente del sensore Defender for Internet (download diretto)](./getting-started.md).

- **Console di gestione locale**: la console di gestione locale consente di eseguire la gestione dei dispositivi, la gestione dei rischi e la gestione delle vulnerabilità. È anche possibile usarlo per eseguire il monitoraggio delle minacce e la risposta agli eventi imprevisti nell'azienda. Fornisce una visualizzazione unificata di tutti i dispositivi di rete, delle chiavi e degli indicatori di rischio OT e degli avvisi rilevati nelle strutture in cui vengono distribuiti i sensori. Usare la console di gestione locale per visualizzare e gestire i sensori nelle reti gapped.

In questo articolo vengono illustrate le seguenti informazioni sull'installazione:

  - **Hardware:** Dettagli del dispositivo fisico dell e HPE.

  - **Software:** Installazione del software della console di gestione locale e del sensore.

  - **Appliance virtuali:** Dettagli della macchina virtuale e installazione del software.

Dopo l'installazione, connettere il sensore alla rete.

## <a name="about-defender-for-iot-appliances"></a>Informazioni su Defender per Appliances 

Le sezioni seguenti forniscono informazioni su Defender per appliance del sensore Internet e l'appliance per il Defender per la console di gestione locale.

### <a name="physical-appliances"></a>Appliance fisiche

Il sensore del dispositivo di protezione per la gestione delle cose si connette a una porta SPAN o a un TAP di rete e inizia immediatamente a raccogliere il traffico di rete ICS usando il monitoraggio passivo (senza agenti). Questo processo ha un effetto zero su reti e dispositivi OT perché non è inserito nel percorso dati e non esegue l'analisi attiva dei dispositivi OT.

Sono disponibili le appliance di montaggio rack seguenti:

| **Tipo di distribuzione** | **Aziendale** | **Funzionalità per le aziende** | **SMB** | **Linea** |
|--|--|--|--|--|
| **Modello** | DL360 ProLiant HPE | Dell PowerEdge R340 supplementare XL | DL20 ProLiant HPE | DL20 ProLiant HPE |
| **Monitoraggio di porte** | fino a 15 RJ45 o 8 OPT | fino a 9 RJ45 o 6 OPT | fino a 8 RJ45 o 6 OPT | 4 RJ45 |
| **Larghezza di banda massima\*** | 3 GB/sec | 1 GB/sec | 1 GB/sec | 100 MB/sec |
| **Numero massimo di dispositivi protetti** | 30.000 | 10,000 | 15.000 | 1\.000 |

* La capacità massima della larghezza di banda può variare a seconda della distribuzione del protocollo.

### <a name="virtual-appliances"></a>Dispositivo virtuale

Sono disponibili le appliance virtuali seguenti:

| **Tipo di distribuzione** | **Funzionalità per le aziende** | **SMB** | **Linea** |
|--|--|--|--|
| **Descrizione** | Appliance virtuale per le distribuzioni aziendali | Appliance virtuale per distribuzioni SMB | Appliance virtuale per le distribuzioni linea |
| **Larghezza di banda massima\*** | 150 MB/sec | 15 MB/sec | 3 MB/sec |
| **Numero massimo di dispositivi protetti** | 3,000 | 300 | 100 |
| **Tipo di distribuzione** | Enterprise | SMB | Linea |
| **Descrizione** | Appliance virtuale per le distribuzioni aziendali | Appliance virtuale per distribuzioni SMB | Appliance virtuale per le distribuzioni linea |

* La capacità massima della larghezza di banda può variare a seconda della distribuzione del protocollo.

### <a name="hardware-specifications-for-the-on-premises-management-console"></a>Specifiche hardware per la console di gestione locale

 | Elemento | Descrizione |
 |----|--|
 **Descrizione** | In un'architettura a più livelli la console di gestione locale offre visibilità e controllo tra siti distribuiti geograficamente. Si integra con gli stack di sicurezza SOC, tra cui SIEM, sistemi di ticket, firewall di nuova generazione, piattaforme di accesso remoto sicure e il Defender per la sandbox di malware per Internet delle cose. |
 **Tipo di distribuzione** | Enterprise |
 **Tipo di dispositivo**  | Dell R340 supplementare, VM |
 **Numero di sensori gestiti** | Nessuna limitazione |

## <a name="prepare-for-the-installation"></a>Preparare l'installazione

### <a name="access-the-iso-installation-image"></a>Accedere all'immagine di installazione ISO

L'immagine di installazione è accessibile da Defender per il portale.

Per accedere al file:

1. Accedere all'account Defender per il proprio account.

1. Passare alla pagina **sensore di rete** o **console di gestione locale** e selezionare una versione da scaricare.

### <a name="install-from-dvd"></a>Installare da DVD

Prima dell'installazione, verificare di avere:

- Unità DVD portatile con connettore USB.

- Immagine del programma di installazione ISO.

Per effettuare l'installazione:

1. Masterizzare l'immagine in un DVD o preparare un disco su una chiave. Connettere un'unità DVD portatile al computer, fare clic con il pulsante destro del mouse sull'immagine ISO e selezionare **Burn su disco**.

1. Connettere il DVD o il disco su una chiave e configurare l'appliance per l'avvio da DVD o disco su una chiave.

### <a name="install-from-disk-on-a-key"></a>Installare da disco su una chiave

Prima dell'installazione, verificare di avere:

  - Rufus installato.
  
  - Un disco su chiave con USB versione 3,0 e versioni successive. La dimensione minima è 4 GB.

  - Un file di immagine del programma di installazione ISO.

Il disco su una chiave verrà cancellato in questo processo.

Per preparare un disco su una chiave:

1. Eseguire Rufus e selezionare **Sensor ISO**.

1. Connettere il disco su una chiave al pannello anteriore.

1. Impostare il BIOS del server per l'avvio dal dispositivo USB.

## <a name="dell-poweredger340xl-installation"></a>Installazione dell PowerEdgeR340XL

Prima di installare il software nell'appliance dell, è necessario modificare la configurazione del BIOS dell'appliance:

  - [Dell PowerEdge R340 supplementare Front Panel](#dell-poweredge-r340-front-panel) e [Dell PowerEdge R340 supplementare back panel](#dell-poweredge-r340-back-panel) contiene la descrizione dei pannelli anteriore e posteriore, insieme alle informazioni necessarie per l'installazione, ad esempio i driver e le porte.

  - La [configurazione del BIOS Dell](#dell-bios-configuration) fornisce informazioni su come connettersi all'interfaccia di gestione Appliance dell e configurare il BIOS.

  - L' [installazione del software (dell R340 supplementare)](#software-installation-dell-r340) descrive la procedura necessaria per installare Defender per il software per i sensori.

### <a name="dell-poweredge-r340xl-requirements"></a>Requisiti di Dell PowerEdge R340XL 

Per installare l'appliance Dell PowerEdge R340XL, è necessario quanto segue:

- Licenza Enterprise per il controller di accesso remoto Dell (iDrac)

- XML di configurazione BIOS

- Versioni del firmware del server:

  - Versione BIOS 2.1.6

  - iDrac versione 3.23.23.23

### <a name="dell-poweredge-r340-front-panel"></a>Pannello anteriore R340 supplementare Dell PowerEdge

:::image type="content" source="media/tutorial-install-components/view-of-dell-poweredge-r340-front-panel.jpg" alt-text="Pannello anteriore del R340 supplementare Dell PowerEdge.":::

 1. Pannello di controllo a sinistra 
 1. Unità ottica (facoltativo) 
 1. Pannello di controllo destro 
 1. Tag informazioni 
 1. Unità  

### <a name="dell-poweredge-r340-back-panel"></a>Pannello indietro R340 supplementare Dell PowerEdge

:::image type="content" source="media/tutorial-install-components/view-of-dell-poweredge-r340-back-panel.jpg" alt-text="Pannello indietro R340 supplementare Dell PowerEdge.":::

1. Porta seriale 
1. Porta NIC (GB 1) 
1. Porta NIC (GB 1) 
1. PCIe a metà altezza 
1. Slot della scheda di espansione PCIe a altezza intera 
1. Unità alimentatore 1 
1. Unità alimentatore 2 
1. Identificazione sistema 
1. Pulsante della porta del cavo indicatore di stato del sistema (CMA) 
1. Porta USB 3,0 (2) 
1. porta di rete dedicata iDRAC9 
1. Porta VGA 

### <a name="dell-bios-configuration"></a>Configurazione BIOS dell

La configurazione del BIOS Dell è necessaria per regolare l'appliance dell in modo che funzioni con il software.

La configurazione del BIOS viene eseguita tramite una configurazione predefinita. Il file è accessibile dal [centro di supporto](https://help.cyberx-labs.com/).

Importare il file di configurazione nell'appliance dell. Prima di usare il file di configurazione, è necessario stabilire la comunicazione tra l'appliance dell e il computer di gestione.

Il dispositivo Dell è gestito da un iDRAC integrato con Lifecycle Controller (LC). La LC è incorporata in ogni server Dell PowerEdge e fornisce funzionalità che consentono di distribuire, aggiornare, monitorare e gestire le appliance Dell PowerEdge.

Per stabilire la comunicazione tra l'appliance dell e il computer di gestione, è necessario definire l'indirizzo IP iDRAC e l'indirizzo IP del computer di gestione nella stessa subnet.

Quando viene stabilita la connessione, il BIOS è configurabile.

Per configurare il BIOS Dell:

1. [Configurare l'indirizzo IP di iDRAC](#configure-idrac-ip-address)

1. [Importare il file di configurazione del BIOS](#import-the-bios-configuration-file)

#### <a name="configure-idrac-ip-address"></a>Configurare l'indirizzo IP di iDRAC

1. Accendere il sensore.

1. Se il sistema operativo è già installato, selezionare il tasto F2 per accedere alla configurazione del BIOS.

1. Selezionare **le impostazioni di Idrac**.

1. Selezionare **rete**.

   > [!NOTE]
   > Durante l'installazione, è necessario configurare l'indirizzo IP e la password iDRAC predefiniti indicati nei passaggi seguenti. Dopo l'installazione, è possibile modificare queste definizioni.

1. Modificare l'indirizzo IPv4 statico in **10.100.100.250**.

1. Modificare il subnet mask statico in **255.255.255.0**.

   :::image type="content" source="media/tutorial-install-components/idrac-network-settings-screen-v2.png" alt-text="Screenshot che mostra la subnet mask statica.":::

1. Selezionare **back**  >  **Finish (fine**).

#### <a name="import-the-bios-configuration-file"></a>Importare il file di configurazione del BIOS

Questo articolo descrive come configurare il BIOS usando il file di configurazione.

1. Collegare un PC con un indirizzo IP preconfigurato statico **10.100.100.200** alla porta **Idrac** .

   :::image type="content" source="media/tutorial-install-components/idrac-port.png" alt-text="Screenshot della porta dell'indirizzo IP preconfigurato.":::

1. Aprire un browser e immettere **10.100.100.250** per connettersi all'interfaccia Web di Idrac.

1. Accedere con i privilegi di amministratore predefinito di Dell:

   - Nome utente: **radice**

   - Password: **Calvin**

1. Le credenziali dell'appliance sono:

   - Nome utente: **xxx**

   - Password: **xxx**

     L'operazione import server profile è stata avviata.

     > [!NOTE]
     > Prima di importare il file, assicurarsi che:
     > - Si è l'unico utente attualmente connesso a iDRAC.
     > - Il sistema non si trova nel menu BIOS.

1. Passare al   >  **profilo di configurazione del server** di configurazione. Impostare i parametri seguenti:

   :::image type="content" source="media/tutorial-install-components/configuration-screen.png" alt-text="Screenshot che mostra la configurazione del profilo del server.":::

   | Parametro | Configurazione |
   |--|--|
   | Tipo percorso | Selezionare **local (locale**). |
   | Percorso file | Selezionare **Scegli file** e aggiungere il file XML di configurazione. |
   | Importa componenti | Selezionare **BIOS, NIC, RAID**. |
   | Tempo massimo di attesa | Selezionare **20 minuti**. |

1. Selezionare **Importa**.

1. Per monitorare il processo, passare a **manutenzione**  >  **coda processi**.

   :::image type="content" source="media/tutorial-install-components/view-the-job-queue.png" alt-text="Screenshot che mostra la coda di processi.":::

#### <a name="manually-configuring-bios"></a>Configurazione manuale del BIOS 

È necessario configurare manualmente il BIOS dell'appliance se:

- L'appliance non è stata acquistata da Arrow.

- Si dispone di un dispositivo, ma non si ha accesso al file di configurazione XML.

Dopo aver eseguito l'accesso al BIOS, passare a **Impostazioni dispositivo**.

Per configurare manualmente:

1. Accedere al BIOS dell'appliance direttamente usando una tastiera e una schermata oppure usare iDRAC.

   - Se il dispositivo non è un Defender per l'appliance Internet, aprire un browser e passare all'indirizzo IP configurato in precedenza. Accedere con i privilegi di amministratore predefinito dell. Utilizzare la **radice** per il nome utente e **Calvin** per la password.

   - Se l'appliance è un Defender per l'appliance per le cose, accedere usando **xxx** come nome utente e **xxx** per la password.

1. Dopo aver eseguito l'accesso al BIOS, passare a **Impostazioni dispositivo**.

1. Scegliere la configurazione controllata da RAID selezionando **Integrated RAID Controller 1: Dell PERC \<PERC H330 Adapter\> Configuration Utility**.

1. Selezionare **Gestione configurazione**.

1. Selezionare **Crea disco virtuale**.

1. Nel campo **Seleziona livello RAID** selezionare **RAID5**. Nel campo **nome disco virtuale** immettere **radice** e selezionare **dischi fisici**.

1. Selezionare **Controlla tutto** , quindi selezionare **Applica modifiche**

1. Selezionare **OK**.

1. Scorrere verso il basso e selezionare **Crea disco virtuale**.

1. Selezionare la casella di controllo **conferma** e selezionare **Sì**.

1. Selezionare **OK**.

1. Tornare alla schermata principale e selezionare **BIOS di sistema**.

1. Selezionare **impostazioni di avvio**.

1. Per l'opzione **modalità di avvio** selezionare **BIOS**.

1. Selezionare **indietro**, quindi fare clic su **fine** per uscire dalle impostazioni del BIOS.

### <a name="software-installation-dell-r340"></a>Installazione software (dell R340 supplementare)

Il processo di installazione richiede circa 20 minuti. Al termine dell'installazione, il sistema viene riavviato più volte.

Per effettuare l'installazione:

1. Verificare che il supporto di versione sia montato nell'appliance in uno dei modi seguenti:

   - Connettere il CD o il disco esterno su una chiave con la versione.

   - Montare l'immagine ISO usando iDRAC. Dopo aver eseguito l'accesso a iDRAC, selezionare la console virtuale e quindi selezionare **supporto virtuale**.

1. Nella sezione **Map CD/DVD** selezionare **Choose file**.

1. Scegliere il file di immagine ISO della versione per questa versione dalla finestra di dialogo visualizzata.

1. Selezionare il pulsante **mappa dispositivo** .

   :::image type="content" source="media/tutorial-install-components/mapped-device-on-virtual-media-screen-v2.png" alt-text="Screenshot che mostra un dispositivo mappato.":::

1. Il supporto viene montato. Selezionare **Chiudi**.

1. Avviare l'appliance. Quando si usa iDRAC, è possibile riavviare i server selezionando il pulsante di **controllo console** . Quindi, sulle **macro della tastiera**, selezionare il pulsante **applica** , che consente di avviare la sequenza di tasti CTRL + ALT + CANC.

1. Selezionare **inglese**.

1. Selezionare **Sensor-Release- \<version\> Enterprise**.

   :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Screenshot che mostra la selezione della versione.":::   

1. Definire il profilo dell'appliance e le proprietà di rete:

   :::image type="content" source="media/tutorial-install-components/appliance-profile-screen-v2.png" alt-text="Screenshot che mostra il profilo dell'appliance.":::   

   | Parametro | Configurazione |
   |--|--|
   | **Profilo hardware** | **Enterprise** |
   | **Interfaccia di gestione** | **eno1** |
   | **Parametri di rete (forniti dal cliente)** | - |
   |**Indirizzo IP della rete di gestione:** | - |
   | **subnet mask:** | - |
   | **nome host dell'appliance:** | - |
   | **DNS** | - |
   | **Indirizzo IP gateway predefinito:** | - |
   | **interfacce di input:** |  Il sistema genera automaticamente l'elenco delle interfacce di input. Per eseguire il mirroring delle interfacce di input, copiare tutti gli elementi presentati nell'elenco con un separatore virgola. Non è necessario configurare l'interfaccia Bridge. Questa opzione viene utilizzata solo per i casi di utilizzo speciali. |

1. Dopo circa 10 minuti, verranno visualizzati i due set di credenziali. Uno per un utente di **CyberX** e uno per un utente di **supporto** .  

1. Salvare l'ID e le password del dispositivo. Queste credenziali sono necessarie per accedere alla piattaforma la prima volta che viene usata.

1. Premere **invio** per continuare.

## <a name="hpe-proliant-dl20-installation"></a>Installazione di HPE ProLiant DL20

Questo articolo descrive il processo di installazione di HPE ProLiant DL20, che include i passaggi seguenti:

  - Abilitare l'accesso remoto e aggiornare la password di amministratore predefinita.
  - Configurare le impostazioni del BIOS e del RAID.
  - Installare il software.

### <a name="about-the-installation"></a>Informazioni sull'installazione

  - È possibile installare Appliance Enterprise e SMB. Il processo di installazione è identico per entrambi i tipi di appliance, ad eccezione della configurazione dell'array.
  - Viene fornito un utente amministratore predefinito. Si consiglia di modificare la password durante il processo di configurazione della rete.
  - Durante il processo di configurazione della rete, la porta iLO viene configurata sulla porta di rete 1.
  - Il processo di installazione richiede circa 20 minuti. Al termine dell'installazione, il sistema viene riavviato più volte.

### <a name="hpe-proliant-dl20-front-panel"></a>Pannello anteriore HPE ProLiant DL20

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl20-front-panel-v2.png" alt-text="Pannello anteriore HPE ProLiant DL20.":::

### <a name="hpe-proliant-dl20-back-panel"></a>Pannello indietro HPE ProLiant DL20

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl20-back-panel-v2.png" alt-text="Il pannello indietro di HPE ProLiant DL20.":::

### <a name="enable-remote-access-and-update-the-password"></a>Abilitare l'accesso remoto e aggiornare la password

Utilizzare la procedura seguente per configurare le opzioni di rete e aggiornare la password predefinita.

Per abilitare e aggiornare la password:

1. Connettere una schermata e una tastiera al dispositivo HP, accendere il dispositivo e premere **F9**.

    :::image type="content" source="media/tutorial-install-components/hpe-proliant-screen-v2.png" alt-text="Screenshot che mostra la finestra di HPE ProLiant.":::

1. Passare a **utilità di sistema**  >  **configurazione del sistema**  >  **ILO 5 configurazione utility**  >  **Opzioni di rete**.

    :::image type="content" source="media/tutorial-install-components/system-configuration-window-v2.png" alt-text="Screenshot che mostra la finestra configurazione sistema.":::

    1.  Selezionare **porta di rete condivisa-Lom** dal campo **scheda interfaccia di rete** .
    
    1.  Disabilitare DHCP.
    
    1.  Immettere l'indirizzo IP, il subnet mask e l'indirizzo IP del gateway.

1. Selezionare **F10: Salva**.

1. Selezionare **ESC** per tornare all'utilità di **configurazione ILO 5**, quindi selezionare **Gestione utenti**.

1. Selezionare **Modifica/Rimuovi utente**. L'amministratore è l'unico utente predefinito definito. 

1. Modificare la password predefinita e selezionare **F10: Save**.

### <a name="configure-the-hpe-bios"></a>Configurare il BIOS HPE

Nella procedura seguente viene descritto come configurare il BIOS HPE per le appliance Enterprise e SMB.

Per configurare il BIOS HPE:

1. Selezionare **System Utilities**  >  **System Configuration**  >  **BIOS/Platform Configuration (RBSU)**.

1. Nel modulo **BIOS/Platform Configuration (RBSU)** selezionare opzioni di **avvio**.

1. Modificare la **modalità di avvio** in **modalità BIOS legacy**, quindi selezionare **F10: Save**.

1. Selezionare **ESC** due volte per chiudere il modulo **configurazione di sistema** .

#### <a name="for-the-enterprise-appliance"></a>Per Enterprise Appliance

1. Selezionare **Embedded RAID 1: HPE Smart Array P408i-a SR gen 10**  >  **Array Configuration**  >  **Create Array**.

1. Nel modulo **Crea matrice** selezionare tutte le opzioni. Sono disponibili tre opzioni per **Enterprise** Appliance.

#### <a name="for-the-smb-appliance"></a>Per l'appliance SMB

1. Selezionare **Embedded RAID 1: HPE Smart Array P208i-a SR gen 10**  >  **Array Configuration**  >  **Create Array**.

1. Selezionare **Vai al modulo successivo**.

1. Nel modulo **set RAID level** impostare il livello su **RAID 5** per le distribuzioni Enterprise e **RAID 1** per le distribuzioni SMB.

1. Selezionare **Vai al modulo successivo**.

1. Nel modulo **etichetta unità logica** immettere **unità logica 1**.

1. Selezionare **Invia modifiche**.

1. Nel modulo **Invia** selezionare **Torna al menu principale**.

1. Selezionare **F10: Salva** e quindi premere **ESC** due volte.

1. Nella finestra **utilità di sistema** selezionare **un menu di avvio** monouso.

1. Nel modulo del **menu di avvio** monouso selezionare **BIOS legacy One-Time menu di avvio**.

1. Verranno visualizzate le finestre **avvio in Legacy** e **sostituzione di avvio** . Scegliere un'opzione di sostituzione dell'avvio; ad esempio, in una shell CD-ROM, USB, HDD o UEFI.

    :::image type="content" source="media/tutorial-install-components/boot-override-window-one-v2.png" alt-text="Screenshot che mostra la prima finestra di sostituzione dell'avvio.":::

    :::image type="content" source="media/tutorial-install-components/boot-override-window-two-v2.png" alt-text="Screenshot che mostra la seconda finestra di sostituzione dell'avvio.":::
### <a name="software-installation-hpe-proliant-dl20-appliance"></a>Installazione software (appliance HPE ProLiant DL20)

Il processo di installazione richiede circa 20 minuti. Al termine dell'installazione, il sistema viene riavviato più volte.

Per installare il software:

1. Connettere lo schermo e la tastiera all'appliance, quindi connettersi all'interfaccia della riga di comando.

1. Connettere un CD o un disco esterno alla chiave con l'immagine ISO scaricata dalla pagina **aggiornamenti** nel portale Defender for Internet.

1. Avviare l'appliance.

1. Selezionare **inglese**.

    :::image type="content" source="media/tutorial-install-components/select-english-screen.png" alt-text="Selezione della lingua inglese nella finestra dell'interfaccia della riga di comando.":::

1. Selezionare **Sensor-Release- <version> Enterprise**.

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Screenshot della schermata per la selezione di una versione.":::

1. Nell'installazione guidata, definire il profilo dell'appliance e le proprietà di rete:

    :::image type="content" source="media/tutorial-install-components/installation-wizard-screen-v2.png" alt-text="Screenshot che mostra l'installazione guidata.":::

    | Parametro | Configurazione |
    | ----------| ------------- |
    | **Profilo hardware** | Selezionare **Enterprise** o **Office** per le distribuzioni SMB. |
    | **Interfaccia di gestione** | **eno2** |
    | **Parametri di rete predefiniti (in genere i parametri vengono forniti dal cliente)** | **Indirizzo IP della rete di gestione:** <br/> <br/>**nome host dell'appliance:** <br/>**DNS** <br/>**Indirizzo IP del gateway predefinito:**|
    | **interfacce di input:** | Il sistema genera automaticamente l'elenco delle interfacce di input.<br/><br/>Per eseguire il mirroring delle interfacce di input, copiare tutti gli elementi presentati nell'elenco con un separatore virgola: **eno5, ENO3, eno1, eno6, eno4**<br/><br/>**Per HPE DL20: non elencare eno1, enp1s0f4u4 (interfacce iLo)**<br/><br/>**Bridge**: non è necessario configurare l'interfaccia Bridge. Questa opzione viene utilizzata solo per i casi di utilizzo speciali. Premere **INVIO** per continuare. |

1. Dopo circa 10 minuti, verranno visualizzati i due set di credenziali. Uno per un utente di **CyberX** e uno per un utente di **supporto** .

1. Salvare l'ID e le password dell'appliance. Sono necessarie le credenziali per accedere alla piattaforma per la prima volta.

1. Premere **invio** per continuare.

## <a name="hpe-proliant-dl360-installation"></a>Installazione di HPE ProLiant DL360

  - Viene fornito un utente amministratore predefinito. Si consiglia di modificare la password durante la configurazione di rete.

  - Durante la configurazione di rete, verrà configurata la porta iLO.

  - Il processo di installazione richiede circa 20 minuti. Al termine dell'installazione, il sistema viene riavviato più volte.

### <a name="hpe-proliant-dl360-front-panel"></a>Pannello anteriore HPE ProLiant DL360

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl360-front-panel.png" alt-text="Pannello anteriore HPE ProLiant DL360.":::

### <a name="hpe-proliant-dl360-back-panel"></a>Pannello indietro HPE ProLiant DL360

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl360-back-panel.png" alt-text="Pannello indietro HPE ProLiant DL360.":::

### <a name="enable-remote-access-and-update-the-password"></a>Abilitare l'accesso remoto e aggiornare la password

Vedere le sezioni precedenti per l'installazione di HPE ProLiant DL20:

  - "Abilita accesso remoto e aggiorna la password"

  - "Configurare il BIOS HPE"

La configurazione aziendale è identica.

> [!Note]
> Nel modulo matrice, verificare di aver selezionato tutte le opzioni.

### <a name="ilo-remote-installation-from-a-virtual-drive"></a>installazione remota di iLO (da un'unità virtuale)

Questa procedura descrive l'installazione di iLO da un'unità virtuale.

Per effettuare l'installazione:

1. Accedere alla console iLO, quindi fare clic con il pulsante destro del mouse sulla schermata Server.

1. Selezionare **console HTML5**.

1. Nella console di selezionare l'icona CD e scegliere l'opzione CD/DVD.

1. Selezionare **file ISO locale**.

1. Nella finestra di dialogo scegliere il file ISO pertinente.

1. Passare all'icona a sinistra, selezionare **Power** e selezionare Reset ( **Reimposta**).

1. Il dispositivo viene riavviato ed esegue il processo di installazione del sensore.

### <a name="software-installation-hpe-dl360"></a>Installazione software (HPE DL360)

Il processo di installazione richiede circa 20 minuti. Al termine dell'installazione, il sistema viene riavviato più volte.

Per effettuare l'installazione:

1. Connettere lo schermo e la tastiera all'appliance, quindi connettersi all'interfaccia della riga di comando.

1. Connettere un CD o un disco esterno su una chiave con l'immagine ISO scaricata dalla pagina **aggiornamenti** nel portale Defender for Internet.

1. Avviare l'appliance.

1. Selezionare **inglese**.

1. Selezionare **Sensor-Release- <version> Enterprise**.

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Screenshot che mostra la selezione della versione.":::

1. Nell'installazione guidata, definire il profilo dell'appliance e le proprietà di rete.

    :::image type="content" source="media/tutorial-install-components/installation-wizard-screen-v2.png" alt-text="Screenshot che mostra l'installazione guidata.":::

    | Parametro | Configurazione |
    | ----------| ------------- |
    | **Profilo hardware** | Selezionare **azienda**. |
    | **Interfaccia di gestione** | **eno2** |
    | **Parametri di rete predefiniti (forniti dal cliente)** | **Indirizzo IP della rete di gestione:** <br/>**subnet mask:** <br/>**nome host dell'appliance:** <br/>**DNS** <br/>**Indirizzo IP del gateway predefinito:**|
    | **interfacce di input:**  | Il sistema genera automaticamente un elenco di interfacce di input.<br/><br/>Per eseguire il mirroring delle interfacce di input, copiare tutti gli elementi presentati nell'elenco con un separatore virgola.<br/><br/> Non è necessario configurare l'interfaccia Bridge. Questa opzione viene utilizzata solo per i casi di utilizzo speciali. |

1. Dopo circa 10 minuti, verranno visualizzati i due set di credenziali. Uno per un utente di **CyberX** e uno per un utente di **supporto** .

1. Salvare l'ID e le password dell'appliance. Queste credenziali sono necessarie per accedere alla piattaforma per la prima volta.

1. Premere **invio** per continuare.

## <a name="sensor-installation-for-the-virtual-appliance"></a>Installazione del sensore per l'appliance virtuale

È possibile distribuire la macchina virtuale per Defender per il sensore di Internet delle cose nelle architetture seguenti:


| Architettura | Specifiche | Utilizzo | Commenti |
|---|---|---|---|
| **Funzionalità per le aziende** | CPU: 8<br/>Memoria: 32G RAM<br/>HDD: 1800 GB | Ambiente di produzione | Predefinito e più comune |
| **Piccola impresa** | CPU: 4 <br/>Memoria: 8G RAM<br/>HDD: 500 GB | Ambienti di produzione di test o di piccole dimensioni | -  |
| **Office** | CPU: 4<br/>Memoria: 8G RAM<br/>HDD: 100 GB | Ambienti di test di piccole dimensioni | -  |

### <a name="prerequisites"></a>Prerequisiti

La console di gestione locale supporta le opzioni di distribuzione VMware e Hyper-V. Prima di iniziare l'installazione, verificare che siano disponibili gli elementi seguenti:

  - VMware (ESXi 5,5 o versione successiva) o hypervisor Hyper-V (Windows 10 Pro o Enterprise) installato e operativo

  - Risorse hardware disponibili per la macchina virtuale

  - File di installazione ISO per il sensore Azure Defender per l'it

Verificare che l'hypervisor sia in esecuzione.

### <a name="create-the-virtual-machine-esxi"></a>Creare la macchina virtuale (ESXi)

1. Accedere a ESXi, scegliere l' **archivio dati** pertinente e selezionare **datastore browser**.

1. **Caricare** l'immagine e selezionare **Chiudi**.

1. Passare a **macchine virtuali** e quindi selezionare **Crea/registra VM**.

1. Selezionare **Crea nuova macchina virtuale** e quindi fare clic su **Avanti**.

1. Aggiungere un nome di sensore e scegliere:

   - Compatibilità: **&lt; versione &gt; più recente di ESXi**

   - Famiglia di sistemi operativi guest: **Linux**

   - Versione del sistema operativo guest: **Ubuntu Linux (64 bit)**

1. Selezionare **Avanti**.

1. Scegliere l'archivio dati pertinente e fare clic su **Avanti**.

1. Modificare i parametri hardware virtuali in base all'architettura richiesta.

1. Per l' **unità CD/DVD 1** selezionare **file ISO archivio dati** e scegliere il file ISO caricato in precedenza.

1. Selezionare **Next** > **Finish** (Avanti > Fine).

### <a name="create-the-virtual-machine-hyper-v"></a>Creazione della macchina virtuale (Hyper-V)

In questa procedura viene descritto come creare una macchina virtuale utilizzando Hyper-V.

Per creare una macchina virtuale:

1. Creare un disco virtuale nella console di gestione di Hyper-V.

1. Selezionare **Format = VHDX**.

1. Selezionare **Type = Dynamic Expanding**.

1. Immettere il nome e il percorso del disco rigido virtuale.

1. Immettere le dimensioni necessarie in base all'architettura.   

1. Rivedere il riepilogo e selezionare **Fine**.

1. Nel menu **azioni** creare una nuova macchina virtuale.

1. Immettere un nome per la macchina virtuale.

1. Selezionare **specifica** generazione generazione  >  **1**.

1. Specificare l'allocazione di memoria, in base all'architettura, e selezionare la casella di controllo per la memoria dinamica.

1. Configurare la scheda di rete in base alla topologia di rete del server.

1. Connettere il VHDX creato in precedenza alla macchina virtuale.

1. Rivedere il riepilogo e selezionare **Fine**.

1. Fare clic con il pulsante destro del mouse sulla nuova macchina virtuale e scegliere **Impostazioni**.

1. Selezionare **Aggiungi hardware** e aggiungere una nuova scheda di rete.

1. Selezionare il commutire virtuale che si connetterà alla rete di gestione dei sensori.

1. Allocare risorse CPU (in base all'architettura).

1. Connettere l'immagine ISO della console di gestione a un'unità DVD virtuale.

1. Avviare la macchina virtuale.

2. Scegliere **Connetti** dal menu **azioni** per continuare l'installazione del software.

### <a name="software-installation-esxi-and-hyper-v"></a>Installazione software (ESXi e Hyper-V)

Questa sezione descrive l'installazione del software ESXi e Hyper-V.

Per effettuare l'installazione:

1. Aprire la console della macchina virtuale.

1. La macchina virtuale verrà avviata dall'immagine ISO e verrà visualizzata la schermata di selezione della lingua. Selezionare **inglese**.

1. Selezionare l'architettura richiesta.

1. Definire il profilo dell'appliance e le proprietà di rete:

    | Parametro | Configurazione |
    | ----------| ------------- |
    | **Profilo hardware** | &lt;architettura necessaria&gt; |
    | **Interfaccia di gestione** | **ens192** |
    | **Parametri di rete (forniti dal cliente)** | **Indirizzo IP della rete di gestione:** <br/>**subnet mask:** <br/>**nome host dell'appliance:** <br/>**DNS** <br/>**gateway predefinito:** <br/>**interfacce di input:**|
    | **Interfacce Bridge:** | Non è necessario configurare l'interfaccia Bridge. Questa opzione è solo per i casi d'uso speciali. |

1. Immettere **Y** per accettare le impostazioni.

1. Le credenziali di accesso vengono generate e presentate automaticamente. Copiare il nome utente e la password in un luogo sicuro, perché sono necessari per l'accesso e l'amministrazione.

      - **Supporto**: utente amministratore per la gestione degli utenti.

      - **CyberX**: l'equivalente della radice per accedere all'appliance.

1. Il dispositivo viene riavviato.

1. Accedere alla console di gestione tramite l'indirizzo IP configurato in precedenza: `https://ip_address` .

    :::image type="content" source="media/tutorial-install-components/defender-for-iot-sign-in-screen.png" alt-text="Screenshot che mostra l'accesso alla console di gestione.":::

## <a name="virtual-appliance-on-premises-management-console-installation"></a>Appliance virtuale: installazione della console di gestione locale

La VM della console di gestione locale supporta le architetture seguenti:

| Architettura | Specifiche | Utilizzo | 
|--|--|--|
| Enterprise <br/>(Impostazione predefinita e più comune) | CPU: 8 <br/>Memoria: 32G RAM<br/> HDD: 1,8 TB | Ambienti di produzione di grandi dimensioni | 
| Enterprise | CPU: 4 <br/> Memoria: 8G RAM<br/> HDD: 500 GB | Ambienti di produzione di grandi dimensioni |
| Enterprise | CPU: 4 <br/>Memoria: 8G RAM <br/> HDD: 100 GB | Ambienti di test di piccole dimensioni | 
   
### <a name="prerequisites"></a>Prerequisiti

La console di gestione locale supporta le opzioni di distribuzione VMware e Hyper-V. Prima di iniziare l'installazione, verificare quanto segue:

- VMware (ESXi 5,5 o versione successiva) o hypervisor Hyper-V (Windows 10 Pro o Enterprise) è installato e operativo.

- Le risorse hardware sono disponibili per la macchina virtuale.

- Si dispone del file di installazione ISO per la console di gestione locale.
    
- L'hypervisor è in esecuzione.

### <a name="create-the-virtual-machine-esxi"></a>Creare la macchina virtuale (ESXi)

Per creare una macchina virtuale (ESXi):

1. Accedere a ESXi, scegliere l' **archivio dati** pertinente e selezionare **datastore browser**.

1. Caricare l'immagine e selezionare **Chiudi**.

1. Passare a **macchine virtuali**.

1. Selezionare **Crea/registra macchina virtuale**.

1. Selezionare **Crea nuova macchina virtuale** e fare clic su **Avanti**.

1. Aggiungere un nome di sensore e scegliere:

   - Compatibilità \<latest ESXi version>

   - Famiglia di sistemi operativi guest: Linux

   - Versione del sistema operativo guest: Ubuntu Linux (64 bit)

1. Selezionare **Avanti**.

1. Scegliere Archivio dati pertinente e fare clic su **Avanti**.

1. Modificare i parametri hardware virtuali in base all'architettura richiesta.

1. Per l' **unità CD/DVD 1** selezionare **file ISO archivio dati** e scegliere il file ISO caricato in precedenza.

1. Selezionare **Next** > **Finish** (Avanti > Fine).

### <a name="create-the-virtual-machine-hyper-v"></a>Creazione della macchina virtuale (Hyper-V)

Per creare una macchina virtuale con Hyper-V:

1. Creare un disco virtuale nella console di gestione di Hyper-V.

1. Selezionare il formato **VHDX**.

1. Selezionare **Avanti**.

1. Selezionare il tipo **Dynamic Expanding**.

1. Selezionare **Avanti**.

1. Immettere il nome e il percorso del disco rigido virtuale.

1. Selezionare **Avanti**.

1. Immettere le dimensioni necessarie in base all'architettura.

1. Selezionare **Avanti**.

1. Rivedere il riepilogo e selezionare **Fine**.

1. Nel menu **azioni** creare una nuova macchina virtuale.

1. Selezionare **Avanti**.

1. Immettere un nome per la macchina virtuale.

1. Selezionare **Avanti**.

1. Selezionare **generazione** e impostarla sulla **generazione 1**.

1. Selezionare **Avanti**.

1. Specificare l'allocazione di memoria, in base all'architettura, e selezionare la casella di controllo per la memoria dinamica.

1. Selezionare **Avanti**.

1. Configurare la scheda di rete in base alla topologia di rete del server.

1. Selezionare **Avanti**.

1. Connettere il VHDX creato in precedenza alla macchina virtuale.

1. Selezionare **Avanti**.

1. Rivedere il riepilogo e selezionare **Fine**.

1. Fare clic con il pulsante destro del mouse sulla nuova macchina virtuale, quindi scegliere **Impostazioni**.

1. Selezionare **Aggiungi hardware** e aggiungere un nuovo adapter per **scheda di rete**.

1. Per **Commuter virtuale** selezionare l'opzione che si connetterà alla rete di gestione dei sensori.

1. Allocare risorse CPU (in base all'architettura).

1. Connettere l'immagine ISO della console di gestione a un'unità DVD virtuale.

1. Avviare la macchina virtuale.

1. Scegliere **Connetti** dal menu **azioni** per continuare l'installazione del software.

### <a name="software-installation-esxi-and-hyper-v"></a>Installazione software (ESXi e Hyper-V)

L'avvio della macchina virtuale avvierà il processo di installazione dall'immagine ISO. Per migliorare la sicurezza, è possibile creare una seconda interfaccia di rete nella console di gestione locale. Un'interfaccia di rete è dedicata agli utenti e può supportare la configurazione di un gateway per le reti indirizzate. La seconda interfaccia di rete è dedicata a tutti i sensori collegati all'interno di un intervallo di indirizzi IP.

Per entrambe le interfacce di rete è abilitata l'interfaccia utente e tutte le funzionalità supportate dall'interfaccia utente saranno disponibili nell'interfaccia di rete secondaria quando il routing non è necessario. La disponibilità elevata viene eseguita nell'interfaccia di rete secondaria.

Se si sceglie di non distribuire un'interfaccia di rete secondaria, tutte le funzionalità saranno disponibili tramite l'interfaccia di rete primaria. 

Per installare il software:

1. Selezionare **inglese**.

1. Selezionare l'architettura necessaria per la distribuzione.

1. Definire l'interfaccia di rete per la rete di gestione dei sensori: interfaccia, IP, subnet, server DNS e gateway predefinito.

1. Opzionale Aggiungere una seconda interfaccia di rete alla console di gestione locale.

    1. `Please type sensor monitoring interface (Optional. Applicable when sensors are on a different network segment. For more information see the Installation instructions): <name of interface>`
    
    1. `Please type an IP address for the sensor monitoring interface (accessible by the sensors): <ip address>`
    
    1. `Please type a subnet mask for the sensor monitoring interface (accessible by the sensors): <subnet>`

1. Le credenziali di accesso vengono generate e presentate automaticamente. Conservarle in un luogo sicuro, perché sono necessarie per l'accesso e l'amministrazione.

    | Username | Descrizione |
    |--|--|
    | Supporto | Utente amministratore per la gestione degli utenti. |
    | CyberX | Equivalente della radice per accedere all'appliance. |

1. Il dispositivo viene riavviato.

1. Accedere alla console di gestione tramite l'indirizzo IP configurato in precedenza: `<https://ip_address>` .

    :::image type="content" source="media/tutorial-install-components/defender-for-iot-management-console-sign-in-screen.png" alt-text="Screenshot che mostra la schermata di accesso della console di gestione.":::

## <a name="post-installation-validation"></a>Convalida post-installazione

Per convalidare l'installazione di un dispositivo fisico, è necessario eseguire molti test. Lo stesso processo di convalida si applica a tutti i tipi di appliance.

Eseguire la convalida usando l'interfaccia utente grafica o l'interfaccia della riga di comando. La convalida è disponibile per il **supporto** utente e per l'utente **CyberX**.

La convalida post-installazione deve includere i test seguenti:

  - **Test di integrità**: verificare che il sistema sia in esecuzione.

  - **Versione**: verificare che la versione sia corretta.

  - **ifconfig**: verificare che tutte le interfacce di input configurate durante il processo di installazione siano in esecuzione.

### <a name="checking-system-health-by-using-the-gui"></a>Verifica dell'integrità del sistema tramite l'interfaccia utente grafica

:::image type="content" source="media/tutorial-install-components/system-health-check-screen.png" alt-text="Screenshot che mostra il controllo integrità sistema.":::

#### <a name="sanity"></a>Integrità

- **Appliance**: esegue la verifica dello stato di integrità dell'appliance. È possibile eseguire lo stesso controllo usando il comando CLI `system-sanity` .

- **Versione**: Visualizza la versione dell'appliance.

- **Proprietà di rete**: Visualizza i parametri di rete del sensore.

#### <a name="redis"></a>Redis

- **Memory**: fornisce l'immagine complessiva dell'utilizzo della memoria, ad esempio la quantità di memoria utilizzata e la quantità di memoria rimanente.

- **Chiave più lunga**: Visualizza le chiavi più lunghe che potrebbero causare un utilizzo intensivo della memoria.

#### <a name="system"></a>Sistema

- **Log principale**: fornisce le ultime 500 righe del log principale, consentendo di visualizzare le righe di log recenti senza esportare l'intero registro di sistema.

- **Gestione attività**: converte le attività visualizzate nella tabella dei processi nei livelli seguenti: 
  
  - Livello permanente (Redis) 
  - Livello di cassa (SQL)

- **Statistiche di rete**: Visualizza le statistiche di rete.

- **Top**: Mostra la tabella dei processi. Si tratta di un comando di Linux che fornisce una visualizzazione dinamica in tempo reale del sistema in esecuzione.

- **Backup Memory Check**: fornisce lo stato della memoria di backup, verificando quanto segue:
  - Percorso della cartella di backup 
  - Dimensioni della cartella di backup
  - Limitazioni della cartella di backup
  - Quando è stato eseguito l'ultimo backup
  - Quantità di spazio disponibile per i file di backup aggiuntivi

- **ifconfig**: Visualizza i parametri per le interfacce fisiche dell'appliance.

- **CyberX carica**: Visualizza il traffico di rete e la larghezza di banda usando i test di sei secondi.

- **Errors from core, log**: Visualizza gli errori dal file di log principale.

Per accedere allo strumento:

1. Accedere al sensore con le credenziali dell'utente di **supporto** .

1. Selezionare **statistiche di sistema** dalla finestra **impostazioni di sistema** .

    :::image type="icon" source="media/tutorial-install-components/system-statistics-icon.png" border="false":::

### <a name="checking-system-health-by-using-the-cli"></a>Verifica dell'integrità del sistema tramite l'interfaccia della riga di comando

**Test 1: integrità**

Verificare che il sistema sia attivo e in esecuzione:

1. Connettersi all'interfaccia della riga di comando con il terminale Linux (ad esempio, PuTTy) e il **supporto** utente.

1. Immettere `system sanity`.

1. Verificare che tutti i servizi siano di colore verde (in esecuzione).

    :::image type="content" source="media/tutorial-install-components/support-screen.png" alt-text="Screenshot che mostra i servizi in esecuzione.":::

1. Verificare che il **sistema sia attivo. (prod)** viene visualizzato nella parte inferiore.

**Test 2: controllo della versione**

Verificare che venga utilizzata la versione corretta:

1. Connettersi all'interfaccia della riga di comando con il terminale Linux (ad esempio, PuTTy) e il **supporto** utente.

1. Immettere `system version`.

1. Verificare che sia visualizzata la versione corretta.

**Test 3: convalida della rete**

Verificare che tutte le interfacce di input configurate durante il processo di installazione siano in esecuzione:

1. Connettersi all'interfaccia della riga di comando con il terminale Linux (ad esempio, PuTTy) e il **supporto** utente.

1. Immettere `network list` (l'equivalente del comando Linux `ifconfig` ).

1. Verificare che vengano visualizzate le interfacce di input obbligatorie. Se, ad esempio, sono installate due schede di interfaccia di rete quad in rame, nell'elenco devono essere presenti 10 interfacce.

    :::image type="content" source="media/tutorial-install-components/interface-list-screen.png" alt-text="Screenshot che mostra l'elenco delle interfacce.":::

**Test 4: accesso alla gestione dell'interfaccia utente**

Verificare che sia possibile accedere alla GUI Web della console:

1. Connettere un computer portatile con un cavo Ethernet alla porta di gestione (**GB1**).

1. Definire l'indirizzo della scheda di interfaccia di rete portatile affinché si trovi nello stesso intervallo del dispositivo.

    :::image type="content" source="media/tutorial-install-components/access-to-ui.png" alt-text="Screenshot che mostra l'accesso di gestione all'interfaccia utente.":::

1. Effettuare il ping dell'indirizzo IP dell'appliance dal computer portatile per verificare la connettività (impostazione predefinita: 10.100.10.1).

1. Aprire il browser Chrome nel computer portatile e immettere l'indirizzo IP dell'appliance.

1. Nella finestra **connessione non privata** selezionare **Avanzate** e continuare.

1. Il test ha esito positivo quando viene visualizzata la schermata di accesso di Defender for all.

   :::image type="content" source="media/tutorial-install-components/defender-for-iot-sign-in-screen.png" alt-text="Screenshot che mostra l'accesso alla console di gestione.":::

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="you-cant-connect-by-using-a-web-interface"></a>Non è possibile connettersi tramite un'interfaccia Web

1. Verificare che il computer che si sta tentando di connettere si trovi nella stessa rete del dispositivo.

1. Verificare che la rete GUI sia connessa alla porta di gestione.

1. Effettuare il ping dell'indirizzo IP dell'appliance. Se non è presente alcun ping:

   1. Connettere un monitor e una tastiera al dispositivo.

   1. Usare l'utente e la password di **supporto** per accedere.

   1. Usare il comando `network list` per visualizzare l'indirizzo IP corrente.

      :::image type="content" source="media/tutorial-install-components/network-list.png" alt-text="Screenshot che mostra l'elenco di rete.":::

1. Se i parametri di rete non sono configurati correttamente, attenersi alla procedura seguente per modificarli:

   1. Usare il comando `network edit-settings` .

   1. Per modificare l'indirizzo IP della rete di gestione, selezionare **Y**.

   1. Per modificare il subnet mask, selezionare **Y**.

   1. Per modificare il DNS, selezionare **Y**.

   1. Per modificare l'indirizzo IP del gateway predefinito, selezionare **Y**.

   1. Per la modifica dell'interfaccia di input (solo sensore), selezionare **N**.

   1. Per applicare le impostazioni, selezionare **Y**.

1. Dopo il riavvio, connettersi con le credenziali dell'utente di supporto e usare il `network list` comando per verificare che i parametri siano stati modificati.

1. Provare di nuovo a eseguire il ping e connettersi dall'interfaccia utente grafica.

### <a name="the-appliance-isnt-responding"></a>L'appliance non risponde

1. Connettere un monitor e una tastiera al dispositivo oppure usare PuTTy per connettersi in remoto all'interfaccia della riga di comando.

1. Usare le credenziali dell'utente di **supporto** per accedere.

1. Usare il `system sanity` comando e verificare che tutti i processi siano in esecuzione.

    :::image type="content" source="media/tutorial-install-components/system-sanity-screen.png" alt-text="Screenshot che mostra il comando di integrità del sistema.":::

Per eventuali altri problemi, contattare [supporto tecnico Microsoft](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

## <a name="appendix-a-mirroring-port-on-vswitch-esxi"></a>Appendice A: porta di mirroring in vSwitch (ESXi)

### <a name="configure-a-span-port-on-an-existing-vswitch"></a>Configurare una porta SPAN in un vSwitch esistente

Un vSwitch non dispone di funzionalità di mirroring, ma è possibile usare una soluzione alternativa per implementare una porta SPAN.

Per configurare una porta SPAN:

1. Aprire le proprietà di vSwitch.

1. Selezionare **Aggiungi**.

1. Selezionare **macchina virtuale**  >  **Avanti**.

1. Inserire un'etichetta di rete **span Network**, selezionare **VLAN ID**  >  **All**, quindi fare clic su **Next (avanti**).

1. Selezionare **Fine**.

1. Selezionare **span Network** > **Edit*.

1. Selezionare **Security (sicurezza**) e verificare che i criteri della **modalità promiscua** siano impostati sulla modalità **Accept** .

1. Fare clic su **OK** e quindi selezionare **Chiudi** per chiudere le proprietà di vswitch.

1. Aprire le proprietà della **VM XSense** .

1. Per **scheda di rete 2** selezionare la rete **span** .

1. Selezionare **OK**.

1. Connettersi al sensore e verificare il funzionamento del mirroring.

## <a name="appendix-b-access-sensors-from-the-on-premises-management-console"></a>Appendice B: accedere ai sensori dalla console di gestione locale

È possibile migliorare la sicurezza del sistema impedendo l'accesso diretto degli utenti al sensore. Usare invece il tunneling del proxy per consentire agli utenti di accedere al sensore dalla console di gestione locale con una singola regola del firewall. Questa tecnica restringe la possibilità di accesso non autorizzato all'ambiente di rete oltre il sensore. L'esperienza dell'utente durante l'accesso al sensore rimane invariata.

:::image type="content" source="media/tutorial-install-components/sensor-system-graph.png" alt-text="Screenshot che mostra l'accesso al sensore.":::

Per abilitare il tunneling:

1. Accedere all'interfaccia della riga di comando della console di gestione locale con **CyberX** o **supportare** le credenziali utente.

1. Immettere `sudo cyberx-management-tunnel-enable`.

1. Premere **INVIO**.

1. Immettere `--port 10000`.

### <a name="next-steps"></a>Passaggi successivi

[Configurare la rete](how-to-set-up-your-network.md)