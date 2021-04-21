---
title: Configurare un contenitore personalizzato
description: Informazioni su come configurare un contenitore personalizzato in Servizio app di Azure. Questo articolo illustra le attività di configurazione più comuni.
ms.topic: article
ms.date: 02/23/2021
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 7bfebe318d93a544c964d70ea0a28144a7f0e43b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764244"
---
# <a name="configure-a-custom-container-for-azure-app-service"></a>Configurare un contenitore personalizzato per il Servizio app di Azure

Questo articolo illustra come configurare un contenitore personalizzato per l'esecuzione in Servizio app di Azure.

::: zone pivot="container-windows"

Questa guida fornisce i concetti chiave e le istruzioni per la containerizzazione delle app di Windows nel servizio app. Se non si è mai usato Servizio app di Azure, seguire prima l'esercitazione e la [guida](quickstart-custom-container.md) introduttiva per i [contenitori](tutorial-custom-container.md) personalizzati.

::: zone-end

::: zone pivot="container-linux"

Questa guida fornisce i concetti chiave e le istruzioni per la containerizzazione di app Linux nel servizio app. Se non si è mai usato Servizio app di Azure, seguire prima l'esercitazione e la [guida](quickstart-custom-container.md) introduttiva per i [contenitori](tutorial-custom-container.md) personalizzati. Sono disponibili anche una guida introduttiva [e un'esercitazione per app multi-contenitore.](quickstart-multi-container.md) [](tutorial-multi-container-app.md)

::: zone-end

::: zone pivot="container-windows"

## <a name="supported-parent-images"></a>Immagini padre supportate

Per l'immagine Windows personalizzata, è necessario scegliere l'immagine padre [destra (immagine di base)](https://docs.docker.com/develop/develop-images/baseimages/) per il framework desiderato:

- Per distribuire .NET Framework, usare un'immagine padre basata sulla versione [ltSC (Windows](/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc) Server Core Long-Term Servicing Channel). 
- Per distribuire app .NET Core, usare un'immagine padre basata sulla versione [SAC (Semi-Annual Servicing Channel) di](/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) Windows Server Nano. 

Il download di un'immagine padre durante l'avvio dell'app richiede tempo. È tuttavia possibile ridurre i tempi di avvio usando una delle immagini padre seguenti, già memorizzate nella cache nel servizio app di Azure.

- [mcr.microsoft.com/windows/servercore](https://hub.docker.com/_/microsoft-windows-servercore):2004
- [mcr.microsoft.com/windows/servercore](https://hub.docker.com/_/microsoft-windows-servercore):ltsc2019
- [mcr.microsoft.com/dotnet/framework/aspnet](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/):4.8-windowsservercore-2004
- [mcr.microsoft.com/dotnet/framework/aspnet](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/):4.8-windowsservercore-ltsc2019
- [mcr.microsoft.com/dotnet/core/runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/):3.1-nanoserver-2004
- [mcr.microsoft.com/dotnet/core/runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/):3.1-nanoserver-1909
- [mcr.microsoft.com/dotnet/core/runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/):3.1-nanoserver-1903
- [mcr.microsoft.com/dotnet/core/runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/):3.1-nanoserver-1809
- [mcr.microsoft.com/dotnet/core/aspnet](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/):3.1-nanoserver-2004
- [mcr.microsoft.com/dotnet/core/aspnet](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/):3.1-nanoserver-1909
- [mcr.microsoft.com/dotnet/core/aspnet](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/):3.1-nanoserver-1903
- [mcr.microsoft.com/dotnet/core/aspnet](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/):3.1-nanoserver-1809

::: zone-end

## <a name="change-the-docker-image-of-a-custom-container"></a>Modificare l'immagine Docker di un contenitore personalizzato

Per modificare un'app contenitore personalizzata esistente dall'immagine Docker corrente a una nuova immagine, usare il comando seguente:

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <docker-hub-repo>/<image>
```

## <a name="use-an-image-from-a-private-registry"></a>Usare un'immagine da un registro privato

Per usare un'immagine da un registro privato, ad esempio Registro Azure Container, eseguire il comando seguente:

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name> --docker-registry-server-url <private-repo-url> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

Per *\<username>* e , specificare le credenziali di accesso per *\<password>* l'account del registro privato.

## <a name="i-dont-see-the-updated-container"></a>Il contenitore aggiornato non è visualizzato

Se si modificano le impostazioni del contenitore Docker in modo che punti a un nuovo contenitore, l'app potrebbe richiedere alcuni minuti prima che l'app possa determinare richieste HTTP dal nuovo contenitore. Mentre il nuovo contenitore viene estratto e avviato, il servizio app continua a gestire le richieste dal contenitore precedente. Solo quando il nuovo contenitore viene avviato e pronto per ricevere richieste, il servizio app inizia a inviare richieste.

## <a name="how-container-images-are-stored"></a>Come vengono archiviate le immagini del contenitore

La prima volta che si esegue un'immagine Docker personalizzata nel servizio app, il servizio app esegue un'operazione ed esegue `docker pull` il pull di tutti i livelli immagine. Questi livelli vengono archiviati su disco, come se si usava Docker in locale. Ogni volta che l'app viene riavviata, il servizio app esegue `docker pull` un' , ma esegue solo il pull dei livelli che sono stati modificati. Se non sono state apportate modifiche, il servizio app usa i livelli esistenti nel disco locale.

Se l'app modifica le istanze di calcolo per qualsiasi motivo, ad esempio per aumentare o ridurre i piani tariffari, il servizio app deve eseguire nuovamente il pull di tutti i livelli. Lo stesso vale se si scala orizzontalmente per aggiungere altre istanze. Esistono anche rari casi in cui le istanze dell'app possono cambiare senza un'operazione di ridimensionamento.

## <a name="configure-port-number"></a>Configurare il numero di porta

Per impostazione predefinita, il servizio app presuppone che il contenitore personalizzato sia in ascolto sulla porta 80. Se il contenitore è in ascolto su una porta diversa, impostare `WEBSITES_PORT` l'impostazione dell'app nell'app del servizio app. È possibile impostarlo tramite il [Cloud Shell](https://shell.azure.com). In Bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

In PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITES_PORT"="8000"}
```

Il servizio app attualmente consente al contenitore di esporre una sola porta per le richieste HTTP. 

## <a name="configure-environment-variables"></a>Configurare le variabili di ambiente

Il contenitore personalizzato può usare variabili di ambiente che devono essere fornite esternamente. È possibile passarli tramite il [Cloud Shell](https://shell.azure.com). In Bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings DB_HOST="myownserver.mysql.database.azure.com"
```

In PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"DB_HOST"="myownserver.mysql.database.azure.com"}
```

Quando l'app viene eseguita, le impostazioni dell'app del servizio app vengono immesse automaticamente nel processo come variabili di ambiente. È possibile verificare le variabili di ambiente del contenitore con l'URL `https://<app-name>.scm.azurewebsites.net/Env)` .

Se l'app usa immagini da un registro privato o da Docker Hub, le credenziali per l'accesso al repository vengono salvate nelle variabili di ambiente : `DOCKER_REGISTRY_SERVER_URL` `DOCKER_REGISTRY_SERVER_USERNAME` e `DOCKER_REGISTRY_SERVER_PASSWORD` . A causa dei rischi per la sicurezza, nessuno di questi nomi di variabili riservate viene esposto all'applicazione.

::: zone pivot="container-windows"
Per i contenitori basati su IIS o .NET Framework (4.0 o versioni successive), vengono inseriti automaticamente come impostazioni dell'app .NET e stringhe di connessione dal `System.ConfigurationManager` servizio app. Per tutti gli altri linguaggi o framework, vengono forniti come variabili di ambiente per il processo, con uno dei prefissi corrispondenti seguenti:

- `APPSETTING_`
- `SQLCONTR_`
- `MYSQLCONTR_`
- `SQLAZURECOSTR_`
- `POSTGRESQLCONTR_`
- `CUSTOMCONNSTR_`

::: zone-end

::: zone pivot="container-linux"

Questo metodo funziona sia per le app a contenitore singolo che per le app multi-contenitore, in cui le variabili di ambiente vengono specificate nel file *docker-compose.yml.*

::: zone-end

## <a name="use-persistent-shared-storage"></a>Usare l'archiviazione condivisa persistente

::: zone pivot="container-windows"

È possibile usare la directory *C:\home* nella directory dell'app file system salvare in modo permanente i file tra i riavvii e condividerli tra le istanze. `C:\home`Nell'app viene fornito per consentire all'app contenitore di accedere all'archiviazione permanente.

Quando l'archiviazione persistente è disabilitata, le scritture nella `C:\home` directory non vengono rese persistenti. [I log dell'host Docker](#access-diagnostic-logs) e i log dei contenitori vengono salvati in un archivio condiviso permanente predefinito non collegato al contenitore. Quando è abilitata l'archiviazione permanente, tutte le scritture nella directory vengono rese persistenti ed è possibile accedervi da tutte le istanze di un'app con scalabilità orizzontale e il `C:\home` log è accessibile all'indirizzo `C:\home\LogFiles` .

::: zone-end

::: zone pivot="container-linux"

È possibile usare la directory */home* nel percorso dell'app file system salvare in modo permanente i file tra i riavvii e condividerli tra le istanze. `/home`Nell'app viene fornito per consentire all'app contenitore di accedere all'archiviazione permanente.

Quando l'archiviazione permanente è disabilitata, le scritture nella directory non vengono `/home` mantenute tra i riavvii dell'app o tra più istanze. L'unica eccezione è la `/home/LogFiles` directory , che viene usata per archiviare i log di Docker e del contenitore. Quando è abilitata l'archiviazione permanente, tutte le scritture nella directory vengono rese persistenti ed è possibile accedervi da tutte le istanze di `/home` un'app con scalabilità orizzontale.

::: zone-end

Per impostazione predefinita, l'archiviazione permanente è disabilitata e l'impostazione non viene esposta nelle impostazioni dell'app. Per abilitarla, impostare `WEBSITES_ENABLE_APP_SERVICE_STORAGE` l'impostazione dell'app [tramite il Cloud Shell](https://shell.azure.com). In Bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=true
```

In PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITES_ENABLE_APP_SERVICE_STORAGE"=true}
```

> [!NOTE]
> È anche possibile [configurare la propria archiviazione permanente.](configure-connect-to-azure-storage.md)

## <a name="detect-https-session"></a>Rilevare una sessione HTTPS

Il servizio app termina TLS/SSL nei front-end. Ciò significa che le richieste TLS/SSL non ottengono mai l'app. Non è necessario e non deve implementare alcun supporto per TLS/SSL nell'app. 

I front-end si trovano all'interno dei data center di Azure. Se si usa TLS/SSL con l'app, il traffico su Internet verrà sempre crittografato in modo sicuro.

::: zone pivot="container-windows"

## <a name="customize-aspnet-machine-key-injection"></a>Personalizzare l ASP.NET della chiave del computer

 Durante l'avvio del contenitore, le chiavi generate automaticamente vengono inserite nel contenitore come chiavi del computer per ASP.NET di crittografia. È possibile [trovare queste chiavi nel contenitore](#connect-to-the-container) cercando le variabili di ambiente `MACHINEKEY_Decryption` seguenti: , , , `MACHINEKEY_DecryptionKey` `MACHINEKEY_ValidationKey` `MACHINEKEY_Validation` . 

Le nuove chiavi a ogni riavvio possono reimpostare l ASP.NET'autenticazione basata su form e lo stato di visualizzazione, se l'app dipende da esse. Per impedire la rigenerazione automatica delle chiavi, [impostarle manualmente come impostazioni dell'app del servizio app.](#configure-environment-variables) 

## <a name="connect-to-the-container"></a>Connettersi al contenitore

È possibile connettersi al contenitore Windows direttamente per le attività di diagnostica passando a `https://<app-name>.scm.azurewebsites.net/DebugConsole` . Il funzionamento è il seguente:

- La console di debug consente di eseguire comandi interattivi, ad esempio l'avvio di sessioni di PowerShell, l'ispezione delle chiavi del Registro di sistema e l'esplorazione dell'intero contenitore file system.
- Funziona separatamente dal browser grafico precedente, che mostra solo i file [nell'archiviazione condivisa.](#use-persistent-shared-storage)
- In un'app con scalabilità orizzontale la console di debug è connessa a una delle istanze del contenitore. È possibile selezionare un'istanza diversa **dall'elenco a** discesa Istanza nel menu in alto.
- Qualsiasi modifica apportata al contenitore  dall'interno della console non viene mantenuta quando l'app viene riavviata (ad eccezione delle modifiche nell'archiviazione condivisa), perché non fa parte dell'immagine Docker. Per rendere persistenti le modifiche, ad esempio le impostazioni del Registro di sistema e l'installazione del software, renderle parte del Dockerfile.

## <a name="access-diagnostic-logs"></a>Accedere ai log di diagnostica

Il servizio app registra le azioni dell'host Docker e le attività dall'interno del contenitore. I log dell'host Docker (log della piattaforma) vengono forniti per impostazione predefinita, ma i log delle applicazioni o i log del server Web dall'interno del contenitore devono essere abilitati manualmente. Per altre informazioni, vedere [Abilitare la registrazione delle applicazioni](troubleshoot-diagnostic-logs.md#enable-application-logging-linuxcontainer) e Abilitare la registrazione del server [Web.](troubleshoot-diagnostic-logs.md#enable-web-server-logging) 

Esistono diversi modi per accedere ai log docker:

- [Nel portale di Azure](#in-azure-portal)
- [Dalla console kudu](#from-the-kudu-console)
- [Con l'API Kudu](#with-the-kudu-api)
- [Inviare i log a Monitoraggio di Azure](troubleshoot-diagnostic-logs.md#send-logs-to-azure-monitor-preview)

### <a name="in-azure-portal"></a>Nel portale di Azure

I log docker vengono visualizzati nel portale, nella pagina **Impostazioni contenitore** dell'app. I log vengono troncati, ma è possibile scaricare tutti i log facendo clic su **Scarica**. 

### <a name="from-the-kudu-console"></a>Dalla console kudu

Passare alla `https://<app-name>.scm.azurewebsites.net/DebugConsole` cartella **LogFiles** e fare clic su per visualizzare i singoli file di log. Per scaricare l'intera directory **LogFiles,** fare clic sull'icona **Download** a sinistra del nome della directory. È anche possibile accedere a questa cartella usando un client FTP.

Nel terminale della console non è possibile accedere alla cartella per impostazione predefinita perché `C:\home\LogFiles` l'archiviazione condivisa permanente non è abilitata. Per abilitare questo comportamento nel terminale della console, [abilitare l'archiviazione condivisa permanente.](#use-persistent-shared-storage)

Se si tenta di scaricare il log di Docker attualmente in uso usando un client FTP, è possibile che venga visualizzato un errore a causa di un blocco di file.

### <a name="with-the-kudu-api"></a>Con l'API Kudu

Passare direttamente a `https://<app-name>.scm.azurewebsites.net/api/logs/docker` per visualizzare i metadati per i log di Docker. È possibile visualizzare più di un file di log elencato e la `href` proprietà consente di scaricare direttamente il file di log. 

Per scaricare tutti i log insieme in un unico file ZIP, accedere a `https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip` .

## <a name="customize-container-memory"></a>Personalizzare la memoria del contenitore

Per impostazione predefinita, tutti i contenitori di Windows Servizio app di Azure sono limitati a 1 GB di RAM. È possibile modificare questo valore specificando `WEBSITE_MEMORY_LIMIT_MB` l'impostazione dell'app [tramite](https://shell.azure.com)il Cloud Shell . In Bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_MEMORY_LIMIT_MB=2000
```

In PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITE_MEMORY_LIMIT_MB"=2000}
```

Il valore è definito in MB e deve essere minore e uguale alla memoria fisica totale dell'host. Ad esempio, in un piano di servizio app con 8 GB di RAM, il totale cumulativo di per tutte le app non `WEBSITE_MEMORY_LIMIT_MB` deve superare gli 8 GB. Per informazioni sulla quantità di memoria disponibile per ogni piano tariffario, vedere Prezzi del servizio app [nella](https://azure.microsoft.com/pricing/details/app-service/windows/)sezione Piano del **contenitore Premium (Windows).**

## <a name="customize-the-number-of-compute-cores"></a>Personalizzare il numero di core di calcolo

Per impostazione predefinita, un contenitore Windows viene eseguito con tutti i core disponibili per il piano tariffario scelto. È ad esempio possibile ridurre il numero di core utilizzati nello slot di staging. Per ridurre il numero di core usati da un contenitore, impostare `WEBSITE_CPU_CORES_LIMIT` l'impostazione dell'app sul numero preferito di core. È possibile impostarlo tramite il [Cloud Shell](https://shell.azure.com). In Bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --slot staging --settings WEBSITE_CPU_CORES_LIMIT=1
```

In PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITE_CPU_CORES_LIMIT"=1}
```

> [!NOTE]
> L'aggiornamento dell'impostazione dell'app attiva il riavvio automatico, causando tempi di inattività minimi. Per un'app di produzione, provare a scambiarla in uno slot di staging, modificare l'impostazione dell'app nello slot di staging e quindi scambiarla nuovamente nell'ambiente di produzione.

Verificare il numero modificato andando alla console Kudu ( ) e `https://<app-name>.scm.azurewebsites.net` digitando i comandi seguenti usando PowerShell. Ogni comando restituisce un numero.

```PowerShell
Get-ComputerInfo | ft CsNumberOfLogicalProcessors # Total number of enabled logical processors. Disabled processors are excluded.
Get-ComputerInfo | ft CsNumberOfProcessors # Number of physical processors.
```

I processori possono essere processori multicore o hyperthreading. Informazioni sul numero di core disponibili per ogni piano tariffario sono disponibili nella sezione Prezzi del servizio [app](https://azure.microsoft.com/pricing/details/app-service/windows/)nella sezione Piano del contenitore **Premium (Windows).**

## <a name="customize-health-ping-behavior"></a>Personalizzare il comportamento del ping di integrità

Il servizio app considera un contenitore avviato correttamente all'avvio del contenitore e risponde a un ping HTTP. La richiesta ping di integrità contiene l'intestazione `User-Agent= "App Service Hyper-V Container Availability Check"` . Se il contenitore viene avviato ma non risponde a un ping dopo un determinato periodo di tempo, il servizio app registra un evento nel log Docker, indicando che il contenitore non è stato avviato. 

Se l'applicazione è a elevato utilizzo di risorse, il contenitore potrebbe non rispondere al ping HTTP nel tempo. Per controllare le azioni quando i ping HTTP hanno esito negativo, impostare `CONTAINER_AVAILABILITY_CHECK_MODE` l'impostazione dell'app. È possibile impostarlo tramite il [Cloud Shell](https://shell.azure.com). In Bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings CONTAINER_AVAILABILITY_CHECK_MODE="ReportOnly"
```

In PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"CONTAINER_AVAILABILITY_CHECK_MODE"="ReportOnly"}
```

La tabella seguente illustra i valori possibili:

| Valore | Descrizioni |
| - | - |
| **Riparazione** | Riavviare il contenitore dopo tre controlli di disponibilità consecutivi |
| **ReportOnly** | Il valore predefinito. Non riavviare il contenitore, ma creare report nei log Docker per il contenitore dopo tre controlli di disponibilità consecutivi. |
| **Disattivato** | Non verificare la disponibilità. |

## <a name="support-for-group-managed-service-accounts"></a>Supporto per gli account del servizio gestiti del gruppo

Gli account del servizio gestito del gruppo (gMSA) non sono attualmente supportati nei contenitori di Windows nel servizio app.

::: zone-end

::: zone pivot="container-linux"

## <a name="enable-ssh"></a>Abilitare SSH

SSH consente la comunicazione sicura tra un contenitore e un client. Perché un contenitore personalizzato supporti SSH, è necessario aggiungerlo all'immagine Docker stessa.

> [!TIP]
> Tutti i contenitori Linux predefiniti nel servizio app hanno aggiunto le istruzioni SSH nei repository di immagini. È possibile seguire le istruzioni seguenti con il [ repositoryNode.js 10.14](https://github.com/Azure-App-Service/node/blob/master/10.14) per vedere come è abilitato. La configurazione nel Node.js'immagine predefinita è leggermente diversa, ma in linea di principio.

- Aggiungere [un file sshd_config al](https://man.openbsd.org/sshd_config) repository, come nell'esempio seguente.

    ```
    Port            2222
    ListenAddress       0.0.0.0
    LoginGraceTime      180
    X11Forwarding       yes
    Ciphers aes128-cbc,3des-cbc,aes256-cbc,aes128-ctr,aes192-ctr,aes256-ctr
    MACs hmac-sha1,hmac-sha1-96
    StrictModes         yes
    SyslogFacility      DAEMON
    PasswordAuthentication  yes
    PermitEmptyPasswords    no
    PermitRootLogin     yes
    Subsystem sftp internal-sftp
    ```

    > [!NOTE]
    > Questo file configura OpenSSH e deve includere gli elementi seguenti:
    > - `Port` deve essere impostato su 2222.
    > - `Ciphers` deve includere almeno un elemento di questo elenco: `aes128-cbc,3des-cbc,aes256-cbc`.
    > - `MACs` deve includere almeno un elemento di questo elenco: `hmac-sha1,hmac-sha1-96`.

- Nel Dockerfile aggiungere i comandi seguenti:

    ```Dockerfile
    # Install OpenSSH and set the password for root to "Docker!". In this example, "apk add" is the install instruction for an Alpine Linux-based image.
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 

    # Copy the sshd_config file to the /etc/ssh/ directory
    COPY sshd_config /etc/ssh/

    # Open port 2222 for SSH access
    EXPOSE 80 2222
    ```

    Questa configurazione non consente connessioni esterne al contenitore. La porta 2222 del contenitore è accessibile solo all'interno della rete bridge di una rete virtuale privata e non è accessibile a un utente malintenzionato su Internet.

- Nello script di avvio per il contenitore avviare il server SSH.

    ```bash
    /usr/sbin/sshd
    ```

## <a name="access-diagnostic-logs"></a>Accedere ai log di diagnostica

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

## <a name="configure-multi-container-apps"></a>Configurare app multi-contenitore

- [Usare l'archiviazione permanente in Docker Compose](#use-persistent-storage-in-docker-compose)
- [Limiti di anteprima](#preview-limitations)
- [Docker Compose opzioni](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Usare l'archiviazione permanente in Docker Compose

Le app multi-contenitore come WordPress necessitano di archiviazione permanente per funzionare correttamente. Per abilitarla, la configurazione Docker Compose deve puntare a una posizione di archiviazione *all'esterno* del contenitore. Le posizioni di archiviazione all'interno del contenitore non masistono nelle modifiche oltre il riavvio dell'app.

Abilitare l'archiviazione permanente impostando `WEBSITES_ENABLE_APP_SERVICE_STORAGE` l'impostazione dell'app usando il [comando az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) in [Cloud Shell](https://shell.azure.com).

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

Nel file *docker-compose.yml* eseguire il mapping `volumes` dell'opzione a `${WEBAPP_STORAGE_HOME}` . 

`WEBAPP_STORAGE_HOME` è una variabile di ambiente nel servizio app mappata all'archiviazione permanente per l'app. Ad esempio:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
  - ${WEBAPP_STORAGE_HOME}/phpmyadmin:/var/www/phpmyadmin
  - ${WEBAPP_STORAGE_HOME}/LogFiles:/var/log
```

### <a name="preview-limitations"></a>Limiti di anteprima

Il contenitore multi-contenitore è attualmente in anteprima. Le funzionalità della piattaforma del servizio app seguenti non sono supportate:

- Autenticazione/Autorizzazione
- Identità gestite
- CORS

### <a name="docker-compose-options"></a>Docker Compose opzioni

Gli elenchi seguenti mostrano le opzioni di configurazione Docker Compose supportate:

#### <a name="supported-options"></a>Opzioni supportate

- .
- entrypoint
- ambiente
- image
- ports
- restart
- services
- volumes

#### <a name="unsupported-options"></a>Opzioni non supportate

- build (non consentita)
- depends_on (ignorata)
- networks (ignorata)
- secrets (ignorata)
- porte diverse da 80 e 8080 (ignorate)

> [!NOTE]
> Tutte le altre opzioni non chiamate in modo esplicito vengono ignorate nell'anteprima pubblica.

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: Eseguire la migrazione di software personalizzato Servizio app di Azure usando un contenitore personalizzato](tutorial-custom-container.md)

::: zone pivot="container-linux"

> [!div class="nextstepaction"]
> [Esercitazione: App WordPress multicontenitore](tutorial-multi-container-app.md)

::: zone-end

In caso contrario, vedere risorse aggiuntive:

[Caricare il certificato in contenitori Windows/Linux](configure-ssl-certificate-in-code.md#load-certificate-in-linuxwindows-containers)
