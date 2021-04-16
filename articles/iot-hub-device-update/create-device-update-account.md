---
title: Creare un account di aggiornamento del dispositivo in Aggiornamento dispositivi per hub IoT di Azure | Microsoft Docs
description: Creare un account di aggiornamento del dispositivo in Aggiornamento dispositivi per hub IoT di Azure.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: d3f7f4e1cdd56675d6084448abc810c9a41992f9
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520140"
---
# <a name="device-update-for-iot-hub-resource-management"></a>Aggiornamento dispositivi per la gestione delle risorse dell'hub IoT

Per iniziare a usare l'aggiornamento dei dispositivi, è necessario creare un account di aggiornamento del dispositivo, un'istanza e impostare i ruoli di controllo di accesso. 

## <a name="prerequisites"></a>Prerequisiti

* Accesso a un hub IoT. È consigliabile usare un livello S1 (Standard) o superiore. 
* Browser supportati:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

## <a name="create-a-device-update-account"></a>Creare un account di aggiornamento del dispositivo

1. Passare a [portale di Azure](https://portal.azure.com)

2. Fare clic su Crea una risorsa e cercare "Aggiornamento del dispositivo per l'hub IoT"

   :::image type="content" source="media/create-device-update-account/device-update-marketplace.png" alt-text="Screenshot dell'aggiornamento del dispositivo per la risorsa dell'hub IoT." lightbox="media/create-device-update-account/device-update-marketplace.png":::

3. Fare clic su Crea -> dispositivo per l'hub IoT

4. Specificare la sottoscrizione di Azure da associare all'account di aggiornamento del dispositivo e al gruppo di risorse

5. Specificare un nome e un percorso per l'account di aggiornamento del dispositivo

   :::image type="content" source="media/create-device-update-account/account-details.png" alt-text="Screenshot dei dettagli dell'account." lightbox="media/create-device-update-account/account-details.png":::

 > [!NOTE]
 > È possibile passare alla pagina [Prodotti di Azure per](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub) area per individuare le aree in cui è disponibile l'aggiornamento dei dispositivi per l'hub IoT. Se l'aggiornamento del dispositivo per l'hub IoT non è disponibile nell'area, è possibile scegliere di creare un account in un'area disponibile più vicina. 

6. Fare clic su "Avanti: Rivedi e crea>"

   :::image type="content" source="media/create-device-update-account/account-review.png" alt-text="Screenshot della revisione dei dettagli dell'account." lightbox="media/create-device-update-account/account-review.png":::

7. Esaminare i dettagli e quindi selezionare "Crea". Verrà visualizzata la distribuzione in corso. 

   :::image type="content" source="media/create-device-update-account/account-deployment-inprogress.png" alt-text="Screenshot della distribuzione dell'account in corso." lightbox="media/create-device-update-account/account-deployment-inprogress.png":::

8. Lo stato della distribuzione verrà modificato in "completato" in pochi minuti. Fare clic su "Vai alla risorsa"

   :::image type="content" source="media/create-device-update-account/account-complete.png" alt-text="Screenshot della distribuzione dell'account completata." lightbox="media/create-device-update-account/account-complete.png":::

## <a name="create-a-device-update-instance"></a>Creare un'istanza di aggiornamento del dispositivo 

Un'istanza di Aggiornamento dispositivi è associata a un singolo hub IoT. Selezionare l'hub IoT che verrà usato con l'aggiornamento del dispositivo. Durante questo passaggio verranno creati nuovi criteri di accesso condiviso per assicurarsi che Aggiornamento dispositivi usi solo le autorizzazioni necessarie per usare l'hub IoT (scrittura del Registro di sistema e connessione al servizio). Questo criterio garantisce che l'accesso sia limitato solo all'aggiornamento del dispositivo.

Per creare un'istanza di Aggiornamento dispositivi dopo la creazione di un account.

1. Dopo aver creato la risorsa account, passare al pannello "Istanze" di Gestione istanze

   :::image type="content" source="media/create-device-update-account/instance-blade.png" alt-text="Screenshot della gestione dell'istanza all'interno dell'account." lightbox="media/create-device-update-account/instance-blade.png":::

2. Fare clic su "Crea e specificare un nome di istanza e selezionare l'hub IoT

   :::image type="content" source="media/create-device-update-account/instance-details.png" alt-text="Screenshot dei dettagli dell'istanza." lightbox="media/create-device-update-account/instance-details.png":::

   > [!NOTE] 
   > L'hub IoT che si collega alla risorsa Aggiornamento dispositivi non deve essere nella stessa area dell'account di aggiornamento del dispositivo. Tuttavia, per prestazioni migliori, è consigliabile che l'hub IoT si trova in un'area uguale o vicina all'area dell'account di aggiornamento del dispositivo. 

3. Fare clic su "Crea". L'istanza verrà visualizzata in uno stato "Creazione". 

   :::image type="content" source="media/create-device-update-account/instance-creating.png" alt-text="Screenshot della creazione dell'istanza." lightbox="media/create-device-update-account/instance-creating.png":::

4. Consentire da 5 a 10 minuti per il completamento della distribuzione dell'istanza. Aggiornare lo stato finché non viene visualizzato il turno "Stato provisioning" su "Operazione completata".

   :::image type="content" source="media/create-device-update-account/instance-succeeded.png" alt-text="Screenshot della creazione dell'istanza completata." lightbox="media/create-device-update-account/instance-succeeded.png":::

## <a name="configure-iot-hub"></a>Configurare un hub IoT 

Per consentire a Aggiornamento dispositivi di ricevere notifiche di modifica dall'hub IoT, l'aggiornamento del dispositivo si integra con l'hub eventi "incorporato". Facendo clic sul pulsante "Configura hub IoT" vengono configurate le route dei messaggi e i criteri di accesso necessari per comunicare con i dispositivi IoT. 

Per configurare l'hub IoT

1. Quando "Stato provisioning" dell'istanza diventa "Operazione completata", selezionare l'istanza nel pannello Gestione istanze. Fare clic su "Configura hub IoT"

   :::image type="content" source="media/create-device-update-account/instance-configure.png" alt-text="Screenshot della configurazione dell'hub IoT per un'istanza." lightbox="media/create-device-update-account/instance-configure.png":::

2. Selezionare "Accetto di apportare queste modifiche"

   :::image type="content" source="media/create-device-update-account/instance-configure-selected.png" alt-text="Screenshot che illustra come configurare l'hub IoT per un'istanza." lightbox="media/create-device-update-account/instance-configure-selected.png":::

3. Fare clic su "Aggiorna"

   > [!NOTE] 
   > Se si usa un livello gratuito di hub IoT di Azure, il numero consentito di route dei messaggi è limitato a 5. L'aggiornamento del dispositivo per l'hub IoT deve configurare 4 route di messaggi per il funzionamento previsto. 

[Informazioni sulle route dei messaggi configurate.](device-update-resources.md) 


## <a name="configure-access-control-roles"></a>Configurare i ruoli di controllo di accesso

Per consentire ad altri utenti di accedere a Aggiornamento dispositivi, agli utenti deve essere concesso l'accesso a questa risorsa. 

1. Passare a Controllo di accesso (IAM) all'interno dell'account di aggiornamento del dispositivo

   :::image type="content" source="media/create-device-update-account/account-access-control.png" alt-text="Screenshot del controllo di accesso all'interno dell'account di aggiornamento del dispositivo." lightbox="media/create-device-update-account/account-access-control.png":::

2. Fare clic su "Aggiungi assegnazioni di ruolo"

3. In "Seleziona un ruolo" selezionare un ruolo Di aggiornamento del dispositivo tra le opzioni specificate
     - Amministratore dell'aggiornamento dei dispositivi
     - Lettore di aggiornamenti del dispositivo
     - Amministratore del contenuto dell'aggiornamento del dispositivo
     - Lettore di contenuto per l'aggiornamento del dispositivo
     - Amministratore delle distribuzioni degli aggiornamenti dei dispositivi
     - Lettore distribuzioni aggiornamenti dispositivi
     
   :::image type="content" source="media/create-device-update-account/role-assignment.png" alt-text="Screenshot delle assegnazioni di ruolo controllo di accesso all'interno dell'account di aggiornamento del dispositivo." lightbox="media/create-device-update-account/role-assignment.png":::
    
    [Informazioni sul controllo degli accessi in base al ruolo in Aggiornamento dei dispositivi per l'hub IoT](device-update-control-access.md) 
    
4. Assegnare l'accesso a un utente o Azure AD gruppo
5. Fare clic su Salva.
6. È ora possibile usare l'esperienza di aggiornamento del dispositivo dall'hub IoT

## <a name="next-steps"></a>Passaggi successivi

Provare ad aggiornare un dispositivo usando una delle esercitazioni rapide seguenti:

 - [Aggiornamento del dispositivo in un simulatore](device-update-simulator.md)
 - [Aggiornamento del dispositivo in Raspberry Pi](device-update-raspberry-pi.md)
 - [Aggiornamento del dispositivo in Ubuntu Server 18.04 x64 Package Agent](device-update-ubuntu-agent.md)

[Informazioni sull'account e sull'istanza di Aggiornamento del dispositivo.](device-update-resources.md) 

[Informazioni sui ruoli di controllo di accesso degli aggiornamenti del dispositivo. ](device-update-control-access.md) 

