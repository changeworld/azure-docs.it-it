---
title: Regole per la denominazione di entità Azure Data Factory
description: Descrive le regole di denominazione per le entità di Data factory.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: a1a0622c0736bad5f6c205fab01f4405577ed67a
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104783354"
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
| Parametri della pipeline & variabile  |Univoco all'interno della pipeline. Per i nomi viene fatta distinzione tra maiuscole e minuscole. | <ul><li>Il controllo della convalida sui nomi dei parametri e delle variabili è limitato all'univocità a causa del motivo della compatibilità con le versioni precedenti.</li><li>Quando si usano parametri o variabili per fare riferimento a nomi di entità, ad esempio il servizio collegato, vengono applicate le regole di denominazione delle entità.</li><li>È consigliabile seguire le regole di denominazione della trasformazione flusso di dati per assegnare un nome ai parametri e alle variabili della pipeline.</li></ul> |

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come creare data factory seguendo le istruzioni dettagliate riportate nell'articolo [Guida introduttiva: creazione di una data factory](quickstart-create-data-factory-powershell.md). 
