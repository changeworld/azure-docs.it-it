---
title: includere file
description: includere file
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/15/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 39b61c78cbd0333183c80234256d6041a109d846
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "68249928"
---
Un ID della risorsa di Azure AD indica i destinatari per i quali è possibile usare un token emesso per consentire l'accesso a una risorsa di Azure. Nel caso di Archiviazione di Azure, l'ID della risorsa può essere specifico di un singolo account di archiviazione o essere valido per qualsiasi account di archiviazione. La tabella seguente descrive i valori che è possibile specificare per l'ID della risorsa:

|ID risorsa  |Description  |
|---------|---------|
|`https://<account>.blob.core.windows.net` <br /><br /> `https://<account>.queue.core.windows.net`    | Endpoint servizio per un determinato account di archiviazione. Usare questo valore per acquisire un token per autorizzare le richieste solo all'account di archiviazione di Azure e al servizio specifici. Sostituire il valore racchiuso tra parentesi quadre con il nome del proprio account di archiviazione.      |
|`https://storage.azure.com/`     | Usare questo valore per acquisire un token per autorizzare le richieste a qualsiasi account di archiviazione di Azure.        |
