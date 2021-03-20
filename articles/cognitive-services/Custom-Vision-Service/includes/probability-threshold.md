---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: 07e7cc991f127bf4bb4f466c0108962786e45bce
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018367"
---
Prendere nota del dispositivo di scorrimento **soglia di probabilità** nel riquadro sinistro della scheda **prestazioni** . Questo è il livello di confidenza che una stima deve avere per essere considerata corretta (ai fini del calcolo della precisione e del richiamo). 

Quando si interpretano le chiamate di stima con una soglia di probabilità alta, i risultati restituiti tendono ad avere una precisione elevata a scapito del richiamo, ossia le classificazioni rilevate sono corrette, ma molte rimangono non rilevate. Con una soglia di probabilità bassa avviene l'opposto, ossia viene rilevata la maggior parte delle classificazioni, ma all'interno del set sono presenti più falsi positivi. Tenendo presente questo aspetto, è consigliabile impostare la soglia di probabilità in base alle esigenze specifiche del progetto. In seguito, quando si ricevono i risultati della stima sul lato client, è consigliabile usare lo stesso valore di soglia di probabilità usato qui.