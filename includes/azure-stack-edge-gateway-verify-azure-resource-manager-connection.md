---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/09/2021
ms.author: alkohli
ms.openlocfilehash: 1298673c475a0308f1db27641aae93837e6b5df3
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305558"
---
Assicurarsi che sia possibile usare i passaggi seguenti per accedere al dispositivo dal client.

Verificare che il client sia in grado di connettersi al Azure Resource Manager locale. 

1. Chiamare le API del dispositivo locale per l'autenticazione:

    ```powershell
    login-AzureRMAccount -EnvironmentName <Environment Name> -TenantId c0257de7-538f-415c-993a-1b87a031879d  
    ```

1. Specificare il nome utente `EdgeArmUser` e la password per la connessione tramite Azure Resource Manager. Se non si richiama la password, [reimpostare la password per Azure Resource Manager](../articles/databox-online/azure-stack-edge-gpu-set-azure-resource-manager-password.md) e usare questa password per accedere.