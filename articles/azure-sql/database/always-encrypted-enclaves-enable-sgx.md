---
title: Abilitare Intel SGX per il database SQL di Azure
description: Informazioni su come abilitare Intel SGX per Always Encrypted con enclave sicure nel database SQL di Azure selezionando una generazione hardware abilitata per SGX.
keywords: Crittografa dati, crittografia SQL, crittografia del database, dati sensibili, Always Encrypted, enclavi sicure, SGX, attestazione
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 01/15/2021
ms.openlocfilehash: ded1406c47bb3f00c366da7a5b28319f3712f8a7
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98733757"
---
# <a name="enable-intel-sgx-for-your-azure-sql-database"></a>Abilitare Intel SGX per il database SQL di Azure 

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Always Encrypted con enclave sicure per il database SQL di Azure è attualmente disponibile in **anteprima pubblica**.

[Always Encrypted con enclavi sicure](/sql/relational-databases/security/encryption/always-encrypted-enclaves) nel database SQL di Azure usa le enclave [Intel Software Guard Extensions (Intel SGX)](https://itpeernetwork.intel.com/microsoft-azure-confidential-computing/) . Per la disponibilità di Intel SGX, è necessario che il database utilizzi il [modello vCore](service-tiers-vcore.md) e la generazione dell'hardware della [serie DC](service-tiers-vcore.md#dc-series) .

La configurazione della generazione di hardware della serie DC per abilitare le enclave Intel SGX è responsabilità dell'amministratore del database SQL di Azure. [Quando si configurano le enclave e l'attestazione SGX, vedere ruoli e responsabilità](always-encrypted-enclaves-plan.md#roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation).

> [!NOTE]
> Intel SGX non è disponibile in generazioni hardware diverse dalla serie DC. Ad esempio, Intel SGX non è disponibile per l'hardware quinta generazione e non è disponibile per i database che usano il [modello DTU](service-tiers-dtu.md).

> [!IMPORTANT]
> Prima di configurare la generazione dell'hardware della serie DC per il database, controllare la disponibilità a livello di area della serie DC e assicurarsi di comprenderne le limitazioni relative alle prestazioni. Per ulteriori informazioni, vedere [serie DC](service-tiers-vcore.md#dc-series).

Per istruzioni dettagliate su come configurare un database nuovo o esistente per l'utilizzo di una generazione hardware specifica, vedere [selezione di una generazione di hardware](service-tiers-vcore.md#selecting-a-hardware-generation).
   
## <a name="next-steps"></a>Passaggi successivi

- [Configurare l'attestazione di Azure per il server di database SQL di Azure](always-encrypted-enclaves-configure-attestation.md)

## <a name="see-also"></a>Vedere anche

- [Esercitazione: Introduzione ad Always Encrypted con enclave sicure nel database SQL di Azure](always-encrypted-enclaves-getting-started.md)