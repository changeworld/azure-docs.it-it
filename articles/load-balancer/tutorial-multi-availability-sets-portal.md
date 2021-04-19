---
title: 'Esercitazione: Creare un servizio di bilanciamento del carico con più set di disponibilità nel pool back-end - portale di Azure'
titleSuffix: Azure Load Balancer
description: In questa esercitazione viene distribuito un Azure Load Balancer con più di un set di disponibilità nel pool back-end.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 04/16/2021
ms.custom: template-tutorial
ms.openlocfilehash: 21ff43217a7b2bd874a384f3b07a48d5223a1be2
ms.sourcegitcommit: 089c2bd1ac4861f43c4b89396d3d056a6eef4913
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107602355"
---
# <a name="tutorial-create-a-load-balancer-with-more-than-one-availability-set-in-the-backend-pool-using-the-azure-portal"></a>Esercitazione: Creare un servizio di bilanciamento del carico con più set di disponibilità nel pool back-end usando il portale di Azure

Come parte di una distribuzione a disponibilità elevata, le macchine virtuali vengono spesso raggruppate in più set di disponibilità. 

Load Balancer supporta più set di disponibilità con macchine virtuali nel pool back-end.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Crea rete virtuale
> * Creare un gateway NAT per la connettività in uscita
> * Creare uno SKU standard Azure Load Balancer
> * Creare quattro macchine virtuali e due set di disponibilità
> * Aggiungere macchine virtuali nei set di disponibilità al pool back-end del servizio di bilanciamento del carico
> * Testare il bilanciamento del carico

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-network"></a>Creare una rete virtuale

In questa sezione si creerà una rete virtuale per il servizio di bilanciamento del carico e le altre risorse usate nell'esercitazione.

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Nella casella di ricerca nella parte superiore del portale immettere **Rete virtuale.**

3. Nei risultati della ricerca selezionare **Reti virtuali**.

4. Selezionare **[+] Create** ([+] Crea).

5. Nella scheda **Informazioni di** base di Crea **rete virtuale** immettere o selezionare le informazioni seguenti:

    | Impostazione | valore |
    | ------- | ------|
    | **Dettagli del progetto** |   |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Gruppo di risorse | Selezionare **Crea nuovo**. </br> Immettere **TutorLBmultiAVS-rg** in **Nome**. |
    | **Dettagli istanza** |   |
    | Nome | Immettere **myVNet**. |
    | Region | Selezionare **(Stati Uniti) Stati Uniti occidentali 2**. |

6. Selezionare la **scheda Indirizzi IP** o il pulsante **Avanti: Indirizzi IP** nella parte inferiore della pagina.

7. Nella scheda **Indirizzi IP** in Nome **subnet selezionare** **predefinito.**

8. Nel riquadro **Modifica subnet** immettere **myBackendSubnet** in **Nome** subnet.

9. Selezionare **Salva**.

10. Selezionare la **scheda** Sicurezza o il **pulsante Avanti: Sicurezza** nella parte inferiore della pagina.

11. Nella scheda **Security** (Sicurezza) in **BastionHost** selezionare **Enable (Abilita).**

12. Immettere o selezionare le informazioni seguenti:

    | Impostazione | valore |
    | ------- | ----- |
    | Nome bastion | Immettere **MyBastionHost.** |
    | Spazio indirizzi della subnet AzureBastionSubnet | Immettere **10.1.1.0/27**. |
    | Indirizzo IP pubblico | Selezionare **Crea nuovo**. </br> Immettere **myBastionIP** in **Nome**. |

13. Selezionare la **scheda Rivedi e crea** o il pulsante blu **Rivedi e** crea nella parte inferiore della pagina.

14. Selezionare **Crea**.

## <a name="create-nat-gateway"></a>Creare il gateway NAT 

In questa sezione verrà creato un gateway NAT per la connettività in uscita delle macchine virtuali.

1. Nella casella di ricerca nella parte superiore del portale immettere **GATEWAY NAT**.

2. Selezionare **Gateway NAT nei** risultati della ricerca.

3. Selezionare **[+] Create** ([+] Crea).

4. Nella scheda **Informazioni di base** di Crea gateway NAT **(Network Address Translation)** immettere o selezionare le informazioni seguenti:

    | Impostazione | valore |
    | ------- | ----- |
    | **Dettagli del progetto** |   |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Resource group | Selezionare **TutorLBmultiAVS-rg**. |
    | **Dettagli istanza** |   |
    | Nome del gateway NAT | Immettere **myNATgateway**. |
    | Region | Selezionare **(Stati Uniti) Stati Uniti occidentali 2**. |
    | Zona di disponibilità | Selezionare **Nessuno**. |
    | Timeout di inattività (minuti) | Immettere **15**. |

5. Selezionare la **scheda IP in** uscita o selezionare il pulsante **Avanti: IP in** uscita nella parte inferiore della pagina.

6. Selezionare **Crea un nuovo indirizzo IP pubblico** accanto a Indirizzi IP **pubblici** nella scheda **IP in** uscita.

7. Immettere **myPublicIP-nat** in **Nome**.

8. Selezionare **OK**.

9. Selezionare la **scheda Subnet** o fare clic sul **pulsante Avanti: Subnet** nella parte inferiore della pagina.

10. Selezionare **myVNet** nel menu a discesa in **Rete virtuale**.

11. Selezionare la casella di controllo accanto a **myBackendSubnet**.

12. Selezionare la **scheda Rivedi e crea** oppure selezionare il pulsante blu **Rivedi e** crea nella parte inferiore della pagina.

13. Selezionare **Crea**.

## <a name="create-load-balancer"></a>Creare un servizio di bilanciamento del carico

In questa sezione verrà creato un servizio di bilanciamento del carico per le macchine virtuali.

1. Nella casella di ricerca nella parte superiore del portale immettere **Load balancer**.

2. Selezionare **Servizi di bilanciamento del carico** nei risultati della ricerca.

3. Selezionare **[+] Create** ([+] Crea).

4. Nella scheda **Informazioni di base** di Crea servizio di **bilanciamento del** carico immettere o selezionare le informazioni seguenti:

    | Impostazione | valore |
    | ------- | ----- |
    | **Dettagli del progetto** |   |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Resource group | Selezionare **TutorLBmultiAVS-rg**. |
    | **Dettagli istanza** |   |
    | Nome | Immettere **myLoadBalancer**. |
    | Region | Selezionare **(Stati Uniti) Stati Uniti occidentali 2**. |
    | Type | Lasciare l'impostazione predefinita **Public.** |
    | SKU | Lasciare l'impostazione **predefinita Standard.** |
    | Livello | Lasciare l'impostazione predefinita **Regional**. |
    | **Indirizzo IP pubblico** |   |
    | Indirizzo IP pubblico | Lasciare l'impostazione predefinita **Crea nuovo**. |
    | Nome dell'indirizzo IP pubblico | Immettere **myPublicIP-lb**. |
    | Zona di disponibilità | Selezionare **Con ridondanza della zona**. |
    | Aggiungi un indirizzo IPv6 pubblico | Lasciare l'impostazione predefinita **No**. |
    | Preferenza di routing | Lasciare l'impostazione predefinita **Rete Microsoft**. |

5. Selezionare la **scheda Rivedi e** crea oppure selezionare il pulsante blu **Rivedi** e crea nella parte inferiore della pagina.

6. Selezionare **Crea**.

### <a name="configure-load-balancer-settings"></a>Configurare le impostazioni del servizio di bilanciamento del carico

In questa sezione si creerà un pool back-end per **myLoadBalancer.**

Si creerà un probe di integrità per monitorare **HTTP** e **la porta 80.** Il probe di integrità monitorerà l'integrità delle macchine virtuali nel pool back-end. 

Si creerà una regola di bilanciamento del carico per **la porta 80** con SNAT in uscita disabilitato. Il gateway NAT creato in precedenza gestirà la connettività in uscita delle macchine virtuali.

1. Nella casella di ricerca nella parte superiore del portale immettere Servizio di **bilanciamento del carico.**

2. Selezionare **Servizi di bilanciamento del** carico nei risultati della ricerca.

3. Selezionare **myLoadBalancer**.

4. In **myLoadBalancer** selezionare **Pool back-end** in **Impostazioni**.

5. Selezionare **+ Aggiungi** in Pool **back-end.**

6. In **Aggiungi pool back-end** immettere o selezionare le informazioni seguenti:

    | Impostazione | Valore |
    | ------- | ----- |
    | Nome | Immettere **myBackendPool**. |
    | Rete virtuale | Selezionare **myVNET**. |
    | Configurazione del pool back-end | Lasciare l'impostazione predefinita **nic**. |
    | Versione indirizzo IP | Lasciare il valore predefinito **IPv4**. |

7. Selezionare **Aggiungi**.

8. Selezionare **Probe di integrità**.

9. Selezionare **+ Aggiungi**.

10. In **Aggiungi probe di** integrità immettere o selezionare le informazioni seguenti:

    | Impostazione | Valore |
    | ------- | ----- |
    | Nome | Immettere **myHTTPProbe**. |
    | Protocollo | Selezionare **HTTP**. |
    | Porta | Lasciare il valore predefinito **80**. |
    | Percorso | Lasciare il valore **/** predefinito . |
    | Interval | Lasciare il valore predefinito **di 5** secondi. |
    | Soglia non integra | Lasciare il valore predefinito **di 2** errori consecutivi. |

11. Selezionare **Aggiungi**.

12. Selezionare **Regole di bilanciamento del carico**. 

13. Selezionare **+ Aggiungi**.

14. Immettere o selezionare le informazioni seguenti in **Aggiungi regola di bilanciamento del carico**:

    | Impostazione | Valore |
    | ------- | ----- |
    | Nome | Immettere **myHTTPRule**. |
    | Versione indirizzo IP | Lasciare il valore predefinito **IPv4**. |
    | Indirizzo IP front-end | Selezionare **LoadBalancerFrontEnd.** |
    | Protocollo | Selezionare il valore predefinito **tcp**. |
    | Porta | Immettere **80**. |
    | Porta back-end | Immettere **80**. |
    | Pool back-end | Selezionare **myBackendPool**. |
    | Probe di integrità | Selezionare **myHTTPProbe**. |
    | Persistenza della sessione | Lasciare l'impostazione predefinita **None**. |
    | Timeout di inattività (minuti) | Modificare il dispositivo di scorrimento **su 15.** |
    | Reimpostazione TCP | Selezionare **Enabled**. |
    | IP mobile | Lasciare l'impostazione predefinita **Disabilitato.** |
    | SNAT (Network Address Translation) di origine in uscita | Lasciare l'impostazione **predefinita (scelta consigliata) Usare le regole in uscita per fornire ai membri del pool back-end l'accesso a Internet.** |

5. Selezionare **Aggiungi**.

## <a name="create-virtual-machines"></a>Creare macchine virtuali

In questa sezione verranno creati due gruppi di disponibilità con due macchine virtuali per gruppo. Questi computer verranno aggiunti al pool back-end del servizio di bilanciamento del carico durante la creazione. 

### <a name="create-first-set-of-vms"></a>Creare il primo set di macchine virtuali

1. Selezionare **+ Crea una risorsa** nella sezione superiore sinistra del portale.

2. In **Nuovo** selezionare **Macchina virtuale** di  >  **calcolo.**

3. Nella scheda **Informazioni di** base di Creare una **macchina virtuale** immettere o selezionare le informazioni seguenti:

    | Impostazione | valore |
    | ------- | ----- |
    | **Dettagli del progetto** |   |
    | Subscription | Selezionare la propria sottoscrizione |
    | Gruppo di risorse | Selezionare **TutorLBmultiAVS-rg**. |
    | **Dettagli istanza** |   |
    | Nome macchina virtuale | Immettere **myVM1**. |
    | Region | Selezionare **(Stati Uniti) Stati Uniti occidentali 2**. |
    | Opzioni di disponibilità | Selezionare **Set di disponibilità**. |
    | Set di disponibilità | Selezionare **Crea nuovo**. </br> Immettere **myAvailabilitySet1** in **Nome**. </br> Selezionare **OK**. |
    | Immagine | Selezionare **Windows Server 2019 Datacenter - Gen1.** |
    | Istanza Spot di Azure | Lasciare deselezionata l'impostazione predefinita. |
    | Dimensione | Selezionare una dimensione per la macchina virtuale. |
    | **Account amministratore** |   |
    | Username | Immettere un nome utente. |
    | Password | Immettere una password. |
    | **Regole porta in ingresso** |   |
    | Porte in ingresso pubbliche | Selezionare **Nessuno**. |

4. Selezionare la **scheda** Rete o selezionare il **pulsante Avanti: Dischi**, quindi **Avanti:** Rete nella parte inferiore della pagina.

5. Nella scheda **Rete** immettere o selezionare le informazioni seguenti:

    | Impostazione | Valore |
    | ------- | ----- |
    | **Interfaccia di rete** |   |
    | Rete virtuale | Selezionare **myVNET**. |
    | Subnet | Selezionare **myBackendSubnet**. |
    | IP pubblico | Selezionare **Nessuno**. |
    | Gruppo di sicurezza di rete della scheda di interfaccia di rete | Fare clic su **Advanced** (Avanzate). |
    | Configura gruppo di sicurezza di rete | Selezionare **Crea nuovo**. </br> In **Nome** immettere **myNSG**. </br> Selezionare **+Aggiungi una regola in ingresso** in Regole in **ingresso**. </br> Selezionare **HTTP** per **Servizio**. </br> Immettere **myHTTPrule per** **Nome**. </br> Selezionare **Aggiungi**. </br> Selezionare **OK**. | 
    | **Bilanciamento del carico** |   |
    | Posizionare questa macchina virtuale dietro una soluzione di bilanciamento del carico esistente? | Selezionare la casella di controllo. |
    | **Impostazioni di bilanciamento del carico** |   |
    | Opzioni di bilanciamento del carico | Selezionare **Servizio di bilanciamento del carico di Azure.** |
    | Selezionare un servizio di bilanciamento del carico | Selezionare **myLoadBalancer**. |
    | Selezionare un pool back-end | Selezionare **myBackendPool**. |

6. Selezionare la **scheda Rivedi e crea** oppure selezionare il pulsante blu **Rivedi e** crea nella parte inferiore della pagina.

7. Selezionare **Crea**.

8. Ripetere i passaggi da 1 a 7 per creare la seconda macchina virtuale del set. Sostituire le impostazioni per la macchina virtuale con le informazioni seguenti:

    | Impostazione | Valore |
    | ------- | ----- |
    | Nome | Immettere **myVM2**. |
    | Set di disponibilità | Selezionare **myAvailabilitySet1**. |
    | Rete virtuale | Selezionare **myVNET**. |
    | Subnet | Selezionare **myBackendSubnet**. |
    | IP pubblico | Selezionare **Nessuno**. |
    | Gruppo di sicurezza di rete | Selezionare **myNSG**. |
    | Opzioni di bilanciamento del carico | Selezionare **Servizio di bilanciamento del carico di Azure.** |
    | Selezionare un servizio di bilanciamento del carico | Selezionare **myLoadBalancer**. |
    | Selezionare un pool back-end | Selezionare **myBackendPool**. |

### <a name="create-second-set-of-vms"></a>Creare un secondo set di macchine virtuali

1. Selezionare **+ Crea una risorsa** nella sezione in alto a sinistra del portale.

2. In **Nuovo** selezionare **Macchina virtuale** di  >  **calcolo**.

3. Nella scheda **Informazioni di** base di Crea una **macchina virtuale** immettere o selezionare le informazioni seguenti:

    | Impostazione | valore |
    | ------- | ----- |
    | **Dettagli del progetto** |   |
    | Subscription | Selezionare la propria sottoscrizione |
    | Gruppo di risorse | Selezionare **TutorLBmultiAVS-rg**. |
    | **Dettagli istanza** |   |
    | Nome macchina virtuale | Immettere **myVM3**. |
    | Region | Selezionare **(Stati Uniti) Stati Uniti occidentali 2**. |
    | Opzioni di disponibilità | Selezionare **Set di disponibilità**. |
    | Set di disponibilità | Selezionare **Crea nuovo**. </br> Immettere **myAvailabilitySet2** in **Nome**. </br> Selezionare **OK**. |
    | Immagine | Selezionare **Windows Server 2019 Datacenter - Gen1.** |
    | Istanza Spot di Azure | Lasciare deselezionata l'impostazione predefinita. |
    | Dimensione | Selezionare una dimensione per la macchina virtuale. |
    | **Account amministratore** |   |
    | Username | Immettere un nome utente. |
    | Password | Immettere una password. |
    | **Regole porta in ingresso** |   |
    | Porte in ingresso pubbliche | Selezionare **Nessuno**. |

4. Selezionare la **scheda** Rete o selezionare il **pulsante Avanti: Dischi**, quindi **Avanti:** Rete nella parte inferiore della pagina.

5. Nella scheda **Rete** immettere o selezionare le informazioni seguenti:

    | Impostazione | Valore |
    | ------- | ----- |
    | **Interfaccia di rete** |   |
    | Rete virtuale | Selezionare **myVNET**. |
    | Subnet | Selezionare **myBackendSubnet**. |
    | IP pubblico | Selezionare **Nessuno**. |
    | Gruppo di sicurezza di rete della scheda di interfaccia di rete | Fare clic su **Advanced** (Avanzate). |
    | Configura gruppo di sicurezza di rete | Selezionare **myNSG.** | 
    | **Bilanciamento del carico** |   |
    | Posizionare questa macchina virtuale dietro una soluzione di bilanciamento del carico esistente? | Selezionare la casella di controllo. |
    | **Impostazioni di bilanciamento del carico** |   |
    | Opzioni di bilanciamento del carico | Selezionare **Servizio di bilanciamento del carico di Azure.** |
    | Selezionare un servizio di bilanciamento del carico | Selezionare **myLoadBalancer**. |
    | Selezionare un pool back-end | Selezionare **myBackendPool**. |

6. Selezionare la **scheda Rivedi e** crea oppure selezionare il pulsante blu **Rivedi** e crea nella parte inferiore della pagina.

7. Selezionare **Crea**.

8. Ripetere i passaggi da 1 a sette per creare la seconda macchina virtuale del set. Sostituire le impostazioni per la macchina virtuale con le informazioni seguenti:

    | Impostazione | Valore |
    | ------- | ----- |
    | Nome | Immettere **myVM4**. |
    | Set di disponibilità | Selezionare **myAvailabilitySet2.** |
    | Rete virtuale | Selezionare **myVNET**. |
    | Gruppo di sicurezza di rete | Selezionare **myNSG.** |
    | Opzioni di bilanciamento del carico | Selezionare **Servizio di bilanciamento del carico di Azure.** |
    | Selezionare un servizio di bilanciamento del carico | Selezionare **myLoadBalancer**. |
    | Selezionare un pool back-end | Selezionare **myBackendPool**. |

## <a name="install-iis"></a>Installare IIS

In questa sezione si userà l'host Azure Bastion creato in precedenza per connettersi alle macchine virtuali e installare IIS.

1. Nella casella di ricerca nella parte superiore del portale immettere **Macchina virtuale**.

2. Selezionare **Macchine virtuali** nei risultati della ricerca.

3. Selezionare **myVM1.**

4. Nella pagina **Panoramica** di myVM1 selezionare Connect   >  **Bastion (Connetti Bastion).**

5. Selezionare **Usa Bastion**.

6. Immettere il **nome utente** e **la password** creati durante la creazione della macchina virtuale.

7. Selezionare **Connetti**.

7. Nel desktop del server passare a **Strumenti di amministrazione Windows** > **Windows PowerShell**.

8. Nella finestra di PowerShell eseguire i comandi seguenti per:

    * Installare il server IIS
    * Rimuovere il file iisstart.htm predefinito
    * Aggiungere un nuovo file iisstart.htm che visualizzi il nome della macchina virtuale:

   ```powershell
    # Install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
    Remove-Item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
8. Chiudere la sessione di Bastion con **myVM1**.

9. Ripetere i passaggi da 1 a 8 **per myVM2**, **myVM3** e **myVM4**.

## <a name="test-the-load-balancer"></a>Testare il bilanciamento del carico

In questa sezione si scoprirà l'indirizzo IP pubblico del servizio di bilanciamento del carico. Si userà l'indirizzo IP per testare il funzionamento del servizio di bilanciamento del carico.

1. Nella casella di ricerca nella parte superiore del portale immettere **IP pubblico**.

2. Selezionare **Indirizzi IP pubblici** nei risultati della ricerca.

3. Selezionare **myPublicIP-lb**.

4. Prendere nota dell'indirizzo IP pubblico **elencato** in Indirizzo IP nella pagina **Panoramica** di **myPublicIP-lb**:

    :::image type="content" source="./media/tutorial-multi-availability-sets-portal/find-public-ip.png" alt-text="Trovare l'indirizzo IP pubblico del servizio di bilanciamento del carico." border="true":::

5. Aprire un Web browser e immettere l'indirizzo IP pubblico nella barra degli indirizzi:

    :::image type="content" source="./media/tutorial-multi-availability-sets-portal/verify-load-balancer.png" alt-text="Testare il servizio di bilanciamento del carico con Web browser." border="true":::

6. Selezionare Aggiorna nel browser per visualizzare il traffico bilanciato con le altre macchine virtuali nel pool back-end.

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si continuerà a usare questa applicazione, eliminare il servizio di bilanciamento del carico e le risorse di supporto con la procedura seguente:

1. Nella casella di ricerca nella parte superiore del portale immettere **Gruppo di risorse**.

2. Selezionare **Gruppi di risorse** nei risultati della ricerca.

3. Selezionare **TutorLBmultiAVS-rg**.

4. Nella pagina di panoramica di **TutorLBmultiAVS-rg** selezionare **Elimina gruppo di risorse**.

5. Immettere **TutorLBmultiAVS-rg** in **DIGITARE IL NOME DEL GRUPPO DI RISORSE**.

6. Selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione:

* È stata creata una rete virtuale e Azure Bastion host.
* Creazione di un'istanza di Azure Load Balancer Standard.
* Sono stati creati due set di disponibilità con due macchine virtuali per ogni set.
* Iis installato e testato il servizio di bilanciamento del carico.

Passare all'articolo successivo per informazioni su come creare un'istanza tra aree Azure Load Balancer:
> [!div class="nextstepaction"]
> [Creare un servizio di bilanciamento del carico tra aree](tutorial-cross-region-portal.md)

