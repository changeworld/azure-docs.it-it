---
title: Configurare la preferenza di routing di rete
titleSuffix: Azure Storage
description: Configurare la preferenza di routing di rete per l'account di archiviazione di Azure per specificare il modo in cui il traffico di rete viene indirizzato all'account da client su Internet.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 02/21/2020
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: fb427de170764e5cd1fca57f9fb2d1410829e521
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101700844"
---
# <a name="configure-network-routing-preference-for-azure-storage"></a>Configurare la preferenza di routing di rete per archiviazione di Azure

Questo articolo descrive come configurare la preferenza di routing di rete e gli endpoint specifici della route per l'account di archiviazione. 

La preferenza di routing di rete specifica il modo in cui il traffico di rete viene indirizzato all'account da client su Internet. Gli endpoint specifici della route sono nuovi endpoint creati da archiviazione di Azure per l'account di archiviazione. Questi endpoint instradano il traffico su un percorso desiderato senza modificare le preferenze di routing predefinite. Per altre informazioni, vedere [preferenza di routing di rete per archiviazione di Azure](network-routing-preference.md).

## <a name="configure-the-routing-preference-for-the-default-public-endpoint"></a>Configurare la preferenza di routing per l'endpoint pubblico predefinito

Per impostazione predefinita, la preferenza di routing per l'endpoint pubblico dell'account di archiviazione è impostata su rete globale Microsoft. Come preferenza di routing predefinita per l'endpoint pubblico dell'account di archiviazione, è possibile scegliere tra la rete globale Microsoft e il routing Internet. Per ulteriori informazioni sulla differenza tra questi due tipi di routing, vedere [preferenza di routing di rete per archiviazione di Azure](network-routing-preference.md). 

Per modificare la preferenza di routing al routing Internet:

1. Passare all'account di archiviazione nel portale.

2. In **Impostazioni** scegliere **rete**.

    > [!div class="mx-imgBorder"]
    > ![Opzione di menu rete](./media/configure-network-routing-preference/networking-option.png)

3.  Nella scheda **firewall e reti virtuali** , in routing di **rete**, modificare l'impostazione delle **Preferenze di routing** su **routing Internet**.

4.  Fare clic su **Salva**.

    > [!div class="mx-imgBorder"]
    > ![opzione di routing Internet](./media/configure-network-routing-preference/internet-routing-option.png)

## <a name="configure-a-route-specific-endpoint"></a>Configurare un endpoint specifico della route

È anche possibile configurare un endpoint specifico della route. Ad esempio, è possibile impostare la preferenza di routing per l'endpoint predefinito su *Internet routing* e quindi pubblicare un endpoint specifico della route che consente il traffico tra i client su Internet e l'account di archiviazione da indirizzare tramite la rete globale Microsoft.

1.  Passare all'account di archiviazione nel portale.

2.  In **Impostazioni** scegliere **rete**.

3.  Nella scheda **firewall e reti virtuali** , in **pubblica endpoint specifici della route**, scegliere la preferenza di routing dell'endpoint specifico della route, quindi fare clic su **Salva**. Questa preferenza ha effetto solo sull'endpoint specifico della route. Questa preferenza non influisce sulle preferenze di routing predefinite.  

    La figura seguente mostra l'opzione di **routing di rete Microsoft** selezionata.

    > [!div class="mx-imgBorder"]
    > ![Opzione di routing di rete Microsoft](./media/configure-network-routing-preference/microsoft-network-routing-option.png)

## <a name="find-the-endpoint-name-for-a-route-specific-endpoint"></a>Trovare il nome dell'endpoint per un endpoint specifico della route

Se è stato configurato un endpoint specifico della route, è possibile trovare l'endpoint nelle proprietà dell'account di archiviazione.

1.  In **Impostazioni** scegliere **Proprietà**.

    > [!div class="mx-imgBorder"]
    > ![opzione del menu proprietà](./media/configure-network-routing-preference/properties.png)

2.  L'endpoint di **routing di rete Microsoft** viene visualizzato per ogni servizio che supporta le preferenze di routing. Questa immagine mostra l'endpoint per i servizi BLOB e file.

    > [!div class="mx-imgBorder"]
    > ![Opzione di routing di rete Microsoft per endpoint specifici della route](./media/configure-network-routing-preference/routing-url.png)


## <a name="see-also"></a>Vedi anche

- [Preferenza di routing di rete](network-routing-preference.md)
- [Configurare i firewall e le reti virtuali di Archiviazione di Azure](storage-network-security.md)
- [Raccomandazioni sulla sicurezza per archiviazione BLOB](../blobs/security-recommendations.md)
