---
title: Configurare l'hub di Azure per la distribuzione tramite gli aggiornamenti dell'aria
description: Informazioni su come configurare l'hub Azure per la distribuzione degli aggiornamenti tramite l'aria in Azure Percept DK
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 80f30ae818b9e74ff97d2bc26552ed7d7124b499
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101662914"
---
# <a name="how-to-set-up-azure-iot-hub-to-deploy-over-the-air-updates-to-your-azure-percept-dk"></a>Come configurare l'hub Azure per la distribuzione in Azure Percept DK
Mantieni le tue esigenze di Azure Percept DK sicure e aggiornate usando gli aggiornamenti in modalità wireless. Con pochi semplici passaggi, sarà possibile configurare l'ambiente Azure con l'aggiornamento dei dispositivi per l'hub Internet e distribuire gli aggiornamenti più recenti in Azure Percept DK.

## <a name="create-a-device-update-account"></a>Creazione di un account di aggiornamento del dispositivo

1. Passare alla [portale di Azure](https://portal.azure.com) e accedere con l'account Azure usato con Azure Percept 

1. Nella finestra di ricerca nella parte superiore della pagina, iniziare a digitare "aggiornamento del dispositivo per l'hub Internet"

1. Selezionare **aggiornamento del dispositivo per gli hub** Internet come appare nella finestra di ricerca.

1. Fare clic sul pulsante **+ Aggiungi** nella parte superiore sinistra della pagina.

1. Selezionare la sottoscrizione di Azure e il gruppo di risorse associato al dispositivo Azure Percept. si tratta della posizione in cui si trova l'hub Internet.

1. Specificare un nome e un percorso per l'account di aggiornamento del dispositivo

1. Esaminare i dettagli e quindi selezionare **Verifica + crea**.
 
1. Al termine della distribuzione, fare clic su **Vai alla risorsa**.
 
## <a name="create-a-device-update-instance"></a>Creare un'istanza di aggiornamento del dispositivo
A questo punto, creare un'istanza nell'aggiornamento del dispositivo per l'account dell'hub Internet.

1. Nella risorsa dell'aggiornamento del dispositivo per l'hub Internet, fare clic su **istanze** in **gestione istanze**.
 
1. Fare clic su **+ Crea**, specificare un nome di istanza e selezionare l'hub Internet associato al dispositivo Percept di Azure, ovvero creato durante l'esperienza di onboarding. Questa operazione richiederà qualche minuto.
 
1. Fare clic su **Crea**

## <a name="configure-iot-hub"></a>Configurare un hub IoT

1. Nella pagina **istanze** di gestione dell'istanza attendere che l'istanza di aggiornamento del dispositivo si sposti in uno stato di **esito positivo** . Fare clic sull'icona di **aggiornamento** accanto a **Elimina** per aggiornare lo stato.
 
1. Selezionare l'istanza creata per l'utente e quindi fare clic su **configura Hub** Internet. Nel riquadro sinistro selezionare **Accetto di apportare le modifiche** e fare clic su **Aggiorna**.
 
1. Attendere che il processo venga completato correttamente.
 
## <a name="configure-access-control-roles"></a>Configurare i ruoli di controllo di accesso
Il passaggio finale consentirà di concedere agli utenti le autorizzazioni per la pubblicazione e la distribuzione degli aggiornamenti.

1. Nell'aggiornamento del dispositivo per la risorsa dell'hub Internet, fare clic su **controllo di accesso (IAM)**
 
2. Fare clic su **+ Aggiungi** e quindi selezionare **Aggiungi assegnazione ruolo**
 
3. Per **ruolo** selezionare **amministratore aggiornamenti del dispositivo**. Per **assegnare l'accesso a** selezione **utente, gruppo o entità servizio**. Per **selezionare** selezionare l'account o l'account della persona che distribuirà gli aggiornamenti. Fare quindi clic su **Salva**. 

    > [!TIP]
    > Se si desidera concedere a un numero maggiore di utenti all'interno dell'organizzazione, è possibile ripetere questo passaggio e fare in modo che ognuno di questi utenti disponga di un **amministratore degli aggiornamenti dei dispositivi**.

## <a name="next-steps"></a>Passaggi successivi

A questo punto si è impostati e si può [aggiornare Azure Percept Dev Kit tramite l'](./how-to-update-over-the-air.md) aggiornamento dei dispositivi per l'hub Internet. Passare all'hub Azure Internet che si sta usando per il dispositivo Percept di Azure.