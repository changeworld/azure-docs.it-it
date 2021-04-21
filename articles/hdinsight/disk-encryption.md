---
title: Doppia crittografia per i dati in stato di inquieto
titleSuffix: Azure HDInsight
description: Questo articolo descrive i due livelli di crittografia disponibili per i dati in Azure HDInsight cluster.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/10/2020
ms.openlocfilehash: 226516b1178f14789570b45b68cfdbf56f63bbd7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775152"
---
# <a name="azure-hdinsight-double-encryption-for-data-at-rest"></a>Azure HDInsight doppia crittografia per i dati in stato di inquieto

Questo articolo illustra i metodi per la crittografia dei dati in Azure HDInsight cluster. La crittografia dei dati in pausa si riferisce alla crittografia su dischi gestiti (dischi dati, dischi del sistema operativo e dischi temporanei) collegati alle macchine virtuali del cluster HDInsight. 

Questo documento non indirizza i dati archiviati nell'account Archiviazione di Azure locale. I cluster possono avere uno o più account Archiviazione di Azure in cui le chiavi di crittografia possono anche essere gestite da Microsoft o gestite dal cliente, ma il servizio di crittografia è diverso. Per altre informazioni sulla crittografia Archiviazione di Azure, vedere Archiviazione di Azure [per i dati in stato di inquieto.](../storage/common/storage-service-encryption.md)

## <a name="introduction"></a>Introduzione

Esistono tre ruoli principali del disco gestito in Azure: il disco dati, il disco del sistema operativo e il disco temporaneo. Per altre informazioni sui diversi tipi di dischi gestiti, vedere [Introduzione ai dischi gestiti di Azure.](../virtual-machines/managed-disks-overview.md) 

HDInsight supporta più tipi di crittografia in due livelli diversi:

- Crittografia lato server :SSE viene eseguita dal servizio di archiviazione. In HDInsight, SSE viene usato per crittografare dischi del sistema operativo e dischi dati. ed è abilitato per impostazione predefinita. SSE è un servizio di crittografia di livello 1.
- Crittografia nell'host tramite chiave gestita dalla piattaforma: analogamente a SSE, questo tipo di crittografia viene eseguito dal servizio di archiviazione. Tuttavia, è solo per i dischi temporanei e non è abilitato per impostazione predefinita. La crittografia nell'host è anche un servizio di crittografia di livello 1.
- Crittografia in stato di inquieto usando la chiave gestita dal cliente: questo tipo di crittografia può essere usata sui dati e sui dischi temporanei. Non è abilitato per impostazione predefinita e richiede al cliente di fornire la propria chiave tramite l'insieme di credenziali delle chiavi di Azure. La crittografia in stato di inquieto è un servizio di crittografia di livello 2.

Questi tipi sono riepilogati nella tabella seguente.

|Tipo di cluster |Disco del sistema operativo (disco gestito) |Disco dati (disco gestito) |Disco dati temporaneo (unità SSD locale) |
|---|---|---|---|
|Kafka, HBase con scritture accelerate|Livello 1: [crittografia SSE per](../virtual-machines/managed-disks-overview.md#encryption) impostazione predefinita|Livello 1: [Crittografia SSE per](../virtual-machines/managed-disks-overview.md#encryption) impostazione predefinita, Livello 2: crittografia dei dati in stato di inquieto facoltativa tramite CMK|Livello 1: Crittografia facoltativa nell'host tramite PMK, Livello 2: crittografia dei dati in stato di insodibile facoltativa tramite CMK|
|Tutti gli altri cluster (Spark, Interactive, Hadoop, HBase senza scritture accelerate)|Livello 1: [crittografia SSE per](../virtual-machines/managed-disks-overview.md#encryption) impostazione predefinita|N/D|Livello 1: Crittografia facoltativa nell'host tramite PMK, Livello 2: crittografia dei dati in stato di insodibile facoltativa tramite CMK|

## <a name="encryption-at-rest-using-customer-managed-keys"></a>Crittografia dei dati in pausa con chiavi gestite dal cliente

La crittografia della chiave gestita dal cliente è un processo in un passaggio gestito durante la creazione del cluster senza costi aggiuntivi. È necessario solo autorizzare un'identità gestita con Azure Key Vault e aggiungere la chiave di crittografia quando si crea il cluster.

Sia i dischi dati che i dischi temporanei in ogni nodo del cluster vengono crittografati con una chiave DEK (Data Encryption Key) simmetrica. La chiave DEK viene protetta con la chiave di crittografia della chiave dall'insieme di credenziali delle chiavi. I processi di crittografia e decrittografia vengono gestiti interamente da Azure HDInsight.

Per i dischi del sistema operativo collegati alle macchine virtuali del cluster è disponibile un solo livello di crittografia (PMK). È consigliabile che i clienti evitino di copiare dati sensibili nei dischi del sistema operativo se è necessaria una crittografia CMK per i propri scenari.

Se il firewall dell'insieme di credenziali delle chiavi è abilitato nell'insieme di credenziali delle chiavi in cui è archiviata la chiave di crittografia del disco, gli indirizzi IP del provider di risorse a livello di area HDInsight per l'area in cui verrà distribuito il cluster devono essere aggiunti alla configurazione del firewall dell'insieme di credenziali delle chiavi. Ciò è necessario perché HDInsight non è un servizio attendibile di Azure Key Vault.

È possibile usare il portale di Azure o l'interfaccia della riga di comando di Azure per ruotare in modo sicuro le chiavi nell'insieme di credenziali delle chiavi. Quando una chiave ruota, il cluster HDInsight inizia a usare la nuova chiave in pochi minuti. Abilitare le [funzionalità di protezione con](../key-vault/general/soft-delete-overview.md) chiave di eliminazione soft per la protezione da scenari ransomware ed eliminazione accidentale. Gli insiemi di credenziali delle chiavi senza questa funzionalità di protezione non sono supportati.

### <a name="get-started-with-customer-managed-keys"></a>Introduzione alle chiavi gestite dal cliente

Per creare un cluster HDInsight abilitato per la chiave gestita dal cliente, seguire questa procedura:

1. Creare identità gestite per le risorse di Azure
1. Creare un Azure Key Vault
1. Creare la chiave
1. Crea criteri di accesso
1. Creare un cluster HDInsight con la chiave gestita dal cliente abilitata
1. Rotazione della chiave di crittografia

Ogni passaggio è illustrato in dettaglio in una delle sezioni seguenti.

### <a name="create-managed-identities-for-azure-resources"></a>Creare identità gestite per le risorse di Azure

Creare un'identità gestita assegnata dall'utente per l'autenticazione Key Vault.

Per passaggi specifici, vedere Creare [un'identità gestita assegnata](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) dall'utente. Per altre informazioni sul funzionamento delle identità gestite Azure HDInsight, vedere [Identità gestite in Azure HDInsight](hdinsight-managed-identities.md). Assicurarsi di salvare l'ID risorsa dell'identità gestita da usare quando lo si aggiungerà ai criteri di accesso di Key Vault.

### <a name="create-azure-key-vault"></a>Creare un Azure Key Vault

Creare un insieme di credenziali delle chiavi. Vedere [Creare Azure Key Vault](../key-vault/general/quick-create-portal.md) per passaggi specifici.

In HDInsight è supportato solo Azure Key Vault. Se si ha un proprio insieme di credenziali delle chiavi, è possibile importare le chiavi in Azure Key Vault. Tenere presente che l'insieme di credenziali delle chiavi deve **avere l'opzione Eliminazione soft** abilitata. Per altre informazioni sull'importazione delle chiavi esistenti, vedere [Informazioni su chiavi, segreti e certificati](../key-vault/general/about-keys-secrets-certificates.md).

### <a name="create-key"></a>Creare la chiave

1. Dal nuovo insieme di credenziali delle chiavi passare a **Chiavi**  >  **impostazioni**  >  **+ Genera/Importa**.

    :::image type="content" source="./media/disk-encryption/create-new-key.png" alt-text="Generare una nuova chiave in Azure Key Vault":::

1. Specificare un nome e quindi selezionare **Crea**. Mantenere il tipo **di chiave predefinito** di **RSA**.

    :::image type="content" source="./media/disk-encryption/create-key.png" alt-text="genera il nome della chiave":::

1. Quando si torna alla **pagina Chiavi,** selezionare la chiave creata.

    :::image type="content" source="./media/disk-encryption/key-vault-key-list.png" alt-text="elenco delle chiavi dell'insieme di credenziali delle chiavi":::

1. Selezionare la versione per aprire la **pagina Versione** chiave. Quando si usa la propria chiave per la crittografia del cluster HDInsight, è necessario specificare l'URI della chiave. Copiare l'**identificatore di chiave** e salvarlo in qualunque posizione fino a quando non si è pronti per creare il cluster.

    :::image type="content" source="./media/disk-encryption/get-key-identifier.png" alt-text="ottenere l'identificatore di chiave":::

### <a name="create-access-policy"></a>Crea criteri di accesso

1. Dal nuovo insieme di credenziali delle chiavi passare a **Impostazioni**  >  **Criteri di accesso**+ Aggiungi criteri di  >  **accesso.**

    :::image type="content" source="./media/disk-encryption/key-vault-access-policy.png" alt-text="Creare il nuovo criterio di accesso di Azure Key Vault":::

1. Nella pagina **Aggiungi criteri di accesso** specificare le informazioni seguenti:

    |Proprietà |Descrizione|
    |---|---|
    |Autorizzazioni per le chiavi|Selezionare **Get (Ottieni),** **Unwrap Key (Annulla il** wrapping della chiave) e Wrap Key **(A capo chiave).**|
    |Autorizzazioni segreto|Selezionare **Ottieni,** **Imposta** ed **Elimina.**|
    |Selezionare un'entità|Selezionare l'identità gestita assegnata dall'utente creata in precedenza.|

    :::image type="content" source="./media/disk-encryption/azure-portal-add-access-policy.png" alt-text="Impostare Selezionare un'entità per il criterio di accesso di Azure Key Vault":::

1. Selezionare **Aggiungi**.

1. Selezionare **Salva**.

    :::image type="content" source="./media/disk-encryption/add-key-vault-access-policy-save.png" alt-text="Salvare i Azure Key Vault di accesso":::

### <a name="create-cluster-with-customer-managed-key-disk-encryption"></a>Creare un cluster con crittografia del disco con chiave gestita dal cliente

È ora possibile creare un nuovo cluster HDInsight. Le chiavi gestite dal cliente possono essere applicate solo ai nuovi cluster durante la creazione del cluster. La crittografia non può essere rimossa dai cluster di chiavi gestite dal cliente e le chiavi gestite dal cliente non possono essere aggiunte ai cluster esistenti.

A partire dalla versione di novembre 2020, HDInsight supporta la creazione di cluster con URI di chiave con e senza versione. Se si crea il cluster con un URI di chiave senza versione, il cluster HDInsight tenterà di eseguire la rotazione automatica delle chiavi quando la chiave viene aggiornata nel Azure Key Vault. Se si crea il cluster con un URI di chiave con versione, sarà necessario eseguire una rotazione manuale della chiave, come descritto in [Rotazione della chiave di crittografia](#rotating-the-encryption-key).

Per i cluster creati prima della versione di novembre 2020, è necessario eseguire manualmente la rotazione delle chiavi usando l'URI della chiave con versione.

#### <a name="using-the-azure-portal"></a>Uso del portale di Azure

Durante la creazione del cluster, è possibile usare una chiave con controllo delle versioni o una chiave senza versione nel modo seguente:

- **Con controllo delle** versioni: durante la creazione del cluster, specificare l'identificatore **di chiave completo,** inclusa la versione della chiave. Ad esempio: `https://contoso-kv.vault.azure.net/keys/myClusterKey/46ab702136bc4b229f8b10e8c2997fa4`.
- **Senza versione:** durante la creazione del cluster, specificare solo **l'identificatore di chiave**. Ad esempio: `https://contoso-kv.vault.azure.net/keys/myClusterKey`.

È anche necessario assegnare l'identità gestita al cluster.

:::image type="content" source="./media/disk-encryption/create-cluster-portal.png" alt-text="Creare un nuovo cluster":::

#### <a name="using-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure

L'esempio seguente illustra come usare l'interfaccia della riga di comando di Azure per creare un nuovo cluster Apache Spark con la crittografia del disco abilitata. Per altre informazioni, vedere Interfaccia della riga [di comando di Azure az hdinsight create](/cli/azure/hdinsight#az_hdinsight_create). Il parametro `encryption-key-version` è facoltativo.

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \
-p "HttpPassword1234!" --workernode-data-disks-per-node 2 \
--storage-account MyStorageAccount \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--assign-identity MyMSI
```

#### <a name="using-azure-resource-manager-templates"></a>Uso di modelli di Azure Resource Manager

L'esempio seguente illustra come usare un modello Azure Resource Manager per creare un nuovo cluster Apache Spark con la crittografia del disco abilitata. Per altre informazioni, vedere [Che cosa sono i modelli arm?](../azure-resource-manager/templates/overview.md). La proprietà del modello di Resource Manager `diskEncryptionKeyVersion` è facoltativa.

In questo esempio viene utilizzato PowerShell per chiamare il modello.

```powershell
$templateFile = "azuredeploy.json"
$ResourceGroupName = "MyResourceGroup"
$clusterName = "MyCluster"
$password = ConvertTo-SecureString 'HttpPassword1234!' -AsPlainText -Force
$diskEncryptionVaultUri = "https://MyKeyVault.vault.azure.net"
$diskEncryptionKeyName = "SparkClusterKey"
$diskEncryptionKeyVersion = "00000000000000000000000000000000"
$managedIdentityName = "MyMSI"

New-AzResourceGroupDeployment `
  -Name mySpark `
  -TemplateFile $templateFile `
  -ResourceGroupName $ResourceGroupName `
  -clusterName $clusterName `
  -clusterLoginPassword $password `
` -sshPassword $password `
  -diskEncryptionVaultUri $diskEncryptionVaultUri `
  -diskEncryptionKeyName $diskEncryptionKeyName `
  -diskEncryptionKeyVersion $diskEncryptionKeyVersion `
  -managedIdentityName $managedIdentityName
```

Contenuto del modello di gestione delle risorse, `azuredeploy.json` :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "0.9.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "metadata": {
        "description": "The name of the HDInsight cluster to create."
      }
    },
    "clusterLoginUserName": {
      "type": "string",
      "defaultValue": "admin",
      "metadata": {
        "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
      }
    },
    "clusterLoginPassword": {
      "type": "securestring",
      "metadata": {
        "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "The location where all azure resources will be deployed."
      }
    },
    "sshUserName": {
      "type": "string",
      "defaultValue": "sshuser",
      "metadata": {
        "description": "These credentials can be used to remotely access the cluster."
      }
    },
    "sshPassword": {
      "type": "securestring",
      "metadata": {
        "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
      }
    },
    "headNodeSize": {
      "type": "string",
      "defaultValue": "Standard_D12_v2",
      "metadata": {
        "description": "The VM size of the head nodes."
      }
    },
    "workerNodeSize": {
      "type": "string",
      "defaultValue": "Standard_D13_v2",
      "metadata": {
        "description": "The VM size of the worker nodes."
      }
    },
    "diskEncryptionVaultUri": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault DNSname."
      }
    },
    "diskEncryptionKeyName": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault key name."
      }
    },
    "diskEncryptionKeyVersion": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault key version for the selected key."
      }
    },
    "managedIdentityName": {
      "type": "string",
      "metadata": {
        "description": "The user-assigned managed identity."
      }
    }
  },
  "variables": {
    "defaultStorageAccount": {
      "name": "[uniqueString(resourceGroup().id)]",
      "type": "Standard_LRS"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('defaultStorageAccount').name]",
      "location": "[parameters('location')]",
      "apiVersion": "2019-06-01",
      "sku": {
        "name": "[variables('defaultStorageAccount').type]"
      },
      "kind": "Storage",
      "properties": {}
    },
    {
      "apiVersion": "2018-06-01-preview",
      "name": "[parameters('clusterName')]",
      "type": "Microsoft.HDInsight/clusters",
      "location": "[parameters('location')]",
      "properties": {
        "clusterVersion": "3.6",
        "osType": "Linux",
        "tier": "standard",
        "clusterDefinition": {
          "kind": "spark",
          "componentVersion": {
            "Spark": "2.3"
          },
          "configurations": {
            "gateway": {
              "restAuthCredential.isEnabled": true,
              "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
              "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
            }
          }
        },
        "storageProfile": {
          "storageaccounts": [
            {
              "name": "[replace(replace(reference(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccount').name), '2019-06-01').primaryEndpoints.blob,'https://',''),'/','')]",
              "isDefault": true,
              "container": "[parameters('clusterName')]",
              "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccount').name), '2019-06-01').keys[0].value]"
            }
          ]
        },
        "computeProfile": {
          "roles": [
            {
              "name": "headnode",
              "minInstanceCount": 1,
              "targetInstanceCount": 2,
              "hardwareProfile": {
                "vmSize": "[parameters('headNodeSize')]"
              },
              "osProfile": {
                "linuxOperatingSystemProfile": {
                  "username": "[parameters('sshUserName')]",
                  "password": "[parameters('sshPassword')]"
                },
              },
            },
            {
              "name": "workernode",
              "targetInstanceCount": 1,
              "hardwareProfile": {
                "vmSize": "[parameters('workerNodeSize')]"
              },
              "osProfile": {
                "linuxOperatingSystemProfile": {
                  "username": "[parameters('sshUserName')]",
                  "password": "[parameters('sshPassword')]"
                },
              },
            }
          ]
        },
        "minSupportedTlsVersion": "1.2",
        "diskEncryptionProperties": {
          "vaultUri": "[parameters('diskEncryptionVaultUri')]",
          "keyName": "[parameters('diskEncryptionKeyName')]",
          "keyVersion": "[parameters('diskEncryptionKeyVersion')]",
          "msiResourceId": "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('managedIdentityName'))]"
        }
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('managedIdentityName'))]": {}
        }
      }
    }
  ]
}
```

### <a name="rotating-the-encryption-key"></a>Rotazione della chiave di crittografia

È possibile modificare le chiavi di crittografia usate nel cluster in esecuzione usando il portale di Azure o l'interfaccia della riga di comando di Azure. Per questa operazione, il cluster deve avere accesso sia alla chiave corrente che alla nuova chiave prevista, in caso contrario l'operazione di rotazione della chiave avrà esito negativo. Per i cluster creati dopo la versione di novembre 2020 è possibile scegliere se la nuova chiave deve avere o meno una versione. Per i cluster creati prima della versione di novembre 2020, è necessario usare una chiave con controllo delle versioni quando si ruota la chiave di crittografia.

#### <a name="using-the-azure-portal"></a>Uso del portale di Azure

Per ruotare la chiave, è necessario l'URI dell'insieme di credenziali delle chiavi di base. Al termine, passare alla sezione delle proprietà del cluster HDInsight nel portale e fare clic su Cambia chiave **in** URL chiave di **crittografia disco**. Immettere il nuovo URL della chiave e inviarlo per ruotare la chiave.

:::image type="content" source="./media/disk-encryption/change-key.png" alt-text="ruotare la chiave di crittografia del disco":::

#### <a name="using-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure

L'esempio seguente illustra come ruotare la chiave di crittografia del disco per un cluster HDInsight esistente. Per altre informazioni, vedere Interfaccia della riga [di comando di Azure az hdinsight rotate-disk-encryption-key](/cli/azure/hdinsight#az_hdinsight_rotate_disk_encryption_key).

```azurecli
az hdinsight rotate-disk-encryption-key \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--name MyCluster \
--resource-group MyResourceGroup
```

## <a name="faq-for-customer-managed-key-encryption"></a>Domande frequenti sulla crittografia della chiave gestita dal cliente

**In che modo il cluster HDInsight accede all'insieme di credenziali delle chiavi?**

HDInsight accede all'istanza Azure Key Vault usando l'identità gestita associata al cluster HDInsight. È possibile creare questa identità gestita prima o durante la creazione del cluster. È anche necessario concedere all'identità gestita l'accesso all'insieme di credenziali delle chiavi in cui viene archiviata la chiave.

**Questa funzionalità è disponibile per tutti i cluster in HDInsight?**

La crittografia della chiave gestita dal cliente è disponibile per tutti i tipi di cluster, ad eccezione di Spark 2.1 e 2.2.

**È possibile usare più chiavi per crittografare dischi o cartelle diversi?**

No, tutti i dischi gestiti e i dischi delle risorse vengono crittografati con la stessa chiave.

**Cosa accade se il cluster perde l'accesso all'insieme di credenziali delle chiavi o alla chiave?**

Se il cluster perde l'accesso alla chiave, gli avvisi verranno visualizzati nel portale di Apache Ambari. In questo stato, **l'operazione Cambia** chiave avrà esito negativo. Dopo il ripristino dell'accesso alla chiave, gli avvisi di Ambari verranno eliminati e sarà possibile eseguire correttamente operazioni come la rotazione delle chiavi.

:::image type="content" source="./media/disk-encryption/ambari-alert.png" alt-text="avviso Ambari per l'accesso alla chiave":::

**Come è possibile recuperare il cluster se le chiavi vengono eliminate?**

Poiché sono supportate solo le chiavi abilitate per l'eliminazione soft, se le chiavi vengono ripristinate nell'insieme di credenziali delle chiavi, il cluster deve ottenere nuovamente l'accesso alle chiavi. Per ripristinare una Azure Key Vault chiave, vedere [Undo-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) o [az-keyvault-key-recover.](/cli/azure/keyvault/key#az_keyvault_key_recover)


**Se un cluster viene ridimensionato, i nuovi nodi supporteranno facilmente le chiavi gestite dal cliente?**

Sì. Il cluster deve avere accesso alla chiave nell'insieme di credenziali delle chiavi durante il passaggio a un piano superiore. La stessa chiave viene usata per crittografare i dischi gestiti e i dischi delle risorse nel cluster.

**Le chiavi gestite dal cliente sono disponibili nella posizione?**

Le chiavi gestite dal cliente di HDInsight sono disponibili in tutti i cloud pubblici e cloud nazionali.

## <a name="encryption-at-host-using-platform-managed-keys"></a>Crittografia nell'host con chiavi gestite dalla piattaforma

### <a name="enable-in-the-azure-portal"></a>Abilitazione nel portale di Azure

La crittografia nell'host può essere abilitata durante la creazione del cluster nel portale di Azure.

> [!Note]
> Quando la crittografia nell'host è abilitata, non è possibile aggiungere applicazioni al cluster HDInsight da Azure Marketplace.

:::image type="content" source="media/disk-encryption/encryption-at-host.png" alt-text="Abilitare la crittografia nell'host.":::

Questa opzione abilita [la crittografia nell'host](../virtual-machines/disks-enable-host-based-encryption-portal.md) per i dischi dati temporanei delle macchine virtuali HDInsight che usano PMK. La crittografia nell'host è [supportata solo in alcuni SKU](../virtual-machines/disks-enable-host-based-encryption-portal.md) di macchine virtuali in aree limitate e HDInsight supporta la configurazione del nodo e gli [SKU seguenti.](./hdinsight-supported-node-configuration.md)

Per comprendere le dimensioni della macchina virtuale giuste per il cluster HDInsight, vedere Selezione delle dimensioni della macchina virtuale più Azure HDInsight [cluster.](hdinsight-selecting-vm-size.md) Lo SKU della macchina virtuale predefinito per il nodo Zookeeper quando la crittografia nell'host è abilitata sarà DS2V2.

### <a name="enable-using-powershell"></a>Abilitare con PowerShell

Il frammento di codice seguente illustra come creare un nuovo cluster Azure HDInsight con la crittografia nell'host abilitata tramite PowerShell. Usa il parametro `-EncryptionAtHost $true` per abilitare la funzionalità.

```powershell
$storageAccountResourceGroupName = "Group"
$storageAccountName = "yourstorageacct001"
$storageAccountKey = Get-AzStorageAccountKey `
    -ResourceGroupName $storageAccountResourceGroupName `
    -Name $storageAccountName | %{ $_.Key1 }
$storageContainer = "container002"
# Cluster configuration info
$location = "East US 2"
$clusterResourceGroupName = "Group"
$clusterName = "your-hadoop-002"
$clusterCreds = Get-Credential
# If the cluster's resource group doesn't exist yet, run:
# New-AzResourceGroup -Name $clusterResourceGroupName -Location $location
# Create the cluster
New-AzHDInsightCluster `
    -ClusterType Hadoop `
    -ClusterSizeInNodes 4 `
    -ResourceGroupName $clusterResourceGroupName `
    -ClusterName $clusterName `
    -HttpCredential $clusterCreds `
    -Location $location `
    -DefaultStorageAccountName "$storageAccountName.blob.core.contoso.net" `
    -DefaultStorageAccountKey $storageAccountKey `
    -DefaultStorageContainer $storageContainer `
    -SshCredential $clusterCreds `
    -EncryptionAtHost $true `
```

### <a name="enable-using-azure-cli"></a>Abilitare tramite l'interfaccia della riga di comando di Azure

Il frammento di codice seguente illustra come creare un nuovo cluster Azure HDInsight con la crittografia nell'host abilitata usando l'interfaccia della riga di comando di Azure. Usa il parametro `--encryption-at-host true` per abilitare la funzionalità.

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \\
-p "yourpass" \\
--storage-account MyStorageAccount --encryption-at-host true
```

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sui Azure Key Vault, vedere [Informazioni Azure Key Vault](../key-vault/general/overview.md).
* [Panoramica della sicurezza aziendale in Azure HDInsight](./domain-joined/hdinsight-security-overview.md).
