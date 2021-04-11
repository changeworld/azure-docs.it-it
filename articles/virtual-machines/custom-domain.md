---
title: Creazione e utilizzo di un dominio personalizzato
description: Connettere un dominio personalizzato a una macchina virtuale in Azure.
author: mimckitt
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/01/2021
ms.author: jamesser
ms.reviewer: cynthn
ms.openlocfilehash: c4797420904b6dc03550f658c2aa950a4de99c9c
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107251000"
---
# <a name="add-custom-domain-to-azure-vm-or-resource"></a>Aggiungere un dominio personalizzato alla macchina virtuale o alla risorsa di Azure

In Azure sono disponibili diversi modi per connettere un dominio personalizzato alla macchina virtuale o alla risorsa. Per qualsiasi risorsa con un indirizzo IP pubblico (macchina virtuale, Load Balancer, gateway applicazione) il modo più semplice consiste nel creare un set di record a nel registrar di dominio corrispondente. 

## <a name="prerequisites"></a>Prerequisiti 
- È necessaria una macchina virtuale con un server Web che esegue. È possibile usare la [Guida introduttiva](./linux/quick-create-cli.md) per creare una macchina virtuale e aggiungere nginx.

- La macchina virtuale deve essere accessibile al Web (aprire la porta 80 o 443). Per una distribuzione più sicura, posizionare prima la macchina virtuale dietro a un servizio di bilanciamento del carico o un gateway applicazione. Per ulteriori informazioni, vedere [Guida introduttiva: Load Balancer](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal?tabs=option-1-create-load-balancer-standard).

- Disporre di un dominio esistente e accedere alle impostazioni DNS. Per altre informazioni, vedere [acquistare un dominio personalizzato per il servizio app Azure](../app-service/manage-custom-dns-buy-domain.md).


## <a name="add-custom-domain-to-vm-public-ip-address"></a>Aggiungere un dominio personalizzato all'indirizzo IP pubblico della macchina virtuale

Quando si crea una macchina virtuale nella portale di Azure, viene creata automaticamente una risorsa IP pubblica per la macchina virtuale. L'indirizzo IP pubblico viene visualizzato nella pagina Panoramica macchina virtuale. 
 
:::image type="content" source="media/custom-domain/essentials.png" alt-text="Mostra l'indirizzo IP pubblico nella sezione informazioni di base della pagina Panoramica macchina virtuale.":::

Se si seleziona l'indirizzo IP, è possibile visualizzare altre informazioni su di esso. Verificare che l' **assegnazione IP** sia impostata su **static**. Un indirizzo IP statico non cambia se la macchina virtuale o la risorsa viene riavviata o arrestata.

:::image type="content" source="media/custom-domain/ip-config.png" alt-text="Mostra la configurazione dell'IP pubblico in modo che sia possibile verificare se l'indirizzo IP è statico.":::

Se l'indirizzo IP non è statico, sarà necessario creare un nome di dominio completo. 

1. Selezionare la macchina virtuale nel portale. 
1. Nel menu a sinistra selezionare **Proprietà** .
1. Nell' **etichetta nome ADDRESS\DNS IP pubblico** selezionare l'indirizzo IP.
2. In **etichetta nome DNS** immettere il prefisso che si vuole usare.
3. Fare clic su **Salva** nella parte superiore della pagina.
4. Selezionare **Panoramica** nel menu a sinistra per tornare al pannello panoramica della macchina virtuale.
5. Verificare che il *nome DNS* venga visualizzato correttamente. 

Aprire un browser e immettere l'indirizzo IP o il nome di dominio completo e verificare che mostri il contenuto Web in esecuzione nella macchina virtuale.
 
Dopo aver verificato l'indirizzo IP statico o FQDN, passare al provider di dominio e passare a impostazioni DNS.

Una volta aggiunto un *record a* , che punta all'indirizzo IP pubblico o al nome di dominio completo. La procedura per il registrar GoDaddy, ad esempio, è la seguente:
1. Accedere al dominio personalizzato da usare e selezionarlo.
2. Nella sezione **domini** selezionare **Gestisci tutto**, quindi selezionare **DNS | Gestire le zone**.
3. In **Domain Name** (Nome di dominio) immettere il proprio nome di dominio, quindi selezionare **Search** (Cerca).
4. Dalla pagina di gestione DNS selezionare Aggiungi e quindi selezionare un nell'elenco tipo.
5. Completare i campi della voce A:
    - Tipo: lasciare **un oggetto** selezionato.
    - Host: invio **@**
    - Punta a: immettere l'indirizzo IP pubblico o il nome di dominio completo della macchina virtuale. 
    - TTL: lasciare selezionata un'ora.
6. Selezionare **Salva**.

La voce di record A viene aggiunta alla tabella dei record DNS.
 
Dopo la creazione del record, in genere richiede circa un'ora per la propagazione DNS, ma a volte può richiedere fino a 48 ore. 


 
## <a name="next-steps"></a>Passaggi successivi
[Panoramica della terminazione TLS e del TLS end-to-end con il gateway applicazione](../application-gateway/ssl-overview.md).

 
