---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 02/09/2021
ms.author: alkohli
ms.openlocfilehash: 71f18cf8448060385ea38be9b2719b1ed545c5d2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100545419"
---
> [!NOTE] 
> Non è possibile modificare le impostazioni di crittografia e le credenziali dell'account di archiviazione associate a un contenitore del volume dopo la sua creazione.

#### <a name="to-modify-a-volume-container"></a>Per modificare un contenitore di volumi

1. Passare al servizio StorSimple Gestione dispositivi, quindi passare a **gestione > contenitori del volume**.

2. Nell'elenco tabulare dei contenitori dei volumi selezionare il contenitore da modificare. Nella pagina **Dispositivi** selezionare il dispositivo, fare doppio clic su di esso, quindi fare clic sulla scheda **Contenitori di volumi**.

3. Nell'elenco tabulare dei contenitori dei volumi selezionare il contenitore da modificare. Nel pannello che viene aperto fare clic su **Modifica** sulla barra dei comandi.

    ![Modificare il contenitore di volumi](./media/storsimple-8000-modify-volume-container/modify-volume-container-01.png)

4. Nel pannello **modifica contenitore del volume** seguire questa procedura:
   
   1. Non è possibile modificare il nome, la chiave di crittografia e l'account di archiviazione associati al contenitore di volumi dopo averli specificati. Modificare l'impostazione della larghezza di banda associata.<!--STEPS NEED WORK. Updated screen doesn't show alternative to Unlimited or subsequent steps if they customize bandwidth. Can we talk them through this (briefly)?-->
      
       ![Modificare l'impostazione della larghezza di banda](./media/storsimple-8000-modify-volume-container/modify-volume-container-02.png)<!--New graphic based on: modify-volume-container-bw-setting.png-->

   1.  Fare clic su **OK**.<!--If they choose Custom, do they still click OK, or are there more steps?-->

5. Nella pagina seguente della finestra di dialogo **Modifica contenitore di volumi**:<!--This step happens only if they choose Custom bandwidth? Are the steps similar to those in "Add volume container," step 3f, above?"-->
   
   1. Dall'elenco a discesa, selezionare un modello di larghezza di banda disponibile.
   1. Fare riferimento alle impostazioni di pianificazione per il modello di larghezza di banda specificato.
   1. Fare clic su **Salva** e confermare le modifiche.
      
       ![Confermare le modifiche](./media/storsimple-8000-modify-volume-container/modify-volume-container-03.png)

      Il pannello **Contenitori di volumi** viene aggiornato per includere le modifiche apportate.
