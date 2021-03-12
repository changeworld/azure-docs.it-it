---
title: File di inclusione
description: file
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: mingshen-ms
ms.author: krsh
ms.date: 10/20/2020
ms.openlocfilehash: c60d2a9b13cce9251ff0f730081a9d677206770d
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "102630129"
---
## <a name="generalize-the-image"></a>Generalizzare l'immagine

Tutte le immagini in Azure Marketplace devono poter essere riutilizzate in modo generico. Per ottenere questo risultato, il disco rigido virtuale del sistema operativo deve essere generalizzato. La generalizzazione è un'operazione che rimuove tutti gli identificatori specifici di istanze e tutti i driver software da una macchina virtuale.

### <a name="for-windows"></a>Per Windows

I dischi del sistema operativo Windows sono generalizzati con lo strumento [Sysprep](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) . Se successivamente si aggiorna o si riconfigura il sistema operativo, è necessario eseguire di nuovo Sysprep.

> [!WARNING]
> Dopo aver eseguito Sysprep, spegnere la macchina virtuale fino a quando non viene distribuita perché gli aggiornamenti possono essere eseguiti automaticamente. Questo arresto eviterà che gli aggiornamenti successivi possano apportare modifiche specifiche di istanza al sistema operativo o ai servizi installati. Per altre informazioni sull'esecuzione di sysprep, vedere [Procedura per generalizzare un disco rigido virtuale](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep).

### <a name="for-linux"></a>Per Linux

Il processo seguente generalizza una macchina virtuale Linux e la ridistribuisce come macchina virtuale separata. Per informazioni dettagliate, vedere [Come creare un'immagine di una macchina virtuale o un disco rigido virtuale](../../virtual-machines/linux/capture-image.md). È possibile arrestare quando si raggiunge la sezione denominata "creare una macchina virtuale dall'immagine acquisita".

1. Rimuovere l'agente Linux di Azure.
    1. Connettersi alla VM Linux tramite un client SSH.
    2. Nella finestra SSH immettere il comando seguente: `sudo waagent –deprovision+user` .
    3. Digitare Y per continuare (è possibile aggiungere il parametro -force al comando precedente per evitare il passaggio di conferma).
    4. Al termine del comando, immettere **Exit** per chiudere il client SSH.
2. Arrestare la macchina virtuale.
    1. Nel portale di Azure selezionare il gruppo di risorse (RG) e deallocare la macchina virtuale.
    2. La VM è ora generalizzata ed è possibile creare una nuova macchina virtuale usando questo disco della macchina virtuale.

### <a name="capture-image"></a>Acquisire l'immagine

Quando la macchina virtuale è pronta, è possibile acquisirla in una raccolta di immagini condivise di Azure. Per acquisire, attenersi alla procedura seguente:

1. In [portale di Azure](https://ms.portal.azure.com/)passare alla pagina della macchina virtuale.
2. Selezionare **Acquisisci**.
3. In **Condividi immagine in raccolta immagini condivise** selezionare **Sì, condividilo in una raccolta come versione dell'immagine**.
4. In **stato del sistema operativo** selezionare generalizzato.
5. Selezionare una raccolta di immagini di destinazione o **crearne una nuova**.
6. Selezionare una definizione di immagine di destinazione o **crearne una nuova**.
7. Specificare un **numero di versione** per l'immagine.
8. Selezionare **Review + create (Revisione e creazione)** per rivedere le scelte effettuate.
9. Una volta superata la convalida, selezionare **Crea**.

Per la pubblicazione, l'account del server di pubblicazione deve disporre di un accesso proprietario al SIG. Per concedere l'accesso:

1. Passare alla raccolta di immagini condivise.
2. Selezionare **controllo di accesso** (IAM) nel pannello di sinistra.
3. Selezionare **Aggiungi** e quindi **Aggiungi assegnazione ruolo**.
4. Selezionare un **ruolo** o un **proprietario**.
5. In **assegnare l'accesso a** selezionare **utente, gruppo o entità servizio**.
6. Selezionare l'indirizzo di posta elettronica di Azure della persona che pubblicherà l'immagine.
7. Selezionare **Salva**.

:::image type="content" source="../media/create-vm/add-role-assignment.png" alt-text="Consente di visualizzare la finestra Aggiungi assegnazione ruolo.":::

> [!NOTE]
> Non è necessario generare URI di firma di accesso condiviso, perché ora è possibile pubblicare un'immagine SIG nel centro per i partner. Tuttavia, se è comunque necessario fare riferimento ai passaggi di generazione dell'URI SAS, vedere [come generare un URI di firma di accesso condiviso per un'immagine di macchina virtuale](../azure-vm-get-sas-uri.md).
