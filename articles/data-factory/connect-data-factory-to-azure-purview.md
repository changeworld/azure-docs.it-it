---
title: Connettere una data factory ad Azure Purview
description: Informazioni su come connettere una Data Factory ad Azure
services: data-factory
ms.author: lle
author: lrtoyou1223
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 12/3/2020
ms.openlocfilehash: 7dc05c88416bb2a23221029bc04c506271a86652
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98108348"
---
# <a name="connect-data-factory-to-azure-purview-preview"></a>Connetti Data Factory ad Azure (anteprima)
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In questo articolo viene illustrato come connettere data factory ad Azure e come segnalare la derivazione dei dati delle attività di ADF copia dati, flusso di dati ed esecuzione pacchetto SSIS.

## <a name="connect-data-factory-to-azure-purview"></a>Connetti data factory ad Azure competenza
Azure competenza è un nuovo servizio cloud per l'uso da parte degli utenti di dati per la gestione centralizzata della governance dei dati nei rispettivi ambienti cloud e locali. È possibile connettere la data factory ad Azure e la connessione consente di sfruttare le competenze di Azure per l'acquisizione dei dati di derivazione di copia, flusso di dati ed esecuzione di un pacchetto SSIS. Per informazioni su come registrare data factory in Azure, vedere [come connettersi Azure Data Factory e Azure](https://docs.microsoft.com/azure/purview/how-to-link-azure-data-factory). 

## <a name="report-lineage-data-to-azure-purview"></a>Segnala i dati di derivazione ad Azure competenza
Quando i clienti eseguono la copia, il flusso di dati o l'attività Esegui pacchetto SSIS in Azure Data Factory, i clienti possono ottenere la relazione di dipendenza e ottenere una panoramica di alto livello dell'intero processo del flusso di lavoro tra origini dati e destinazione.
Per informazioni su come raccogliere la derivazione da Azure Data Factory, vedere [Data Factory](https://docs.microsoft.com/azure/purview/how-to-link-azure-data-factory#supported-azure-data-factory-activities)derivazione.

## <a name="next-steps"></a>Passaggi successivi
[Guida dell'utente di derivazione del catalogo](https://docs.microsoft.com/azure/purview/catalog-lineage-user-guide)

[Esercitazione: effettuare il push dei dati di derivazione Data Factory in Azure](turorial-push-lineage-to-purview.md)
