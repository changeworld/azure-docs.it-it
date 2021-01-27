---
title: includere file
description: includere file
author: danielstocker
ms.service: azure-quantum
ms.topic: include
ms.date: 01/08/2021
ms.author: dasto
ms.openlocfilehash: c312ee63f9f00e4eef726924fc01f2862ba2884f
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920470"
---
### <a name="provider-limits--quota"></a>Limiti del provider & quota

Il servizio Quantum di Azure supporta i provider di Servizi First e di terze parti. I provider di terze parti sono proprietari dei limiti e delle quote. Gli utenti possono visualizzare le offerte e i limiti nella portale di Azure durante la configurazione di provider di terze parti nel pannello del provider. 

È possibile trovare i limiti di quota pubblicati per il provider di soluzioni di ottimizzazione per la prima parte di Microsoft. 

#### <a name="learn--develop-sku"></a>Informazioni & sviluppare SKU

| Risorsa | Limite |
| --- | --- |
| Processi simultanei basati su CPU | fino a 5 processi simultanei |
| Processi simultanei basati su FPGA | fino a 2 processi simultanei |
| Ore di Risolutore basate su CPU | 20 ore al mese  |
| Ore di Risolutore basate su FPGA | 1 ora al mese  |

Se si usa lo SKU Learn & develop, non è **possibile** richiedere un aumento dei limiti di quota. In alternativa, è consigliabile passare alle prestazioni su SKU di scala.

#### <a name="performance-at-scale-sku"></a>Prestazioni a livello di SKU

| Risorsa | Limite |
| --- | --- |
| Processi simultanei basati su CPU | fino a 100 processi simultanei |
| Processi simultanei basati su FPGA | fino a 10 processi simultanei |
| Ore del Risolutore | 50.000 ore al mese  |

Se è necessario richiedere un aumento del limite, contattare il supporto tecnico di Azure. 

Per altre informazioni, vedere la [pagina dei prezzi di Quantum di Azure](https://aka.ms/AQ/Pricing).
Per informazioni sulle offerte di terze parti, vedere la pagina del provider pertinente nella portale di Azure.
