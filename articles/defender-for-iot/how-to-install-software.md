---
title: Installazione di Defender per IoT
description: Informazioni su come installare un sensore e la console di gestione locale per Azure Defender per IoT.
ms.date: 4/20/2021
ms.topic: how-to
ms.openlocfilehash: e8366a3408e64d95e6c4d50e3ddef84309b4e8e5
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829734"
---
# <a name="defender-for-iot-installation"></a>Installazione di Defender per IoT

Questo articolo descrive come installare gli elementi seguenti di Azure Defender per IoT:

- **Sensore:** Defender per i sensori IoT raccoglie il traffico di rete ICS usando il monitoraggio passivo (senza agente). Passivi e non intrusivi, i sensori non hanno alcun impatto sulle reti e i dispositivi OT e IoT. Il sensore si connette a una porta SPAN o a un TAP di rete e inizia immediatamente a monitorare la rete. I rilevamenti vengono visualizzati nella console del sensore. Qui è possibile visualizzarli, analizzarli e analizzarli in una mappa di rete, nell'inventario dei dispositivi e in un'ampia gamma di report. Ad esempio, report di valutazione dei rischi, data mining query e vettori di attacco. Altre informazioni sulle funzionalità dei sensori sono disponibili nella [Guida per l'utente del sensore Defender per IoT (download diretto).](./getting-started.md)

- **Console di gestione locale:** la console di gestione locale consente di eseguire la gestione dei dispositivi, la gestione dei rischi e la gestione delle vulnerabilità. È anche possibile usarlo per eseguire il monitoraggio delle minacce e la risposta agli eventi imprevisti nell'azienda. Offre una visualizzazione unificata di tutti i dispositivi di rete, gli indicatori di rischio IoT e OT e gli avvisi rilevati nelle strutture in cui vengono distribuiti i sensori. Usare la console di gestione locale per visualizzare e gestire i sensori nelle reti con flusso d'aria.

Questo articolo illustra le informazioni di installazione seguenti:

  - **Hardware:** Dettagli dell'appliance fisica Dell e HPE.

  - **Software:** Installazione del software del sensore e della console di gestione locale.

  - **Appliance virtuali:** Dettagli della macchina virtuale e installazione del software.

Dopo l'installazione, connettere il sensore alla rete.

## <a name="about-defender-for-iot-appliances"></a>Informazioni su Defender per le appliance IoT 

Le sezioni seguenti forniscono informazioni sulle appliance del sensore Defender per IoT e sull'appliance per la console di gestione locale di Defender per IoT.

### <a name="physical-appliances"></a>Appliance fisiche

Il sensore dell'appliance Defender per IoT si connette a una porta SPAN o a un TAP di rete e inizia immediatamente a raccogliere il traffico di rete ICS usando il monitoraggio passivo (senza agente). Questo processo non ha alcun impatto sulle reti e sui dispositivi OT perché non viene inserito nel percorso dati e non esegue attivamente l'analisi dei dispositivi OT.

Sono disponibili le appliance di montaggio su rack seguenti:

| **Tipo di distribuzione** | **Aziendale** | **Funzionalità per le aziende** | **SMB** | **Linea** |
|--|--|--|--|--|
| **Modello** | HPE ProLiant DL360 | Dell PowerEdge R340 XL | HPE ProLiant DL20 | HPE ProLiant DL20 |
| **Monitoraggio delle porte** | fino a 15 RJ45 o 8 OPT | fino a 9 RJ45 o 6 OPT | fino a 8 RJ45 o 6 OPT | 4 RJ45 |
| **Larghezza di banda massima\*** | 3 Gb/sec | 1 Gb/sec | 1 Gb/sec | 100 Mb/sec |
| **Numero massimo di dispositivi protetti** | 30.000 | 10,000 | 15.000 | 1\.000 |

*La capacità massima della larghezza di banda può variare a seconda della distribuzione del protocollo.

### <a name="virtual-appliances"></a>Dispositivo virtuale

Sono disponibili le appliance virtuali seguenti:

| **Tipo di distribuzione** | **Funzionalità per le aziende** | **SMB** | **Linea** |
|--|--|--|--|
| **Descrizione** | Appliance virtuale per distribuzioni aziendali | Appliance virtuale per distribuzioni SMB | Appliance virtuale per le distribuzioni line |
| **Larghezza di banda massima\*** | 150 Mb/sec | 15 Mb/sec | 3 Mb/sec |
| **Numero massimo di dispositivi protetti** | 3,000 | 300 | 100 |
| **Tipo di distribuzione** | Enterprise | SMB | A linee |
| **Descrizione** | Appliance virtuale per distribuzioni aziendali | Appliance virtuale per distribuzioni SMB | Appliance virtuale per le distribuzioni line |

*La capacità massima della larghezza di banda può variare a seconda della distribuzione del protocollo.

### <a name="hardware-specifications-for-the-on-premises-management-console"></a>Specifiche hardware per la console di gestione locale

 | Elemento | Descrizione |
 |----|--|
 **Descrizione** | In un'architettura a più livelli, la console di gestione locale offre visibilità e controllo nei siti distribuiti geograficamente. Si integra con gli stack di sicurezza SOC, tra cui SIEM, sistemi di creazione di ticket, firewall di nuova generazione, piattaforme di accesso remoto sicure e sandbox di malware Defender per IoT ICS. |
 **Tipo di distribuzione** | Enterprise |
 **Tipo di appliance**  | Dell R340, VM |
 **Numero di sensori gestiti** | Nessuna limitazione |

## <a name="prepare-for-the-installation"></a>Preparare l'installazione

### <a name="access-the-iso-installation-image"></a>Accedere all'immagine di installazione ISO

L'immagine di installazione è accessibile dal portale di Defender per IoT.

Per accedere al file:

1. Accedere all'account Defender per IoT.

1. Passare alla **pagina Sensore di rete** o console di **gestione** locale e selezionare una versione da scaricare.

### <a name="install-from-dvd"></a>Installare da DVD

Prima dell'installazione, assicurarsi di disporre di:

- Un'unità DVD portatile con il connettore USB.

- Immagine del programma di installazione ISO.

Per effettuare l'installazione:

1. Masterizzare l'immagine in un DVD o preparare un disco su una chiave. Connettere un'unità DVD portatile al computer, fare clic con il pulsante destro del mouse sull'immagine ISO e scegliere **Masterizza su disco**.

1. Connettere il DVD o il disco su una chiave e configurare l'appliance per l'avvio da DVD o disco su una chiave.

### <a name="install-from-disk-on-a-key"></a>Eseguire l'installazione da disco in una chiave

Prima dell'installazione, assicurarsi di disporre di:

  - Rufus installato.
  
  - Disco su chiave con USB versione 3.0 e successive. La dimensione minima è di 4 GB.

  - Un file di immagine del programma di installazione ISO.

Il disco su una chiave verrà cancellato in questo processo.

Per preparare un disco in una chiave:

1. Eseguire Rufus e selezionare **SENSOR ISO**.

1. Connettere il disco su una chiave al pannello anteriore.

1. Impostare il BIOS del server per l'avvio da USB.

## <a name="dell-poweredger340xl-installation"></a>Installazione di Dell PowerEdgeR340XL

Prima di installare il software nell'appliance Dell, è necessario modificare la configurazione DEL BIOS dell'appliance:

  - Il pannello anteriore Dell [PowerEdge R340](#dell-poweredge-r340-front-panel) e il pannello posteriore di [Dell PowerEdge R340](#dell-poweredge-r340-back-panel) contengono la descrizione dei pannelli anteriore e posteriore, insieme alle informazioni necessarie per l'installazione, ad esempio driver e porte.

  - [Configurazione BIOS Dell](#dell-bios-configuration) fornisce informazioni su come connettersi all'interfaccia di gestione dell'appliance Dell e configurare il BIOS.

  - [Installazione software (Dell R340)](#software-installation-dell-r340) descrive la procedura necessaria per installare il software del sensore Defender per IoT.

### <a name="dell-poweredge-r340xl-requirements"></a>Requisiti di Dell PowerEdge R340XL 

Per installare l'appliance Dell PowerEdge R340XL, è necessario:

- Licenza Enterprise per Del Remote Access Controller (iDrac)

- CONFIGURAZIONE DEL BIOS XML

- Versioni del firmware del server:

  - BIOS versione 2.1.6

  - iDrac versione 3.23.23.23

### <a name="dell-poweredge-r340-front-panel"></a>Pannello anteriore Dell PowerEdge R340

:::image type="content" source="media/tutorial-install-components/view-of-dell-poweredge-r340-front-panel.jpg" alt-text="Pannello anteriore di Dell PowerEdge R340.":::

 1. Pannello di controllo a sinistra 
 1. Unità ottica (facoltativo) 
 1. Pannello di controllo a destra 
 1. Tag di informazioni 
 1. Unità  

### <a name="dell-poweredge-r340-back-panel"></a>Pannello posteriore di Dell PowerEdge R340

:::image type="content" source="media/tutorial-install-components/view-of-dell-poweredge-r340-back-panel.jpg" alt-text="Pannello posteriore di Dell PowerEdge R340.":::

1. Porta seriale 
1. Porta NIC (Gb 1) 
1. Porta NIC (Gb 1) 
1. PCIe a mezza altezza 
1. Slot scheda di espansione PCIe a altezza intera 
1. Unità di alimentazione 1 
1. Unità di alimentazione 2 
1. Identificazione del sistema 
1. Pulsante CMA (System Status Indicator Cable Port) 
1. Porta USB 3.0 (2) 
1. Porta di rete dedicata iDRAC9 
1. Porta VGA 

### <a name="dell-bios-configuration"></a>Configurazione del BIOS Dell

La configurazione del BIOS di Dell è necessaria per regolare l'appliance Dell in modo che funzioni con il software.

La configurazione del BIOS viene eseguita tramite una configurazione predefinita. Il file è accessibile da [Help Center.](https://help.cyberx-labs.com/)

Importare il file di configurazione nell'appliance Dell. Prima di usare il file di configurazione, è necessario stabilire la comunicazione tra l'appliance Dell e il computer di gestione.

L'appliance Dell è gestita da un dispositivo iDRAC integrato con Lifecycle Controller (LC). L'LC è incorporato in ogni server Dell PowerEdge e offre funzionalità che consentono di distribuire, aggiornare, monitorare e gestire le appliance Dell PowerEdge.

Per stabilire la comunicazione tra l'appliance Dell e il computer di gestione, è necessario definire l'indirizzo IP iDRAC e l'indirizzo IP del computer di gestione nella stessa subnet.

Quando viene stabilita la connessione, il BIOS è configurabile.

Per configurare il BIOS Dell:

1. [Configurare l'indirizzo IP iDRAC](#configure-idrac-ip-address)

1. [Importare il file di configurazione del BIOS](#import-the-bios-configuration-file)

#### <a name="configure-idrac-ip-address"></a>Configurare l'indirizzo IP iDRAC

1. Alimentare il sensore.

1. Se il sistema operativo è già installato, premere F2 per immettere la configurazione del BIOS.

1. Selezionare **iDRAC Settings (Impostazioni iDRAC).**

1. Selezionare **Rete.**

   > [!NOTE]
   > Durante l'installazione, è necessario configurare l'indirizzo IP e la password iDRAC predefiniti indicati nei passaggi seguenti. Dopo l'installazione, è possibile modificare queste definizioni.

1. Modificare l'indirizzo IPv4 statico **in 10.100.100.250.**

1. Modificare l'subnet mask statica in **255.255.255.0.**

   :::image type="content" source="media/tutorial-install-components/idrac-network-settings-screen-v2.png" alt-text="Screenshot che mostra l'subnet mask.":::

1. Selezionare **Back**  >  **Finish (Indietro fine).**

#### <a name="import-the-bios-configuration-file"></a>Importare il file di configurazione del BIOS

Questo articolo descrive come configurare il BIOS usando il file di configurazione.

1. Collegare un PC con un indirizzo IP statico preconfigurato **10.100.100.200** alla **porta iDRAC.**

   :::image type="content" source="media/tutorial-install-components/idrac-port.png" alt-text="Screenshot della porta dell'indirizzo IP preconfigurato.":::

1. Aprire un browser e immettere **10.100.100.250** per connettersi all'interfaccia Web iDRAC.

1. Accedere con i privilegi di amministratore predefiniti di Dell:

   - Nome utente: **radice**

   - Password: **{**

1. Le credenziali dell'appliance sono:

   - Nome utente: **XXX**

   - Password: **XXX**

     Viene avviata l'operazione di importazione del profilo server.

     > [!NOTE]
     > Prima di importare il file, assicurarsi di:
     > - Si è l'unico utente attualmente connesso a iDRAC.
     > - Il sistema non è presente nel menu BIOS.

1. Passare a **Profilo di** configurazione del server  >  **di configurazione**. Impostare i parametri seguenti:

   :::image type="content" source="media/tutorial-install-components/configuration-screen.png" alt-text="Screenshot che mostra la configurazione del profilo del server.":::

   | Parametro | Configurazione |
   |--|--|
   | Tipo percorso | Selezionare **Local (Locale).** |
   | Percorso file | Selezionare **Scegli file e** aggiungere il file XML di configurazione. |
   | Importare componenti | Selezionare **BIOS, NIC, RAID.** |
   | Tempo di attesa massimo | Selezionare **20 minuti.** |

1. Selezionare **Import** (Importa).

1. Per monitorare il processo, passare a **Coda**  >  **processi di manutenzione.**

   :::image type="content" source="media/tutorial-install-components/view-the-job-queue.png" alt-text="Screenshot che mostra coda processi.":::

#### <a name="manually-configuring-bios"></a>Configurazione manuale del BIOS 

È necessario configurare manualmente il BIOS dell'appliance se:

- L'appliance non è stata acquistata da Arrow.

- Si dispone di un'appliance, ma non si ha accesso al file di configurazione XML.

Dopo aver accesso al BIOS, passare a **Impostazioni del dispositivo.**

Per configurare manualmente:

1. Accedere al BIOS dell'appliance direttamente tramite tastiera e schermo oppure usare iDRAC.

   - Se l'appliance non è un'appliance Defender per IoT, aprire un browser e passare all'indirizzo IP configurato in precedenza. Accedere con i privilegi di amministratore predefiniti di Dell. Usare **root** per il nome utente **e il nome utente** per la password.

   - Se l'appliance è un'appliance Defender per IoT, accedere usando **XXX** come nome utente e **XXX** per la password.

1. Dopo aver accesso al BIOS, passare a **Impostazioni del dispositivo.**

1. Scegliere la configurazione controllata da RAID selezionando **Controller RAID integrato 1: Dell PERC \<PERC H330 Adapter\> Configuration Utility**.

1. Selezionare **Gestione configurazione**.

1. Selezionare **Crea disco virtuale.**

1. Nel campo **Select RAID Level (Seleziona** livello RAID) selezionare **RAID5.** Nel campo **Virtual Disk Name (Nome disco** virtuale) immettere **ROOT** e selezionare **Physical Disks (Dischi fisici).**

1. Selezionare **Check All (Controlla** tutto) e quindi **Apply Changes (Applica modifiche)**

1. Selezionare **OK**.

1. Scorrere verso il basso e **selezionare Crea disco virtuale.**

1. Selezionare la **casella di** controllo Conferma e selezionare **Sì.**

1. Selezionare **OK**.

1. Tornare alla schermata principale e selezionare **BIOS di sistema.**

1. Selezionare **Impostazioni di avvio**.

1. Per **l'opzione Modalità** di avvio selezionare **BIOS.**

1. Selezionare **Indietro** e quindi Fine **per chiudere** le impostazioni del BIOS.

### <a name="software-installation-dell-r340"></a>Installazione del software (Dell R340)

Il processo di installazione richiede circa 20 minuti. Dopo l'installazione, il sistema viene riavviato più volte.

Per effettuare l'installazione:

1. Verificare che il supporto della versione sia montato nell'appliance in uno dei modi seguenti:

   - Connettere il CD o il disco esterno su una chiave con la versione.

   - Montare l'immagine ISO usando iDRAC. Dopo aver effettuato l'accesso a iDRAC, selezionare la console virtuale e quindi selezionare **Supporto virtuale.**

1. Nella sezione **Map CD/DVD (Cd/DVD** mappa) **selezionare Choose File (Scegli file).**

1. Scegliere il file di immagine ISO versione per questa versione dalla finestra di dialogo visualizzata.

1. Selezionare il **pulsante Map Device (Dispositivo** mappa).

   :::image type="content" source="media/tutorial-install-components/mapped-device-on-virtual-media-screen-v2.png" alt-text="Screenshot che mostra un dispositivo mappato.":::

1. Il supporto è montato. Selezionare **Chiudi**.

1. Avviare l'appliance. Quando si usa iDRAC, è possibile riavviare i server selezionando il pulsante **Controllo console.** In Macro da **tastiera** selezionare quindi il **pulsante** Applica, che avvia la sequenza CTRL+ALT+CANC.

1. Selezionare **Inglese.**

1. Selezionare **SENSOR-RELEASE- \<version\> Enterprise.**

   :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Selezionare la versione del sensore e il tipo di organizzazione.":::   

1. Definire il profilo dell'appliance e le proprietà di rete:

   :::image type="content" source="media/tutorial-install-components/appliance-profile-screen-v2.png" alt-text="Screenshot che mostra il profilo dell'appliance e le proprietà di rete.":::   

   | Parametro | Configurazione |
   |--|--|
   | **Profilo hardware** | **azienda** |
   | **Interfaccia di gestione** | **eno1** |
   | **Parametri di rete (forniti dal cliente)** | - |
   |**indirizzo IP della rete di gestione:** | - |
   | **subnet mask:** | - |
   | **nome host dell'appliance:** | - |
   | **Dns:** | - |
   | **indirizzo IP del gateway predefinito:** | - |
   | **Interfacce di input:** |  Il sistema genera automaticamente l'elenco di interfacce di input. Per eseguire il mirroring delle interfacce di input, copiare tutti gli elementi presentati nell'elenco con un separatore virgola. Non è necessario configurare l'interfaccia del bridge. Questa opzione viene usata solo per casi d'uso speciali. |

1. Dopo circa 10 minuti, vengono visualizzati i due set di credenziali. Una è per un **utente di CyberX** e una per un **utente del** supporto.  

1. Salvare l'ID e le password dell'appliance. Queste credenziali saranno necessarie per accedere alla piattaforma la prima volta che la si usa.

1. Selezionare **INVIO** per continuare.

## <a name="hpe-proliant-dl20-installation"></a>Installazione di HPE ProLiant DL20

Questo articolo descrive il processo di installazione di HPE ProLiant DL20, che include i passaggi seguenti:

  - Abilitare l'accesso remoto e aggiornare la password di amministratore predefinita.
  - Configurare le impostazioni DEL BIOS e RAID.
  - Installare il software.

### <a name="about-the-installation"></a>Informazioni sull'installazione

  - È possibile installare appliance Enterprise e SMB. Il processo di installazione è identico per entrambi i tipi di appliance, ad eccezione della configurazione della matrice.
  - Viene fornito un utente amministratore predefinito. È consigliabile modificare la password durante il processo di configurazione della rete.
  - Durante il processo di configurazione della rete, si configurerà la porta iLO sulla porta di rete 1.
  - Il processo di installazione richiede circa 20 minuti. Dopo l'installazione, il sistema viene riavviato più volte.

### <a name="hpe-proliant-dl20-front-panel"></a>Pannello anteriore di HPE ProLiant DL20

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl20-front-panel-v2.png" alt-text="Pannello anteriore di HPE ProLiant DL20.":::

### <a name="hpe-proliant-dl20-back-panel"></a>Pannello indietro di HPE ProLiant DL20

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl20-back-panel-v2.png" alt-text="Pannello indietro di HPE ProLiant DL20.":::

### <a name="enable-remote-access-and-update-the-password"></a>Abilitare l'accesso remoto e aggiornare la password

Usare la procedura seguente per configurare le opzioni di rete e aggiornare la password predefinita.

Per abilitare e aggiornare la password:

1. Connettere uno schermo e una tastiera all'appliance HP, accendere l'appliance e premere **F9.**

    :::image type="content" source="media/tutorial-install-components/hpe-proliant-screen-v2.png" alt-text="Screenshot che mostra la finestra HPE ProLiant.":::

1. Passare a **System Utilities**  >  **System Configuration** iLO 5 Configuration Utility Network Options (Opzioni di rete dell'utilità di configurazione  >  **iLO 5).**  >  

    :::image type="content" source="media/tutorial-install-components/system-configuration-window-v2.png" alt-text="Screenshot che mostra la finestra Configurazione di sistema.":::

    1.  Selezionare **Porta di rete condivisa-LOM** nel campo Scheda di interfaccia **di** rete.
    
    1.  Disabilitare DHCP.
    
    1.  Immettere l'indirizzo IP, subnet mask e l'indirizzo IP del gateway.

1. Selezionare **F10: Salva**.

1. Selezionare **ESC** per tornare all'utilità di configurazione **iLO 5** e quindi selezionare **Gestione utenti.**

1. Selezionare **Modifica/Rimuovi utente.** L'amministratore è l'unico utente predefinito definito. 

1. Modificare la password predefinita e selezionare **F10: Salva**.

### <a name="configure-the-hpe-bios"></a>Configurare il BIOS HPE

La procedura seguente descrive come configurare il BIOS HPE per le appliance enterprise e SMB.

Per configurare il BIOS HPE:

1. Selezionare **System Utilities** System  >  **Configuration**  >  **BIOS/Platform Configuration (RBSU)**.

1. Nel modulo **BIOS/Platform Configuration (RBSU)** selezionare **Opzioni di avvio**.

1. Impostare **modalità di avvio** in modalità **BIOS legacy** e quindi selezionare **F10: Salva**.

1. Selezionare **Esc due** volte per chiudere il modulo Configurazione **di** sistema.

#### <a name="for-the-enterprise-appliance"></a>Per l'appliance aziendale

1. Selezionare **Embedded RAID 1: HPE Smart Array P408i-a SR Gen 10**  >  **Array Configuration** Create  >  **Array**.

1. Nel modulo **Crea matrice** selezionare tutte le opzioni. Sono disponibili tre opzioni per **l'appliance Enterprise.**

#### <a name="for-the-smb-appliance"></a>Per l'appliance SMB

1. Selezionare **Embedded RAID 1: HPE Smart Array P208i-a SR Gen 10**  >  **Array Configuration** Create  >  **Array**.

1. Selezionare **Passa al modulo successivo**.

1. Nel modulo **Imposta livello RAID** impostare il livello su RAID **5** per le distribuzioni aziendali e **RAID 1** per le distribuzioni SMB.

1. Selezionare **Passa al modulo successivo**.

1. Nel modulo **Etichetta unità** logica immettere Unità **logica 1.**

1. Selezionare **Invia modifiche**.

1. Nel modulo **Invia** selezionare **Torna al menu principale**.

1. Premere **F10: Salva e** quindi premere ESC **due** volte.

1. Nella finestra **Utilità di** sistema selezionare Menu di avvio una **sola volta**.

1. Nel modulo **One-Time Boot Menu (Menu** di avvio una sola volta) selezionare **Legacy BIOS One-Time Boot Menu (Menu di avvio legacy).**

1. Vengono **visualizzate le finestre Avvio in legacy** e **Override** di avvio. Scegliere un'opzione di sostituzione di avvio. ad esempio in una shell CD-ROM, USB, HDD o UEFI.

    :::image type="content" source="media/tutorial-install-components/boot-override-window-one-v2.png" alt-text="Screenshot che mostra la prima finestra Di sostituzione di avvio.":::

    :::image type="content" source="media/tutorial-install-components/boot-override-window-two-v2.png" alt-text="Screenshot che mostra la seconda finestra Di sostituzione di avvio.":::
### <a name="software-installation-hpe-proliant-dl20-appliance"></a>Installazione del software (appliance HPE ProLiant DL20)

Il processo di installazione richiede circa 20 minuti. Dopo l'installazione, il sistema viene riavviato più volte.

Per installare il software:

1. Connettere la schermata e la tastiera all'appliance e quindi connettersi all'interfaccia della riga di comando.

1. Connettere un CD o un disco esterno nella chiave con l'immagine ISO scaricata dalla **pagina Aggiornamenti** del portale di Defender per IoT.

1. Avviare l'appliance.

1. Selezionare **Inglese.**

    :::image type="content" source="media/tutorial-install-components/select-english-screen.png" alt-text="Selezione dell'inglese nella finestra dell'interfaccia della riga di comando.":::

1. Selezionare **SENSOR-RELEASE- <version> Enterprise.**

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Screenshot della schermata per la selezione di una versione.":::

1. Nell'Installazione guidata definire il profilo hardware e le proprietà di rete:

    :::image type="content" source="media/tutorial-install-components/installation-wizard-screen-v2.png" alt-text="Screenshot che mostra l'Installazione guidata.":::

    | Parametro | Configurazione |
    | ----------| ------------- |
    | **Profilo hardware** | Selezionare **Enterprise** o **Office per** le distribuzioni SMB. |
    | **Interfaccia di gestione** | **eno2** |
    | **Parametri di rete predefiniti (in genere i parametri vengono forniti dal cliente)** | **indirizzo IP della rete di gestione:** <br/> <br/>**nome host dell'appliance:** <br/>**Dns:** <br/>**l'indirizzo IP del gateway predefinito:**|
    | **Interfacce di input:** | Il sistema genera automaticamente l'elenco di interfacce di input.<br/><br/>Per eseguire il mirroring delle interfacce di input, copiare tutti gli elementi presentati nell'elenco con un separatore virgola: **eno5, eno3, eno1, eno6, eno4**<br/><br/>**Per HPE DL20: Non elencare eno1, enp1s0f4u4 (interfacce iLo)**<br/><br/>**BRIDGE:** non è necessario configurare l'interfaccia del bridge. Questa opzione viene usata solo per casi d'uso speciali. Premere **INVIO** per continuare. |

1. Dopo circa 10 minuti, vengono visualizzati i due set di credenziali. Uno è per un **utente di CyberX** e uno per un **utente del** supporto.

1. Salvare l'ID e le password dell'appliance. Saranno necessarie le credenziali per accedere alla piattaforma per la prima volta.

1. Selezionare **INVIO** per continuare.

## <a name="hpe-proliant-dl360-installation"></a>Installazione di HPE ProLiant DL360

  - Viene fornito un utente amministratore predefinito. È consigliabile modificare la password durante la configurazione di rete.

  - Durante la configurazione di rete, si configurerà la porta iLO.

  - Il processo di installazione richiede circa 20 minuti. Dopo l'installazione, il sistema viene riavviato più volte.

### <a name="hpe-proliant-dl360-front-panel"></a>Pannello anteriore di HPE ProLiant DL360

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl360-front-panel.png" alt-text="Pannello anteriore di HPE ProLiant DL360.":::

### <a name="hpe-proliant-dl360-back-panel"></a>Pannello indietro di HPE ProLiant DL360

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl360-back-panel.png" alt-text="Pannello indietro di HPE ProLiant DL360.":::

### <a name="enable-remote-access-and-update-the-password"></a>Abilitare l'accesso remoto e aggiornare la password

Vedere le sezioni precedenti per l'installazione di HPE ProLiant DL20:

  - "Abilitare l'accesso remoto e aggiornare la password"

  - "Configurare HPE BIOS"

La configurazione aziendale è identica.

> [!Note]
> Nel modulo matrice verificare di aver selezionato tutte le opzioni.

### <a name="ilo-remote-installation-from-a-virtual-drive"></a>Installazione remota iLO (da un'unità virtuale)

Questa procedura descrive l'installazione di iLO da un'unità virtuale.

Per effettuare l'installazione:

1. Accedere alla console iLO e quindi fare clic con il pulsante destro del mouse sulla schermata dei server.

1. Selezionare **HTML5 Console (Console HTML5).**

1. Nella console selezionare l'icona CD e scegliere l'opzione CD/DVD.

1. Selezionare **File ISO locale**.

1. Nella finestra di dialogo scegliere il file ISO pertinente.

1. Passare all'icona a sinistra, selezionare **Alimentazione** e quindi fare clic su **Reimposta**.

1. L'appliance verrà riavviata ed eseguirà il processo di installazione del sensore.

### <a name="software-installation-hpe-dl360"></a>Installazione del software (HPE DL360)

Il processo di installazione richiede circa 20 minuti. Dopo l'installazione, il sistema viene riavviato più volte.

Per effettuare l'installazione:

1. Connettere lo schermo e la tastiera all'appliance e quindi connettersi all'interfaccia della riga di comando.

1. Connettere un CD o un disco esterno in una chiave con l'immagine ISO scaricata dalla pagina **Aggiornamenti** del portale di Defender per IoT.

1. Avviare l'appliance.

1. Selezionare **Inglese.**

1. Selezionare **SENSOR-RELEASE- <version> Enterprise.**

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Screenshot che mostra la selezione della versione.":::

1. Nell'Installazione guidata definire il profilo dell'appliance e le proprietà di rete.

    :::image type="content" source="media/tutorial-install-components/installation-wizard-screen-v2.png" alt-text="Screenshot che mostra l'Installazione guidata.":::

    | Parametro | Configurazione |
    | ----------| ------------- |
    | **Profilo hardware** | Selezionare **aziendale**. |
    | **Interfaccia di gestione** | **eno2** |
    | **Parametri di rete predefiniti (forniti dal cliente)** | **indirizzo IP di rete di gestione:** <br/>**subnet mask:** <br/>**nome host dell'appliance:** <br/>**Dns:** <br/>**l'indirizzo IP del gateway predefinito:**|
    | **Interfacce di input:**  | Il sistema genera automaticamente un elenco di interfacce di input.<br/><br/>Per eseguire il mirroring delle interfacce di input, copiare tutti gli elementi presentati nell'elenco con un separatore virgola.<br/><br/> Non è necessario configurare l'interfaccia del bridge. Questa opzione viene usata solo per casi d'uso speciali. |

1. Dopo circa 10 minuti, vengono visualizzati i due set di credenziali. Uno è per un **utente CyberX** e uno per un utente **del** supporto.

1. Salvare l'ID e le password dell'appliance. Queste credenziali saranno necessarie per accedere alla piattaforma per la prima volta.

1. Premere **INVIO** per continuare.

## <a name="sensor-installation-for-the-virtual-appliance"></a>Installazione del sensore per l'appliance virtuale

È possibile distribuire la macchina virtuale per il sensore Defender per IoT nelle architetture seguenti:


| Architettura | Specifiche | Utilizzo | Commenti |
|---|---|---|---|
| **Funzionalità per le aziende** | CPU: 8<br/>Memoria: 32 G di RAM<br/>HDD: 1800 GB | Ambiente di produzione | Impostazione predefinita e più comune |
| **Piccole imprese** | CPU: 4 <br/>Memoria: 8G di RAM<br/>HDD: 500 GB | Ambienti di test o di produzione di piccole dimensioni | -  |
| **Office** | CPU: 4<br/>Memoria: 8G di RAM<br/>HDD: 100 GB | Ambienti di test di piccole dimensioni | -  |

### <a name="prerequisites"></a>Prerequisiti

La console di gestione locale supporta entrambe le opzioni di distribuzione VMware e Hyper-V. Prima di iniziare l'installazione, assicurarsi di avere gli elementi seguenti:

  - VMware (ESXi 5.5 o versione successiva) o hypervisor Hyper-V (Windows 10 Pro o Enterprise) installato e operativo

  - Risorse hardware disponibili per la macchina virtuale

  - File di installazione ISO per il sensore Azure Defender per IoT

Assicurarsi che l'hypervisor sia in esecuzione.

### <a name="create-the-virtual-machine-esxi"></a>Creare la macchina virtuale (ESXi)

1. Accedere a ESXi, scegliere **l'archivio dati pertinente** e selezionare **Datastore Browser**.

1. **Caricare** l'immagine e selezionare **Chiudi**.

1. Passare a **Macchine virtuali** e quindi selezionare **Crea/Registra macchina virtuale.**

1. Selezionare **Crea nuova macchina virtuale** e quindi selezionare **Avanti.**

1. Aggiungere un nome di sensore e scegliere:

   - Compatibilità: **&lt; versione più &gt; recente di ESXi**

   - Famiglia di sistemi operativi guest: **Linux**

   - Versione del sistema operativo guest: **Ubuntu Linux (64 bit)**

1. Selezionare **Avanti**.

1. Scegliere l'archivio dati pertinente e selezionare **Avanti.**

1. Modificare i parametri dell'hardware virtuale in base all'architettura richiesta.

1. Per **Unità CD/DVD 1** selezionare **File ISO** dell'archivio dati e scegliere il file ISO caricato in precedenza.

1. Selezionare **Next** > **Finish** (Avanti > Fine).

### <a name="create-the-virtual-machine-hyper-v"></a>Creare la macchina virtuale (Hyper-V)

Questa procedura descrive come creare una macchina virtuale usando Hyper-V.

Per creare una macchina virtuale:

1. Creare un disco virtuale nella console di gestione di Hyper-V.

1. Selezionare **format = VHDX**.

1. Selezionare **type = Dynamic Expanding**.

1. Immettere il nome e il percorso del disco rigido virtuale.

1. Immettere le dimensioni necessarie (in base all'architettura).   

1. Rivedere il riepilogo e selezionare **Fine**.

1. Nel menu **Azioni** creare una nuova macchina virtuale.

1. Immettere un nome per la macchina virtuale.

1. Selezionare **Specifica generazione**  >  **1.**

1. Specificare l'allocazione di memoria (in base all'architettura) e selezionare la casella di controllo per la memoria dinamica.

1. Configurare l'adattatore di rete in base alla topologia di rete del server.

1. Connettere il file VHDX creato in precedenza alla macchina virtuale.

1. Rivedere il riepilogo e selezionare **Fine**.

1. Fare clic con il pulsante destro del mouse sulla nuova macchina virtuale e **scegliere Impostazioni**.

1. Selezionare **Aggiungi hardware e** aggiungere una nuova scheda di rete.

1. Selezionare il commutatore virtuale che si connetterà alla rete di gestione dei sensori.

1. Allocare risorse CPU (in base all'architettura).

1. Connettere l'immagine ISO della console di gestione a un'unità DVD virtuale.

1. Avviare la macchina virtuale.

2. Scegliere **Connetti** dal menu Azioni **per** continuare l'installazione del software.

### <a name="software-installation-esxi-and-hyper-v"></a>Installazione del software (ESXi e Hyper-V)

Questa sezione descrive l'installazione del software ESXi e Hyper-V.

Per effettuare l'installazione:

1. Aprire la console della macchina virtuale.

1. La macchina virtuale verrà avviata dall'immagine ISO e verrà visualizzata la schermata di selezione della lingua. Selezionare **Inglese.**

1. Selezionare l'architettura richiesta.

1. Definire il profilo dell'appliance e le proprietà di rete:

    | Parametro | Configurazione |
    | ----------| ------------- |
    | **Profilo hardware** | &lt;architettura richiesta&gt; |
    | **Interfaccia di gestione** | **ens192** |
    | **Parametri di rete (forniti dal cliente)** | **indirizzo IP della rete di gestione:** <br/>**subnet mask:** <br/>**nome host dell'appliance:** <br/>**Dns:** <br/>**gateway predefinito:** <br/>**Interfacce di input:**|
    | **Interfacce bridge:** | Non è necessario configurare l'interfaccia del bridge. Questa opzione è disponibile solo per casi d'uso speciali. |

1. Immettere **Y** per accettare le impostazioni.

1. Le credenziali di accesso vengono generate e presentate automaticamente. Copiare il nome utente e la password in un luogo sicuro, perché sono necessari per l'accesso e l'amministrazione.

      - **Supporto:** utente amministratore per la gestione degli utenti.

      - **CyberX:** equivalente alla radice per l'accesso all'appliance.

1. L'appliance viene riavviata.

1. Accedere alla console di gestione tramite l'indirizzo IP configurato in precedenza: `https://ip_address` .

    :::image type="content" source="media/tutorial-install-components/defender-for-iot-sign-in-screen.png" alt-text="Screenshot che mostra l'accesso alla console di gestione.":::

## <a name="on-premises-management-console-installation"></a>Installazione della console di gestione locale

Prima di installare il software nell'appliance, è necessario modificare la configurazione DEL BIOS dell'appliance:

### <a name="bios-configuration"></a>Configurazione del BIOS

Per configurare il BIOS per l'appliance:

1. [Abilitare l'accesso remoto e aggiornare la password](#enable-remote-access-and-update-the-password).

1. [Configurare il BIOS](#configure-the-hpe-bios).

### <a name="software-installation"></a>Installazione del software

Il processo di installazione richiede circa 20 minuti. Dopo l'installazione, il sistema viene riavviato più volte. 

Durante il processo di installazione, è possibile aggiungere una scheda di interfaccia di rete secondaria. Se si sceglie di non installare la scheda di interfaccia di rete secondaria durante l'installazione, è possibile aggiungere una scheda di interfaccia [di](#add-a-secondary-nic) rete secondaria in un secondo momento. 

Per installare il software:

1. Selezionare la lingua preferita per il processo di installazione.

   :::image type="content" source="media/tutorial-install-components/on-prem-language-select.png" alt-text="Selezionare la lingua preferita per il processo di installazione.":::     

1. Selezionare **MANAGEMENT-RELEASE- \<version\> \<deployment type\>**.

   :::image type="content" source="media/tutorial-install-components/on-prem-install-screen.png" alt-text="Selezionare la versione.":::   

1. Nell'Installazione guidata definire le proprietà di rete:

   :::image type="content" source="media/tutorial-install-components/on-prem-first-steps-install.png" alt-text="Screenshot che mostra il profilo dell'appliance.":::   

   | Parametro | Configurazione |
   |--|--|
   | **configurare l'interfaccia di rete di gestione** | Per Dell: **eth0, eth1** <br /> Per HP: **enu1, enu2** <br /> oppure <br />**valore possibile** |
   | **configurare l'indirizzo IP di rete di gestione:** | **Indirizzo IP fornito dal cliente** |
   | **configurare subnet mask:** | **Indirizzo IP fornito dal cliente** |
   | **configurare DNS:** | **Indirizzo IP fornito dal cliente** |
   | **configurare l'indirizzo IP del gateway predefinito:** | **Indirizzo IP fornito dal cliente** |
   
1. **(Facoltativo)** Se si vuole installare una scheda di interfaccia di rete (NIC) secondaria, definire il profilo dell'appliance e le proprietà di rete seguenti:

    :::image type="content" source="media/tutorial-install-components/on-prem-secondary-nic-install.png" alt-text="Screenshot che mostra le domande di installazione della scheda di interfaccia di rete secondaria.":::

   | Parametro | Configurazione |
   |--|--|
   | **Configurare l'interfaccia di monitoraggio dei sensori (facoltativo):** | **eth1** o **valore possibile** |
   | **configurare un indirizzo IP per l'interfaccia di monitoraggio del sensore:** | **Indirizzo IP fornito dal cliente** |
   | **configurare un subnet mask per l'interfaccia di monitoraggio dei sensori:** | **Indirizzo IP fornito dal cliente** |

1. Accettare i settling e continuare digitando `Y` . 

1. Dopo circa 10 minuti, vengono visualizzati i due set di credenziali. Uno è per un **utente di CyberX** e uno per un **utente del** supporto.

   :::image type="content" source="media/tutorial-install-components/credentials-screen.png" alt-text="Copiare queste credenziali perché non verranno presentate di nuovo.":::  

   Salvare i nomi utente e le password, queste credenziali saranno necessarie per accedere alla piattaforma la prima volta che la si usa.

1. Selezionare **INVIO** per continuare.

Per informazioni su come trovare la porta fisica nell'appliance, vedere [Trovare la porta](#find-your-port).

### <a name="add-a-secondary-nic"></a>Aggiungere una scheda di interfaccia di rete secondaria

È possibile migliorare la sicurezza della console di gestione locale aggiungendo una scheda di interfaccia di rete secondaria. Aggiungendo una scheda di interfaccia di rete secondaria, si avrà una scheda di interfaccia di rete dedicata per gli utenti e l'altra supporterà la configurazione di un gateway per le reti instradate. La seconda scheda di interfaccia di rete è dedicata a tutti i sensori collegati all'interno di un intervallo di indirizzi IP.

Entrambe le schede di interfaccia di rete hanno l'interfaccia utente abilitata. Quando il routing non è necessario, tutte le funzionalità supportate dall'interfaccia utente saranno disponibili nella scheda di interfaccia di rete secondaria. La disponibilità elevata verrà eseguita nella scheda di interfaccia di rete secondaria.

Se si sceglie di non distribuire una scheda di interfaccia di rete secondaria, tutte le funzionalità saranno disponibili tramite la scheda di interfaccia di rete primaria. 

Se la console di gestione locale è già stata configurata e si vuole aggiungere una scheda di interfaccia di rete secondaria alla console di gestione locale, seguire questa procedura:

1. Usare il comando network reconfigure:

    ```bash
    sudo cyberx-management-network-reconfigure
    ```

1. Immettere le risposte seguenti alle domande seguenti:

    :::image type="content" source="media/tutorial-install-components/network-reconfig-command.png" alt-text="Immettere le risposte seguenti per configurare l'appliance.":::

    | Parametri | Risposta da immettere |
    |--|--|
    | **Indirizzo IP della rete di gestione** | `N` |
    | **Subnet mask** | `N` |
    | **DNS** | `N` |
    | **Indirizzo IP del gateway predefinito** | `N` |
    | **Interfaccia di monitoraggio dei sensori (facoltativo. Applicabile quando i sensori si applicano a un segmento di rete diverso. Per altre informazioni, vedere le istruzioni di installazione.**| `Y`, **selezionare un valore possibile** |
    | **Un indirizzo IP per l'interfaccia di monitoraggio dei sensori (accessibile dai sensori)** | `Y`, **indirizzo IP fornito dal cliente**|
    | **Un subnet mask per l'interfaccia di monitoraggio dei sensori (accessibile dai sensori)** | `Y`, **indirizzo IP fornito dal cliente** |
    | **Hostname** | **fornito dal cliente** |

1. Esaminare tutte le opzioni e immettere `Y` per accettare le modifiche. Il sistema viene riavviato.

### <a name="find-your-port"></a>Trovare la porta

Se si verificano problemi durante l'individuazione della porta fisica nel dispositivo, è possibile usare il comando seguente per:

```bash
sudo ethtool -p <port value> <time-in-seconds>
```

Questo comando farà lampeggiare la luce sulla porta per il periodo di tempo specificato. Ad esempio, se si immette , la porta eno1 flash per 2 minuti consente di trovare la porta sul `sudo ethtool -p eno1 120` retro dell'appliance. 

## <a name="virtual-appliance-on-premises-management-console-installation"></a>Appliance virtuale: installazione della console di gestione locale

La macchina virtuale della console di gestione locale supporta le architetture seguenti:

| Architettura | Specifiche | Utilizzo | 
|--|--|--|
| Enterprise <br/>(Impostazione predefinita e più comune) | CPU: 8 <br/>Memoria: 32G di RAM<br/> HDD: 1,8 TB | Ambienti di produzione di grandi dimensioni | 
| Enterprise | CPU: 4 <br/> Memoria: 8G di RAM<br/> HDD: 500 GB | Ambienti di produzione di grandi dimensioni |
| Enterprise | CPU: 4 <br/>Memoria: 8G di RAM <br/> HDD: 100 GB | Ambienti di test di piccole dimensioni | 
   
### <a name="prerequisites"></a>Prerequisiti

La console di gestione locale supporta entrambe le opzioni di distribuzione VMware e Hyper-V. Prima di iniziare l'installazione, verificare quanto segue:

- VMware (ESXi 5.5 o versione successiva) o hypervisor Hyper-V (Windows 10 Pro o Enterprise) è installato e operativo.

- Le risorse hardware sono disponibili per la macchina virtuale.

- Si dispone del file di installazione ISO per la console di gestione locale.
    
- L'hypervisor è in esecuzione.

### <a name="create-the-virtual-machine-esxi"></a>Creare la macchina virtuale (ESXi)

Per creare una macchina virtuale (ESXi):

1. Accedere a ESXi, scegliere l'archivio dati **pertinente** e selezionare **Datastore Browser (Browser archivio dati).**

1. Caricare l'immagine e selezionare **Chiudi.**

1. Passare a **Macchine virtuali.**

1. Selezionare **Create/Register VM (Crea/Registra macchina virtuale).**

1. Selezionare **Crea nuova macchina virtuale** e quindi **Avanti.**

1. Aggiungere un nome di sensore e scegliere:

   - Compatibilità: \<latest ESXi version>

   - Famiglia di sistemi operativi guest: Linux

   - Versione del sistema operativo guest: Ubuntu Linux (64 bit)

1. Selezionare **Avanti**.

1. Scegliere l'archivio dati pertinente e selezionare **Avanti.**

1. Modificare i parametri dell'hardware virtuale in base all'architettura richiesta.

1. Per **Unità CD/DVD 1** selezionare **File ISO** dell'archivio dati e scegliere il file ISO caricato in precedenza.

1. Selezionare **Next** > **Finish** (Avanti > Fine).

### <a name="create-the-virtual-machine-hyper-v"></a>Creare la macchina virtuale (Hyper-V)

Per creare una macchina virtuale usando Hyper-V:

1. Creare un disco virtuale nella console di gestione di Hyper-V.

1. Selezionare il formato **VHDX**.

1. Selezionare **Avanti**.

1. Selezionare il tipo **Espansione dinamica**.

1. Selezionare **Avanti**.

1. Immettere il nome e il percorso per il disco rigido virtuale.

1. Selezionare **Avanti**.

1. Immettere le dimensioni richieste (in base all'architettura).

1. Selezionare **Avanti**.

1. Rivedere il riepilogo e selezionare **Fine**.

1. Nel menu **Azioni** creare una nuova macchina virtuale.

1. Selezionare **Avanti**.

1. Immettere un nome per la macchina virtuale.

1. Selezionare **Avanti**.

1. Selezionare **Generazione** e impostarlo su **Generazione 1.**

1. Selezionare **Avanti**.

1. Specificare l'allocazione di memoria (in base all'architettura) e selezionare la casella di controllo per la memoria dinamica.

1. Selezionare **Avanti**.

1. Configurare l'adattatore di rete in base alla topologia di rete del server.

1. Selezionare **Avanti**.

1. Connettere il file VHDX creato in precedenza alla macchina virtuale.

1. Selezionare **Avanti**.

1. Rivedere il riepilogo e selezionare **Fine**.

1. Fare clic con il pulsante destro del mouse sulla nuova macchina virtuale e quindi scegliere **Impostazioni**.

1. Selezionare **Aggiungi hardware e** aggiungere una nuova scheda per Scheda di **rete**.

1. Per **Commutatore virtuale** selezionare il commutatore che si connetterà alla rete di gestione dei sensori.

1. Allocare risorse CPU (in base all'architettura).

1. Connettere l'immagine ISO della console di gestione a un'unità DVD virtuale.

1. Avviare la macchina virtuale.

1. Scegliere **Connetti** dal menu Azioni **per** continuare l'installazione del software.

### <a name="software-installation-esxi-and-hyper-v"></a>Installazione software (ESXi e Hyper-V)

L'avvio della macchina virtuale avvia il processo di installazione dall'immagine ISO.

Per installare il software:

1. Selezionare **Inglese.**

1. Selezionare l'architettura necessaria per la distribuzione.

1. Definire l'interfaccia di rete per la rete di gestione dei sensori: interfaccia, IP, subnet, server DNS e gateway predefinito.

1. Le credenziali di accesso vengono generate automaticamente. Salvare il nome utente e le password. Queste credenziali saranno necessarie per accedere alla piattaforma la prima volta che la si usa.

   L'appliance verrà quindi riavviata.

1. Accedere alla console di gestione tramite l'indirizzo IP configurato in precedenza: `<https://ip_address>` .

    :::image type="content" source="media/tutorial-install-components/defender-for-iot-management-console-sign-in-screen.png" alt-text="Screenshot che mostra la schermata di accesso della console di gestione.":::

## <a name="post-installation-validation"></a>Convalida post-installazione

Per convalidare l'installazione di un'appliance fisica, è necessario eseguire molti test. Lo stesso processo di convalida si applica a tutti i tipi di appliance.

Eseguire la convalida usando l'interfaccia utente grafica o l'interfaccia della riga di comando. La convalida è disponibile per l'utente **Supporto** tecnico e l'utente **CyberX**.

La convalida post-installazione deve includere i test seguenti:

  - **Test di sanità:** verificare che il sistema sia in esecuzione.

  - **Versione:** verificare che la versione sia corretta.

  - **ifconfig:** verificare che tutte le interfacce di input configurate durante il processo di installazione siano in esecuzione.

### <a name="checking-system-health-by-using-the-gui"></a>Controllo dell'integrità del sistema tramite l'interfaccia utente grafica

:::image type="content" source="media/tutorial-install-components/system-health-check-screen.png" alt-text="Screenshot che mostra il controllo dell'integrità del sistema.":::

#### <a name="sanity"></a>Sanità mentale

- **Appliance**: esegue il controllo di sanità dell'appliance. È possibile eseguire lo stesso controllo usando il comando dell'interfaccia della riga di comando `system-sanity` .

- **Versione**: visualizza la versione dell'appliance.

- **Proprietà di rete**: visualizza i parametri di rete del sensore.

#### <a name="redis"></a>Redis

- **Memoria**: fornisce il quadro generale dell'utilizzo della memoria, ad esempio la quantità di memoria usata e la quantità di memoria rimasta.

- **Chiave più lunga:** visualizza le chiavi più lunghe che potrebbero causare un utilizzo elevato della memoria.

#### <a name="system"></a>Sistema

- **Log principale**: fornisce le ultime 500 righe del log principale, consentendo di visualizzare le righe del log recenti senza esportare l'intero log di sistema.

- **Gestione attività**: converte le attività visualizzate nella tabella dei processi nei livelli seguenti: 
  
  - Livello persistente (Redis) 
  - Livello cassa (SQL)

- **Statistiche di rete**: visualizza le statistiche di rete.

- **TOP:** mostra la tabella dei processi. Si tratta di un comando Linux che fornisce una visualizzazione dinamica in tempo reale del sistema in esecuzione.

- **Controllo memoria backup**: fornisce lo stato della memoria di backup, controllando quanto segue:
  - Percorso della cartella di backup 
  - Dimensioni della cartella di backup
  - Limitazioni della cartella di backup
  - Data e ora dell'ultimo backup
  - Quantità di spazio disponibile per i file di backup aggiuntivi

- **ifconfig:** visualizza i parametri per le interfacce fisiche dell'appliance.

- **CyberX nload:** visualizza il traffico di rete e la larghezza di banda usando i test di sei secondi.

- **Errors from Core, log**(Errori da Core, log): visualizza gli errori del file di log principale.

Per accedere a questo strumento:

1. Accedere al sensore con le credenziali **utente** del supporto.

1. Selezionare **Statistiche di sistema** nella finestra Impostazioni **di** sistema.

    :::image type="icon" source="media/tutorial-install-components/system-statistics-icon.png" border="false":::

### <a name="checking-system-health-by-using-the-cli"></a>Controllo dell'integrità del sistema tramite l'interfaccia della riga di comando

**Test 1: Sanity**

Verificare che il sistema sia operativo:

1. Connettersi all'interfaccia della riga di comando con il terminale Linux (ad esempio PuTTY) e l'utente **Support .**

1. Immettere `system sanity`.

1. Verificare che tutti i servizi siano verdi (in esecuzione).

    :::image type="content" source="media/tutorial-install-components/support-screen.png" alt-text="Screenshot che mostra i servizi in esecuzione.":::

1. Verificare che **System is UP! (prod)** viene visualizzato nella parte inferiore.

**Test 2: Controllo della versione**

Verificare che sia usata la versione corretta:

1. Connettersi all'interfaccia della riga di comando con il terminale Linux (ad esempio PuTTY) e l'utente **Support .**

1. Immettere `system version`.

1. Verificare che venga visualizzata la versione corretta.

**Test 3: Convalida della rete**

Verificare che tutte le interfacce di input configurate durante il processo di installazione siano in esecuzione:

1. Connettersi all'interfaccia della riga di comando con il terminale Linux (ad esempio, PuTTY) e l'utente **Support**.

1. Immettere `network list` (l'equivalente del comando Linux `ifconfig` ).

1. Verificare che vengano visualizzate le interfacce di input necessarie. Ad esempio, se sono installate due schede di interfaccia di rete Quad Copper, nell'elenco dovrebbero essere presenti 10 interfacce.

    :::image type="content" source="media/tutorial-install-components/interface-list-screen.png" alt-text="Screenshot che mostra l'elenco di interfacce.":::

**Test 4: Gestione dell'accesso all'interfaccia utente**

Verificare che sia possibile accedere all'interfaccia utente grafica Web della console:

1. Connettere un portatile con un cavo Ethernet alla porta di gestione (**Gb1**).

1. Definire l'indirizzo NIC del portatile in modo che sia nello stesso intervallo dell'appliance.

    :::image type="content" source="media/tutorial-install-components/access-to-ui.png" alt-text="Screenshot che mostra l'accesso di gestione all'interfaccia utente.":::

1. Eseguire il ping dell'indirizzo IP dell'appliance dal portatile per verificare la connettività (impostazione predefinita: 10.100.10.1).

1. Aprire il browser Chrome nel portatile e immettere l'indirizzo IP dell'appliance.

1. Nella finestra **La connessione non è privata** selezionare **Avanzate** e procedere.

1. Il test ha esito positivo quando viene visualizzata la schermata di accesso di Defender per IoT.

   :::image type="content" source="media/tutorial-install-components/defender-for-iot-sign-in-screen.png" alt-text="Screenshot che mostra l'accesso alla console di gestione.":::

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="you-cant-connect-by-using-a-web-interface"></a>Non è possibile connettersi tramite un'interfaccia Web

1. Verificare che il computer che si sta tentando di connettersi si trova nella stessa rete dell'appliance.

1. Verificare che la rete GUI sia connessa alla porta di gestione.

1. Eseguire il ping dell'indirizzo IP dell'appliance. Se non è presente alcun ping:

   1. Connettere un monitor e una tastiera all'appliance.

   1. Usare **l'utente** e la password del supporto per accedere.

   1. Usare il comando `network list` per visualizzare l'indirizzo IP corrente.

      :::image type="content" source="media/tutorial-install-components/network-list.png" alt-text="Screenshot che mostra l'elenco di reti.":::

1. Se i parametri di rete non sono configurati correttamente, usare la procedura seguente per modificarli:

   1. Usare il comando `network edit-settings` .

   1. Per modificare l'indirizzo IP della rete di gestione, selezionare **Y**.

   1. Per modificare il subnet mask, selezionare **Y.**

   1. Per modificare il DNS, selezionare **Y.**

   1. Per modificare l'indirizzo IP del gateway predefinito, selezionare **Y.**

   1. Per la modifica dell'interfaccia di input (solo sensore), selezionare **N**.

   1. Per applicare le impostazioni, selezionare **Y.**

1. Dopo il riavvio, connettersi con le credenziali utente di supporto e usare il `network list` comando per verificare che i parametri siano stati modificati.

1. Provare di nuovo a eseguire il ping e a connettersi dall'interfaccia utente grafica.

### <a name="the-appliance-isnt-responding"></a>L'appliance non risponde

1. Connettere un monitor e una tastiera all'appliance oppure usare PuTTY per connettersi in remoto all'interfaccia della riga di comando.

1. Usare le **credenziali dell'utente** del supporto per accedere.

1. Usare il `system sanity` comando e verificare che tutti i processi siano in esecuzione.

    :::image type="content" source="media/tutorial-install-components/system-sanity-screen.png" alt-text="Screenshot che mostra il comando di sanità del sistema.":::

Per eventuali altri problemi, contattare [Supporto tecnico Microsoft](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

## <a name="appendix-a-mirroring-port-on-vswitch-esxi"></a>Appendice A: Porta di mirroring in vSwitch (ESXi)

### <a name="configure-a-span-port-on-an-existing-vswitch"></a>Configurare una porta SPAN in un vSwitch esistente

Un vSwitch non dispone di funzionalità di mirroring, ma è possibile usare una soluzione alternativa per implementare una porta SPAN.

Per configurare una porta SPAN:

1. Aprire le proprietà vSwitch.

1. Selezionare **Aggiungi**.

1. Selezionare **Macchina virtuale**  >  **successiva.**

1. Inserire un'etichetta di rete **SPAN Network,** selezionare **VLAN ID**  >  **All** e quindi **selezionare Avanti.**

1. Selezionare **Fine**.

1. Selezionare **SPAN Network** > * Edit *(Modifica).*

1. Selezionare **Sicurezza** e verificare che il **criterio Modalità promiscua** sia impostato su **Modalità accetta.**

1. Selezionare **OK** e quindi Chiudi **per** chiudere le proprietà di vSwitch.

1. Aprire le **proprietà della macchina virtuale XSense.**

1. Per **Scheda di rete 2** selezionare la rete **SPAN.**

1. Selezionare **OK**.

1. Connettersi al sensore e verificare che il mirroring funzioni.

## <a name="appendix-b-access-sensors-from-the-on-premises-management-console"></a>Appendice B: Accedere ai sensori dalla console di gestione locale

È possibile migliorare la sicurezza del sistema impedendo l'accesso diretto degli utenti al sensore. Usare invece il tunneling proxy per consentire agli utenti di accedere al sensore dalla console di gestione locale con una singola regola del firewall. Questa tecnica limita la possibilità di accesso non autorizzato all'ambiente di rete oltre il sensore. L'esperienza dell'utente durante l'accesso al sensore rimane invariata.

:::image type="content" source="media/tutorial-install-components/sensor-system-graph.png" alt-text="Screenshot che mostra l'accesso al sensore.":::

Per abilitare il tunneling:

1. Accedere all'interfaccia della riga di comando della console di gestione locale con **CyberX** o **supportare** le credenziali utente.

1. Immettere `sudo cyberx-management-tunnel-enable`.

1. Premere **INVIO**.

1. Immettere `--port 10000`.

### <a name="next-steps"></a>Passaggi successivi

[Configurare la rete](how-to-set-up-your-network.md)