---
title: 'Esercitazione: Creare un servizio di bilanciamento del carico tra più aree usando il portale di Azure'
titleSuffix: Azure Load Balancer
description: Questa esercitazione illustra come distribuire un servizio di Azure Load Balancer tra più aree con il portale di Azure.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 02/24/2021
ms.openlocfilehash: 9107ef6100a3c362eae982412d54a981851bcb42
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102561399"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-the-azure-portal"></a>Esercitazione: Creare un servizio di Azure Load Balancer tra più aree usando il portale di Azure

Un servizio di bilanciamento del carico tra più aree garantisce che un servizio sia disponibile a livello globale in più aree di Azure. Se si verifica un errore in un'area, il traffico viene indirizzato al servizio di bilanciamento del carico a livello di area integro più vicino.  

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare un servizio di bilanciamento del carico tra aree.
> * Creare un pool back-end contenente due servizi di bilanciamento del carico a livello di area.
> * Creare una regola del servizio di bilanciamento del carico.
> * Testare il servizio di bilanciamento del carico.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

> [!IMPORTANT]
> Azure Load Balancer tra aree è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure.
- Due istanze **standard** di SKU Azure Load Balancer con pool back-end distribuiti tra due diverse aree di Azure.
    - Per informazioni sulla creazione di un servizio di bilanciamento del carico standard a livello di area e di macchine virtuali per i pool back-end, vedere [Guida introduttiva: Creare un servizio di bilanciamento del carico pubblico per bilanciare il carico delle macchine virtuali con il portale di Azure](quickstart-load-balancer-standard-public-portal.md).
        - Aggiungere il nome dei servizi di bilanciamento del carico, delle macchine virtuali e di altre risorse in ogni area con **-R1** e **-R2**. 

## <a name="sign-in-to-azure-portal"></a>Accedere al portale di Azure

[Accedere](https://preview.portal.azure.com) al portale di anteprima di Azure.

## <a name="create-cross-region-load-balancer"></a>Creare un servizio di bilanciamento del carico tra aree

In questa sezione verranno creati un servizio di bilanciamento del carico tra più aree e un indirizzo IP pubblico.

1. Selezionare **Crea una risorsa**. 
2. Nella casella di ricerca immettere **Load Balancer**. Selezionare **Load Balancer** nei risultati della ricerca.
3. Nella pagina **bilanciamento del carico** selezionare **Crea**.
4. Nella scheda **Informazioni di base** della pagina **Crea servizio di bilanciamento del carico** immettere o selezionare le informazioni seguenti: 

    | Impostazione                 | Valore                                              |
    | ---                     | ---                                                |
    | Subscription               | Selezionare la propria sottoscrizione.    |    
    | Resource group         | Selezionare **Crea nuovo** e immettere **CreateCRLBTutorial-rg** nella casella di testo.|
    | Nome                   | Immettere **myLoadBalancer-CR**                                   |
    | Region         | Select **(Stati Uniti) Stati Uniti occidentali**.                                        |
    | Type          | Selezionare **Pubblica**.                                        |
    | SKU           | Lasciare il valore predefinito **standard**. |
    | Livello           | Selezionare **Globale** |
    | Indirizzo IP pubblico | Selezionare **Crea nuovo**.|
    | Nome dell'indirizzo IP pubblico | Digitare **myPublicIP-CR** nella casella di testo.|
    | Preferenza di routing| Selezionare **rete Microsoft**. </br> Per ulteriori informazioni sulle preferenze di routing, vedere informazioni sulle preferenze di [routing (anteprima)](../virtual-network/routing-preference-overview.md). |

    > [!NOTE]
    > Il servizio di bilanciamento del carico tra più aree può essere distribuito solo nelle aree seguenti: **Stati Uniti orientali 2, Stati Uniti occidentali, Europa occidentale, Asia sud-orientale, Stati Uniti centrali, Europa settentrionale, Asia orientale**. Per altre informazioni, vedere **https://aka.ms/homeregionforglb**.


3. Accettare i valori predefiniti per le impostazioni rimanenti e quindi selezionare **Rivedi e crea**.

4. Nella scheda **Rivedi e crea** selezionare **Crea**.   

    :::image type="content" source="./media/tutorial-cross-region-portal/create-cross-region.png" alt-text="Creare un servizio di bilanciamento del carico tra aree" border="true":::

## <a name="create-backend-pool"></a>Creare un pool back-end

In questa sezione verranno aggiunti due servizi di bilanciamento del carico standard a livello di area al pool back-end del servizio di bilanciamento del carico tra più aree.

> [!IMPORTANT]
> Per completare questi passaggi, assicurarsi che nella sottoscrizione siano stati distribuiti due servizi di bilanciamento del carico a livello di area con pool back-end.  Per altre informazioni, vedere **[Avvio rapido: Creare un servizio di bilanciamento del carico pubblico per bilanciare il carico delle macchine virtuali con il portale di Azure](quickstart-load-balancer-standard-public-portal.md)** .

Creare il pool di indirizzi back-end **myBackendPool-CR** per includere i servizi di bilanciamento del carico a livello di area.

1. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine selezionare **myLoadBalancer-CR** nell'elenco di risorse.

2. In **Impostazioni** selezionare **Pool back-end** e quindi selezionare **Aggiungi**.

3. Nella pagina **Aggiungere un pool back-end** digitare **myBackendPool-CR** come nome.

4. Selezionare **Aggiungi**.

4. Selezionare **myBackendPool-CR**.

5. In **Servizi di bilanciamento del carico** selezionare il menu a discesa sotto **Bilanciamento del carico**.

5. Selezionare **myLoadBalancer-R1** o il nome del servizio di bilanciamento del carico nell'area 1.

6. Selezionare il menu a discesa sotto **Configurazione dell'indirizzo IP front-end**. Scegliere **LoadBalancerFrontEnd**.

7. Ripetere i passaggi da 4 a 6 per aggiungere **myLoadBalancer-R2**.

8. Selezionare **Aggiungi**.

    :::image type="content" source="./media/tutorial-cross-region-portal/add-to-backendpool.png" alt-text="Aggiungere i servizi di bilanciamento del carico a livello di area al pool back-end" border="true":::

## <a name="create-a-health-probe"></a>Creare un probe di integrità

In questa sezione verrà creato un probe di integrità per creare la regola di bilanciamento del carico:

* Denominato **myHealthProbe**.
* Protocollo **TCP**.
* Intervallo di **5** secondi.
* Soglia di non integrità di **due** errori.

1. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine selezionare **myLoadBalancer-CR** nell'elenco di risorse.

2. In **Impostazioni** selezionare **Probe di integrità**.

3. Usare questi valori per configurare il probe di integrità:

    | Impostazione | Valore |
    | ------- | ----- |
    | Nome | Immettere **myHealthProbe**. |
    | Protocollo | selezionare **TCP**. |
    | Porta | Immettere **80**. |
    | Interval | Immettere **5**. |
    | Soglia non integra | Immettere **2**. |

4. Selezionare **OK**.

    > [!NOTE]
    > Il servizio di bilanciamento del carico tra più aree ha un probe di integrità incorporato. Questo probe è un segnaposto per il funzionamento della creazione della regola di bilanciamento del carico.  Per altre informazioni, vedere **[Limitazioni del servizio di bilanciamento del carico tra più aree](cross-region-overview.md#limitations)** .

## <a name="create-a-load-balancer-rule"></a>Creare una regola di bilanciamento del carico

In questa sezione verrà creata una regola di bilanciamento del carico:

* Denominata **myHTTPRule**.
* Nel front-end denominato **LoadBalancerFrontEnd**.
* In ascolto sulla **porta 80**.
* Che indirizza il traffico con carico bilanciato al back-end denominato **myBackendPool-CR** sulla **porta 80**.

    > [!NOTE]
    > La porta front-end deve corrispondere alla porta back-end e alla porta front-end dei servizi di bilanciamento del carico a livello di area nel pool back-end.

1. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine selezionare **myLoadBalancer-CR** nell'elenco di risorse.

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
    | Timeout di inattività (minuti) | Spostare il dispositivo di scorrimento su **15**. |
    | Reimpostazione TCP | Selezionare **Enabled**. |

4. Accettare tutte le impostazioni predefinite e quindi selezionare **OK**.

    :::image type="content" source="./media/tutorial-cross-region-portal/create-lb-rule.png" alt-text="Creare le regole del bilanciamento del carico" border="true":::

## <a name="test-the-load-balancer"></a>Testare il servizio di bilanciamento del carico

In questa sezione verrà testato il servizio di bilanciamento del carico tra più aree. Ci si connetterà all'indirizzo IP pubblico in un Web browser.  Si arresteranno le macchine virtuali in uno dei pool back-end del servizio di bilanciamento del carico a livello di area e si osserverà il failover.

1. Individuare l'indirizzo IP pubblico del servizio di bilanciamento del carico nella schermata **Panoramica**. Selezionare **Tutti i servizi** nel menu a sinistra, quindi **Tutte le risorse** e infine selezionare **myPublicIP-CR**.

2. Copiare l'indirizzo IP pubblico e quindi incollarlo nella barra degli indirizzi del browser. Nel browser verrà visualizzata la pagina predefinita del server Web IIS.

    :::image type="content" source="./media/tutorial-cross-region-portal/test-cr-lb-1.png" alt-text="Testare il bilanciamento del carico" border="true":::

3. Arrestare le macchine virtuali nel pool di back-end di uno dei servizi di bilanciamento del carico a livello di area.

4. Aggiornare il Web browser e osservare il failover della connessione all'altro servizio di bilanciamento del carico a livello di area.

    :::image type="content" source="./media/tutorial-cross-region-portal/test-cr-lb-2.png" alt-text="Testare il servizio di bilanciamento del carico dopo il failover" border="true":::

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare il gruppo di risorse, il servizio di bilanciamento del carico e tutte le risorse correlate. 

A questo scopo, selezionare il gruppo di risorse **CreateCRLBTutorial-rg** che contiene le risorse e quindi selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione:

* È stato creato un servizio di bilanciamento del carico tra aree.
* Sono stati aggiunti i servizi di bilanciamento del carico a livello di area al pool back-end del servizio di bilanciamento del carico tra più aree.
* È stata creata una regola di bilanciamento del carico.
* È stato testato il servizio di bilanciamento del carico.

Per ulteriori informazioni sul bilanciamento del carico tra aree, vedere:
> [!div class="nextstepaction"]
> [Bilanciamento del carico tra aree (anteprima)](cross-region-overview.md)
