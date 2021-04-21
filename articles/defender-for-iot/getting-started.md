---
title: 'Guida introduttiva: Introduzione'
description: Questa guida introduttiva illustra come iniziare a comprendere il flusso di lavoro di base per la distribuzione di Defender per IoT.
ms.topic: quickstart
ms.date: 04/17/2021
ms.openlocfilehash: b1e7686e1d68d5a3f239320930d69f22c78e13cb
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750447"
---
# <a name="quickstart-get-started-with-defender-for-iot"></a>Guida introduttiva: Introduzione a Defender per IoT

Questo articolo offre una panoramica dei passaggi da eseguire per configurare Azure Defender per IoT. Per il processo è necessario:

- Registrare la sottoscrizione e i sensori Azure Defender per IoT portale.
- Installare il sensore e il software della console di gestione locale.
- Eseguire l'attivazione iniziale del sensore e della console di gestione.

## <a name="permission-requirements"></a>Requisiti relativi alle autorizzazioni

Alcuni passaggi di configurazione richiedono autorizzazioni utente specifiche.

Le autorizzazioni utente amministrative sono necessarie per attivare il sensore e la console di gestione, caricare certificati SSL/TLS e generare nuove password.

Nella tabella seguente vengono descritte le autorizzazioni di accesso utente Azure Defender per IoT strumenti del portale:

| Autorizzazione | Ruolo con autorizzazioni di lettura per la sicurezza | Amministratore della sicurezza | Collaboratore alla sottoscrizione | Proprietario della sottoscrizione |
|--|--|--|--|--|
| Visualizzare i dettagli e accedere al software, ai file di attivazione e ai pacchetti di intelligence per le minacce  | ✓ | ✓ | ✓ | ✓ |
| Eseguire l'onboarding di un sensore  |  |  ✓ | ✓ | ✓ |
| Aggiornare i prezzi  |  |  ✓ | ✓ | ✓ |
| Ripristinare la password  | ✓  |  ✓ | ✓ | ✓ |

## <a name="identify-the-solution-infrastructure"></a>Identificare l'infrastruttura della soluzione

**Chiarire le esigenze di configurazione della rete**

Ricercare l'architettura di rete, la larghezza di banda monitorata e altri dettagli di rete. Per altre informazioni, vedere [Informazioni sull'Azure Defender per IoT di rete.](how-to-set-up-your-network.md)

**Chiarire quali sensori e appliance della console di gestione sono necessari per gestire il carico di rete**

Azure Defender per IoT supporta distribuzioni sia fisiche che virtuali. Per le distribuzioni fisiche, è possibile acquistare varie appliance certificate. Per altre informazioni, vedere [Identificare le appliance necessarie.](how-to-identify-required-appliances.md)

È consigliabile calcolare il numero approssimativo di dispositivi che verranno monitorati. Successivamente, quando si registra la sottoscrizione di Azure nel portale, verrà richiesto di immettere questo numero. I numeri possono essere aggiunti a intervalli di 1.000 secondi. Il numero di dispositivi monitorati è detto dispositivi di cui *è stato eseguito il commit.*

## <a name="register-with-azure-defender-for-iot"></a>Eseguire la registrazione con Azure Defender per IoT

La registrazione include:

- Onboarding delle sottoscrizioni di Azure in Defender per IoT.
- Definizione di dispositivi di cui è stato eseguito il commit.
- Download di un file di attivazione per la console di gestione locale.

Per eseguire la registrazione:

1. Passare al portale Azure Defender per IoT app.

1. Selezionare **Onboard subscription (Onboard della sottoscrizione).**

1. Nella pagina **Prezzi** selezionare una sottoscrizione o crearne una nuova e aggiungere il numero di dispositivi di cui è stato eseguito il commit.

1. Selezionare la **scheda Scarica la console di gestione** locale e salvare il file di attivazione scaricato. Questo file contiene i dispositivi aggregati di cui è stato eseguito il commit definiti. Il file verrà caricato nella console di gestione dopo l'accesso iniziale.

Per informazioni su come eseguire l'offboard di una sottoscrizione, vedere [Eseguire l'offboard di una sottoscrizione.](how-to-manage-subscriptions.md#offboard-a-subscription)

## <a name="install-and-set-up-the-on-premises-management-console"></a>Installare e configurare la console di gestione locale

Dopo aver acquisito l'appliance della console di gestione locale:

- Scaricare il pacchetto ISO dal portale Azure Defender per IoT.
- Installare il software.
- Attivare ed eseguire la configurazione iniziale della console di gestione.

Per installare e configurare:

1. Selezionare **Attività iniziali** nel portale di Defender per IoT.
1. Selezionare la scheda Console **di gestione** locale.
1. Scegliere una versione e selezionare **Scarica**.
1. Installare il software della console di gestione locale. Per altre informazioni, vedere [Installazione di Defender per IoT.](how-to-install-software.md)
1. Attivare e configurare la console di gestione. Per altre informazioni, vedere [Attivare e configurare la console di gestione locale.](how-to-activate-and-set-up-your-on-premises-management-console.md)

## <a name="onboard-a-sensor"></a>Eseguire l'onboarding di un sensore ##

Eseguire l'onboarding di un sensore registrando Azure Defender per IoT e scaricando un file di attivazione del sensore:

1. Definire il nome di un sensore e associarlo a una sottoscrizione.
1. Scegliere una modalità di connessione del sensore:

   - **Sensori connessi al cloud:** le informazioni rilevate dai sensori vengono visualizzate nella console dei sensori. Inoltre, le informazioni sugli avvisi vengono recapitate tramite un hub IoT e possono essere condivise con altri servizi di Azure, ad esempio Azure Sentinel.  È anche possibile scegliere di eseguire automaticamente il push dei pacchetti di intelligence per le minacce dal portale Azure Defender per IoT ai sensori. Per altre informazioni, vedere Ricerca e pacchetti [di Intelligence per le minacce](how-to-work-with-threat-intelligence-packages.md).

   - **Sensori gestiti in locale:** le informazioni rilevate dai sensori vengono visualizzate nella console dei sensori. Se si lavora in una rete air-gapped e si vuole una visualizzazione unificata di tutte le informazioni rilevate da più sensori gestiti in locale, usare la console di gestione locale.

1. Scaricare un file di attivazione del sensore.

Per informazioni dettagliate sull'onboarding, vedere [Onboarding e gestione dei sensori nel portale di Defender per IoT.](how-to-manage-sensors-on-the-cloud.md)

## <a name="install-and-set-up-the-sensor"></a>Installare e configurare il sensore

Scaricare il pacchetto ISO dal portale Azure Defender per IoT, installare il software e configurare il sensore.

1. Selezionare **Attività iniziali** nel portale di Defender per IoT.

1. Selezionare **Set up sensor (Configura sensore).**

1. Scegliere una versione e selezionare **Scarica**.

1. Installare il software del sensore. Per altre informazioni, vedere [Installazione di Defender per IoT.](how-to-install-software.md)

1. Attivare e configurare il sensore. Per altre informazioni, vedere [Accedere e attivare un sensore.](how-to-activate-and-set-up-your-sensor.md)

## <a name="connect-sensors-to-an-on-premises-management-console"></a>Connettere sensori a una console di gestione locale

Connettere i sensori alla console di gestione per assicurarsi che:

- I sensori inviano informazioni sull'inventario dei dispositivi e degli avvisi alla console di gestione locale.

- La console di gestione locale può eseguire backup dei sensori, gestire gli avvisi rilevati dai sensori, analizzare le disconnessioni dei sensori ed eseguire altre attività sui sensori connessi.

È consigliabile raggruppare più sensori che monitorino le stesse reti in un'unica zona. In questo modo si uniranno le informazioni raccolte da più sensori.

Per altre informazioni, [vedere Connettere sensori alla console di gestione locale.](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console)

## <a name="populate-azure-sentinel-with-alert-information-optional"></a>Popolare Azure Sentinel con informazioni di avviso (facoltativo)

Inviare informazioni di avviso Azure Sentinel configurando Azure Sentinel. Vedere [Connettere i dati da Defender per IoT a Azure Sentinel](how-to-configure-with-sentinel.md).

## <a name="next-steps"></a>Passaggi successivi ##

[Benvenuti in Azure Defender per IoT](overview.md)

[Azure Defender per IoT architettura](architecture.md)
