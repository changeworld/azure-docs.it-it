---
title: includere file
description: includere file
author: danielstocker
ms.service: azure-quantum
ms.topic: include
ms.date: 01/08/2021
ms.author: dasto
ms.openlocfilehash: 2106a48a583f120f8b4dde4eb32a30f1a1b1d85b
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947782"
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

| Risorsa | Limite predefinito | Limite massimo |
| --- | --- | --- |
| Processi simultanei basati su CPU | fino a 100 processi simultanei | uguale al limite predefinito |
| Processi simultanei basati su FPGA | fino a 10 processi simultanei | uguale al limite predefinito |
| Ore del Risolutore | 1.000 ore al mese  | fino a 50.000 ore al mese |

Se è necessario richiedere un aumento del limite, contattare il supporto tecnico di Azure. 

Per altre informazioni, vedere la [pagina dei prezzi di Quantum di Azure](https://aka.ms/AQ/Pricing).
Per informazioni sulle offerte di terze parti, vedere la pagina del provider pertinente nella portale di Azure.
