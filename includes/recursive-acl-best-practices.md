---
title: includere file
description: includere file
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 02/03/2021
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: b0be074c995fcc62f63a4a2ebf4149d3040e7b6b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101750642"
---
## <a name="best-practices"></a>Procedure consigliate

In questa sezione vengono illustrate alcune procedure consigliate per l'impostazione di ACL in modo ricorsivo. 

#### <a name="handling-runtime-errors"></a>Gestione degli errori di runtime

È possibile che si verifichi un errore di runtime per diversi motivi, ad esempio un'interruzione o un problema di connettività client. Se si verifica un errore di runtime, riavviare il processo ACL ricorsivo. Gli ACL possono essere riapplicati agli elementi senza causare un impatto negativo. 

#### <a name="handling-permission-errors-403"></a>Gestione degli errori di autorizzazione (403)

Se si verifica un'eccezione di controllo di accesso durante l'esecuzione di un processo ACL ricorsivo, è possibile che l' [entità di sicurezza](../articles/role-based-access-control/overview.md#security-principal) di Active Directory non disponga di autorizzazioni sufficienti per applicare un ACL a uno o più elementi figlio nella gerarchia di directory. Quando si verifica un errore di autorizzazione, il processo viene arrestato e viene fornito un token di continuazione. Correggere il problema di autorizzazione e quindi usare il token di continuazione per elaborare il set di dati rimanente. Non sarà necessario elaborare nuovamente le directory e i file che sono già stati elaborati correttamente. È anche possibile scegliere di riavviare il processo ACL ricorsivo. Gli ACL possono essere riapplicati agli elementi senza causare un impatto negativo. 

#### <a name="credentials"></a>Credenziali 

Si consiglia di effettuare il provisioning di un Azure AD entità di sicurezza a cui è stato assegnato il ruolo di [proprietario dei dati BLOB di archiviazione](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner) nell'ambito del contenitore o dell'account di archiviazione di destinazione. 

#### <a name="performance"></a>Prestazioni 

Per ridurre la latenza, è consigliabile eseguire il processo ACL ricorsivo in una macchina virtuale (VM) di Azure che si trova nella stessa area dell'account di archiviazione. 

#### <a name="acl-limits"></a>Limiti ACL

Il numero massimo di ACL che è possibile applicare a una directory o a un file è 32 ACL di accesso e 32 ACL predefiniti. Per altre informazioni, vedere [Access control in Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md) (Controllo di accesso in Azure Data Lake Storage Gen2).