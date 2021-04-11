---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/08/2021
ms.author: alkohli
ms.openlocfilehash: 4740ce4bd59598747cdd9c2147fbbd460b6e648e
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285199"
---
Il dispositivo è associato a un account di archiviazione usato come destinazione per i dati in Azure. L'accesso all'account di archiviazione è controllato dalla sottoscrizione e dalle due chiavi di accesso alle risorse di archiviazione a 512 bit che sono associate all'account di archiviazione.

Una delle chiavi viene usata per l'autenticazione quando il dispositivo Azure Stack Edge accede all'account di archiviazione. L'altra chiave è tenuta di riserva, in modo da poter ruotare le chiavi periodicamente.

Per motivi di sicurezza, molti data center richiedono la rotazione delle chiavi. Per la rotazione delle chiavi, è opportuno seguire queste procedure consigliate:

- La chiave dell’account di archiviazione è simile alla password radice per l'account di archiviazione. Proteggere con attenzione la chiave dell'account. Non divulgare la password ad altri utenti, non impostarla come hardcoded o non salvarla in testo normale in una posizione accessibile ad altri.
- [Rigenerare la chiave dell'account](../articles/storage/common/storage-account-keys-manage.md#manually-rotate-access-keys) tramite il portale di Azure se si ritiene che possa essere compromessa.
- L'amministratore di Azure deve modificare o rigenerare periodicamente la chiave primaria o secondaria usando la sezione archiviazione del portale di Azure per accedere direttamente all'account di archiviazione.
- È anche possibile usare la propria chiave di crittografia per proteggere i dati nell'account di archiviazione di Azure. Quando si specifica una chiave gestita dal cliente, tale chiave viene usata per proteggere e controllare l'accesso alla chiave che crittografa i dati. Per altre informazioni su come proteggere i dati, vedere [abilitare le chiavi gestite dal cliente per l'account di archiviazione di Azure](../articles/storage/common/customer-managed-keys-overview.md#enable-customer-managed-keys-for-a-storage-account).
