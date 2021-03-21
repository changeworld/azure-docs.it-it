---
title: 'Esercitazione: integrare il gateway NAT con un servizio di bilanciamento del carico pubblico-portale di Azure'
titleSuffix: Virtual Network NAT
description: In questa esercitazione si apprenderà come integrare un gateway NAT di rete virtuale con un servizio di bilanciamento del carico pubblico usando il portale di Azure.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/19/2021
ms.custom: template-tutorial
ms.openlocfilehash: 345ccb68ebb31460f4a75b31a7d3a946160da6e6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104657973"
---
# <a name="tutorial-integrate-a-nat-gateway-with-a-public-load-balancer-using-the-azure-portal"></a>Esercitazione: integrare un gateway NAT con un servizio di bilanciamento del carico pubblico usando il portale di Azure

In questa esercitazione si apprenderà come integrare un gateway NAT con un servizio di bilanciamento del carico pubblico.

Per impostazione predefinita, un Load Balancer Standard di Azure è protetto. La connettività in uscita viene definita in modo esplicito abilitando SNAT in uscita (Source Network Address Translation). SNAT è abilitato in una regola di bilanciamento del carico o in regole in uscita. 

L'integrazione del gateway NAT sostituisce la necessità di regole in uscita per il pool back-end in uscita SNAT. 

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare un'istanza di Azure Load Balancer
> * Creare due macchine virtuali per il pool back-end della Azure Load Balancer
> * Creare un gateway NAT
> * Convalidare la connettività in uscita delle macchine virtuali nel pool back-end del servizio di bilanciamento del carico

## <a name="prerequisites"></a>Prerequisiti

Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-load-balancer"></a>Creare un servizio di bilanciamento del carico

In questa sezione verrà creato un Azure Load Balancer standard. 

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Crea una risorsa**. 
3. Nella casella di ricerca immettere **Load Balancer**. Selezionare **Load Balancer** nei risultati della ricerca.
4. Nella pagina **bilanciamento del carico** selezionare **Crea**.
5. Nella pagina **Crea** servizio di bilanciamento del carico immettere o selezionare le informazioni seguenti: 

    | Impostazione                 | Valore                                              |
    | ---                     | ---                                                |
    | Subscription               | Selezionare la propria sottoscrizione.    |    
    | Resource group         | Selezionare **Crea nuovo** e immettere **TutorPubLBNAT-RG** nella casella di testo.|
    | Nome                   | Immettere **myLoadBalancer**                                   |
    | Region         | Select **(Stati Uniti) Stati Uniti orientali**.                                        |
    | Type          | Selezionare **Pubblica**.                                        |
    | SKU           | Lasciare lo **standard** predefinito. |
    | Livello          | Lasciare l'impostazione predefinita **Regional**. |
    | Indirizzo IP pubblico | Selezionare **Crea nuovo**. Se si vuole usare un indirizzo IP pubblico esistente, selezionare **Usa esistente**. |
    | Nome dell'indirizzo IP pubblico | Digitare **myPublicIP-lb** nella casella di testo.|
    | Zona di disponibilità | Selezionare **Con ridondanza della zona** per creare un servizio di bilanciamento del carico resiliente. Per creare un servizio di bilanciamento del carico di zona, selezionare una zona specifica tra 1, 2 o 3 |
    | Aggiungi un indirizzo IPv6 pubblico | Selezionare **No**. </br> Per altre informazioni sugli indirizzi IPv6 e il servizio di bilanciamento del carico, vedere [Che cos'è IPv6 per la rete virtuale di Azure?](../virtual-network/ipv6-overview.md)  |
    | Preferenza di routing | Lasciare l'impostazione predefinita **rete Microsoft**. </br> Per ulteriori informazioni sulle preferenze di routing, vedere informazioni sulle preferenze di [routing (anteprima)](../virtual-network/routing-preference-overview.md). |

6. Accettare i valori predefiniti per le impostazioni rimanenti e quindi selezionare **Rivedi e crea**.

7. Nella scheda **Rivedi e crea** selezionare **Crea**.

## <a name="create-load-balancer-resources"></a>Creare risorse di bilanciamento del carico

In questa sezione vengono configurati:

* Le impostazioni del servizio di bilanciamento del carico per un pool di indirizzi back-end.
* Un probe di integrità.
* Una regola di bilanciamento del carico.

### <a name="create-a-backend-pool"></a>Creare un pool back-end

Un pool di indirizzi back-end contiene gli indirizzi IP delle schede di interfaccia di rete virtuale connesse al bilanciamento del carico. 

Creare il pool di indirizzi back-end **myBackendPool** per includere le macchine virtuali per il bilanciamento del carico del traffico Internet.

1. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine selezionare **myLoadBalancer** nell'elenco di risorse.

2. In **Impostazioni** selezionare **Pool back-end** e quindi selezionare **Aggiungi**.

3. Nella pagina **Aggiungi pool back-end** digitare **myBackEndPool** come nome per il pool back-end e quindi selezionare **Aggiungi**.

### <a name="create-a-health-probe"></a>Creare un probe di integrità

Il servizio di bilanciamento del carico monitora lo stato dell'app con un probe di integrità. 

Il probe di integrità aggiunge o rimuove le macchine virtuali nel servizio di bilanciamento del carico in base alla rispettiva risposta ai controlli di integrità. 

Creare un probe di integrità denominato **myHealthProbe** per monitorare l'integrità delle macchine virtuali.

1. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine selezionare **myLoadBalancer** nell'elenco di risorse.

2. In **Impostazioni** selezionare **Probe integrità** e quindi selezionare **Aggiungi**.
    
    | Impostazione | Valore |
    | ------- | ----- |
    | Nome | Immettere **myHealthProbe**. |
    | Protocollo | selezionare **TCP**. |
    | Porta | Immettere **80**.|
    | Interval | Immettere **15** in **Intervallo** come numero di secondi tra i tentativi del probe. |
    | Soglia non integra | Selezionare **2** per **Soglia di non integrità** come numero di errori di probe consecutivi che devono verificarsi prima che una macchina virtuale venga considerata non integra.|
   

3. Lasciare le impostazioni predefinite REST e selezionare **Aggiungi**.

### <a name="create-a-load-balancer-rule"></a>Creare una regola di bilanciamento del carico

Una regola di bilanciamento del carico consente di definire come il traffico verrà distribuito alle VM. È possibile definire la configurazione IP front-end per il traffico in ingresso e il pool IP back-end che riceve il traffico. La porta di origine e quella di destinazione vengono definite nella regola. 

In questa sezione verrà creata una regola di bilanciamento del carico:

* Denominata **myHTTPRule**.
* Nel front-end denominato **LoadBalancerFrontEnd**.
* In ascolto sulla **porta 80**.
* Che indirizza il traffico con carico bilanciato al back-end denominato **myBackendPool** sulla **porta 80**.

1. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine selezionare **myLoadBalancer** nell'elenco di risorse.

2. In **Impostazioni** selezionare **Regole di bilanciamento del carico** e quindi selezionare **Aggiungi**.

3. Usare questi valori per configurare la regola di bilanciamento del carico:
    
    | Impostazione | Valore |
    | ------- | ----- |
    | Nome | Immettere **myHTTPRule**. |
    | Versione indirizzo IP | Selezionare **IPv4** |
    | Indirizzo IP front-end | Selezionare **LoadBalancerFrontend** |
    | Protocollo | selezionare **TCP**. |
    | Porta | Immettere **80**.|
    | Porta back-end | Immettere **80**. |
    | Pool back-end | Selezionare **myBackendPool**.|
    | Probe di integrità | Selezionare **myHealthProbe**. |
    | Timeout di inattività (minuti) | Spostare il dispositivo di scorrimento su **15** minuti. |
    | Reimpostazione TCP | Selezionare **Enabled**. |
    | SNAT (Network Address Translation) di origine in uscita | Selezionare **(Scelta consigliata) Usa regole in uscita per fornire l'accesso a Internet ai membri del pool back-end**. |

4. Accettare tutte le impostazioni predefinite e quindi selezionare **OK**.

## <a name="create-the-virtual-network"></a>Creare la rete virtuale

In questa sezione si crea una rete virtuale e una subnet.

1. In alto a sinistra nella schermata selezionare **Crea una risorsa > Rete > Rete virtuale** o cercare **Rete virtuale** nella casella di ricerca.

2. Selezionare **Crea**. 

3. In **Crea rete virtuale** immettere o selezionare queste informazioni nella scheda **Generale**:

    | **Impostazione**          | **Valore**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Dettagli del progetto**  |                                                                 |
    | Subscription     | Selezionare la sottoscrizione ad Azure                                  |
    | Gruppo di risorse   | Selezionare **TutorPubLBNAT-RG** |
    | **Dettagli istanza** |                                                                 |
    | Nome             | Immettere **myVNet**                                    |
    | Region           | Selezionare **Stati Uniti orientali**. |

4. Selezionare la scheda **Indirizzi IP** oppure il pulsante **Avanti: Indirizzi IP** nella parte inferiore della pagina.

5. Nella scheda **Indirizzi IP** immettere queste informazioni:

    | Impostazione            | valore                      |
    |--------------------|----------------------------|
    | Spazio indirizzi IPv4 | Immettere **10.1.0.0/16** |

6. In **Nome subnet** selezionare la parola **predefinito**.

7. In **Modifica subnet** immettere queste informazioni:

    | Impostazione            | valore                      |
    |--------------------|----------------------------|
    | Nome della subnet | Immettere **myBackendSubnet** |
    | Intervallo di indirizzi subnet | Immettere **10.1.0.0/24** |

8. Selezionare **Salva**.

9. Selezionare la scheda **Sicurezza**.

10. In **BastionHost** selezionare **Abilita**. Immettere le informazioni seguenti:

    | Impostazione            | valore                      |
    |--------------------|----------------------------|
    | Nome bastion | Immettere **myBastionHost** |
    | Spazio indirizzi della subnet AzureBastionSubnet | Immettere **10.1.1.0/24** |
    | Indirizzo IP pubblico | Selezionare **Crea nuovo**. </br> Per **Nome** immettere **myBastionIP**. </br> Selezionare **OK**. |


11. Selezionare la scheda **Rivedi e crea** oppure il pulsante **Rivedi e crea**.

12. Selezionare **Crea**.

## <a name="create-virtual-machines"></a>Creare macchine virtuali

In questa sezione verranno create due macchine virtuali (**myVM1** e **myVM2**) in due zone diverse (**zona 1** e **zona 2**).

Queste macchine virtuali vengono aggiunte al pool back-end del servizio di bilanciamento del carico creato in precedenza.

1. Nella parte superiore sinistra del portale selezionare **Crea una risorsa** > **Calcolo** > **Macchina virtuale**. 
   
2. In **Crea macchina virtuale** digitare o selezionare i valori nella scheda **Nozioni di base**:

    | Impostazione | Valore                                          |
    |-----------------------|----------------------------------|
    | **Dettagli del progetto** |  |
    | Subscription | Selezionare la sottoscrizione ad Azure |
    | Gruppo di risorse | Selezionare **TutorPubLBNAT-RG** |
    | **Dettagli istanza** |  |
    | Nome macchina virtuale | Immettere **myVM1** |
    | Region | Selezionare **Stati Uniti orientali**. |
    | Opzioni di disponibilità | Selezionare **Zone di disponibilità** |
    | Zona di disponibilità | Selezionare **1** |
    | Immagine | Selezionare **Windows Server 2019 Datacenter** |
    | Istanza Spot di Azure | Lasciare l'impostazione predefinita |
    | Dimensione | Scegliere le dimensioni della macchina virtuale o usare l'impostazione predefinita |
    | **Account amministratore** |  |
    | Username | Immettere un nome utente |
    | Password | Immettere una password |
    | Conferma password | Reimmettere la password |
    | **Regole porta in ingresso** |  |
    | Porte in ingresso pubbliche | Selezionare **Nessuno** |

3. Selezionare la scheda **Rete**, oppure selezionare **Avanti: Dischi**, quindi **Avanti: Rete**.
  
4. Nella scheda Rete selezionare o immettere:

    | Impostazione | Valore |
    |-|-|
    | **Interfaccia di rete** |  |
    | Rete virtuale | **myVNet** |
    | Subnet | **myBackendSubnet** |
    | IP pubblico | Selezionare **Nessuno**. |
    | Gruppo di sicurezza di rete della scheda di interfaccia di rete | Selezionare **Avanzato**|
    | Configura gruppo di sicurezza di rete | Selezionare **Crea nuovo**. </br> Nella pagina **Crea gruppo di sicurezza di rete** immettere **myNSG** in **Nome**. </br> In **Regole in ingresso** selezionare **+ Aggiungi una regola in ingresso**. </br> In **Intervalli di porte di destinazione** immettere **80**. </br> In **Priorità** immettere **100**. </br> In **Nome** immettere **myHTTPRule** </br> Selezionare **Aggiungi** </br> Selezionare **OK**. |
    | **Bilanciamento del carico**  |
    | Posizionare questa macchina virtuale dietro una soluzione di bilanciamento del carico esistente? | Selezionare **Sì** |
    | **Impostazioni di bilanciamento del carico** |
    | Opzioni di bilanciamento del carico | Selezionare il servizio di **bilanciamento del carico di Azure** |
    | Selezionare un servizio di bilanciamento del carico | Selezionare **myLoadBalancer**  |
    | Selezionare un pool back-end | Selezionare **myBackendPool** |
   
5. Selezionare **Rivedi e crea**. 
  
6. Rivedere le impostazioni e quindi selezionare **Crea**.

7. Seguire i passaggi da 1 a 7 per creare una macchina virtuale con i valori seguenti e tutte le altre impostazioni come **myVM1**:

    | Impostazione | VM 2 |
    | ------- | ----- |
    | Nome |  **myVM2** |
    | Zona di disponibilità | **2** |
    | Gruppo di sicurezza di rete | Selezionare il gruppo di sicurezza di rete **myNSG** esistente| 

## <a name="create-nat-gateway"></a>Creare il gateway NAT

In questa sezione si creerà un gateway NAT che verrà assegnato alla subnet nella rete virtuale creata in precedenza.

1. Sul lato superiore sinistro della schermata selezionare **Crea una risorsa > rete > gateway NAT** o Cerca **gateway NAT** nella casella di ricerca.

2. Selezionare **Crea**. 

3. In **crea Network Address Translation (NAT) gateway** immettere o selezionare queste informazioni nella scheda **nozioni di base** :

    | **Impostazione**          | **Valore**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Dettagli del progetto**  |                                                                 |
    | Subscription     | Selezionare la sottoscrizione di Azure.                                  |
    | Gruppo di risorse   | Selezionare **TutorPubLBNAT-RG**. |
    | **Dettagli istanza** |                                                                 |
    | Nome             | Immettere **myNATGateway**                                    |
    | Area           | Selezionare **(Stati Uniti) Stati Uniti orientali**  |
    | Zona di disponibilità | Selezionare **Nessuno**. |
    | Timeout di inattività (minuti) | Immettere **10**. |

4. Selezionare la scheda **IP in uscita** oppure fare clic sul pulsante **Avanti: IP in uscita** nella parte inferiore della pagina.

5. Nella scheda **IP in uscita** immettere o selezionare le informazioni seguenti:

    | **Impostazione** | **Valore** |
    | ----------- | --------- |
    | Indirizzi IP pubblici | Selezionare **Crea un nuovo indirizzo IP pubblico**. </br> In **nome** immettere **myPublicIP-NAT**. </br> Selezionare **OK**. |

6. Selezionare la scheda **subnet** oppure fare clic sul pulsante **Avanti: Subnet** nella parte inferiore della pagina.

7. Nella scheda **subnet** selezionare **myVNet** nel pannello a discesa **rete virtuale** .

8. Selezionare la casella accanto a **myBackendSubnet**.

9. Selezionare la scheda **Verifica + crea** oppure selezionare il pulsante **Verifica blu + crea** nella parte inferiore della pagina.

10. Selezionare **Crea**.

## <a name="test-nat-gateway"></a>Testare il gateway NAT

In questa sezione verrà testato il gateway NAT. Si scoprirà prima l'indirizzo IP pubblico del gateway NAT. Ci si connette quindi alla macchina virtuale di test e si verifica la connessione in uscita tramite il gateway NAT.
    
1. Trovare l'indirizzo IP pubblico per il gateway NAT nella schermata **Panoramica** . Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine selezionare **myPublicIP**.

2. Prendere nota dell'indirizzo IP pubblico:

    :::image type="content" source="./media/tutorial-nat-gateway-load-balancer-public-portal/find-public-ip.png" alt-text="Individuare l'indirizzo IP pubblico del gateway NAT" border="true":::

3. Selezionare **tutti i servizi** nel menu a sinistra, selezionare **tutte le risorse** e quindi nell'elenco delle risorse selezionare **myVM1** che si trova nel gruppo di risorse **TutorPubLBNAT-RG** .

4. Nella pagina **Panoramica** selezionare **Connetti**, quindi **Bastion**.

5. Selezionare il pulsante blu **Usa Bastion**.

6. Immettere il nome utente e la password specificati durante la creazione della VM.

7. Aprire **Internet Explorer** in **myVM1**.

8. Immettere **https://whatsmyip.com** nella barra degli indirizzi.

9. Verificare che l'indirizzo IP visualizzato corrisponda all'indirizzo del gateway NAT annotato nel passaggio precedente:

    :::image type="content" source="./media/tutorial-nat-gateway-load-balancer-public-portal/my-ip.png" alt-text="Internet Explorer che mostra l'indirizzo IP in uscita esterno" border="true":::

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare questa applicazione, eliminare la rete virtuale, la macchina virtuale e il gateway NAT con i passaggi seguenti:

1. Nel menu a sinistra selezionare **Gruppi di risorse**.

2. Selezionare il gruppo di risorse **TutorPubLBNAT-RG** .

3. Selezionare **Elimina gruppo di risorse**.

4. Immettere **TutorPubLBNAT-RG** e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla rete virtuale di Azure NAT, vedere:
> [!div class="nextstepaction"]
> [Panoramica NAT della rete virtuale](nat-overview.md)
