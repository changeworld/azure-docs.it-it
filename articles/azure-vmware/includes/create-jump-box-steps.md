---
title: Creare la jump box della soluzione Azure VMware
description: Passaggi per creare la jump box della soluzione Azure VMware.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: f746e11763e1df1686f3134960dea167bf1c9908
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103462254"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-access-private-cloud.md -->

1. Nel gruppo di risorse selezionare **+ Aggiungi**, quindi cercare e selezionare **Microsoft Windows 10** e selezionare **Crea**.

   :::image type="content" source="../media/tutorial-access-private-cloud/ss8-azure-w10vm-create.png" alt-text="Aggiungere una nuova macchina virtuale Windows 10 per una jump box." border="true":::

1. Immettere le informazioni obbligatorie nei campi e quindi selezionare **Verifica e crea**. 

   Per altre informazioni sui campi, vedere la tabella seguente.

   | Campo | Valore |
   | --- | --- |
   | **Sottoscrizione** | Il valore è già popolato con la sottoscrizione appartenente al gruppo di risorse. |
   | **Gruppo di risorse** | Il valore è già popolato per il gruppo di risorse corrente, creato nell'esercitazione precedente.  |
   | **Nome macchina virtuale** | Immettere un nome univoco per la VM. |
   | **Area** | Selezionare la posizione geografica della VM. |
   | **Opzioni di disponibilità** | Lasciare selezionato il valore predefinito. |
   | **Immagine** | Selezionare l'immagine della VM. |
   | **Dimensione** | Lasciare il valore predefinito. |
   | **Tipo di autenticazione**  | selezionare **Password**. |
   | **Nome utente** | Immettere il nome utente per l'accesso alla VM. |
   | **Password** | Immettere la password per l'accesso alla VM. |
   | **Conferma password** | Immettere la password per l'accesso alla VM. |
   | **Porte in ingresso pubbliche** | Selezionare **Nessuno**. Se si seleziona Nessuno, è possibile usare l'[accesso JIT](../../security-center/security-center-just-in-time.md#jit-configure) per controllare l'accesso alla macchina virtuale solo quando si vuole accedervi. In alternativa, è possibile usare un [Bastion di Azure](../../bastion/tutorial-create-host-portal.md) se si vuole accedere al server jump box in modo sicuro da Internet senza esporre alcuna porta di rete.  |


1. Al termine della convalida, selezionare **Crea** per avviare il processo di creazione della macchina virtuale.

