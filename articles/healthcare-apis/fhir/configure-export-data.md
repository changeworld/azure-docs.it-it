---
title: Configurare le impostazioni di esportazione nell'API di Azure per FHIR
description: Questo articolo descrive come configurare le impostazioni di esportazione nell'API di Azure per FHIR
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 3/18/2021
ms.author: matjazl
ms.openlocfilehash: ee110420c697afb6ecad857ba823c61d03c6be6c
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105046992"
---
# <a name="configure-export-setting-and-set-up-the-storage-account"></a>Configurare l'impostazione di esportazione e impostare l'account di archiviazione

API di Azure per FHIR supporta $export comando che consente di esportare i dati fuori dall'API di Azure per l'account FHIR in un account di archiviazione.

Per la configurazione dell'esportazione nell'API di Azure per FHIR sono necessari tre passaggi:

1. Abilitare l'identità gestita nell'API di Azure per il servizio FHIR.
2. Creazione di un account di archiviazione di Azure (se non eseguito in precedenza) e assegnazione dell'autorizzazione all'API di Azure per FHIR all'account di archiviazione.
3. Selezione dell'account di archiviazione nell'API di Azure per FHIR come esportazione dell'account di archiviazione.

## <a name="enabling-managed-identity-on-azure-api-for-fhir"></a>Abilitazione dell'identità gestita nell'API di Azure per FHIR

Il primo passaggio per la configurazione dell'API di Azure per FHIR per l'esportazione consiste nell'abilitare l'identità gestita a livello di sistema nel servizio. Per altre informazioni sulle identità gestite in Azure, vedere [informazioni sulle identità gestite per le risorse di Azure](../../active-directory/managed-identities-azure-resources/overview.md).

A tale scopo, passare al servizio API di Azure per FHIR e selezionare **Identity (identità**). Se si modifica lo stato **su on** , viene abilitata l'identità gestita nell'API di Azure per il servizio FHIR.

![Abilita identità gestita](media/export-data/fhir-mi-enabled.png)

È ora possibile passare al passaggio successivo creando un account di archiviazione e assegnando l'autorizzazione al servizio.

## <a name="adding-permission-to-storage-account"></a>Aggiunta dell'autorizzazione per l'account di archiviazione

Il passaggio successivo nell'esportazione consiste nell'assegnare l'autorizzazione per l'API di Azure per il servizio FHIR per la scrittura nell'account di archiviazione.

Dopo aver creato un account di archiviazione, passare a **controllo di accesso (IAM)** nell'account di archiviazione e selezionare **Aggiungi assegnazione ruolo**.

![Esporta assegnazione di ruolo](media/export-data/fhir-export-role-assignment.png)

È qui che verrà aggiunto il **collaboratore dei dati BLOB di archiviazione** del ruolo al nome del servizio e quindi selezionare **Save (Salva**).

![Aggiungi ruolo](media/export-data/fhir-export-role-add.png)

A questo punto si è pronti per selezionare l'account di archiviazione nell'API di Azure per FHIR come account di archiviazione predefinito per $export.

## <a name="selecting-the-storage-account-for-export"></a>Selezione dell'account di archiviazione per $export

Il passaggio finale consiste nell'assegnare l'account di archiviazione di Azure che verrà usato dall'API di Azure per FHIR per esportare i dati in. A tale scopo, passare a **integrazione** in API di Azure per il servizio FHIR e selezionare l'account di archiviazione.

![FHIR Esporta archiviazione](media/export-data/fhir-export-storage.png)

Al termine di questo passaggio finale, è ora possibile esportare i dati usando $export comando.

> [!Note]
> Solo gli account di archiviazione nella stessa sottoscrizione dell'API di Azure per FHIR possono essere registrati come destinazione per le operazioni di $export.

Per ulteriori informazioni sulla configurazione delle impostazioni del database, il controllo di accesso, l'abilitazione della registrazione diagnostica e l'utilizzo di intestazioni personalizzate per aggiungere dati ai log di controllo, vedere:

>[!div class="nextstepaction"]
>[Impostazioni aggiuntive](azure-api-for-fhir-additional-settings.md)
