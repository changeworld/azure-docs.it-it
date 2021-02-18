---
title: Come aggiungere un dominio personalizzato alla configurazione dello SKU Standard/Premium di Azure front door
description: In questa esercitazione si apprenderà come caricare un dominio personalizzato nello SKU di Azure front door standard/Premium.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: amsriva
ms.openlocfilehash: 164e06024844fb5262586450b737db9c807e373a
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101099889"
---
# <a name="create-a-custom-domain-on-azure-front-door-standardpremium-sku-preview-using-the-azure-portal"></a>Creare un dominio personalizzato nello SKU Standard/Premium dello sportello anteriore di Azure (anteprima) usando il portale di Azure

> [!Note]
> Questa documentazione è destinata a Azure front door standard/Premium (Preview). Stai cercando informazioni su Azure front door? Visualizza [qui](../front-door-overview.md).

Quando si usa Azure front door standard/Premium per la distribuzione di applicazioni, è necessario un dominio personalizzato se si desidera che il proprio nome di dominio sia visibile nelle richieste dell'utente finale. Avere un nome di dominio visibile può essere pratico per i clienti e utile a scopo di personalizzazione.

Dopo aver creato un profilo standard/Premium di Azure front door, l'host front-end predefinito avrà un sottodominio di azurefd.net. Questo sottodominio viene incluso nell'URL quando Azure front door standard/Premium fornisce contenuto dal back-end per impostazione predefinita. Ad esempio: `https://contoso-frontend.azurefd.net/activeusers.htm`. Per comodità, Frontdoor di Azure offre la possibilità di associare un dominio personalizzato all’host predefinito. Con questa opzione è possibile distribuire i contenuti con un dominio personalizzato nell'URL invece che con un nome di dominio di proprietà standard/Premium di Azure. Ad esempio: https://www.contoso.com/photo.png.

> [!IMPORTANT]
> Azure front door standard/Premium (anteprima) è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere le [**Condizioni supplementari per l'uso delle anteprime di Microsoft Azure**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti
* Prima di poter completare i passaggi di questa esercitazione, è necessario creare una frontdoor. Per altre informazioni, vedere [Guida introduttiva: creare uno standard/Premium per la porta anteriore](create-front-door-portal.md).

* Se non si dispone già di un dominio personalizzato, è necessario prima acquistarne uno con un provider di dominio. Ad esempio, vedere [Acquistare un nome di dominio personalizzato](../../app-service/manage-custom-dns-buy-domain.md).

* Se si usa Azure per ospitare i [domini DNS](../../dns/dns-overview.md), è necessario delegare il DNS (Domain Name System) del provider di dominio a un DNS di Azure. Per altre informazioni, vedere [Delegare un dominio a DNS di Azure](../../dns/dns-delegate-domain-azure-dns.md). In caso contrario, se si usa un provider di dominio per gestire il dominio DNS, è necessario convalidare manualmente il dominio immettendo i record TXT DNS richiesti.

## <a name="add-a-new-custom-domain"></a>Aggiungere un nuovo dominio personalizzato

Un dominio personalizzato è gestito dalla sezione Domains nel portale. Un dominio personalizzato può essere creato e convalidato prima dell'associazione a un endpoint. Un dominio personalizzato e i relativi sottodomini possono essere associati solo a un singolo endpoint alla volta. Tuttavia, è possibile usare diversi sottodomini dello stesso dominio personalizzato per porte frontali diverse. È anche possibile eseguire il mapping di domini personalizzati con sottodomini diversi allo stesso endpoint della porta anteriore.

1. In impostazioni per il profilo di sportello anteriore di Azure selezionare *domini*  e quindi il pulsante **Aggiungi un dominio** .

    :::image type="content" source="../media/how-to-add-custom-domain/add-domain-button.png" alt-text="Screenshot del pulsante Aggiungi dominio nella pagina di destinazione del dominio.":::

1. Verrà visualizzata la pagina **Aggiungi un dominio** in cui è possibile immettere informazioni sul dominio personalizzato. È possibile scegliere DNS gestito da Azure, consigliato oppure è possibile scegliere di usare il proprio provider DNS. Se si sceglie DNS gestito da Azure, selezionare una zona DNS esistente e quindi selezionare un sottodominio personalizzato o crearne uno nuovo. Se si usa un altro provider DNS, immettere manualmente il nome di dominio personalizzato. Selezionare **Aggiungi** per aggiungere il dominio personalizzato.

    :::image type="content" source="../media/how-to-add-custom-domain/add-domain-page.png" alt-text="Screenshot della pagina Aggiungi un dominio.":::

    Viene creato un nuovo dominio personalizzato con lo stato di convalida dell' **invio**.

    :::image type="content" source="../media/how-to-add-custom-domain/validation-state-submitting.png" alt-text="Screenshot dello stato di convalida del dominio che invia.":::

    Attendere che lo stato di convalida venga modificato in **in sospeso**. Questa operazione potrebbe richiedere alcuni minuti.

    :::image type="content" source="../media/how-to-add-custom-domain/validation-state-pending.png" alt-text="Screenshot dello stato di convalida del dominio in sospeso.":::

1. Selezionare lo stato di convalida **in sospeso** . Verrà visualizzata una nuova pagina con le informazioni sul record TXT DNS necessarie per convalidare il dominio personalizzato. Il formato del record TXT è `_dnsauth.<your_subdomain>` . Se si usa la zona basata su DNS di Azure, selezionare il pulsante **Aggiungi** e verrà creato un nuovo record TXT con il valore di record visualizzato nella zona DNS di Azure. Se si usa un altro provider DNS, creare manualmente un nuovo record TXT di nome `dnsauth.<your_subdomain>` con il valore del record, come illustrato nella pagina.

    :::image type="content" source="../media/how-to-add-custom-domain/validate-custom-domain.png" alt-text="Screenshot della pagina convalida dominio personalizzato.":::

1. Selezionare lo stato di aggiornamento. Una volta convalidato il dominio usando il record TXT DNS, lo stato di convalida verrà modificato in **verificato**. Questa operazione può richiedere alcuni minuti per la convalida.

    :::image type="content" source="../media/how-to-add-custom-domain/domain-status-verified.png" alt-text="Screenshot del dominio personalizzato verificato.":::

1. Chiudere la pagina per tornare alla pagina di destinazione elenco domini personalizzati. Lo stato di provisioning del dominio personalizzato deve essere modificato in stato di **provisioning** e lo stato di convalida deve essere modificato in **approvato**.

    :::image type="content" source="../media/how-to-add-custom-domain/provisioned-approved-status.png" alt-text="Screenshot dello stato di provisioning e approvato.":::

## <a name="associate-the-custom-domain-with-your-front-door-endpoint"></a>Associare il dominio personalizzato all'endpoint della porta anteriore

Dopo aver convalidato il dominio personalizzato, è possibile aggiungerlo all'endpoint standard/Premium di Azure front door.

1. Dopo che il dominio personalizzato è stato convalidato, è possibile associarlo a un endpoint e a un endpoint di Azure front door anteriore esistente. Selezionare il collegamento **endpoint Association (associazione endpoint** ) per aprire la pagina **associa endpoint e route** . Selezionare un endpoint e le route a cui si desidera associare. Quindi selezionare **associa**. Al termine dell'operazione di associazione, chiudere la pagina.

    :::image type="content" source="../media/how-to-add-custom-domain/associate-endpoint-routes.png" alt-text="Screenshot della pagina associazione di endpoint e route.":::

    Lo stato dell'associazione di endpoint deve essere modificato in modo da riflettere l'endpoint a cui è attualmente associato il dominio personalizzato. 

    :::image type="content" source="../media/how-to-add-custom-domain/endpoint-association-status.png" alt-text="Screenshot del collegamento dell'associazione di endpoint.":::

1. Selezionare il collegamento stato DNS.

    :::image type="content" source="../media/how-to-add-custom-domain/dns-state-link.png" alt-text="Screenshot del collegamento di stato DNS.":::

1. Verrà visualizzata la pagina **Aggiungi o Aggiorna record CNAME** e verranno visualizzate le informazioni sul record CNAME che devono essere fornite prima che il traffico possa avviare il flusso. Se si usano zone ospitate da DNS di Azure, è possibile creare i record CNAME selezionando il pulsante **Aggiungi** nella pagina. Se si usa un altro provider DNS, è necessario immettere manualmente il nome e il valore del record CNAME, come illustrato nella pagina.

    :::image type="content" source="../media/how-to-add-custom-domain/add-update-cname-record.png" alt-text="Screenshot dell'aggiunta o dell'aggiornamento del record CNAME.":::

1. Dopo che il record CNAME è stato creato e il dominio personalizzato è associato all'endpoint di Azure front door, il flusso del traffico inizierà a essere eseguito.

    > [!NOTE]
    > Se HTTPS è abilitato, il provisioning e la propagazione del certificato possono richiedere alcuni minuti perché la propagazione viene eseguita in tutte le posizioni perimetrali. 

## <a name="verify-the-custom-domain"></a>Verificare il dominio personalizzato

Dopo aver convalidato e associato il dominio personalizzato, verificare che al dominio personalizzato venga fatto riferimento correttamente all'endpoint.

:::image type="content" source="../media/how-to-add-custom-domain/verify-configuration.png" alt-text="Screenshot del dominio personalizzato convalidato e associato.":::

Infine, verificare che il contenuto dell'applicazione venga servito usando un browser.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come abilitare HTTPS per il dominio personalizzato, continuare con l'esercitazione successiva.

> [!div class="nextstepaction"]
> [Abilitare HTTPS per un dominio personalizzato]()
