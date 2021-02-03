---
title: Azure Defender per SQL
description: Informazioni sulle funzionalità per la gestione delle vulnerabilità del database e sul rilevamento di attività anomale che potrebbero indicare una minaccia per il database nel database SQL di Azure, in Istanza gestita di Azure SQL o in una sinapsi di Azure.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
ms.author: memildin
manager: rkarlin
author: memildin
ms.date: 02/02/2021
ms.openlocfilehash: 48df96373554f6e474c3835bf81e38a9aea5450c
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99508812"
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

È possibile accedere ad Azure Defender tramite il [portale di Azure](https://portal.azure.com). Abilitare Azure Defender passando al **Centro sicurezza** sotto l'intestazione di **sicurezza** per il server o l'istanza gestita.

> [!NOTE]
> Un account di archiviazione viene creato e configurato automaticamente per archiviare i risultati dell'analisi della **valutazione della vulnerabilità** . Se Azure Defender è già stato abilitato per un altro server nello stesso gruppo di risorse e nella stessa area, viene usato l'account di archiviazione esistente.
>
> Il costo di Azure Defender è allineato con i prezzi del livello standard del Centro sicurezza di Azure per nodo, dove un nodo è l'intero server o istanza gestita. Si paga una sola volta per proteggere tutti i database nel server o nell'istanza gestita con Azure Defender. È possibile provare Azure Defender inizialmente con una versione di valutazione gratuita.

:::image type="content" source="media/azure-defender-for-sql/enable-azure-defender.png" alt-text="Abilitare Azure Defender per SQL dall'interno dei database SQL di Azure":::

## <a name="track-vulnerabilities-and-investigate-threat-alerts"></a>Tenere traccia delle vulnerabilità ed esaminare gli avvisi per le minacce

Fare clic sulla scheda **Valutazione della vulnerabilità** per visualizzare e gestire le analisi e i report sulle vulnerabilità e tenere traccia del livello di sicurezza. Se sono stati ricevuti gli avvisi di sicurezza, fare clic sulla scheda **Advanced Threat Protection** per visualizzare i dettagli degli avvisi e per visualizzare un report consolidato su tutti gli avvisi nella sottoscrizione di Azure tramite la pagina avvisi di sicurezza del Centro sicurezza di Azure.

## <a name="manage-azure-defender-settings"></a>Gestire le impostazioni di Azure Defender

Per visualizzare e gestire le impostazioni di Azure Defender:

1. Dall'area **sicurezza** del server o dell'istanza gestita, selezionare **Centro sicurezza**.

    In questa pagina verrà visualizzato lo stato di Azure Defender per SQL:

    :::image type="content" source="media/azure-defender-for-sql/status-of-defender-for-sql.png" alt-text="Verifica dello stato di Azure Defender per SQL all'interno dei database SQL di Azure":::

1. Se Azure Defender per SQL è abilitato, verrà visualizzato un collegamento **Configura** , come illustrato nell'immagine precedente. Per modificare le impostazioni di Azure Defender per SQL, selezionare **Configura**.

    :::image type="content" source="media/azure-defender-for-sql/security-server-settings.png" alt-text="impostazioni del server di sicurezza":::

1. Apportare le modifiche necessarie e selezionare **Salva**.


## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Valutazione della vulnerabilità](sql-vulnerability-assessment.md)
- Altre informazioni su [Advanced Threat Protection](threat-detection-configure.md)
- Altre informazioni sul [Centro sicurezza di Azure](../../security-center/security-center-introduction.md)