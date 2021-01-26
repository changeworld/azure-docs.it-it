---
title: Distribuire il ruolo Gateway Desktop remoto Windows desktop virtuale-Azure
description: Come distribuire il ruolo Gateway Desktop remoto nel desktop virtuale di Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 01/26/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: ca3c36f339d5cbca6d5b5990415fba180ae42a13
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98798435"
---
# <a name="deploy-the-rd-gateway-role-in-windows-virtual-desktop"></a>Distribuire il ruolo Gateway Desktop remoto in desktop virtuale di Windows

Questo articolo illustra come distribuire i server Gateway Desktop remoto nell'ambiente in uso. Puoi installare i ruoli del server in computer fisici o macchine virtuali, a seconda che tu stia creando un ambiente locale, basato sul cloud o ibrido.

## <a name="install-the-rd-gateway-role"></a>Installare il ruolo Gateway Desktop remoto

1. Accedere al server di destinazione con credenziali amministrative.

2. In **Server Manager** selezionare **Gestisci** e quindi selezionare **Aggiungi ruoli e funzionalità**. Si aprirà il programma **di installazione Aggiungi ruoli e funzionalità** .

3. In **Operazioni preliminari** selezionare **Avanti**.

4. In **Selezione tipo di installazione** selezionare **installazione basata su ruoli o basata su funzionalità** e quindi fare clic su **Avanti**.

5. Per **Selezione server di destinazione** selezionare **selezionare un server dal pool di server**. Per **pool di server**, selezionare il nome del computer locale. Al termine, selezionare **Avanti**.

6. In **selezione**  >  **ruoli** server selezionare **Servizi Desktop remoto**. Al termine, selezionare **Avanti**.

7. In **Servizi Desktop remoto** selezionare **Avanti.**

8. Per **Selezione servizi ruolo** selezionare solo **Desktop remoto Gateway** quando viene richiesto di aggiungere le funzionalità necessarie, selezionare **Aggiungi funzionalità**. Al termine, selezionare **Avanti**.

9. Per **servizi di accesso e criteri di rete** selezionare **Avanti**.

10. Per **ruolo server Web (IIS)**, fare clic su **Avanti**.

11. Per **Selezione servizi ruolo** selezionare **Avanti**.

12. Per **Conferma selezioni** per l'installazione, selezionare **Installa**. Non chiudere il programma di installazione mentre è in corso il processo di installazione.

## <a name="configure-rd-gateway-role"></a>Configurare il ruolo Gateway Desktop remoto

Una volta installato il ruolo Gateway Desktop remoto, è necessario configurarlo.

Per configurare il ruolo Gateway Desktop remoto:

1. Aprire il **Server Manager**, quindi selezionare **Servizi Desktop remoto**.

2. Passare a **Server**, fare clic con il pulsante destro del mouse sul nome del server, quindi selezionare **Gestione Gateway Desktop remoto**.

3. In Gestione Gateway Desktop remoto fare clic con il pulsante destro del mouse sul nome del gateway, quindi scegliere **Proprietà**.

4. Aprire la scheda **certificato SSL** , selezionare la bolla **Importa un certificato in Gateway Desktop remoto** , quindi selezionare **Sfoglia e importa certificato...**.

5. Selezionare il nome del file PFX, quindi selezionare **Apri**.

6. Quando richiesto, immettere la password per il file PFX.

7. Dopo aver importato il certificato e la relativa chiave privata, verranno visualizzati gli attributi chiave del certificato.

>[!NOTE]
>Poiché il ruolo Gateway Desktop remoto deve essere pubblico, è consigliabile utilizzare un certificato rilasciato pubblicamente. Se si utilizza un certificato rilasciato privatamente, è necessario assicurarsi di configurare tutti i client con la catena di certificati del certificato in anticipo.

## <a name="next-steps"></a>Passaggi successivi

Per aggiungere la disponibilità elevata al ruolo Gateway Desktop remoto, vedere la pagina relativa all' [aggiunta di disponibilità elevata al server Web front-end Web e Gateway Desktop remoto](/windows-server/remote/remote-desktop-services/rds-rdweb-gateway-ha).