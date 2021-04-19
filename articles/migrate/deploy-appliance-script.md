---
title: Configurare un'appliance Azure Migrate con uno script
description: Informazioni su come configurare un'appliance Azure Migrate con uno script
ms.topic: how-to
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.date: 03/18/2021
ms.openlocfilehash: ff05a01ad8173923ff614657d0231f743f38ba1c
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714759"
---
# <a name="set-up-an-appliance-with-a-script"></a>Configurare un'appliance con uno script

Seguire questo articolo per creare [un'appliance Azure Migrate per](./migrate-appliance-architecture.md) la valutazione/migrazione di server in VMware e in Hyper-V. Eseguire uno script per creare un'appliance e verificare che possa connettersi ad Azure. 

È possibile distribuire l'appliance per i server in VMware e in Hyper-V usando uno script o un modello scaricato dal portale di Azure. L'uso di uno script è utile se non è possibile creare un'appliance usando il modello scaricato.

- Per usare un modello, seguire le esercitazioni per [VMware](./tutorial-discover-vmware.md) [o Hyper-V.](./tutorial-discover-hyper-v.md)
- Per configurare un'appliance per i server fisici, è possibile usare solo uno script. Seguire [questo articolo.](how-to-set-up-appliance-physical.md)
- Per configurare un'appliance in un cloud Azure per enti pubblici, seguire [questo articolo.](deploy-appliance-script-government.md)

## <a name="prerequisites"></a>Prerequisiti

Lo script configura l'appliance Azure Migrate in un server esistente.

- Il server che fungerà da appliance deve soddisfare i requisiti hardware e del sistema operativo seguenti:

Scenario | Requisiti
--- | ---
VMware | Windows Server 2016, con 32 GB di memoria, otto vC CPU, circa 80 GB di spazio di archiviazione su disco
Hyper-V | Windows Server 2016, con 16 GB di memoria, otto vC CPU, circa 80 GB di spazio di archiviazione su disco

- Il server richiede anche un commutatore virtuale esterno. Richiede un indirizzo IP statico o dinamico. 
- Prima di distribuire l'appliance, esaminare i requisiti dettagliati dell'appliance per [i server in VMware](migrate-appliance.md#appliance---vmware)in [Hyper-V.](migrate-appliance.md#appliance---hyper-v)
- Non eseguire lo script in un'appliance Azure Migrate esistente.

## <a name="set-up-the-appliance-for-vmware"></a>Configurare l'appliance per VMware

Per configurare l'appliance per VMware, scaricare il file compresso denominato AzureMigrateInstaller-Server-Public.zip dal portale o da [qui](https://go.microsoft.com/fwlink/?linkid=2140334)ed estrarre il contenuto. Eseguire lo script di PowerShell per avviare l'app Web dell'appliance. L'appliance viene configurata e configurata per la prima volta. Registrare quindi l'appliance con il progetto.

### <a name="verify-file-security"></a>Verificare la sicurezza dei file

Prima di distribuire il file compresso, verificarne la sicurezza.

1. Nel server in cui è stato scaricato il file aprire una finestra di comando dell'amministratore.
2. Eseguire il comando seguente per generare il codice hash per il file compresso
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Esempio: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMware-Public.zip SHA256```
3. Verificare la versione e lo script dell'appliance più recenti per il cloud pubblico di Azure:

    **Algoritmo** | **Scaricare** | **SHA256**
    --- | --- | ---
    VMware (85,8 MB) | [Versione più recente](https://go.microsoft.com/fwlink/?linkid=2116601) | 85b74d93dfcee43412386141808d82147916330e6669df94c7969fe1b3d0fe72

### <a name="run-the-script"></a>Eseguire lo script

Ecco cosa fa lo script:

- Installa gli agenti e un'applicazione Web.
- Installa i ruoli di Windows, tra cui il servizio attivazione di Windows, IIS e PowerShell ISE.
- Scarica e installa un modulo risibile di IIS. [Altre informazioni](https://www.microsoft.com/download/details.aspx?id=7435)
- Aggiorna una chiave del Registro di sistema (HKLM), con impostazioni persistenti per Azure Migrate.
- Crea i file di log e di configurazione come indicato di seguito:
    - **File di configurazione:**%ProgramData%\Microsoft Azure\Config
    - **File di log:**%ProgramData%\Microsoft Azure\Logs

Per eseguire lo script:

1. Estrarre il file compresso in una cartella nel server che ospiterà l'appliance. Assicurarsi di non eseguire lo script in un'appliance Azure Migrate esistente.
2. Avviare PowerShell nel server con privilegi di amministratore (con privilegi elevati).
3. Modificare la directory di PowerShell nella cartella contenente il contenuto estratto dal file compresso scaricato.
4. Eseguire lo script **AzureMigrateInstaller.ps1**, come indicato di seguito:

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 -scenario VMware ```
  
5. Al termine dell'esecuzione, lo script avvia l'applicazione Web dell'appliance in modo che sia possibile configurare l'appliance. Se si verificano problemi, esaminare i log di script in C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Verificare l'accesso

Assicurarsi che l'appliance possa connettersi agli URL di Azure per il [cloud](migrate-appliance.md#public-cloud-urls) pubblico.

## <a name="set-up-the-appliance-for-hyper-v"></a>Configurare l'appliance per Hyper-V

Per configurare l'appliance per Hyper-V, scaricare il file compresso denominato AzureMigrateInstaller-Server-Public.zip dal portale o da [qui](https://go.microsoft.com/fwlink/?linkid=2105112)ed estrarre il contenuto. Eseguire lo script di PowerShell per avviare l'app Web dell'appliance. L'appliance viene configurata per la prima volta. Registrare quindi l'appliance con il progetto.


### <a name="verify-file-security"></a>Verificare la sicurezza dei file

Prima di distribuire il file compresso, verificarne la sicurezza.

1. Nel server in cui è stato scaricato il file aprire una finestra di comando dell'amministratore.
2. Eseguire il comando seguente per generare il codice hash per il file compresso
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Esempio: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-HyperV.zip SHA256```

3. Verificare la versione e lo script dell'appliance più recenti per il cloud pubblico di Azure:

    **Scenario** | **Scaricare** | **SHA256**
    --- | --- | ---
    Hyper-V (85,8 MB) | [Versione più recente](https://go.microsoft.com/fwlink/?linkid=2116657) |  9bbef62e2e22481eda4b77c7fdf05db98c3767c20f0a873114fb0dcfa6ed682a

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

1. Estrarre il file compresso in una cartella nel server che ospiterà l'appliance. Assicurarsi di non eseguire lo script in un'appliance Azure Migrate esistente.
2. Avviare PowerShell nel server con privilegi di amministratore (con privilegi elevati).
3. Modificare la directory di PowerShell nella cartella contenente il contenuto estratto dal file compresso scaricato.
4. Eseguire lo script **AzureMigrateInstaller.ps1**, come indicato di seguito:

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 -scenario Hyperv ```
   
5. Dopo che lo script viene eseguito correttamente, avvia l'applicazione Web dell'appliance in modo che sia possibile configurare l'appliance. In caso di problemi, esaminare i log di script in C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="verify-access"></a>Verificare l'accesso

Assicurarsi che l'appliance possa connettersi agli URL di Azure per il [cloud](migrate-appliance.md#public-cloud-urls) pubblico.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver distribuito l'appliance, è necessario configurarla per la prima volta e registrarla nel progetto.

- Configurare l'appliance per [VMware](how-to-set-up-appliance-vmware.md#4-configure-the-appliance).
- Configurare l'appliance per [Hyper-V](how-to-set-up-appliance-hyper-v.md#configure-the-appliance).