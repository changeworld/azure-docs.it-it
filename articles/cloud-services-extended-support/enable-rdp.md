---
title: Applicare la Desktop remoto nei servizi cloud (supporto esteso)
description: Abilitare Desktop remoto per i servizi cloud (supporto esteso)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 53f873013a6f16ce5a28ee5d915afa556057f643
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98744420"
---
# <a name="apply-the-remote-desktop-extension-to-azure-cloud-services-extended-support"></a>Applicare l'estensione Desktop remoto ai servizi cloud di Azure (supporto esteso)

Il portale di Azure usa l'estensione desktop remoto per abilitare Desktop remoto anche dopo la distribuzione dell'applicazione. Le impostazioni di desktop remoto per il servizio cloud consentono di abilitare Desktop remoto, aggiornare l'account amministratore locale, selezionare i certificati usati nell'autenticazione e impostare la data di scadenza per tali certificati. 

## <a name="apply-remote-desktop--extension"></a>Applica estensione Desktop remoto
1. Passare al servizio cloud per il quale si vuole abilitare Desktop remoto e selezionare **"Desktop remoto"** nel riquadro di spostamento a sinistra.

    :::image type="content" source="media/remote-desktop-1.png" alt-text="Immagine mostra la selezione dell'opzione Desktop remoto nell'portale di Azure":::

2. Selezionare **Aggiungi**.
3. Scegliere i ruoli per i quali abilitare Desktop remoto.
4. Compilare i campi obbligatori per nome utente, password, scadenza e certificato (non obbligatorio).

    :::image type="content" source="media/remote-desktop-2.png" alt-text="Image Mostra come inserire le informazioni necessarie per connettersi a desktop remoto.":::

5. Al termine, selezionare **Salva**. Ci vorranno alcuni minuti affinché le istanze del ruolo siano pronte a ricevere le connessioni.

## <a name="connect-to-role-instances-with-remote-desktop-enabled"></a>Connessione alle istanze del ruolo con Desktop remoto abilitata
Una volta abilitato Desktop remoto nei ruoli, è possibile avviare una connessione direttamente dalla portale di Azure.

1. Fare clic su **ruoli e istanze** per aprire le impostazioni dell'istanza.

    :::image type="content" source="media/remote-desktop-3.png" alt-text="Immagine mostra la selezione dell'opzione ruoli e istanze nel pannello configurazione.":::

2. Selezionare un'istanza del ruolo per cui è configurato desktop remoto.
3. Fare clic su **Connetti** per scaricare un file di connessione Desktop remoto.

    :::image type="content" source="media/remote-desktop-4.png" alt-text="Image Mostra la selezione dell'istanza del ruolo di lavoro nel portale di Azure.":::
    
4. Aprire il file per connettersi all'istanza del ruolo.


## <a name="next-steps"></a>Passaggi successivi 
- Esaminare i [prerequisiti di distribuzione](deploy-prerequisite.md) per i servizi cloud (supporto esteso).
- Esaminare le [domande frequenti](faq.md) per i servizi cloud (supporto esteso).
- Distribuire un servizio cloud (supporto esteso) usando il [portale di Azure](deploy-portal.md), [PowerShell](deploy-powershell.md), il [modello](deploy-template.md) o [Visual Studio](deploy-visual-studio.md).