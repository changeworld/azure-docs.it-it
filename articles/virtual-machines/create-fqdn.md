---
title: Creare il nome di dominio completo per una macchina virtuale nel portale di Azure
description: Informazioni su come creare un nome di dominio completo (FQDN) per una macchina virtuale nell'portale di Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/01/2021
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 42c53e72de7f6b2943086544f607042957f4b849
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220080"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Creare un nome di dominio completo nel portale di Azure per una macchina virtuale Linux

Quando si crea una macchina virtuale nel [portale di Azure](https://portal.azure.com), il portale crea automaticamente una risorsa IP pubblica per la macchina virtuale. Usare questo indirizzo IP per accedere in remoto alla VM. Anche se il portale non crea un [nome di dominio completo](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), o FQDN (Fully Qualified Domain Name), è possibile aggiungerne uno dopo aver creato la VM. Questo articolo illustra i passaggi per creare un nome DNS o un FQDN. 

## <a name="create-a-fqdn"></a>Creare un nome di dominio completo
In questo articolo si presuppone che sia già stata creata una VM. Se necessario, è possibile creare una VM [Linux](./linux/quick-create-portal.md) o [Windows](./windows/quick-create-portal.md) nel portale. Dopo che la macchina virtuale è operativa, seguire questi passaggi:


1. Selezionare la macchina virtuale nel portale. 
1. Nel menu a sinistra selezionare **Proprietà** .
1. Nell' **etichetta nome ADDRESS\DNS IP pubblico** selezionare l'indirizzo IP.
2. In **etichetta del nome del DNA** immettere il prefisso che si vuole usare.
3. Fare clic su **Salva** nella parte superiore della pagina.
4. Selezionare **Panoramica** nel menu a sinistra per tornare al pannello panoramica della macchina virtuale.
5. Verificare che il **nome DNS** venga visualizzato correttamente. 

## <a name="next-steps"></a>Passaggi successivi

È anche possibile gestire DNS con le [zone DNS di Azure](../dns/dns-getstarted-portal.md).

