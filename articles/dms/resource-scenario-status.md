---
title: Stato dello scenario di migrazione del database
titleSuffix: Azure Database Migration Service
description: Informazioni sullo stato degli scenari di migrazione supportati da Servizio Migrazione del database di Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 07/08/2020
ms.openlocfilehash: 6c1a0853dc59b2e2ceabfd47d81aac364a2b5716
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589432"
---
# <a name="status-of-migration-scenarios-supported-by-azure-database-migration-service"></a>Stato degli scenari di migrazione supportati da Servizio Migrazione del database di Azure

Servizio Migrazione del database di Azure è progettato per supportare diversi scenari di migrazione (coppie di origine/destinazione) per migrazioni offline (una sola volta) e online (sincronizzazione continua). La copertura dello scenario fornita Servizio Migrazione del database di Azure viene estesa nel tempo. Nuovi scenari vengono aggiunti regolarmente. Questo articolo identifica gli scenari di migrazione attualmente supportati da Servizio Migrazione del database di Azure e lo stato (anteprima privata, anteprima pubblica o disponibilità generale) per ogni scenario.

## <a name="offline-versus-online-migrations"></a>Migrazioni offline e online

Con Servizio Migrazione del database di Azure, è possibile eseguire una migrazione offline o online. Con le migrazioni *offline*, il tempo di inattività delle applicazioni inizia quando inizia la migrazione. Per limitare il tempo di inattività al tempo necessario per eseguire il cut over nel nuovo ambiente al termine della migrazione, usare una *migrazione online.* È consigliabile testare una migrazione offline per determinare se il tempo di inattività è accettabile. In caso contrario, eseguire una migrazione online.

## <a name="migration-scenario-support"></a>Supporto degli scenari di migrazione

Le tabelle seguenti illustrano gli scenari di migrazione supportati quando si usa Servizio Migrazione del database di Azure.

> [!NOTE]
> Se uno scenario elencato come supportato di seguito non viene visualizzato nell'interfaccia utente, contattare l'alias [Ask Azure Database Migrations](mailto:AskAzureDatabaseMigrations@service.microsoft.com) per altre informazioni.

> [!IMPORTANT]
> Per visualizzare tutti gli scenari attualmente supportati da Servizio Migrazione del database di Azure in anteprima privata, vedere il sito di anteprima del Servizio [Gestione dei dispositivi.](https://aka.ms/dms-preview)

### <a name="offline-one-time-migration-support"></a>Supporto della migrazione offline (unica)

La tabella seguente mostra il supporto del Servizio Migrazione del database di Azure per le migrazioni offline.

| Destinazione  | Source (Sorgente) | Supporto tecnico | Stato |
| ------------- | ------------- |:-------------:|:-------------:|
| **Database SQL di Azure** | SQL Server | ✔ | GA |
|   | Servizi Desktop remoto SQL | X |  |
|   | Oracle | X |  |
| **Istanza gestita del database SQL di Azure** | SQL Server | ✔ | GA |
|   | Servizi Desktop remoto SQL | X |  |
|   | Oracle | X |   |
| **VM di Azure SQL** | SQL Server | ✔ | GA |
|   | Oracle | X |   |
| **Azure Cosmos DB** | MongoDB | ✔ | GA |
| **Database di Azure per MySQL** | MySQL | X |   |
|   | Servizi Desktop remoto MySQL | X |   |
| **Database di Azure per PostgreSQL - Server singolo** | PostgreSQL | X |
|  | Servizi Desktop remoto PostgreSQL | X |   |
| **Database di Azure per PostgreSQL - Hyperscale (Citus)** | PostgreSQL | X |
|  | Servizi Desktop remoto PostgreSQL | X |   |

### <a name="online-continuous-sync-migration-support"></a>Supporto della migrazione online (sincronizzazione continua)

La tabella seguente mostra il supporto del Servizio Migrazione del database di Azure per le migrazioni online.

| Destinazione  | Source (Sorgente) | Supporto tecnico | Stato |
| ------------- | ------------- |:-------------:|:-------------:|
| **Database SQL di Azure** | SQL Server | X |  |
|   | Servizi Desktop remoto SQL | X |  |
|   | Oracle | X |  |
| **Istanza gestita del database SQL di Azure** | SQL Server | ✔ | GA |
|   | Servizi Desktop remoto SQL | X |  |
|   | Oracle | X |  |
| **VM di Azure SQL** | SQL Server | X |   |
|   | Oracle  | X |  |
| **Azure Cosmos DB** | MongoDB | ✔ | GA |
| **Database di Azure per MySQL** | MySQL | ✔ | GA |
|   | Servizi Desktop remoto MySQL | ✔ | GA |
| **Database di Azure per PostgreSQL - Server singolo** | PostgreSQL | ✔ | GA |
|   | Database di Azure per PostgreSQL - Server singolo | ✔ | GA |
|   | Servizi Desktop remoto PostgreSQL | ✔ | GA |
|   | Oracle | ✔ | Anteprima pubblica (deprecata dopo il 1° maggio 2021) |
| **Database di Azure per PostgreSQL - Hyperscale (Citus)** | PostgreSQL | ✔ | GA |
|   | Servizi Desktop remoto PostgreSQL | ✔ | GA |

> [!IMPORTANT]
> Lo scenario di migrazione "Da Oracle a Database di Azure per PostgreSQL" (attualmente in anteprima) non sarà più disponibile dopo il 1° maggio 2021. Microsoft continuerà a fornire supporto tramite strumenti alternativi (ad esempio Ora2pg) e offrirà la migliore esperienza di migrazione per le migrazioni da Oracle a PostgreSQL. Per le procedure consigliate per la migrazione, vedere Guida alla migrazione da Oracle a Database di [Azure per PostgreSQL.](https://aka.ms/OracletoPGguide)


## <a name="next-steps"></a>Passaggi successivi

Per una panoramica della Servizio Migrazione del database di Azure e della disponibilità a livello di regione, vedere l'articolo Che [cos'è Servizio Migrazione del database di Azure](dms-overview.md).
