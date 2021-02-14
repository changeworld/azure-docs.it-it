---
title: Limitazioni dell'iperscalabilità di PostgreSQL abilitata per Azure Arc
description: Limitazioni dell'iperscalabilità di PostgreSQL abilitata per Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: b1a56c8acf1789690c01f1c16b7c37a237720e39
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417810"
---
# <a name="limitations-of-azure-arc-enabled-postgresql-hyperscale"></a>Limitazioni dell'iperscalabilità di PostgreSQL abilitata per Azure Arc

Questo articolo descrive le limitazioni dell'iperscalabilità di PostgreSQL abilitata per Azure Arc. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="backup-and-restore"></a>Backup e ripristino

- Il ripristino temporizzato (ad esempio il ripristino di una data e un'ora specifiche) allo stesso gruppo di server non è supportato. Quando si esegue un ripristino temporizzato, è necessario eseguire il ripristino in un gruppo di server diverso da quello distribuito prima del ripristino. Dopo il ripristino al nuovo gruppo di server, è possibile eliminare il gruppo di server di origine.
- Il ripristino dell'intero contenuto di un backup, invece del ripristino fino a un momento specifico, allo stesso gruppo di server è supportato per PostgreSQL versione 12. Non è supportata per PostgreSQL versione 11 a causa di una limitazione del motore PostgreSQL con sequenze temporali. Per ripristinare l'intero contenuto di un backup per un gruppo di server PostgreSQL della versione 11, è necessario ripristinarlo in un altro gruppo di server.


## <a name="databases"></a>Database

L'hosting di più di un database in un gruppo di server non è supportato.


## <a name="security"></a>Sicurezza

La gestione di utenti e ruoli non è supportata. Per il momento, continuare a usare l'utente standard postgres.

## <a name="roles-and-responsibilities"></a>Ruoli e responsabilità

I ruoli e le responsabilità tra Microsoft e i relativi clienti si differenziano tra i servizi PaaS di Azure (piattaforma distribuita come servizio) e Azure Hybrid, ad esempio Azure Arc abilitata per l'iperscalabilità PostgreSQL. 

### <a name="frequently-asked-questions"></a>Domande frequenti

Nella tabella seguente sono riepilogate le risposte alle domande frequenti relative ai ruoli e alle responsabilità del supporto tecnico.

| Domanda                      | Piattaforma distribuita come servizio (PaaS) di Azure | Servizi ibridi di Azure Arc |
|:----------------------------------|:------------------------------------:|:---------------------------:|
| Chi fornisce l'infrastruttura?  | Microsoft                          | Customer                  |
| Chi fornisce il software? *       | Microsoft                          | Microsoft                 |
| Chi esegue le operazioni? | Microsoft                          | Customer                  |
| Microsoft fornisce contratti di contratto?      | Sì                                | No                        |
| Chi è responsabile dei contratti di contratto? | Microsoft                          | Customer                  |

\* Servizi di Azure

__Perché Microsoft non fornisce contratti di servizio per i servizi ibridi di Azure Arc?__ Poiché Microsoft non è il proprietario dell'infrastruttura e non lo utilizza. I clienti lo fanno.

## <a name="next-steps"></a>Passaggi successivi

- **Provalo.** Inizia rapidamente a usare [Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) in Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) o in una VM di Azure. 

- **Crearne di personalizzati.** Seguire questa procedura per creare nel cluster Kubernetes: 
   1. [Installare gli strumenti client](install-client-tools.md)
   2. [Creare il controller di dati di Azure Arc](create-data-controller.md)
   3. [Creare un gruppo di server di scalabilità di database di Azure per PostgreSQL in Azure Arc](create-postgresql-hyperscale-server-group.md) 

- **Learn**
   - [Scopri di più su Azure Arc Enabled Data Services](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
   - [Scopri di più su Azure Arc](https://aka.ms/azurearc)
