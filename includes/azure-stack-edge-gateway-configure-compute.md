---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/07/2021
ms.author: alkohli
ms.openlocfilehash: c51577882e75facb1d8eb03c7cfab82467c5ec51
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99524926"
---
Per configurare il calcolo in Azure Stack Edge Pro, si creerà una risorsa hub IoT tramite il portale di Azure.

1. Nel portale di Azure della risorsa Azure Stack Edge passare a **Panoramica** e selezionare **IoT Edge**.

   ![Introduzione al calcolo](./media/azure-stack-edge-gateway-configure-compute/configure-compute-1.png)

2. In **Abilita servizio IoT Edge** selezionare **Aggiungi**.

   ![Configurare il calcolo](./media/azure-stack-edge-gateway-configure-compute/configure-compute-2.png)

3. Nel pannello **Configura calcolo Edge** immettere le informazioni seguenti:
   
    |Campo  |Valore  |
    |---------|---------|
    |Subscription     |Selezionare una sottoscrizione per la risorsa hub IoT. È possibile usare la stessa sottoscrizione usata dalla risorsa Azure Stack Edge.         |
    |Gruppo di risorse     |Selezionare un gruppo di risorse per la risorsa hub IoT. È possibile usare lo stesso gruppo di risorse usato dalla risorsa Azure Stack Edge.         |
    |Hub IoT     | Scegliere **Nuovo** o **Esistente**. <br> Per impostazione predefinita, per creare una risorsa IoT viene usato un livello Standard (S1). Per usare una risorsa IoT di un livello gratuito, crearne uno e quindi selezionare la risorsa esistente. <br> In ogni caso, la risorsa hub IoT usa la stessa sottoscrizione e lo stesso gruppo di risorse usati dalla risorsa Azure Stack Edge.     |
    |Nome     |Accettare il nome predefinito o immettere un nome per la risorsa dell'hub Internet delle cose.         |

   ![Introduzione al calcolo 2](./media/azure-stack-edge-gateway-configure-compute/configure-compute-3.png)

4. Dopo aver completato le impostazioni, selezionare **Rivedi e crea**. Verificare le impostazioni della risorsa hub IoT e selezionare **Crea**.

   La creazione di una risorsa hub IoT richiede alcuni minuti. Una volta creata la risorsa, la pagina **Panoramica** indica che il servizio IoT Edge è ora in esecuzione.

   ![Introduzione al calcolo 3](./media/azure-stack-edge-gateway-configure-compute/configure-compute-4.png)

5. Per verificare che il ruolo di calcolo perimetrale sia stato configurato, passare a **IoT Edge > proprietà**.

   ![Introduzione al calcolo 4](./media/azure-stack-edge-gateway-configure-compute/configure-compute-5.png)

   Quando il ruolo di calcolo Edge è configurato nel dispositivo Edge, crea due dispositivi: un dispositivo IoT e un dispositivo IoT Edge. Entrambi i dispositivi possono essere visualizzati nella risorsa dell'hub IoT. Nel dispositivo IoT Edge viene eseguito anche un runtime IoT Edge. Attualmente per il dispositivo IoT Edge è disponibile solo la piattaforma Linux.

Per configurare il calcolo possono essere necessari 20-30 minuti, dato che in background vengono creati un cluster Kubernetes e le macchine virtuali.

Dopo aver configurato correttamente il calcolo nel portale di Azure, sono disponibili un cluster Kubernetes e un utente predefinito associato allo spazio dei nomi IoT (spazio dei nomi di sistema controllato da Azure Stack Edge Pro).
