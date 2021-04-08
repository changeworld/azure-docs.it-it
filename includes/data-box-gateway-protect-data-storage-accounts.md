---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 160f85b34b3730b14ed96854d5f60ad81f4eb63b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96582354"
---
Il dispositivo è associato a un account di archiviazione usato come destinazione per i dati in Azure. L'accesso all'account di archiviazione è controllato dalla sottoscrizione e dalle due chiavi di accesso alle risorse di archiviazione a 512 bit che sono associate all'account di archiviazione.

Una delle chiavi viene usata per l'autenticazione quando il dispositivo Azure Stack Edge accede all'account di archiviazione. L'altra chiave è tenuta di riserva, in modo da poter ruotare le chiavi periodicamente.

Per motivi di sicurezza, molti data center richiedono la rotazione delle chiavi. Per la rotazione delle chiavi, è opportuno seguire queste procedure consigliate:

- La chiave dell’account di archiviazione è simile alla password radice per l'account di archiviazione. Proteggere con attenzione la chiave dell'account. Non distribuire la password ad altri utenti, impostarla come hardcoded o salvarla in qualsiasi posizione in testo normale accessibile ad altri utenti.
- Rigenerare la chiave dell'account tramite il portale di Azure se si ritiene che possa essere compromessa. Per altre informazioni, vedere [Gestire le chiavi di accesso dell'account di archiviazione](../articles/storage/common/storage-account-keys-manage.md).
- L'amministratore di Azure deve modificare o rigenerare periodicamente la chiave primaria o secondaria usando la sezione archiviazione del portale di Azure per accedere direttamente all'account di archiviazione.