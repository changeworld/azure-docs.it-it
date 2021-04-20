---
title: Come usare DFS-N con File di Azure
description: Casi d'uso comuni di DFS-N con File di Azure
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 3/02/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: a8f1b8c54ad4fd42cc8ebda4965aaf1233143f39
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741987"
---
# <a name="how-to-use-dfs-namespaces-with-azure-files"></a>Come usare spazi dei nomi DFS con File di Azure
Spazi dei nomi dei [file system](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview)distribuiti, comunemente definiti spazi dei nomi DFS o DFS-N, è un ruolo del server Windows Server ampiamente usato per semplificare la distribuzione e la manutenzione delle condivisioni file SMB nell'ambiente di produzione. Spazi dei nomi DFS è una tecnologia di virtualizzazione dello spazio dei nomi di archiviazione, che consente di fornire un livello di riferimento indiretto tra il percorso UNC delle condivisioni file e le condivisioni file effettive. Spazi dei nomi DFS funziona con condivisioni file SMB, indipendente da tali condivisioni file ospitate: può essere usato con condivisioni SMB ospitate in un file server Windows locale con o senza Sincronizzazione file di Azure, condivisioni file di Azure direttamente, condivisioni file SMB ospitate in Azure NetApp Files o altre offerte di terze parti e anche con condivisioni file ospitate in altri cloud. 

Gli spazi dei nomi DFS forniscono un mapping tra un percorso UNC facile da usare, ad esempio e il percorso UNC sottostante della `\\contoso\shares\ProjectX` condivisione SMB, ad `\\Server01-Prod\ProjectX` esempio o `\\storageaccount.file.core.windows.net\projectx` . Quando l'utente finale vuole passare alla condivisione file, digita il percorso UNC descrittivo, ma il client SMB accede al percorso SMB sottostante del mapping. È anche possibile estendere questo concetto di base per assumere il controllo di un nome file server esistente, ad esempio `\\MyServer\ProjectX` . È possibile usare questa funzionalità per ottenere gli scenari seguenti:

- Specificare un nome a prova di migrazione per un set logico di dati. In questo esempio è presente un mapping simile a `\\contoso\shares\Engineering` che esegue il mapping a `\\OldServer\Engineering` . Dopo aver completato la migrazione a File di Azure, è possibile modificare il mapping in modo che il percorso UNC facile da usare punti a `\\storageaccount.file.core.windows.net\engineering` . Quando un utente finale accede al percorso UNC facile da usare, verrà reindirizzato facilmente al percorso di condivisione file di Azure.

- Definire un nome comune per un set logico di dati distribuito a più server in siti fisici diversi, ad esempio tramite Sincronizzazione file di Azure. In questo esempio viene eseguito il mapping di un nome come `\\contoso\shares\FileSyncExample` a più percorsi UNC, ad esempio `\\FileSyncServer1\ExampleShare` , , `\\FileSyncServer2\DifferentShareName` `\\FileSyncServer3\ExampleShare` . Quando l'utente accede all'unc descrittivo, gli viene fornito un elenco di possibili percorsi UNC e sceglie quello più vicino in base alle definizioni del sito di Windows Server Active Directory (AD).

- Estendere un set logico di dati tra dimensioni, I/O o altre soglie di scalabilità. Questo è comune quando si gestiscono directory utente, in cui ogni utente ottiene la propria cartella in una condivisione o con condivisioni scratch, in cui gli utenti ottengono spazio arbitrario per gestire le esigenze di dati temporanei. Con Gli spazi dei nomi DFS è possibile unire più cartelle in uno spazio dei nomi coeso. Ad esempio, `\\contoso\shares\UserShares\user1` esegue il mapping a , esegue il mapping a e così `\\storageaccount.file.core.windows.net\user1` `\\contoso\shares\UserShares\user2` `\\storageaccount.file.core.windows.net\user2` via.  

È possibile vedere un esempio di come usare Spazi dei nomi DFS con File di Azure distribuzione nella panoramica video seguente.

[![Demo su come configurare DFS-N con File di Azure: fare clic per riprodurre.](./media/files-manage-namespaces/video-snapshot-dfsn.png)](https://www.youtube.com/watch?v=jd49W33DxkQ)
> [!NOTE]  
> Passare alle 10:10 del video per informazioni su come configurare Spazi dei nomi DFS.

Se è già disponibile uno spazio dei nomi DFS, non sono necessari passaggi speciali per usarlo con File di Azure e Sincronizzazione file. Se si accede alla condivisione file di Azure dall'ambiente locale, si applicano le normali considerazioni sulla rete. Vedere [File di Azure considerazioni sulla rete](./storage-files-networking-overview.md) per altre informazioni.

## <a name="namespace-types"></a>Tipi di spazio dei nomi
Spazi dei nomi DFS offre due tipi di spazio dei nomi principali:

- **Spazio dei nomi basato su** dominio: spazio dei nomi ospitato come parte del Windows Server AD dominio. Gli spazi dei nomi ospitati come parte di AD avranno un percorso UNC contenente il nome del dominio, ad esempio `\\contoso.com\shares\myshare` , se il dominio è `contoso.com` . Gli spazi dei nomi basati su dominio supportano limiti di scalabilità maggiori e ridondanza incorporata tramite Ad. Gli spazi dei nomi basati su dominio non possono essere una risorsa cluster in un cluster di failover. 
- **Spazio dei nomi autonomo:** spazio dei nomi ospitato in un singolo server, non ospitato come parte di Windows Server AD. Gli spazi dei nomi autonomi avranno un nome basato sul nome del server autonomo, ad esempio , in cui il server autonomo `\\MyStandaloneServer\shares\myshare` è denominato `MyStandaloneServer` . Gli spazi dei nomi autonomi supportano destinazioni di scalabilità inferiori rispetto agli spazi dei nomi basati su dominio, ma possono essere ospitati come risorsa cluster in un cluster di failover.

## <a name="requirements"></a>Requisiti
Per usare spazi dei nomi DFS con File di Azure e Sincronizzazione file, è necessario disporre delle risorse seguenti:

- Di un domino Active Directory. Può essere ospitato ovunque si desideri, ad esempio in locale, in una macchina virtuale (VM) di Azure o anche in un altro cloud.
- Windows Server che può ospitare lo spazio dei nomi. Un modello di distribuzione modello comune per spazi dei nomi DFS è usare il controller di dominio Active Directory per ospitare gli spazi dei nomi, tuttavia gli spazi dei nomi possono essere installati da qualsiasi server con il ruolo del server Spazi dei nomi DFS installato. Spazi dei nomi DFS è disponibile in tutte le versioni supportate di Windows Server.
- Una condivisione file SMB ospitata in un ambiente aggiunto a un dominio, ad esempio una condivisione file di Azure ospitata all'interno di un account di archiviazione aggiunto a un dominio o una condivisione file ospitata in un file server Windows aggiunto al dominio usando Sincronizzazione file di Azure. Per altre informazioni sull'aggiunta al dominio dell'account di archiviazione, vedere [Autenticazione basata su identità.](storage-files-active-directory-overview.md) I file server Windows vengono aggiunti a un dominio nello stesso modo indipendentemente dal fatto che si Sincronizzazione file di Azure.
- Le condivisioni file SMB da usare con Spazi dei nomi DFS devono essere raggiungibili dalle reti locali. Si tratta principalmente di un problema per le condivisioni file di Azure, ma tecnicamente si applica a qualsiasi condivisione file ospitata in Azure o in qualsiasi altro cloud. Per altre informazioni sulla rete, vedere Considerazioni [sulla rete per l'accesso diretto.](storage-files-networking-overview.md)

## <a name="install-the-dfs-namespaces-server-role"></a>Installare il ruolo del server Spazi dei nomi DFS
Se si usano già spazi dei nomi DFS o si vuole configurare Spazi dei nomi DFS nel controller di dominio, è possibile ignorare questi passaggi.

# <a name="portal"></a>[Portale](#tab/azure-portal)
Per installare il ruolo del server Spazi dei nomi DFS, aprire il Server Manager nel server. Selezionare **Gestisci** e quindi Aggiungi ruoli **e funzionalità**. La procedura guidata risultante illustra l'installazione dei componenti di Windows necessari per eseguire e gestire spazi dei nomi DFS. 

Nella sezione **Tipo di installazione** dell'installazione guidata selezionare il pulsante di opzione **Installazione** basata su ruoli o basata su funzionalità e selezionare **Avanti.** Nella sezione **Selezione server** selezionare i server desiderati in cui si vuole installare il ruolo del server Spazi dei nomi DFS e selezionare **Avanti.** 

Nella sezione **Ruoli server** selezionare e selezionare il ruolo Spazi dei **nomi DFS** dall'elenco dei ruoli. È possibile trovarlo in **File di Servizi file e** archiviazione e Servizi  >  **ISCSI.** Quando si seleziona il ruolo del server Spazi dei nomi DFS, è anche possibile aggiungere eventuali ruoli server o funzionalità di supporto necessari non ancora installati.

![Screenshot della procedura guidata **Aggiungi ruoli e funzionalità** con il ruolo **Spazi dei nomi DFS** selezionato.](./media/files-manage-namespaces/dfs-namespaces-install.png)

Dopo aver selezionato il ruolo Spazi dei  nomi **DFS,** è  possibile selezionare Avanti in tutte le schermate successive e selezionare Installa non appena la procedura guidata abilita il pulsante. Al termine dell'installazione, è possibile configurare lo spazio dei nomi.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Da una sessione di PowerShell con privilegi elevati (o tramite la comunicazione remota di PowerShell), eseguire i comandi seguenti.

```PowerShell
Install-WindowsFeature -Name "FS-DFS-Namespace", "RSAT-DFS-Mgmt-Con"
```
---

## <a name="take-over-existing-server-names-with-root-consolidation"></a>Assumere i nomi dei server esistenti con il consolidamento radice
Un uso importante per Spazi dei nomi DFS è la proprietà di un nome di server esistente allo scopo di eseguire il refactoring del layout fisico delle condivisioni file. Ad esempio, è possibile consolidare le condivisioni file da più file server vecchi in un singolo file server durante una migrazione di modernizzazione. Tradizionalmente, la familiarità con l'utente finale e il collegamento dei documenti limitano la possibilità di consolidare le condivisioni file da file server diversi in un unico host, ma la funzionalità di consolidamento radice di Spazi dei nomi DFS consente di configurare un singolo server a più nomi di server e di instradare al nome di condivisione appropriato.

Sebbene sia utile per diversi scenari di migrazione dei data center, il consolidamento radice è particolarmente utile per l'adozione di condivisioni file di Azure native del cloud per i motivi seguenti:

- Le condivisioni file di Azure non consentono di mantenere i nomi dei server locali esistenti.
- È necessario accedere alle condivisioni file di Azure tramite il nome di dominio completo (FQDN) dell'account di archiviazione. Ad esempio, una condivisione file di Azure denominata `share` nell'account di archiviazione `storageaccount` è sempre accessibile tramite il percorso `\\storageaccount.file.core.windows.net\share` UNC. Questo può generare confusione per gli utenti finali che si aspettano un nome breve (ad esempio `\\MyServer\share`) o un nome che è un sottodominio del nome di dominio dell'organizzazione (ad esempio `\\MyServer.contoso.com\share`).

Il consolidamento radice può essere usato solo con spazi dei nomi autonomi. Se si dispone già di uno spazio dei nomi basato su dominio esistente per le condivisioni file, non è necessario creare uno spazio dei nomi radice consolidato.

### <a name="enabling-root-consolidation"></a>Abilitazione del consolidamento radice
Il consolidamento radice può essere abilitato impostando le chiavi del Registro di sistema seguenti da una sessione di PowerShell con privilegi elevati o usando la comunicazione remota di PowerShell.

```PowerShell
New-Item `
    -Path "HKLM:SYSTEM\CurrentControlSet\Services\Dfs" `
    -Type Registry `
    -ErrorAction SilentlyContinue
New-Item `
    -Path "HKLM:SYSTEM\CurrentControlSet\Services\Dfs\Parameters" `
    -Type Registry `
    -ErrorAction SilentlyContinue
New-Item `
    -Path "HKLM:SYSTEM\CurrentControlSet\Services\Dfs\Parameters\Replicated" `
    -Type Registry `
    -ErrorAction SilentlyContinue
Set-ItemProperty `
    -Path "HKLM:SYSTEM\CurrentControlSet\Services\Dfs\Parameters\Replicated" `
    -Name "ServerConsolidationRetry" `
    -Value 1
```

### <a name="creating-dns-entries-for-existing-file-server-names"></a>Creazione di voci DNS per i nomi file server esistenti
Per consentire agli spazi dei nomi DFS di rispondere ai nomi file server esistenti, creare record alias (CNAME) per i file server esistenti che puntano al nome del server Spazi dei nomi DFS. La procedura esatta per aggiornare i record DNS può dipendere dai server che l'organizzazione sta usando e se l'organizzazione usa strumenti personalizzati per automatizzare la gestione del DNS. La procedura seguente viene illustrata per il server DNS incluso in Windows Server e usata automaticamente da Windows AD.

# <a name="portal"></a>[Portale](#tab/azure-portal)
In un server DNS Windows aprire la console di gestione DNS. A tale scopo, selezionare il **pulsante Start** e digitare **DNS**. Passare alla zona di ricerca diretta per il dominio. Ad esempio, se il dominio è , la zona di ricerca diretta è disponibile in Zone di ricerca `contoso.com` **diretta** nella console  >  **`contoso.com`** di gestione. La gerarchia esatta visualizzata in questa finestra di dialogo dipende dalla configurazione DNS per la rete.

Fare clic con il pulsante destro del mouse sulla zona di ricerca diretta e **scegliere Nuovo alias (CNAME).** Nella finestra di dialogo risultante immettere il nome breve per il file server che si sta sostituendo. Il nome di dominio completo verrà popolato automaticamente nella casella di testo Nome di dominio **completo**. Nella casella di testo Nome di dominio completo **(FQDN)** per l'host di destinazione immettere il nome del server DFS-N configurato. È possibile usare il **pulsante** Sfoglia per selezionare il server, se necessario. Selezionare **OK** per creare il record CNAME per il server.

![Screenshot che illustra il **nuovo record di risorse** per una voce DNS CNAME.](./media/files-manage-namespaces/root-consolidation-cname.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
In un server DNS Windows aprire una sessione di PowerShell (o usare la comunicazione remota di PowerShell) per eseguire i comandi seguenti, popolando e , con i valori pertinenti per l'ambiente ( verrà popolato automaticamente con il nome di dominio, ma è anche possibile digitarlo `$oldServer` `$dfsnServer` `$domain` manualmente).

```PowerShell
# Variables
$oldServer = "MyServer"
$domain = Get-CimInstance -ClassName "Win32_ComputerSystem" | `
    Select-Object -ExpandProperty Domain
$dfsnServer = "CloudDFSN.$domain"

# Create CNAME record
Import-Module -Name DnsServer
Add-DnsServerResourceRecordCName `
    -Name $oldServer `
    -HostNameAlias $dfsnServer `
    -ZoneName $domain
```

---

## <a name="create-a-namespace"></a>Creare uno spazio dei nomi
L'unità di base di gestione per Spazi dei nomi DFS è lo spazio dei nomi . La radice dello spazio dei nomi, o nome, è il punto iniziale dello spazio dei nomi, in modo che nel percorso UNC la radice dello spazio dei `\\contoso.com\Public\` nomi sia `Public` . 

Se si usano spazi dei nomi DFS per assumere la proprietà di un nome di server esistente con consolidamento radice, il nome dello spazio dei nomi deve essere il nome del server che si vuole assumere, preceduto dal carattere `#` . Ad esempio, se si desidera assumere la proprietà di un server esistente denominato , si creerà uno spazio dei `MyServer` nomi DFS-N denominato `#MyServer` . La sezione di PowerShell seguente si occupa di anteporre , ma se si crea tramite la console di Gestione DFS, sarà necessario anteporre anteposto `#` in base alle esigenze. 

# <a name="portal"></a>[Portale](#tab/azure-portal)
Per creare un nuovo spazio dei nomi, aprire la console **Gestione DFS.** A tale scopo, fare clic sul **pulsante Start** e digitare **Gestione DFS.** La console di gestione  risultante include due sezioni Spazi dei nomi e Replica **,** che fanno riferimento rispettivamente a Spazi dei nomi DFS e Replica DFS (DFS-R). Sincronizzazione file di Azure offre un meccanismo di replica e sincronizzazione moderno che può essere usato al posto di DFS-R se si desidera anche la replica.

Selezionare la **sezione Spazi dei** nomi e selezionare il pulsante **Nuovo** spazio dei nomi (è anche possibile fare clic con il pulsante destro del mouse sulla sezione Spazi **dei** nomi). La Creazione **guidata nuovo spazio dei nomi risultante** illustra la creazione di uno spazio dei nomi. 

La prima sezione della procedura guidata richiede di selezionare il server Spazio dei nomi DFS per ospitare lo spazio dei nomi. Più server possono ospitare uno spazio dei nomi, ma è necessario configurare spazi dei nomi DFS con un server alla volta. Immettere il nome del server spazio dei nomi DFS desiderato e selezionare **Avanti.** Nella sezione **Nome spazio dei** nomi e impostazioni è possibile immettere il nome desiderato dello spazio dei nomi e selezionare **Avanti.** 

La **sezione Tipo spazio** dei nomi consente di scegliere tra uno spazio dei nomi basato su dominio e uno spazio dei nomi **autonomo.**  Se si intende usare Spazi dei nomi DFS per mantenere un nome di dispositivo file server/NAS esistente, è necessario selezionare l'opzione spazio dei nomi autonomo. Per qualsiasi altro scenario, è necessario selezionare uno spazio dei nomi basato su dominio. Per altre [informazioni sulla scelta tra i](#namespace-types) tipi di spazio dei nomi, vedere i tipi di spazio dei nomi precedenti.

![Screenshot della selezione tra uno spazio dei nomi basato su dominio e uno spazio dei nomi autonomo nella **Creazione guidata nuovo spazio dei nomi**.](./media/files-manage-namespaces/dfs-namespace-type.png)

Selezionare il tipo di spazio dei nomi desiderato per l'ambiente e selezionare **Avanti.** La procedura guidata riepiloga quindi lo spazio dei nomi da creare. Selezionare **Crea per** creare lo spazio dei nomi e Chiudi **al** termine della finestra di dialogo.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Da una sessione di PowerShell nel server Spazio dei nomi DFS eseguire i comandi di PowerShell seguenti, popolando , e con i valori rilevanti `$namespace` `$type` per `$takeOverName` l'ambiente.

```PowerShell
# Variables
$namespace = "Public"
$type = "DomainV2" # "Standalone"
$takeOverName = $false # $true

$namespace = if ($takeOverName -and $type -eq "Standalone" -and $namespace[0] -ne "#") { 
    "#$namespace" 
} else { $namespace }
$dfsnServer = $env:ComputerName
$namespaceServer = if ($type -eq "DomainV2") { 
    Get-CimInstance -ClassName "Win32_ComputerSystem" | `
    Select-Object -ExpandProperty Domain
} else { $dfsnServer }

# Create share for DFS-N namespace
$smbShare = "C:\DFSRoots\$namespace"
if (!(Test-Path -Path $smbShare)) { New-Item -Path $smbShare -ItemType Directory }
New-SmbShare -Name $namespace -Path $smbShare -FullAccess Everyone

# Create DFS-N namespace
Import-Module -Name DFSN
$namespacePath = "\\$namespaceServer\$namespace"
$targetPath = "\\$dfsnServer\$namespace"
New-DfsnRoot -Path $namespacePath -TargetPath $targetPath -Type $type
```
---

## <a name="configure-folders-and-folder-targets"></a>Configurare cartelle e destinazioni cartella
Per essere utile, uno spazio dei nomi deve avere cartelle e destinazioni cartella. Ogni cartella può avere una o più destinazioni cartella, che sono puntatori alle condivisioni file SMB che ospitano tale contenuto. Quando gli utenti esplorano una cartella con destinazioni cartella, il computer client riceve un riferimento che reindirizza in modo trasparente il computer client a una delle destinazioni cartella. È anche possibile avere cartelle senza destinazioni cartella per aggiungere struttura e gerarchia allo spazio dei nomi.

Le cartelle spazi dei nomi DFS sono analoghe alle condivisioni file. 

# <a name="portal"></a>[Portale](#tab/azure-portal)
Nella console Gestione DFS selezionare lo spazio dei nomi appena creato e selezionare **Nuova cartella.** La finestra **di dialogo Nuova** cartella risultante consentirà di creare sia la cartella che le relative destinazioni.

![Screenshot della finestra di dialogo **Nuova cartella**.](./media/files-manage-namespaces/dfs-folder-targets.png)

Nella casella di testo Name **(Nome)** specificare il nome della cartella. Selezionare **Aggiungi...** per aggiungere le destinazioni cartella per questa cartella. La finestra **di dialogo Aggiungi** destinazione  cartella risultante include una casella di testo con l'etichetta Percorso destinazione cartella in cui è possibile specificare il percorso UNC della cartella desiderata. Selezionare **OK** nella finestra **di dialogo Aggiungi destinazione** cartella. Se si aggiunge un percorso UNC a una condivisione file di Azure, è possibile che venga visualizzato un messaggio che segnala che il server `storageaccount.file.core.windows.net` non può essere contattata. Questo comportamento è previsto. Selezionare **Sì** per continuare. Infine, selezionare **OK nella** finestra di **dialogo Nuova** cartella per creare le destinazioni cartella e cartella.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```PowerShell
# Variables
$shareName = "MyShare"
$targetUNC = "\\storageaccount.file.core.windows.net\myshare"

# Create folder and folder targets
$sharePath = "$namespacePath\$shareName"
New-DfsnFolder -Path $sharePath -TargetPath $targetUNC
```

---

Dopo aver creato uno spazio dei nomi, una cartella e una destinazione cartella, dovrebbe essere possibile montare la condivisione file tramite Spazi dei nomi DFS. Se si usa uno spazio dei nomi basato su dominio, il percorso completo della condivisione deve essere `\\<domain-name>\<namespace>\<share>` . Se si usa uno spazio dei nomi autonomo, il percorso completo della condivisione deve essere `\\<DFS-server>\<namespace>\<share>` . Se si usa uno spazio dei nomi autonomo con consolidamento della radice, è possibile accedere direttamente tramite il nome del server precedente, ad esempio `\\<old-server>\<share>` .

## <a name="see-also"></a>Vedi anche
- Distribuzione di una condivisione file di Azure: [pianificazione di una distribuzione File di Azure e](storage-files-planning.md) Come creare una condivisione [file](storage-how-to-create-file-share.md).
- Configurazione dell'accesso alla condivisione file: [considerazioni sull'autenticazione basata sull'identità](storage-files-active-directory-overview.md) [e rete per l'accesso diretto.](storage-files-networking-overview.md)
- [Spazi dei nomi file system distribuito Windows Server](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview)