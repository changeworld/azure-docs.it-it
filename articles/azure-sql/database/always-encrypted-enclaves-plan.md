---
title: Pianificare le enclavi e l'attestazione di Intel SGX nel database SQL di Azure
description: Pianificare la distribuzione di Always Encrypted con enclavi sicure nel database SQL di Azure.
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
ms.openlocfilehash: 4448ce051b0c9e73865e8057cc4f224c9cbeb571
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98732745"
---
# <a name="plan-for-intel-sgx-enclaves-and-attestation-in-azure-sql-database"></a>Pianificare le enclavi e l'attestazione di Intel SGX nel database SQL di Azure

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Always Encrypted con enclave sicure per il database SQL di Azure è attualmente disponibile in **anteprima pubblica**.

[Always Encrypted con enclavi sicure](/sql/relational-databases/security/encryption/always-encrypted-enclaves) nel database SQL di Azure usa le enclave [Intel Software Guard Extensions (Intel SGX)](https://itpeernetwork.intel.com/microsoft-azure-confidential-computing/) e richiede [Microsoft Azure attestazione](/sql/relational-databases/security/encryption/always-encrypted-enclaves#secure-enclave-attestation).

## <a name="plan-for-intel-sgx-in-azure-sql-database"></a>Pianificare Intel SGX nel database SQL di Azure

Intel SGX è una tecnologia per l'ambiente di esecuzione attendibile basata su hardware. Intel SGX è disponibile per i database che usano il [modello vCore](service-tiers-vcore.md) e la generazione di hardware della [serie DC](service-tiers-vcore.md?#dc-series) . Per essere certi di poter usare Always Encrypted con le enclavi sicure nel database, è quindi necessario selezionare la generazione di hardware della serie DC quando si crea il database oppure aggiornare il database esistente per usare la generazione di hardware della serie DC.

> [!NOTE]
> Intel SGX non è disponibile in generazioni hardware diverse dalla serie DC. Ad esempio, Intel SGX non è disponibile per l'hardware quinta generazione e non è disponibile per i database che usano il [modello DTU](service-tiers-dtu.md).

> [!IMPORTANT]
> Prima di configurare la generazione dell'hardware della serie DC per il database, controllare la disponibilità a livello di area della serie DC e assicurarsi di comprenderne le limitazioni relative alle prestazioni. Per informazioni dettagliate, vedere [serie DC](service-tiers-vcore.md#dc-series).

## <a name="plan-for-attestation-in-azure-sql-database"></a>Pianificare l'attestazione nel database SQL di Azure

[Microsoft Azure attestazione](../../attestation/overview.md) (anteprima) è una soluzione per l'attestazione di ambienti di esecuzione attendibile (TEEs), incluse le enclave Intel SGX nei database SQL di Azure con la generazione dell'hardware della serie DC.

Per usare l'attestazione di Azure per l'attestazione delle enclave Intel SGX nel database SQL di Azure, è necessario:

1. Creare un [provider di attestazione](../../attestation/basic-concepts.md#attestation-provider) e configurarlo con un criterio di attestazione. 

2. Concedere al server logico SQL di Azure l'accesso al provider di attestazione creato.

## <a name="roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation"></a>Ruoli e responsabilità quando si configurano le enclave e l'attestazione SGX

La configurazione dell'ambiente per supportare le enclave e l'attestazione di Intel SGX per Always Encrypted nel database SQL di Azure comporta la configurazione di componenti di tipi diversi: attestazione Microsoft Azure, database SQL di Azure e applicazioni che attivano l'attestazione dell'enclave. La configurazione dei componenti di ogni tipo viene eseguita dagli utenti presupponendo uno dei ruoli distinti seguenti:

- Amministratore di attestazione: crea un provider di attestazione in Microsoft Azure attestazione, autore dei criteri di attestazione, concede l'accesso al server logico di Azure SQL al provider di attestazione e condivide l'URL di attestazione che punta al criterio agli amministratori dell'applicazione.
- Amministratore del database SQL di Azure: Abilita le enclave SGX nei database selezionando la generazione di hardware della serie DC e fornisce all'amministratore di attestazione l'identità del server logico SQL di Azure che deve accedere al provider di attestazione.
- Amministratore applicazione: configura le applicazioni con l'URL di attestazione ottenuto dall'amministratore di attestazione.

Negli ambienti di produzione (che gestiscono dati sensibili reali), è importante che l'organizzazione rispetti la separazione dei ruoli durante la configurazione dell'attestazione, assicurandosi che ogni ruolo distinto venga assunto da persone diverse. In particolare, se l'obiettivo della distribuzione di Always Encrypted nell'organizzazione consiste nel ridurre la superficie di attacco, assicurandosi che gli amministratori del database SQL di Azure non possano accedere ai dati sensibili, gli amministratori del database SQL di Azure non devono controllare i criteri di attestazione.

## <a name="next-steps"></a>Passaggi successivi

- [Abilitare Intel SGX per il database SQL di Azure](always-encrypted-enclaves-enable-sgx.md)

## <a name="see-also"></a>Vedi anche

- [Esercitazione: Introduzione ad Always Encrypted con enclave sicure nel database SQL di Azure](always-encrypted-enclaves-getting-started.md)