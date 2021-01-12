---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 02/10/2020
ms.author: cynthn
ms.openlocfilehash: cd3ff3fce80e66d7cd61636b4416cb2fc28f5e77
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97956525"
---
| Risorsa | Limite |
| --- | --- |
| VM per [sottoscrizione](https://azure.microsoft.com/pricing/) |25.000<sup>1</sup> per area. |
| Numero totale di core della VM per ogni [sottoscrizione](https://azure.microsoft.com/pricing/) |20<sup>1</sup> per area. Per incrementare il limite, contattare il supporto tecnico. |
| Numero totale di core della VM Spot di Azure per [sottoscrizione](https://azure.microsoft.com/pricing/) |20<sup>1</sup> per area. Per incrementare il limite, contattare il supporto tecnico. |
| Core di VM per serie, ad esempio Dv2 e F, per ogni [sottoscrizione](https://azure.microsoft.com/pricing/) |20<sup>1</sup> per area. Per incrementare il limite, contattare il supporto tecnico. |
| [Set di disponibilità](../articles/virtual-machines/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) per sottoscrizione |2\.500 per area. |
| Macchine virtuali per set di disponibilità | 200 |
| [Gruppi di posizionamento di prossimità](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups-portal) per [gruppo di risorse](../articles/azure-resource-manager/management/overview.md#resource-groups) | 800 | 
| Certificati per set di disponibilità | 199<sup>2</sup> |
| Certificati per sottoscrizione |Illimitati<sup>3</sup> |

<sup>1</sup>I limiti predefiniti variano in base al tipo di categoria di offerta, ad esempio versione di valutazione gratuita e con pagamento in base al consumo, e in base alla serie, ad esempio Dv2, F e G. Ad esempio, il valore predefinito per le sottoscrizioni con contratto Enterprise è 350.

<sup>2</sup> Vengono inserite come certificati anche proprietà come le chiavi pubbliche SSH, che vengono conteggiate ai fini di questo limite. Per ignorare questo limite, usare l'[estensione di Azure Key Vault per Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/key-vault-windows) o l'[estensione di Azure Key Vault per Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/key-vault-linux) per installare i certificati.

<sup>3</sup> Con Azure Resource Manager i certificati vengono archiviati in Azure Key Vault. Il numero di certificati per una sottoscrizione è illimitato. Esiste un limite di 1 MB di certificati per ogni distribuzione, costituito da una singola macchina virtuale o un set di disponibilità.



> [!NOTE]
> I core delle macchine virtuali hanno un limite totale per l'area. Hanno anche un limite per le serie locali per dimensione, ad esempio Dv2 e F. Questi limiti vengono applicati separatamente. Si consideri ad esempio una sottoscrizione con limite di core di VM totale per gli Stati Uniti orientali pari a 30, un limite di core di serie A pari a 30 e un limite di core di serie D pari a 30. Questa sottoscrizione può distribuire 30 VM A1 o 30 VM D1 oppure una combinazione delle due che non superi un totale di 30 core. Un esempio di combinazione è rappresentato da 10 VM A1 e 20 VM D1.  
> <!-- -->
>
