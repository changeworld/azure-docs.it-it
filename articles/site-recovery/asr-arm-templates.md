---
title: Modelli di Azure Resource Manager
description: Azure Resource Manager modelli per l'utilizzo delle funzionalità di Azure Site Recovery.
services: site-recovery
author: rishjai
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 02/04/2021
ms.author: rishjai
ms.openlocfilehash: efd5c1ab3e23348373be76c79d1b9689bc5f4941
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101720529"
---
# <a name="azure-resource-manager-templates-for-azure-site-recovery"></a>Modelli di Azure Resource Manager per Azure Site Recovery

La tabella seguente include i collegamenti ai modelli di Azure Resource Manager per l'uso delle funzionalità di Azure Site Recovery.

| Modello | Descrizione |
|---|---|
|**Da Azure ad Azure** | |
| [Creare un insieme di credenziali di Servizi di ripristino](./quickstart-create-vault-template.md)| Creare un insieme di credenziali dei servizi di ripristino. L'insieme di credenziali può essere usato per Backup di Azure e Azure Site Recovery. |
| [Abilitare la replica per le macchine virtuali di Azure](https://aka.ms/asr-arm-enable-replication) | Abilitare la replica per le VM di Azure usando l'insieme di credenziali esistente e le impostazioni di destinazione personalizzate.|
| [Attivare il failover e la riprotezione](https://aka.ms/asr-arm-failover-reprotect) | Attivare un'operazione di failover e riprotezione per un set di macchine virtuali di Azure. |
| [Eseguire un flusso di ripristino di emergenza end-to-end per macchine virtuali di Azure](https://aka.ms/asr-arm-e2e-flow) | Avviare un flusso di ripristino di emergenza end-to-end completo (abilitare la replica e il failover e riproteggere + failback e riprotezione) per le macchine virtuali di Azure, denominate anche flusso 540 °.|
|   |   |