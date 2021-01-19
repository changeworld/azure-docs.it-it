---
title: 'Avvio rapido: Creare un servizio Collegamento privato tramite il portale di Azure'
titlesuffix: Azure Private Link
description: Questa guida di avvio rapido illustra come creare un servizio Collegamento privato con il portale di Azure
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 01/18/2021
ms.author: allensu
ms.openlocfilehash: 3e9ade329d2b26d36763db579b0fcec03e938aad
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2021
ms.locfileid: "98555458"
---
# <a name="quickstart-create-a-private-link-service-by-using-the-azure-portal"></a>Guida introduttiva: Creare un servizio Collegamento privato usando il portale di Azure

Introduzione alla creazione di un servizio Collegamento privato che fa riferimento al proprio servizio.  Assegnare l'accesso con collegamento privato al servizio o alla risorsa distribuita dietro un'istanza di Azure Load Balancer Standard.  Gli utenti del servizio hanno accesso privato dalla loro rete virtuale.

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="create-an-internal-load-balancer"></a>Creare un bilanciamento del carico interno

In questa sezione si crea una rete virtuale e un'istanza interna di Azure Load Balancer.

### <a name="virtual-network"></a>Rete virtuale

In questa sezione si creano la rete virtuale e la subnet che ospiteranno l'istanza di Load Balancer usata per accedere al servizio Collegamento privato.

1. In alto a sinistra nella schermata selezionare **Crea una risorsa > Rete > Rete virtuale** o cercare **Rete virtuale** nella casella di ricerca.

2. In **Crea rete virtuale** immettere o selezionare queste informazioni nella scheda **Generale**:

    | **Impostazione**          | **Valore**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Dettagli del progetto**  |                                                                 |
    | Subscription     | Selezionare la sottoscrizione ad Azure                                  |
    | Gruppo di risorse   | Selezionare **CreatePrivLinkService-rg** |
    | **Dettagli istanza** |                                                                 |
    | Nome             | Immettere **myVNet**                                    |
    | Region           | Selezionare **Stati Uniti orientali 2** |

3. Selezionare la scheda **Indirizzi IP** oppure il pulsante **Avanti: Indirizzi IP** nella parte inferiore della pagina.

4. Nella scheda **Indirizzi IP** immettere queste informazioni:

    | Impostazione            | valore                      |
    |--------------------|----------------------------|
    | Spazio indirizzi IPv4 | Immettere **10.1.0.0/16** |

5. In **Nome subnet** selezionare la parola **predefinito**.

6. In **Modifica subnet** immettere queste informazioni:

    | Impostazione            | valore                      |
    |--------------------|----------------------------|
    | Nome della subnet | Immettere **mySubnet** |
    | Intervallo di indirizzi subnet | Immettere **10.1.0.0/24** |

7. Selezionare **Salva**.

8. Selezionare la scheda **Rivedi e crea** oppure il pulsante **Rivedi e crea**.

9. Selezionare **Crea**.

### <a name="create-a-standard-load-balancer"></a>Creare un'istanza di Load Balancer Standard

Usare il portale per creare un servizio Load Balancer Standard interno. 

1. In alto a sinistra nella schermata selezionare **Crea una risorsa** > **Rete** > **Load Balancer**.

2. Nella scheda **Informazioni di base** della pagina **Crea servizio di bilanciamento del carico** immettere o selezionare le informazioni seguenti: 

    | Impostazione                 | Valore                                              |
    | ---                     | ---                                                |
    | Subscription               | Selezionare la propria sottoscrizione.    |    
    | Resource group         | Selezionare il gruppo **CreatePrivLinkService-rg** creato nel passaggio precedente.|
    | Nome                   | Immettere **myLoadBalancer**                                   |
    | Region         | Selezionare **Stati Uniti orientali 2**.                                        |
    | Type          | Selezionare **Interno**.                                        |
    | SKU           | selezionare **Standard** |
    | Rete virtuale | Selezionare la rete **myVNet** creata nel passaggio precedente. |
    | Subnet  | Selezionare la subnet **mySubnet** creata nel passaggio precedente. |
    | Assegnazione indirizzi IP | Selezionare **Dinamico**. |
    | Zona di disponibilità | Selezionare **Con ridondanza della zona**. |

3. Accettare i valori predefiniti per le impostazioni rimanenti e quindi selezionare **Rivedi e crea**.

4. Nella scheda **Rivedi e crea** selezionare **Crea**.   

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
    | | |

3. Accettare tutte le impostazioni predefinite e selezionare **OK**.

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

4. Accettare tutte le impostazioni predefinite e quindi selezionare **OK**.

## <a name="create-a-private-link-service"></a>Creare un servizio Collegamento privato

In questa sezione verrà creato un servizio Collegamento privato dietro un'istanza di Load Balancer Standard.

1. Nell'angolo in alto a sinistra della pagina nel portale di Azure selezionare **Crea una risorsa**.

2. Cercare **Collegamento privato** nella casella **Cerca nel Marketplace**.

3. Selezionare **Crea**.

4. In **Panoramica**, nella pagina **Centro collegamento privato**, selezionare il pulsante blu **Crea servizio Collegamento privato**.

5. Nella scheda **Informazioni di base** della pagina **Crea servizio Collegamento privato** immettere o selezionare le informazioni seguenti:

    | Impostazione | valore |
    | ------- | ----- |
    | **Dettagli del progetto** |  |
    | Subscription | Selezionare la propria sottoscrizione. |
    | Gruppo di risorse | Selezionare **CreatePrivLinkService-rg**. |
    | **Dettagli istanza** |  |
    | Nome | Immettere **myPrivateLinkService**. |
    | Region | Selezionare **Stati Uniti orientali 2**. |

6. Selezionare la scheda **Impostazioni traffico in uscita** oppure **Avanti: Impostazioni traffico in uscita** nella parte inferiore della pagina.

7. Nella scheda **Informazioni traffico in uscita** immettere o selezionare le informazioni seguenti:

    | Impostazione | Valore |
    | ------- | ----- |
    | Bilanciamento del carico | Selezionare **myLoadBalancer**. |
    | Indirizzo IP front-end del servizio di bilanciamento del carico | Selezionare **LoadBalancerFrontEnd (10.1.0.4)** . |
    | Subnet NAT di origine | Selezionare **mySubnet (10.1.0.0/24)** . |
    | Abilita proxy TCP V2 | Lasciare l'impostazione predefinita **No**. </br> Se nell'applicazione è prevista l'intestazione proxy TCP v2, selezionare **Sì**. |
    | **Impostazioni dell'indirizzo IP privato** |  |
    | Lasciare le impostazioni predefinite |  |

8. Selezionare la scheda **Sicurezza di accesso** oppure **Avanti: Sicurezza di accesso** nella parte inferiore della pagina.

9. Nella scheda **Sicurezza di accesso** lasciare l'impostazione predefinita **Solo controllo degli accessi in base al ruolo**.

10. Selezionare la scheda **Tag** oppure **Avanti: Tag** nella parte inferiore della pagina.

11. Selezionare la scheda **Rivedi e crea** oppure **Avanti: Rivedi e crea** nella parte inferiore della pagina.

12. Nella scheda **Rivedi e crea** selezionare **Crea**.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non si ha più bisogno del servizio Collegamento privato, eliminare il gruppo di risorse per pulire le risorse usate in questo argomento di avvio rapido.

1. Immettere **CreatePrivLinkService-rg** nella casella di ricerca nella parte superiore del portale e selezionare **CreatePrivLinkService-rg** nei risultati della ricerca.
1. Selezionare **Elimina gruppo di risorse**.
1. Immettere **CreatePrivLinkService-rg** in **DIGITARE IL NOME DEL GRUPPO DI RISORSE**.
1. Selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido:

* Sono state create una rete virtuale e un'istanza interna di Azure Load Balancer.
* È stato creato un servizio Collegamento privato

Per altre informazioni sull'endpoint privato di Azure, passare a:
> [!div class="nextstepaction"]
> [Avvio rapido: Creare un endpoint privato con il portale di Azure](create-private-endpoint-portal.md)