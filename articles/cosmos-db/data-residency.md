---
title: Come soddisfare i requisiti di residenza dei dati in Azure Cosmos DB
description: informazioni su come soddisfare i requisiti di residenza dei dati in Azure Cosmos DB perché i dati e i backup rimangano in una singola area.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: c5f8a4361774368e3934d1e2b16c8311876f5caa
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491866"
---
# <a name="how-to-meet-data-residency-requirements-in-azure-cosmos-db"></a>Come soddisfare i requisiti di residenza dei dati in Azure Cosmos DB

In Azure Cosmos DB, è possibile configurare i dati e i backup affinché rimangano in una singola area per soddisfare i[ requisiti di residenza.](https://azure.microsoft.com/en-us/global-infrastructure/data-residency/)

## <a name="residency-requirements-for-data"></a>Requisiti di residenza per i dati

In Azure Cosmos DB, è necessario configurare in modo esplicito la replica dei dati tra aree. Informazioni su come configurare la replica geografica usando [portale di Azure](how-to-manage-database-account.md#addremove-regions-from-your-database-account), l'interfaccia della riga di comando di [Azure](scripts/cli/common/regions.md). Per soddisfare i requisiti di residenza dei dati, è possibile creare criteri di Azure che consentono a determinate aree di impedire la replica dei dati in aree indesiderate.

## <a name="residency-requirements-for-backups"></a>Requisiti di residenza per i backup

**Backup in modalità continua**: questi backup sono residenti per impostazione predefinita perché sono archiviati nell'archiviazione con ridondanza locale o con ridondanza della zona. Per altre informazioni, vedere l'articolo [backup continuo](continuous-backup-restore-portal.md) .

**Backup in modalità periodica**: per impostazione predefinita, i backup degli account in modalità periodica verranno archiviati nell'archiviazione con ridondanza geografica. Per le modalità di backup periodici, è possibile configurare la ridondanza dei dati a livello di account. Sono disponibili tre opzioni di ridondanza per l'archiviazione di backup. Si tratta di ridondanza locale, ridondanza della zona o ridondanza geografica. Per altre informazioni, vedere come [configurare la ridondanza dei backup](configure-periodic-backup-restore.md#configure-backup-interval-retention) tramite il portale.

## <a name="use-azure-policy-to-enforce-the-residency-requirements"></a>Usare i criteri di Azure per applicare i requisiti di residenza

Se i requisiti di residenza dei dati richiedono la conservazione di tutti i dati in una singola area di Azure, è possibile applicare backup con ridondanza della zona o localmente ridondanti per l'account usando criteri di Azure.  È anche possibile applicare un criterio per cui gli account Azure Cosmos DB non vengono replicati geograficamente in altre aree.

Criteri di Azure è un servizio che è possibile usare per creare, assegnare e gestire criteri che applicano regole alle risorse di Azure. Criteri di Azure consente di garantire che queste risorse siano conformi agli standard aziendali e ai contratti di servizio. Per altre informazioni, vedere come usare i [criteri di Azure](policy.md) per implementare la governance e i controlli per le risorse Azure Cosmos DB

## <a name="next-steps"></a>Passaggi successivi

* Configurare e gestire il backup periodico con [portale di Azure](configure-periodic-backup-restore.md)
* Configurare e gestire il backup continuo con [portale di Azure](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [CLI](continuous-backup-restore-command-line.md)o [Azure Resource Manager](continuous-backup-restore-template.md).
