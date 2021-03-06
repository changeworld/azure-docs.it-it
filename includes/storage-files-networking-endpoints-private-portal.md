---
title: includere file
description: includere file
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 04/15/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 958c40ed311ebe8b2a83ce0d4d394d00f48d1afa
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107732296"
---
Passare all'account di archiviazione per cui creare un endpoint privato. Nel sommario dell'account di archiviazione selezionare **Rete,** Connessioni **endpoint** privato e quindi **+ Endpoint privato** per creare un nuovo endpoint privato. 

[![Screenshot della voce Connessioni endpoint privato nel sommario dell'account di archiviazione](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-0.png)](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-0.png#lightbox)

La procedura guidata risultante include più pagine da completare.

Nel pannello **Informazioni di base** selezionare il gruppo di risorse, il nome e l'area da usare per l'endpoint privato, che non devono necessariamente corrispondere a quelli dell'account di archiviazione, anche se l'endpoint privato deve essere creato nella stessa area della rete virtuale in cui inserirlo.

![Screenshot della sezione Informazioni di base nella sezione per creare un endpoint privato](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-1.png)

Nel pannello **Risorsa** selezionare il pulsante di opzione **Connettersi a una risorsa di Azure nella directory**. In **Tipo di risorsa** selezionare **Microsoft.Storage/storageAccounts**. Il campo **Risorsa** corrisponde all'account di archiviazione con la condivisione file di Azure a cui connettersi. La sottorisorsa di destinazione è **file**, perché la procedura riguarda File di Azure.

Il pannello **Configurazione** consente di selezionare la rete virtuale e la subnet specifiche a cui aggiungere l'endpoint privato. È necessario selezionare una subnet distinta da quella aggiunta all'endpoint di servizio in precedenza. Il pannello Configurazione contiene anche le informazioni per la creazione o l'aggiornamento della zona DNS privato. È consigliabile usare la zona `privatelink.file.core.windows.net` predefinita.

![Screenshot della sezione Configurazione](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-2.png)

Fare clic su **Rivedi e crea** per creare l'endpoint privato. 
