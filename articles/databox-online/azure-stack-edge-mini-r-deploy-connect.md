---
title: Esercitazione per connettersi ad Azure Stack Edge Mini R nel portale di Azure
description: Informazioni su come connettersi al dispositivo Azure Stack Edge Mini R usando l'interfaccia utente Web locale.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/20/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Mini R so I can use it to transfer data to Azure.
ms.openlocfilehash: fe76391a5cfce8d7d39e47131db108ab87e5aed5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96464997"
---
# <a name="tutorial-connect-to-azure-stack-edge-mini-r"></a>Esercitazione: Connettersi ad Azure Stack Edge Mini R

Questa esercitazione descrive come connettersi al dispositivo Azure Stack Edge Mini R usando l'interfaccia utente Web locale.

Per il completamento del processo di connessione sono necessari circa 5 minuti.

Questa esercitazione descrive quanto segue:

> [!div class="checklist"]
>
> * Prerequisiti
> * Connettersi a un dispositivo fisico



## <a name="prerequisites"></a>Prerequisiti

Prima di configurare il dispositivo Azure Stack Edge, assicurarsi di:

* Aver installato il dispositivo fisico come descritto in [Installare Azure Stack Edge](azure-stack-edge-mini-r-deploy-install.md).


## <a name="connect-to-the-local-web-ui-setup"></a>Connettersi all'installazione dell'interfaccia utente Web locale

1. Configurare l'adattatore Ethernet nel computer per connettersi al dispositivo Azure Stack Edge Pro con l'indirizzo IP statico 192.168.100.5 e la subnet 255.255.255.0.

2. Collegare il computer alla porta 1 sul dispositivo. Se si connette direttamente il computer al dispositivo (senza switch), usare un cavo crossover o una scheda Ethernet USB. Usare la figura seguente per identificare PORT 1 sul dispositivo.

    ![Cablaggio per la rete Wi-Fi](./media/azure-stack-edge-mini-r-deploy-install/wireless-cabled.png)

[!INCLUDE [azure-stack-edge-gateway-delpoy-connect](../../includes/azure-stack-edge-gateway-deploy-connect.md)]


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati trattati gli argomenti seguenti:

> [!div class="checklist"]
> * Prerequisiti
> * Connettersi a un dispositivo fisico


Per informazioni su come configurare le impostazioni di rete nel dispositivo, vedere:

> [!div class="nextstepaction"]
> [Configurare la rete](./azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md)
