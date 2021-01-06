---
title: Eseguire un modulo GPU sul dispositivo GPU Pro Microsoft Azure Stack Edge | Microsoft Docs
description: Viene descritto come configurare ed eseguire un modulo in GPU in un dispositivo Azure Stack Edge Pro tramite il portale di Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/04/2021
ms.author: alkohli
ms.openlocfilehash: d172ce98ba93360c621a91fb0e2a55d022470943
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935561"
---
# <a name="configure-and-run-a-module-on-gpu-on-azure-stack-edge-pro-device"></a>Configurare ed eseguire un modulo in GPU sul dispositivo Azure Stack Edge Pro

Il dispositivo Azure Stack Edge Pro contiene una o più unità di elaborazione grafica (GPU). Le GPU sono una scelta comune per i calcoli di intelligenza artificiale, perché offrono funzionalità di elaborazione parallela e sono più veloci al rendering delle immagini rispetto alle unità di elaborazione centrali (CPU). Per altre informazioni sulla GPU contenuta nel dispositivo Azure Stack Edge Pro, vedere le [specifiche tecniche del dispositivo Azure stack Edge Pro](azure-stack-edge-gpu-technical-specifications-compliance.md).

Questo articolo descrive come configurare ed eseguire un modulo sulla GPU sul dispositivo Azure Stack Edge Pro. In questo articolo si useranno le **cifre** del modulo contenitore disponibili pubblicamente scritte per le GPU NVIDIA T4. Questa procedura può essere usata per configurare qualsiasi altro modulo pubblicato da NVIDIA per queste GPU.


## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare che:

1. È possibile accedere a un dispositivo a 1 nodo Azure Stack Edge Pro con GPU abilitata. Questo dispositivo viene attivato con una risorsa in Azure.  

## <a name="configure-module-to-use-gpu"></a>Configurare un modulo per l'uso della GPU

Per configurare un modulo per l'uso della GPU sul dispositivo Azure Stack Edge Pro per eseguire un modulo,<!--Can it be simplified? "To configure a module to be run by the GPU on your Azure Stack Edge Pro device,"?--> attenersi alla seguente procedura.

1. Nella portale di Azure passare alla risorsa associata al dispositivo.

2. In **Panoramica** selezionare **IOT Edge**.

    ![Configurare il modulo per l'uso della GPU 1](media/azure-stack-edge-j-series-configure-gpu-modules/configure-compute-1.png)

3. In **Abilita servizio IOT Edge** selezionare **Aggiungi**.

   ![Configurare il modulo per l'uso della GPU 2](media/azure-stack-edge-j-series-configure-gpu-modules/configure-compute-2.png)

4. In **Create IOT Edge service** immettere le impostazioni per la risorsa dell'hub Internet delle cose:

   |Campo   |Valore    |
   |--------|---------|
   |Sottoscrizione      | Sottoscrizione usata dalla risorsa Azure Stack Edge. |
   |Gruppo di risorse    | Gruppo di risorse usato dalla risorsa Azure Stack Edge. |
   |Hub IoT           | Scegliere **Crea nuovo** o **Usa esistente**. <br> Per impostazione predefinita, per creare una risorsa IoT viene usato un livello Standard (S1). Per usare una risorsa IoT di un livello gratuito, crearne uno e quindi selezionare la risorsa esistente. <br> In ogni caso, la risorsa hub IoT usa la stessa sottoscrizione e lo stesso gruppo di risorse usati dalla risorsa Azure Stack Edge.     |
   |Nome              | Se non si vuole usare il nome predefinito specificato per una nuova risorsa dell'hub Internet, immettere un nome diverso. |

   Al termine delle impostazioni, selezionare **Verifica + crea**. Esaminare le impostazioni per la risorsa dell'hub Internet e selezionare **Crea**.

   ![Introduzione al calcolo 2](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-3.png)

   La creazione di risorse per una risorsa dell'hub Internet richiede diversi minuti. Dopo la creazione della risorsa, la **Panoramica** indica che il servizio IOT Edge è in esecuzione.

   ![Introduzione al calcolo 3](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-4.png)

5. Per verificare che sia stato configurato il ruolo di calcolo perimetrale, selezionare **Proprietà**.

   ![Introduzione al calcolo 4](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-5.png)

6. In **Proprietà** selezionare il collegamento per **IOT Edge dispositivo**.

   ![Configurare il modulo per l'uso della GPU 6](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-2.png)

   Nel riquadro di destra viene visualizzato il dispositivo IoT Edge associato al dispositivo Azure Stack Edge Pro. Questo dispositivo corrisponde al dispositivo IoT Edge creato durante la creazione della risorsa dell'hub Internet.
 
7. Selezionare questo dispositivo IoT Edge.

   ![Configurare il modulo per l'uso della GPU 7](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-3.png)

8. Selezionare **Imposta moduli**.

   ![Configurare il modulo per l'uso della GPU 8](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-4.png)

9. Selezionare **+ Aggiungi** e quindi selezionare **+ IOT Edge modulo**. 

    ![Configurare il modulo per l'uso della GPU 9](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-5.png)

10. Nella scheda **aggiungi IOT Edge modulo** :

    1. Specificare l' **URI dell'immagine**. Qui si useranno le **cifre** del modulo NVIDIA disponibili pubblicamente. 
    
    2. Impostare il **criterio di riavvio** su **Always**.
    
    3. Impostare **lo stato desiderato** su **in esecuzione**.
    
    ![Configurare il modulo per l'uso della GPU 10](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-6.png)

11. Nella scheda **variabili di ambiente** specificare il nome della variabile e il valore corrispondente. 

    1. Per fare in modo che il modulo corrente usi una GPU in questo dispositivo, usare la NVIDIA_VISIBLE_DEVICES. 

    2. Impostare il valore su 0 o 1. Il valore 0 o 1 garantisce che almeno una GPU venga usata dal dispositivo per questo modulo. Quando si imposta il valore su 0, 1, significa che entrambe le GPU del dispositivo sono utilizzate da questo modulo.

       ![Configurare il modulo per l'uso della GPU 11](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-7.png)

       Per altre informazioni sulle variabili di ambiente che è possibile usare con la GPU NVIDIA, vedere [runtime del contenitore NVIDIA](https://github.com/NVIDIA/nvidia-container-runtime#environment-variables-oci-spec).

    > [!NOTE]
    > È possibile eseguire il mapping di una GPU solo a un modulo. Un modulo può tuttavia usare una o nessuna GPU.

12. Immettere un nome per il modulo. A questo punto è possibile scegliere di specificare l'opzione di creazione del contenitore e modificare le impostazioni del modulo gemello o, se necessario, selezionare **Aggiungi**. 

    ![Configurare il modulo per l'uso della GPU 12](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-8.png)

13. Verificare che il modulo sia in esecuzione e selezionare **Verifica + crea**.

    ![Configurare il modulo per l'uso della GPU 13](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-9.png)

14. Nella scheda **Verifica e crea** sono visualizzate le opzioni di distribuzione selezionate. Esaminare le opzioni e selezionare **Crea**.
    
    ![Configurare il modulo per l'uso della GPU 14](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-10.png)

15. Prendere nota dello stato di **Runtime** del modulo.
    
    ![Configurare il modulo per l'uso della GPU 15](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-11.png)

    Sono necessari alcuni minuti per la distribuzione del modulo. Selezionare **Aggiorna** per visualizzare l'aggiornamento **dello stato di runtime** per l' **esecuzione**.

    ![Configurare il modulo per l'uso della GPU 16](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-12.png)


## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulle [variabili di ambiente che è possibile usare con la GPU NVIDIA](https://github.com/NVIDIA/nvidia-container-runtime#environment-variables-oci-spec).
