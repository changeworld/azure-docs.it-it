---
title: Filtrare il traffico di rete-esercitazione-portale di Azure
titlesuffix: Azure Virtual Network
description: Questa esercitazione illustra come filtrare il traffico di rete a una subnet, con un gruppo di sicurezza di rete, usando il portale di Azure.
services: virtual-network
author: KumudD
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.service: virtual-network
ms.topic: tutorial
ms.date: 03/06/2021
ms.author: kumud
ms.openlocfilehash: 746e44c85d4dd9a662556a73f1e4ab0701d31400
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102435925"
---
# <a name="tutorial-filter-network-traffic-with-a-network-security-group-using-the-azure-portal"></a>Esercitazione: filtrare il traffico di rete con un gruppo di sicurezza di rete usando il portale di Azure

È possibile usare un gruppo di sicurezza di rete per filtrare il traffico di rete in ingresso e in uscita da una subnet di rete virtuale.

I gruppi di sicurezza di rete contengono regole di sicurezza per filtrare il traffico di rete in base a indirizzo IP, porta e protocollo. Le regole di sicurezza vengono applicate alle risorse distribuite in una subnet. 

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare un gruppo di sicurezza di rete e le regole di sicurezza
> * Creare una rete virtuale e associare un gruppo di sicurezza di rete a una subnet
> * Distribuire le macchine virtuali in una subnet
> * Testare i filtri del traffico

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="create-a-virtual-network"></a>Crea rete virtuale

1. Selezionare **Crea una risorsa** nell'angolo superiore sinistro del portale.

2. Nella casella di ricerca immettere **rete virtuale**. Selezionare **rete virtuale** nei risultati della ricerca.

3. Nella pagina **rete virtuale** selezionare **Crea**.

4. In **Crea rete virtuale** immettere o selezionare queste informazioni nella scheda **Generale**:

    | Impostazione | valore |
    | ------- | ----- |
    | **Dettagli del progetto** |   |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Resource group | Selezionare **Crea nuovo**.  </br> Immettere **myResourceGroup**. </br> Selezionare **OK**. |
    | **Dettagli istanza** |   |
    | Nome | Immettere **myVNet**. |
    | Region | Select **(Stati Uniti) Stati Uniti orientali**. |

5. Selezionare la scheda **Verifica + crea** oppure selezionare il pulsante **Verifica blu + crea** nella parte inferiore della pagina.

6. Selezionare **Crea**.

## <a name="create-application-security-groups"></a>Creare gruppi di sicurezza dell'applicazione

Un gruppo di sicurezza delle applicazioni consente di raggruppare i server con funzioni simili, ad esempio i server Web.

1. Selezionare **Crea una risorsa** nell'angolo superiore sinistro del portale.

2. Nella casella di ricerca immettere **gruppo di sicurezza dell'applicazione**. Selezionare **gruppo di sicurezza delle applicazioni** nei risultati della ricerca.

3. Nella pagina **gruppo di sicurezza delle applicazioni** selezionare **Crea**.

4. In **creare un gruppo di sicurezza dell'applicazione** immettere o selezionare queste informazioni nella scheda **nozioni di base** :

    | Impostazione | valore |
    | ------- | ----- |
    |**Dettagli del progetto** |  |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Resource group | Selezionare **myResourceGroup**. |
    | **Dettagli istanza** |  |
    | Nome | Immettere **myAsgWebServers**. |
    | Region | Select **(Stati Uniti) Stati Uniti orientali**. | 

5. Selezionare la scheda **Verifica + crea** oppure selezionare il pulsante **Verifica blu + crea** nella parte inferiore della pagina.

6. Selezionare **Crea**.

7. Ripetere il passaggio 4, specificando i valori seguenti:

    | Impostazione | valore |
    | ------- | ----- |
    |**Dettagli del progetto** |  |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Resource group | Selezionare **myResourceGroup**. |
    | **Dettagli istanza** |  |
    | Nome | Immettere **myAsgMgmtServers**. |
    | Region | Select **(Stati Uniti) Stati Uniti orientali**. |

8. Selezionare la scheda **Verifica + crea** oppure selezionare il pulsante **Verifica blu + crea** nella parte inferiore della pagina.

9. Selezionare **Crea**.

## <a name="create-a-network-security-group"></a>Creare un gruppo di sicurezza di rete

Un gruppo di sicurezza di rete protegge il traffico di rete nella rete virtuale.

1. Selezionare **Crea una risorsa** nell'angolo superiore sinistro del portale.

2. Nella casella di ricerca immettere **gruppo di sicurezza di rete**. Selezionare **gruppo di sicurezza di rete** nei risultati della ricerca.

3. Nella pagina **gruppo di sicurezza di rete** selezionare **Crea**.

4. In **Crea gruppo di sicurezza di rete** immettere o selezionare queste informazioni nella scheda **nozioni di base** :

    | Impostazione | valore |
    | ------- | ----- |
    | **Dettagli del progetto** |   |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Resource group | Selezionare **myResourceGroup**. |
    | **Dettagli istanza** |   |
    | Nome | Immettere **myNSG**. |
    | Location | Select **(Stati Uniti) Stati Uniti orientali**. | 

5. Selezionare la scheda **Verifica + crea** oppure selezionare il pulsante **Verifica blu + crea** nella parte inferiore della pagina.

6. Selezionare **Crea**.

## <a name="associate-network-security-group-to-subnet"></a>Associare il gruppo di sicurezza di rete alla subnet

In questa sezione si associerà il gruppo di sicurezza di rete alla subnet della rete virtuale creata in precedenza.

1. Nella casella **Cerca risorse, servizi e documentazione** nella parte superiore del portale iniziare a digitare **myNsg**. Selezionare **myNsg** quando viene visualizzato nei risultati della ricerca.

2. Nella pagina Panoramica di **myNSG** selezionare **Subnet** in **Impostazioni**.

3. Nella pagina **Impostazioni** selezionare **associa**:

    :::image type="content" source="./media/tutorial-filter-network-traffic/associate-nsg-subnet.png" alt-text="Associare NSG alla subnet." border="true":::

3. In **associa subnet** selezionare **rete virtuale** e quindi selezionare **myVNet**. 

4. Selezionare **subnet**, selezionare **predefinito**, quindi fare clic su **OK**.

## <a name="create-security-rules"></a>Creare regole di sicurezza

1. In **Impostazioni** di **myNSG** Selezionare **regole di sicurezza in ingresso**.

2. In **regole di sicurezza in ingresso** selezionare **+ Aggiungi**:

    :::image type="content" source="./media/tutorial-filter-network-traffic/add-inbound-rule.png" alt-text="Aggiungere la regola di sicurezza in ingresso." border="true":::

3. Creare una regola di sicurezza che consenta le porte 80 e 443 per il gruppo di sicurezza delle applicazioni **myAsgWebServers**. In **Aggiungi regola di sicurezza in ingresso** immettere o selezionare le informazioni seguenti:

    | Impostazione | Valore |
    | ------- | ----- |
    | Source (Sorgente) | Lasciare il valore predefinito di **any**. |
    | Intervalli di porte di origine | Lascia il valore predefinito di **(*)** |
    | Destination | Selezionare **gruppo di sicurezza dell'applicazione**. |
    | Gruppo di sicurezza dell'applicazione di destinazione | Selezionare **myAsgWebServers**. |
    | Servizio | Lasciare l'impostazione predefinita **personalizzata**. |
    | Intervalli di porte di destinazione | Immettere **80.443**. |
    | Protocollo | selezionare **TCP**. |
    | Azione | Lasciare l'impostazione predefinita **Consenti**. |
    | Priorità | Lasciare il valore predefinito di **100**. |
    | Nome | Immettere **Allow-Web-all**. |

    :::image type="content" source="./media/tutorial-filter-network-traffic/inbound-security-rule.png" alt-text="Regola di sicurezza in ingresso." border="true":::

3. Completare di nuovo il passaggio 2, usando i valori seguenti:

    | Impostazione | Valore |
    | ------- | ----- |
    | Source (Sorgente) | Lasciare il valore predefinito di **any**. |
    | Intervalli di porte di origine | Lascia il valore predefinito di **(*)** |
    | Destination | Selezionare **gruppo di sicurezza dell'applicazione**. |
    | Gruppo di sicurezza dell'applicazione di destinazione | Selezionare **myAsgMgmtServers**. |
    | Servizio | Lasciare l'impostazione predefinita **personalizzata**. |
    | Intervalli di porte di destinazione | Immettere **3389**. |
    | Protocollo | selezionare **TCP**. |
    | Azione | Lasciare l'impostazione predefinita **Consenti**. |
    | Priorità | Lasciare il valore predefinito di **110**. |
    | Nome | Immettere **Allow-RDP-all**. |

    > [!CAUTION]
    > In questo articolo il protocollo RDP (porta 3389) è esposto a Internet per la macchina virtuale assegnata al gruppo di sicurezza delle applicazioni **myAsgMgmtServers** . 
    >
    > Per gli ambienti di produzione, anziché esporre la porta 3389 a Internet, è consigliabile connettersi alle risorse di Azure che si vuole gestire con una VPN, una connessione di rete privata o un bastione di Azure.
    >
    > Per altre informazioni su Azure Bastion, vedere [Informazioni su Azure Bastion](../bastion/bastion-overview.md).

Dopo aver completato i passaggi da 1 a 3, esaminare le regole create. L'elenco dovrebbe essere simile all'elenco nell'esempio seguente:

:::image type="content" source="./media/tutorial-filter-network-traffic/security-rules.png" alt-text="Regole di sicurezza." border="true":::

## <a name="create-virtual-machines"></a>Creare macchine virtuali

Creare due VM nella rete virtuale.

### <a name="create-the-first-vm"></a>Creare la prima VM

1. Selezionare **Crea una risorsa** nell'angolo superiore sinistro del portale.

2. Selezionare **calcolo**, quindi selezionare **macchina virtuale**.

3. In **creare una macchina virtuale** immettere o selezionare queste informazioni nella scheda **nozioni di base** :

    | Impostazione | valore |
    | ------- | ----- |
    | **Dettagli del progetto** |  |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Resource group | Selezionare **myResourceGroup**. |
    | **Dettagli istanza** |   |
    | Nome macchina virtuale | Immettere **myVMWeb**. |
    | Region | Select **(Stati Uniti) Stati Uniti orientali**. |
    | Opzioni di disponibilità | Lasciare invariata l'impostazione predefinita nessuna ridondanza. |
    | Immagine | Selezionare **Windows Server 2019 datacenter-Gen1**. |
    | Istanza Spot di Azure | Lasciare deselezionata l'opzione predefinita. |
    | Dimensione | Selezionare **Standard_D2s_V3**. |
    | **Account amministratore** |   |
    | Username | Immettere un nome utente. |
    | Password | Immettere una password. |
    | Conferma password | Reimmettere la password. |
    | **Regole porta in ingresso** |   |
    | Porte in ingresso pubbliche | Selezionare **Nessuno**. |

4. Selezionare la scheda **Rete**.

5. Nella scheda **Rete** immettere o selezionare le informazioni seguenti:

    | Impostazione | Valore |
    | ------- | ----- |
    | **Interfaccia di rete** |   |
    | Rete virtuale | Selezionare **myVNET**. |
    | Subnet | Selezionare **predefinito (10.0.0.0/24)**. |
    | IP pubblico | Lasciare il valore predefinito di un nuovo indirizzo IP pubblico. |
    | Gruppo di sicurezza di rete della scheda di interfaccia di rete | Selezionare **Nessuno**. | 

6. Selezionare la scheda **Verifica + crea** oppure selezionare il pulsante **Verifica blu + crea** nella parte inferiore della pagina.

7. Selezionare **Crea**.

### <a name="create-the-second-vm"></a>Creare la seconda VM

Completare di nuovo i passaggi 1-7, ma nel passaggio 3 assegnare alla macchina virtuale il nome **myVMMgmt**. La distribuzione della VM richiede alcuni minuti. 

Non continuare con il passaggio successivo fino a quando la macchina virtuale non viene distribuita.

## <a name="associate-network-interfaces-to-an-asg"></a>Associare le interfacce di rete a un gruppo di sicurezza delle applicazioni

Quando il portale ha creato le VM, ha creato un'interfaccia di rete per ogni VM e ha associato l'interfaccia di rete alla VM. 

Aggiungere l'interfaccia di rete per ogni VM a uno dei gruppi di sicurezza delle applicazioni creati in precedenza:

1. Nella casella **Cerca risorse, servizi e documentazione** nella parte superiore del portale iniziare a digitare **myVMWeb**. Quando la macchina virtuale **myVMWeb** viene visualizzata nei risultati della ricerca, selezionarla.

2. In **Impostazioni** selezionare **Rete**.  

3. Selezionare la scheda **gruppi di sicurezza delle applicazioni** , quindi selezionare **Configura gruppi di sicurezza dell'applicazione**.

    :::image type="content" source="./media/tutorial-filter-network-traffic/configure-app-sec-groups.png" alt-text="Configurare i gruppi di sicurezza delle applicazioni." border="true":::

4. In **Configura i gruppi di sicurezza delle applicazioni** selezionare **myAsgWebServers**. Selezionare **Salva**.

    :::image type="content" source="./media/tutorial-filter-network-traffic/select-asgs.png" alt-text="Selezionare gruppi di sicurezza dell'applicazione." border="true":::

5. Completare di nuovo i passaggi 1 e 2, cercando la macchina virtuale **myVMMgmt** e selezionando  **myAsgMgmtServers** ASG.

## <a name="test-traffic-filters"></a>Testare i filtri del traffico

1. Connettersi alla macchina virtuale **myVMMgmt** . Immettere **myVMMgmt** nella casella di ricerca nella parte superiore del portale. Selezionare **myVMMgmt** quando viene visualizzato nei risultati della ricerca. Scegliere il pulsante **Connetti**.

2. Selezionare **Scarica file RDP**.

3. Aprire il file RDP scaricato e selezionare **Connetti**. Immettere il nome utente e la password specificati al momento della creazione della VM.

4. Selezionare **OK**.

5. È possibile che venga visualizzato un avviso di certificato durante il processo di connessione. Se viene visualizzato un avviso, selezionare **Sì** o **continua** per continuare con la connessione.

    La connessione ha esito positivo, perché la porta 3389 è consentita in ingresso da Internet al gruppo di sicurezza delle applicazioni **myAsgMgmtServers** . 
    
    L'interfaccia di rete per **myVMMgmt** è associata al gruppo di sicurezza dell'applicazione **myAsgMgmtServers** e consente la connessione.

6. Aprire una sessione di PowerShell in **myVMMgmt**. Connettersi a **myVMWeb** usando l'esempio seguente: 

    ```powershell
    mstsc /v:myVmWeb
    ```

    La connessione RDP da **myVMMgmt** a **myVMWeb** ha esito positivo perché le macchine virtuali nella stessa rete possono comunicare con ogni porta per impostazione predefinita.
    
    Non è possibile creare una connessione RDP alla macchina virtuale **myVMWeb** da Internet. La regola di sicurezza per **myAsgWebServers** impedisce le connessioni alla porta 3389 in ingresso da Internet. Per impostazione predefinita, il traffico in ingresso da Internet viene negato a tutte le risorse.

7. Per installare Microsoft IIS nella macchina virtuale **myVMWeb** , immettere il comando seguente da una sessione di PowerShell nella macchina virtuale **myVMWeb** :

    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    ```

8. Al termine dell'installazione di IIS, disconnettersi dalla macchina virtuale **myVMWeb** , che lascia la connessione Desktop remoto della macchina virtuale **myVMMgmt** .

9. Disconnettersi dalla VM **myVMMgmt** .

10. Nella casella **Cerca risorse, servizi e documentazione** nella parte superiore del portale di Azure iniziare a digitare **myVMWeb** dal computer. Selezionare **myVMWeb** quando viene visualizzato nei risultati della ricerca. Si noti l'**indirizzo IP pubblico** della macchina virtuale. L'indirizzo illustrato nell'esempio seguente è 23.96.39.113, ma l'indirizzo è diverso:

    :::image type="content" source="./media/tutorial-filter-network-traffic/public-ip-address.png" alt-text="Indirizzo IP pubblico." border="true":::
    
11. Per verificare che sia possibile accedere al server Web **myVMWeb** da Internet, aprire un browser Internet nel computer e passare a `http://<public-ip-address-from-previous-step>` . 

Viene visualizzata la schermata iniziale di IIS, perché la porta 80 è consentita in ingresso da Internet al gruppo di sicurezza delle applicazioni **myAsgWebServers** . 

L'interfaccia di rete associata per **myVMWeb** è associata al gruppo di sicurezza dell'applicazione **myAsgWebServers** e consente la connessione. 

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare il gruppo di risorse e tutte le risorse in esso contenute:

1. Immettere **myResourceGroup** nella casella di **ricerca** nella parte superiore del portale. Selezionare **myResourceGroup** quando viene visualizzato nei risultati della ricerca.
2. Selezionare **Elimina gruppo di risorse**.
3. Immettere **myResourceGroup** in **DIGITARE IL NOME DEL GRUPPO DI RISORSE** e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione:

* Creazione di un gruppo di sicurezza di rete e relativa associazione a una subnet di rete virtuale. 
* Creazione di gruppi di sicurezza delle applicazioni per il Web e la gestione.
* Sono state create due macchine virtuali.
* Test del gruppo di sicurezza dell'applicazione filtro di rete.

Per altre informazioni sui gruppi di sicurezza di rete, vedere [Panoramica dei gruppi di sicurezza di rete](./network-security-groups-overview.md) e [Gestire un gruppo di sicurezza di rete](manage-network-security-group.md).

Per impostazione predefinita, Azure instrada il traffico tra subnet. È anche possibile ad esempio scegliere di instradare il traffico tra subnet tramite una VM che funge da firewall. 

Per informazioni su come creare una tabella di route, passare all'esercitazione successiva.
> [!div class="nextstepaction"]
> [Creare una tabella di route](./tutorial-create-route-table-portal.md)