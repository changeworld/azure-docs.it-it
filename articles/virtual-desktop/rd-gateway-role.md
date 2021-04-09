---
title: Distribuire il ruolo Gateway Desktop remoto Windows desktop virtuale-Azure
description: Come distribuire il ruolo Gateway Desktop remoto nel desktop virtuale di Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 01/30/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 71bd7d38727d99c05a15c54e5141c613960d9050
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99220820"
---
# <a name="deploy-the-rd-gateway-role-in-windows-virtual-desktop-preview"></a>Distribuire il ruolo Gateway Desktop remoto in desktop virtuale Windows (anteprima)

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questo articolo illustra come usare il ruolo Gateway Desktop remoto (anteprima) per distribuire i server Gateway Desktop remoto nell'ambiente in uso. È possibile installare i ruoli del server in computer fisici o macchine virtuali a seconda che si stia creando un ambiente locale, basato sul cloud o ibrido.

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