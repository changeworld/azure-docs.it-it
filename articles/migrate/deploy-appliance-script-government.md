---
title: Configurare un'appliance Azure Migrate in Azure per enti pubblici
description: Informazioni su come configurare un appliance Azure Migrate in Azure per enti pubblici
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/13/2021
ms.openlocfilehash: c4ca8d8ac24ac174158957e44b5eabe4a89a5340
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775205"
---
# <a name="set-up-an-appliance-in-azure-government"></a>Configurare un'appliance in Azure per enti pubblici 

Seguire questo articolo per distribuire un [appliance Azure migrate](./migrate-appliance-architecture.md) per i server in ambiente VMware, server in Hyper-V e server fisici in un cloud di Azure per enti pubblici. Eseguire uno script per creare l'appliance e verificare che sia in grado di connettersi ad Azure. Se si vuole configurare un'appliance nel cloud pubblico, seguire [questo articolo](deploy-appliance-script.md).


> [!NOTE]
> L'opzione per distribuire un'appliance usando un modello (per i server in ambiente VMware e l'ambiente Hyper-V) non è supportata in Azure per enti pubblici.


## <a name="prerequisites"></a>Prerequisiti

Lo script configura il dispositivo Azure Migrate in un server fisico esistente o in un server virtualizzato.

- Il server che fungerà da dispositivo deve eseguire Windows Server 2016, con 32 GB di memoria, otto vCPU, circa 80 GB di spazio di archiviazione su disco e un Commuter virtuale esterno. Richiede un indirizzo IP statico o dinamico e l'accesso a Internet.
- Prima di distribuire il dispositivo, esaminare i requisiti dettagliati dell'appliance per i [server in VMware](migrate-appliance.md#appliance---vmware), [in Hyper-V](migrate-appliance.md#appliance---hyper-v)e [nei server fisici](migrate-appliance.md#appliance---physical).
- Non eseguire lo script in un'appliance Azure Migrate esistente.

## <a name="set-up-the-appliance-for-vmware"></a>Configurare l'appliance per VMware

Per configurare l'appliance per VMware, scaricare un file compresso dal portale di Azure ed estrarre il contenuto. Eseguire lo script di PowerShell per avviare l'app Web Appliance. Si configura l'appliance e la si configura per la prima volta. Si registra quindi l'appliance con il progetto.

### <a name="download-the-script"></a>Scaricare lo script

1. Negli **obiettivi di migrazione**  >  **Windows, Linux e SQL Server**  >  **Azure migrate: individuazione e valutazione**, fare clic su **individua**.
2. In **individua server**  >  **i server sono virtualizzati?** selezionare **Sì, con VMware vSphere Hypervisor**.
3. Fare clic su **download** per scaricare il file compresso.

### <a name="verify-file-security"></a>Verificare la sicurezza del file

Prima di distribuire il file compresso, verificarne la sicurezza.

1. Aprire una finestra di comando amministratore nel server in cui è stato scaricato il file.
2. Eseguire il comando seguente per generare il codice hash per il file compresso
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Esempio: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMWare-USGov.zip SHA256```

3. Verificare la versione più recente del dispositivo e il valore hash:

    **Algoritmo** | **Scaricare** | **SHA256**
    --- | --- | ---
    VMware (85,8 MB) | [Versione più recente](https://go.microsoft.com/fwlink/?linkid=2140337) | 2daaa2a59302bf911e8ef195f8add7d7c8352de77a9af0b860e2a627979085ca


### <a name="run-the-script"></a>Eseguire lo script

Ecco cosa fa lo script:

- Installa gli agenti e un'applicazione Web.
- Installa i ruoli di Windows, tra cui il servizio Attivazione Windows, IIS e PowerShell ISE.
- Scarica e installa un modulo riscrivibile di IIS. [Altre informazioni](https://www.microsoft.com/download/details.aspx?id=7435)
- Aggiorna una chiave del registro di sistema (HKLM) con impostazioni permanenti per Azure Migrate.
- Crea i file di log e di configurazione come segue:
    - **File di configurazione**:%ProgramData%\Microsoft Azure\Config
    - **File di log**:%ProgramData%\Microsoft Azure\Logs

Per eseguire lo script:

1. Estrarre il file compresso in una cartella nel server che ospiterà l'appliance. Assicurarsi di non eseguire lo script in un server con un dispositivo Azure Migrate esistente.
2. Avviare PowerShell nel server con privilegi di amministratore (con privilegi elevati).
3. Modificare la directory di PowerShell nella cartella che contiene il contenuto estratto dal file compresso scaricato.
4. Eseguire lo script **AzureMigrateInstaller.ps1**, come indicato di seguito:
    
    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-VMWare-USGov>.\AzureMigrateInstaller.ps1 ```
1. Dopo che lo script è stato eseguito correttamente, viene avviata l'applicazione Web Appliance per poter configurare l'appliance. Se si verificano problemi, esaminare i log di script in C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Verificare l'accesso

Assicurarsi che l'appliance possa connettersi agli URL di Azure per i [cloud governativi](migrate-appliance.md#government-cloud-urls).


## <a name="set-up-the-appliance-for-hyper-v"></a>Configurare l'appliance per Hyper-V

Per configurare l'appliance per Hyper-V, è possibile scaricare un file compresso dal portale di Azure ed estrarre il contenuto. Eseguire lo script di PowerShell per avviare l'app Web Appliance. Si configura l'appliance e la si configura per la prima volta. Si registra quindi l'appliance con il progetto.

### <a name="download-the-script"></a>Scaricare lo script

1.  Negli **obiettivi di migrazione**  >  **Windows, Linux e SQL Server**  >  **Azure migrate: individuazione e valutazione**, fare clic su **individua**.
2.  In **individua server**  >  **i server sono virtualizzati?** selezionare **Sì, con Hyper-V**.
3.  Fare clic su **download** per scaricare il file compresso. 


### <a name="verify-file-security"></a>Verificare la sicurezza del file

Prima di distribuire il file compresso, verificarne la sicurezza.

1. Aprire una finestra di comando amministratore nel server in cui è stato scaricato il file.
2. Eseguire il comando seguente per generare il codice hash per il file compresso
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Esempio: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-HyperV-USGov.zip SHA256```

3. Verificare la versione più recente del dispositivo e il valore hash:

    **Scenario** | **Scaricare** | **SHA256**
    --- | --- | ---
    Hyper-V (85,8 MB) | [Versione più recente](https://go.microsoft.com/fwlink/?linkid=2140424) |  db5311de3d1d4a1167183a94e8347456db9c5749c7332ff2eb4b777798765e48

          

### <a name="run-the-script"></a>Eseguire lo script

Ecco cosa fa lo script:

- Installa gli agenti e un'applicazione Web.
- Installa i ruoli di Windows, tra cui il servizio Attivazione Windows, IIS e PowerShell ISE.
- Scarica e installa un modulo riscrivibile di IIS. [Altre informazioni](https://www.microsoft.com/download/details.aspx?id=7435)
- Aggiorna una chiave del registro di sistema (HKLM) con impostazioni permanenti per Azure Migrate.
- Crea i file di log e di configurazione come segue:
    - **File di configurazione**:%ProgramData%\Microsoft Azure\Config
    - **File di log**:%ProgramData%\Microsoft Azure\Logs

Per eseguire lo script:

1. Estrarre il file compresso in una cartella nel server che ospiterà l'appliance. Assicurarsi di non eseguire lo script in un server che esegue un'appliance Azure Migrate esistente.
2. Avviare PowerShell nel server con privilegi di amministratore (con privilegi elevati).
3. Modificare la directory di PowerShell nella cartella che contiene il contenuto estratto dal file compresso scaricato.
4. Eseguire lo script **AzureMigrateInstaller.ps1**, come indicato di seguito: 

    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-HyperV-USGov>.\AzureMigrateInstaller.ps1 ``` 
1. Dopo che lo script è stato eseguito correttamente, viene avviata l'applicazione Web Appliance per poter configurare l'appliance. Se si verificano problemi, esaminare i log di script in C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Verificare l'accesso

Assicurarsi che l'appliance possa connettersi agli URL di Azure per i [cloud governativi](migrate-appliance.md#government-cloud-urls).


## <a name="set-up-the-appliance-for-physical-servers"></a>Configurare l'appliance per i server fisici

Per configurare l'appliance per VMware, scaricare un file compresso dal portale di Azure ed estrarre il contenuto. Eseguire lo script di PowerShell per avviare l'app Web Appliance. Si configura l'appliance e la si configura per la prima volta. Si registra quindi l'appliance con il progetto.

### <a name="download-the-script"></a>Scaricare lo script

1. Negli **obiettivi di migrazione**  >  **Windows, Linux e SQL Server**  >  **Azure migrate: individuazione e valutazione**, fare clic su **individua**.
2. In **individua server**  >  **i server sono virtualizzati?** selezionare **non virtualizzato/altro**.
3. Fare clic su **download** per scaricare il file compresso.

### <a name="verify-file-security"></a>Verificare la sicurezza del file

Prima di distribuire il file compresso, verificarne la sicurezza.

1. Aprire una finestra di comando amministratore nei server in cui è stato scaricato il file.
2. Eseguire il comando seguente per generare il codice hash per il file compresso
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Esempio: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip SHA256```

3. Verificare la versione più recente del dispositivo e il valore hash:

    **Scenario** | **Scaricare** _ | _ *Valore hash**
    --- | --- | ---
    Fisico (85 MB) | [Versione più recente](https://go.microsoft.com/fwlink/?linkid=2140338) | cfed44bb52c9ab3024a628dc7a5d0df8c624f156ec1ecc3507116bae330b257f
          

### <a name="run-the-script"></a>Eseguire lo script

Ecco cosa fa lo script:

- Installa gli agenti e un'applicazione Web.
- Installa i ruoli di Windows, tra cui il servizio Attivazione Windows, IIS e PowerShell ISE.
- Scarica e installa un modulo riscrivibile di IIS. [Altre informazioni](https://www.microsoft.com/download/details.aspx?id=7435)
- Aggiorna una chiave del registro di sistema (HKLM) con impostazioni permanenti per Azure Migrate.
- Crea i file di log e di configurazione come segue:
    - **File di configurazione**:%ProgramData%\Microsoft Azure\Config
    - **File di log**:%ProgramData%\Microsoft Azure\Logs

Per eseguire lo script:

1. Estrarre il file compresso in una cartella nel server che ospiterà l'appliance. Assicurarsi di non eseguire lo script in un server che esegue un'appliance Azure Migrate esistente.
2. Avviare PowerShell nel server con privilegi di amministratore (con privilegi elevati).
3. Modificare la directory di PowerShell nella cartella che contiene il contenuto estratto dal file compresso scaricato.
4. Eseguire lo script **AzureMigrateInstaller.ps1**, come indicato di seguito:

    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>.\AzureMigrateInstaller.ps1 ```
1. Dopo che lo script è stato eseguito correttamente, viene avviata l'applicazione Web Appliance per poter configurare l'appliance. Se si verificano problemi, esaminare i log di script in C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log.

### <a name="verify-access"></a>Verificare l'accesso

Assicurarsi che l'appliance possa connettersi agli URL di Azure per i [cloud governativi](migrate-appliance.md#government-cloud-urls).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver distribuito il dispositivo, è necessario configurarlo per la prima volta e registrarlo con il progetto.

- Configurare l'appliance per [VMware](how-to-set-up-appliance-vmware.md#4-configure-the-appliance).
- Configurare l'appliance per [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).
- Configurare l'appliance per i [server fisici](how-to-set-up-appliance-physical.md).
