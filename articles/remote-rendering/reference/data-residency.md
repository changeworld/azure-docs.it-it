---
title: Residenza dei dati
description: Descrive la residenza dei dati quando si usa il rendering remoto di Azure.
author: rapete
ms.author: rapete
ms.date: 02/04/2021
ms.topic: reference
ms.openlocfilehash: f20b3bb3de877ac627f5f6909c98cb9e1553f2a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "99576894"
---
# <a name="azure-remote-rendering-data-residency"></a>Residenza dei dati di rendering remoto di Azure 
Questo articolo illustra il concetto di residenza dei dati e il modo in cui si applica al rendering remoto di Azure. 

## <a name="data-residency"></a>Residenza dei dati 
Il rendering remoto di Azure usa i contenitori BLOB di Azure forniti dall'utente per l'archiviazione del modello. Quando il modello non è in uso, rimane nell'area di archiviazione BLOB di Azure fornita dall'utente. Quando il modello viene usato per il rendering, i dati vengono copiati nell'area selezionata dall'utente all'avvio della sessione di rendering.

## <a name="next-steps"></a>Passaggi successivi
Per informazioni su come configurare un contenitore di archiviazione BLOB di Azure per il rendering remoto di Azure, vedere [la pagina relativa alla conversione di un modello per il rendering](../quickstarts/convert-model.md).
