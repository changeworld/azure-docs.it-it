---
title: Configurare Azure Defender per Archiviazione
titleSuffix: Azure Storage
description: Configurare Azure Defender per l'archiviazione per rilevare le anomalie nell'attività dell'account e ricevere notifiche relative a tentativi potenzialmente dannosi di accesso all'account.
services: storage
author: tamram
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: tamram
ms.reviewer: ozgun
ms.openlocfilehash: e2f044ab267365885260b031638572846184bc83
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063186"
---
# <a name="configure-azure-defender-for-storage"></a>Configurare Azure Defender per Archiviazione

Azure Defender per Archiviazione offre un livello aggiuntivo di intelligence di sicurezza in grado di rilevare tentativi insoliti e potenzialmente dannosi di accesso o exploit degli account di archiviazione. Questo livello di protezione consente di affrontare le minacce senza dover essere esperti di sicurezza o dover gestire sistemi di monitoraggio della sicurezza.

Gli avvisi di sicurezza vengono attivati quando si verifica un'anomalia nell'attività. Questi avvisi di sicurezza sono integrati con il [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/)e vengono inviati anche tramite posta elettronica agli amministratori della sottoscrizione, con informazioni dettagliate sulle attività sospette e consigli su come analizzare e correggere le minacce.

Il servizio inserisce i log delle risorse di richieste di lettura, scrittura ed eliminazione nell'archivio BLOB e in File di Azure per il rilevamento delle minacce. Per esaminare gli avvisi di Azure Defender, è possibile visualizzare le attività di archiviazione correlate usando Analisi archiviazione registrazione. Per altre informazioni, vedere **configurare la registrazione** in [monitorare un account di archiviazione nell'portale di Azure](./manage-storage-analytics-logs.md#configure-logging).

## <a name="availability"></a>Disponibilità

Azure Defender per Archiviazione è attualmente disponibile per Archiviazione BLOB, File di Azure e Azure Data Lake Storage Gen2. I tipi di account che supportano Azure Defender includono account per utilizzo generico v2, BLOB in blocchi e account di archiviazione BLOB. Azure Defender per Archiviazione è disponibile in tutti i cloud pubblici e nei cloud US Government, ma non in altre aree del cloud Azure per enti pubblici o sovrano.

Gli account con spazi dei nomi gerarchici abilitati per Data Lake Storage supportano le transazioni usando sia le API di archiviazione BLOB di Azure che le API di Data Lake Storage. Le condivisioni file di Azure supportano le transazioni tramite SMB.

Per informazioni dettagliate sui prezzi, inclusa una versione di valutazione gratuita di 30 giorni, vedere la [pagina dei prezzi del Centro sicurezza di Azure](https://azure.microsoft.com/pricing/details/security-center/).

Nell'elenco seguente viene riepilogata la disponibilità di Azure Defender per l'archiviazione:

- Stato della versione:
  - [Archiviazione BLOB](https://azure.microsoft.com/services/storage/blobs/) (disponibilità generale)
  - [File di Azure](../files/storage-files-introduction.md) (disponibilità generale)
  - Azure Data Lake Storage Gen2 (disponibilità generale)
- Cloud:<br>
    ✔ Cloud commerciali<br>
    ✔ US Gov<br>
    ✘ Cina, altro gov

## <a name="set-up-azure-defender"></a>Configurare Azure Defender

È possibile configurare Azure Defender per l'archiviazione in diversi modi, descritti nelle sezioni seguenti.

### <a name="azure-security-center"></a>[Centro sicurezza di Azure](#tab/azure-security-center)

Azure Defender è integrato nel centro sicurezza di Azure. Quando si Abilita Azure Defender sulla sottoscrizione, Azure Defender per archiviazione di Azure viene abilitato automaticamente per tutti gli account di archiviazione. È possibile abilitare o disabilitare Azure Defender per gli account di archiviazione in una sottoscrizione specifica, come indicato di seguito:

1. Avviare il **Centro sicurezza di Azure** nella [portale di Azure](https://portal.azure.com).
1. Dal menu principale, in **gestione**, selezionare **prezzi & impostazioni**.
1. Selezionare la sottoscrizione per cui si vuole abilitare o disabilitare Azure Defender.
1. Selezionare **Azure Defender in** per abilitare Azure Defender per la sottoscrizione.
1. In **selezione piano di Azure Defender per tipo di risorsa** individuare la riga di **archiviazione** e selezionare **abilitata** nella colonna **piano** .
1. Salvare le modifiche.

    :::image type="content" source="media/azure-defender-storage-configure/enable-azure-defender-security-center.png" alt-text="Screenshot che illustra come abilitare Azure Defender per l'archiviazione nel centro sicurezza":::

Azure Defender è ora abilitato per tutti gli account di archiviazione in questa sottoscrizione.

### <a name="portal"></a>[Portale](#tab/azure-portal)

1. Avviare il [portale di Azure](https://portal.azure.com/).
1. Passare all'account di archiviazione. In **Impostazioni** selezionare **Sicurezza avanzata**.
1. Selezionare **Abilita Azure Defender per l'archiviazione**.

    :::image type="content" source="media/azure-defender-storage-configure/enable-azure-defender-portal.png" alt-text="Screenshot che illustra come abilitare Azure Defender per un account di archiviazione di Azure":::

Azure Defender è ora abilitato per questo account di archiviazione.

### <a name="template"></a>[Modello](#tab/template)

Usare un modello di Azure Resource Manager per distribuire un account di archiviazione di Azure con Azure Defender abilitato. Per altre informazioni, vedere [account di archiviazione con Advanced Threat Protection](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/).

### <a name="azure-policy"></a>[Criteri di Azure](#tab/azure-policy)

Usare un criterio di Azure per abilitare Azure Defender tra gli account di archiviazione in una sottoscrizione o un gruppo di risorse specifico.

1. Avviare la pagina **criteri di Azure-definizioni** .
1. Cercare i criteri **deploy Azure Defender on Storage Accounts** .

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy-definitions.png" alt-text="Applicare i criteri per abilitare Azure Defender per gli account di archiviazione":::

1. Selezionare una sottoscrizione o un gruppo di risorse di Azure.

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy2.png" alt-text="Selezionare una sottoscrizione o un gruppo di risorse per l'ambito dei criteri ":::

1. Assegnare i criteri.

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy1.png" alt-text="Assegnare i criteri per abilitare Azure Defender per l'archiviazione":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per abilitare Azure Defender per un account di archiviazione con PowerShell, assicurarsi prima di aver installato il modulo [AZ. Security](https://www.powershellgallery.com/packages/Az.Security) . Chiamare quindi il comando [Enable-AzSecurityAdvancedThreatProtection](/powershell/module/az.security/enable-azsecurityadvancedthreatprotection) . Ricordare di sostituire i valori tra parentesi angolari con valori personalizzati:

```azurepowershell
Enable-AzSecurityAdvancedThreatProtection -ResourceId "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/"
```

Per controllare l'impostazione di Azure Defender per un account di archiviazione con PowerShell, chiamare il comando [Get-AzSecurityAdvancedThreatProtection](/powershell/module/az.security/get-azsecurityadvancedthreatprotection) . Ricordare di sostituire i valori tra parentesi angolari con valori personalizzati:

```azurepowershell
Get-AzSecurityAdvancedThreatProtection -ResourceId "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/"
```

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per abilitare Azure Defender per un account di archiviazione con l'interfaccia della riga di comando di Azure, chiamare il comando [AZ Security ATP Storage Update](/cli/azure/security/atp/storage#az_security_atp_storage_update) . Ricordare di sostituire i valori tra parentesi angolari con valori personalizzati:

```azurecli
az security atp storage update \
    --resource-group <resource-group> \
    --storage-account <storage-account> \
    --is-enabled true
```

Per controllare l'impostazione di Azure Defender per un account di archiviazione con l'interfaccia della riga di comando di Azure, chiamare il comando [AZ Security ATP storage Show](/cli/azure/security/atp/storage#az_security_atp_storage_show) . Ricordare di sostituire i valori tra parentesi angolari con valori personalizzati:

```azurecli
az security atp storage show \
    --resource-group <resource-group> \
    --storage-account <storage-account>
```

---

## <a name="explore-security-anomalies"></a>Esplorare le anomalie della sicurezza

Quando si verificano anomalie nelle attività di archiviazione, si riceve una e-mail di notifica con le informazioni sull'evento sospetto di sicurezza. I dettagli sull'evento comprendono:

- Natura dell'anomalia
- Il nome dell'account di archiviazione
- Ora dell'evento
- Tipo di archiviazione
- Possibili cause
- Passaggi per l'indagine
- Passaggi per la correzione

L'e-mail fornisce inoltre informazioni sulle possibili cause e le azioni consigliate per analizzare e ridurre il rischio di una potenziale minaccia.

:::image type="content" source="media/azure-defender-storage-configure/storage-advanced-threat-protection-alert-email.png" alt-text="Posta elettronica di avviso di Azure Defender per archiviazione":::

È possibile esaminare e gestire gli avvisi di sicurezza correnti dal [riquadro avvisi di sicurezza](../../security-center/security-center-managing-and-responding-alerts.md)del Centro sicurezza di Azure. Facendo clic su un avviso specifico vengono visualizzati altri dettagli e azioni per analizzare la minaccia e risolvere eventuali minacce future.

:::image type="content" source="media/azure-defender-storage-configure/storage-advanced-threat-protection-alert.png" alt-text="Avviso di Azure Defender per archiviazione":::

## <a name="security-alerts"></a>Avvisi di sicurezza

Gli avvisi sono generati dai tentativi insoliti e potenzialmente dannosi di accesso o sfruttamento degli account di archiviazione. Per un elenco degli avvisi per archiviazione di Azure, vedere [avvisi per archiviazione di Azure](../../security-center/alerts-reference.md#alerts-azurestorage).

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione ad Azure Defender per Archiviazione](../../security-center/defender-for-storage-introduction.md)
- [Centro sicurezza di Azure](../../security-center/security-center-introduction.md)
- [Log negli account di archiviazione di Azure](/rest/api/storageservices/About-Storage-Analytics-Logging)
