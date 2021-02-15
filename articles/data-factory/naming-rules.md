---
title: Regole per la denominazione di entità Azure Data Factory
description: Descrive le regole di denominazione per le entità di Data factory.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: f0d14760ce3e6403c9b6fe8cc7a2100aeb3f39a6
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100372910"
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory - Regole di denominazione

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

La tabella seguente specifica le regole di denominazione per gli elementi di Data factory.

| Nome | Univocità del nome | Controlli di convalida |
|:--- |:--- |:--- |
| Data factory | Univoco in Microsoft Azure. Per i nomi non viene fatta distinzione tra maiuscole e minuscole: `MyDF` e `mydf`, ad esempio, fanno riferimento alla stessa data factory. |<ul><li>Ogni data factory è collegata a una sola sottoscrizione di Azure.</li><li>I nomi degli oggetti devono iniziare con una lettera o un numero e possono contenere solo lettere, numeri e il carattere trattino (-).</li><li>Ogni carattere trattino (-) deve essere immediatamente preceduto e seguito da una lettera o un numero. Nei nomi di contenitori non sono consentiti trattini consecutivi.</li><li>Il nome può contenere da 3 a 63 caratteri.</li></ul> |
| Servizi collegati/set di dati/pipeline/flussi di dati | Univoco all'interno di un data factory. Per i nomi viene fatta distinzione tra maiuscole e minuscole. |<ul><li>I nomi degli oggetti devono iniziare con una lettera.</li><li>I caratteri seguenti non sono consentiti: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":", " \\ "</li><li>I trattini ("-") non sono consentiti nei nomi di servizi collegati, flussi di dati e set di dati.</li></ul>  |
| Integration Runtime |Univoco all'interno di un data factory. Per i nomi viene fatta distinzione tra maiuscole e minuscole. |<ul><li>Il nome del runtime di integrazione può contenere solo lettere, numeri e il carattere trattino (-).</li><li>Il primo e l'ultimo carattere devono essere una lettera o un numero. Ogni carattere trattino (-) deve essere immediatamente preceduto e seguito da una lettera o un numero.</li><li>I trattini consecutivi non sono consentiti nel nome del runtime di integrazione. </li></ul> |
| Trasformazioni del flusso di dati | Univoco all'interno di un flusso di dati. I nomi non fanno distinzione tra maiuscole e minuscole | <ul><li>I nomi di trasformazione del flusso di dati possono contenere solo lettere e numeri</li><li>Il primo carattere deve essere una lettera. </li></ul> |
| Gruppo di risorse |Univoco in Microsoft Azure. Per i nomi viene fatta distinzione tra maiuscole e minuscole. | Per altre informazioni, vedere [Regole di denominazione e restrizioni](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming). |

## <a name="next-steps"></a>Passaggi successivi
Informazioni su come creare data factory seguendo le istruzioni dettagliate riportate nell'articolo [Guida introduttiva: creazione di una data factory](quickstart-create-data-factory-powershell.md). 
