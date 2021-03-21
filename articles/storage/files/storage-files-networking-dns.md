---
title: Configurazione dell'invio DNS per File di Azure | Microsoft Docs
description: Informazioni su come configurare l'invio DNS per File di Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 3/19/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 9abe306668a4b20e42e45c498bf85b540dfaaee5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94630193"
---
# <a name="configuring-dns-forwarding-for-azure-files"></a>Configurazione dell'inoltro DNS per File di Azure
File di Azure consente di creare endpoint privati per gli account di archiviazione contenenti le condivisioni file. Sebbene sia utile per molte applicazioni diverse, gli endpoint privati sono particolarmente utili per connettersi alle condivisioni file di Azure dalla rete locale usando una connessione VPN o ExpressRoute tramite il peering privato. 

Per consentire alle connessioni all'account di archiviazione di passare attraverso il tunnel di rete, il nome di dominio completo (FQDN) dell'account di archiviazione deve essere risolto nell'indirizzo IP privato dell'endpoint privato. A tale scopo, è necessario inviare il suffisso dell'endpoint di archiviazione ( `core.windows.net` per le aree del cloud pubblico) al servizio DNS privato di Azure accessibile dall'interno della rete virtuale. Questa guida illustra come configurare e configurare l'invio DNS per la corretta risoluzione nell'indirizzo IP dell'endpoint privato dell'account di archiviazione.

Prima di completare i passaggi descritti in questo articolo, è consigliabile leggere la [pianificazione per una distribuzione file di Azure](storage-files-planning.md) e [file di Azure considerazioni sulla rete](storage-files-networking-overview.md) .

## <a name="overview"></a>Panoramica
File di Azure prevede due tipi principali di endpoint per l'accesso alle condivisioni file di Azure: 
- Endpoint pubblici, che hanno un indirizzo IP pubblico e sono accessibili da qualsiasi parte del mondo.
- Endpoint privati, presenti all'interno di una rete virtuale, che hanno un indirizzo IP privato all'interno dello spazio di indirizzi della rete virtuale.

Gli endpoint pubblici e privati sono disponibili nell'account di archiviazione di Azure. Un account di archiviazione è un costrutto di gestione che rappresenta un pool di archiviazione condiviso in cui è possibile distribuire più condivisioni file oltre ad altre risorse di archiviazione, ad esempio contenitori BLOB o code.

Ogni account di archiviazione ha un nome di dominio completo (FQDN). Per le aree del cloud pubblico, questo FQDN segue il modello in `storageaccount.file.core.windows.net` cui `storageaccount` è il nome dell'account di archiviazione. Quando si effettuano richieste con questo nome, ad esempio montando la condivisione nella workstation usando SMB, il sistema operativo esegue una ricerca DNS per risolvere il nome di dominio completo in un indirizzo IP che può usare per inviare le richieste SMB a.

Per impostazione predefinita, `storageaccount.file.core.windows.net` viene risolto nell'indirizzo IP dell'endpoint pubblico. L'endpoint pubblico per un account di archiviazione è ospitato in un cluster di archiviazione di Azure che ospita molti altri endpoint pubblici degli account di archiviazione. Quando si crea un endpoint privato, una zona DNS privata viene collegata alla rete virtuale a cui è stata aggiunta, con un record CNAME che esegue il mapping `storageaccount.file.core.windows.net` a una voce di record a per l'indirizzo IP privato dell'endpoint privato dell'account di archiviazione. Questo consente di usare il `storageaccount.file.core.windows.net` nome di dominio completo nella rete virtuale e di risolverlo nell'indirizzo IP dell'endpoint privato.

Poiché l'obiettivo finale è l'accesso alle condivisioni file di Azure ospitate nell'account di archiviazione da locale usando un tunnel di rete, ad esempio una connessione VPN o ExpressRoute, è necessario configurare i server DNS locali per l'invio delle richieste effettuate al servizio File di Azure al servizio DNS privato di Azure. A tale scopo, è necessario configurare l' *invio condizionale* di `*.core.windows.net` (o il suffisso dell'endpoint di archiviazione appropriato per il governo degli Stati Uniti, la Germania o i cloud nazionali Cina) a un server DNS ospitato nella rete virtuale di Azure. Questo server DNS inoltrerà in modo ricorsivo la richiesta al servizio DNS privato di Azure che risolverà il nome di dominio completo dell'account di archiviazione nell'indirizzo IP privato appropriato.

Per la configurazione dell'invio DNS per File di Azure sarà necessario eseguire una macchina virtuale per ospitare un server DNS per l'invio delle richieste. Tuttavia, si tratta di un passaggio una volta per tutte le condivisioni file di Azure ospitate nella rete virtuale. Inoltre, questo non è un requisito esclusivo per File di Azure. tutti i servizi di Azure che supportano gli endpoint privati a cui si vuole accedere dall'ambiente locale possono usare l'invio DNS che verrà configurato in questa guida: archiviazione BLOB di Azure, SQL Azure, Cosmos DB e così via. 

Questa guida illustra i passaggi per la configurazione dell'invio DNS per l'endpoint di archiviazione di Azure, quindi, oltre a File di Azure, le richieste di risoluzione dei nomi DNS per tutti gli altri servizi di archiviazione di Azure (archiviazione BLOB di Azure, archiviazione tabelle di Azure, archiviazione code di Azure e così via) verranno inviate al servizio DNS privato di Azure. Se lo si desidera, è anche possibile aggiungere altri endpoint per altri servizi di Azure. Verrà configurato anche l'invio DNS ai server DNS locali, abilitando le risorse cloud all'interno della rete virtuale, ad esempio un server DFS-N, per risolvere i nomi dei computer locali. 

## <a name="prerequisites"></a>Prerequisiti
Prima di poter configurare l'invio DNS a File di Azure, è necessario avere completato i passaggi seguenti:

- Un account di archiviazione contenente una condivisione file di Azure che si desidera montare. Per informazioni su come creare un account di archiviazione e una condivisione file di Azure, vedere [creare una condivisione file di Azure](storage-how-to-create-file-share.md).
- Un endpoint privato per l'account di archiviazione. Per informazioni su come creare un endpoint privato per File di Azure, vedere [creare un endpoint privato](storage-files-networking-endpoints.md#create-a-private-endpoint).
- [Versione più recente](/powershell/azure/install-az-ps) del modulo Azure PowerShell.

> [!Important]  
> Questa guida presuppone che si stia usando il server DNS all'interno di Windows Server nell'ambiente locale. Tutti i passaggi descritti in questa guida sono possibili con qualsiasi server DNS, non solo con il server DNS di Windows.

## <a name="manually-configuring-dns-forwarding"></a>Configurazione manuale dell'invio DNS
Se sono già presenti server DNS nella rete virtuale di Azure o se si preferisce semplicemente distribuire le proprie macchine virtuali in modo che siano server DNS con qualsiasi metodologia usata dall'organizzazione, è possibile configurare il DNS manualmente con i cmdlet di PowerShell predefiniti del server DNS.

Nei server DNS locali, creare un server d'istruzione condizionale usando `Add-DnsServerConditionalForwarderZone` . Questo server d'invio condizionale deve essere distribuito in tutti i server DNS locali per essere efficace quando il traffico viene correttamente inviato ad Azure. Ricordarsi di sostituire `<azure-dns-server-ip>` con gli indirizzi IP appropriati per l'ambiente in uso.

```powershell
$vnetDnsServers = "<azure-dns-server-ip>", "<azure-dns-server-ip>"

$storageAccountEndpoint = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty StorageEndpointSuffix

Add-DnsServerConditionalForwarderZone `
        -Name $storageAccountEndpoint `
        -MasterServers $vnetDnsServers
```

Nei server DNS all'interno della rete virtuale di Azure, è necessario anche inserire un server di riferimento in modo che le richieste per la zona DNS dell'account di archiviazione vengano indirizzate al servizio DNS privato di Azure, che è stato sottoposto all'indirizzo IP riservato `168.63.129.16` . (Ricordare di popolare `$storageAccountEndpoint` se si eseguono i comandi all'interno di una sessione di PowerShell diversa).

```powershell
Add-DnsServerConditionalForwarderZone `
        -Name $storageAccountEndpoint `
        -MasterServers "168.63.129.16"
```

## <a name="using-the-azure-files-hybrid-module-to-configure-dns-forwarding"></a>Uso del modulo ibrido File di Azure per configurare l'invio DNS
Per semplificare il più possibile la configurazione dell'invio DNS, l'automazione è disponibile nel modulo File di Azure Hybrid. I cmdlet forniti per la manipolazione del DNS in questo modulo consentiranno di distribuire i server DNS nella rete virtuale di Azure e di aggiornare i server DNS locali in modo che li inoltrino. 

Se non si è mai usato il modulo ibrido File di Azure, è necessario prima installarlo nella workstation. Scaricare la [versione più recente](https://github.com/Azure-Samples/azure-files-samples/releases) del modulo di PowerShell file di Azure ibrido:

```PowerShell
# Unzip the downloaded file
Expand-Archive -Path AzFilesHybrid.zip

# Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\AzFilesHybrid\CopyToPSPath.ps1 

# Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid
```

La distribuzione della soluzione di invio DNS prevede due passaggi, ovvero la creazione di un set di regole di invio DNS, che definisce i servizi di Azure a cui si vogliono inviare le richieste e la distribuzione effettiva dei server d'inoltri DNS. 

L'esempio seguente invia le richieste all'account di archiviazione, le richieste inclusive per File di Azure, archiviazione BLOB di Azure, archiviazione tabelle di Azure e archiviazione code di Azure. Se lo si desidera, è possibile aggiungere un ulteriore servizio di Azure alla regola tramite il `-AzureEndpoints` parametro del `New-AzDnsForwardingRuleSet` cmdlet. Ricordarsi di sostituire `<virtual-network-resource-group>`, `<virtual-network-name>` e `<subnet-name>` con i valori appropriati per l'ambiente.

```PowerShell
# Create a rule set, which defines the forwarding rules
$ruleSet = New-AzDnsForwardingRuleSet -AzureEndpoints StorageAccountEndpoint

# Deploy and configure DNS forwarders
New-AzDnsForwarder `
        -DnsForwardingRuleSet $ruleSet `
        -VirtualNetworkResourceGroupName "<virtual-network-resource-group>" `
        -VirtualNetworkName "<virtual-network-name>" `
        -VirtualNetworkSubnetName "<subnet-name>"
```

Potrebbe inoltre risultare utile o necessario fornire diversi parametri aggiuntivi:

| Nome parametro | Tipo | Descrizione |
|----------------|------|-------------|
| `DnsServerResourceGroupName` | `string` | Per impostazione predefinita, i server DNS verranno distribuiti nello stesso gruppo di risorse della rete virtuale. Se questa operazione non è necessaria, questo parametro consente di selezionare un gruppo di risorse alternativo in cui distribuirli. |
| `DnsForwarderRootName` | `string` | Per impostazione predefinita, i server DNS distribuiti in Azure hanno i nomi `DnsFwder-*` , in cui l'asterisco viene popolato da un iteratore. Questo parametro modifica la radice del nome, ad `DnsFwder` esempio. |
| `VmTemporaryPassword` | `SecureString` | Per impostazione predefinita, viene scelta una password casuale per l'account predefinito temporaneo di una macchina virtuale prima che sia aggiunto a un dominio. Dopo che è stato aggiunto a un dominio, l'account predefinito è disabilitato. |
| `DomainToJoin` | `string` | Dominio a cui aggiungere le VM DNS. Per impostazione predefinita, questo dominio viene scelto in base al dominio del computer in cui vengono eseguiti i cmdlet. |
| `DnsForwarderRedundancyCount` | `int` | Il numero di VM DNS da distribuire per la rete virtuale. Per impostazione predefinita, `New-AzDnsForwarder` distribuisce due server DNS nella rete virtuale di Azure, in un set di disponibilità, per garantire la ridondanza. Questo numero può essere modificato nel modo desiderato. |
| `OnPremDnsHostNames` | `HashSet<string>` | Elenco specificato manualmente di nomi host DNS locali su cui creare i server d'inoltri. Questo parametro è utile quando non si desidera applicare i server d'esecuzione in tutti i server DNS locali, ad esempio quando si dispone di un intervallo di client con nomi DNS specificati manualmente. |
| `Credential` | `PSCredential` | Credenziale da usare quando si aggiornano i server DNS. Questa operazione è utile quando l'account utente con cui si è eseguito l'accesso non dispone delle autorizzazioni necessarie per modificare le impostazioni DNS. |
| `SkipParentDomain` | `SwitchParameter` | Per impostazione predefinita, i server d'inoltri DNS vengono applicati al dominio di livello più alto esistente nell'ambiente in uso. Se, ad esempio, `northamerica.corp.contoso.com` è un dominio figlio di `corp.contoso.com` , il server d'invio verrà creato per i server DNS associati a `corp.contoso.com` . Questo parametro provocherà la creazione di server di trasmissione in `northamerica.corp.contoso.com` . |

## <a name="confirm-dns-forwarders"></a>Conferma server d'inoltri DNS
Prima di eseguire il test per verificare se i server d'invio DNS sono stati applicati correttamente, è consigliabile cancellare la cache DNS nella workstation locale usando `Clear-DnsClientCache` . Per verificare se è possibile risolvere correttamente il nome di dominio completo dell'account di archiviazione, usare `Resolve-DnsName` o `nslookup` .

```powershell
# Replace storageaccount.file.core.windows.net with the appropriate FQDN for your storage account.
# Note the proper suffix (core.windows.net) depends on the cloud your deployed in.
Resolve-DnsName -Name storageaccount.file.core.windows.net
```

Se la risoluzione dei nomi ha esito positivo, verrà visualizzato l'indirizzo IP risolto corrispondente all'indirizzo IP dell'account di archiviazione.

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  29    Answer     csostoracct.privatelink.file.core.windows.net
net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 1769
Section    : Answer
IP4Address : 192.168.0.4
```

Se è già stata configurata una connessione VPN o ExpressRoute, è possibile usare anche `Test-NetConnection` per verificare che una connessione TCP possa essere eseguita correttamente nell'account di archiviazione.

```PowerShell
Test-NetConnection -ComputerName storageaccount.file.core.windows.net -CommonTCPPort SMB
```

## <a name="see-also"></a>Vedi anche
- [Pianificazione per la distribuzione di File di Azure](storage-files-planning.md)
- [Considerazioni sulla rete per File di Azure](storage-files-networking-overview.md)
- [Configurazione degli endpoint di rete di File di Azure](storage-files-networking-endpoints.md)