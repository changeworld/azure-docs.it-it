---
title: 'Avvio rapido: Configurare Azure Bastion e connettersi a una VM tramite un indirizzo IP privato e un browser'
titleSuffix: Azure Bastion
description: Questo articolo di avvio rapido illustra come creare un host di Azure Bastion da una macchina virtuale e connettersi alla VM in modo sicuro tramite un browser e un indirizzo IP privato.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: quickstart
ms.date: 02/18/2021
ms.author: cherylmc
ms.openlocfilehash: 53f09eed89f9667611ed4d5e0268c889609d560a
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553567"
---
# <a name="quickstart-connect-to-a-vm-securely-through-a-browser-via-private-ip-address"></a>Avvio rapido: Connettersi a una VM in modo sicuro tramite un browser e un indirizzo IP privato

È possibile connettersi a una macchina virtuale (VM) tramite il browser usando il portale di Azure e Azure Bastion. Questo articolo di avvio rapido illustra come configurare Azure Bastion in base alle impostazioni della VM e quindi connettersi alla VM tramite il portale. La VM non deve necessariamente avere un indirizzo IP pubblico, il software client, un agente o una configurazione speciale. Una volta effettuato il provisioning del servizio, l'esperienza RDP/SSH è disponibile per tutte le macchine virtuali nella stessa rete virtuale. Per altre informazioni su Azure Bastion, vedere [Che cos'è Azure Bastion?](bastion-overview.md)

## <a name="prerequisites"></a><a name="prereq"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. Se non è disponibile, [crearne uno gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Per potersi connettere a una VM tramite il browser con Bastion, è necessario essere in grado di accedere al portale di Azure.

* Una macchina virtuale Windows in una rete virtuale. Se non si ha una macchina virtuale, crearne una come descritto in [Avvio rapido: Creare una macchina virtuale](../virtual-machines/windows/quick-create-portal.md).

  * Se necessario, vedere i [valori di esempio](#values) disponibili.
  * Se si ha già una rete virtuale, assicurarsi di selezionarla nella scheda Rete quando si crea la VM.
  * Se non si ha una rete virtuale, è possibile crearne una contemporaneamente alla VM.
  * Non è necessario avere un indirizzo IP pubblico per connettersi alla VM tramite Azure Bastion.

* Ruoli VM richiesti:
  * Ruolo Lettore nella macchina virtuale.
  * Ruolo Lettore nella scheda di interfaccia di rete con l'indirizzo IP privato della macchina virtuale.
  
* Porte della VM richieste:
  * Porte in ingresso: RDP (3389)

 >[!NOTE]
 >L'uso di Azure Bastion con le zone DNS privato di Azure non è attualmente supportato. Prima di iniziare, assicurarsi che la rete virtuale in cui si intende distribuire la risorsa Bastion non sia collegata a una zona DNS privata.
 >

### <a name="example-values"></a><a name="values"></a>Valori di esempio

Quando si crea questa configurazione, è possibile usare i valori di esempio seguenti oppure è possibile sostituirli con altri personalizzati.

**Valori di base per la rete virtuale e la VM:**

|**Nome** | **Valore** |
| --- | --- |
| Macchina virtuale| TestVM |
| Resource group | TestRG1 |
| Area | Stati Uniti orientali |
| Rete virtuale | VNet1 |
| Spazio degli indirizzi | 10.1.0.0/16 |
| Subnet | Front-end: 10.1.0.0/24 |

**Valori di Azure Bastion:**

|**Nome** | **Valore** |
| --- | --- |
| Nome | VNet1-Bastion |
| + Nome subnet | AzureBastionSubnet |
| Indirizzi della subnet AzureBastionSubnet | Una subnet con lo spazio indirizzi della rete virtuale con una subnet mask /27, Ad esempio, 10.1.1.0/27.  |
| Indirizzo IP pubblico |  Creare un nuovo gruppo di risorse |
| Nome dell'indirizzo IP pubblico | VNet1-IP  |
| SKU indirizzo IP pubblico |  Standard  |
| Assegnazione  | Statico |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>Creare un host bastion

È possibile configurare un host bastion in vari modi diversi. Nei passaggi seguenti si creerà un host bastion nel portale di Azure direttamente dalla macchina virtuale. Quando si crea un host da una macchina virtuale, le varie impostazioni verranno popolate automaticamente in base alla macchina virtuale e/o alla rete virtuale.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare alla macchina virtuale a cui connettersi e selezionare **Connetti**.

   :::image type="content" source="./media/quickstart-host-portal/vm-connect.png" alt-text="Screenshot delle impostazioni della macchina virtuale." lightbox="./media/quickstart-host-portal/vm-connect.png":::
1. Nell'elenco a discesa selezionare **Bastion**.

   :::image type="content" source="./media/quickstart-host-portal/bastion.png" alt-text="Screenshot dell'elenco a discesa Bastion." lightbox="./media/quickstart-host-portal/bastion.png":::
1. Nella pagina **TestVM | Connetti** selezionare **Usa Bastion**.

   :::image type="content" source="./media/quickstart-host-portal/select-bastion.png" alt-text="Screenshot di use Bastion.":::

1. Nella pagina **Connetti con Azure Bastion** configurare i valori.

   * **Passaggio 1:** I valori sono già popolati perché si crea l'host Bastion direttamente dalla VM.

   * **Passaggio 2:** Lo spazio degli indirizzi è già popolato con uno spazio di indirizzi suggerito. Il AzureBastionSubnet deve avere uno spazio degli indirizzi di/27 o più grande (/26,/25 e così via).

   :::image type="content" source="./media/quickstart-host-portal/create-subnet.png" alt-text="Screenshot della creazione della subnet Bastion.":::

1. Fare clic su **Crea subnet** per creare il AzureBastionSubnet.
1. Dopo la creazione della subnet, la pagina viene spostata automaticamente al **passaggio 3**. Per il passaggio 3, usare i valori seguenti:

   * **Nome:** Assegnare un nome all'host Bastion.
   * **Indirizzo IP pubblico**: selezionare **Crea nuovo**.
   * **Nome indirizzo IP pubblico:** Nome della risorsa indirizzo IP pubblico.
   * **SKU indirizzo IP pubblico:** Pre-configurato come **standard**
   * **Assegnazione:** Pre-configurato in **static**. Non è possibile usare un'assegnazione dinamica per Azure Bastion.
   * **Gruppo di risorse**: lo stesso gruppo di risorse della VM.

   :::image type="content" source="./media/quickstart-host-portal/create-bastion.png" alt-text="Screenshot del passaggio 3.":::
1. Dopo aver completato i valori, selezionare **Crea Azure Bastion usando le impostazioni predefinite**. Azure convalida le impostazioni e quindi crea l'host. Per la creazione e la distribuzione dell'host e delle relative risorse sono necessari circa 5 minuti.

## <a name="connect"></a><a name="connect"></a>Connettersi

Al termine della distribuzione della risorsa Bastion nella rete virtuale, la schermata passa alla pagina di connessione.

1. Digitare il nome utente e la password per la macchina virtuale. Selezionare **Connect** (Connetti).

   :::image type="content" source="./media/quickstart-host-portal/connect.png" alt-text="Screenshot che mostra la finestra di dialogo Connetti per la connessione tramite Azure Bastion.":::
1. La connessione RDP a questa macchina virtuale verrà aperta direttamente nel portale di Azure (tramite HTML5) usando la porta 443 e il servizio Bastion.

   :::image type="content" source="./media/quickstart-host-portal/connected.png" alt-text="Connessione RDP":::

## <a name="clean-up-resources"></a>Pulire le risorse

Quando la rete virtuale e le macchine virtuali non sono più necessarie, rimuovere il gruppo di risorse e tutte le risorse al suo interno:

1. Immettere il nome del gruppo di risorse nella casella **Cerca** nella parte superiore del portale e selezionarlo nei risultati della ricerca.

1. Selezionare **Elimina gruppo di risorse**.

1. Immettere il nome del gruppo di risorse in **DIGITARE IL NOME DEL GRUPPO DI RISORSE** e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stato creato un host bastion per la rete virtuale ed è stata stabilita una connessione sicura a una macchina virtuale tramite Bastion. Per connettersi a un set di scalabilità di macchine virtuali, continuare con il passaggio successivo.

> [!div class="nextstepaction"]
> [Connettersi a un set di scalabilità di macchine virtuali tramite Azure Bastion](bastion-connect-vm-scale-set.md)
