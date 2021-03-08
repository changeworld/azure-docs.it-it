---
title: Azure Defender per SQL
description: Informazioni sulle funzionalità per la gestione delle vulnerabilità del database e sul rilevamento di attività anomale che potrebbero indicare una minaccia per il database nel database SQL di Azure, in Istanza gestita di Azure SQL o in una sinapsi di Azure.
ms.service: sql-db-mi
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
ms.author: memildin
manager: rkarlin
author: memildin
ms.date: 03/08/2021
ms.openlocfilehash: 27f17b3d1060e8b693c2a34cdb4643840f1bfd13
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2021
ms.locfileid: "102452314"
---
# <a name="azure-defender-for-sql"></a>Azure Defender per SQL

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure Defender per SQL è un pacchetto unificato che include le funzionalità di sicurezza avanzate SQL. Azure Defender è disponibile per database SQL di Azure, Azure SQL Istanza gestita e Azure sinapsi Analytics. Include funzionalità per l'individuazione e la classificazione di dati sensibili, il rilevamento e l'attenuazione di potenziali vulnerabilità dei database e il rilevamento di attività anomale che possono indicare una minaccia per il database. Offre una posizione unica per l'abilitazione e la gestione di queste funzionalità.

## <a name="what-are-the-benefits-of-azure-defender-for-sql"></a>Quali sono i vantaggi di Azure Defender per SQL?

Azure Defender offre un set di funzionalità avanzate per la sicurezza di SQL, tra cui la valutazione della vulnerabilità SQL e la protezione avanzata dalle minacce.
- La [valutazione della vulnerabilità](sql-vulnerability-assessment.md) è un servizio facile da configurare che consente di individuare, monitorare e risolvere potenziali vulnerabilità del database. Offre visibilità sullo stato di sicurezza e include passaggi di utilità pratica per risolvere i problemi di sicurezza e migliorare le fortificazioni del database.
- [Advanced Threat Protection](threat-detection-overview.md) rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i database. Monitora costantemente il database per individuare le attività sospette e fornisce avvisi di sicurezza immediata su potenziali vulnerabilità, attacchi intrusivi in SQL di Azure e modelli di accesso al database anomali. Gli avvisi della funzionalità di Advanced Threat Protection includono dettagli sulle attività sospette e consigliano azioni per l'analisi e la mitigazione della minaccia.

Abilitare Azure Defender per SQL una volta per abilitare tutte le funzionalità incluse. Con un solo clic è possibile abilitare Azure Defender per tutti i database nel [Server](logical-servers.md) in Azure o in SQL istanza gestita. Per abilitare o gestire le impostazioni di Azure Defender è necessario appartenere al ruolo [Gestione sicurezza SQL](../../role-based-access-control/built-in-roles.md#sql-security-manager) o a uno dei ruoli di amministratore del database o del server.

Per altre informazioni sui prezzi di Azure Defender per SQL, vedere la [pagina dei prezzi del Centro sicurezza di Azure](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="enable-azure-defender"></a>Abilitare Azure Defender 
Sono disponibili diversi modi per abilitare i piani di Azure Defender. È possibile abilitarlo a livello di sottoscrizione (**scelta consigliata**) da:

- [Centro sicurezza di Azure](#enable-azure-defender-for-azure-sql-database-at-the-subscription-level-from-azure-security-center)
- [A livello con l'API REST, l'interfaccia della riga di comando di Azure, PowerShell o criteri di Azure](#enable-azure-defender-plans-programatically)

In alternativa, è possibile abilitarlo a livello di risorsa, come descritto in [abilitare Azure Defender per il database SQL di Azure a livello di risorsa](#enable-azure-defender-for-azure-sql-database-at-the-resource-level)

### <a name="enable-azure-defender-for-azure-sql-database-at-the-subscription-level-from-azure-security-center"></a>Abilitare Azure Defender per il database SQL di Azure a livello di sottoscrizione dal centro sicurezza di Azure
Per abilitare Azure Defender per il database SQL di Azure a livello di sottoscrizione dall'interno del Centro sicurezza di Azure:

1. Nel [portale di Azure](https://portal.azure.com) aprire **Centro sicurezza**.
1. Dal menu del Centro sicurezza selezionare **prezzi e impostazioni**.
1. Selezionare la sottoscrizione pertinente.
1. Modificare l'impostazione del piano **su on**.

    :::image type="content" source="media/azure-defender-for-sql/enable-azure-defender-sql-subscription-level.png" alt-text="Abilitazione di Azure Defender per il database SQL di Azure a livello di sottoscrizione.":::

1. Selezionare **Salva**.


### <a name="enable-azure-defender-plans-programatically"></a>Abilita i piani di Azure Defender a livello 

La flessibilità di Azure consente diversi metodi a livello di codice per l'abilitazione dei piani di Azure Defender. 

Per abilitare Azure Defender per la sottoscrizione, usare uno degli strumenti seguenti: 

| Metodo       | Istruzioni                                                                                                                                       |
|--------------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| API REST     | [API di prezzi](/rest/api/securitycenter/pricings)                                                                                                  |
| Interfaccia della riga di comando di Azure    | [az security pricing](/cli/azure/security/pricing)                                                                                                 |
| PowerShell   | [Set-AzSecurityPricing](/powershell/module/az.security/set-azsecuritypricing)                                                                      |
| Criteri di Azure | [Prezzi del bundle](https://github.com/Azure/Azure-Security-Center/blob/master/Pricing%20%26%20Settings/ARM%20Templates/Set-ASC-Bundle-Pricing.json) |
|              |                                                                                                                                                    |

### <a name="enable-azure-defender-for-azure-sql-database-at-the-resource-level"></a>Abilitare Azure Defender per il database SQL di Azure a livello di risorsa

È consigliabile abilitare i piani di Azure Defender a livello di sottoscrizione e questo può consentire la creazione di risorse non protette. Tuttavia, se si ha un motivo aziendale per abilitare Azure Defender a livello di server, seguire questa procedura:

1. Dal [portale di Azure](https://portal.azure.com)aprire il server o l'istanza gestita.
1. Sotto l'intestazione **sicurezza** selezionare **Centro sicurezza**.
1. Selezionare **Abilita Azure Defender per SQL**.

    :::image type="content" source="media/azure-defender-for-sql/enable-azure-defender.png" alt-text="Abilitare Azure Defender per SQL dall'interno dei database SQL di Azure.":::

> [!NOTE]
> Un account di archiviazione viene creato e configurato automaticamente per archiviare i risultati dell'analisi della **valutazione della vulnerabilità** . Se Azure Defender è già stato abilitato per un altro server nello stesso gruppo di risorse e nella stessa area, viene usato l'account di archiviazione esistente.
>
> Il costo di Azure Defender è allineato con i prezzi del livello standard del Centro sicurezza di Azure per nodo, dove un nodo è l'intero server o istanza gestita. Si paga una sola volta per proteggere tutti i database nel server o nell'istanza gestita con Azure Defender. È possibile provare Azure Defender inizialmente con una versione di valutazione gratuita.


## <a name="manage-azure-defender-settings"></a>Gestire le impostazioni di Azure Defender

Per visualizzare e gestire le impostazioni di Azure Defender:

1. Dall'area **sicurezza** del server o dell'istanza gestita, selezionare **Centro sicurezza**.

    In questa pagina verrà visualizzato lo stato di Azure Defender per SQL:

    :::image type="content" source="media/azure-defender-for-sql/status-of-defender-for-sql.png" alt-text="Verifica dello stato di Azure Defender per SQL all'interno dei database SQL di Azure.":::

1. Se Azure Defender per SQL è abilitato, verrà visualizzato un collegamento **Configura** , come illustrato nell'immagine precedente. Per modificare le impostazioni di Azure Defender per SQL, selezionare **Configura**.

    :::image type="content" source="media/azure-defender-for-sql/security-server-settings.png" alt-text="Impostazioni per Azure Defender per SQL.":::

1. Apportare le modifiche necessarie e selezionare **Salva**.


## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Valutazione della vulnerabilità](sql-vulnerability-assessment.md)
- Altre informazioni su [Advanced Threat Protection](threat-detection-configure.md)
- Altre informazioni sul [Centro sicurezza di Azure](../../security-center/security-center-introduction.md)