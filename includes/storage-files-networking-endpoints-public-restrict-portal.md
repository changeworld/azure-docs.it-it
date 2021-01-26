---
title: includere file
description: includere file
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 01/25/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3c76988557bfa338bcfb606f568036422a536c1d
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98798592"
---
Passare all'account di archiviazione per cui limitare tutto l'accesso all'endpoint pubblico consentendolo solo a specifiche reti virtuali. Nel sommario per l'account di archiviazione selezionare **rete**. 

Nella parte superiore della pagina selezionare il pulsante di opzione **Reti selezionate**. Verranno rese visibili diverse impostazioni nascoste per il controllo della restrizione dell'endpoint pubblico. Fare clic su **+ Aggiungi rete virtuale esistente** per selezionare la rete virtuale specifica a cui deve essere consentito l'accesso all'account di archiviazione tramite l'endpoint pubblico. Sarà necessario selezionare una rete virtuale e una subnet associata. 

Selezionare **Consenti ai servizi Microsoft attendibili di accedere a questo account di archiviazione** per consentire ai servizi Microsoft attendibili, ad esempio Sincronizzazione file di Azure, di accedere all'account di archiviazione.

[![Screenshot del pannello rete con una rete virtuale specifica autorizzata ad accedere all'account di archiviazione tramite l'endpoint pubblico](media/storage-files-networking-endpoints-public-restrict-portal/restrict-public-endpoint-0.png)](media/storage-files-networking-endpoints-public-restrict-portal/restrict-public-endpoint-0.png#lightbox)