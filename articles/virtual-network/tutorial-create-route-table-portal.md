---
title: Instradare il traffico di rete - Esercitazione - Portale di Azure
titlesuffix: Azure Virtual Network
description: In questa esercitazione si apprenderà come instradare il traffico di rete con una tabella di route usando il portale di Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/16/2021
ms.author: kumud
ms.openlocfilehash: 63ea6821097327498803abc6577f28e595883bc0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932931"
---
# <a name="tutorial-route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Esercitazione: Instradare il traffico di rete con una tabella di route usando il portale di Azure

Per impostazione predefinita, Azure instrada il traffico tra tutte le subnet di una rete virtuale. È possibile creare le proprie route per eseguire l'override del routing predefinito di Azure. Le route personalizzate sono utili quando, ad esempio, si vuole indirizzare il traffico tra subnet attraverso un'appliance virtuale di rete. In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare un'appliance virtuale di rete che indirizza il traffico
> * Creare una tabella di route
> * Creare una route
> * Associare una route a una subnet
> * Distribuire le macchine virtuali in subnet diverse
> * Indirizzare il traffico da una subnet a un'altra attraverso un'appliance virtuale di rete

Questa esercitazione usa il [portale di Azure](https://portal.azure.com). È anche possibile usare l'[interfaccia della riga di comando di Azure](tutorial-create-route-table-cli.md) o [Azure PowerShell](tutorial-create-route-table-powershell.md).

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, è necessario un account Azure con una sottoscrizione attiva. Se non si dispone di un account, è possibile [crearne uno gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="create-a-virtual-network"></a>Crea rete virtuale

1. Nel menu del portale di Azure selezionare **Crea una risorsa**. Da Azure **Marketplace selezionare rete**  >  **rete virtuale** o cercare **rete virtuale** nella casella di ricerca.

2. Selezionare **Crea**.

2. In **Crea rete virtuale** immettere o selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    | Subscription | Selezionare la propria sottoscrizione.|
    | Resource group | Selezionare **Crea nuovo**, immettere **myResourceGroup**. </br> Selezionare **OK**. |
    | Nome | Immettere **myVirtualNetwork**. |
    | Location | Select **(Stati Uniti) Stati Uniti orientali**.|

3. Selezionare la scheda **indirizzi IP** oppure fare clic sul pulsante **Avanti: indirizzi IP** nella parte inferiore della pagina.

4. In **spazio indirizzi IPv4** selezionare lo spazio degli indirizzi esistente e modificarlo in **10.0.0.0/16**.

4. Selezionare **+ Aggiungi subnet**, quindi immettere **public** per **nome subnet** e **10.0.0.0/24** per **intervallo di indirizzi subnet**.

5. Selezionare **Aggiungi**.

6. Selezionare **+ Aggiungi subnet**, quindi immettere **privato** per **nome subnet** e **10.0.1.0/24** per **intervallo di indirizzi subnet**.

7. Selezionare **Aggiungi**.

8. Selezionare **+ Aggiungi subnet**, quindi immettere **DMZ** per **nome subnet** e **10.0.2.0/24** per **intervallo di indirizzi subnet**.

9. Selezionare **Aggiungi**.

10. Selezionare la scheda **sicurezza** oppure fare clic sul pulsante **Avanti: sicurezza** nella parte inferiore della pagina.

11. In **BastionHost** selezionare **Abilita**. Immettere le informazioni seguenti:

    | Impostazione            | valore                      |
    |--------------------|----------------------------|
    | Nome bastion | Immettere **myBastionHost** |
    | Spazio indirizzi della subnet AzureBastionSubnet | Immettere **10.0.3.0/24** |
    | Indirizzo IP pubblico | Selezionare **Crea nuovo**. </br> Per **Nome** immettere **myBastionIP**. </br> Selezionare **OK**. |

12. Selezionare la scheda **Rivedi e crea** oppure il pulsante **Rivedi e crea**.

13. Selezionare **Crea**.

## <a name="create-an-nva"></a>Creare un'appliance virtuale di rete

Le appliance virtuali di rete (appliance virtuali) sono macchine virtuali utili per le funzioni di rete, ad esempio l'ottimizzazione del routing e del firewall. Questa esercitazione presuppone che si stia usando **Windows Server 2019 datacenter**. Se si desidera, è possibile selezionare un altro sistema operativo.

1. Nella parte superiore sinistra del portale selezionare **Crea una risorsa** > **Calcolo** > **Macchina virtuale**. 
   
2. In **Crea macchina virtuale** digitare o selezionare i valori nella scheda **Nozioni di base**:

    | Impostazione | Valore                                          |
    |-----------------------|----------------------------------|
    | **Dettagli del progetto** |  |
    | Subscription | Selezionare la sottoscrizione ad Azure |
    | Gruppo di risorse | Selezionare **MyResourceGroup** |
    | **Dettagli istanza** |  |
    | Nome macchina virtuale | Immettere **myVMNVA** |
    | Area | Selezionare **(Stati Uniti) Stati Uniti orientali** |
    | Opzioni di disponibilità | Selezionare **La ridondanza dell'infrastruttura non è richiesta** |
    | Immagine | Selezionare **Windows Server 2019 Datacenter** |
    | Istanza Spot di Azure | Selezionare **No** |
    | Dimensione | Scegliere le dimensioni della macchina virtuale o usare l'impostazione predefinita |
    | **Account amministratore** |  |
    | Username | Immettere un nome utente |
    | Password | Immettere una password |
    | Conferma password | Reimmettere la password |
    | **Regole porta in ingresso** |    |
    | Porte in ingresso pubbliche | Selezionare **Nessuno**. |
    |

3. Selezionare la scheda **Rete**, oppure selezionare **Avanti: Dischi**, quindi **Avanti: Rete**.
  
4. Nella scheda Rete selezionare o immettere:

    | Impostazione | Valore |
    |-|-|
    | **Interfaccia di rete** |  |
    | Rete virtuale | Selezionare **myVirtualNetwork**. |
    | Subnet | Selezionare **la** rete perimetrale |
    | IP pubblico | Selezionare **Nessuno** |
    | Gruppo di sicurezza di rete della scheda di interfaccia di rete | Selezionare **Basic**|
    | Rete porte in ingresso pubbliche | Selezionare **Nessuno**. |
   
5. Selezionare la scheda **Verifica + crea** oppure selezionare il pulsante **Verifica blu + crea** nella parte inferiore della pagina.
  
6. Rivedere le impostazioni e quindi selezionare **Crea**.

## <a name="create-a-route-table"></a>Creare una tabella di route

1. Nel menu del [portale di Azure](https://portal.azure.com) o dalla pagina **Home** selezionare **Crea una risorsa**.

2. Nella casella di ricerca immettere **Tabella di route**. Selezionare **Tabella di route** quando viene visualizzata nei risultati della ricerca.

3. Nella pagina **Tabella di route** selezionare **Crea**.

4. In **Crea tabella di route** nella scheda informazioni di **base** immettere o selezionare le informazioni seguenti:

    | Impostazione | valore |
    | ------- | ----- |
    | **Dettagli del progetto** |   |
    | Subscription | Selezionare la propria sottoscrizione.|
    | Resource group | Selezionare **myResourceGroup**. |
    | **Dettagli istanza** |    |
    | Region | Selezionare **Stati Uniti orientali**. |
    | Name | Immettere **myRouteTablePublic**. |
    | Propagazione delle route del gateway | Selezionare **Sì**. |

    :::image type="content" source="./media/tutorial-create-route-table-portal/create-route-table.png" alt-text="Creare una tabella di route, portale di Azure." border="true":::

5. Selezionare la scheda **Verifica + crea** oppure selezionare il pulsante **Verifica blu + crea** nella parte inferiore della pagina.

## <a name="create-a-route"></a>Creare una route

1. Passare al [portale di Azure](https://portal.azure.com) per gestire la tabella di route. Cercare e selezionare **Tabelle di route**.

2. Selezionare il nome della tabella di route **myRouteTablePublic**.

3. Nella sezione **Impostazioni** della pagina **myRouteTablePublic** selezionare **Route**.

4. Nella pagina Route selezionare il pulsante **+ Aggiungi** .

5. In **Aggiungi route** immettere o selezionare queste informazioni:

    | Impostazione | valore |
    | ------- | ----- |
    | Nome route | Immettere **ToPrivateSubnet** |
    | Prefisso indirizzo | Immettere **10.0.1.0/24** (l'intervallo di indirizzi della subnet **privata** creata in precedenza) |
    | Tipo hop successivo | Selezionare **Appliance virtuale**. |
    | Indirizzo hop successivo | Immettere **10.0.2.4** (un indirizzo compreso nell'intervallo di indirizzi della subnet **DMZ** ) |

6. Selezionare **OK**.

## <a name="associate-a-route-table-to-a-subnet"></a>Associare una route a una subnet

1. Passare al [portale di Azure](https://portal.azure.com) per gestire la rete virtuale. Cercare e selezionare **Reti virtuali**.

2. Selezionare il nome della rete virtuale **myVirtualNetwork**.

3. Nella sezione **Impostazioni** della pagina **myVirtualNetwork** selezionare **subnet**.

4. Nell'elenco subnet della rete virtuale selezionare **public**.

5. In **tabella di route** scegliere la tabella di route creata **myRouteTablePublic**. 

6. Selezionare **Save (Salva** ) per associare la tabella di route alla subnet **public** .

    :::image type="content" source="./media/tutorial-create-route-table-portal/associate-route-table.png" alt-text="Associare una tabella di route, un elenco di subnet, una rete virtuale portale di Azure." border="true":::

## <a name="turn-on-ip-forwarding"></a>Abilitare l'inoltro IP

Successivamente, attivare l'indirizzo IP per la nuova macchina virtuale di appliance virtuale di sistema, **myVMNVA**. Quando Azure invia il traffico di rete a **myVMNVA**, se il traffico è destinato a un indirizzo IP diverso, l'invio IP invia il traffico alla posizione corretta.

1. Passare al [portale di Azure](https://portal.azure.com) per gestire la macchina virtuale. Cercare e selezionare **Macchine virtuali**.

2. Selezionare il nome della macchina virtuale **myVMNVA**.

3. Nella pagina Panoramica di **myVMNVA** , in **Impostazioni**, selezionare **rete**.

4. Nella pagina **rete** di **myVMNVA** Selezionare l'interfaccia di rete accanto a **interfaccia di rete**.  Il nome dell'interfaccia inizierà con **myvmnva**.

    :::image type="content" source="./media/tutorial-create-route-table-portal/virtual-machine-networking.png" alt-text="Rete, macchina virtuale appliance virtuale di rete, portale di Azure" border="true":::

5. Nella pagina Panoramica dell'interfaccia di rete, in **Impostazioni**, selezionare **configurazioni IP**.

6. Nella pagina **configurazioni IP** impostare **inoltri IP** su **abilitato**, quindi selezionare **Salva**.

    :::image type="content" source="./media/tutorial-create-route-table-portal/enable-ip-forwarding.png" alt-text="Abilitare l'inoltro IP" border="true":::

## <a name="create-public-and-private-virtual-machines"></a>Creare macchine virtuali pubbliche e private

Creare una macchina virtuale pubblica e una macchina virtuale privata nella rete virtuale. In un secondo momento, li si userà per verificare che Azure instradi il traffico della subnet **pubblica** a quella **privata** attraverso l'appliance virtuale di rete.


### <a name="public-vm"></a>Macchina virtuale pubblica

1. Nella parte superiore sinistra del portale selezionare **Crea una risorsa** > **Calcolo** > **Macchina virtuale**. 
   
2. In **Crea macchina virtuale** digitare o selezionare i valori nella scheda **Nozioni di base**:

    | Impostazione | Valore                                          |
    |-----------------------|----------------------------------|
    | **Dettagli del progetto** |  |
    | Subscription | Selezionare la sottoscrizione ad Azure |
    | Gruppo di risorse | Selezionare **MyResourceGroup** |
    | **Dettagli istanza** |  |
    | Nome macchina virtuale | Immettere **myVMPublic** |
    | Area | Selezionare **(Stati Uniti) Stati Uniti orientali** |
    | Opzioni di disponibilità | Selezionare **La ridondanza dell'infrastruttura non è richiesta** |
    | Immagine | Selezionare **Windows Server 2019 Datacenter** |
    | Istanza Spot di Azure | Selezionare **No** |
    | Dimensione | Scegliere le dimensioni della macchina virtuale o usare l'impostazione predefinita |
    | **Account amministratore** |  |
    | Username | Immettere un nome utente |
    | Password | Immettere una password |
    | Conferma password | Reimmettere la password |
    | **Regole porta in ingresso** |    |
    | Porte in ingresso pubbliche | Selezionare **Nessuno**. |
    |

3. Selezionare la scheda **Rete**, oppure selezionare **Avanti: Dischi**, quindi **Avanti: Rete**.
  
4. Nella scheda Rete selezionare o immettere:

    | Impostazione | Valore |
    |-|-|
    | **Interfaccia di rete** |  |
    | Rete virtuale | Selezionare **myVirtualNetwork**. |
    | Subnet | Seleziona **pubblico** |
    | IP pubblico | Selezionare **Nessuno** |
    | Gruppo di sicurezza di rete della scheda di interfaccia di rete | Selezionare **Basic**|
    | Rete porte in ingresso pubbliche | Selezionare **Nessuno**. |
   
5. Selezionare la scheda **Verifica + crea** oppure selezionare il pulsante **Verifica blu + crea** nella parte inferiore della pagina.
  
6. Rivedere le impostazioni e quindi selezionare **Crea**.

### <a name="private-vm"></a>Macchina virtuale privata

1. Nella parte superiore sinistra del portale selezionare **Crea una risorsa** > **Calcolo** > **Macchina virtuale**. 
   
2. In **Crea macchina virtuale** digitare o selezionare i valori nella scheda **Nozioni di base**:

    | Impostazione | Valore                                          |
    |-----------------------|----------------------------------|
    | **Dettagli del progetto** |  |
    | Subscription | Selezionare la sottoscrizione ad Azure |
    | Gruppo di risorse | Selezionare **MyResourceGroup** |
    | **Dettagli istanza** |  |
    | Nome macchina virtuale | Immettere **myVMPrivate** |
    | Area | Selezionare **(Stati Uniti) Stati Uniti orientali** |
    | Opzioni di disponibilità | Selezionare **La ridondanza dell'infrastruttura non è richiesta** |
    | Immagine | Selezionare **Windows Server 2019 Datacenter** |
    | Istanza Spot di Azure | Selezionare **No** |
    | Dimensione | Scegliere le dimensioni della macchina virtuale o usare l'impostazione predefinita |
    | **Account amministratore** |  |
    | Username | Immettere un nome utente |
    | Password | Immettere una password |
    | Conferma password | Reimmettere la password |
    | **Regole porta in ingresso** |    |
    | Porte in ingresso pubbliche | Selezionare **Nessuno**. |
    |

3. Selezionare la scheda **Rete**, oppure selezionare **Avanti: Dischi**, quindi **Avanti: Rete**.
  
4. Nella scheda Rete selezionare o immettere:

    | Impostazione | Valore |
    |-|-|
    | **Interfaccia di rete** |  |
    | Rete virtuale | Selezionare **myVirtualNetwork**. |
    | Subnet | Selezionare **privato** |
    | IP pubblico | Selezionare **Nessuno** |
    | Gruppo di sicurezza di rete della scheda di interfaccia di rete | Selezionare **Basic**|
    | Rete porte in ingresso pubbliche | Selezionare **Nessuno**. |
   
5. Selezionare la scheda **Verifica + crea** oppure selezionare il pulsante **Verifica blu + crea** nella parte inferiore della pagina.
  
6. Rivedere le impostazioni e quindi selezionare **Crea**.

## <a name="route-traffic-through-an-nva"></a>Indirizzare il traffico attraverso un'appliance virtuale di rete

### <a name="sign-in-to-private-vm"></a>Accedere a una VM privata

1. Passare al [portale di Azure](https://portal.azure.com) per gestire la macchina virtuale privata. Cercare e selezionare **Macchine virtuali**.

2. Selezionare il nome della macchina virtuale privata **myVmPrivate**.

3. Nella barra dei menu della macchina virtuale selezionare **Connetti**, quindi selezionare **Bastion**.

4. Nella pagina **Connetti** selezionare il pulsante Blue **use Bastion** .

5. Nella pagina **Bastion** immettere il nome utente e la password creati in precedenza per la macchina virtuale.

6. Selezionare **Connetti**.

### <a name="configure-firewall"></a>Configurare il firewall

In un passaggio successivo viene usato lo strumento di tracciamento delle route per testare il routing. Il tracciamento delle route usa il protocollo ICMP (Internet Control Message Protocol), che viene rifiutato da Windows Firewall per impostazione predefinita. 

Abilitare ICPM in Windows Firewall.

1. Nella connessione Bastion di **myVMPrivate** aprire PowerShell con privilegi amministrativi.

2. Immettere questo comando:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    In questa esercitazione verrà usata la route di traccia per testare il routing. Per gli ambienti di produzione, non è consigliabile consentire il protocollo ICMP tramite Windows Firewall.

### <a name="turn-on-ip-forwarding-within-myvmnva"></a>Attivare l'inoltri IP in myVMNVA

Si è [attivato l'inoltro IP](#turn-on-ip-forwarding) per interfaccia di rete della macchina virtuale tramite Azure. È inoltre necessario che il sistema operativo della macchina virtuale inoltri il traffico di rete. 

Attivare l'invio IP per **myVMNVA** con questi comandi.

1. Da PowerShell nella VM **myVMPrivate** aprire un desktop remoto alla macchina virtuale **myVMNVA** :

    ```powershell
    mstsc /v:myvmnva
    ```

2. Da PowerShell nella VM **myVMNVA** , immettere questo comando per attivare l'invio IP:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```

3. Riavviare **myVMNVA**.

    ```powershell
    Restart-Computer
    ```

4. Dopo il riavvio di **myVMNVA** , creare una sessione Desktop remoto in **myVMPublic**. 
    
    Mentre si è ancora connessi a **myVMPrivate**, aprire PowerShell ed eseguire il comando seguente:

    ```powershell
    mstsc /v:myvmpublic
    ```
5. Nel desktop remoto di **myVMPublic** aprire PowerShell.

6. Abilitare il protocollo ICMP tramite Windows Firewall immettendo il comando seguente:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

## <a name="test-the-routing-of-network-traffic"></a>Testare il routing del traffico di rete

Per prima cosa, è necessario testare il routing del traffico di rete da **myVMPublic** a **myVMPrivate**.

1. Da PowerShell in **myVMPublic**, immettere questo comando:

    ```powershell
    tracert myvmprivate
    ```

    La risposta restituita è simile all'esempio seguente:

    ```powershell
    Tracing route to myvmprivate.q04q2hv50taerlrtdyjz5nza1f.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:

      1     1 ms     *        2 ms  myvmnva.internal.cloudapp.net [10.0.2.4]
      2     2 ms     1 ms     1 ms  myvmprivate.internal.cloudapp.net [10.0.1.4]

    Trace complete.
    ```

    * È possibile vedere il primo hop è 10.0.2.4, che è l'indirizzo IP privato **di myVMNVA** . 

    * Il secondo hop viene indirizzato all'indirizzo IP privato di **myVMPrivate**: 10.0.1.4. 

    In precedenza è stata aggiunta la route alla tabella di route **myRouteTablePublic** ed è stata associata alla subnet *Public* (pubblica). Azure ha inviato il traffico attraverso l'appliance virtuale di Azure e non direttamente alla subnet *privata* .

2. Chiudere la sessione Desktop remoto in **myVMPublic**, che lascia ancora connessa a **myVMPrivate**.

3. Aprire PowerShell in **myVMPrivate**, immettere questo comando:

    ```powershell
    tracert myvmpublic
    ```

    Questo comando testa il routing del traffico di rete dalla macchina virtuale *myVmPrivate* alla macchina virtuale *myVmPublic*. La risposta restituita è simile all'esempio seguente:

    ```cmd
    Tracing route to myvmpublic.q04q2hv50taerlrtdyjz5nza1f.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:

      1     1 ms     1 ms     1 ms  myvmpublic.internal.cloudapp.net [10.0.0.4]

    Trace complete.
    ```

    Come si può notare, Azure instrada il traffico direttamente da *myVMPrivate* a *myVMPublic*. Per impostazione predefinita, Azure instrada il traffico direttamente tra subnet.

4. Chiudere la sessione Bastion a **myVMPrivate**.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando il gruppo di risorse non è più necessario, eliminare **myResourceGroup** e tutte le risorse in esso contenute:

1. Passare al [portale di Azure](https://portal.azure.com) per gestire il gruppo di risorse. Cercare e selezionare **Gruppi di risorse**.

2. Selezionare il nome del gruppo di risorse **myResourceGroup**.

3. Selezionare **Elimina gruppo di risorse**.

4. Nella finestra di dialogo di conferma dell'eliminazione, nella casella **DIGITARE IL NOME DEL GRUPPO DI RISORSE** immettere **myResourceGroup** e quindi selezionare **Elimina**. 


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione:

* Creazione di una tabella di route e relativa associazione a una subnet.
* È stata creata una semplice appliance virtuale di dominio che ha indirizzato il traffico da una subnet pubblica a una subnet privata. 

È possibile distribuire appliance virtuali preconfigurate diverse da [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking), che forniscono molte funzioni di rete utili. 

Per altre informazioni sul routing, vedere [Panoramica del routing](virtual-networks-udr-overview.md) e [Gestire una tabella di route](manage-route-table.md).

Per filtrare il traffico di rete in una rete virtuale, vedere:
> [!div class="nextstepaction"]
> [Esercitazione: filtrare il traffico di rete con un gruppo di sicurezza di rete usando il portale di Azure](tutorial-filter-network-traffic.md)
