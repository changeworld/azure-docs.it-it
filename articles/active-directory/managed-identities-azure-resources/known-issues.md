---
title: Problemi noti relativi alle identità gestite-Azure Active Directory
description: Problemi noti relativi alle identità gestite per le risorse di Azure.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 04/08/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 8d2b6323a15595e57e7e89c6a83f9f718422e1d7
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226923"
---
# <a name="known-issues-with-managed-identities"></a>Problemi noti relativi alle identità gestite

In questo articolo vengono illustrati alcuni problemi relativi alle identità gestite e come risolverli. Le domande frequenti sulle identità gestite sono descritte nell'articolo [domande frequenti](managed-identities-faq.md) .
## <a name="vm-fails-to-start-after-being-moved"></a>Non è possibile avviare la macchina virtuale dopo lo spostamento 

Se si sposta una macchina virtuale in stato di esecuzione da un gruppo di risorse o una sottoscrizione, l'esecuzione continua durante lo spostamento. Tuttavia, dopo lo spostamento, se la macchina virtuale viene arrestata e riavviata, non si avvierà. Questo problema si verifica perché la macchina virtuale non aggiorna il riferimento alle identità gestite per le risorse di Azure e continua a puntare a questa nel gruppo di risorse precedente.

**Soluzione alternativa** 
 
Attivare un aggiornamento nella macchina virtuale così da poter ottenere i valori corretti per le identità gestite per le risorse di Azure. È possibile apportare una modifica alla proprietà della macchina virtuale per aggiornare il riferimento alle identità gestite per le risorse di Azure. Ad esempio, è possibile impostare un nuovo valore di tag nella macchina virtuale con il comando seguente:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
Questo comando imposta un nuovo tag "fixVM" con valore 1 nella macchina virtuale. 
 
Impostando questa proprietà, la macchina virtuale si aggiorna inserendo l'URI risorsa corretto delle identità gestite per le risorse di Azure. La macchina virtuale dovrebbe a questo punto avviarsi. 
 
Dopo aver avviato la macchina virtuale, il tag può essere rimosso tramite il seguente comando:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

## <a name="transferring-a-subscription-between-azure-ad-directories"></a>Trasferimento di una sottoscrizione tra directory di Azure AD

Le identità gestite non vengono aggiornate quando una sottoscrizione viene spostata/trasferita a un'altra directory. Di conseguenza, tutte le identità gestite assegnate dal sistema o assegnate dall'utente saranno interrotte. 

Soluzione alternativa per le identità gestite in una sottoscrizione che è stata spostata in un'altra directory:

 - Per le identità gestite assegnate dal sistema: disabilitare e abilitare di nuovo. 
 - Per le identità gestite assegnate dall'utente: eliminare, ricreare e collegarle nuovamente alle risorse necessarie, ad esempio macchine virtuali.

Per altre informazioni, vedere [Trasferire una sottoscrizione di Azure in una directory di Azure AD diversa](../../role-based-access-control/transfer-subscription.md).


## <a name="next-steps"></a>Passaggi successivi

È possibile esaminare l'articolo elencando i [servizi che supportano le identità gestite](services-support-managed-identities.md) e le [domande frequenti](managed-identities-faq.md)
