---
title: Apache Hadoop & archiviazione sicura per il trasferimento - Azure HDInsight
description: Informazioni su come creare cluster HDInsight con account di archiviazione di Azure con trasferimento sicuro abilitato.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 02/18/2020
ms.openlocfilehash: 22804015ebf0344c00e60c88f780fe22ba440b52
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774990"
---
# <a name="apache-hadoop-clusters-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Apache Hadoop cluster con account di archiviazione per il trasferimento sicuro in Azure HDInsight

La funzionalità [Trasferimento sicuro obbligatorio](../storage/common/storage-require-secure-transfer.md) aumenta la sicurezza dell'account di archiviazione di Azure perché consente l'invio di tutte le richieste all'account solo tramite connessioni sicure. Questa funzionalità e lo schema wasbs sono supportati solo dal cluster HDInsight versione 3.6 o successiva.

> [!IMPORTANT]
> L'abilitazione del trasferimento sicuro dell'archiviazione dopo la creazione di un cluster può causare errori durante l'uso dell'account di archiviazione e non è consigliabile. È meglio creare un nuovo cluster usando un account di archiviazione con il trasferimento sicuro già abilitato.

## <a name="storage-accounts"></a>Account di archiviazione

### <a name="azure-portal"></a>Portale di Azure

Per impostazione predefinita, la proprietà obbligatoria per il trasferimento sicuro è abilitata quando si crea un account di archiviazione in portale di Azure.

Per aggiornare un account di archiviazione esistente con portale di Azure, vedere [Richiedere il trasferimento sicuro con portale di Azure](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-for-an-existing-storage-account).

### <a name="powershell"></a>PowerShell

Per il cmdlet di PowerShell [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount), verificare che il parametro `-EnableHttpsTrafficOnly` sia impostato su `1` .

Per aggiornare un account di archiviazione esistente con PowerShell, vedere [Richiedere il trasferimento sicuro con PowerShell.](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-powershell)

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per il comando dell'interfaccia della riga di comando di Azure [az storage account create,](/cli/azure/storage/account#az_storage_account_create)verificare che il `--https-only` parametro sia impostato su `true` .

Per aggiornare un account di archiviazione esistente con l'interfaccia della riga di comando di Azure, vedere Richiedere il trasferimento sicuro con l'interfaccia [della riga di comando di Azure.](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-azure-cli)

## <a name="add-additional-storage-accounts"></a>Aggiungere altri account di archiviazione

Sono disponibili diverse opzioni per aggiungere altri account di archiviazione con trasferimento sicuro abilitato:

* Modificare il modello di Azure Resource Manager nell'ultima sezione.
* Creare un cluster usando il [portale di Azure](https://portal.azure.com) e specificare l'account di archiviazione collegato.
* Usare l'azione script per aggiungere altri account di archiviazione con trasferimento sicuro abilitato a un cluster HDInsight esistente. Per altre informazioni, vedere [Aggiungere altri account di archiviazione a HDInsight](hdinsight-hadoop-add-storage.md).

## <a name="next-steps"></a>Passaggi successivi

* L'uso di archiviazione di Azure (WASB) anziché di [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) come archivio predefinito di dati
* Per informazioni sul modo in cui HDInsight usa Archiviazione di Azure, vedere [Usare Archiviazione di Azure con HDInsight](hdinsight-hadoop-use-blob-storage.md).
* Per informazioni su come caricare i dati in HDInsight, vedere [Caricare dati in HDInsight](hdinsight-upload-data.md).