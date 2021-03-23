---
title: Configurare un'appliance Azure Migrate per VMware
description: Informazioni su come configurare un'appliance Azure Migrate per la valutazione e la migrazione dei server in ambiente VMware.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 04/16/2020
ms.openlocfilehash: 1217b51ea91758d25b76394b27d3b21b2e9808b3
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104780872"
---
# <a name="set-up-an-appliance-for-servers-in-vmware-environment"></a>Configurare un'appliance per i server in ambiente VMware

Seguire questo articolo per configurare il Azure Migrate Appliance per la valutazione con lo strumento [Azure migrate: individuazione e valutazione](migrate-services-overview.md#azure-migrate-server-assessment-tool) e per la migrazione senza agenti usando lo strumento di [migrazione Azure migrate: server](migrate-services-overview.md#azure-migrate-server-migration-tool) .

Il [Azure migrate Appliance](migrate-appliance.md) è un'appliance semplice usata da Azure migrate: individuazione e valutazione e migrazione del server per individuare i server in esecuzione in server vCenter, inviare i metadati di configurazione e delle prestazioni del server ad Azure e per la replica dei server con la migrazione senza agenti.

È possibile distribuire l'appliance usando due metodi:

- Creare un server in server vCenter usando un modello OVA scaricato. Questo è il metodo descritto in questo articolo.
- Configurare l'appliance in un server esistente usando uno script del programma di installazione di PowerShell. [Questo metodo](deploy-appliance-script.md) deve essere usato se non è possibile usare il modello OVA o se si è in Azure per enti pubblici.

Dopo aver creato l'appliance, verificare che sia in grado di connettersi a Azure Migrate: individuazione e valutazione, registrarlo con il progetto Azure Migrate e configurare l'appliance per avviare l'individuazione.

## <a name="deploy-with-ova"></a>Distribuire con OVA

Per configurare l'appliance con un modello OVA:
1. Specificare un nome di appliance e generare una chiave del progetto di Azure Migrate nel portale.
1. Scaricare un file modello OVA e importarlo nel server vCenter. Verificare che gli OVA siano protetti.
1. Creare la macchina virtuale dell'appliance dal file OVA e verificare che sia in grado di connettersi a Azure Migrate.
1. Configurare il dispositivo per la prima volta e registrarlo con il progetto usando la chiave del progetto Azure Migrate.

### <a name="1-generate-the-azure-migrate-project-key"></a>1. generare la chiave del progetto Azure Migrate

1. In server degli **obiettivi di migrazione**  >    >  **Azure migrate: individuazione e valutazione** selezionare **individua**.
2. In **individua server**  >  **i server sono virtualizzati?** selezionare **Sì, con VMware vSphere Hypervisor**.
3. In **1: generare Azure migrate chiave del progetto**, specificare un nome per il dispositivo Azure migrate da configurare per l'individuazione dei server nell'ambiente VMware. Il nome deve essere alfanumerico con un massimo di 14 caratteri.
1. Fare clic su **Genera chiave** per avviare la creazione delle risorse di Azure necessarie. Non chiudere la pagina di individuazione durante la creazione delle risorse.
1. Al termine della creazione delle risorse di Azure, viene generata una **Chiave progetto Azure Migrate**.
1. Copiare la chiave perché sarà necessaria per completare la registrazione dell'appliance durante la configurazione.

### <a name="2-download-the-ova-template"></a>2. scaricare il modello OVA

In **2: Scaricare l'appliance di Azure Migrate** selezionare il file OVA e fare clic su **Scarica**.

### <a name="verify-security"></a>Verificare la sicurezza

Prima di distribuire il file OVA, verificarne la sicurezza:

1. Aprire una finestra di comando amministratore nel server in cui è stato scaricato il file.
2. Eseguire il comando seguente per generare l'hash per il file OVA:
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   Esempio di utilizzo: ```C:\>CertUtil -HashFile C:\Users\Administrator\Desktop\MicrosoftAzureMigration.ova SHA256```

3. Verificare le versioni più recenti dell'appliance e i valori hash:

    - Per il cloud pubblico di Azure:
    
        **Algoritmo** | **Scaricare** | **SHA256**
        --- | --- | ---
        VMware (11,9 GB) | [Versione più recente](https://go.microsoft.com/fwlink/?linkid=2140333) | e9c9a1fe4f3ebae81008328e8f3a7933d78ff835ecd871d1b17f367621ce3c74


### <a name="3-create-the-appliance-server"></a>3. creare il server appliance

Importare il file scaricato e creare un server in ambiente VMware

1. Nella console di vSphere Client fare clic su **File** > **Deploy OVF Template** (Distribuisci modello OVF).
2. Nella procedura guidata Distribuire il modello OVF > **Origine** specificare il percorso del file con estensione ova.
3. In **nome** e **percorso** specificare un nome descrittivo per il server. Selezionare l'oggetto inventario in cui verrà ospitato il server.
5. In **host/cluster** specificare l'host o il cluster in cui viene eseguito il server.
6. In **archiviazione** specificare la destinazione di archiviazione per il server.
7. In **Disk Format** (Formato disco) specificare il tipo e la dimensione del disco.
8. In **mapping di rete** specificare la rete a cui si connetterà il server. La rete richiede la connettività Internet per inviare i metadati a Azure Migrate.
9. Rivedere e confermare le impostazioni e quindi fare clic su **Finish** (Fine).


### <a name="verify-appliance-access-to-azure"></a>Verificare l'accesso dell'appliance ad Azure

Verificare che il server del dispositivo sia in grado di connettersi agli URL di Azure per i cloud [pubblici](migrate-appliance.md#public-cloud-urls) e [governativi](migrate-appliance.md#government-cloud-urls) .


### <a name="4-configure-the-appliance"></a>4. configurare l'appliance

Configurare l'appliance per la prima volta.

> [!NOTE]
> Se si configura l'appliance usando uno [**script di PowerShell**](deploy-appliance-script.md) invece degli OVA scaricati, i primi due passaggi di questa procedura non sono rilevanti.

1. Nella console del client di vSphere, fare clic con il pulsante destro del mouse sul server e quindi scegliere **Apri console**.
2. Specificare la lingua, il fuso orario e la password per l'appliance.
3. Aprire un browser in qualsiasi server in grado di connettersi al server appliance e aprire l'URL di gestione configurazione Appliance: `https://appliance name or IP address: 44368` .

   In alternativa, è possibile aprire Gestione configurazione dal desktop del server appliance selezionando il collegamento per Gestione configurazione.
1. Accettare le **condizioni di licenza** e leggere le informazioni di terze parti.
1. In Configuration Manager > **configurare i prerequisiti**, procedere come segue:
   - **Connettività**: il dispositivo verifica che il server disponga di accesso a Internet. Se il server usa un proxy:
     - Fare clic su **Configura proxy** per specificare l'indirizzo proxy nel formato `http://ProxyIPAddress` o nella `http://ProxyFQDN` porta di ascolto.
     - Se il proxy richiede l'autenticazione, specificare le credenziali.
     - È supportato solo il proxy HTTP.
     - Se sono stati aggiunti dettagli del proxy o sono stati disabilitati il proxy e/o l'autenticazione, fare clic su **Save** (Salva) per attivare di nuovo il controllo della connettività.
   - **Time sync** (Sincronizzazione ora): Per il corretto funzionamento dell'individuazione delle macchine virtuali, l'ora dell'appliance deve essere sincronizzata con l'ora Internet.
   - **Install updates** (Installa aggiornamenti): l'appliance verifica che siano installati gli aggiornamenti più recenti. Al termine della verifica, è possibile fare clic su **Visualizza servizi Appliance** per visualizzare lo stato e le versioni dei servizi in esecuzione nel server appliance.
   - **Install VDDK** (Installa VDDK): l'appliance verifica che VMware vSphere Virtual Disk Development Kit (VDDK) sia installato. Se non è già installato, scaricare VDDK 6.7 da VMware ed estrarre il contenuto dello ZIP scaricato nel percorso specificato nell'appliance, come indicato nelle **istruzioni di installazione**.

     Azure Migrate migrazione del server USA VDDK per replicare i server durante la migrazione ad Azure. 
1. Se lo si desidera, è possibile **rieseguire i prerequisiti** in qualsiasi momento durante la configurazione dell'appliance per verificare se l'appliance soddisfa tutti i prerequisiti.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-prerequisites.png" alt-text="Pannello 1 in Gestione configurazione Appliance":::


## <a name="register-the-appliance-with-azure-migrate"></a>Registrare l'appliance con Azure Migrate

1. Incollare la **chiave del progetto Azure Migrate** copiata dal portale. Se non si dispone della chiave, passare a **individuazione e valutazione> individua> Gestisci appliance esistenti**, selezionare il nome dell'appliance fornito al momento della generazione della chiave e copiare la chiave corrispondente.
1. Per eseguire l'autenticazione con Azure, è necessario un codice del dispositivo. Facendo clic su **Accedi** si aprirà una finestra modale con il codice del dispositivo, come illustrato di seguito.

    :::image type="content" source="./media/tutorial-discover-vmware/device-code.png" alt-text="Finestra modale con il codice del dispositivo":::

1. Fare clic su **Copy code & Login** (Copia il codice e accedi) per copiare il codice del dispositivo e aprire una richiesta di accesso ad Azure in una nuova scheda del browser. Se l'opzione non è visualizzata, verificare di aver disabilitato il blocco popup nel browser.
1. Nella nuova scheda incollare il codice del dispositivo e accedere usando il nome utente e la password di Azure.
   
   L'accesso con un PIN non è supportato.
3. Se si chiude la scheda per errore senza eseguire l'accesso, è necessario aggiornare la scheda del browser di gestione configurazione dell'appliance per abilitare di nuovo il pulsante di accesso.
1. Dopo aver eseguito l'accesso, tornare nella scheda precedente di gestione configurazione dell'appliance.
1. Se l'account utente di Azure usato per la registrazione ha le autorizzazioni corrette per le risorse di Azure create durante la generazione della chiave, la registrazione dell'appliance verrà avviata.
1. Al termine della registrazione dell'appliance è possibile visualizzare i dettagli della registrazione facendo clic su **Visualizza dettagli**.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-registration.png" alt-text="Pannello 2 in Gestione configurazione Appliance":::

## <a name="start-continuous-discovery"></a>Avviare l'individuazione continua

### <a name="provide-vcenter-server-details"></a>Fornire dettagli server vCenter

L'appliance deve connettersi a server vCenter per individuare i dati relativi alla configurazione e alle prestazioni dei server.

1. In **passaggio 1: fornire le credenziali server vCenter** fare clic su **Aggiungi credenziali** per specificare un nome descrittivo per le credenziali, aggiungere **nome utente** e **password** per l'account server vCenter che l'appliance userà per individuare i server in esecuzione nel server vCenter.
    - È necessario configurare un account con le autorizzazioni necessarie, come descritto in questo articolo.
    - Per definire l'ambito dell'individuazione di oggetti VMware specifici (server vCenter Data Center, cluster, una cartella di cluster, host, una cartella di host o singoli server), vedere le istruzioni in [questo articolo](set-discovery-scope.md) per limitare l'account usato da Azure migrate.
1. In **passaggio 2:** specificare i dettagli della server vCenter fare clic su **Aggiungi origine individuazione** per selezionare il nome descrittivo per le credenziali dall'elenco a discesa, specificare l' **indirizzo IP/FQDN** del server vCenter. È possibile lasciare l'impostazione predefinita per **Porta** (443) o specificare una porta personalizzata sulla quale sarà in ascolto il server vCenter e fare clic su **Salva**.
1. Quando si fa clic su **Salva**, il dispositivo tenterà di convalidare la connessione al server vCenter con le credenziali specificate e indicherà lo **stato di convalida** nella tabella rispetto al server vCenter indirizzo IP/FQDN.
1. È possibile **riconvalidare** la connettività al server vCenter in qualsiasi momento prima di avviare l'individuazione.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="Pannello 3 in Gestione configurazione Appliance per server vCenter dettagli":::

### <a name="provide-server-credentials"></a>Fornire le credenziali del server

In **passaggio 3: fornire le credenziali del server per eseguire l'inventario software, l'analisi delle dipendenze senza agenti e l'individuazione di istanze e database di SQL Server**, è possibile scegliere di fornire più credenziali del server o se non si desidera utilizzare queste funzionalità, è possibile scegliere di ignorare il passaggio e procedere con server vCenter individuazione. È possibile modificare le finalità in un secondo momento.

:::image type="content" source="./media/tutorial-discover-vmware/appliance-server-credentials-mapping.png" alt-text="Pannello 3 in Gestione configurazione Appliance per i dettagli del server":::

> [!Note]
> L'individuazione e la valutazione di SQL Server istanze e database in esecuzione nell'ambiente VMware sono ora in anteprima. Per provare questa funzionalità, usare [**questo collegamento**](https://aka.ms/AzureMigrate/SQL) per creare un progetto nell'area **Australia orientale**. Se si dispone già di un progetto nell'Australia orientale e si vuole provare questa funzionalità, assicurarsi di aver completato questi [**prerequisiti**](how-to-discover-sql-existing-project.md) nel portale.

Se si desidera utilizzare queste funzionalità, è possibile fornire le credenziali del server attenendosi alla procedura descritta di seguito. Il dispositivo tenterà di eseguire automaticamente il mapping delle credenziali ai server per eseguire le funzionalità di individuazione.

- È possibile aggiungere le credenziali del server facendo clic sul pulsante **Aggiungi credenziali** . Verrà aperto un modale in cui è possibile scegliere il **tipo di credenziali** dall'elenco a discesa.
- È possibile specificare credenziali di autenticazione di dominio/Windows (non di dominio)/Linux (non di dominio)/SQL Server. [Altre](add-server-credentials.md) informazioni su come fornire le credenziali e su come gestirle.
- Per ogni tipo di credenziali, è necessario specificare un nome descrittivo per le credenziali, aggiungere il nome **utente** e la **password** e fare clic su **Salva**.
- Se si scelgono le credenziali del dominio, è necessario specificare anche il nome di dominio completo per il dominio. Il nome di dominio completo è necessario per convalidare l'autenticità delle credenziali con la Active Directory di tale dominio.
- Esaminare le [autorizzazioni necessarie](add-server-credentials.md#required-permissions) per l'account per l'individuazione delle applicazioni installate, l'analisi delle dipendenze senza agenti o l'individuazione di SQL Server istanze e database.
- Per aggiungere più credenziali contemporaneamente e salvarle, fare clic su **Aggiungi altre**.
- Quando si fa clic su **Salva** o **Aggiungi altro**, l'appliance convalida le credenziali del dominio con la Active Directory del dominio per la relativa autenticità. Questa operazione viene eseguita per evitare i blocchi degli account quando l'Appliance esegue più iterazioni per eseguire il mapping delle credenziali ai rispettivi server.
- È possibile visualizzare lo **stato di convalida** di tutte le credenziali del dominio nella tabella Credentials. Verranno convalidate solo le credenziali di dominio.
- Se la convalida ha esito negativo, è possibile fare clic su stato **non riuscito** per visualizzare l'errore rilevato e fare clic su riconvalida **credenziali** dopo la correzione del problema per convalidare di nuovo le credenziali di dominio non riuscite.


### <a name="start-discovery"></a>Avviare l'individuazione

1. Fare clic su **Avvia individuazione per avviare** l'individuazione server vCenter. Dopo che l'individuazione è stata avviata correttamente, è possibile controllare lo stato di individuazione in base all'indirizzo IP/FQDN server vCenter nella tabella sources.
1. Se sono state specificate le credenziali del server, l'inventario software (individuazione delle applicazioni installate) verrà avviato automaticamente dopo il completamento dell'individuazione del server vCenter. L'inventario software viene eseguito una volta ogni 12 ore.
1. [Inventario software](how-to-discover-applications.md) identifica le istanze SQL Server in esecuzione nei server e usando le informazioni, il dispositivo tenta di connettersi alle istanze tramite l'autenticazione di Windows o le credenziali di autenticazione SQL Server fornite nel dispositivo e raccoglie i dati nei database SQL Server e le relative proprietà. L'individuazione SQL viene eseguita ogni 24 ore.
1. Durante l'inventario software, le credenziali dei server aggiunti verranno iterate sui server e convalidate per l'analisi delle dipendenze senza agenti. È possibile abilitare l'analisi delle dipendenze senza agenti per i server dal portale. È possibile selezionare solo i server in cui è stata eseguita la convalida per abilitare l'analisi delle dipendenze senza agenti.

L'individuazione funziona come segue:
- Sono necessari circa 15 minuti per visualizzare l'inventario dei server individuati nel portale.
- L'individuazione delle applicazioni installate può richiedere del tempo. La durata dipende dal numero di server individuati. Per i server 500, la visualizzazione dell'inventario individuato nel portale di Azure Migrate richiede circa un'ora.
- Al termine dell'individuazione dei server, è possibile abilitare l'analisi delle dipendenze senza agenti nei server dal portale.
- SQL Server le istanze e i dati dei database inizieranno a essere visualizzati nel portale entro 24 ore dall'avvio dell'individuazione.

## <a name="next-steps"></a>Passaggi successivi

Esaminare le esercitazioni per la [valutazione VMware](./tutorial-assess-vmware-azure-vm.md) e la [migrazione senza agenti](tutorial-migrate-vmware.md).