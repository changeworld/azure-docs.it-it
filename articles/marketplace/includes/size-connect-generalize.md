---
title: File di inclusione
description: file
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: mingshen-ms
ms.author: krsh
ms.date: 04/16/2021
ms.openlocfilehash: e119d40cd0b8f482d33c3c86c644cf6a0846390a
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727125"
---
## <a name="generalize-the-image"></a>Generalizzare l'immagine

Tutte le immagini in Azure Marketplace devono poter essere riutilizzate in modo generico. Per ottenere questo risultato, il disco rigido virtuale del sistema operativo deve essere generalizzato. La generalizzazione è un'operazione che rimuove tutti gli identificatori specifici di istanze e tutti i driver software da una macchina virtuale.

### <a name="for-windows"></a>Per Windows

I dischi del sistema operativo Windows vengono generalizzati con lo [strumento sysprep.](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) Se in un secondo momento si aggiorna o riconfigura il sistema operativo, è necessario eseguire nuovamente sysprep.

> [!WARNING]
> Dopo aver eseguito sysprep, disattivare la macchina virtuale finché non viene distribuita perché gli aggiornamenti possono essere eseguiti automaticamente. Questo arresto eviterà che gli aggiornamenti successivi possano apportare modifiche specifiche di istanza al sistema operativo o ai servizi installati. Per altre informazioni sull'esecuzione di sysprep, vedere [Procedura per generalizzare un disco rigido virtuale](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep).

### <a name="for-linux"></a>Per Linux

Il processo seguente generalizza una macchina virtuale Linux e la ridistribuisce come macchina virtuale separata. Per informazioni dettagliate, vedere [Come creare un'immagine di una macchina virtuale o un disco rigido virtuale](../../virtual-machines/linux/capture-image.md). È possibile arrestarsi quando si raggiunge la sezione "Creare una macchina virtuale dall'immagine acquisita".

1. Rimuovere l'agente Linux di Azure.
    1. Connettersi alla VM Linux tramite un client SSH.
    2. Nella finestra SSH immettere questo comando: `sudo waagent –deprovision+user` .
    3. Digitare Y per continuare (è possibile aggiungere il parametro -force al comando precedente per evitare il passaggio di conferma).
    4. Al termine del comando, immettere **Exit per** chiudere il client SSH.
2. Arrestare la macchina virtuale.
    1. Nel portale di Azure selezionare il gruppo di risorse (RG) e deallocare la macchina virtuale.
    2. La macchina virtuale è ora generalizzata ed è possibile creare una nuova macchina virtuale usando questo disco della macchina virtuale.

### <a name="capture-image"></a>Acquisire l'immagine

> [!NOTE]
> La sottoscrizione di Azure contenente il sig deve essere nello stesso tenant dell'account dell'editore per poter pubblicare. Inoltre, l'account del server di pubblicazione deve avere almeno l'accesso collaboratore alla sottoscrizione contenente SIG.

Quando la macchina virtuale è pronta, è possibile acquisiscerla in una raccolta di immagini condivise di Azure. Seguire questa procedura per acquisire:

1. Nella [portale di Azure](https://ms.portal.azure.com/)passare alla pagina della macchina virtuale.
2. Selezionare **Acquisisci**.
3. In **Condividi immagine in Raccolta immagini condivise** selezionare **Sì, condividerla in una raccolta come versione dell'immagine.**
4. In **Stato del sistema operativo** selezionare Generalizzato.
5. Selezionare una raccolta di immagini di destinazione o **Crea nuovo**.
6. Selezionare una definizione di immagine di destinazione o **Crea nuovo**.
7. Specificare un **numero di versione** per l'immagine.
8. Selezionare **Review + create (Revisione e creazione)** per rivedere le scelte effettuate.
9. Dopo aver superato la convalida, selezionare **Crea.**

Per concedere l'accesso:

1. Passare a Raccolta immagini condivise.
2. Selezionare **Controllo di** accesso (IAM) nel pannello sinistro.
3. Selezionare **Aggiungi** e quindi Aggiungi **assegnazione di ruolo.**
4. Selezionare un **ruolo** o **un proprietario.**
5. In **Assegna accesso a selezionare** **Utente, gruppo o entità servizio**.
6. Selezionare l'indirizzo di posta elettronica di Azure della persona che pubblica l'immagine.
7. Selezionare **Salva**.

:::image type="content" source="../media/create-vm/add-role-assignment.png" alt-text="Visualizza la finestra Aggiungi assegnazione di ruolo.":::

> [!NOTE]
> Non è necessario generare URI di firma di accesso condiviso perché è ora possibile pubblicare un'immagine SIG in Partner Center. Tuttavia, se è ancora necessario fare riferimento ai passaggi di generazione dell'URI di firma di accesso condiviso, vedere Come generare un URI di firma di accesso [condiviso per un'immagine di macchina virtuale.](../azure-vm-get-sas-uri.md)
