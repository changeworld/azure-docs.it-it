---
title: Configurare Azure Defender per Archiviazione
titleSuffix: Azure Storage
description: Configurare Azure Defender per l'archiviazione per rilevare le anomalie nell'attività dell'account e ricevere notifiche relative a tentativi potenzialmente dannosi di accesso all'account.
services: storage
author: tamram
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: tamram
ms.reviewer: ozgun
ms.openlocfilehash: cdfc54b1eca3b07202148b7099884a04f35939ef
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101698145"
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

Quando si sottoscrive il livello standard nel centro sicurezza di Azure, Azure Defender viene impostato automaticamente su tutti gli account di archiviazione. È possibile abilitare o disabilitare Azure Defender per gli account di archiviazione in una sottoscrizione specifica, come indicato di seguito:

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

### <a name="rest-api"></a>[REST API](#tab/rest-api)

Usare i comandi dell'API REST per creare, aggiornare o ottenere l'impostazione di Azure Defender per un account di archiviazione specifico.

- [Advanced Threat Protection-crea](/rest/api/securitycenter/advancedthreatprotection/create)
- [Advanced Threat Protection-Ottieni](/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Usare i cmdlet di PowerShell seguenti:

- [Abilita Advanced Threat Protection](/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
- [Ottenere Advanced Threat Protection](/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
- [Disabilitare Advanced Threat Protection](/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

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

- Altre informazioni sui [log negli account di archiviazione di Azure](/rest/api/storageservices/About-Storage-Analytics-Logging)
- Altre informazioni sul [Centro sicurezza di Azure](../../security-center/security-center-introduction.md)