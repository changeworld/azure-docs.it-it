---
title: Panoramica della sicurezza per Condivisione dati di Azure
description: Panoramica della sicurezza per Condivisione dati di Azure
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 12/17/2020
ms.openlocfilehash: 4e62645dd5a7a8336df4fccf12daebc730a91168
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2020
ms.locfileid: "97678431"
---
# <a name="security-overview-for-azure-data-share"></a>Panoramica della sicurezza per Condivisione dati di Azure

Questo articolo fornisce una panoramica della sicurezza del servizio Condivisione dati di Azure.

## <a name="security-overview"></a>Panoramica della sicurezza

Condivisione dati di Azure sfrutta le funzionalità di sicurezza sottostanti offerte da Azure per proteggere i dati inattivi e in movimento. I dati inattivi vengono crittografati, purché la funzionalità sia supportata dall'archivio dati sottostante. Anche i dati vengono crittografati in transito usando TLS 1,2. così come i metadati su una condivisione dati quando sono inattivi e in movimento. La condivisione di dati di Azure non archivia i contenuti dei dati del cliente da condividere.

La condivisione di dati di Azure sfrutta l'identità gestita (precedentemente nota come MSI) per accedere agli archivi dati usati per la condivisione dei dati. Non viene effettuato alcuno scambio di credenziali tra un provider di dati e un consumer. Per altre informazioni sull'identità gestita, vedere [identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md). Per ulteriori informazioni sui ruoli e sulle autorizzazioni necessarie per condividere i dati, fare riferimento a [ruoli e requisiti](concepts-roles-permissions.md).

## <a name="access-control"></a>Controllo di accesso

I controlli di accesso alla condivisione dati di Azure possono essere impostati sul livello di risorsa della condivisione dati per assicurarsi che siano accessibili da quelli autorizzati. Il proprietario e il collaboratore di una risorsa di condivisione dati possono condividere dati, ricevere condivisioni e modificare le condivisioni esistenti. Il lettore di una risorsa di condivisione dati può visualizzare le condivisioni, ma non apportare modifiche. 

Una volta creata o ricevuta una condivisione, gli utenti con l'autorizzazione appropriata per la risorsa di condivisione dati possono apportare modifiche. Quando un utente che crea o riceve una condivisione lascia l'organizzazione, non termina la condivisione o interrompe il flusso di dati. Altri utenti con le autorizzazioni appropriate per la risorsa di condivisione dati possono continuare a gestire la condivisione.

## <a name="share-data-from-or-to-data-stores-with-firewall-enabled"></a>Condividere dati da o in archivi dati con il firewall abilitato
Per condividere dati da o in account di archiviazione con il firewall attivato, è necessario abilitare **Consenti servizi Microsoft attendibili** nell'account di archiviazione. Per informazioni dettagliate, vedere [configurare i firewall e le reti virtuali di archiviazione di Azure](
https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services) .


## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come iniziare a condividere i dati, procedere con l'esercitazione sulla [condivisione dei dati](share-your-data.md).
