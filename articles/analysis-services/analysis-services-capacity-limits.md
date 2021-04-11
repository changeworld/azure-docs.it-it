---
title: Limiti di risorse e oggetti di Azure Analysis Services | Microsoft Docs
description: Questo articolo descrive i limiti di risorse e oggetti per un server di Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: beb4ca31b65d5de0b81030cdf3222418cb968060
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105732007"
---
# <a name="analysis-services-resource-and-object-limits"></a>Limiti di risorse e oggetti di Azure Analysis Services

In questo articolo vengono descritti i limiti di risorse e oggetti del modello.

## <a name="tier-limits"></a>Limiti di livello

Per i limiti di QPU e di memoria per i livelli Developer, Basic e Standard, vedere la pagina [Prezzi di Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="object-limits"></a>Limiti di oggetto

Questi limiti sono teorici. Le prestazioni risulteranno inferiori con numeri più bassi.

|Oggetto|Quantità/dimensioni massime|  
|------------|----------------------------|  
|Database in un'istanza|16.000|  
|Numero combinato di tabelle e colonne in un database|16.000|  
|Righe in una tabella|Nessuna limitazione<br /><br /> **Avviso:** Con la restrizione che nessuna colonna singola nella tabella può avere più di 1.999.999.997 valori distinti.|  
|Gerarchie in una tabella|15.999|  
|Livelli in una gerarchia|15.999|  
|Relazioni|8\.000|  
|Colonne chiave in tutte le tabelle|15.999|  
|Misure nelle tabelle|2^31-1 = 2.147.483.647|  
|Celle restituite da una query|2^31-1 = 2.147.483.647|  
|Dimensioni dei record della query di origine|64 K|  
|Lunghezza dei nomi degli oggetti|512 caratteri|  


