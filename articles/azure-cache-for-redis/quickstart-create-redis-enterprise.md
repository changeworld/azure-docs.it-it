---
title: 'Guida introduttiva: creare una cache Redis Enterprise'
description: Questa Guida introduttiva illustra come creare un'istanza di cache di Azure per Redis nei livelli Enterprise
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: mvc
ms.topic: quickstart
ms.date: 02/08/2021
ms.openlocfilehash: 8dbde1f74c700e772823d61b3e35a464f9bbc17e
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102120964"
---
# <a name="quickstart-create-a-redis-enterprise-cache-preview"></a>Guida introduttiva: creare una cache di redis Enterprise (anteprima)

I livelli Enterprise della cache di Azure per Redis offrono una soluzione [Redis Enterprise](https://redislabs.com/redis-enterprise/) completamente integrata e gestita in Azure. Sono attualmente disponibili in anteprima. L'anteprima include due nuovi livelli:
* Enterprise, che usa memoria volatile (DRAM) in una macchina virtuale per archiviare i dati
* Enterprise Flash, che usa memoria volatile e non volatile (NVMe o SSD) per archiviare i dati.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, è necessaria una sottoscrizione di Azure. Se non se ne ha una, creare un [account](https://azure.microsoft.com/). Per ulteriori informazioni, vedere [considerazioni speciali per i livelli Enterprise](cache-overview.md#special-considerations-for-enterprise-tiers).

## <a name="create-a-cache"></a>Creare una cache
1. Per creare una cache, accedere al portale di Azure seguendo il collegamento nell'invito all'anteprima e quindi selezionare **Crea una risorsa**.

1. Nella pagina **Nuovo** selezionare **Database** e quindi **Cache di Azure per Redis**.
   
   :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="Selezionare Cache di Azure per Redis":::
   
1. Nella pagina **Nuova cache Redis** configurare le impostazioni per la nuova cache.
   
   | Impostazione      | Valore consigliato  | Descrizione |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Sottoscrizione** | Nell'elenco a discesa selezionare la sottoscrizione. | Sottoscrizione in cui creare la nuova istanza della cache di Azure per Redis. | 
   | **Gruppo di risorse** | Nell'elenco a discesa selezionare un gruppo di risorse oppure scegliere **Crea nuovo** e immettere il nome di un nuovo gruppo di risorse. | Nome del gruppo di risorse in cui creare la cache e altre risorse. L'inserimento di tutte le risorse di un'app in un unico gruppo di risorse ne semplifica la gestione o l'eliminazione. | 
   | **Nome DNS** | Immettere un nome univoco globale. | Il nome della cache deve essere una stringa compresa tra 1 e 63 caratteri contenente solo numeri, lettere o trattini. Il nome deve iniziare e terminare con un numero o una lettera e non può contenere trattini consecutivi. Il *nome host* dell'istanza della cache sarà *\<DNS name>.<Azure region>.redisenterprise.cache.azure.net*. | 
   | **Posizione** | Nell'elenco a discesa selezionare una località. | Durante la fase di anteprima, i livelli Enterprise sono disponibili in un numero limitato di aree di Azure. |
   | **Tipo di cache** | Nell'elenco a discesa selezionare il livello *Enterprise* o *Enterprise Flash* e le dimensioni. |  Il livello determina le dimensioni, le prestazioni e le funzionalità disponibili per la cache. |
   
   :::image type="content" source="media/cache-create/enterprise-tier-basics.png" alt-text="Scheda nozioni di base livello Enterprise":::

   > [!NOTE] 
   > Prima di procedere, assicurarsi di selezionare la casella in "Condizioni".
   >

1. Selezionare **Avanti: Rete** e ignorare la pagina.

1. Selezionare **Avanti: Avanzate** e impostare i **criteri di clustering** su **Enterprise**. Abilitare **l'accesso non TLS solo** se si prevede di connettersi alla nuova cache senza usare TLS. Questa operazione non è tuttavia consigliata.

   :::image type="content" source="media/cache-create/enterprise-tier-advanced.png" alt-text="Scheda Avanzate livello Enterprise":::

1. Selezionare **Avanti: Tag** e ignorare la pagina.

1. Selezionare **Avanti: Rivedi e crea**.

   :::image type="content" source="media/cache-create/enterprise-tier-summary.png" alt-text="Verifica livello Enterprise + scheda Crea":::

1. Rivedere le impostazioni e fare clic su **Crea**.
   
   La creazione della cache richiede un po' di tempo. È possibile monitorare lo stato di avanzamento nella pagina **Panoramica** della cache di Azure per Redis. Quando l'elemento **Stato** indica **In esecuzione**, la cache è pronta per l'uso.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido si è appreso come creare un'istanza di livello Enterprise della cache di Azure per Redis.

> [!div class="nextstepaction"]
> [Creare un'app Web ASP.NET che usa Cache Redis di Azure.](./cache-web-app-howto.md)

