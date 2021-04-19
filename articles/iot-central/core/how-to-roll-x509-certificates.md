---
title: Implementare certificati X.509 in Azure IoT Central
description: Come eseguire il roll-to-roll dei certificati X.509 con l'IoT Central applicazione
author: dominicbetts
ms.author: dobett
ms.date: 07/31/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: c25af944b4c748307f4f974ca8616ecc9f7b28c3
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714525"
---
# <a name="how-to-roll-x509-device-certificates-in-iot-central-application"></a>Come eseguire il roll-to-roll dei certificati dei dispositivi X.509 nell IoT Central app IoT Central dispositivo

Durante il ciclo di vita di una soluzione IoT, è necessario rinnovare i certificati. Due dei principali motivi del rinnovo sono costituiti da una possibile violazione della sicurezza e dalle scadenze dei certificati.

In caso di violazione della sicurezza, l'implementazione dei certificati è una procedura di sicurezza consigliata per proteggere il sistema. Come parte della [metodologia di presunzione delle violazioni](https://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf), Microsoft sostiene che, parallelamente alle misure preventive, sia necessario mettere in atto processi di sicurezza reattivi. In questi processi di sicurezza deve essere incluso anche il rinnovo dei certificati dei dispositivi. La frequenza di rinnovo dei certificati dipende dalle esigenze di sicurezza della soluzione in uso. I clienti di soluzioni con dati altamente sensibili potrebbero rinnovare i certificati ogni giorno, mentre altri potrebbero rinnovarli ogni due anni.


## <a name="obtain-new-x509-certificates"></a>Ottenere nuovi certificati X.509

È possibile creare certificati X.509 personalizzati usando uno strumento come OpenSSL. Questa è un'ottima soluzione per eseguire il test dei certificati X.509 ma offre poche garanzie in termini di sicurezza. Usare questo approccio solo per i test, a meno che non si sia pronti a fungere da provider di autorità di certificazione.

## <a name="enrollment-groups-and-security-breaches"></a>Gruppi di registrazioni e violazioni della sicurezza

Per aggiornare la registrazione di un gruppo in risposta a una violazione della sicurezza, è necessario usare l'approccio seguente che aggiorna immediatamente il certificato corrente:

1. Passare ad **Amministrazione** nel riquadro sinistro e selezionare **Connessione del dispositivo.**

2. Selezionare **Gruppi di registrazioni** e selezionare il nome del gruppo nell'elenco.

3. Per l'aggiornamento del certificato, **selezionare Gestisci database primario** o Gestisci **secondario.**

4. Aggiungere e verificare il certificato X.509 radice nel gruppo di registrazioni.

   Completare questi passaggi per i certificati primario e secondario, se entrambi sono compromessi.

## <a name="enrollment-groups-and-certificate-expiration"></a>Gruppi di registrazione e scadenza del certificato

Se si stanno aggiornando i certificati per gestire le scadenze dei certificati, usare l'approccio seguente per aggiornare immediatamente il certificato corrente:

1. Passare ad **Amministrazione** nel riquadro sinistro e selezionare **Connessione del dispositivo.**

2. Selezionare **Gruppi di registrazioni** e selezionare il nome del gruppo nell'elenco.

3. Per l'aggiornamento del certificato, **selezionare Gestisci primario.**

4. Aggiungere e verificare il certificato X.509 radice nel gruppo di registrazioni.

5. Successivamente, quando il certificato secondario è scaduto, tornare indietro e aggiornare il certificato secondario.

## <a name="individual-enrollments-and-security-breaches"></a>Registrazioni singole e violazioni della sicurezza

Se i certificati vengono rilasciati in risposta a una violazione della sicurezza, usare l'approccio seguente per aggiornare immediatamente il certificato corrente:

1. Selezionare **Dispositivi** e selezionare il dispositivo.

2. Selezionare **Connetti** e selezionare Metodo di connessione come **Registrazione singola**

3. Selezionare **Certificati (X.509)** come meccanismo.

    ![Gestire le registrazioni singole](./media/how-to-roll-x509-certificates/certificate-update.png)

4. Per l'aggiornamento del certificato, selezionare l'icona della cartella per selezionare il nuovo certificato da caricare per la voce di registrazione. Selezionare **Salva**.

    Completare questi passaggi per i certificati primario e secondario, se entrambi sono compromessi

## <a name="individual-enrollments-and-certificate-expiration"></a>Registrazioni singole e scadenza del certificato

Se si rinnovano i certificati per gestirne la scadenza, è consigliabile usare la configurazione del certificato secondario come indicato di seguito per ridurre il tempo di inattività per i dispositivi che tentano il provisioning.

Quando il certificato secondario sta per scadere e deve essere eseguito il roll-to-roll, è possibile ruotare in usando la configurazione primaria. La rotazione tra il certificato primario e quello secondario consente così di ridurre il tempo di inattività per i dispositivi che tentano il provisioning.

1. Selezionare **Dispositivi** e selezionare il dispositivo.

2. Selezionare **Connetti** e selezionare Metodo di connessione come **Registrazione singola**

3. Selezionare **Certificati (X.509)** come meccanismo.

    ![Gestire le registrazioni singole](./media/how-to-roll-x509-certificates/certificate-update.png)

4. Per l'aggiornamento del certificato secondario, selezionare l'icona della cartella per selezionare il nuovo certificato da caricare per la voce di registrazione. Selezionare **Salva**.

5. Successivamente, quando il certificato primario è scaduto, tornare indietro e aggiornare il certificato primario.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come eseguire il roll-to-roll dei certificati X.509 nell'applicazione Azure IoT Central, è possibile ottenere la connessione [a Azure IoT Central](concepts-get-connected.md).


