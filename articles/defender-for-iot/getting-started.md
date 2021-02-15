---
title: Guida introduttiva
description: Iniziare a comprendere il flusso di lavoro di base per Defender per la distribuzione di Internet.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/26/2020
ms.author: shhazam
ms.openlocfilehash: 06cf8b5f35bdebed0ebaa9e6949261d7c0d516f2
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522564"
---
# <a name="get-started-with-defender-for-iot"></a>Inizia a usare Defender per le cose

Questo articolo fornisce una panoramica dei passaggi da eseguire per configurare Azure Defender per l'it. Il processo richiede:

- Registrare la sottoscrizione e i sensori in Azure Defender per il portale delle cose.
- Installare il sensore e il software della console di gestione locale.
- Eseguire l'attivazione iniziale del sensore e della console di gestione.

## <a name="permission-requirements"></a>Requisiti relativi alle autorizzazioni

Per alcuni dei passaggi di configurazione sono necessarie autorizzazioni utente specifiche.

Le autorizzazioni utente amministrative sono necessarie per attivare la console di gestione e il sensore, caricare i certificati SSL/TLS e generare nuove password.

La tabella seguente descrive le autorizzazioni di accesso degli utenti per gli strumenti del portale di Azure Defender per le cose:

| Autorizzazione | Ruolo con autorizzazioni di lettura per la sicurezza | Amministratore della sicurezza | Collaboratore sottoscrizione | Proprietario della sottoscrizione |
|--|--|--|--|--|
| Visualizza tutte le schermate e i dati di Defender | ✓ | ✓ | ✓ | ✓ |
| Eseguire l'onboarding di un sensore  |  |  ✓ | ✓ | ✓ |
| Aggiornare i prezzi  |  |  ✓ | ✓ | ✓ |
| Ripristina password  | ✓  |  ✓ | ✓ | ✓ |

## <a name="1-identify-the-solution-infrastructure"></a>1. identificare l'infrastruttura della soluzione

**Chiarire le esigenze di configurazione della rete**

Ricercare l'architettura di rete, la larghezza di banda monitorata e altri dettagli sulla rete. Per ulteriori informazioni, vedere [informazioni su Azure Defender per l'installazione di rete](how-to-set-up-your-network.md).

**Chiarire i sensori e le appliance della console di gestione necessari per gestire il carico di rete**

Azure Defender per Internet delle cose supporta le distribuzioni sia fisiche che virtuali. Per le distribuzioni fisiche, è possibile acquistare varie appliance certificate. Per altre informazioni, vedere [identificare le appliance obbligatorie](how-to-identify-required-appliances.md).

Si consiglia di calcolare il numero approssimativo di dispositivi che verranno monitorati. Successivamente, quando si registra la sottoscrizione di Azure nel portale, verrà chiesto di immettere questo numero. I numeri possono essere aggiunti a intervalli di 1.000 secondi. Il numero di dispositivi monitorati è denominato dispositivi di cui è stato *eseguito il commit*.

## <a name="2-register-with-azure-defender-for-iot"></a>2. registrazione con Azure Defender per l'it

La registrazione include:

- Onboarding delle sottoscrizioni di Azure a Defender for Internet.
- Definizione dei dispositivi sottoposti a commit.
- Download di un file di attivazione per la console di gestione locale.

Per eseguire la registrazione:

1. Passare al portale di Azure Defender per l'it.
1. Selezionare **onboarding Subscription**.
1. Nella pagina dei **prezzi** selezionare una sottoscrizione o crearne una nuova e aggiungere il numero di dispositivi di cui è stato eseguito il commit.
1. Selezionare la scheda **download the on-premises Management Console** e salvare il file di attivazione scaricato. Questo file contiene i dispositivi aggregati di cui è stato eseguito il commit definiti. Il file verrà caricato nella console di gestione dopo l'accesso iniziale.

## <a name="3-install-and-set-up-the-on-premises-management-console"></a>3. installare e configurare la console di gestione locale

Dopo aver acquisito il dispositivo della console di gestione locale:

- Scaricare il pacchetto ISO dal portale di Azure Defender per l'it.
- Installare il software.
- Attivare ed eseguire l'installazione della console di gestione iniziale.

Per installare e configurare:

1. Selezionare **Introduzione** dal portale Defender for Internet.
1. Selezionare la scheda **console di gestione locale** .
1. Scegliere una versione e selezionare **Scarica**.
1. Installare il software della console di gestione locale. Per altre informazioni, vedere [Defender per l'installazione di](how-to-install-software.md)Internet.
1. Attivare e configurare la console di gestione di. Per altre informazioni, vedere [attivare e configurare la console di gestione locale](how-to-activate-and-set-up-your-on-premises-management-console.md).

## <a name="4-onboard-a-sensor"></a>4. onboarding di un sensore

Eseguire l'onboarding di un sensore eseguendo la registrazione con Azure Defender per l'IT e scaricando un file di attivazione del sensore:

1. Definire un nome di sensore e associarlo a una sottoscrizione.
1. Scegliere una modalità di gestione dei sensori:

   - **Sensori connessi al cloud**: le informazioni rilevate dai sensori vengono visualizzate nella console del sensore. Inoltre, le informazioni sugli avvisi vengono fornite tramite un hub Internet e possono essere condivise con altri servizi di Azure, ad esempio Azure Sentinel.

   - **Sensori gestiti localmente**: le informazioni rilevate dai sensori vengono visualizzate nella console del sensore. Se si lavora in una rete gapped e si vuole una visualizzazione unificata di tutte le informazioni rilevate da più sensori gestiti localmente, usare la console di gestione locale. 

1. Scaricare un file di attivazione del sensore.

Per altre informazioni, vedere [onboarding and Manage Sensors in the Defender for tutto Portal](how-to-manage-sensors-on-the-cloud.md).

## <a name="5-install-and-set-up-the-sensor"></a>5. installare e configurare il sensore

Scaricare il pacchetto ISO dal portale di Azure Defender per l'it, installare il software e configurare il sensore.

1. Selezionare **Introduzione** dal portale Defender for Internet.
1. Selezionare **Configura sensore**.
1. Scegliere una versione e selezionare **Scarica**.
1. Installare il software del sensore. Per altre informazioni, vedere [Defender per l'installazione di](how-to-install-software.md)Internet.
1. Attivare e configurare il sensore. Per altre informazioni, vedere [accedere e attivare un sensore](how-to-activate-and-set-up-your-sensor.md).

## <a name="6-connect-sensors-to-an-on-premises-management-console"></a>6. connettere i sensori a una console di gestione locale

Connettere i sensori alla console di gestione per verificare che:

- I sensori inviano informazioni sull'inventario dei dispositivi e degli avvisi alla console di gestione locale.

- La console di gestione locale può eseguire i backup dei sensori, gestire gli avvisi rilevati dai sensori, esaminare le disconnessioni dei sensori ed eseguire altre attività sui sensori connessi.

Si consiglia di raggruppare più sensori monitorando le stesse reti in una zona. Questa operazione condurrà le informazioni raccolte da più sensori.

Per ulteriori informazioni, vedere [Connect Sensors to the on-premises Management Console](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console).

## <a name="7-populate-azure-sentinel-with-alert-information-optional"></a>7. popolare Azure Sentinel con le informazioni sugli avvisi (facoltativo)

Inviare informazioni sugli avvisi ad Azure Sentinel configurando Sentinel di Azure. Vedere [connettere i dati da Defender per le cose a sentinella di Azure](how-to-configure-with-sentinel.md).
## <a name="next-steps"></a>Passaggi successivi

[Benvenuti in Azure Defender](overview.md)

[Azure Defender per l'architettura dell'it](architecture.md)
