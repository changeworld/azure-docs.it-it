---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 02/09/2021
ms.author: alkohli
ms.openlocfilehash: 23ce17844a0113f63931c6ece7d36bfefedc2de5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100551467"
---
#### <a name="to-add-a-storsimple-backup-policy"></a>Per aggiungere un criterio di backup per StorSimple

1. Andare al dispositivo StorSimple e fare clic su **Criteri di backup**.

2. Nel pannello **Criteri di backup** fare clic su **Aggiungi criteri** dalla barra dei comandi.
   
    ![Aggiungere un criterio di backup](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-01.png)

3. Nel pannello **Creare i criteri di backup** seguire questa procedura:
   
   1. **Seleziona dispositivo** viene popolato automaticamente in base al dispositivo selezionato.
   
   2. Specificare il nome di un **criterio** di backup che include da 3 a 150 caratteri. Dopo essere stati creati, i criteri non possono essere rinominati.
       
   3. Per assegnare volumi ai criteri di backup, selezionare **Aggiungi volumi** e nell'elenco tabulare dei volumi fare clic sulle caselle di controllo per assegnare uno o più volumi ai criteri di backup.

       ![Aggiungere un criterio di backup 2](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-02.png)<!--Replacement screen source: create-backup-policy-addvolumes.png-->

   4. Per definire una pianificazione per i criteri di backup, fare clic su **Prima pianificazione** e quindi modificare i parametri seguenti:<!--Do the substeps remain the same? Can they follow without a screenshot?-->

       ![Aggiungere un criterio di backup 3](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-03.png)<!--Replacement screen source: create-backup-policy-first-schedule.png-->

       1. Per **Tipo di snapshot** selezionare **Cloud** o **Locale**.

       2. Indicare la frequenza dei backup (specificare un numero, quindi scegliere **giorni** o **settimane** dall'elenco a discesa.

       3. Immettere una pianificazione di conservazione.

       4. Immettere una data e ora di inizio per il criterio di backup.

       5. Fare clic su **OK** per definire la pianificazione.

   5. Fare clic su **Crea** per creare criteri di backup.
   
   6. Dopo la creazione dei criteri di backup si riceve una notifica. I criteri appena aggiunti vengono visualizzati nella vista tabulare del pannello **Criteri di backup**.

       ![Aggiungere un criterio di backup 5](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-07.png)
