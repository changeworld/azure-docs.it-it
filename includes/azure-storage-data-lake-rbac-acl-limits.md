---
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/29/2020
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 97b47b48a183951002d04cd58b08d5e2b9051eda
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017679"
---
| Mechanism | Ambito |Limiti | Livello di autorizzazione supportato |
|---|---|---|---|
| Controllo degli accessi in base al ruolo di Azure | Account di archiviazione, contenitori. <br>Assegnazioni di ruolo di Azure tra risorse a livello di sottoscrizione o di gruppo di risorse. | 2000 assegnazioni di ruolo di Azure in una sottoscrizione | Ruoli di Azure (predefinito o personalizzato) |
| ACL| Directory, file |32 voci ACL (in effetti 28 voci ACL) per file e per directory. Gli ACL di accesso e predefiniti hanno ciascuno il rispettivo limite di immissione di 32 ACL. |Autorizzazione ACL|
