---
title: Esercitazione-eliminare un cloud privato della soluzione VMware di Azure
description: Informazioni su come eliminare un cloud privato della soluzione VMware di Azure non più necessario.
ms.topic: tutorial
ms.date: 02/09/2021
ms.openlocfilehash: b11b8f902691db4bd71fd3f52aaa67d46efea643
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/10/2021
ms.locfileid: "100101709"
---
# <a name="tutorial-delete-an-azure-vmware-solution-private-cloud"></a>Esercitazione: eliminare un cloud privato della soluzione VMware di Azure

Se si dispone di un cloud privato della soluzione Azure VMware che non è più necessario, è possibile eliminarlo. Il cloud privato include un dominio di rete isolato, uno o più cluster vSphere sottoposti a provisioning in host server dedicati e diverse macchine virtuali (VM). Quando si elimina un cloud privato, vengono eliminate tutte le VM, i relativi dati e i cluster. Gli host dedicati vengono cancellati e restituiti in modo sicuro al pool gratuito. Viene anche eliminato il dominio di rete di cui è stato effettuato il provisioning per il cliente.  

> [!CAUTION]
> L'eliminazione del cloud privato è un'operazione irreversibile. Una volta eliminato il cloud privato, non è possibile recuperare i dati perché questa operazione termina tutti i carichi di lavoro in esecuzione e i componenti ed elimina definitivamente tutti i dati e le impostazioni di configurazione del cloud privato, inclusi gli indirizzi IP pubblici.

## <a name="prerequisites"></a>Prerequisiti

Se le macchine virtuali e i relativi dati sono necessari in un secondo momento, assicurarsi di eseguire il backup dei dati prima di eliminare il cloud privato.  Non è possibile ripristinare le macchine virtuali e i relativi dati.


## <a name="delete-the-private-cloud"></a>Eliminare il cloud privato

1. Accedere alla console soluzioni VMware di Azure nella [portale di Azure](https://portal.azure.com).

2. Selezionare il cloud privato da eliminare.
 
3. Immettere il nome del cloud privato e selezionare **Sì**. 

>[!NOTE]
>Il completamento del processo di eliminazione richiede alcune ore.  
