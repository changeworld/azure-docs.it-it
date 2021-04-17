---
title: Come aggiungere un dominio personalizzato alla configurazione Frontdoor di Azure SKU Standard/Premium
description: In questa esercitazione si apprenderà come eseguire l'onboard di un dominio personalizzato per Frontdoor di Azure SKU Standard/Premium.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: amsriva
ms.openlocfilehash: 69e216c8893f9361a18354e5d165ecc0499601aa
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107387922"
---
# <a name="create-a-custom-domain-on-azure-front-door-standardpremium-sku-preview-using-the-azure-portal"></a>Creare un dominio personalizzato Frontdoor di Azure SKU Standard/Premium (anteprima) usando il portale di Azure

> [!Note]
> Questa documentazione è per Frontdoor di Azure Standard/Premium (anteprima). Per informazioni sulle Frontdoor di Azure? Vedere [qui](../front-door-overview.md).

Quando si usa Frontdoor di Azure Standard/Premium per il recapito delle applicazioni, è necessario un dominio personalizzato se si vuole che il proprio nome di dominio sia visibile nelle richieste dell'utente finale. Avere un nome di dominio visibile può essere pratico per i clienti e utile a scopo di personalizzazione.

Dopo aver creato un Frontdoor di Azure Standard/Premium, l'host front-end predefinito avrà un sottodominio di azurefd.net. Questo sottodominio viene incluso nell'URL quando Frontdoor di Azure Standard/Premium recapita contenuto dal back-end per impostazione predefinita. Ad esempio: `https://contoso-frontend.azurefd.net/activeusers.htm`. Per comodità, Frontdoor di Azure offre la possibilità di associare un dominio personalizzato all’host predefinito. Con questa opzione, il contenuto viene recapitato con un dominio personalizzato nell'URL anziché con un Frontdoor di Azure di proprietà Standard/Premium. Ad esempio, "https://www.contoso.com/photo.png".

> [!IMPORTANT]
> Frontdoor di Azure Standard/Premium (anteprima) è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere le [**Condizioni supplementari per l'uso delle anteprime di Microsoft Azure**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti
* Prima di poter completare i passaggi di questa esercitazione, è necessario creare una frontdoor. Per altre informazioni, vedere [Avvio rapido: Creare una front door Standard/Premium.](create-front-door-portal.md)

* Se non si dispone già di un dominio personalizzato, è necessario acquistarne uno con un provider di dominio. Ad esempio, vedere [Acquistare un nome di dominio personalizzato](../../app-service/manage-custom-dns-buy-domain.md).

* Se si usa Azure per ospitare i domini [DNS,](../../dns/dns-overview.md)è necessario delegare il sistema DNS (Domain Name System) del provider di dominio a un DNS di Azure. Per altre informazioni, vedere [Delegare un dominio a DNS di Azure](../../dns/dns-delegate-domain-azure-dns.md). In caso contrario, se si usa un provider di dominio per gestire il dominio DNS, è necessario convalidare manualmente il dominio immettendo i record TXT DNS richiesto.

## <a name="add-a-new-custom-domain"></a>Aggiungere un nuovo dominio personalizzato

> [!NOTE]
> In anteprima pubblica, l'uso DNS di Azure per creare domini Apex non è supportato Frontdoor di Azure Standard/Premium. Esistono altri provider DNS che supportano l'appiattimento CNAME o la ricerca DNS che consentirà l'uso di domini APEX per Frontdoor di Azure Standard/Premium.

Un dominio personalizzato viene gestito dalla sezione Domini nel portale. Un dominio personalizzato può essere creato e convalidato prima dell'associazione a un endpoint. Un dominio personalizzato e i relativi sottodomini possono essere associati a un solo endpoint alla volta. Tuttavia, è possibile usare sottodomini diversi dello stesso dominio personalizzato per frontdomini diversi. È anche possibile eseguire il mapping di domini personalizzati con sottodomini diversi allo stesso endpoint frontdominio.

1. In Impostazioni per il profilo Frontdoor di Azure selezionare *Domini*  e quindi il **pulsante Aggiungi un** dominio.

    :::image type="content" source="../media/how-to-add-custom-domain/add-domain-button.png" alt-text="Screenshot del pulsante Aggiungi dominio nella pagina di destinazione del dominio.":::

1. Verrà **visualizzata la pagina Aggiungi** un dominio in cui è possibile immettere informazioni sul dominio personalizzato. È possibile scegliere IL DNS gestito da Azure, che è consigliato oppure è possibile scegliere di usare il proprio provider DNS. Se si sceglie DNS gestito da Azure, selezionare una zona DNS esistente e quindi selezionare un sottodominio personalizzato o crearne uno nuovo. Se si usa un altro provider DNS, immettere manualmente il nome di dominio personalizzato. Selezionare **Aggiungi** per aggiungere il dominio personalizzato.

    :::image type="content" source="../media/how-to-add-custom-domain/add-domain-page.png" alt-text="Screenshot della pagina aggiungi un dominio.":::

    Viene creato un nuovo dominio personalizzato con stato di convalida **Invio**.

    :::image type="content" source="../media/how-to-add-custom-domain/validation-state-submitting.png" alt-text="Screenshot dell'invio dello stato di convalida del dominio.":::

    Attendere che lo stato di convalida cambi in **In sospeso.** Questa operazione potrebbe richiedere alcuni minuti.

    :::image type="content" source="../media/how-to-add-custom-domain/validation-state-pending.png" alt-text="Screenshot dello stato di convalida del dominio in sospeso.":::

1. Selezionare lo stato **Convalida** in sospeso. Verrà visualizzata una nuova pagina con le informazioni sui record TXT DNS necessarie per convalidare il dominio personalizzato. Il record TXT è nel formato `_dnsauth.<your_subdomain>` . Se si usa una zona DNS di Azure, selezionare  il pulsante Aggiungi e verrà creato un nuovo record TXT con il valore del record visualizzato nella DNS di Azure locale. Se si usa un altro provider DNS, creare manualmente un nuovo record TXT di nome con il `dnsauth.<your_subdomain>` valore del record, come illustrato nella pagina.

    :::image type="content" source="../media/how-to-add-custom-domain/validate-custom-domain.png" alt-text="Screenshot della pagina convalida dominio personalizzato.":::

1. Selezionare lo stato di aggiornamento. Dopo che il dominio è stato convalidato usando il record TXT DNS, lo stato di convalida cambierà in **verificato.** La convalida di questa operazione potrebbe richiedere alcuni minuti.

    :::image type="content" source="../media/how-to-add-custom-domain/domain-status-verified.png" alt-text="Screenshot del dominio personalizzato verificato.":::

1. Chiudere la pagina per tornare alla pagina di destinazione dell'elenco di domini personalizzati. Lo stato di provisioning del dominio personalizzato deve essere modificato in **Provisioning e** lo stato di convalida deve essere modificato in **Approvato.**

    :::image type="content" source="../media/how-to-add-custom-domain/provisioned-approved-status.png" alt-text="Screenshot dello stato di provisioning e approvazione.":::

## <a name="associate-the-custom-domain-with-your-front-door-endpoint"></a>Associare il dominio personalizzato all'endpoint front-door

Dopo aver convalidato il dominio personalizzato, è possibile aggiungerlo all'endpoint Frontdoor di Azure Standard/Premium.

1. Dopo la convalida del dominio personalizzato, è possibile associarlo a un endpoint Standard/Premium e a una route Frontdoor di Azure esistente. Selezionare il **collegamento Associazione endpoint** per aprire la pagina Associa endpoint e **route.** Selezionare un endpoint e le route da associare. Selezionare quindi **Associa**. Chiudere la pagina al termine dell'operazione di associazione.

    :::image type="content" source="../media/how-to-add-custom-domain/associate-endpoint-routes.png" alt-text="Screenshot della pagina associa endpoint e route.":::

    Lo stato dell'associazione dell'endpoint deve cambiare in base all'endpoint a cui è attualmente associato il dominio personalizzato. 

    :::image type="content" source="../media/how-to-add-custom-domain/endpoint-association-status.png" alt-text="Screenshot del collegamento all'associazione dell'endpoint.":::

1. Selezionare il collegamento Stato DNS.

    :::image type="content" source="../media/how-to-add-custom-domain/dns-state-link.png" alt-text="Screenshot del collegamento allo stato DNS.":::

1. Verrà visualizzata la pagina Aggiungi o aggiorna **il record CNAME** e verranno visualizzate le informazioni sul record CNAME che devono essere fornite prima che il traffico possa iniziare a scorrere. Se si usano DNS di Azure ospitate, è possibile creare i record CNAME selezionando il **pulsante** Aggiungi nella pagina. Se si usa un altro provider DNS, è necessario immettere manualmente il nome e il valore del record CNAME, come illustrato nella pagina.

    :::image type="content" source="../media/how-to-add-custom-domain/add-update-cname-record.png" alt-text="Screenshot dell'aggiunta o dell'aggiornamento del record CNAME.":::

1. Dopo aver creato il record CNAME e aver associato il dominio personalizzato all'endpoint Frontdoor di Azure, il flusso di traffico inizierà a scorrere.

    > [!NOTE]
    > Se HTTPS è abilitato, il provisioning e la propagazione dei certificati potrebbero richiedere alcuni minuti perché la propagazione viene eseguita in tutte le posizioni perimetrali. 

## <a name="verify-the-custom-domain"></a>Verificare il dominio personalizzato

Dopo aver convalidato e associato il dominio personalizzato, verificare che all'endpoint sia stato fatto riferimento correttamente al dominio personalizzato.

:::image type="content" source="../media/how-to-add-custom-domain/verify-configuration.png" alt-text="Screenshot del dominio personalizzato convalidato e associato.":::

Infine, verificare che il contenuto dell'applicazione sia servito tramite un browser.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come abilitare HTTPS per il dominio personalizzato, continuare con l'esercitazione successiva.

> [!div class="nextstepaction"]
> [Abilitare HTTPS per un dominio personalizzato]()
