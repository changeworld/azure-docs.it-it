---
title: Configurare un'appliance Azure Migrate in Azure per enti pubblici
description: Informazioni su come configurare un'appliance Azure Migrate in Azure per enti pubblici
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/13/2021
ms.openlocfilehash: b0bc2b69a4a1ec31cfa560d51920378fe1ab52b8
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714795"
---
# <a name="set-up-an-appliance-in-azure-government"></a>Configurare un'appliance in Azure per enti pubblici 

Seguire questo articolo per distribuire [un'appliance Azure Migrate](./migrate-appliance-architecture.md) per server in ambiente VMware, server in Hyper-V e server fisici in un cloud Azure per enti pubblici. Eseguire uno script per creare l'appliance e verificare che possa connettersi ad Azure. Se si vuole configurare un'appliance nel cloud pubblico, seguire [questo articolo.](deploy-appliance-script.md)


> [!NOTE]
> L'opzione per distribuire un'appliance usando un modello (per i server nell'ambiente VMware e nell'ambiente Hyper-V) non è supportata in Azure per enti pubblici.


## <a name="prerequisites"></a>Prerequisiti

Lo script configura l'appliance Azure Migrate in un server fisico esistente o in un server virtualizzato.

- Il server che fungerà da appliance deve eseguire Windows Server 2016, con 32 GB di memoria, otto vC CPU, circa 80 GB di spazio di archiviazione su disco e un commutatore virtuale esterno. Richiede un indirizzo IP statico o dinamico. 
- Prima di distribuire l'appliance, esaminare i requisiti dettagliati dell'appliance per i server [in VMware](migrate-appliance.md#appliance---vmware), in [Hyper-V](migrate-appliance.md#appliance---hyper-v)e [nei server fisici](migrate-appliance.md#appliance---physical).
- Non eseguire lo script in un'appliance Azure Migrate esistente.

## <a name="set-up-the-appliance-for-vmware"></a>Configurare l'appliance per VMware

Per configurare l'appliance per VMware, scaricare un file compresso dal portale di Azure ed estrarre il contenuto. Eseguire lo script di PowerShell per avviare l'app Web dell'appliance. L'appliance viene configurata per la prima volta. Registrare quindi l'appliance con il progetto.

### <a name="download-the-script"></a>Scaricare lo script

1. In **Obiettivi di migrazione**  >  **Windows, Linux e SQL Server**  >  **Azure Migrate: Individuazione e valutazione** fare clic su **Individua**.
2. In **Individua server** I server sono  >  **virtualizzati?** selezionare **Sì, con VMware vSphere hypervisor.**
3. Fare **clic su** Scarica per scaricare il file compresso.

### <a name="verify-file-security"></a>Verificare la sicurezza dei file

Prima di distribuire il file compresso, verificarne la sicurezza.

1. Nel server in cui è stato scaricato il file aprire una finestra di comando dell'amministratore.
2. Eseguire il comando seguente per generare il codice hash per il file compresso
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Esempio: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMWare-USGov.zip SHA256```

3. Verificare la versione più recente dell'appliance e il valore hash:

    **Algoritmo** | **Scaricare** | **SHA256**
    --- | --- | ---
    VMware (85,8 MB) | [Versione più recente](https://go.microsoft.com/fwlink/?linkid=2140337) | 2daaa2a59302bf911e8ef195f8add7d7c8352de77a9af0b860e2a627979085ca


### <a name="run-the-script"></a>Eseguire lo script

Ecco cosa fa lo script:

- Installa gli agenti e un'applicazione Web.
- Installa i ruoli di Windows, inclusi il servizio attivazione di Windows, IIS e PowerShell ISE.
- Scarica e installa un modulo risibile di IIS. [Altre informazioni](https://www.microsoft.com/download/details.aspx?id=7435)
- Aggiorna una chiave del Registro di sistema (HKLM), con impostazioni persistenti per Azure Migrate.
- Crea i file di log e di configurazione come indicato di seguito:
    - **File di configurazione:**%ProgramData%\Microsoft Azure\Config
    - **File di log:**%ProgramData%\Microsoft Azure\Logs

Per eseguire lo script:

1. Estrarre il file compresso in una cartella nel server che ospiterà l'appliance. Assicurarsi di non eseguire lo script in un server con un'appliance Azure Migrate esistente.
2. Avviare PowerShell nel server con privilegi di amministratore (con privilegi elevati).
3. Modificare la directory di PowerShell nella cartella contenente il contenuto estratto dal file compresso scaricato.
4. Eseguire lo script **AzureMigrateInstaller.ps1**, come indicato di seguito:
    
    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-VMWare-USGov>.\AzureMigrateInstaller.ps1 ```
1. Dopo che lo script viene eseguito correttamente, avvia l'applicazione Web dell'appliance in modo che sia possibile configurare l'appliance. In caso di problemi, esaminare i log di script in C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Verificare l'accesso

Assicurarsi che l'appliance possa connettersi agli URL di Azure per [cloud per enti pubblici.](migrate-appliance.md#government-cloud-urls)


## <a name="set-up-the-appliance-for-hyper-v"></a>Configurare l'appliance per Hyper-V

Per configurare l'appliance per Hyper-V, scaricare un file compresso dal portale di Azure ed estrarre il contenuto. Eseguire lo script di PowerShell per avviare l'app Web dell'appliance. L'appliance viene configurata per la prima volta. Registrare quindi l'appliance con il progetto.

### <a name="download-the-script"></a>Scaricare lo script

1.  In **Obiettivi di migrazione**  >  **Windows, Linux e SQL Server**  >  **Azure Migrate: Individuazione e valutazione** fare clic su **Individua**.
2.  In **Individua server** I server sono  >  **virtualizzati?** selezionare **Sì, con Hyper-V.**
3.  Fare **clic su** Scarica per scaricare il file compresso. 


### <a name="verify-file-security"></a>Verificare la sicurezza dei file

Prima di distribuire il file compresso, verificarne la sicurezza.

1. Nel server in cui è stato scaricato il file aprire una finestra di comando dell'amministratore.
2. Eseguire il comando seguente per generare il codice hash per il file compresso
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Esempio: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-HyperV-USGov.zip SHA256```

3. Verificare la versione più recente dell'appliance e il valore hash:

    **Scenario** | **Scaricare** | **SHA256**
    --- | --- | ---
    Hyper-V (85,8 MB) | [Versione più recente](https://go.microsoft.com/fwlink/?linkid=2140424) |  db5311de3d1d4a1167183a94e8347456db9c5749c7332ff2eb4b777798765e48

          

### <a name="run-the-script"></a>Eseguire lo script

Ecco cosa fa lo script:

- Installa gli agenti e un'applicazione Web.
- Installa i ruoli di Windows, tra cui servizio di attivazione di Windows, IIS e PowerShell ISE.
- Scarica e installa un modulo risibilibile iis. [Altre informazioni](https://www.microsoft.com/download/details.aspx?id=7435)
- Aggiorna una chiave del Registro di sistema (HKLM), con impostazioni persistenti per Azure Migrate.
- Crea i file di log e di configurazione come indicato di seguito:
    - **File di configurazione:**%ProgramData%\Microsoft Azure\Config
    - **File di** log: %ProgramData%\Microsoft Azure\Logs

Per eseguire lo script:

1. Estrarre il file compresso in una cartella nel server che ospiterà l'appliance. Assicurarsi di non eseguire lo script in un server che esegue un'appliance Azure Migrate esistente.
2. Avviare PowerShell nel server con privilegi di amministratore (con privilegi elevati).
3. Modificare la directory di PowerShell nella cartella contenente il contenuto estratto dal file compresso scaricato.
4. Eseguire lo script **AzureMigrateInstaller.ps1**, come indicato di seguito: 

    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-HyperV-USGov>.\AzureMigrateInstaller.ps1 ``` 
1. Dopo che lo script viene eseguito correttamente, avvia l'applicazione Web dell'appliance in modo che sia possibile configurare l'appliance. In caso di problemi, esaminare i log di script in C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Verificare l'accesso

Assicurarsi che l'appliance possa connettersi agli URL di Azure per [cloud per enti pubblici.](migrate-appliance.md#government-cloud-urls)


## <a name="set-up-the-appliance-for-physical-servers"></a>Configurare l'appliance per i server fisici

Per configurare l'appliance per VMware, scaricare un file compresso dal portale di Azure ed estrarre il contenuto. Eseguire lo script di PowerShell per avviare l'app Web dell'appliance. L'appliance viene configurata e configurata per la prima volta. Registrare quindi l'appliance con il progetto.

### <a name="download-the-script"></a>Scaricare lo script

1. In **Obiettivi di migrazione**  >  **Windows, Linux e SQL Server**  >  **Azure Migrate: Individuazione e valutazione** fare clic su **Individua**.
2. In **Individua server** I server sono  >  **virtualizzati?** selezionare Non **virtualizzato/Altro**.
3. Fare **clic su** Scarica per scaricare il file compresso.

### <a name="verify-file-security"></a>Verificare la sicurezza dei file

Prima di distribuire il file compresso, verificarne la sicurezza.

1. Nei server in cui è stato scaricato il file aprire una finestra di comando dell'amministratore.
2. Eseguire il comando seguente per generare il codice hash per il file compresso
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Esempio: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip SHA256```

3. Verificare la versione più recente dell'appliance e il valore hash:

    **Scenario** | **Scaricare** _ | _ *Valore hash**
    --- | --- | ---
    Fisico (85 MB) | [Versione più recente](https://go.microsoft.com/fwlink/?linkid=2140338) | cfed44bb52c9ab3024a628dc7a5d0df8c624f156ec1ecc3507116bae330b257f
          

### <a name="run-the-script"></a>Eseguire lo script

Ecco cosa fa lo script:

- Installa gli agenti e un'applicazione Web.
- Installa i ruoli di Windows, tra cui servizio di attivazione di Windows, IIS e PowerShell ISE.
- Scarica e installa un modulo risibile di IIS. [Altre informazioni](https://www.microsoft.com/download/details.aspx?id=7435)
- Aggiorna una chiave del Registro di sistema (HKLM), con impostazioni persistenti per Azure Migrate.
- Crea i file di log e di configurazione come indicato di seguito:
    - **File di configurazione:**%ProgramData%\Microsoft Azure\Config
    - **File di** log: %ProgramData%\Microsoft Azure\Logs

Per eseguire lo script:

1. Estrarre il file compresso in una cartella nel server che ospiterà l'appliance. Assicurarsi di non eseguire lo script in un server che esegue un'appliance Azure Migrate esistente.
2. Avviare PowerShell nel server con privilegi di amministratore (con privilegi elevati).
3. Modificare la directory di PowerShell nella cartella contenente il contenuto estratto dal file compresso scaricato.
4. Eseguire lo script **AzureMigrateInstaller.ps1**, come indicato di seguito:

    ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>.\AzureMigrateInstaller.ps1 ```
1. Al termine dell'esecuzione, lo script avvia l'applicazione Web dell'appliance in modo che sia possibile configurare l'appliance. Se si verificano problemi, esaminare i log di script in C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Verificare l'accesso

Assicurarsi che l'appliance possa connettersi agli URL di Azure per [i cloud per enti pubblici.](migrate-appliance.md#government-cloud-urls)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver distribuito l'appliance, è necessario configurarla per la prima volta e registrarla con il progetto.

- Configurare l'appliance per [VMware.](how-to-set-up-appliance-vmware.md#4-configure-the-appliance)
- Configurare l'appliance per [Hyper-V.](how-to-set-up-appliance-hyper-v.md#configure-the-appliance)
- Configurare l'appliance per [i server fisici](how-to-set-up-appliance-physical.md).
