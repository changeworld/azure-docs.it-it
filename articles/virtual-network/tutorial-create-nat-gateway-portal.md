---
title: 'Esercitazione: Creare un gateway NAT - Portale di Azure'
titlesuffix: Azure Virtual Network NAT
description: Questa esercitazione illustra come creare e convalidare un gateway NAT usando il portale di Azure.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial
ms.openlocfilehash: 82b5892b027627871e5492e3c6cd3776a923632b
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102553443"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-portal"></a>Esercitazione: Creare un gateway NAT usando il portale di Azure

Questa esercitazione illustra come usare il servizio NAT di rete virtuale di Azure. Verrà creato un gateway NAT per fornire connettività in uscita per una macchina virtuale in Azure. 

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare una rete virtuale.
> * Creare una macchina virtuale.
> * Creare un gateway NAT e associarlo alla rete virtuale.
> * Connettersi alla macchina virtuale e verificare l'indirizzo IP NAT.

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="virtual-network"></a>Rete virtuale

Prima di distribuire una macchina virtuale e di poter usare il gateway NAT, è necessario creare il gruppo di risorse e la rete virtuale.

1. Accedere al [portale di Azure](https://portal.azure.com).

2. In alto a sinistra nella schermata selezionare **Crea una risorsa > Rete > Rete virtuale** o cercare **Rete virtuale** nella casella di ricerca.

3. Selezionare **Crea**.

4. In **Crea rete virtuale** immettere o selezionare queste informazioni nella scheda **Generale**:

    | **Impostazione**          | **Valore**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Dettagli del progetto**  |                                                                 |
    | Subscription     | Selezionare la sottoscrizione ad Azure                                  |
    | Gruppo di risorse   | Selezionare **Crea nuovo**. </br> Immettere **myResourceGroupNAT**. </br> Selezionare **OK**. |
    | **Dettagli istanza** |                                                                 |
    | Nome             | Immettere **myVNet**                                    |
    | Region           | Select **(Europa) Europa occidentale** |

5. Selezionare la scheda **Indirizzi IP** oppure il pulsante **Avanti: Indirizzi IP** nella parte inferiore della pagina.

6. Nella scheda **Indirizzi IP** immettere queste informazioni:

    | Impostazione            | valore                      |
    |--------------------|----------------------------|
    | Spazio indirizzi IPv4 | Immettere **10.1.0.0/16** |

7. Selezionare **+ Aggiungi subnet**. 

8. In **Modifica subnet** immettere queste informazioni:

    | Impostazione            | valore                      |
    |--------------------|----------------------------|
    | Nome della subnet | Immettere **mySubnet** |
    | Intervallo di indirizzi subnet | Immettere **10.1.0.0/24** |

9. Selezionare **Aggiungi**.

10. Selezionare la scheda **Sicurezza**.

11. In **BastionHost** selezionare **Abilita**. Immettere le informazioni seguenti:

    | Impostazione            | valore                      |
    |--------------------|----------------------------|
    | Nome bastion | Immettere **myBastionHost** |
    | Spazio indirizzi della subnet AzureBastionSubnet | Immettere **10.1.1.0/24** |
    | Indirizzo IP pubblico | Selezionare **Crea nuovo**. </br> Per **Nome** immettere **myBastionIP**. </br> Selezionare **OK**. |

12. Selezionare la scheda **Rivedi e crea** oppure il pulsante **Rivedi e crea**.

13. Selezionare **Crea**.

## <a name="nat-gateway"></a>Gateway NAT

È possibile usare una o più risorse indirizzo IP pubblico, prefissi IP pubblici o entrambi. Verranno aggiunte una risorsa IP pubblico e una risorsa gateway NAT.

1. Sul lato superiore sinistro della schermata selezionare **Crea una risorsa > rete > gateway NAT** o Cerca **gateway NAT** nella casella di ricerca.

2. Selezionare **Crea**. 

3. In **crea Network Address Translation (NAT) gateway** immettere o selezionare queste informazioni nella scheda **nozioni di base** :

    | **Impostazione**          | **Valore**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Dettagli del progetto**  |                                                                 |
    | Subscription     | Selezionare la sottoscrizione di Azure.                                  |
    | Gruppo di risorse   | Selezionare **myResourceGroupNAT**. |
    | **Dettagli istanza** |                                                                 |
    | Nome             | Immettere **myNATgateway**                                    |
    | Region           | Select **(Europa) Europa occidentale**  |
    | Zona di disponibilità | Selezionare **Nessuno**. |
    | Timeout di inattività (minuti) | Immettere **10**. |

4. Selezionare la scheda **IP in uscita** oppure fare clic sul pulsante **Avanti: IP in uscita** nella parte inferiore della pagina.

5. Nella scheda **IP in uscita** immettere o selezionare le informazioni seguenti:

    | **Impostazione** | **Valore** |
    | ----------- | --------- |
    | Indirizzi IP pubblici | Selezionare **Crea un nuovo indirizzo IP pubblico**. </br> In **nome** immettere **myPublicIP**. </br> Selezionare **OK**. |

6. Selezionare la scheda **subnet** oppure fare clic sul pulsante **Avanti: Subnet** nella parte inferiore della pagina.

7. Nella scheda **subnet** selezionare **myVNet** nel pannello a discesa **rete virtuale** .

8. Selezionare la casella accanto a **subnet**.

9. Selezionare la scheda **Verifica + crea** oppure selezionare il pulsante **Verifica blu + crea** nella parte inferiore della pagina.

10. Selezionare **Crea**.
    
## <a name="virtual-machine"></a>Macchina virtuale

In questa sezione si creerà una macchina virtuale per testare il gateway NAT e si verificherà l'indirizzo IP pubblico della connessione in uscita.

1. Nella parte superiore sinistra del portale selezionare **Crea una risorsa** > **Calcolo** > **Macchina virtuale**. 

2. Nella scheda **nozioni di base** della pagina **creare una macchina virtuale** immettere o selezionare le informazioni seguenti:

    | **Impostazione** | **Valore** |
    | ----------- | --------- |
    | **Dettagli del progetto** |   |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Resource group | selezionare **myResourceGroupNAT**. |
    | **Dettagli istanza** |   |
    | Nome macchina virtuale | Immettere **myVM**. |
    | Region | Select **(Europe) West Europe**. |
    | Opzioni di disponibilità | Lasciare invariata l'impostazione predefinita nessuna ridondanza. |
    | Immagine | Selezionare **Windows Server 2019 datacenter-Gen1**. |
    | Dimensione | Selezionare **Standard_DS1_v2**. |
    | **Account amministratore** |   |
    | Username | Immettere un nome utente per la macchina virtuale. |
    | Password | Immettere una password. |
    | Conferma password | Conferma password. |
    | **Regole porta in ingresso** |    |
    | Porte in ingresso pubbliche | Selezionare **Nessuno**. |

3. Selezionare la scheda **dischi** oppure fare clic sul pulsante **Avanti: dischi** nella parte inferiore della pagina.

4. Lasciare l'impostazione predefinita nella scheda **dischi** .

5. Selezionare la scheda **rete** oppure fare clic sul pulsante **Avanti: rete** nella parte inferiore della pagina.

6. Nella scheda **Rete** immettere o selezionare le informazioni seguenti:

    | **Impostazione** | **Valore** |
    | ----------- | --------- |
    | **Interfaccia di rete** |   |
    | Rete virtuale | Selezionare **myVNET**. |
    | Subnet | Selezionare **mySubnet**. |
    | IP pubblico | Selezionare **Nessuno**. |
    | Gruppo di sicurezza di rete della scheda di interfaccia di rete | Selezionare **Basic**. |
    | Porte in ingresso pubbliche | Selezionare **Nessuno**. |

7. Selezionare la scheda **Verifica + crea** oppure selezionare il pulsante **Verifica blu + crea** nella parte inferiore della pagina.

8. Selezionare **Crea**.

## <a name="test-nat-gateway"></a>Testare il gateway NAT

In questa sezione verrà testato il gateway NAT. Si scoprirà prima l'indirizzo IP pubblico del gateway NAT. Ci si connette quindi alla macchina virtuale di test e si verifica la connessione in uscita tramite il gateway NAT.
    
1. Trovare l'indirizzo IP pubblico per il gateway NAT nella schermata **Panoramica** . Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine selezionare **myPublicIP**.

2. Prendere nota dell'indirizzo IP pubblico:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/find-public-ip.png" alt-text="Individuare l'indirizzo IP pubblico del gateway NAT" border="true":::

3. Selezionare **tutti i servizi** nel menu a sinistra, selezionare **tutte le risorse** e quindi nell'elenco delle risorse selezionare **myVM** che si trova nel gruppo di risorse **myResourceGroupNAT** .

4. Nella pagina **Panoramica** selezionare **Connetti**, quindi **Bastion**.

5. Selezionare il pulsante blu **Usa Bastion**.

6. Immettere il nome utente e la password specificati durante la creazione della VM.

7. Aprire **Internet Explorer** in **myTestVM**.

8. Immettere **https://whatsmyip.com** nella barra degli indirizzi.

9. Verificare che l'indirizzo IP visualizzato corrisponda all'indirizzo del gateway NAT annotato nel passaggio precedente:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/my-ip.png" alt-text="Internet Explorer che mostra l'indirizzo IP in uscita esterno" border="true":::

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare questa applicazione, eliminare la rete virtuale, la macchina virtuale e il gateway NAT con i passaggi seguenti:

1. Nel menu a sinistra selezionare **Gruppi di risorse**.

2. Selezionare il gruppo di risorse **myResourceGroupNAT** .

3. Selezionare **Elimina gruppo di risorse**.

4. Immettere **myResourceGroupNAT** e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla rete virtuale di Azure NAT, vedere:
> [!div class="nextstepaction"]
> [Panoramica NAT della rete virtuale](nat-overview.md)
