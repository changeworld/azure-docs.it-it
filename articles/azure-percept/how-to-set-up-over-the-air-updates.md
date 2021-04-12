---
title: Configurare l'hub Azure per la distribuzione di aggiornamenti in tempo
description: Informazioni su come configurare l'hub Azure per la distribuzione degli aggiornamenti in Azure Percept DK
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/30/2021
ms.custom: template-how-to
ms.openlocfilehash: 5890ca90b0ad0dcb3d5141e62e986475fd386959
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106064121"
---
# <a name="how-to-set-up-azure-iot-hub-to-deploy-over-the-air-updates-to-your-azure-percept-dk"></a>Come configurare l'hub Azure per la distribuzione in Azure Percept DK

Mantieni le tue esigenze di Azure Percept DK sicure e aggiornate usando gli aggiornamenti in modalità wireless. Con pochi semplici passaggi, sarà possibile configurare l'ambiente Azure con l'aggiornamento dei dispositivi per l'hub Internet e distribuire gli aggiornamenti più recenti in Azure Percept DK.

## <a name="prerequisites"></a>Prerequisiti

- Azure Percept DK (DevKit)
- [Sottoscrizione di Azure](https://azure.microsoft.com/free/)
- [Esperienza di installazione di Azure PERCEPT dk](./quickstart-percept-dk-set-up.md): il kit di sviluppo è stato connesso a una rete di Wi-Fi, è stato creato un hub Internet e il kit dev è stato connesso all'hub Internet.

## <a name="create-a-device-update-account"></a>Creazione di un account di aggiornamento del dispositivo

1. Passare alla [portale di Azure](https://portal.azure.com) e accedere con l'account Azure usato con Azure Percept.

1. Nella barra di ricerca nella parte superiore della pagina immettere l' **aggiornamento del dispositivo per l'hub** Internet.

1. Selezionare **aggiornamento del dispositivo per l'hub** Internet quando viene visualizzato nella barra di ricerca.

1. Fare clic sul pulsante **+ Aggiungi** nella parte superiore sinistra della pagina.

1. Selezionare la **sottoscrizione di Azure** e il **gruppo di risorse** associato al dispositivo Azure Percept e all'hub Internet.

1. Specificare un **nome** e un **percorso** per l'account di aggiornamento del dispositivo.

1. Esaminare i dettagli e selezionare **Verifica + crea**.

1. Al termine della distribuzione, fare clic su **Vai alla risorsa**.

## <a name="create-a-device-update-instance"></a>Creare un'istanza di aggiornamento del dispositivo

1. Nella risorsa dell'aggiornamento del dispositivo per l'hub Internet, fare clic su **istanze** in **gestione istanze**.

1. Fare clic su **+ Crea**, specificare un nome di istanza e selezionare l'hub Internet associato al dispositivo Azure Percept. Questa operazione richiederà qualche minuto.

1. Fare clic su **Crea**.

## <a name="configure-iot-hub"></a>Configurare un hub IoT

1. Nella pagina **istanze** di gestione dell'istanza attendere che l'istanza di aggiornamento del dispositivo si sposti in uno stato di **esito positivo** . Fare clic **sull'icona di aggiornamento per** aggiornare lo stato.

1. Selezionare l'istanza che è stata creata e fare clic su **configura Hub** Internet. Nel riquadro sinistro selezionare **Accetto di apportare le modifiche** e fare clic su **Aggiorna**.

1. Attendere che il processo venga completato correttamente.

## <a name="configure-access-control-roles"></a>Configurare i ruoli di controllo di accesso

Il passaggio finale consentirà di concedere agli utenti le autorizzazioni per la pubblicazione e la distribuzione degli aggiornamenti.

1. Nell'aggiornamento del dispositivo per la risorsa dell'hub Internet, fare clic su **controllo di accesso (IAM)**.

1. Fare clic su **+ Aggiungi** e quindi selezionare **Aggiungi assegnazione ruolo**.

1. Per **ruolo** selezionare **amministratore aggiornamenti del dispositivo**. Per **assegnare l'accesso a** selezione **utente, gruppo o entità servizio**. Per **Select** selezionare l'account o l'account della persona che distribuirà gli aggiornamenti. Fare clic su **Salva**.

> [!TIP]
> Se si desidera concedere a un numero maggiore di utenti all'interno dell'organizzazione, è possibile ripetere questo passaggio e fare in modo che ognuno di questi utenti disponga di un **amministratore degli aggiornamenti dei dispositivi**.

## <a name="next-steps"></a>Passaggi successivi

A questo punto è possibile [aggiornare Azure Percept Dev Kit con l'](./how-to-update-over-the-air.md) aggiornamento dei dispositivi per l'hub Internet.