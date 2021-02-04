---
title: includere file
description: includere file
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 04/19/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 9151a84aa03e9b87b02278672622d458fbc5281e
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99538712"
---
Per creare un hub IoT usando il portale di Azure:

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Dalla Home page di Azure selezionare **Crea una risorsa** e quindi immettere *Hub* tutto in **Cerca nel Marketplace**.

1. Nei risultati della ricerca selezionare **Hub IoT** e quindi fare clic su **Crea**.

1. Nella scheda **Informazioni di base** completare i campi come indicato di seguito:

   - **Sottoscrizione** Selezionare la sottoscrizione da usare per l'hub.

   - **Gruppo di risorse**: selezionare un gruppo di risorse o crearne uno nuovo. Per crearne uno nuovo, fare clic su **Crea nuovo** e specificare il nome da usare. Per usare un gruppo di risorse esistente, selezionarlo. Per altre informazioni, vedere l'articolo su come [gestire gruppi di risorse di Azure Resource Manager](../articles/azure-resource-manager/management/manage-resource-groups-portal.md). Questa esercitazione usa il nome **tutorials-iot-hub-rg**.

   - **Area**: selezionare l'area in cui si vuole collocare l'hub. Selezionare la località più vicina. Alcune funzionalità, ad esempio i [flussi dei dispositivi dell'hub IoT](../articles/iot-hub/iot-hub-device-streams-overview.md), sono disponibili solo in aree specifiche. Per queste funzionalità limitate, è necessario selezionare una delle aree supportate. Questa esercitazione usa l'area **Stati Uniti occidentali** .

   - **Nome hub IoT**: immettere un nome per l'hub. Il nome deve essere univoco a livello globale. Questa esercitazione usa le **esercitazioni-l'hub**. È necessario scegliere un nome univoco quando si crea l'hub.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-basics.png" alt-text="Creare un hub nel portale di Azure":::

1. Selezionare **Avanti: Rete** per continuare a creare l'hub.

   Scegliere gli endpoint che possono connettersi all'hub IoT. È possibile selezionare l'impostazione predefinita **Endpoint pubblico (tutte le reti)** oppure scegliere **Endpoint pubblico (intervalli IP selezionati)** o **Endpoint privato**. Accettare l'impostazione predefinita per questa esercitazione.

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-networking.png" alt-text="Scegliere gli endpoint che possono connettersi.":::

1. Selezionare **Avanti: Gestione** per continuare a creare l'hub.

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-management.png" alt-text="Imposta la dimensione e la scala per un nuovo hub usando il portale di Azure.":::

   In questa schermata è possibile accettare le impostazioni predefinite. Se si preferisce, è possibile modificare uno dei campi seguenti:

   - **Piano tariffario e livello di scalabilità**: il piano e il livello selezionati. Scegliere il livello gratuito. Il livello gratuito è utilizzabile a scopo di test e valutazione. Consente la connessione di 500 dispositivi all'hub e un massimo di 8.000 messaggi al giorno. Per ogni sottoscrizione di Azure è possibile creare un solo hub IoT nel livello gratuito.

   - **Unità di hub IoT**: Il numero di messaggi consentiti per unità al giorno dipende dal piano tariffario dell'hub. Se ad esempio si vuole che l'hub supporti 700.000 messaggi in ingresso, selezionare due unità del piano S1.
   Per ogni sottoscrizione di Azure è possibile creare un solo hub IoT nel livello gratuito. Per informazioni dettagliate sulle altre opzioni relative al livello, vedere [Scegliere il livello più adatto di hub IoT](../articles/iot-hub/iot-hub-scaling.md).

   - **Defender per IoT**: attivare questa opzione per aggiungere un livello aggiuntivo di protezione dalle minacce a IoT e ai dispositivi. Questa opzione non è disponibile per gli hub del livello gratuito. Per altre informazioni su questa funzionalità, vedere [Centro sicurezza di Azure per IoT](/azure/asc-for-iot/).

   - **Impostazioni avanzate** > **Partizioni da dispositivo a cloud**: questa proprietà associa i messaggi da dispositivo a cloud al numero di lettori simultanei di tali messaggi. La maggior parte degli hub richiede solo quattro partizioni. Un hub di livello gratuito è limitato a due partizioni.

1. Selezionare **Avanti: Tag** per passare alla schermata successiva.

   I tag sono coppie nome/valore. È possibile assegnare lo stesso tag a più risorse e gruppi di risorse per classificare le risorse e consolidare la fatturazione. Per altre informazioni, vedere [Usare tag per organizzare le risorse di Azure](../articles/azure-resource-manager/management/tag-resources.md).

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-tags.png" alt-text="Assegnare i tag per l'hub usando il portale di Azure.":::

1. Selezionare **Avanti: Rivedi e crea** per rivedere le scelte effettuate. Viene visualizzata una schermata simile a questa, ma con i valori selezionati durante la creazione dell'hub.

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-create.png" alt-text="Esaminare le informazioni per la creazione del nuovo hub.":::

1. Prendere nota del nome dell'hub IoT scelto. Questo valore verrà usato più avanti nell'esercitazione.
