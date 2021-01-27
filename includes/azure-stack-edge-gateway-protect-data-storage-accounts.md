---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 2e0a7ca8fb9eaafbc50c0ce60799dd68d83b2fa3
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98901001"
---
Il dispositivo è associato a un account di archiviazione usato come destinazione per i dati in Azure. L'accesso all'account di archiviazione è controllato dalla sottoscrizione e dalle due chiavi di accesso alle risorse di archiviazione a 512 bit che sono associate all'account di archiviazione.

Una delle chiavi viene usata per l'autenticazione quando il dispositivo Data Box Edge accede all'account di archiviazione. L'altra chiave è tenuta di riserva, in modo da poter ruotare le chiavi periodicamente.

Per motivi di sicurezza, molti data center richiedono la rotazione delle chiavi. Per la rotazione delle chiavi, è opportuno seguire queste procedure consigliate:

- La chiave dell’account di archiviazione è simile alla password radice per l'account di archiviazione. Proteggere con attenzione la chiave dell'account. Non divulgare la password ad altri utenti, non impostarla come hardcoded o non salvarla in testo normale in una posizione accessibile ad altri.
- [Rigenerare la chiave dell'account](../articles/storage/common/storage-account-keys-manage.md#manually-rotate-access-keys) tramite il portale di Azure se si ritiene che possa essere compromessa.
- L'amministratore di Azure deve modificare o rigenerare periodicamente la chiave primaria o secondaria usando la sezione archiviazione del portale di Azure per accedere direttamente all'account di archiviazione.
