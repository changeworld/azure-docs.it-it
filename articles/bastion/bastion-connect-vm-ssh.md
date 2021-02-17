---
title: Connettersi a una VM Linux con Azure Bastion
description: Questo articolo illustra come connettersi a una macchina virtuale Linux usando Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 02/12/2021
ms.author: cherylmc
ms.openlocfilehash: a5cda5d4d447bc04f853ea4a9abd15be75e7e177
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100588648"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion"></a>Connettersi tramite SSH a una macchina virtuale Linux con Azure Bastion

Questo articolo illustra come accedere in modo sicuro e trasparente alle macchine virtuali Linux in una rete virtuale di Azure. È possibile connettersi a una macchina virtuale direttamente dalla portale di Azure. Quando si usa Azure Bastion, le macchine virtuali non richiedono un client, un agente o un software aggiuntivo. Per altre informazioni su Azure Bastion, vedere la [Panoramica](bastion-overview.md).

È possibile usare Azure Bastion per connettersi a una macchina virtuale Linux tramite SSH. Per l'autenticazione, è possibile usare sia il nome utente che la password e le chiavi SSH. È possibile connettersi alla macchina virtuale con chiavi SSH usando uno dei seguenti:

* Chiave privata da immettere manualmente
* Un file che contiene le informazioni sulla chiave privata

La chiave privata SSH deve essere in un formato che inizia con  `"-----BEGIN RSA PRIVATE KEY-----"` e termina con `"-----END RSA PRIVATE KEY-----"` .

## <a name="prerequisites"></a>Prerequisiti

Assicurarsi di aver configurato un host Bastion di Azure per la rete virtuale in cui risiede la macchina virtuale. Per altre informazioni, vedere [creare un host Bastion di Azure](./tutorial-create-host-portal.md). Una volta eseguito il provisioning e la distribuzione del servizio Bastion nella rete virtuale, è possibile usarlo per connettersi a qualsiasi macchina virtuale in questa rete virtuale. 

Quando si usa Bastion per connettersi, si presuppone che si usi il protocollo RDP per connettersi a una macchina virtuale Windows e SSH per connettersi alle macchine virtuali Linux. Per informazioni sulla connessione a una macchina virtuale Windows, vedere [connettersi a una VM-Windows](bastion-connect-vm-rdp.md).

### <a name="required-roles"></a>Ruoli necessari

Per stabilire una connessione, sono necessari i ruoli seguenti:

* Ruolo Lettore nella macchina virtuale
* Ruolo Lettore nella scheda di interfaccia di rete con l'indirizzo IP privato della macchina virtuale
* Ruolo Lettore nella risorsa Azure Bastion

### <a name="ports"></a>Porte

Per connettersi alla VM Linux tramite SSH, è necessario che le porte seguenti siano aperte nella macchina virtuale:

* Porta in ingresso: SSH (22)

## <a name="connect-using-username-and-password"></a><a name="username"></a>Connetti: uso del nome utente e della password

1. Aprire il [portale di Azure](https://portal.azure.com). Passare alla macchina virtuale a cui si vuole connettersi, quindi fare clic su **Connetti** e selezionare **Bastion** nell'elenco a discesa.

   :::image type="content" source="./media/bastion-connect-vm-ssh/connect.png" alt-text="Screenshot mostra la Panoramica di una macchina virtuale in portale di Azure con Connetti selezionato":::
1. Dopo aver selezionato Bastion, fare clic su **use Bastion**. Se non è stato effettuato il provisioning di Bastion per la rete virtuale, vedere [Configure Bastion](./quickstart-host-portal.md).
1. Nella pagina **Connetti con Azure Bastion** immettere il **nome utente** e la **password**.

   :::image type="content" source="./media/bastion-connect-vm-ssh/password.png" alt-text="Autenticazione della password":::
1. Selezionare **Connetti** per connettersi alla macchina virtuale.

## <a name="connect-manually-enter-a-private-key"></a><a name="privatekey"></a>Connetti: immettere manualmente una chiave privata

1. Aprire il [portale di Azure](https://portal.azure.com). Passare alla macchina virtuale a cui si vuole connettersi, quindi fare clic su **Connetti** e selezionare **Bastion** nell'elenco a discesa.

   :::image type="content" source="./media/bastion-connect-vm-ssh/connect.png" alt-text="Screenshot mostra la Panoramica di una macchina virtuale in portale di Azure con Connetti selezionato":::
1. Dopo aver selezionato Bastion, fare clic su **use Bastion**. Se non è stato effettuato il provisioning di Bastion per la rete virtuale, vedere [Configure Bastion](./quickstart-host-portal.md).
1. Nella pagina **Connetti con Azure Bastion** immettere il **nome utente** e la **chiave privata SSH**.

   :::image type="content" source="./media/bastion-connect-vm-ssh/ssh-private-key.png" alt-text="Autenticazione con chiave privata SSH":::
1. Immettere la chiave privata nella **chiave privata SSH** dell'area di testo (o incollarla direttamente).
1. Selezionare **Connetti** per connettersi alla macchina virtuale.

## <a name="connect-using-a-private-key-file"></a><a name="ssh"></a>Connetti: uso di un file di chiave privata

1. Aprire il [portale di Azure](https://portal.azure.com). Passare alla macchina virtuale a cui si vuole connettersi, quindi fare clic su **Connetti** e selezionare **Bastion** nell'elenco a discesa.

   :::image type="content" source="./media/bastion-connect-vm-ssh/connect.png" alt-text="Screenshot mostra la Panoramica di una macchina virtuale in portale di Azure con Connetti selezionato":::
1. Dopo aver selezionato Bastion, fare clic su **use Bastion**. Se non è stato effettuato il provisioning di Bastion per la rete virtuale, vedere [Configure Bastion](./quickstart-host-portal.md).
1. Nella pagina **Connetti con Azure Bastion** immettere il **nome utente** e la **chiave privata SSH da file locale**.

   :::image type="content" source="./media/bastion-connect-vm-ssh/private-key-file.png" alt-text="File di chiave privata SSH":::

1. Individuare il file e quindi selezionare **Apri**.
1. Selezionare **Connetti** per connettersi alla macchina virtuale. Quando si fa clic su Connetti, la connessione SSH a questa macchina virtuale verrà aperta direttamente nella portale di Azure. Questa connessione avviene tramite HTML5 usando la porta 443 nel servizio Bastion sull'IP privato della macchina virtuale.

## <a name="connect-using-a-private-key-stored-in-azure-key-vault"></a><a name="akv"></a>Connetti: uso di una chiave privata archiviata in Azure Key Vault

>[!NOTE]
>È in corso l'implementazione dell'aggiornamento del portale per questa funzionalità per le aree.
>

1. Aprire il [portale di Azure](https://portal.azure.com). Passare alla macchina virtuale a cui si vuole connettersi, quindi fare clic su **Connetti** e selezionare **Bastion** nell'elenco a discesa.

   :::image type="content" source="./media/bastion-connect-vm-ssh/connect.png" alt-text="Screenshot mostra la Panoramica di una macchina virtuale in portale di Azure con Connetti selezionato":::
1. Dopo aver selezionato Bastion, fare clic su **use Bastion**. Se non è stato effettuato il provisioning di Bastion per la rete virtuale, vedere [Configure Bastion](./quickstart-host-portal.md).
1. Nella pagina **Connetti con Azure Bastion** immettere il **nome utente** e selezionare **chiave privata SSH da Azure Key Vault**.

   :::image type="content" source="./media/bastion-connect-vm-ssh/ssh-key-vault.png" alt-text="Chiave privata SSH da Azure Key Vault":::
1. Selezionare l'elenco a discesa **Azure Key Vault** e selezionare la risorsa in cui è stata archiviata la chiave privata SSH. Se non è stata configurata una risorsa di Azure Key Vault, vedere [creare un](../key-vault/general/quick-create-portal.md) insieme di credenziali delle chiavi e archiviare la chiave privata SSH come valore di un nuovo segreto Key Vault.

   :::image type="content" source="./media/bastion-connect-vm-ssh/key-vault.png" alt-text="Azure Key Vault":::

   Assicurarsi di avere un **elenco** e **ottenere** l'accesso ai segreti archiviati nella risorsa key Vault. Per assegnare e modificare i criteri di accesso per la risorsa Key Vault, vedere [assegnare un criterio di accesso key Vault](../key-vault/general/assign-access-policy-portal.md).
1. Selezionare l'elenco a discesa **Azure Key Vault Secret** e selezionare il segreto Key Vault che contiene il valore della chiave privata SSH.
1. Selezionare **Connetti** per connettersi alla macchina virtuale. Quando si fa clic su Connetti, la **connessione** SSH a questa macchina virtuale verrà aperta direttamente nella portale di Azure. Questa connessione avviene tramite HTML5 usando la porta 443 nel servizio Bastion sull'IP privato della macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure Bastion, vedere le [domande frequenti su Bastion](bastion-faq.md). 
