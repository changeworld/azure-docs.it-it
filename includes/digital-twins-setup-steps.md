---
author: baanders
description: Panoramica del file di inclusione per i passaggi nell'installazione dei dispositivi gemelli di Azure
ms.service: digital-twins
ms.topic: include
ms.date: 10/14/2020
ms.author: baanders
ms.openlocfilehash: c1cfab8c9e68d9e6b0c3b84e8848645a50c24710
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "100560773"
---
La configurazione completa per una nuova istanza di Azure Digital Twins è costituita da due parti:
1. **Creazione dell'istanza**
2. **Impostazione delle autorizzazioni di accesso utente**: gli utenti di Azure devono avere il ruolo di *proprietario dei dati di Azure Digital gemelli* nell'istanza di Azure Digital Twins per poterli gestire e i relativi dati. In questo passaggio, come proprietario/amministratore della sottoscrizione di Azure, questo ruolo verrà assegnato alla persona che gestirà l'istanza di Azure Digital gemelli. Potrebbe trattarsi di un utente o di un altro utente nell'organizzazione.
 
>[!NOTE]
>Queste operazioni devono essere completate da un utente con la possibilità di gestire sia le risorse che l'accesso degli utenti nella sottoscrizione di Azure. Sebbene alcuni passaggi possano essere completati con autorizzazioni inferiori, sarà necessario collaborare con tali autorizzazioni per configurare completamente un'istanza utilizzabile. Per altre informazioni, vedere la sezione [*prerequisiti: autorizzazioni necessarie*](#prerequisites-permission-requirements) di seguito.