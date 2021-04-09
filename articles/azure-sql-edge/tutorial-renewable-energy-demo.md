---
title: Distribuzione di SQL Edge di Azure nelle turbine di un parco eolico Contoso
description: In questa esercitazione si userà SQL Edge di Azure per il rilevamento di scie nelle turbine di un parco eolico Contoso.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 12/18/2020
ms.openlocfilehash: e966d756744210dc8f6739b96501dd91f0d8d1b5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97723474"
---
# <a name="using-azure-sql-edge-to-build-smarter-renewable-resources"></a>Uso di SQL Edge di Azure per creare risorse rinnovabili più intelligenti

Questa demo di SQL Edge di Azure si basa sull'energia rinnovabile Contoso, un parco eolico che usa SQL DB Edge per l'elaborazione dei dati all'interno del generatore. 

Questa demo illustra la risoluzione di un avviso generato a causa di una turbolenza del vento rilevata nel dispositivo. Si eseguirà il training di un modello e lo si distribuirà a SQL DB Edge per correggere la scia del vento rilevata e ottimizzare infine la potenza erogata.

SQL Edge di Azure: video dimostrativo sulle energie rinnovabili su Channel 9:
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Edge-Demo-Renewable-Energy/player]

## <a name="setting-up-the-demo-on-your-local-computer"></a>Configurazione della demo nel computer locale
Git verrà usato per copiare tutti i file dalla demo nel computer locale. 

1. Installare git da [qui](https://git-scm.com/download).
2. Aprire un prompt dei comandi e passare a una cartella in cui scaricare il repository. 
3. Eseguire il comando https://github.com/microsoft/sql-server-samples.git.
4. Passare a **'sql-server-samples\samples\demos\azure-sql-edge-demos\Wind Turbine Demo'** nel percorso in cui è stato clonato il repository.
5. Seguire le istruzioni in README.md per configurare l'ambiente demo ed eseguire la demo.