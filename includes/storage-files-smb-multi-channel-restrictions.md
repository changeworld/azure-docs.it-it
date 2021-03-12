---
title: includere file
description: includere file
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/16/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 06db7bcb5698f152dd5062762fdb3d59ae326e22
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102603392"
---
SMB multicanale per le condivisioni file di Azure presenta attualmente le restrizioni seguenti:
- Può essere utilizzato solo con account di archiviazione con ridondanza locale.
- Supportato solo per i client Windows. 
- Il numero massimo di canali è quattro.
- SMB diretto non è supportato.
- Gli endpoint privati per gli account di archiviazione non sono supportati.
- Per gli account di archiviazione con autenticazione Active Directory Domain Services locale (AD DS) o Azure AD DS [basata sull'identità](../articles/storage/files/storage-files-active-directory-overview.md) abilitata per file di Azure, i client SMB non saranno in grado di utilizzare Esplora file di Windows per configurare le autorizzazioni NTFS per directory e file.
    - Usare invece lo strumento [icacls](/windows-server/administration/windows-commands/icacls) di Windows o il comando [Set-ACL](/powershell/module/microsoft.powershell.security/set-acl) per configurare le autorizzazioni.

