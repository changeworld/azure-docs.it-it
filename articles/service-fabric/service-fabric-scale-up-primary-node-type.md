---
title: Ridimensionare un tipo di nodo primario di Azure Service Fabric
description: Ridimensiona verticalmente il cluster di Service Fabric aggiungendo un nuovo tipo di nodo e rimuovendo quello precedente.
ms.date: 12/11/2020
ms.author: pepogors
ms.topic: how-to
ms.openlocfilehash: 325ece761481077171a670c52e9d98071237601a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98251182"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type"></a>Aumentare le prestazioni di un tipo di nodo primario di un cluster di Service Fabric

Questo articolo descrive come aumentare la scalabilità verticale di un Service Fabric tipo di nodo primario del cluster con tempi di inattività minimi. La strategia generale per l'aggiornamento di un tipo di nodo del cluster Service Fabric consiste nel:

1. Aggiungere un nuovo tipo di nodo al cluster di Service Fabric, supportato dalla configurazione e dallo SKU del set di scalabilità di macchine virtuali (o modificati) aggiornati. Questo passaggio prevede anche la configurazione di un nuovo servizio di bilanciamento del carico, una subnet e un indirizzo IP pubblico per il set di scalabilità.

1. Quando entrambi i set di scalabilità originali e aggiornati vengono eseguiti side-by-Side, disabilitare le istanze del nodo originale una alla volta in modo che i servizi di sistema (o le repliche dei servizi con stato) eseguano la migrazione al nuovo set di scalabilità.

1. Verificare che il cluster e i nuovi nodi siano integri, quindi rimuovere il set di scalabilità originale (e le risorse correlate) e lo stato del nodo per i nodi eliminati.

Di seguito viene illustrato il processo di aggiornamento delle dimensioni della macchina virtuale e del sistema operativo delle VM del tipo di nodo primario di un cluster di esempio con [durabilità Silver](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster), supportata da un singolo set di scalabilità con cinque nodi. Verrà eseguito l'aggiornamento del tipo di nodo primario:

- Dalle dimensioni della macchina virtuale *Standard_D2_V2* al *D4_V2 standard* e
- Dal sistema operativo VM *Windows server 2016 Datacenter con contenitori* per *Windows Server 2019 Datacenter con contenitori*.

> [!WARNING]
> Prima di provare a eseguire questa procedura su un cluster di produzione, è consigliabile esaminare i modelli di esempio e verificare il processo su un cluster di test. È inoltre possibile che il cluster non sia disponibile per un breve periodo di tempo.
>
> Non tentare una procedura di scalabilità verticale del tipo di nodo primario se lo stato del cluster non è integro, perché il cluster verrà ulteriormente destabilizzato.

Ecco i modelli di distribuzione di Azure Step-by-step che verranno usati per completare questo scenario di aggiornamento di esempio: https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade

## <a name="set-up-the-test-cluster"></a>Configurare il cluster di test

Si configurano il cluster iniziale Service Fabric test. Per prima cosa, [scaricare](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade) i modelli di esempio Azure Resource Manager che verranno usati per completare questo scenario.

Successivamente, accedere all'account Azure.

```powershell
# Sign in to your Azure account
Login-AzAccount -SubscriptionId "<subscription ID>"
```

Aprire quindi il [*parameters.jsnel*](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade/parameters.json) file e aggiornare il valore di `clusterName` in un elemento univoco (in Azure).

I comandi seguenti consentono di generare un nuovo certificato autofirmato e di distribuire il cluster di test. Se si dispone già di un certificato che si vuole usare, passare a [usare un certificato esistente per distribuire il cluster](#use-an-existing-certificate-to-deploy-the-cluster).

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>Generare un certificato autofirmato e distribuire il cluster

Per prima cosa, assegnare le variabili necessarie per la distribuzione del cluster Service Fabric. Modificare i valori per `resourceGroupName`, `certSubjectName`, `parameterFilePath`e `templateFilePath` per l'account e l'ambiente specifici:

```powershell
# Assign deployment variables
$resourceGroupName = "sftestupgradegroup"
$certOutputFolder = "c:\certificates"
$certPassword = "Password!1" | ConvertTo-SecureString -AsPlainText -Force
$certSubjectName = "sftestupgrade.southcentralus.cloudapp.azure.com"
$parameterFilePath = "C:\parameters.json"
$templateFilePath = "C:\Initial-TestClusterSetup.json"
```

> [!NOTE]
> Verificare che il `certOutputFolder` percorso esista nel computer locale prima di eseguire il comando per distribuire un nuovo cluster Service Fabric.

Quindi distribuire il cluster di test di Service Fabric:

```powershell
# Deploy the initial test cluster
New-AzServiceFabricCluster `
    -ResourceGroupName $resourceGroupName `
    -CertificateOutputFolder $certOutputFolder `
    -CertificatePassword $certPassword `
    -CertificateSubjectName $certSubjectName `
    -TemplateFile $templateFilePath `
    -ParameterFile $parameterFilePath
```

Al termine della distribuzione, individuare il file *.pfx* (`$certPfx`) nel computer locale e importarlo nell'archivio certificati:

```powershell
cd c:\certificates
$certPfx = ".\sftestupgradegroup20200312121003.pfx"

Import-PfxCertificate `
     -FilePath $certPfx `
     -CertStoreLocation Cert:\CurrentUser\My `
     -Password (ConvertTo-SecureString Password!1 -AsPlainText -Force)
```

L'operazione restituirà l'identificazione personale del certificato, che ora è possibile usare per [connettersi al nuovo cluster](#connect-to-the-new-cluster-and-check-health-status) e controllarne lo stato di integrità. Ignorare la sezione seguente, che rappresenta un approccio alternativo alla distribuzione di cluster.

### <a name="use-an-existing-certificate-to-deploy-the-cluster"></a>Usare un certificato esistente per distribuire il cluster

In alternativa, è possibile usare un certificato di Azure Key Vault esistente per distribuire il cluster di test. A tale scopo, è necessario [ottenere riferimenti all'identificazione personale del Key Vault e del](#obtain-your-key-vault-references) certificato.

```powershell
# Key Vault variables
$certUrlValue = "https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
$sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
```

Successivamente, designare il nome di un gruppo di risorse per il cluster e impostare le `templateFilePath` `parameterFilePath` posizioni e:

> [!NOTE]
> Il gruppo di risorse designato deve esistere già e trovarsi nella stessa area del Key Vault.

```powershell
$resourceGroupName = "sftestupgradegroup"
$templateFilePath = "C:\Initial-TestClusterSetup.json"
$parameterFilePath = "C:\parameters.json"
```

Infine, eseguire il comando seguente per distribuire il cluster di test iniziale:

```powershell
# Deploy the initial test cluster
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

### <a name="connect-to-the-new-cluster-and-check-health-status"></a>Connettersi al nuovo cluster e controllarne lo stato di integrità

Connettersi al cluster e assicurarsi che tutti e cinque i relativi nodi siano integri (sostituire le `clusterName` `thumb` variabili e con valori personalizzati):

```powershell
# Connect to the cluster
$clusterName = "sftestupgrade.southcentralus.cloudapp.azure.com:19000"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"

Connect-ServiceFabricCluster `
    -ConnectionEndpoint $clusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My

# Check cluster health
Get-ServiceFabricClusterHealth
```

A questo punto, è possibile iniziare la procedura di aggiornamento.

## <a name="deploy-a-new-primary-node-type-with-upgraded-scale-set"></a>Distribuire un nuovo tipo di nodo primario con un set di scalabilità aggiornato

Per eseguire l'aggiornamento (scalabilità verticale) di un tipo di nodo, è prima di tutto necessario distribuire un nuovo tipo di nodo supportato da un nuovo set di scalabilità e le risorse di supporto. Il nuovo set di scalabilità verrà contrassegnato come primario ( `isPrimary: true` ), proprio come il set di scalabilità originale (a meno che non si stia eseguendo un aggiornamento del tipo di nodo non primario). Le risorse create nella sezione seguente diventeranno infine il nuovo tipo di nodo primario nel cluster e le risorse del tipo di nodo primario originale verranno eliminate.

### <a name="update-the-cluster-template-with-the-upgraded-scale-set"></a>Aggiornare il modello del cluster con il set di scalabilità aggiornato

Di seguito sono riportate le modifiche della sezione per sezione del modello di distribuzione del cluster originale per l'aggiunta di un nuovo tipo di nodo primario e le risorse di supporto.

Le modifiche necessarie per questo passaggio sono state già apportate all'utente nel file di modello [*Step1-AddPrimaryNodeType.js*](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade/Step1-AddPrimaryNodeType.json) e le informazioni seguenti illustrano in modo dettagliato tali modifiche. Se si preferisce, è possibile ignorare la spiegazione e continuare a [ottenere i riferimenti Key Vault](#obtain-your-key-vault-references) e [distribuire il modello aggiornato](#deploy-the-updated-template) che aggiunge un nuovo tipo di nodo primario al cluster.

> [!Note]
> Assicurarsi di usare nomi univoci dal tipo di nodo originale, dal set di scalabilità, dal servizio di bilanciamento del carico, dall'IP pubblico e dalla subnet del tipo di nodo primario originale, in quanto queste risorse verranno eliminate in un passaggio successivo del processo.

#### <a name="create-a-new-subnet-in-the-existing-virtual-network"></a>Creare una nuova subnet nella rete virtuale esistente

```json
{
    "name": "[variables('subnet1Name')]",
    "properties": {
        "addressPrefix": "[variables('subnet1Prefix')]"
    }
}
```

#### <a name="create-a-new-public-ip-with-a-unique-domainnamelabel"></a>Creare un nuovo indirizzo IP pubblico con un domainNameLabel univoco

```json
{
    "apiVersion": "[variables('publicIPApiVersion')]",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))]",
    "location": "[variables('computeLocation')]",
    "properties": {
    "dnsSettings": {
        "domainNameLabel": "[concat(variables('dnsName'),'-','nt1')]"
    },
    "publicIPAllocationMethod": "Dynamic"
    },
    "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
    }
}
```

#### <a name="create-a-new-load-balancer-for-the-public-ip"></a>Creare un nuovo servizio di bilanciamento del carico per l'indirizzo IP pubblico

```json
"dependsOn": [
    "[concat('Microsoft.Network/publicIPAddresses/',concat(variables('lbIPName'),'-',variables('vmNodeType1Name')))]"
]
```

#### <a name="create-a-new-virtual-machine-scale-set-with-upgraded-vm-and-os-skus"></a>Creare un nuovo set di scalabilità di macchine virtuali (con SKU della VM e del sistema operativo aggiornati)

Ref del tipo di nodo

```json
"nodeTypeRef": "[variables('vmNodeType1Name')]"
```

SKU di VM

```json
"sku": {
    "name": "[parameters('vmNodeType1Size')]",
    "capacity": "[parameters('nt1InstanceCount')]",
    "tier": "Standard"
}
```

SKU DEL SISTEMA OPERATIVO

```json
"imageReference": {
    "publisher": "[parameters('vmImagePublisher1')]",
    "offer": "[parameters('vmImageOffer1')]",
    "sku": "[parameters('vmImageSku1')]",
    "version": "[parameters('vmImageVersion1')]"
}
```

Assicurarsi inoltre di includere eventuali estensioni aggiuntive necessarie per il carico di lavoro.

#### <a name="add-a-new-primary-node-type-to-the-cluster"></a>Aggiungere un nuovo tipo di nodo primario al cluster

Ora che il nuovo tipo di nodo (vmNodeType1Name) ha un nome, una subnet, un IP, un servizio di bilanciamento del carico e un set di scalabilità, può riutilizzare tutte le altre variabili del tipo di nodo originale (ad esempio `nt0applicationEndPort` , `nt0applicationStartPort` e `nt0fabricTcpGatewayPort` ):

```json
"name": "[variables('vmNodeType1Name')]",
"applicationPorts": {
    "endPort": "[variables('nt0applicationEndPort')]",
    "startPort": "[variables('nt0applicationStartPort')]"
},
"clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
"durabilityLevel": "Bronze",
"ephemeralPorts": {
    "endPort": "[variables('nt0ephemeralEndPort')]",
    "startPort": "[variables('nt0ephemeralStartPort')]"
},
"httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
"isPrimary": true,
"reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
"vmInstanceCount": "[parameters('nt1InstanceCount')]"
```

Dopo aver implementato tutte le modifiche nei file di modello e di parametri, passare alla sezione successiva per acquisire i riferimenti Key Vault e distribuire gli aggiornamenti al cluster.

### <a name="obtain-your-key-vault-references"></a>Ottenere i riferimenti Key Vault

Per distribuire la configurazione aggiornata, sono necessari diversi riferimenti al certificato del cluster archiviato nel Key Vault. Il modo più semplice per trovare questi valori consiste nell'portale di Azure. Saranno necessari gli elementi seguenti:

* **URL Key Vault del certificato del cluster.** Dal Key Vault in portale di Azure selezionare **certificati**  >  *l'*  >  **identificatore del segreto** del certificato desiderato:

    ```powershell
    $certUrlValue="https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
    ```

* **Identificazione personale del certificato del cluster.** Probabilmente è già presente se è stata effettuata [la connessione al cluster iniziale](#connect-to-the-new-cluster-and-check-health-status) per verificarne lo stato di integrità. Dallo stesso pannello **certificato (Certificates**  >  *your desired certificate*) in portale di Azure, copy **X. 509 SHA-1 identificazione personale (in esadecimale)**:

    ```powershell
    $thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
    ```

* **ID risorsa della Key Vault.** Dal Key Vault in portale di Azure selezionare **Proprietà**  >  **ID risorsa**:

    ```powershell
    $sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
    ```

### <a name="deploy-the-updated-template"></a>Distribuire il modello aggiornato

Modificare `templateFilePath` in base alle esigenze ed eseguire il comando seguente:

```powershell
# Deploy the new node type and its resources
$templateFilePath = "C:\Step1-AddPrimaryNodeType.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

Al termine della distribuzione, controllare di nuovo l'integrità del cluster e assicurarsi che tutti i nodi in entrambi i tipi di nodo siano integri.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-seed-nodes-to-the-new-node-type"></a>Migrare i nodi di inizializzazione al nuovo tipo di nodo

A questo punto è possibile aggiornare il tipo di nodo originale come non primario e iniziare a disabilitare i relativi nodi. Quando i nodi vengono disabilitati, i nodi di sistema e di inizializzazione del cluster vengono migrati al nuovo set di scalabilità.

### <a name="unmark-the-original-node-type-as-primary"></a>Deseleziona il tipo di nodo originale come primario

Rimuovere innanzitutto la `isPrimary` designazione nel modello dal tipo di nodo originale.

```json
{
    "isPrimary": false,
}
```

Quindi, distribuire il modello con l'aggiornamento. Verrà avviata la migrazione dei nodi di inizializzazione al nuovo set di scalabilità.

```powershell
$templateFilePath = "C:\Step2-UnmarkOriginalPrimaryNodeType.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

> [!Note]
> Il completamento della migrazione del nodo di inizializzazione al nuovo set di scalabilità potrebbe richiedere del tempo. Per garantire la coerenza dei dati, è possibile modificare un solo nodo di inizializzazione alla volta. Per ogni modifica del nodo di inizializzazione è necessario un aggiornamento del cluster. La sostituzione di un nodo di inizializzazione richiede quindi due aggiornamenti del cluster, uno ciascuno per l'aggiunta e la rimozione di nodi. L'aggiornamento dei cinque nodi di inizializzazione in questo scenario di esempio comporterà dieci aggiornamenti del cluster.

Usare Service Fabric Explorer per monitorare la migrazione dei nodi di inizializzazione al nuovo set di scalabilità. I nodi del tipo di nodo originale (nt0vm) devono essere tutti *false* nella colonna del nodo del valore di **inizializzazione** e quelli del nuovo tipo di nodo (nt1vm) saranno *true*.

### <a name="disable-the-nodes-in-the-original-node-type-scale-set"></a>Disabilitare i nodi nel set di scalabilità del tipo di nodo originale

Una volta che tutti i nodi di inizializzazione sono stati migrati al nuovo set di scalabilità, è possibile disabilitare i nodi del set di scalabilità originale.

```powershell
# Disable the nodes in the original scale set.
$nodeType = "nt0vm"
$nodes = Get-ServiceFabricNode

Write-Host "Disabling nodes..."
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Disable-ServiceFabricNode -Intent RemoveNode -NodeName $node.NodeName -Force
  }
}
```

Usare Service Fabric Explorer per monitorare lo stato di avanzamento dei nodi nel set di scalabilità originale dalla *disabilitazione* allo stato *disabilitato* .

:::image type="content" source="./media/scale-up-primary-node-type/service-fabric-explorer-node-status.png" alt-text="Service Fabric Explorer che mostra lo stato dei nodi disabilitati":::

Per la durabilità Silver e Gold, alcuni nodi entrano nello stato disabilitato, mentre altri potrebbero rimanere in uno stato di *disabilitazione* . In Service Fabric Explorer controllare la scheda **Dettagli** dei nodi in stato di disabilitazione. Se visualizzano un *controllo di sicurezza in sospeso* di tipo *EnsurePartitionQuorem* (garantendo il quorum per le partizioni del servizio di infrastruttura), è possibile continuare con sicurezza.

:::image type="content" source="./media/scale-up-primary-node-type/service-fabric-explorer-node-status-disabling.png" alt-text="È possibile procedere con l'arresto dei dati e la rimozione dei nodi bloccati nello stato &quot;Disabilitazione&quot; Se viene visualizzato un controllo di sicurezza in sospeso di tipo &quot;EnsurePartitionQuorum&quot;.":::

Se il cluster è durabilità Bronze, attendere che tutti i nodi raggiungano lo stato *disabilitato* .

### <a name="stop-data-on-the-disabled-nodes"></a>Arrestare i dati nei nodi disabilitati

A questo punto è possibile arrestare i dati nei nodi disabilitati.

```powershell
# Stop data on the disabled nodes.
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Start-ServiceFabricNodeTransition -Stop -OperationId (New-Guid) -NodeInstanceId $node.NodeInstanceId -NodeName $node.NodeName -StopDurationInSeconds 10000
  }
}
```

## <a name="remove-the-original-node-type-and-cleanup-its-resources"></a>Rimuovere il tipo di nodo originale e pulire le risorse

È ora possibile rimuovere il tipo di nodo originale e le risorse associate per concludere la procedura di scalabilità verticale.

### <a name="remove-the-original-scale-set"></a>Rimuovere il set di scalabilità originale

Rimuovere prima di tutto il set di scalabilità di backup del tipo di nodo.

```powershell
$scaleSetName = "nt0vm"
$scaleSetResourceType = "Microsoft.Compute/virtualMachineScaleSets"

Remove-AzResource -ResourceName $scaleSetName -ResourceType $scaleSetResourceType -ResourceGroupName $resourceGroupName -Force
```

### <a name="delete-the-original-ip-and-load-balancer-resources"></a>Elimina le risorse IP e di bilanciamento del carico originali

È ora possibile eliminare l'indirizzo IP originale e le risorse del servizio di bilanciamento del carico. In questo passaggio verrà aggiornato anche il nome DNS.

> [!Note]
> Questo passaggio è facoltativo se si usa già un IP pubblico con SKU *standard* e un servizio di bilanciamento del carico. In questo caso è possibile avere più tipi di set di scalabilità/nodo nello stesso servizio di bilanciamento del carico.

Eseguire i comandi seguenti, modificando il `$lbname` valore in base alle esigenze.

```powershell
# Delete the original IP and load balancer resources
$lbName = "LB-sftestupgrade-nt0vm"
$lbResourceType = "Microsoft.Network/loadBalancers"
$ipResourceType = "Microsoft.Network/publicIPAddresses"
$oldPublicIpName = "PublicIP-LB-FE-nt0vm"
$newPublicIpName = "PublicIP-LB-FE-nt1vm"

$oldPrimaryPublicIP = Get-AzPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $resourceGroupName
$primaryDNSName = $oldPrimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldPrimaryPublicIP.DnsSettings.Fqdn

Remove-AzResource -ResourceName $lbName -ResourceType $lbResourceType -ResourceGroupName $resourceGroupName -Force
Remove-AzResource -ResourceName $oldPublicIpName -ResourceType $ipResourceType -ResourceGroupName $resourceGroupName -Force

$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $resourceGroupName
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP
```

### <a name="remove-node-state-from-the-original-node-type"></a>Rimuovere lo stato del nodo dal tipo di nodo originale

Nei nodi del tipo di nodo originale verrà ora visualizzato un *errore* per il relativo **stato di integrità**. Rimuovere lo stato del nodo dal cluster.

```powershell
# Remove state of the obsolete nodes from the cluster
$nodeType = "nt0vm"
$nodes = Get-ServiceFabricNode

Write-Host "Removing node state..."
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Remove-ServiceFabricNodeState -NodeName $node.NodeName -Force
  }
}
```

Service Fabric Explorer dovrebbe ora riflettere solo i cinque nodi del nuovo tipo di nodo (nt1vm), tutti con i valori dello stato di integrità *OK*. Lo stato di integrità del cluster mostrerà comunque un *errore*. Questa operazione verrà quindi aggiornata aggiornando il modello in modo da riflettere le ultime modifiche e ridistribuzioni.

### <a name="update-the-deployment-template-to-reflect-the-newly-scaled-up-primary-node-type"></a>Aggiornare il modello di distribuzione in modo da riflettere il tipo di nodo primario appena scalato

Le modifiche necessarie per questo passaggio sono state già apportate nel file di modello [*Step3-CleanupOriginalPrimaryNodeType.js*](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade/Step3-CleanupOriginalPrimaryNodeType.json) e le sezioni seguenti illustrano in dettaglio queste modifiche del modello. Se si preferisce, è possibile ignorare la spiegazione e continuare a [distribuire il modello aggiornato](#deploy-the-finalized-template) e completare l'esercitazione.

#### <a name="update-the-cluster-management-endpoint"></a>Aggiornare l'endpoint di gestione del cluster

Aggiornare il cluster `managementEndpoint` nel modello di distribuzione in modo che faccia riferimento al nuovo IP (aggiornando *VmNodeType0Name* con *vmNodeType1Name*).

```json
  "managementEndpoint": "[concat('https://',reference(concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))).dnsSettings.fqdn,':',variables('nt0fabricHttpGatewayPort'))]",
```

#### <a name="remove-the-original-node-type-reference"></a>Rimuovere il riferimento al tipo di nodo originale

Rimuovere il riferimento al tipo di nodo originale dalla risorsa Service Fabric nel modello di distribuzione:

```json
"name": "[variables('vmNodeType0Name')]",
"applicationPorts": {
    "endPort": "[variables('nt0applicationEndPort')]",
    "startPort": "[variables('nt0applicationStartPort')]"
},
"clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
"durabilityLevel": "Bronze",
"ephemeralPorts": {
    "endPort": "[variables('nt0ephemeralEndPort')]",
    "startPort": "[variables('nt0ephemeralStartPort')]"
},
"httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
"isPrimary": true,
"reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
"vmInstanceCount": "[parameters('nt0InstanceCount')]"
```

#### <a name="configure-health-policies-to-ignore-existing-errors"></a>Configurare i criteri di integrità per ignorare gli errori esistenti

Solo per i cluster con durabilità Silver e versioni successive, aggiornare la risorsa cluster nel modello e configurare i criteri di integrità per ignorare l' `fabric:/System` integrità dell'applicazione aggiungendo *applicationDeltaHealthPolicies* in proprietà risorse cluster come indicato di seguito. Il criterio seguente ignorerà gli errori esistenti ma non consentirà nuovi errori di integrità.

```json
"upgradeDescription":  
{ 
 "forceRestart": false, 
 "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807", 
 "healthCheckWaitDuration": "00:05:00", 
 "healthCheckStableDuration": "00:05:00", 
 "healthCheckRetryTimeout": "00:45:00", 
 "upgradeTimeout": "12:00:00", 
 "upgradeDomainTimeout": "02:00:00", 
 "healthPolicy": { 
   "maxPercentUnhealthyNodes": 100, 
   "maxPercentUnhealthyApplications": 100 
 }, 
 "deltaHealthPolicy":  
 { 
   "maxPercentDeltaUnhealthyNodes": 0, 
   "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0, 
   "maxPercentDeltaUnhealthyApplications": 0, 
   "applicationDeltaHealthPolicies":  
   { 
       "fabric:/System":  
       { 
           "defaultServiceTypeDeltaHealthPolicy":  
           { 
                   "maxPercentDeltaUnhealthyServices": 0 
           } 
       } 
   } 
 } 
}
```

#### <a name="remove-supporting-resources-for-the-original-node-type"></a>Rimuovere le risorse di supporto per il tipo di nodo originale

Rimuovere tutte le altre risorse correlate al tipo di nodo originale dal modello ARM e dal file dei parametri. Eliminare quanto segue:

```json
    "vmImagePublisher": {
      "value": "MicrosoftWindowsServer"
    },
    "vmImageOffer": {
      "value": "WindowsServer"
    },
    "vmImageSku": {
      "value": "2016-Datacenter-with-Containers"
    },
    "vmImageVersion": {
      "value": "latest"
    },
```

#### <a name="deploy-the-finalized-template"></a>Distribuire il modello finalizzato

Infine, distribuire il modello di Azure Resource Manager modificato.

```powershell
# Deploy the updated template file
$templateFilePath = "C:\Step3-CleanupOriginalPrimaryNodeType"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

> [!NOTE]
> Questa operazione richiederà un po' di tempo, in genere fino a due ore.

L'aggiornamento modificherà le impostazioni in *InfrastructureService*; Pertanto, è necessario riavviare un nodo. In questo caso, *forceRestart* viene ignorato. Il parametro `upgradeReplicaSetCheckTimeout` specifica il tempo massimo di attesa Service Fabric prima che una partizione sia in uno stato sicuro, se non è già in uno stato sicuro. Una volta superati i controlli di sicurezza per tutte le partizioni in un nodo, Service Fabric procede con l'aggiornamento su tale nodo. Il valore per il parametro `upgradeTimeout` può essere ridotto a 6 ore, ma è consigliabile usare per la massima sicurezza 12 ore.

Al termine della distribuzione, verificare che in portale di Azure che lo stato della risorsa Service Fabric sia *pronto*. Verificare che sia possibile raggiungere il nuovo endpoint di Service Fabric Explorer, che lo **stato di integrità del cluster** sia *OK* e che tutte le applicazioni distribuite funzionino correttamente.

A questo proposito, è stato scalato verticalmente un tipo di nodo primario del cluster.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [aggiungere un tipo di nodo a un cluster](virtual-machine-scale-set-scale-node-type-scale-out.md)
* Informazioni sulla [scalabilità delle applicazioni](service-fabric-concepts-scalability.md).
* [Ridimensionamento orizzontale di un cluster di Azure](service-fabric-tutorial-scale-cluster.md).
* [Ridimensionamento di un cluster di Azure a livello di codice](service-fabric-cluster-programmatic-scaling.md) tramite l'SDK di calcolo di Azure Fluent.
* [Ridimensionamento orizzontale di un cluster autonomo](service-fabric-cluster-windows-server-add-remove-nodes.md).
