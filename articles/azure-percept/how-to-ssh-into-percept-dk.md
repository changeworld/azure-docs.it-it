---
title: Connettersi ad Azure Percept DK tramite SSH
description: Informazioni su come eseguire SSH in Azure Percept DK con PuTTy
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 8dda18271de9b7d65246f0882ee7a68191031c05
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102096616"
---
# <a name="connect-to-your-azure-percept-dk-over-ssh"></a>Connettersi ad Azure Percept DK tramite SSH

Seguire questa procedura per configurare una connessione SSH ad Azure Percept DK tramite [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html).

## <a name="prerequisites"></a>Prerequisiti

- Un computer host basato su Windows, Linux o OS X con funzionalità Wi-Fi
- Un client SSH
    - Se il computer host esegue Windows, [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) è un client SSH efficace e verrà usato in questa guida.
    - Se il computer host esegue Linux o OS X, i servizi SSH sono inclusi in tali sistemi operativi e possono essere eseguiti senza un'applicazione client separata. Per ulteriori informazioni su come eseguire i servizi SSH, consultare la documentazione del prodotto del sistema operativo.
- Azure Percept DK
- Configurare un account di accesso SSH durante l' [esperienza di onboarding di Azure PERCEPT dk](./quickstart-percept-dk-set-up.md)

## <a name="initiate-the-ssh-connection"></a>Avviare la connessione SSH

1. Power in Azure Percept DK (Dev Kit)

1. Se il kit dev è già connesso a una rete tramite Ethernet o Wi-Fi, andare al passaggio successivo. In caso contrario, connettere il computer host direttamente al punto di accesso Wi-Fi del kit di sviluppo, proprio come la connessione ad altre Wi-Fi rete:
    - **nome di rete**: SCZ-xxxx (dove "xxxx" è le ultime quattro cifre dell'indirizzo di rete Mac del Dev Kit)
    - **password**: si trova nella scheda di benvenuto fornita con Dev Kit

    > [!WARNING]
    > Quando si è connessi al punto di accesso Wi-Fi DK di Azure Percept, il computer host perderà temporaneamente la connessione a Internet. Le chiamate alle conferenze video attive, lo streaming web o altre esperienze basate sulla rete verranno interrotte fino al completamento del passaggio 3 dell'esperienza di onboarding di Azure Percept DK.

1. Aprire PuTTY. Immettere quanto segue e fare clic su **Apri** per SSH in DevKit:

    1. Nome host: 10.1.1.1
    1. Porta: 22
    1. Tipo di connessione: SSH

    > [!NOTE]
    > Il **nome host** corrisponde all'indirizzo IP del dispositivo. Se il kit di sviluppo è connesso al punto di accesso Wi-Fi del kit di sviluppo, l'indirizzo IP sarà 10.1.1.1. Se il kit di sviluppo è connesso tramite Ethernet, usare l'indirizzo IP locale del dispositivo, che è possibile ottenere dall'hub o dal router Ethernet. Se il dispositivo è connesso tramite Wi-Fi, è necessario usare l'indirizzo IP fornito durante l' [esperienza di onboarding di Azure PERCEPT dk](./quickstart-percept-dk-set-up.md).

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/ssh-putty.png" alt-text="Immagine.":::

1. Accedere al terminale PuTTy con il nome utente e la password SSH creati durante l'esperienza di onboarding.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver eseguito la connessione ad Azure Percept DK tramite SSH, è possibile eseguire diverse attività, tra cui la risoluzione dei problemi, gli aggiornamenti USB e l'esecuzione dello strumento DiagTool o SoftAP.