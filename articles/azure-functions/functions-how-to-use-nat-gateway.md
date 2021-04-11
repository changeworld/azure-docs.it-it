---
title: Controllare l'IP in uscita di funzioni di Azure con un gateway NAT di rete virtuale di Azure
description: Esercitazione dettagliata che illustra come configurare NAT per una funzione connessa a una rete virtuale di Azure
ms.topic: tutorial
ms.author: kyburns
ms.date: 2/26/2021
ms.openlocfilehash: 5bb491e367ed813f09197a193745c231261c88c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104658158"
---
# <a name="tutorial-control-azure-functions-outbound-ip-with-an-azure-virtual-network-nat-gateway"></a>Esercitazione: controllare l'IP in uscita di funzioni di Azure con un gateway NAT di rete virtuale di Azure

Virtual Network Address Translation (NAT) semplifica la connettività Internet in uscita per le reti virtuali. Quando viene configurato in una subnet, per tutta la connettività in uscita vengono usati gli indirizzi IP pubblici statici specificati. Un NAT può essere utile per le funzioni di Azure o per le app Web che devono usare un servizio di terze parti che usa un oggetto consentita di un indirizzo IP come misura di sicurezza. Per ulteriori informazioni, vedere la pagina relativa alla [rete virtuale NAT](../virtual-network/nat-overview.md).

In questa esercitazione viene illustrato come utilizzare NAT della rete virtuale per instradare il traffico in uscita da una funzione attivata tramite HTTP. Questa funzione consente di controllare il proprio indirizzo IP in uscita. Durante questa esercitazione, è necessario:

> [!div class="checklist"]
> * Crea rete virtuale
> * Creare un'app per le funzioni del piano Premium
> * Creare un indirizzo IP pubblico
> * Creare un gateway NAT
> * Configurare l'app per le funzioni per instradare il traffico in uscita attraverso il gateway NAT

## <a name="topology"></a>Topologia

Il diagramma seguente illustra l'architettura della soluzione creata:

![Interfaccia utente per l'integrazione del gateway NAT](./media/functions-how-to-use-nat-gateway/topology.png)

Le funzioni in esecuzione nel piano Premium hanno le stesse funzionalità di hosting di app Web nel servizio app Azure, che include la funzionalità di integrazione VNet. Per altre informazioni sull'integrazione di VNet, incluse la risoluzione dei problemi e la configurazione avanzata, vedere [integrare l'app con una rete virtuale di Azure](../app-service/web-sites-integrate-with-vnet.md).

## <a name="prerequisites"></a>Prerequisiti

Per questa esercitazione è importante comprendere l'indirizzamento IP e la creazione di subnet. Si può iniziare con [questo articolo che illustra le nozioni di base dell'indirizzamento e della creazione di subnet](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Molti altri articoli e video sono disponibili online.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

Se è già stata completata l'esercitazione [integrare funzioni con una rete virtuale di Azure](./functions-create-vnet.md) , è possibile passare alla [creazione di una funzione trigger http](#create-function).

## <a name="create-a-virtual-network"></a>Crea rete virtuale

1. Nel menu del portale di Azure selezionare **Crea una risorsa**. In Azure Marketplace selezionare **Rete** > **Rete virtuale**.

1. In **Crea rete virtuale** immettere o selezionare le impostazioni specificate come illustrato nella tabella seguente:

    | Impostazione | Valore |
    | ------- | ----- |
    | Subscription | Selezionare la propria sottoscrizione.|
    | Resource group | Selezionare **Crea nuovo**, immettere *myResourceGroup* e selezionare **OK**. |
    | Nome | Immettere *myResourceGroup-VNET*. |
    | Location | Selezionare **Stati Uniti orientali**.|

1. Selezionare **Avanti: indirizzi IP** e per **spazio indirizzi IPv4** immettere *10.10.0.0/16*.

1. Selezionare **Aggiungi subnet**, quindi immettere *tutorial-NET* per **nome subnet** e *10.10.1.0/24* per **intervallo di indirizzi subnet**.

    ![Scheda indirizzi IP per la creazione di un VNET](./media/functions-how-to-use-nat-gateway/create-vnet-2-ip-space.png)

1. Selezionare **Aggiungi** e quindi **Rivedi e crea**. Lasciare le altre impostazioni sui valori predefiniti e selezionare **Crea**.

1. In **Crea rete virtuale** selezionare **Crea**.

Successivamente, si crea un'app per le funzioni nel [piano Premium](functions-premium-plan.md). Questo piano offre una scalabilità senza server per supportare l'integrazione della rete virtuale.

## <a name="create-a-function-app-in-a-premium-plan"></a>Creare un'app per le funzioni in un piano Premium

> [!NOTE]  
> Per un'esperienza ottimale in questa esercitazione, scegliere .NET per stack di runtime e scegliere Windows per sistema operativo. Inoltre, creare un'app per le funzioni nella stessa area della rete virtuale.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

## <a name="connect-your-function-app-to-the-virtual-network"></a>Connettere l'app per le funzioni alla rete virtuale

È ora possibile connettere l'app per le funzioni alla rete virtuale.

1. Nell'app per le funzioni selezionare **rete** nel menu a sinistra, quindi in **integrazione VNet** Selezionare **fare clic qui per configurare**.

    :::image type="content" source="./media/functions-how-to-use-nat-gateway/networking-0.png" alt-text="Scegliere la rete nell'app per le funzioni":::

1. Nella pagina **integrazione rete virtuale** selezionare **Aggiungi VNET**.

1. In **stato funzionalità di rete** usare le impostazioni nella tabella sotto l'immagine:

    ![Definire la rete virtuale dell'app per le funzioni](./media/functions-how-to-use-nat-gateway/networking-3.png)

    | Impostazione      | Valore consigliato  | Descrizione      |
    | ------------ | ---------------- | ---------------- |
    | **Rete virtuale** | MyResourceGroup-VNET | Questa rete virtuale è quella creata in precedenza. |
    | **Subnet** | Crea nuova subnet | Creare una subnet nella rete virtuale da usare per l'app per le funzioni. L'integrazione di VNet deve essere configurata per l'uso di una subnet vuota. |
    | **Nome della subnet** | Function-Net | Nome della nuova subnet. |
    | **Blocco di indirizzi della rete virtuale** | 10.10.0.0/16 | È necessario definire un solo blocco di indirizzi. |
    | **Blocco di indirizzi della subnet** | 10.10.2.0/24   | Le dimensioni della subnet limitano il numero totale di istanze per le quali l'app per le funzioni del piano Premium può essere scalata. Questo esempio usa una `/24` subnet con 254 indirizzi host disponibili. Questa subnet viene sottoposta a provisioning eccessivo, ma facile da calcolare. |

1. Selezionare **OK** per aggiungere la subnet. Chiudere le pagine di **integrazione VNet** e di **stato della funzionalità di rete** per tornare alla pagina dell'app per le funzioni.

L'app per le funzioni può ora accedere alla rete virtuale. Si aggiungerà quindi una funzione attivata tramite HTTP all'app per le funzioni.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>Creare una funzione Trigger HTTP

1. Selezionare **Funzioni** nel menu a sinistra della finestra **Funzioni** e quindi **Aggiungi** nel menu in alto. 
 
1. Dalla finestra **nuova funzione** selezionare **trigger http** e accettare il nome predefinito per la **nuova funzione** oppure immettere un nuovo nome. 

1. In **codice + test** sostituire il codice di script C# (con estensione CSX) generato dal modello con il codice seguente: 

    ```csharp
    #r "Newtonsoft.Json"
    
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");
    
        var client = new HttpClient();
        var response = await client.GetAsync(@"https://ifconfig.me");
        var responseMessage = await response.Content.ReadAsStringAsync();
    
        return new OkObjectResult(responseMessage);
    }
    ```

    Questo codice chiama un sito Web esterno che restituisce l'indirizzo IP del chiamante, che in questo caso è questa funzione. Questo metodo consente di determinare facilmente l'indirizzo IP in uscita usato dall'app per le funzioni.

A questo punto si è pronti per eseguire la funzione e controllare gli indirizzi IP in uscita correnti.

## <a name="verify-current-outbound-ips"></a>Verificare gli indirizzi IP in uscita correnti

A questo punto, è possibile eseguire la funzione. Prima di tutto, archiviare il portale e verificare quali indirizzi IP in uscita vengono usati dall'app per le funzioni.  

1. Nell'app per le funzioni selezionare **Proprietà** ed esaminare il campo **indirizzi IP in uscita** .

    ![Visualizzare gli indirizzi IP in uscita dell'app per le funzioni](./media/functions-how-to-use-nat-gateway/function-properties-ip.png)

1. Tornare ora alla funzione trigger HTTP, selezionare **codice + test** e quindi **test/Esegui**.

    ![Test (funzione)](./media/functions-how-to-use-nat-gateway/function-code-test.png)

1. Selezionare **Esegui** per eseguire la funzione, quindi passare all' **output**. 

    ![Output della funzione di test](./media/functions-how-to-use-nat-gateway/function-test-1-output.png)

1. Verificare che l'indirizzo IP nel corpo della risposta HTTP sia uno dei valori degli indirizzi IP in uscita visualizzati in precedenza.

A questo punto, è possibile creare un IP pubblico e usare un gateway NAT per modificare questo indirizzo IP in uscita.

## <a name="create-public-ip"></a>Crea IP pubblico

1. Dal gruppo di risorse selezionare **Aggiungi**, cercare **indirizzo IP pubblico** in Azure Marketplace e selezionare **Crea**. Usare le impostazioni della tabella sotto l'immagine:

    ![Crea indirizzo IP pubblico](./media/functions-how-to-use-nat-gateway/create-public-ip.png)

    | Impostazione      | Valore consigliato  |
    | ------------ | ---------------- |
    | **Versione indirizzo IP** | IPv4 |
    | **SKU** | Standard |
    | **Livello** | Regionale |
    | **Nome** | IP in uscita |
    | **Sottoscrizione** | Assicurarsi che la sottoscrizione sia visualizzata | 
    | **Gruppo di risorse** | myResourceGroup (o nome assegnato al gruppo di risorse) |
    | **Posizione** | Stati Uniti orientali (o località assegnata alle altre risorse) |
    | **Availability Zone** (Zona di disponibilità) | Nessuna zona |

1. Selezionare **Crea** per inviare la distribuzione.

1. Al termine della distribuzione, passare alla risorsa indirizzo IP pubblico appena creato e visualizzare l'indirizzo IP nella **Panoramica**.

    ![Visualizzare l'indirizzo IP pubblico](./media/functions-how-to-use-nat-gateway/public-ip-overview.png)

## <a name="create-nat-gateway"></a>Creare il gateway NAT

A questo punto, creare il gateway NAT. Quando si inizia con la [precedente esercitazione](functions-create-vnet.md)sulla rete virtuale, `Function-Net` è stato il nome della subnet suggerito ed `MyResourceGroup-vnet` è stato il nome della rete virtuale suggerito in questa esercitazione.

1. Dal gruppo di risorse selezionare **Aggiungi**, cercare il **gateway NAT** in Azure Marketplace e selezionare **Crea**. Usare le impostazioni nella tabella sotto l'immagine per popolare la scheda **nozioni di base** :

    ![Creare il gateway NAT](./media/functions-how-to-use-nat-gateway/create-nat-1-basics.png)

    | Impostazione      | Valore consigliato  |
    | ------------ | ---------------- |  
    | **Sottoscrizione** | Sottoscrizione in uso | 
    | **Gruppo di risorse** | myResourceGroup (o nome assegnato al gruppo di risorse) |
    | **Nome del gateway NAT** | myNatGateway |
    | **Area** | Stati Uniti orientali (o località assegnata alle altre risorse) |
    | **Availability Zone** (Zona di disponibilità) | nessuno |

1. Selezionare **Avanti: IP in uscita**. Nel campo **indirizzi IP pubblici** selezionare l'indirizzo IP pubblico creato in precedenza. Lasciare deselezionati i **prefissi IP pubblici** .

1. Selezionare **Avanti: subnet**. Selezionare la risorsa *myResourceGroup-VNET* nel campo **rete virtuale** e *funzione-subnet NET* .

    ![Selezionare una subnet](./media/functions-how-to-use-nat-gateway/create-nat-3-subnet.png)

1. Selezionare **Verifica + crea** e quindi **Crea** per inviare la distribuzione.

Al termine della distribuzione, il gateway NAT è pronto per instradare il traffico dalla subnet dell'app per le funzioni a Internet.

## <a name="update-function-configuration"></a>Aggiorna configurazione funzione

A questo punto, è necessario aggiungere un'impostazione dell'applicazione `WEBSITE_VNET_ROUTE_ALL` impostata sul valore `1` .  Questa impostazione forza il traffico in uscita attraverso la rete virtuale e il gateway NAT associato. Senza questa impostazione, il traffico Internet non viene instradato attraverso la rete virtuale integrata e verranno visualizzati gli stessi indirizzi IP in uscita. 

1. Passare all'app per le funzioni nella portale di Azure e selezionare **configurazione** dal menu a sinistra.

1. In **Impostazioni applicazione** selezionare **+ nuova impostazione applicazione** e completare usare i valori seguenti per compilare i campi:

    |Nome campo  |Valore |
    |---|---|
    |**Nome**    |WEBSITE_VNET_ROUTE_ALL|
    |**Valore**   |1|

1. Selezionare **OK** per chiudere la finestra di dialogo nuova impostazione applicazione.

1. Selezionare **Save (Salva** ) e quindi **continue (continua** ) per salvare le impostazioni.

L'app per le funzioni è ora configurata per instradare il traffico attraverso la rete virtuale associata.

## <a name="verify-new-outbound-ips"></a>Verifica nuovi indirizzi IP in uscita

Ripetere [i passaggi precedenti](#verify-current-outbound-ips) per eseguire di nuovo la funzione. A questo punto dovrebbe essere visualizzato l'indirizzo IP in uscita configurato nel NAT visualizzato nell'output della funzione.

## <a name="clean-up-resources"></a>Pulire le risorse

Sono state create risorse per completare questa esercitazione. Verranno addebitate le risorse, a seconda [dello stato dell'account](https://azure.microsoft.com/account/) e dei prezzi del [servizio](https://azure.microsoft.com/pricing/). Per evitare di sostenere costi aggiuntivi, eliminare le risorse quando sono più necessarie. 

[!INCLUDE [functions-quickstart-cleanup-inner](../../includes/functions-quickstart-cleanup-inner.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Opzioni di rete di Funzioni di Azure](functions-networking-options.md)
