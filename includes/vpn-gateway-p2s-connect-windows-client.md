---
title: includere file
description: includere file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/29/2020
ms.author: cherylmc
ms.openlocfilehash: 15c29648e42ba190991d51188489883e29bee165
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93041575"
---
>[!NOTE]
>È necessario avere diritti di amministratore per il computer client Windows da cui ci si connette.
>

1. Per connettersi a VNet, nel computer client passare a impostazioni VPN e individuare la connessione VPN creata. Viene denominato lo stesso nome della rete virtuale. Selezionare **Connetti**. È possibile che venga visualizzato un messaggio popup che fa riferimento all'uso del certificato. Selezionare **continua** per usare privilegi elevati.

1. Nella pagina di stato **Connessione** fare clic su **Connetti** per avviare la connessione. Se viene visualizzata una schermata **Seleziona certificato**, verificare che il certificato client visualizzato sia quello che si desidera utilizzare per la connessione. In caso contrario, utilizzare la freccia a discesa per selezionare il certificato corretto e quindi fare clic su **OK**.

   :::image type="content" source="./media/vpn-gateway-p2s-connect-windows-client/connection-status.png" alt-text="Connettersi da un computer Windows":::

1. Verrà stabilita la connessione.

   :::image type="content" source="./media/vpn-gateway-p2s-connect-windows-client/connected.png" alt-text="Connettersi da un computer a un diagramma di connessione da punto a sito di Azure VNet":::
