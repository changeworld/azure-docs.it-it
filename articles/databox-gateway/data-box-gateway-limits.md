---
title: Limiti di Azure Data Box Gateway | Microsoft Docs
description: Descrive i limiti di sistema e le dimensioni consigliate per Microsoft Azure Data Box Gateway.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 10/20/2020
ms.author: alkohli
ms.openlocfilehash: 15b01f92fe0d39d099c10c7c086790a4dbb91379
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96582104"
---
# <a name="azure-data-box-gateway-limits"></a>Limiti di Azure Data Box Gateway

Quando si distribuisce e si usa la soluzione Microsoft Azure Data Box Gateway, tenere presenti i limiti seguenti.

## <a name="data-box-gateway-service-limits"></a>Limiti del servizio Data Box Gateway

[!INCLUDE [data-box-gateway-service-limits](../../includes/data-box-gateway-service-limits.md)]

## <a name="data-box-gateway-device-limits"></a>Limiti del dispositivo Data Box Gateway

La tabella seguente illustra i limiti del dispositivo Data Box Gateway.

| Descrizione | Valore |
|---|---|
|No. di file per dispositivo |100 milioni <br> Per ogni file 25 milioni aggiunto (con limite massimo a 100 milioni), è necessario aggiungere 2 TB di spazio su disco, 8 GB di RAM e 4 core di CPU. |
|No. di condivisioni per dispositivo |24 |
|No. di condivisioni per ogni contenitore di archiviazione di Azure |1 |
|Dimensione massima dei file scritti in una condivisione|Per un dispositivo virtuale di 2 TB, le dimensioni massime del file sono di 500 GB. <br> La dimensione massima del file aumenta con la dimensione del disco dati nel rapporto indicato in precedenza fino a raggiungere un massimo di 5 TB. |

## <a name="azure-storage-limits"></a>Limiti di Archiviazione di Azure

[!INCLUDE [data-box-gateway-storage-limits](../../includes/data-box-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Avvertenze sul caricamento dei dati

[!INCLUDE [data-box-gateway-storage-data-upload-caveats](../../includes/data-box-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Limiti delle dimensioni dell'oggetto e dell'account di archiviazione di Azure

[!INCLUDE [data-box-gateway-storage-acct-limits](../../includes/data-box-gateway-storage-acct-limits.md)]

## <a name="azure-object-size-limits"></a>Limiti delle dimensioni degli oggetti di Azure

[!INCLUDE [data-box-gateway-storage-object-limits](../../includes/data-box-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Preparare la distribuzione di Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
