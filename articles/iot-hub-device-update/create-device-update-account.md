---
title: Creare un account di aggiornamento del dispositivo nell'aggiornamento del dispositivo per l'hub Azure Microsoft Docs
description: Creare un account di aggiornamento del dispositivo nell'aggiornamento del dispositivo per l'hub Azure.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: 5956b7b74d27a4f9a2b79ee3950c8ac765610c70
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105558483"
---
# <a name="device-update-for-iot-hub-resource-management"></a>Aggiornamento dispositivi per la gestione delle risorse dell'hub IoT

Per iniziare a usare l'aggiornamento del dispositivo, è necessario creare un account di aggiornamento del dispositivo, un'istanza e impostare i ruoli di controllo di accesso. 

## <a name="prerequisites"></a>Prerequisiti

* Accesso a un hub. Si consiglia di usare un livello S1 (standard) o superiore. 
* Browser supportati:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

## <a name="create-a-device-update-account"></a>Creazione di un account di aggiornamento del dispositivo

1. Vai a [portale di Azure](https://portal.azure.com)

2. Fare clic su Crea una risorsa e cercare "aggiornamento del dispositivo per l'hub Internet"

   :::image type="content" source="media/create-device-update-account/device-update-marketplace.png" alt-text="Screenshot dell'aggiornamento del dispositivo per la risorsa dell'hub Internet." lightbox="media/create-device-update-account/device-update-marketplace.png":::

3. Fare clic su Crea-> aggiornamento del dispositivo per l'hub Internet

4. Specificare la sottoscrizione di Azure da associare all'account di aggiornamento del dispositivo e al gruppo di risorse

5. Specificare un nome e un percorso per l'account di aggiornamento del dispositivo

   :::image type="content" source="media/create-device-update-account/account-details.png" alt-text="Screenshot dei dettagli dell'account." lightbox="media/create-device-update-account/account-details.png":::

 > [!NOTE]
 > È possibile passare alla [pagina dei prodotti Azure per area](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub) per individuare le aree in cui è disponibile l'aggiornamento del dispositivo per l'hub Internet. Se l'aggiornamento del dispositivo per l'hub Internet non è disponibile nella propria area geografica, è possibile scegliere di creare un account in un'area disponibile più vicina. 

6. Fare clic su "Avanti: verifica + crea>"

   :::image type="content" source="media/create-device-update-account/account-review.png" alt-text="Screenshot della revisione dei dettagli dell'account." lightbox="media/create-device-update-account/account-review.png":::

7. Esaminare i dettagli e quindi selezionare "Crea". Si noterà che la distribuzione è in corso. 

   :::image type="content" source="media/create-device-update-account/account-deployment-inprogress.png" alt-text="Screenshot della distribuzione dell'account in corso." lightbox="media/create-device-update-account/account-deployment-inprogress.png":::

8. Lo stato della distribuzione passerà a "completato" tra pochi minuti. Fare clic su "Vai alla risorsa"

   :::image type="content" source="media/create-device-update-account/account-complete.png" alt-text="Screenshot della distribuzione dell'account completata." lightbox="media/create-device-update-account/account-complete.png":::

## <a name="create-a-device-update-instance"></a>Creare un'istanza di aggiornamento del dispositivo 

Un'istanza dell'aggiornamento del dispositivo è associata a un singolo hub. Selezionare l'hub Internet delle cose che verrà usato con l'aggiornamento del dispositivo. Durante questo passaggio verrà creato un nuovo criterio di accesso condiviso per assicurarsi che l'aggiornamento del dispositivo usi solo le autorizzazioni necessarie per lavorare con l'hub Internet (scrittura del registro di sistema e connessione al servizio). Questo criterio garantisce che l'accesso sia limitato solo all'aggiornamento del dispositivo.

Per creare un'istanza di aggiornamento del dispositivo dopo la creazione di un account.

1. Quando ci si trova nella risorsa account appena creata, passare al pannello "istanze" di gestione dell'istanza

   :::image type="content" source="media/create-device-update-account/instance-blade.png" alt-text="Screenshot della gestione dell'istanza all'interno dell'account." lightbox="media/create-device-update-account/instance-blade.png":::

2. Fare clic su "Crea e specificare un nome di istanza e selezionare l'hub Internet.

   :::image type="content" source="media/create-device-update-account/instance-details.png" alt-text="Screenshot dei dettagli dell'istanza." lightbox="media/create-device-update-account/instance-details.png":::

   > [!NOTE] 
   > L'hub tutto collegato alla risorsa di aggiornamento del dispositivo non deve trovarsi nella stessa area dell'account di aggiornamento del dispositivo. Tuttavia, per ottenere prestazioni ottimali, è consigliabile che l'hub Internet delle cose si trovi in un'area identica o vicina all'area dell'account di aggiornamento del dispositivo. 

3. Fare clic su "Crea". L'istanza sarà visualizzata in uno stato di "creazione". 

   :::image type="content" source="media/create-device-update-account/instance-creating.png" alt-text="Screenshot della creazione dell'istanza." lightbox="media/create-device-update-account/instance-creating.png":::

4. Consente di 5-10 minuti per il completamento della distribuzione dell'istanza. Aggiornare lo stato finché non viene visualizzato lo stato "provisioning", che diventa "succeeded".

   :::image type="content" source="media/create-device-update-account/instance-succeeded.png" alt-text="Screenshot della creazione dell'istanza completata." lightbox="media/create-device-update-account/instance-succeeded.png":::

## <a name="configure-iot-hub"></a>Configurare un hub IoT 

Per consentire all'aggiornamento del dispositivo di ricevere le notifiche di modifica dall'hub Internet, l'aggiornamento del dispositivo si integra con l'hub eventi "predefinito". Facendo clic sul pulsante "configura Hub Internet" vengono configurate le route di messaggi e i criteri di accesso necessari per comunicare con i dispositivi Internet. 

Per configurare l'hub Internet

1. Una volta che l'istanza "stato provisioning" si rivolge a "succeeded", selezionare l'istanza nel pannello di gestione dell'istanza. Fare clic su "configura Hub Internet"

   :::image type="content" source="media/create-device-update-account/instance-configure.png" alt-text="Screenshot della configurazione dell'hub Internet per un'istanza." lightbox="media/create-device-update-account/instance-configure.png":::

2. Selezionare "Accetto di apportare queste modifiche"

   :::image type="content" source="media/create-device-update-account/instance-configure-selected.png" alt-text="Schermata di accettazione della configurazione dell'hub Internet per un'istanza." lightbox="media/create-device-update-account/instance-configure-selected.png":::

3. Fare clic su "Aggiorna"

[Informazioni sulle route dei messaggi configurate.](device-update-resources.md) 


## <a name="configure-access-control-roles"></a>Configurare i ruoli di controllo di accesso

Per consentire ad altri utenti di accedere all'aggiornamento del dispositivo, è necessario che agli utenti venga concesso l'accesso a questa risorsa. 

1. Passare a controllo di accesso (IAM) all'interno dell'account di aggiornamento del dispositivo

   :::image type="content" source="media/create-device-update-account/account-access-control.png" alt-text="Screenshot del controllo di accesso nell'account di aggiornamento del dispositivo." lightbox="media/create-device-update-account/account-access-control.png":::

2. Fare clic su "Aggiungi assegnazioni di ruolo"

3. In "selezionare un ruolo" selezionare un ruolo di aggiornamento del dispositivo dalle opzioni specificate
     - Amministratore degli aggiornamenti del dispositivo
     - Lettore di aggiornamento dispositivo
     - Amministratore contenuto aggiornamento dispositivo
     - Lettore contenuto aggiornamento dispositivo
     - Amministratore delle distribuzioni degli aggiornamenti del dispositivo
     - Lettore di distribuzioni di aggiornamenti del dispositivo
     
   :::image type="content" source="media/create-device-update-account/role-assignment.png" alt-text="Screenshot delle assegnazioni dei ruoli di controllo di accesso nell'account di aggiornamento del dispositivo." lightbox="media/create-device-update-account/role-assignment.png":::
    
    [Informazioni sul controllo degli accessi in base al ruolo nell'aggiornamento dei dispositivi per l'hub](device-update-control-access.md) 
    
4. Assegnare l'accesso a un utente o a un gruppo di Azure AD
5. Fare clic su Salva.
6. A questo punto si è pronti per usare l'esperienza di aggiornamento del dispositivo dall'hub Internet delle cose

## <a name="next-steps"></a>Passaggi successivi

Provare ad aggiornare un dispositivo usando una delle esercitazioni rapide seguenti:

 - [Aggiornamento del dispositivo in un simulatore](device-update-simulator.md)
 - [Aggiornamento del dispositivo in Raspberry Pi](device-update-raspberry-pi.md)
 - [Aggiornamento del dispositivo nell'agente di pacchetti di Ubuntu server 18,04 x64](device-update-ubuntu-agent.md)

[Informazioni sull'account e l'istanza di aggiornamento del dispositivo.](device-update-resources.md) 

[Informazioni sui ruoli di controllo di accesso per l'aggiornamento dei dispositivi. ](device-update-control-access.md) 

