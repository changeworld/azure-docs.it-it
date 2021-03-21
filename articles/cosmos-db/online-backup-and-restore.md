---
title: Backup online e ripristino dei dati su richiesta in Azure Cosmos DB.
description: Questo articolo descrive come funziona il backup automatico, il ripristino dei dati su richiesta. Viene inoltre illustrata la differenza tra le modalità di backup continuo e periodico.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/13/2020
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 2629e9c6e048620d9490a1e091a16c138fd1e615
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99525433"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Backup online e ripristino dei dati su richiesta in Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB esegue automaticamente il backup dei dati a intervalli regolari. I backup automatici vengono eseguiti senza impatto sulle prestazioni o sulla disponibilità delle operazioni del database. Tutti i backup vengono archiviati separatamente in un servizio di archiviazione. I backup automatici sono utili in caso di eliminazione accidentale o aggiornamento dell'account, di database o di contenitori Azure Cosmos, quando in seguito occorre ripristinare i dati. Sono disponibili due modalità di backup:

* **Modalità di backup periodico** : questa modalità è la modalità di backup predefinita per tutti gli account esistenti. In questa modalità, il backup viene effettuato a intervalli periodici e i dati vengono ripristinati creando una richiesta con il team di supporto. In questa modalità è possibile configurare un intervallo di backup e la conservazione per l'account. Il periodo di conservazione massimo si estende a un mese. L'intervallo di backup minimo può essere di un'ora.  Per altre informazioni, vedere l'articolo relativo alla [modalità di backup periodico](configure-periodic-backup-restore.md) .

* **Modalità di backup continuo** (attualmente disponibile in anteprima pubblica): è possibile scegliere questa modalità durante la creazione dell'account Azure Cosmos DB. Questa modalità consente di eseguire il ripristino in qualsiasi momento negli ultimi 30 giorni. Per altre informazioni, vedere [Introduzione alla modalità di backup continuo](continuous-backup-restore-introduction.md), configurare il backup continuo [con portale di Azure](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), l' [interfaccia](continuous-backup-restore-command-line.md)della riga di comando e [Gestione risorse](continuous-backup-restore-template.md) articoli.

  > [!NOTE]
  > Se si configura un nuovo account con backup continuo, è possibile eseguire il ripristino self-service tramite portale di Azure, PowerShell o l'interfaccia della riga di comando. Se l'account è configurato in modalità continua, non è possibile tornare alla modalità periodica. Gli account attualmente esistenti con modalità di backup periodico non possono essere modificati in modalità continua.  

## <a name="next-steps"></a>Passaggi successivi

Successivamente, è possibile ottenere informazioni su come configurare e gestire le modalità di backup periodici e continui per l'account:

* [Configurare e gestire i criteri di backup periodici](configure-periodic-backup-restore.md) .
* Che cos'è la modalità di [backup continuo](continuous-backup-restore-introduction.md) ?
* Configurare e gestire il backup continuo con [portale di Azure](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [CLI](continuous-backup-restore-command-line.md)o [Azure Resource Manager](continuous-backup-restore-template.md).
* [Gestire le autorizzazioni](continuous-backup-restore-permissions.md) necessarie per ripristinare i dati con la modalità di backup continuo.
