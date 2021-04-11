---
title: Risoluzione dei problemi dell'hub Azure Internet Error 400027 ConnectionForcefullyClosedOnNewConnection
description: Informazioni su come correggere l'errore 400027 ConnectionForcefullyClosedOnNewConnection
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom:
- mqtt
- fasttrack-edit
- iot
ms.openlocfilehash: cd531abe1a10abead26055c6b0d4dd328cd3e836
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061367"
---
# <a name="400027-connectionforcefullyclosedonnewconnection"></a>400027 ConnectionForcefullyClosedOnNewConnection

Questo articolo descrive le cause e le soluzioni per gli errori di **400027 ConnectionForcefullyClosedOnNewConnection** .

## <a name="symptoms"></a>Sintomi

Il dispositivo viene scollegato con **Communication_Error** come **CONNECTIONSTATUSCHANGEREASON** usando .NET SDK e il tipo di trasporto MQTT.

L'operazione del dispositivo gemello da dispositivo a cloud (ad esempio, le proprietà di lettura o di correzione) o la chiamata diretta al metodo ha esito negativo con il codice di errore **400027**.

## <a name="cause"></a>Causa

Un altro client ha creato una nuova connessione all'hub Internet con le stesse credenziali, quindi l'hub Internet ha chiuso la connessione precedente. L'hub Internet delle cose non consente a più di un client di connettersi usando lo stesso set di credenziali.

## <a name="solution"></a>Soluzione

Verificare che ogni client si connetta all'hub Internet con la propria identità.