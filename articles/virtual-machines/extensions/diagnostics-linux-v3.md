---
title: Calcolo di Azure - Estensione diagnostica Linux 3.0
description: Come configurare l'estensione di diagnostica Linux di Azure (LAD) 3.0 per raccogliere metriche e registrare eventi da macchine virtuali Linux in esecuzione in Azure.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 12/13/2018
ms.openlocfilehash: fe03bbfb33f3637eecc4e68f24846c929dad5fa4
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479254"
---
# <a name="use-linux-diagnostic-extension-30-to-monitor-metrics-and-logs"></a>Usare l'estensione di diagnostica Linux 3.0 per monitorare metriche e log

Questo documento descrive la versione 3.0 e successiva dell'estensione di diagnostica Linux (LAD).

> [!IMPORTANT]
> Per informazioni sulla versione 2.3 e precedenti, vedere Monitorare le prestazioni e i dati [di diagnostica di una macchina virtuale Linux.](https://docs.microsoft.com/previous-versions/azure/virtual-machines/linux/classic/diagnostic-extension-v2)

## <a name="introduction"></a>Introduzione

L'estensione di diagnostica Linux consente a un utente di monitorare l'integrità di una macchina virtuale Linux in esecuzione Microsoft Azure. Questo servizio offre le funzionalità seguenti:

* Raccoglie le metriche delle prestazioni di sistema dalla macchina virtuale e le inserisce in una tabella specifica in un account di archiviazione designato.
* Recupera gli eventi di registrazione da SysLog e li inserisce in una tabella specifica nell'account di archiviazione designato.
* Consente agli utenti di personalizzare le metriche dei dati che verranno raccolte e caricate.
* Consente agli utenti di personalizzare i servizi di SysLog e i livelli di gravità degli eventi che vengono raccolti e caricati.
* Consente agli utenti di caricare in una tabella di archiviazione designata i file di log specificati.
* Supporta l'invio di metriche ed eventi di log a endpoint Hub eventi di Azure e BLOB in formato JSON nell'account di archiviazione designato.

Questa estensione funziona con entrambi i modelli di distribuzione di Azure.

## <a name="install-the-extension-on-a-vm"></a>Installare l'estensione in una macchina virtuale

È possibile abilitare l'estensione usando Azure PowerShell cmdlet, script dell'interfaccia della riga di comando di Azure, modelli di Azure Monitoraggio risorse (modelli arm) o portale di Azure. Per altre informazioni, vedere [Funzionalità delle estensioni](features-linux.md).

>[!NOTE]
>Alcuni componenti dell'estensione VM LAD vengono forniti anche nell'estensione vm [di Log Analytics](./oms-linux.md). A causa di questa architettura, possono verificarsi conflitti se viene creata un'istanza di entrambe le estensioni nello stesso modello arm. 
>
>Per evitare conflitti in fase di installazione, usare [ `dependsOn` la direttiva per](../../azure-resource-manager/templates/define-resource-dependency.md#dependson) assicurarsi che le estensioni siano installate in sequenza. Le estensioni possono essere installate in entrambi gli ordini.

Queste istruzioni di installazione e una [configurazione di esempio scaricabile](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json) per configurare LAD 3.0 per:

* Acquisire e archiviare le stesse metriche di LAD 2.3.
* Acquisire un set utile di file system metriche. Questa funzionalità è una novità di LAD 3.0.
* Acquisire la raccolta syslog predefinita abilitata da LAD 2.3.
* Abilitare l'portale di Azure per la creazione di grafici e la creazione di avvisi sulle metriche delle macchine virtuali.

La configurazione scaricabile è solo un esempio. Modificarlo in base alle proprie esigenze.

### <a name="supported-linux-distributions"></a>Distribuzioni Linux supportate

LAD supporta le distribuzioni e le versioni seguenti. L'elenco di distribuzioni e versioni si applica solo alle immagini del fornitore Linux approvate da Azure. L'estensione in genere non supporta immagini BYOL e BYOS di terze parti, ad esempio appliance.

Una distribuzione che elenca solo le versioni principali, come Debian 7, è supportata anche per tutte le versioni secondarie. Se viene specificata una versione secondaria, è supportata solo tale versione. Se viene aggiunto un segno più (+), sono supportate le versioni secondarie uguali o successive alla versione specificata.

Distribuzioni e versioni supportate:

- Ubuntu 18.04, 16.04, 14.04
- CentOS 7, 6.5+
- Oracle Linux 7, 6.4+
- OpenSUSE 13.1+
- SUSE Linux Enterprise Server 12
- Debian 9, 8, 7
- Red Hat Enterprise Linux (RHEL) 7, 6.7+

### <a name="prerequisites"></a>Prerequisiti

* **Agente Linux di Azure 2.2.0 o versione successiva**. La maggior parte delle immagini della raccolta Linux di macchine virtuali di Azure include la versione 2.2.7 o successive. Eseguire `/usr/sbin/waagent -version` per verificare la versione installata nella macchina virtuale. Se la macchina virtuale esegue una versione precedente, [aggiornare l'agente guest](./update-linux-agent.md).
* **Interfaccia della riga di comando di Azure**. Se necessario, [configurare l'ambiente dell'interfaccia della](/cli/azure/install-azure-cli) riga di comando di Azure nel computer.
* Comando **wget**. Se non è già presente, eseguire `sudo apt-get install wget` .
* Sottoscrizione **di Azure esistente.** 
* Un account **di archiviazione per utilizzo generico esistente** per archiviare i dati. Gli account di archiviazione per utilizzo generico devono supportare l'archiviazione tabelle. Un account di archiviazione BLOB non funzionerà.
* **Python 2**.

### <a name="python-requirement"></a>Requisito di Python

L'estensione di diagnostica Linux richiede Python 2. Se la macchina virtuale usa una distribuzione che non include Python 2 per impostazione predefinita, è necessario installarla. I comandi di esempio seguenti installano Python 2 in diverse distribuzioni:   

- Red Hat, CentOS, Oracle: `yum install -y python2`
- Ubuntu, Debian: `apt-get install -y python2`
- SUSE: `zypper install -y python2`

Il `python2` file eseguibile deve essere con alias per *Python.* Ecco un metodo per impostare questo alias:

1. Eseguire il comando seguente per rimuovere eventuali alias esistenti.
 
    ```
    sudo update-alternatives --remove-all python
    ```

2. Eseguire il comando seguente per creare l'alias.

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
    ```

### <a name="sample-installation"></a>Installazione di esempio

La configurazione di esempio scaricata negli esempi seguenti raccoglie un set di dati standard e li invia all'archiviazione tabelle. L'URL per la configurazione di esempio e il relativo contenuto possono cambiare. 

Nella maggior parte dei casi, è consigliabile scaricare una copia del file JSON delle impostazioni del portale e personalizzarla in base alle esigenze. Usare quindi i modelli o l'automazione personalizzata per usare una versione personalizzata del file di configurazione invece di eseguire ogni volta il download dall'URL.

> [!NOTE]
> Per gli esempi seguenti, inserire i valori corretti per le variabili nella prima sezione prima di eseguire il codice. 
#### <a name="azure-cli-sample"></a>Esempio di interfaccia della riga di comando di Azure

```azurecli
# Set your Azure VM diagnostic variables.
my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
my_linux_vm=<your_azure_linux_vm_name>
my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Login to Azure before you do anything else.
az login

# Select the subscription that contains the storage account.
az account set --subscription <your_azure_subscription_id>

# Download the sample public settings. (You could also use curl or any web browser.)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the VM resource ID. Replace the storage account name and resource ID in the public settings.
my_vm_resource_id=$(az vm show -g $my_resource_group -n $my_linux_vm --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vm_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token).
my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finally, tell Azure to install and enable the extension.
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vm-name $my_linux_vm --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```
#### <a name="azure-cli-sample-to-install-lad-30-on-the-virtual-machine-scale-set-instance"></a>Esempio di interfaccia della riga di comando di Azure per installare LAD 3.0 nell'istanza del set di scalabilità di macchine virtuali

```azurecli
#Set your Azure Virtual Machine Scale Sets diagnostic variables.
$my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
$my_linux_vmss=<your_azure_linux_vmss_name>
$my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Login to Azure before you do anything else.
az login

# Select the subscription that contains the storage account.
az account set --subscription <your_azure_subscription_id>

# Download the sample public settings. (You could also use curl or any web browser.)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the virtual machine scale set resource ID. Replace the storage account name and resource ID in the public settings.
$my_vmss_resource_id=$(az vmss show -g $my_resource_group -n $my_linux_vmss --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vmss_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token).
$my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
$my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finally, tell Azure to install and enable the extension.
az vmss extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vmss-name $my_linux_vmss --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```

#### <a name="powershell-sample"></a>Esempio PowerShell

```powershell
$storageAccountName = "yourStorageAccountName"
$storageAccountResourceGroup = "yourStorageAccountResourceGroupName"
$vmName = "yourVMName"
$VMresourceGroup = "yourVMResourceGroupName"

# Get the VM object
$vm = Get-AzVM -Name $vmName -ResourceGroupName $VMresourceGroup

# Get the public settings template from GitHub and update the templated values for storage account and resource ID
$publicSettings = (Invoke-WebRequest -Uri https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json).Content
$publicSettings = $publicSettings.Replace('__DIAGNOSTIC_STORAGE_ACCOUNT__', $storageAccountName)
$publicSettings = $publicSettings.Replace('__VM_RESOURCE_ID__', $vm.Id)

# If you have customized public settings, you can inline those rather than using the preceding template: $publicSettings = '{"ladCfg":  { ... },}'

# Generate a SAS token for the agent to use to authenticate with the storage account
$sasToken = New-AzStorageAccountSASToken -Service Blob,Table -ResourceType Service,Container,Object -Permission "racwdlup" -Context (Get-AzStorageAccount -ResourceGroupName $storageAccountResourceGroup -AccountName $storageAccountName).Context -ExpiryTime $([System.DateTime]::Now.AddYears(10))

# Build the protected settings (storage account SAS token)
$protectedSettings="{'storageAccountName': '$storageAccountName', 'storageAccountSasToken': '$sasToken'}"

# Finally, install the extension with the settings you built
Set-AzVMExtension -ResourceGroupName $VMresourceGroup -VMName $vmName -Location $vm.Location -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 3.0 
```

### <a name="update-the-extension-settings"></a>Aggiornare le impostazioni dell'estensione

Dopo aver modificato le impostazioni protette o pubbliche, distribuirle nella macchina virtuale eseguendo lo stesso comando. Se le impostazioni sono state modificate, gli aggiornamenti vengono inviati all'estensione. LAD ricarica la configurazione e si riavvia.

### <a name="migrate-from-previous-versions-of-the-extension"></a>Eseguire la migrazione da versioni precedenti dell'estensione

La versione più recente dell'estensione *è 4.0.* 

> [!IMPORTANT]
> Questa estensione introduce modifiche di rilievo alla configurazione. Una di queste modifiche ha migliorato la sicurezza dell'estensione, quindi non è stato possibile mantenere la compatibilità con la versione 2.x. Inoltre, l'editore dell'estensione per questa estensione è diverso dal server di pubblicazione per le versioni 2.x.
>
> Per eseguire la migrazione dalla versione 2.x alla nuova versione, disinstallare prima l'estensione precedente (con il nome del server di pubblicazione precedente). Installare quindi la versione 3.

Consigli:

* Installare l'estensione abilitando l'aggiornamento automatico della versione secondaria.
  * Nelle macchine virtuali del modello di distribuzione classica specificare la versione se si installa l'estensione tramite l'interfaccia della riga di comando `3.*` di Azure XPLAT o PowerShell.
  * Nelle Azure Resource Manager del modello di distribuzione, includere `"autoUpgradeMinorVersion": true` nel modello di distribuzione della macchina virtuale.
* Usare un account di archiviazione nuovo o diverso per LAD 3.0. LAD 2.3 e LAD 3.0 hanno diverse piccole incompatibilità che rendono problematica la condivisione di un account:
  * LAD 3.0 archivia gli eventi syslog in una tabella con un nome diverso.
  * Le `counterSpecifier` stringhe per le metriche `builtin` differiscono in LAD 3.0.

## <a name="protected-settings"></a>Impostazioni protette

Questo set di informazioni di configurazione contiene informazioni riservate che devono essere protette dalla visualizzazione pubblica. Contiene, ad esempio, le credenziali di archiviazione. Queste impostazioni vengono trasmesse e memorizzate dall'estensione in modo crittografato.

```json
{
    "storageAccountName" : "the storage account to receive data",
    "storageAccountEndPoint": "the hostname suffix for the cloud for this account",
    "storageAccountSasToken": "SAS access token",
    "mdsdHttpProxy": "HTTP proxy settings",
    "sinksConfig": { ... }
}
```

Nome | valore
---- | -----
storageAccountName | Nome dell'account di archiviazione in cui l'estensione scrive i dati.
storageAccountEndPoint | (Facoltativo) Endpoint che identifica il cloud in cui si trova l'account di archiviazione. Se questa impostazione è assente, il valore predefinito di LAD è il cloud pubblico di Azure, `https://core.windows.net` . Per usare un account di archiviazione Azure Germania, Azure per enti pubblici o Azure China (21Vianet), impostare questo valore come richiesto.
storageAccountSasToken | Token [di firma di accesso condiviso dell'account](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/) per servizi BLOB e tabelle ( `ss='bt'` ). Si applica a contenitori e oggetti ( `srt='co'` ). Concede autorizzazioni di aggiunta, creazione, elenco, aggiornamento e scrittura ( `sp='acluw'` ). *Non* includere il punto interrogativo (?) principale.
mdsdHttpProxy | (Facoltativo) Informazioni proxy HTTP necessarie per la connessione dell'estensione all'account di archiviazione e all'endpoint specificati.
sinksConfig | (Facoltativo) Dettagli delle destinazioni alternative a cui è possibile recapitare metriche ed eventi. Nelle sezioni seguenti vengono fornite informazioni dettagliate su ogni sink di dati supportato dall'estensione.

Per ottenere un token di firma di accesso condiviso all'interno di un modello di Arm, usare la `listAccountSas` funzione . Per un modello di esempio, vedere l'[esempio di funzione list](../../azure-resource-manager/templates/template-functions-resource.md#list-example).

È possibile costruire il token di firma di accesso condiviso richiesto tramite il portale di Azure:

1. Selezionare l'account di archiviazione per utilizzo generico in cui si vuole che l'estensione scrivo.
1. Nel menu a sinistra, in **Impostazioni** selezionare **Firma di accesso condiviso.**
1. Effettuare le selezioni come descritto in precedenza.
1. Selezionare **Generate SAS (Genera firma di accesso condiviso).**

:::image type="content" source="./media/diagnostics-linux/make_sas.png" alt-text="Screenshot che mostra la pagina Firma di accesso condiviso con il pulsante Genera firma di accesso condiviso.":::

Copiare la firma di accesso condiviso generata nel `storageAccountSasToken` campo . Rimuovere il punto interrogativo iniziale (?).

### <a name="sinksconfig"></a>sinksConfig

```json
"sinksConfig": {
    "sink": [
        {
            "name": "sinkname",
            "type": "sinktype",
            ...
        },
        ...
    ]
},
```

La `sinksConfig` sezione facoltativa definisce più destinazioni a cui l'estensione invia le informazioni raccolte. La `sink` matrice contiene un oggetto per ogni sink di dati aggiuntivo. `type`L'attributo determina gli altri attributi nell'oggetto .

Elemento | valore
------- | -----
name | Stringa che fa riferimento a questo sink altrove nella configurazione dell'estensione.
type | Il tipo di sink da definire. Determina gli altri valori, se presenti, nelle istanze di questo tipo.

LAD versione 3.0 supporta due tipi di sink: `EventHub` e `JsonBlob` .

#### <a name="eventhub-sink"></a>Sink EventHub

```json
"sink": [
    {
        "name": "sinkname",
        "type": "EventHub",
        "sasURL": "https SAS URL"
    },
    ...
]
```

La `"sasURL"` voce contiene l'URL completo, incluso il token di firma di accesso condiviso, per l'hub eventi in cui devono essere pubblicati i dati. LAD richiede una firma di accesso condiviso per assegnare un nome a un criterio che abilita l'attestazione di invio. 

Ad esempio:

* Creare uno spazio dei nomi Hub eventi di Azure denominato `contosohub` .
* Creare un hub eventi nello spazio dei nomi denominato `syslogmsgs` .
* Creare un criterio di accesso condiviso nell'hub eventi che abilita l'attestazione di invio. Assegnare al criterio il nome `writer` .

Se la firma di accesso condiviso è buona fino alla mezzanotte UTC del 1° gennaio 2018, il valore sasURL potrebbe essere simile all'esempio seguente:

```https
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

Per altre informazioni sulla generazione e il recupero di informazioni sui token di firma di accesso condiviso per Hub eventi, vedere [Generare un token di firma di accesso condiviso](/rest/api/eventhub/generate-sas-token#powershell).

#### <a name="jsonblob-sink"></a>Sink JsonBlob

```json
"sink": [
    {
        "name": "sinkname",
        "type": "JsonBlob"
    },
    ...
]
```

I dati indirizzati a `JsonBlob` un sink vengono archiviati in BLOB in Archiviazione di Azure. Ogni istanza di LAD crea un BLOB all'ora per ogni nome di sink. Ogni BLOB contiene sempre una matrice di oggetti JSON sintatticamente valida. Le nuove voci vengono aggiunte in modo atomico alla matrice. 

I BLOB vengono archiviati in un contenitore con lo stesso nome del sink. Le Archiviazione di Azure per i nomi dei contenitori BLOB si applicano ai nomi dei `JsonBlob` sink. Il nome deve contenere da 3 a 63 caratteri ASCII minuscoli o trattini.

## <a name="public-settings"></a>Impostazioni pubbliche

La struttura delle impostazioni pubbliche contiene vari blocchi di impostazioni che controllano le informazioni raccolte dall'estensione. Tutte le impostazioni sono facoltative. Se si specifica `ladCfg`, è necessario specificare anche `StorageAccount`.

```json
{
    "ladCfg":  { ... },
    "perfCfg": { ... },
    "fileLogs": { ... },
    "StorageAccount": "the storage account to receive data",
    "mdsdHttpProxy" : ""
}
```

Elemento | valore
------- | -----
StorageAccount | Nome dell'account di archiviazione in cui l'estensione scrive i dati. Deve essere il nome specificato nelle [impostazioni protette.](#protected-settings)
mdsdHttpProxy | (Facoltativo) Come nelle impostazioni [protette](#protected-settings). Il valore pubblico viene sottoposto a override dal valore privato, se impostato. Inserire le impostazioni proxy che contengono un segreto, ad esempio una password, nelle [impostazioni protette](#protected-settings).

Le sezioni seguenti forniscono informazioni dettagliate per gli elementi rimanenti.

### <a name="ladcfg"></a>ladCfg

```json
"ladCfg": {
    "diagnosticMonitorConfiguration": {
        "eventVolume": "Medium",
        "metrics": { ... },
        "performanceCounters": { ... },
        "syslogEvents": { ... }
    },
    "sampleRateInSeconds": 15
}
```

La `ladCfg` struttura è facoltativa. Controlla la raccolta di metriche e log che vengono recapitati al Monitoraggio di Azure Metrics e ad altri sink di dati. È necessario specificare:

* O `performanceCounters` `syslogEvents` entrambi. 
* Struttura `metrics` .

Elemento | valore
------- | -----
eventVolume | (Facoltativo) Controlla il numero di partizioni create all'interno della tabella di archiviazione. Deve essere `"Large"` `"Medium"` , o `"Small"` . Se non viene specificato un valore, il valore predefinito è `"Medium"` .
sampleRateInSeconds | (Facoltativo) Intervallo predefinito tra la raccolta di metriche non elaborate (non aggregate). La frequenza di esempio più piccola supportata è 15 secondi. Se il valore non viene specificato, il valore predefinito è `15` .

#### <a name="metrics"></a>Metriche

```json
"metrics": {
    "resourceId": "/subscriptions/...",
    "metricAggregation" : [
        { "scheduledTransferPeriod" : "PT1H" },
        { "scheduledTransferPeriod" : "PT5M" }
    ]
}
```

Elemento | valore
------- | -----
resourceId | ID Azure Resource Manager risorsa della macchina virtuale o del set di scalabilità a cui appartiene la macchina virtuale. Questa impostazione deve essere specificata anche se nella configurazione `JsonBlob` viene usato un sink.
scheduledTransferPeriod | Frequenza con cui le metriche di aggregazione vengono calcolate e trasferite Monitoraggio di Azure metriche. La frequenza è espressa come intervallo di tempo IS 8601. Il periodo di trasferimento più piccolo è 60 secondi, ovvero PT1M. Specificare almeno un `scheduledTransferPeriod` .

I campioni delle metriche specificate nella sezione vengono raccolti ogni 15 secondi o alla frequenza di campionamento definita in modo `performanceCounters` esplicito per il contatore. Se vengono `scheduledTransferPeriod` visualizzate più frequenze, come nell'esempio, ogni aggregazione viene calcolata in modo indipendente.

#### <a name="performancecounters"></a>performanceCounters

```json
"performanceCounters": {
    "sinks": "",
    "performanceCounterConfiguration": [
        {
            "type": "builtin",
            "class": "Processor",
            "counter": "PercentIdleTime",
            "counterSpecifier": "/builtin/Processor/PercentIdleTime",
            "condition": "IsAggregate=TRUE",
            "sampleRate": "PT15S",
            "unit": "Percent",
            "annotation": [
                {
                    "displayName" : "Aggregate CPU %idle time",
                    "locale" : "en-us"
                }
            ]
        }
    ]
}
```

La `performanceCounters` sezione facoltativa controlla la raccolta di metriche. Gli esempi non elaborati vengono aggregati per ognuno [`scheduledTransferPeriod`](#metrics) di essi per produrre questi valori:

* Media
* Minima
* Massimo
* Ultimo valore raccolto
* Numero di campioni non elaborati usati per calcolare l'aggregazione

Elemento | valore
------- | -----
sinks | (Facoltativo) Elenco delimitato da virgole di nomi di sink a cui LAD invia i risultati aggregati delle metriche. Tutte le metriche aggregate vengono pubblicate in ogni sink elencato. Esempio: `"EHsink1, myjsonsink"`. Per altre informazioni, vedere [`sinksConfig`](#sinksconfig).
type | Identifica il provider effettivo della metrica.
class | Insieme a `"counter"` , identifica la metrica specifica all'interno dello spazio dei nomi del provider.
counter | Insieme a `"class"` , identifica la metrica specifica all'interno dello spazio dei nomi del provider.
counterSpecifier | Identifica la metrica specifica nello spazio dei nomi Monitoraggio di Azure Metrics.
condizione | (Facoltativo) Seleziona un'istanza specifica dell'oggetto a cui si applica la metrica. Oppure seleziona l'aggregazione in tutte le istanze dell'oggetto. 
sampleRate | Intervallo IS 8601 che imposta la frequenza con cui vengono raccolti i campioni non elaborati per questa metrica. Se il valore non è impostato, l'intervallo di raccolta viene impostato dal valore di [`sampleRateInSeconds`](#ladcfg) . La frequenza di esempio più piccola supportata è 15 secondi (PT15S).
unit | Definisce l'unità per la metrica. Deve essere una di queste stringhe: `"Count"` , , , , , , `"Bytes"` `"Seconds"` `"Percent"` `"CountPerSecond"` `"BytesPerSecond"` `"Millisecond"` . Gli utenti dei dati raccolti prevedono che i valori dei dati raccolti corrispondano a questa unità. LAD ignora questo campo.
displayName | Etichetta da associare ai dati in Monitoraggio di Azure metriche. Questa etichetta si trova nella lingua specificata dalle impostazioni locali associate. LAD ignora questo campo.

è `counterSpecifier` un identificatore arbitrario. I consumer di metriche, ad esempio portale di Azure di creazione di grafici e avvisi, usano come "chiave" che identifica una metrica o un'istanza `counterSpecifier` di una metrica. 

Per `builtin` le metriche, è `counterSpecifier` consigliabile usare valori che iniziano con `/builtin/` . Se si sta raccogliendo un'istanza specifica di una metrica, è consigliabile associare l'identificatore dell'istanza al `counterSpecifier` valore . 

Ecco alcuni esempi:

* `/builtin/Processor/PercentIdleTime` - Tempo di inattività medio calcolato per tutte le CPU virtuali
* `/builtin/Disk/FreeSpace(/mnt)` - Spazio disponibile per il `/mnt` file system
* `/builtin/Disk/FreeSpace` - Spazio disponibile medio in tutti i file system montati

LAD e il portale di Azure non prevedono che il `counterSpecifier` valore corrisponda ad alcun modello. Essere coerenti nel modo in cui si costruiscono `counterSpecifier` i valori.

Quando si specifica `performanceCounters` , il LAD scrive sempre i dati in una tabella in Archiviazione di Azure. Gli stessi dati possono essere scritti in BLOB JSON o in Hub eventi o in entrambi. Non è tuttavia possibile disabilitare l'archiviazione dei dati in una tabella. 

Tutte le istanze di LAD che usano lo stesso nome dell'account di archiviazione e lo stesso endpoint aggiungono le metriche e i log alla stessa tabella. Se troppe macchine virtuali scrivono nella stessa partizione di tabella, Azure può limitazione delle operazioni di scrittura in tale partizione. 

L'impostazione determina la diffusione delle voci `eventVolume` tra 1 (piccola), 10 (media) o 100 partizioni (grandi). In genere, le partizioni medie sono sufficienti per evitare la limitazione del traffico. 

La Monitoraggio di Azure delle metriche del portale di Azure usa i dati in questa tabella per produrre grafici o attivare avvisi. Il nome della tabella è la concatenazione delle stringhe seguenti:

* `WADMetrics`
* Oggetto `"scheduledTransferPeriod"` per i valori aggregati archiviati nella tabella
* `P10DV2S`
* Una data nel formato "AAAAMMGG", che cambia ogni 10 giorni

Gli esempi includono `WADMetricsPT1HP10DV2S20170410` e `WADMetricsPT1MP10DV2S20170609`.

#### <a name="syslogevents"></a>syslogEvents

```json
"syslogEvents": {
    "sinks": "",
    "syslogEventConfiguration": {
        "facilityName1": "minSeverity",
        "facilityName2": "minSeverity",
        ...
    }
}
```

La `syslogEvents` sezione facoltativa controlla la raccolta di eventi di log da syslog. Se la sezione viene omessa, gli eventi syslog non vengono acquisiti.

La `syslogEventConfiguration` raccolta include una voce per ogni struttura syslog di interesse. Se è per una particolare struttura o se tale struttura non viene visualizzata nell'elemento, non viene acquisito alcun evento `minSeverity` `"NONE"` da tale struttura.

Elemento | valore
------- | -----
sinks | Un elenco delimitato da virgole di nomi di sink in cui vengono pubblicati i singoli eventi del registro. Tutti gli eventi del log che corrispondono alle restrizioni in `syslogEventConfiguration` vengono pubblicati in ogni sink elencato. Esempio: `"EHforsyslog"`
facilityName | Nome della struttura syslog, ad esempio `"LOG_USER"` o `"LOG\LOCAL0"` . Per altre informazioni, vedere la sezione "Facility" della pagina [dell'utente syslog](http://man7.org/linux/man-pages/man3/syslog.3.html).
minSeverity | Livello di gravità syslog, ad esempio `"LOG_ERR"` o `"LOG_INFO"` . Per altre informazioni, vedere la sezione "Level" (Livello) della [pagina syslog man](http://man7.org/linux/man-pages/man3/syslog.3.html). L'estensione acquisisce gli eventi inviati all'impianto con livello superiore o uguale a quello specificato.

Quando si specifica `syslogEvents` , LAD scrive sempre i dati in una tabella in Archiviazione di Azure. Gli stessi dati possono essere scritti in BLOB JSON, hub eventi o entrambi. Ma non è possibile disabilitare l'archiviazione dei dati in una tabella. 

Il comportamento di partizionamento per la tabella è identico a quello descritto per `performanceCounters` . Il nome della tabella è la concatenazione delle stringhe seguenti:

* `LinuxSyslog`
* Una data nel formato "AAAAMMGG", che cambia ogni 10 giorni

Gli esempi includono `LinuxSyslog20170410` e `LinuxSyslog20170609`.

### <a name="perfcfg"></a>perfCfg

La sezione `perfCfg` è facoltativa. Controlla l'esecuzione di query [OMI (Open Management Infrastructure)](https://github.com/Microsoft/omi) arbitrarie.

```json
"perfCfg": [
    {
        "namespace": "root/scx",
        "query": "SELECT PercentAvailableMemory, PercentUsedSwap FROM SCX_MemoryStatisticalInformation",
        "table": "LinuxOldMemory",
        "frequency": 300,
        "sinks": ""
    }
]
```

Elemento | valore
------- | -----
spazio dei nomi | (Facoltativo) Spazio dei nomi OMI all'interno del quale deve essere eseguita la query. Se non specificato, il valore predefinito è `"root/scx"` . Viene implementato dall'System Center [multipiattaforma](https://github.com/Microsoft/SCXcore).
query | Query OMI da eseguire.
table | (Facoltativo) Tabella Archiviazione di Azure tabella, nell'account di archiviazione designato. Per altre informazioni, vedere [Impostazioni protette](#protected-settings).
frequency | (Facoltativo) Numero di secondi tra le esecuzioni di query. Il valore predefinito è 300 (5 minuti). Il valore minimo è 15 secondi.
sinks | (Facoltativo) Elenco delimitato da virgole di nomi di più sink in cui devono essere pubblicati i risultati delle metriche di esempio non elaborati. Nessuna aggregazione di questi esempi non elaborati viene calcolata dall'estensione o Monitoraggio di Azure metriche.

È `"table"` necessario specificare o `"sinks"` entrambi.

### <a name="filelogs"></a>fileLogs

La `fileLogs` sezione controlla l'acquisizione dei file di log. LAD acquisisce nuove righe di testo mentre vengono scritte nel file. Li scrive nelle righe della tabella e/o nei sink specificati ( `JsonBlob` o `EventHub` ).

> [!NOTE]
> `fileLogs`L'oggetto viene acquisito da un sottocomponente di LAD denominato `omsagent` . Per raccogliere `fileLogs` , assicurarsi che `omsagent` l'utente abbia le autorizzazioni di lettura per i file specificati. L'utente deve anche disporre delle autorizzazioni di esecuzione per tutte le directory nel percorso di tale file. Dopo aver installato LAD, è possibile controllare le autorizzazioni eseguendo `sudo su omsagent -c 'cat /path/to/file'` .

```json
"fileLogs": [
    {
        "file": "/var/log/mydaemonlog",
        "table": "MyDaemonEvents",
        "sinks": ""
    }
]
```

Elemento | valore
------- | -----
file | Nome completo del percorso del file di log da controllare e acquisire. Il nome del percorso deve assegnare un nome a un singolo file. Non può assegnare un nome a una directory o contenere caratteri jolly. `omsagent`L'account utente deve avere accesso in lettura al percorso del file.
table | (Facoltativo) Tabella Archiviazione di Azure in cui vengono scritte le nuove righe della "coda" del file. La tabella deve essere nell'account di archiviazione designato, come specificato nella configurazione protetta. 
sinks | (Facoltativo) Elenco delimitato da virgole di nomi di più sink a cui vengono inviate le righe di log.

È `"table"` necessario specificare o o `"sinks"` entrambi.

## <a name="metrics-supported-by-the-builtin-provider"></a>Metriche supportate dal provider Builtin

Il `builtin` provider di metriche è un'origine di metriche più interessanti per un'ampia gamma di utenti. Queste metriche sono suddivise in cinque grandi categorie:

* Processore
* Memoria
* Rete
* File system
* Disco

### <a name="builtin-metrics-for-the-processor-class"></a>Metriche Builtin per la classe Processore

La classe di metriche Processore offre informazioni sull'uso del processore nella macchina virtuale. Quando le percentuali vengono aggregate, il risultato è la media tra tutte le CPU. 

In una macchina virtuale con due vCPU, se una vCPU è occupata al 100% e l'altra è inattiva al 100%, il valore segnalato è `PercentIdleTime` 50. Se ogni vCPU è occupata al 50% per lo stesso periodo, anche il risultato restituito è 50. In una macchina virtuale con quattro vCPU, quando una vCPU è occupata al 100% e le altre sono inattive, il valore segnalato `PercentIdleTime` è 75.

Contatore | Significato
------- | -------
PercentIdleTime | Percentuale di tempo durante la finestra di aggregazione in cui i processori hanno eseguito il ciclo di inattività del kernel
PercentProcessorTime | Percentuale di tempo di esecuzione di un thread non inattivo
PercentIOWaitTime | Percentuale di tempo di attesa del completamento delle operazioni di I/O
PercentInterruptTime | Percentuale di tempo durante l'esecuzione di interrupt hardware o software e DPC (chiamate di procedura posticipate)
PercentUserTime | Tempo di non inattività durante la finestra di aggregazione, percentuale di tempo impiegato in modalità utente con priorità normale
PercentNiceTime | Relativamente al tempo di inattività, la percentuale di tempo impiegata a bassa priorità (interessante)
PercentPrivilegedTime | La percentuale di tempo di inattività impiegata in modalità privilegiata (kernel)

I primi quattro contatori dovrebbero essere sommati al 100%. Anche gli ultimi tre contatori vengono sommati al 100%. Questi tre contatori suddivide la somma di `PercentProcessorTime` `PercentIOWaitTime` , e `PercentInterruptTime` .

Per aggregare una singola metrica in tutti i processori, impostare `"condition": "IsAggregate=TRUE"` . Per ottenere una metrica per un processore specifico, ad esempio per il secondo processore logico di una macchina virtuale con quattro CPU virtuali, impostare `"condition": "Name=\\"1\\""`. I valori del processore logico sono compresi nell'intervallo `[0..n-1]`.

### <a name="builtin-metrics-for-the-memory-class"></a>Metriche Builtin per la classe Memoria

La classe Memory delle metriche fornisce informazioni sull'uso della memoria, il paging e lo scambio.

Contatore | Significato
------- | -------
AvailableMemory | Memoria fisica disponibile in MiB
PercentAvailableMemory | Memoria fisica disponibile come percentuale della memoria totale
UsedMemory | Memoria fisica in uso (MiB)
PercentUsedMemory | Memoria fisica in uso come percentuale della memoria totale
PagesPerSec | Paging totale (lettura/scrittura)
PagesReadPerSec | Pagine lette dall'archivio di backup, ad esempio file di scambio, file di programma e file mappato
PagesWrittenPerSec | Pagine scritte nell'archivio di backup, ad esempio file di scambio e file mappato
AvailableSwap | Spazio di swapping inutilizzato (MiB)
PercentAvailableSwap | Spazio di swapping non usato come percentuale dello scambio totale
UsedSwap | Spazio di swapping in uso (MiB)
PercentUsedSwap | Spazio di scambio in uso come percentuale dello scambio totale

Questa classe di metriche ha una sola istanza. `"condition"`L'attributo non ha impostazioni utili e deve essere omesso.

### <a name="builtin-metrics-for-the-network-class"></a>Metriche Builtin per la classe Rete

La classe Di rete delle metriche fornisce informazioni sull'attività di rete in una singola interfaccia di rete dall'avvio. 

LAD non espone le metriche della larghezza di banda. È possibile ottenere queste metriche dalle metriche host.

Contatore | Significato
------- | -------
BytesTransmitted | Totale byte inviati dall'avvio
BytesReceived | Totale byte ricevuti dall'avvio
BytesTotal | Totale byte inviati o ricevuti dall'avvio
PacketsTransmitted | Totale pacchetti inviati dall'avvio
PacketsReceived | Totale pacchetti ricevuti dall'avvio
TotalRxErrors | Numero di errori di ricezione dall'avvio
TotalTxErrors | Numero di errori di trasmissione dall'avvio
TotalCollisions | Numero di collisioni segnalate dalle porte di rete dall'avvio

Anche se viene creata un'istanza della classe Network, LAD non supporta l'acquisizione delle metriche di rete aggregate in tutti i dispositivi di rete. Per ottenere le metriche per un'interfaccia specifica, ad esempio eth0, impostare `"condition": "InstanceID=\\"eth0\\""`.

### <a name="builtin-metrics-for-the-file-system-class"></a>Metriche incorporate per la classe File system

La classe di metriche File system fornisce informazioni sull'utilizzo file system file system. I valori assoluti e percentuali vengono segnalati come verrebbero visualizzati a un utente normale (non radice).

Contatore | Significato
------- | -------
FreeSpace | Spazio disponibile su disco in byte
UsedSpace | Spazio su disco usato in byte
PercentFreeSpace | Percentuale di spazio libero
PercentUsedSpace | Percentuale di spazio usato
PercentFreeInodes | Percentuale di nodi indice inutilizzati (inodi)
PercentUsedInodes | Percentuale di inodi allocati (in uso) sommati in tutti i file system
BytesReadPerSecond | Byte letti per secondo
BytesWrittenPerSecond | Byte scritti per secondo
Byte al secondo | Byte letti o scritti per secondo
ReadsPerSecond | Operazioni di lettura per secondo
WritesPerSecond | Operazioni di scrittura per secondo
TransfersPerSecond | Operazioni di lettura o scrittura per secondo

È possibile ottenere valori aggregati in tutti i file system impostando `"condition": "IsAggregate=True"` . Ottenere i valori per uno specifico file system montato, ad esempio `"/mnt"` , impostando `"condition": 'Name="/mnt"'` . 

> [!NOTE]
> Se si lavora nel portale di Azure invece di JSON, il modulo del campo della condizione è `Name='/mnt'` .

### <a name="builtin-metrics-for-the-disk-class"></a>Metriche Builtin per la classe Disco

La classe di metriche Disco contiene informazioni sull'uso del dispositivo del disco. Queste statistiche si applicano all'intera unità. 

Quando un dispositivo ha più file system, i contatori per tale dispositivo vengono aggregati in modo efficace in tutti i file system.

Contatore | Significato
------- | -------
ReadsPerSecond | Operazioni di lettura per secondo
WritesPerSecond | Operazioni di scrittura per secondo
TransfersPerSecond | Operazioni totali per secondo
AverageReadTime | Media di secondi per operazione di lettura
AverageWriteTime | Media di secondi per operazione di scrittura
AverageTransferTime | Media di secondi per operazione
AverageDiskQueueLength | Media delle operazioni del disco in coda
ReadBytesPerSecond | Numero di byte letti al secondo
WriteBytesPerSecond | Numero di byte scritti al secondo
Byte al secondo | Numero di byte letti o scritti al secondo

È possibile ottenere valori aggregati in tutti i dischi impostando `"condition": "IsAggregate=True"` . Per ottenere informazioni per un dispositivo specifico ,ad esempio , `/dev/sdf1` impostare `"condition": "Name=\\"/dev/sdf1\\""` .

## <a name="install-and-configure-lad-30"></a>Installare e configurare LAD 3.0

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Se le impostazioni protette si trova nel file *ProtectedSettings.js* e le informazioni di configurazione pubblica siPublicSettings.js *in*, eseguire il comando seguente.

```azurecli
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group <resource_group_name> --vm-name <vm_name> --protected-settings ProtectedSettings.json --settings PublicSettings.json
```

Il comando presuppone che si sta usando la modalità Azure Resource Manager dell'interfaccia della riga di comando di Azure. Per configurare LAD per le macchine virtuali del modello di distribuzione classica, passare alla modalità "asm" ( ) e omettere il nome del gruppo `azure config mode asm` di risorse nel comando. 

Per altre informazioni, vedere la [documentazione sull'interfaccia della riga di comando multipiattaforma](/cli/azure/authenticate-azure-cli).

### <a name="powershell"></a>PowerShell

Se le impostazioni protette si trova nella `$protectedSettings` variabile e le informazioni di configurazione pubblica sono nella variabile , eseguire questo `$publicSettings` comando:

```powershell
Set-AzVMExtension -ResourceGroupName <resource_group_name> -VMName <vm_name> -Location <vm_location> -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 3.0
```

## <a name="example-lad-30-configuration"></a>Esempio di configurazione di LAD 3.0

In base alle definizioni precedenti, questa sezione fornisce una configurazione dell'estensione LAD 3.0 di esempio e alcune spiegazioni. Per applicare questo esempio al caso specifico, usare il nome dell'account di archiviazione, il token di firma di accesso condiviso dell'account e i token di firma di accesso condiviso di Hub eventi.

> [!NOTE]
> A seconda che si usi l'interfaccia della riga di comando di Azure o PowerShell per installare LAD, il metodo per fornire impostazioni pubbliche e protette è diverso: 
>
> * Se si usa l'interfaccia della riga  di comando di  Azure, salvare le impostazioni seguenti perProtectedSettings.jssu ePublicSettings.jsusare il comando di esempio precedente. 
> * Se si usa PowerShell, salvare le impostazioni seguenti in `$protectedSettings` e `$publicSettings` eseguendo `$protectedSettings = '{ ... }'` .

### <a name="protected-settings"></a>Impostazioni protette

Le impostazioni protette configurano:

* Account di archiviazione.
* Token di firma di accesso condiviso dell'account corrispondente.
* Diversi sink ( `JsonBlob` o con token di firma di accesso `EventHub` condiviso).

```json
{
  "storageAccountName": "yourdiagstgacct",
  "storageAccountSasToken": "sv=xxxx-xx-xx&ss=bt&srt=co&sp=wlacu&st=yyyy-yy-yyT21%3A22%3A00Z&se=zzzz-zz-zzT21%3A22%3A00Z&sig=fake_signature",
  "sinksConfig": {
    "sink": [
      {
        "name": "SyslogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "FilelogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "MyJsonMetricsBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=fake_signature&se=1808096361&skn=yourehpolicy"
      },
      {
        "name": "MyMetricEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&skn=yourehpolicy"
      },
      {
        "name": "LoggingEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&se=1808096361&skn=yourehpolicy"
      }
    ]
  }
}
```

### <a name="public-settings"></a>Impostazioni pubbliche

Le impostazioni pubbliche causano la connessione LAD a:

* Caricare le metriche percent-processor-time e used-disk-space metrics nella `WADMetrics*` tabella.
* Caricare i messaggi dalla struttura syslog `"user"` e dalla `"info"` gravità nella `LinuxSyslog*` tabella.
* Caricare i risultati delle query OMI non elaborati ( `PercentProcessorTime` e ) nella tabella `PercentIdleTime` `LinuxCPU` denominata.
* Caricare le righe aggiunte nel file `/var/log/myladtestlog` nella `MyLadTestLog` tabella.

In ogni caso, i dati vengono anche caricati:

* Archiviazione BLOB di Azure. Il nome del contenitore è definito nel `JsonBlob` sink.
* Endpoint di Hub eventi, come specificato nel `EventHub` sink.

```json
{
  "StorageAccount": "yourdiagstgacct",
  "ladCfg": {
    "sampleRateInSeconds": 15,
    "diagnosticMonitorConfiguration": {
      "performanceCounters": {
        "sinks": "MyMetricEventHub,MyJsonMetricsBlob",
        "performanceCounterConfiguration": [
          {
            "unit": "Percent",
            "type": "builtin",
            "counter": "PercentProcessorTime",
            "counterSpecifier": "/builtin/Processor/PercentProcessorTime",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Aggregate CPU %utilization"
              }
            ],
            "condition": "IsAggregate=TRUE",
            "class": "Processor"
          },
          {
            "unit": "Bytes",
            "type": "builtin",
            "counter": "UsedSpace",
            "counterSpecifier": "/builtin/FileSystem/UsedSpace",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Used disk space on /"
              }
            ],
            "condition": "Name=\"/\"",
            "class": "Filesystem"
          }
        ]
      },
      "metrics": {
        "metricAggregation": [
          {
            "scheduledTransferPeriod": "PT1H"
          },
          {
            "scheduledTransferPeriod": "PT1M"
          }
        ],
        "resourceId": "/subscriptions/your_azure_subscription_id/resourceGroups/your_resource_group_name/providers/Microsoft.Compute/virtualMachines/your_vm_name"
      },
      "eventVolume": "Large",
      "syslogEvents": {
        "sinks": "SyslogJsonBlob,LoggingEventHub",
        "syslogEventConfiguration": {
          "LOG_USER": "LOG_INFO"
        }
      }
    }
  },
  "perfCfg": [
    {
      "query": "SELECT PercentProcessorTime, PercentIdleTime FROM SCX_ProcessorStatisticalInformation WHERE Name='_TOTAL'",
      "table": "LinuxCpu",
      "frequency": 60,
      "sinks": "LinuxCpuJsonBlob,LinuxCpuEventHub"
    }
  ],
  "fileLogs": [
    {
      "file": "/var/log/myladtestlog",
      "table": "MyLadTestLog",
      "sinks": "FilelogJsonBlob,LoggingEventHub"
    }
  ]
}
```

Nella configurazione `resourceId` deve corrispondere al valore della macchina virtuale o al valore del set di scalabilità della macchina virtuale.

* La creazione di grafici e avvisi delle metriche della piattaforma Azure conosce la macchina virtuale `resourceId` su cui si sta lavorando. Si prevede di trovare i dati per la macchina virtuale usando la `resourceId` chiave di ricerca.
* Se si usa scalabilità automatica di Azure, nella configurazione di scalabilità `resourceId` automatica deve corrispondere a quella utilizzata da `resourceId` LAD.
* `resourceId`l'oggetto è incorporato nei nomi dei BLOB JSON scritti da LAD.

## <a name="view-your-data"></a>Visualizzare i dati

Usare il portale di Azure per visualizzare i dati sulle prestazioni o per impostare gli avvisi:

:::image type="content" source="./media/diagnostics-linux/graph_metrics.png" alt-text="Screenshot che mostra la portale di Azure. La metrica Spazio su disco usato nella metrica è selezionata. Viene visualizzato il grafico risultante.":::

I `performanceCounters` dati vengono sempre archiviati in una Archiviazione di Azure tabella. Le API di Archiviazione di Azure sono disponibili per più linguaggi e piattaforme.

I dati inviati `JsonBlob` ai sink vengono archiviati in BLOB nell'account di archiviazione denominato nelle [impostazioni protette](#protected-settings). È possibile utilizzare i dati BLOB usando Archiviazione BLOB di Azure API.

È anche possibile usare questi strumenti dell'interfaccia utente per accedere ai dati in Archiviazione di Azure:

* Esplora server di Visual Studio.
* [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/)

Lo screenshot seguente di una sessione Azure Storage Explorer mostra le tabelle e i contenitori di Archiviazione di Azure generati da un'estensione LAD 3.0 configurata correttamente in una macchina virtuale di test. L'immagine non corrisponde esattamente alla configurazione [di LAD 3.0 di esempio.](#example-lad-30-configuration)

:::image type="content" source="./media/diagnostics-linux/stg_explorer.png" alt-text="Screenshot che mostra Azure Storage Explorer.":::


Per altre informazioni su come usare i messaggi pubblicati in un endpoint di Hub eventi, vedere la documentazione [di Hub eventi pertinente.](../../event-hubs/event-hubs-about.md)

## <a name="next-steps"></a>Passaggi successivi

* In [Monitoraggio di Azure](../../azure-monitor/alerts/alerts-classic-portal.md)creare avvisi per le metriche raccolte.
* Creare [grafici di monitoraggio](../../azure-monitor/data-platform.md) per le metriche.
* [Creare un set di scalabilità di macchine](../linux/tutorial-create-vmss.md) virtuali usando le metriche per controllare la scalabilità automatica.
