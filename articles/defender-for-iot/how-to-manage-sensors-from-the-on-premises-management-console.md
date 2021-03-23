---
title: Gestire i sensori dalla console di gestione locale
description: Informazioni su come gestire i sensori dalla console di gestione, incluso l'aggiornamento delle versioni dei sensori, il push delle impostazioni di sistema ai sensori e l'abilitazione e disabilitazione dei motori sui sensori.
ms.date: 12/07/2020
ms.topic: how-to
ms.openlocfilehash: 4706fbcf58b8e5f5eed6532f9a08f65c7716e07e
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781926"
---
# <a name="manage-sensors-from-the-management-console"></a>Gestire i sensori dalla console di gestione

Questo articolo descrive come gestire i sensori dalla console di gestione, tra cui:

- Push delle impostazioni di sistema ai sensori

- Abilitare e disabilitare i motori sui sensori

- Aggiornare le versioni del sensore

## <a name="push-configurations"></a>Configurazioni push

È possibile definire diverse impostazioni di sistema e applicarle automaticamente ai sensori connessi alla console di gestione. Questo consente di risparmiare tempo e di garantire impostazioni ottimizzate tra i sensori aziendali.

Dalla console di gestione è possibile definire le impostazioni di sistema del sensore seguenti:

- Server di posta elettronica

- Monitoraggio MIB SNMP

- Active Directory

- Impostazioni DNS

- Subnet

- Alias di porta

Per applicare le impostazioni di sistema:

1. Nel riquadro sinistro della console selezionare impostazioni di **sistema**.

2. Nel riquadro **Configura sensori** selezionare una delle opzioni.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-system-setting-options.png" alt-text="Opzioni di impostazione del sistema per un sensore.":::

   Nell'esempio seguente viene descritto come definire i parametri del server di posta elettronica per i sensori aziendali.

3. Selezionare **server di posta**.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-system-settings-screen.png" alt-text="Selezionare il server di posta dalla schermata Impostazioni sistema.":::

4. Selezionare un sensore a sinistra.

5. Impostare i parametri del server di posta e selezionare **duplicato**. Ogni elemento nell'albero del sensore viene visualizzato con una casella di controllo accanto.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/check-off-each-sensor.png" alt-text="Verificare che le caselle di controllo siano selezionate per i sensori.":::

6. Nell'albero dei sensori selezionare gli elementi a cui si desidera applicare la configurazione.

7. Selezionare **Salva**.

## <a name="update-versions"></a>Versioni di aggiornamento

È possibile aggiornare diversi sensori contemporaneamente dalla console di gestione locale.

Per aggiornare diversi sensori:

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Passa ad Azure Defender per l'it.

3. Passare alla pagina **aggiornamenti** .

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/update-screen.png" alt-text="Screenshot della visualizzazione dashboard degli aggiornamenti.":::

4. Selezionare **Scarica** dalla sezione **sensori** e salvare il file.

5. Accedere alla console di gestione di e selezionare **impostazioni di sistema**.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/admin-system-settings.png" alt-text="Screenshot del menu di amministrazione per selezionare le impostazioni di sistema.":::

6. Contrassegnare i sensori che si desidera aggiornare nella sezione di **configurazione del motore dei sensori** , quindi selezionare **aggiornamenti automatici**.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensors-select.png" alt-text="Due sensori che mostrano la modalità di apprendimento e gli aggiornamenti automatici.":::

7. Selezionare **Save changes** (Salva modifiche).

8. Nel riquadro **aggiornamento versione sensori** selezionare :::image type="icon" source="media/how-to-manage-sensors-from-the-on-premises-management-console/plus-icon.png" border="false"::: .

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/display-files.png" alt-text="Schermata di aggiornamento della versione del sensore per visualizzare i file.":::

9. Verrà visualizzata la finestra di dialogo **Carica file** . Caricare il file scaricato dalla pagina **aggiornamenti** .

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/upload-file.png" alt-text="Selezionare il pulsante Sfoglia per caricare il file.":::

10. Durante il processo di aggiornamento, lo stato di aggiornamento di ogni sensore viene visualizzato nella finestra di **gestione del sito** .

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/progress.png" alt-text="Osservare lo stato di avanzamento dell'aggiornamento.":::

## <a name="update-threat-intelligence-packages"></a>Aggiornare i pacchetti di intelligence per le minacce 

Il pacchetto di dati per l'Intelligence per le minacce viene fornito con ogni nuovo Defender per la versione dell'it o, se necessario, tra le versioni. Il pacchetto contiene le firme (incluse le firme di malware), CVEs e altri contenuti di sicurezza. 

È possibile caricare manualmente questo file dalla pagina degli **aggiornamenti** del portale di Defender per le cose e aggiornarlo automaticamente ai sensori. 

Per aggiornare i dati di intelligence per le minacce: 

1. Passare alla pagina Defender for Internet **Updates** . 

2. Scaricare e salvare il file.

3. Accedere alla console di gestione di. 

4. Nel menu laterale selezionare impostazioni di **sistema**. 

5. Selezionare i sensori che devono ricevere l'aggiornamento nella sezione di **configurazione del motore del sensore** .  

6. Nella sezione **selezionare i dati di intelligence** per le minacce selezionare il segno più ( **+** ). 

7. Caricare il pacchetto scaricato dalla pagina Defender for Internet **Updates** .

## <a name="understand-sensor-disconnection-events"></a>Informazioni sugli eventi di disconnessione del sensore

La finestra **Gestione sito** Visualizza le informazioni di disconnessione se i sensori si disconnette dalla console di gestione locale assegnata. Sono disponibili le informazioni di disconnessione del sensore seguenti:

- "La console di gestione locale non può elaborare i dati ricevuti dal sensore".

- "Tempo di Drift rilevato. La console di gestione locale è stata disconnessa dal sensore ".

- "Il sensore non comunica con la console di gestione locale. Controllare la connettività di rete o la convalida del certificato ".

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-system-settings-screen.png" alt-text="Screenshot della visualizzazione della zona 1.":::

È possibile inviare avvisi a terze parti con informazioni sui sensori disconnessi. Per ulteriori informazioni, vedere la pagina relativa agli [avvisi in](how-to-manage-individual-sensors.md#forward-sensor-failure-alerts)caso di errore del sensore.

## <a name="enable-or-disable-sensors"></a>Abilitare o disabilitare i sensori

I sensori sono protetti da cinque difensori per i motori di Internet delle cose. È possibile abilitare o disabilitare i motori per i sensori connessi.

| Motore | Descrizione | Scenario di esempio |
|--|--|--|
| Motore di violazione del protocollo | Una violazione del protocollo si verifica quando i valori del campo o della struttura del pacchetto non sono conformi alla specifica del protocollo. | Avviso "operazione MODBUS non valida (codice zero funzione)". Questo avviso indica che un dispositivo primario ha inviato una richiesta con codice di funzione 0 a un dispositivo secondario. Questa operazione non è consentita in base alla specifica del protocollo e il dispositivo secondario potrebbe non gestire correttamente l'input. |
| Motore di violazione dei criteri | Si verifica una violazione dei criteri con una deviazione dal comportamento di base definito nei criteri appresi o configurati. | Avviso "agente utente HTTP non autorizzato". Questo avviso indica che un'applicazione che non è stata appresa o approvata dal criterio viene usata come client HTTP in un dispositivo. Potrebbe trattarsi di un nuovo Web browser o di un'applicazione su tale dispositivo. |
| Motore malware | Il motore di malware rileva un'attività di rete dannosa. | Avviso "sospetto di attività dannosa (Stuxnet)". Questo avviso indica che il sensore ha rilevato un'attività di rete sospetta nota come correlata al malware Stuxnet, che è una minaccia permanente avanzata destinata a controllo industriale e reti SCADA. |
| Motore anomalie | Il motore di malware rileva un'anomalia nel comportamento di rete. | "Comportamento periodico nel canale di comunicazione". Si tratta di un componente che controlla le connessioni di rete e rileva il comportamento periodico o ciclico della trasmissione dei dati, che è comune nelle reti industriali. |
| Motore operativo | Questo motore rileva gli eventi imprevisti operativi o le entità che non funzionano correttamente. | `Device is Suspected to be Disconnected (Unresponsive)` avviso. Questo avviso viene attivato quando un dispositivo non risponde a richieste per un periodo predefinito. Potrebbe indicare un arresto del dispositivo, la disconnessione o il malfunzionamento.
|

Per abilitare o disabilitare i motori per i sensori connessi:

1. Nel riquadro sinistro della console selezionare impostazioni di **sistema**.

2. Nella sezione **configurazione del motore dei sensori** selezionare **Abilita** o **Disabilita** per i motori.
         
3. Selezionare **Salva modifiche**.

   Se è presente una mancata corrispondenza dei motori abilitati in uno dei sensori aziendali, viene visualizzato un punto esclamativo rosso. Il motore potrebbe essere stato disabilitato direttamente dal sensore.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/red-exclamation-example.png" alt-text="Mancata corrispondenza dei motori abilitati."::: 

## <a name="define-sensor-backup-schedules"></a>Definire le pianificazioni dei backup dei sensori

È possibile pianificare i backup dei sensori ed eseguire backup dei sensori su richiesta dalla console di gestione locale. Ciò consente di proteggersi dagli errori del disco rigido e dalla perdita di dati.

- Elementi di cui viene eseguito il backup: configurazioni e dati.

- Elementi di cui non è stato eseguito il backup: PCAP file e log. È possibile eseguire manualmente il backup e il ripristino di PCAPs e log.

Per impostazione predefinita, il backup dei sensori viene eseguito automaticamente a 3:00 AM ogni giorno. La funzionalità di pianificazione del backup per i sensori consente di raccogliere questi backup e archiviarli nella console di gestione locale o in un server di backup esterno. La copia di file dai sensori alla console di gestione locale avviene tramite un canale crittografato.

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-backup-schedule-screen.png" alt-text="Visualizzazione della schermata di backup del sensore.":::

Quando viene modificato il percorso di backup del sensore predefinito, la console di gestione locale recupera automaticamente i file dalla nuova posizione nel sensore o in una posizione esterna, purché la Console disponga dell'autorizzazione per accedere al percorso. 

Quando i sensori non sono registrati con la console di gestione locale, la finestra di dialogo **pianificazione backup del sensore** indica che non sono gestiti sensori.  

Il processo di ripristino è lo stesso indipendentemente dalla posizione in cui sono archiviati i file.

### <a name="backup-storage-for-sensors"></a>Archiviazione di backup per i sensori

È possibile usare la console di gestione locale per gestire fino a nove backup per ogni sensore gestito, a condizione che i file di cui è stato eseguito il backup non superino lo spazio massimo di backup allocato. 

Lo spazio disponibile viene calcolato in base al modello di console di gestione che si sta utilizzando: 

- **Modello di produzione**: l'archiviazione predefinita è 40 GB; il limite è di 100 GB. 

- **Modello medio**: lo spazio di archiviazione predefinito è 20 GB; il limite è di 50 GB. 

- **Modello portatile**: lo spazio di archiviazione predefinito è 10 GB; il limite è 25 GB. 

- **Modello thin**: l'archiviazione predefinita è di 2 GB. il limite è 4 GB. 

- **Modello robusto**: l'archiviazione predefinita è di 10 GB; il limite è 25 GB. 

L'allocazione predefinita viene visualizzata nella finestra di dialogo **pianificazione backup del sensore** . 

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/edit-mail-server-configuration.png" alt-text="Schermata di modifica della configurazione del server di posta.":::

Non esiste alcun limite di archiviazione quando si esegue il backup in un server esterno. È necessario, tuttavia, definire un limite di allocazione superiore nel  >  campo **percorso personalizzato** pianificazione backup del sensore. Sono supportati i numeri e i caratteri seguenti: `/, a-z, A-Z, 0-9, and _` . 

Ecco le informazioni sul superamento dei limiti di archiviazione di allocazione:

- Se si supera lo spazio di archiviazione allocato, non viene eseguito il backup del sensore. 

- Se si sta eseguendo il backup di più di un sensore, la console di gestione tenta di recuperare i file di sensore per i sensori gestiti.  

- Se il recupero da un sensore supera il limite, la console di gestione tenterà di recuperare le informazioni di backup dal sensore successivo. 

Quando si supera il numero di backup mantenuto definito, il file di backup meno recente viene eliminato per adattarlo a quello nuovo.

I file di backup del sensore vengono denominati automaticamente nel formato seguente: `<sensor name>-backup-version-<version>-<date>.tar` . Ad esempio: `Sensor_1-backup-version-2.6.0.102-2019-06-24_09:24:55.tar`. 

Per eseguire il backup dei sensori:

1. Selezionare **Pianifica backup del sensore** dalla finestra **Impostazioni sistema** . I sensori gestiti dalla console di gestione locale vengono visualizzati nella finestra di dialogo **pianificazione backup del sensore** .  

2. Abilitare l'interruttore **Raccogli backup** .  

3. Selezionare un intervallo di calendario, una data e un fuso orario. Il formato dell'ora è basato su un orario a 24 ore. Ad esempio, immettere 6:00 PM come **18:00**. 

4. Nel campo **allocazione spazio di archiviazione di backup** immettere lo spazio di archiviazione che si desidera allocare per i backup. Si riceve una notifica se si supera lo spazio massimo.

5. Nel campo **Mantieni ultimo** , indicare il numero di backup per sensore che si desidera mantenere. Quando viene superato il limite, il backup meno recente viene eliminato.  

6. Scegliere un percorso di backup:  

   - Per eseguire il backup nella console di gestione locale, disabilitare l'interruttore **percorso personalizzato** . Il percorso predefinito è `/var/cyberx/sensor-backups`.  

   - Per eseguire il backup su un server esterno, abilitare l'interruttore **percorso personalizzato** e immettere una posizione. Sono supportati i numeri e i caratteri seguenti: `/, a-z, A-Z, 0-9, and, _` . 

7. Selezionare **Salva**. 

Per eseguire immediatamente il backup: 

- Selezionare **Esegui backup ora**. La console di gestione locale crea e raccoglie i file di backup del sensore. 

### <a name="receiving-backup-notifications-for-sensors"></a>Ricezione di notifiche di backup per i sensori 

La finestra di dialogo **pianificazione backup del sensore** e il registro di backup elencano automaticamente le informazioni relative a errori e operazioni di backup.  

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/sensor-location.png" alt-text="Visualizzare i sensori e la posizione in cui si trovano e tutte le informazioni rilevanti.":::

Potrebbero verificarsi errori per i motivi seguenti:    

- Non è stato trovato alcun file di backup. 

- Un file è stato trovato ma non può essere recuperato.  

- Si è verificato un errore di connessione di rete. 

- Lo spazio disponibile nella console di gestione locale non è sufficiente per completare il backup.  

Quando si verifica un errore, è possibile inviare una notifica di posta elettronica, gli aggiornamenti syslog e le notifiche di sistema. A tale scopo, creare una regola di inoltri nelle **notifiche di sistema**. 

### <a name="restoring-sensors"></a>Ripristino di sensori 

È possibile ripristinare i backup dalla console di gestione locale e usando l'interfaccia della riga di comando.  

Per eseguire il ripristino dalla console: 

- Selezionare **Ripristina immagine** dalla finestra impostazione del **sistema di sensori** .

  :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/restore.png" alt-text="Ripristinare un backup dell'immagine.":::

  Nella console vengono visualizzati gli errori di ripristino.  

Per eseguire il ripristino tramite l'interfaccia della riga di comando: 

- Accedere a un account amministrativo e immettere `$ sudo cyberx-xsense-system-restore` . 

### <a name="save-a-sensor-backup-to-an-external-smb-server"></a>Salvare un backup del sensore in un server SMB esterno

Per configurare un server SMB in modo che sia possibile salvare un backup del sensore in un'unità esterna: 

1. Creare una cartella condivisa nel server SMB esterno. 

2. Ottenere il percorso della cartella, il nome utente e la password necessari per accedere al server SMB. 

3. In Defender for Internet, creare una directory per i backup: 

   `sudo mkdir /<backup_folder_name_on_server>` 

   `sudo chmod 777 /<backup_folder_name_on_server>/` 

4. Modifica fstab:  

   `sudo nano /etc/fstab` 

   `add - //<server_IP>/<folder_path> /<backup_folder_name_on_cyberx_server> cifs rw,credentials=/etc/samba/user,vers=3.0,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0` 

5. Modificare o creare le credenziali da condividere. Queste sono le credenziali per il server SMB: 

   `sudo nano /etc/samba/user` 

6. Aggiungere:  

   `username=<user name>` 

   `password=<password>` 

7. Montare la directory: 

   `sudo mount -a` 

8. Configurare una directory di backup nella cartella condivisa in Defender per il sensore Internet:  

   `sudo nano /var/cyberx/properties/backup.properties` 

9. Impostare `Backup.shared_location` su `<backup_folder_name_on_cyberx_server>`.

## <a name="see-also"></a>Vedi anche

[Gestire singoli sensori](how-to-manage-individual-sensors.md)
