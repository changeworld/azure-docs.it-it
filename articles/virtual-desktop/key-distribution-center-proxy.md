---
title: Configurare Kerberos Centro distribuzione chiavi proxy desktop virtuale Windows-Azure
description: Come configurare un pool host di desktop virtuali Windows per l'uso di un proxy Centro distribuzione chiavi Kerberos.
author: Heidilohr
ms.topic: how-to
ms.date: 01/30/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 102ddc1c8937c66a92416ddb6d5f2d25f2a3c349
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99219656"
---
# <a name="configure-a-kerberos-key-distribution-center-proxy-preview"></a>Configurare un proxy di Centro distribuzione chiavi Kerberos (anteprima)

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

In questo articolo viene illustrato come configurare un proxy di Centro distribuzione chiavi Kerberos (KDC) (anteprima) per il pool host. Questo proxy consente alle organizzazioni di eseguire l'autenticazione con Kerberos al di fuori dei confini aziendali. Ad esempio, è possibile usare il proxy KDC per abilitare l'autenticazione con smart card per i client esterni.

## <a name="how-to-configure-the-kdc-proxy"></a>Come configurare il proxy KDC

Per configurare il proxy KDC:

1. Accedre al portale di Azure come amministratore.

2. Passare alla pagina desktop virtuale di Windows.

3. Selezionare il pool di host per il quale si desidera abilitare il proxy KDC, quindi selezionare **Proprietà RDP**.

    > [!div class="mx-imgBorder"]
    > ![Screenshot della pagina portale di Azure che mostra un utente che seleziona pool host, quindi il nome del pool di host di esempio e le proprietà RDP.](media/rdp-properties.png)

4. Selezionare la scheda **Avanzate** , quindi immettere un valore nel formato seguente senza spazi:

    > kdcproxyname: s:\<fqdn\>

    > [!div class="mx-imgBorder"]
    > ![Screenshot che mostra la scheda Avanzate selezionata con il valore immesso come descritto nel passaggio 4.](media/advanced-tab-selected.png)

5. Selezionare **Salva**.

6. Il pool host selezionato dovrebbe ora iniziare a rilasciare i file di connessione RDP con il campo kdcproxyname immesso incluso.

## <a name="next-steps"></a>Passaggi successivi

Il ruolo RDGateway in Servizi Desktop remoto include un servizio proxy KDC. Vedere [distribuire il ruolo Gateway Desktop remoto nel desktop virtuale di Windows](rd-gateway-role.md) per informazioni su come impostarne uno come destinazione per desktop virtuale di Windows.
