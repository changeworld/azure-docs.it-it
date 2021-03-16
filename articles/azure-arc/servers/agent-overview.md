---
title: Panoramica dell'agente computer connesso
description: Questo articolo fornisce una panoramica dettagliata dell'agente server abilitati per Azure Arc, che supporta il monitoraggio di macchine virtuali ospitate in ambienti ibridi.
ms.date: 03/15/2021
ms.topic: conceptual
ms.openlocfilehash: 1fd863ccacc7768401e35254a98c7bb494b3d358
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/15/2021
ms.locfileid: "103470490"
---
# <a name="overview-of-azure-arc-enabled-servers-agent"></a>Panoramica dell'agente di server abilitati per Azure Arc

Azure Arc Enabled Servers Connected Machine Agent consente di gestire i computer Windows e Linux ospitati all'esterno di Azure nella rete aziendale o in un altro provider di servizi cloud. Questo articolo offre una panoramica dettagliata dell'agente, dei requisiti di sistema e di rete, nonché dei diversi metodi di distribuzione.

>[!NOTE]
>A partire dalla versione generale di Azure Arc Enabled Servers nel 2020 settembre, tutte le versioni non definitive dell'agente del computer connesso di Azure (gli agenti con versioni inferiori a 1,0) verranno **deprecate** entro il **2 febbraio 2021**.  Questo intervallo di tempo consente di eseguire l'aggiornamento alla versione 1,0 o successiva prima che gli agenti pre-rilasciati non siano più in grado di comunicare con il servizio server abilitati per Azure Arc.

## <a name="agent-component-details"></a>Dettagli del componente agente

Il pacchetto agente computer connesso di Azure contiene diversi componenti logici, che vengono aggregati insieme.

* Il servizio metadati dell'istanza ibrida (HIMDS) gestisce la connessione ad Azure e all'identità di Azure del computer connesso.

* L'agente di configurazione Guest fornisce funzionalità di configurazione dei criteri e Guest di In-Guest, ad esempio la valutazione della conformità del computer ai criteri richiesti.

    Si noti il comportamento seguente con la [configurazione Guest](../../governance/policy/concepts/guest-configuration.md) di criteri di Azure per un computer disconnesso:

    * Un'assegnazione dei criteri di configurazione Guest destinata A computer disconnessi non è interessata.
    * L'assegnazione Guest viene archiviata localmente per 14 giorni. Nel periodo di 14 giorni, se l'agente del computer connesso si riconnette al servizio, verranno riapplicate le assegnazioni dei criteri.
    * Le assegnazioni vengono eliminate dopo 14 giorni e non vengono riassegnate al computer dopo il periodo di 14 giorni.

* L'agente di estensione gestisce le estensioni della macchina virtuale, tra cui installazione, disinstallazione e aggiornamento. Le estensioni vengono scaricate da Azure e copiate nella `%SystemDrive%\%ProgramFiles%\AzureConnectedMachineAgent\ExtensionService\downloads` cartella in Windows e per Linux in `/opt/GC_Ext/downloads` . In Windows, l'estensione viene installata nel percorso seguente `%SystemDrive%\Packages\Plugins\<extension>` e in Linux l'estensione viene installata in `/var/lib/waagent/<extension>` .

## <a name="instance-metadata"></a>Metadati dell'istanza

Le informazioni sui metadati relativi al computer connesso vengono raccolte dopo la registrazione dell'agente computer connesso con i server abilitati per Arc. In particolare:

* Nome, tipo e versione del sistema operativo
* Nome computer
* Nome di dominio completo (FQDN) del computer
* Versione dell'agente Connected Machine
* Nome di dominio completo (FQDN) di Active Directory e DNS
* UUID (ID BIOS)
* Heartbeat agente computer connesso
* Versione dell'agente Connected Machine
* Chiave pubblica per identità gestita
* Stato di conformità dei criteri e dettagli (se si usano i criteri di configurazione Guest di criteri di Azure)

Le seguenti informazioni sui metadati sono richieste dall'agente da Azure:

* Posizione della risorsa (regione)
* ID macchina virtuale
* Tag
* Certificato di identità gestito Azure Active Directory
* Assegnazioni dei criteri di configurazione Guest
* Richieste di estensione: installare, aggiornare ed eliminare.

## <a name="download-agents"></a>Scaricare agenti

È possibile scaricare il pacchetto dell'agente Azure Connected Machine per Windows e Linux dai percorsi elencati di seguito.

* [Pacchetto di installazione dell'agente Connected Machine per Windows](https://aka.ms/AzureConnectedMachineAgent) dall'Area download Microsoft.

* Il pacchetto dell'agente per Linux viene distribuito dal [repository dei pacchetti](https://packages.microsoft.com/) di Microsoft usando il formato di pacchetto preferito per la distribuzione (RPM o DEB).

L'agente Azure Connected Machine per Windows e Linux può essere aggiornato alla versione più recente manualmente o automaticamente in base alle esigenze. Per altre informazioni, vedere [qui](manage-agent.md).

## <a name="prerequisites"></a>Prerequisiti

### <a name="supported-environments"></a>Ambienti supportati

I server abilitati per Arc supportano l'installazione dell'agente del computer connesso in qualsiasi server fisico e macchina virtuale ospitata *all'esterno* di Azure. Sono incluse le macchine virtuali in esecuzione su piattaforme come VMware, Azure Stack HCI e altri ambienti cloud. I server abilitati per Arc non supportano l'installazione dell'agente in macchine virtuali in esecuzione in Azure o le macchine virtuali in esecuzione in Azure Stack Hub o Azure Stack Edge, perché sono già modellate come macchine virtuali di Azure.

### <a name="supported-operating-systems"></a>Sistemi operativi supportati

Le versioni seguenti dei sistemi operativi Windows e Linux sono ufficialmente supportate per l'agente Azure Connected Machine:

- Windows Server 2008 R2, Windows Server 2012 R2 e versioni successive (incluso Server Core)
- Ubuntu 16,04 e 18,04 LTS (x64)
- CentOS Linux 7 (x64)
- SUSE Linux Enterprise Server (SLES) 15 (x64)
- Red Hat Enterprise Linux (RHEL) 7 (x64)
- Amazon Linux 2 (x64)
- Oracle Linux 7

> [!WARNING]
> Il nome host Linux o il nome computer Windows non può contenere una delle parole riservate o dei marchi registrati, altrimenti la registrazione del computer connesso con Azure non riuscirà. Per un elenco delle parole riservate, vedere [Risolvere gli errori relativi ai nomi riservati delle risorse](../../azure-resource-manager/templates/error-reserved-resource-name.md).

### <a name="required-permissions"></a>Autorizzazioni necessarie

* Per caricare i computer, si è membri del ruolo di **onboarding** o [collaboratore](../../role-based-access-control/built-in-roles.md#contributor) del computer connesso di Azure nel gruppo di risorse.

* Per leggere, modificare ed eliminare un computer, si è membri del ruolo di **amministratore delle risorse del computer connesso di Azure** nel gruppo di risorse.

* Per selezionare un gruppo di risorse dall'elenco a discesa quando si usa il metodo **Genera script** , come minimo si è un membro del ruolo [lettore](../../role-based-access-control/built-in-roles.md#reader) per quel gruppo di risorse.

### <a name="azure-subscription-and-service-limits"></a>Limiti del servizio e della sottoscrizione di Azure

Prima di configurare i computer con i server abilitati per Azure Arc, esaminare i limiti della [sottoscrizione](../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits) Azure Resource Manager e dei [gruppi di risorse](../../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits) per pianificare il numero di computer da connettere.

I server abilitati per Azure Arc supportano fino a 5.000 istanze di computer in un gruppo di risorse.

### <a name="transport-layer-security-12-protocol"></a>Protocollo Transport Layer Security 1,2

Per garantire la sicurezza dei dati in transito verso Azure, è consigliabile configurare la macchina per usare il protocollo Transport Layer Security (TLS) 1.2. Le versioni precedenti di TLS/Secure Sockets Layer (SSL) sono state considerate vulnerabili. Nonostante siano ancora attualmente in uso per questioni di compatibilità con le versioni precedenti, **non sono consigliate**.

|Piattaforma/linguaggio | Supporto | Altre informazioni |
| --- | --- | --- |
|Linux | Le distribuzioni Linux si basano generalmente su [OpenSSL](https://www.openssl.org) per supportare TLS 1.2. | Controllare nel [log delle modifiche di OpenSSL](https://www.openssl.org/news/changelog.html) per assicurarsi che la versione di OpenSSL sia supportata.|
| Windows Server 2012 R2 e versioni successive | Supportato e abilitato per impostazione predefinita. | Assicurarsi che le [impostazioni predefinite](/windows-server/security/tls/tls-registry-settings) siano ancora in uso.|

### <a name="networking-configuration"></a>Configurazione delle impostazioni di rete

L'agente Connected Machine per Linux e Windows comunica in modo sicuro in uscita con Azure Arc sulla porta TCP 443. Se il computer si connette tramite un firewall o un server proxy per la comunicazione tramite Internet, esaminare quanto segue per conoscere i requisiti di configurazione di rete.

> [!NOTE]
> I server abilitati per Arc non supportano l'uso di un [gateway log Analytics](../../azure-monitor/agents/gateway.md) come proxy per l'agente del computer connesso.
>

Se la connettività in uscita è limitata dal firewall o dal server proxy, verificare che gli URL elencati di seguito non siano bloccati. Quando si concedono solo gli intervalli IP o i nomi di dominio necessari per la comunicazione dell'agente con il servizio, è necessario consentire l'accesso ai seguenti tag e URL del servizio.

Tag del servizio:

* AzureActiveDirectory
* AzureTrafficManager
* AzureResourceManager
* AzureArcInfrastructure

URL:

| Risorsa dell'agente | Descrizione |
|---------|---------|
|`management.azure.com`|Azure Resource Manager|
|`login.windows.net`|Azure Active Directory|
|`login.microsoftonline.com`|Azure Active Directory|
|`dc.services.visualstudio.com`|Application Insights|
|`*.guestconfiguration.azure.com` |Configurazione guest|
|`*.his.arc.azure.com`|Servizio ibrido di gestione delle identità|
|`www.office.com`|Office 365|

Gli agenti di anteprima (versione 0,11 e inferiore) richiedono anche l'accesso agli URL seguenti:

| Risorsa dell'agente | Descrizione |
|---------|---------|
|`agentserviceapi.azure-automation.net`|Configurazione guest|
|`*-agentservice-prod-1.azure-automation.net`|Configurazione guest|

Per un elenco degli indirizzi IP per ogni tag del servizio/area, vedere il file JSON [Azure IP Ranges and Service Tags – Public Cloud](https://www.microsoft.com/download/details.aspx?id=56519) (Indirizzi IP di Azure e tag del servizio - Cloud pubblico). Microsoft pubblica aggiornamenti settimanali contenenti ogni servizio di Azure e gli intervalli IP usati dal servizio. Queste informazioni nel file JSON sono l'elenco temporizzato corrente degli intervalli IP che corrispondono a ogni tag di servizio. Gli indirizzi IP sono soggetti a modifiche. Se gli intervalli di indirizzi IP sono necessari per la configurazione del firewall, occorre usare il tag del servizio **AzureCloud** per consentire l'accesso a tutti i servizi di Azure. Non disabilitare il monitoraggio della sicurezza o l'ispezione di questi URL, ma consentire tali URL come si farebbe con il resto del traffico Internet.

Per altre informazioni, vedere [Cenni preliminari sui tag di servizio](../../virtual-network/service-tags-overview.md).

### <a name="register-azure-resource-providers"></a>Registrare i provider di risorse di Azure

I server abilitati per Azure Arc dipendono dai provider di risorse di Azure seguenti nella sottoscrizione per poter usare questo servizio:

* **Microsoft.HybridCompute**
* **Microsoft.GuestConfiguration**

Se non sono registrati, è possibile registrarli con i comandi seguenti:

Azure PowerShell:

```azurepowershell-interactive
Login-AzAccount
Set-AzContext -SubscriptionId [subscription you want to onboard]
Register-AzResourceProvider -ProviderNamespace Microsoft.HybridCompute
Register-AzResourceProvider -ProviderNamespace Microsoft.GuestConfiguration
```

Interfaccia della riga di comando di Azure:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

È anche possibile registrare i provider di risorse nel portale seguendo la procedura descritta nel [portale di Azure](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

## <a name="installation-and-configuration"></a>Installazione e configurazione

La connessione delle macchine virtuali nell'ambiente ibrido direttamente con Azure può essere eseguita usando metodi diversi a seconda dei requisiti. La tabella seguente illustra ogni metodo per determinare quello più adatto alla propria organizzazione.

> [!IMPORTANT]
> Non è possibile installare l'agente del computer connesso in una macchina virtuale Windows di Azure. Se si tenta di eseguire questa operazione, viene rilevata l'installazione e viene eseguito il rollback.

| Metodo | Descrizione |
|--------|-------------|
| In modo interattivo | Installare manualmente l'agente in una singola o in un numero limitato di macchine virtuali seguendo i passaggi descritti in [Connettere i computer ad Azure con Azure Arc per server - Portale](onboard-portal.md).<br> Dal portale di Azure è possibile generare uno script ed eseguirlo sulla macchina virtuale per automatizzare i passaggi di installazione e configurazione dell'agente.|
| Su larga scala | Installare e configurare l'agente per più macchine virtuali seguendo le procedure in [Connettere computer ad Azure con Azure Arc per server - PowerShell](onboard-service-principal.md).<br> Questo metodo crea un'entità servizio per connettere le macchine virtuali in modo non interattivo.|
| Su larga scala | Installare e configurare l'agente per più computer seguendo il metodo descritto in [Uso della piattaforma DSC di Azure PowerShell](onboard-dsc.md).<br> Questo metodo prevede l'uso di un'entità servizio per connettere i computer in modo non interattivo con DSC di PowerShell. |

## <a name="connected-machine-agent-technical-overview"></a>Panoramica tecnica sull'agente di computer connesso

### <a name="windows-agent-installation-details"></a>Dettagli sull'installazione dell'agente per Windows

È possibile installare l'agente Connected Machine per Windows usando uno dei tre metodi seguenti:

* Facendo doppio clic sul file `AzureConnectedMachineAgent.msi`.
* Manualmente eseguendo il pacchetto di Windows Installer `AzureConnectedMachineAgent.msi` dalla shell dei comandi.
* Da una sessione di PowerShell usando un metodo con script.

Dopo aver installato l'agente computer connesso per Windows, vengono applicate le seguenti modifiche di configurazione a livello di sistema.

* Durante l'installazione, vengono create le cartelle di installazione seguenti.

    |Cartella |Descrizione |
    |-------|------------|
    |%ProgramFiles%\AzureConnectedMachineAgent |Percorso di installazione predefinito che contiene i file di supporto dell'agente.|
    |%ProgramData%\AzureConnectedMachineAgent |Contiene i file di configurazione dell'agente.|
    |%ProgramData%\AzureConnectedMachineAgent\Tokens |Contiene i token acquisiti.|
    |%ProgramData%\AzureConnectedMachineAgent\Config |Contiene il file di configurazione dell'agente `agentconfig.json`, che documenta le informazioni di registrazione con il servizio.|
    |%ProgramFiles%\ArcConnectedMachineAgent\ExtensionService\GC | Percorso di installazione contenente i file dell'agente di configurazione Guest. |
    |%ProgramData%\GuestConfig |Contiene i criteri applicati da Azure.|
    |%ProgramFiles%\AzureConnectedMachineAgent\ExtensionService\downloads | Le estensioni vengono scaricate da Azure e copiate qui.|

* I servizi Windows seguenti vengono creati nel computer di destinazione durante l'installazione dell'agente.

    |Nome servizio |Nome visualizzato |Nome del processo |Descrizione |
    |-------------|-------------|-------------|------------|
    |himds |Servizio metadati dell'istanza di Azure Hybrid |himds |Questo servizio implementa il servizio metadati dell'istanza di Azure (IMDS) per gestire la connessione ad Azure e l'identità di Azure del computer connesso.|
    |GCArcService |Servizio Arc di configurazione Guest |gc_service |Monitora la configurazione dello stato desiderato del computer.|
    |ExtensionService |Servizio estensione configurazione Guest | gc_service |Installa le estensioni necessarie destinate al computer.|

* Durante l'installazione dell'agente, vengono create le variabili di ambiente seguenti.

    |Nome |Valore predefinito |Descrizione |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |http://localhost:40342/metadata/identity/oauth2/token ||
    |IMDS_ENDPOINT |http://localhost:40342 ||

* Sono disponibili diversi file di log per la risoluzione dei problemi. descritti nella tabella seguente.

    |File di log |Descrizione |
    |----|------------|
    |%ProgramData%\AzureConnectedMachineAgent\Log\himds.log |Registra informazioni dettagliate sul servizio agenti (HIMDS) e sull'interazione con Azure.|
    |%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log |Contiene l'output dei comandi dello strumento azcmagent, quando viene usato l'argomento verbose (-v).|
    |%ProgramData%\GuestConfig\gc_agent_logs\gc_agent.log |Registra informazioni dettagliate sull'attività del servizio DSC,<br> in particolare la connettività tra il servizio HIMDS e i criteri di Azure.|
    |%ProgramData%\GuestConfig\gc_agent_logs\gc_agent_telemetry.txt |Registra informazioni dettagliate sulla telemetria del servizio DSC e la registrazione dettagliata.|
    |Ext_mgr_logs%ProgramData%\GuestConfig\|Registra informazioni dettagliate sul componente agente di estensione.|
    |Extension_logs%ProgramData%\GuestConfig\\<Extension>|Registra i dettagli dall'estensione installata.|

* Viene creato il gruppo di sicurezza locale **applicazioni di estensione dell'agente ibrido**.

* Durante la disinstallazione dell'agente, non vengono rimossi gli artefatti seguenti.

    * %ProgramData%\AzureConnectedMachineAgent\Log
    * %ProgramData%\AzureConnectedMachineAgent e sottodirectory
    * %ProgramData%\GuestConfig

### <a name="linux-agent-installation-details"></a>Dettagli sull'installazione dell'agente per Linux

L'agente Connected Machine per Linux viene fornito nel formato di pacchetto preferito per la distribuzione (RPM o DEB) ospitata nel [repository dei pacchetti](https://packages.microsoft.com/) di Microsoft. L'agente viene installato e configurato con il bundle di script della shell [Install_linux_azcmagent.sh](https://aka.ms/azcmagent).

Dopo aver installato l'agente computer connesso per Linux, vengono applicate le seguenti modifiche di configurazione a livello di sistema.

* Durante l'installazione, vengono create le cartelle di installazione seguenti.

    |Cartella |Descrizione |
    |-------|------------|
    |/var/opt/azcmagent/ |Percorso di installazione predefinito che contiene i file di supporto dell'agente.|
    |/opt/azcmagent/ |
    |GC_Ext/opt/ | Percorso di installazione contenente i file dell'agente di configurazione Guest.|
    |/opt/DSC/ |
    |/var/opt/azcmagent/tokens |Contiene i token acquisiti.|
    |/var/lib/GuestConfig |Contiene i criteri applicati da Azure.|
    |/opt/GC_Ext/downloads|Le estensioni vengono scaricate da Azure e copiate qui.|

* I daemon seguenti vengono creati nel computer di destinazione durante l'installazione dell'agente.

    |Nome servizio |Nome visualizzato |Nome del processo |Descrizione |
    |-------------|-------------|-------------|------------|
    |himdsd.service |Servizio Agente computer connesso di Azure |himds |Questo servizio implementa il servizio metadati dell'istanza di Azure (IMDS) per gestire la connessione ad Azure e l'identità di Azure del computer connesso.|
    |GCAD. servce |Servizio GC Arc |gc_linux_service |Monitora la configurazione dello stato desiderato del computer. |
    |extd. Service |Servizio di estensione |gc_linux_service | Installa le estensioni necessarie destinate al computer.|

* Sono disponibili diversi file di log per la risoluzione dei problemi. descritti nella tabella seguente.

    |File di log |Descrizione |
    |----|------------|
    |/var/opt/azcmagent/log/himds.log |Registra informazioni dettagliate sul servizio agenti (HIMDS) e sull'interazione con Azure.|
    |/var/opt/azcmagent/log/azcmagent.log |Contiene l'output dei comandi dello strumento azcmagent, quando viene usato l'argomento verbose (-v).|
    |/opt/logs/dsc.log |Registra informazioni dettagliate sull'attività del servizio DSC,<br> in particolare la connettività tra il servizio himds e i criteri di Azure.|
    |/opt/logs/dsc.telemetry.txt |Registra informazioni dettagliate sulla telemetria del servizio DSC e la registrazione dettagliata.|
    |ext_mgr_logs/var/lib/GuestConfig/ |Registra informazioni dettagliate sul componente agente di estensione.|
    |extension_logs/var/lib/GuestConfig/|Registra i dettagli dall'estensione installata.|

* Durante l'installazione dell'agente, vengono create le variabili di ambiente seguenti. Queste variabili vengono impostate in `/lib/systemd/system.conf.d/azcmagent.conf`.

    |Nome |Valore predefinito |Descrizione |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |http://localhost:40342/metadata/identity/oauth2/token ||
    |IMDS_ENDPOINT |http://localhost:40342 ||

* Durante la disinstallazione dell'agente, non vengono rimossi gli artefatti seguenti.

    * /var/opt/azcmagent
    * /opt/logs

## <a name="next-steps"></a>Passaggi successivi

* Per iniziare a valutare i server abilitati per Azure Arc, seguire l'articolo [connettere macchine ibride ad Azure dalla portale di Azure](onboard-portal.md).

* Le informazioni sulla risoluzione dei problemi sono reperibili nella [Guida alla risoluzione dei problemi relativi all'agente del computer connesso](troubleshoot-agent-onboard.md).
