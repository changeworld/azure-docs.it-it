---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 02/10/2020
ms.author: cynthn
ms.openlocfilehash: 61af1f1a97e0ffad0332a34034f5dd1d007d1a5c
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98901012"
---
| Risorsa | Limite |
| --- | --- |
| VM per [sottoscrizione](https://azure.microsoft.com/pricing/) |25.000<sup>1</sup> per area. |
| Numero totale di core della VM per ogni [sottoscrizione](https://azure.microsoft.com/pricing/) |20<sup>1</sup> per area. Per incrementare il limite, contattare il supporto tecnico. |
| Numero totale di core della VM Spot di Azure per [sottoscrizione](https://azure.microsoft.com/pricing/) |20<sup>1</sup> per area. Per incrementare il limite, contattare il supporto tecnico. |
| Core di VM per serie, ad esempio Dv2 e F, per ogni [sottoscrizione](https://azure.microsoft.com/pricing/) |20<sup>1</sup> per area. Per incrementare il limite, contattare il supporto tecnico. |
| [Set di disponibilità](../articles/virtual-machines/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) per sottoscrizione |2\.500 per area. |
| Macchine virtuali per set di disponibilità | 200 |
| [Gruppi di posizionamento di prossimità](../articles/virtual-machines/windows/proximity-placement-groups-portal.md) per [gruppo di risorse](../articles/azure-resource-manager/management/overview.md#resource-groups) | 800 | 
| Certificati per set di disponibilità | 199<sup>2</sup> |
| Certificati per sottoscrizione |Illimitati<sup>3</sup> |

<sup>1</sup>I limiti predefiniti variano in base al tipo di categoria di offerta, ad esempio versione di valutazione gratuita e con pagamento in base al consumo, e in base alla serie, ad esempio Dv2, F e G. Ad esempio, il valore predefinito per le sottoscrizioni con contratto Enterprise è 350.

<sup>2</sup> Vengono inserite come certificati anche proprietà come le chiavi pubbliche SSH, che vengono conteggiate ai fini di questo limite. Per ignorare questo limite, usare l'[estensione di Azure Key Vault per Windows](../articles/virtual-machines/extensions/key-vault-windows.md) o l'[estensione di Azure Key Vault per Linux](../articles/virtual-machines/extensions/key-vault-linux.md) per installare i certificati.

<sup>3</sup> Con Azure Resource Manager i certificati vengono archiviati in Azure Key Vault. Il numero di certificati per una sottoscrizione è illimitato. Esiste un limite di 1 MB di certificati per ogni distribuzione, costituito da una singola macchina virtuale o un set di disponibilità.



> [!NOTE]
> I core delle macchine virtuali hanno un limite totale per l'area. Hanno anche un limite per le serie locali per dimensione, ad esempio Dv2 e F. Questi limiti vengono applicati separatamente. Si consideri ad esempio una sottoscrizione con limite di core di VM totale per gli Stati Uniti orientali pari a 30, un limite di core di serie A pari a 30 e un limite di core di serie D pari a 30. Questa sottoscrizione può distribuire 30 VM A1 o 30 VM D1 oppure una combinazione delle due che non superi un totale di 30 core. Un esempio di combinazione è rappresentato da 10 VM A1 e 20 VM D1.  
> <!-- -->
>