---
title: includere il file
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/06/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: f014ce55dc40723faf1b60f908814f9fa0428b8e
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99213929"
---
Azure prevede i seguenti ruoli predefiniti di Azure per l'autorizzazione dell'accesso a dati di BLOB e code tramite Azure AD e OAuth:

- [Proprietario dei dati del BLOB di archiviazione](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): consente di impostare la proprietà e gestire il controllo di accesso POSIX per Azure Data Lake Storage Gen2. Per altre informazioni, vedere [Access control in Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md) (Controllo di accesso in Azure Data Lake Storage Gen2).
- [Collaboratore ai dati del BLOB di archiviazione](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Consente di concedere le autorizzazioni di lettura, scrittura ed eliminazione alle risorse di archiviazione BLOB.
- [Lettore dei dati del BLOB di archiviazione](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): Consente di concedere le autorizzazioni di sola lettura alle risorse di archiviazione BLOB.
- [Delegante di BLOB di archiviazione](../articles/role-based-access-control/built-in-roles.md#storage-blob-delegator): consente di ottenere una chiave di delega utente da usare per creare una firma di accesso condiviso per un contenitore o un BLOB firmato con credenziali di Azure AD.
- [Collaboratore ai dati della coda di archiviazione](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Consente di concedere le autorizzazioni di lettura, scrittura ed eliminazione alle code di Azure.
- [Lettore dei dati della coda di archiviazione](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): Consente di concedere le autorizzazioni di sola lettura alle code di Azure.
- [Ruolo con autorizzazioni di elaborazione per i messaggi sui dati della coda di archiviazione](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): consente di concedere autorizzazioni di visualizzazione, recupero ed eliminazione per i messaggi nelle code di Archiviazione di Azure.
- [Mittente dei messaggi sui dati della coda di archiviazione](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): consente di concedere autorizzazioni di aggiunta per i messaggi nelle code di Archiviazione di Azure.

Solo i ruoli definiti in modo esplicito per l'accesso ai dati consentono a un'entità di sicurezza di accedere ai dati di BLOB o code. I ruoli predefiniti, ad esempio **Proprietario**, **Collaboratore** e **Collaboratore Account di archiviazione** permettono a un'entità di sicurezza di gestire un account di archiviazione, ma non forniscono l'accesso ai dati di BLOB o code all'interno di tale account tramite Azure AD. Tuttavia, se un ruolo include **Microsoft.Storage/storageAccounts/listKeys/action**, un utente a cui è assegnato può accedere ai dati nell'account di archiviazione tramite l'autorizzazione con chiave condivisa con le chiavi di accesso all'account. Per altre informazioni, vedere [Usare il portale di Azure per accedere ai dati di BLOB o code](../articles/storage/blobs/authorize-data-operations-portal.md).

Per informazioni dettagliate sui ruoli predefiniti di Azure per Archiviazione di Azure, sia per i servizi dati che per il servizio di gestione, vedere la sezione **Archiviazione** in [Ruoli predefiniti di Azure per il controllo degli accessi in base al ruolo di Azure](../articles/role-based-access-control/built-in-roles.md#storage). Inoltre, per informazioni sui diversi tipi di ruolo che forniscono le autorizzazioni in Azure, vedere [Ruoli di amministratore della sottoscrizione classica, ruoli di Azure e ruoli di Azure AD](../articles/role-based-access-control/rbac-and-directory-admin-roles.md).

> [!IMPORTANT]
> Le assegnazioni di ruolo di Azure possono richiedere fino a 30 minuti per la propagazione.
