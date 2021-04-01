---
title: Creare il nome di dominio completo per una macchina virtuale nel portale di Azure
description: Informazioni su come creare un nome di dominio completo (FQDN) per una macchina virtuale nell'portale di Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 1/12/2020
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aadf4000da4ab88a3c3b4dee37d2179eb49d39d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98132065"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Creare un nome di dominio completo nel portale di Azure per una macchina virtuale Linux

Quando si crea una macchina virtuale nel [portale di Azure](https://portal.azure.com), il portale crea automaticamente una risorsa IP pubblica per la macchina virtuale. Usare questo indirizzo IP per accedere in remoto alla VM. Anche se il portale non crea un [nome di dominio completo](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), o FQDN (Fully Qualified Domain Name), è possibile aggiungerne uno dopo aver creato la VM. Questo articolo illustra i passaggi per creare un nome DNS o un FQDN. 

## <a name="create-a-fqdn"></a>Creare un nome di dominio completo
In questo articolo si presuppone che sia già stata creata una VM. Se necessario, è possibile creare una VM [Linux](./linux/quick-create-portal.md) o [Windows](./windows/quick-create-portal.md) nel portale. Dopo che la macchina virtuale è operativa, seguire questi passaggi:


1. Selezionare la macchina virtuale nel portale. 
1. Nel menu a sinistra selezionare **configurazione** .
1. In **etichetta nome DNS** immettere il prefisso che si vuole usare.
1. Fare clic su **Salva** nella parte superiore della pagina.
1. Tornare al pannello panoramica della VM selezionando **Panoramica** nel menu a sinistra. 
1. Verificare che il *nome DNS* venga visualizzato correttamente. 

## <a name="next-steps"></a>Passaggi successivi

È anche possibile gestire DNS con le [zone DNS di Azure](../dns/dns-getstarted-portal.md).

