---
title: Distribuire il cloud Spring di Azure in una rete virtuale
description: Distribuire Azure Spring Cloud in una rete virtuale (VNet injection).
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 07/21/2020
ms.custom: devx-track-java
ms.openlocfilehash: 82dcd8c59c55a2866b51fd6dee896ea1298b6cf6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031804"
---
# <a name="deploy-azure-spring-cloud-in-a-virtual-network"></a>Distribuire il cloud Spring di Azure in una rete virtuale

**Questo articolo si applica a:** ✔️ Java ✔️ C#

Questa esercitazione illustra come distribuire un'istanza di Azure Spring Cloud nella rete virtuale, una funzionalità nota anche come VNet injection.

La distribuzione rende possibili gli scenari seguenti:

* Isolamento delle app di Azure Spring Cloud e del runtime del servizio da Internet nella rete aziendale.
* Interazione di Azure Spring Cloud con i sistemi nei data center locali o con i servizi di Azure in altre reti virtuali.
* Possibilità per i clienti di controllare le comunicazioni di rete in ingresso e in uscita per Azure Spring Cloud.

> [!Note]
> È possibile selezionare la rete virtuale di Azure solo quando si crea una nuova istanza del servizio cloud di Azure Spring. Non è possibile modificare l'uso di un'altra rete virtuale dopo la creazione di Azure Spring cloud.

## <a name="prerequisites"></a>Prerequisiti

Registrare i provider di risorse **Microsoft.AppPlatform** e **Microsoft.ContainerService** di Azure Spring Cloud in base alle istruzioni riportate in [Registrare il provider di risorse nel portale di Azure](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) oppure eseguendo il comando seguente dell'interfaccia della riga di comando di Azure:

```azurecli
az provider register --namespace Microsoft.AppPlatform
az provider register --namespace Microsoft.ContainerService
```

## <a name="virtual-network-requirements"></a>Requisiti della rete virtuale

La rete virtuale in cui viene distribuita l'istanza di Azure Spring Cloud deve soddisfare i requisiti seguenti:

* **Località**: la rete virtuale deve risiedere nella stessa località dell'istanza di Azure Spring Cloud.
* **Sottoscrizione** la rete virtuale deve essere inclusa nella stessa sottoscrizione dell'istanza di Azure Spring Cloud.
* **Subnet**: la rete virtuale deve includere due subnet dedicate a un'istanza di Azure Spring Cloud:

    * Una per il runtime del servizio.
    * Una per le applicazioni di microservizi Spring Boot.
    * Deve esistere una relazione uno-a-uno tra queste subnet e un'istanza di Azure Spring Cloud. Usare una nuova subnet per ogni istanza del servizio da distribuire. Ogni subnet può includere solo una singola istanza del servizio.
* **Spazio degli indirizzi**: blocchi CIDR fino a */28* per la subnet del runtime del servizio e per la subnet delle applicazioni di microservizi Spring Boot.
* **Tabella di route**: per impostazione predefinita, le subnet non necessitano delle tabelle di route esistenti associate. È possibile [importare una tabella di route personalizzata](#bring-your-own-route-table).

Le procedure seguenti descrivono la configurazione della rete virtuale in cui contenere l'istanza di Azure Spring Cloud.

## <a name="create-a-virtual-network"></a>Creare una rete virtuale

Se è già disponibile una rete virtuale in cui ospitare un'istanza di Azure Spring Cloud, ignorare i passaggi 1, 2 e 3. È possibile iniziare dal passaggio 4 per preparare le subnet per la rete virtuale.

1. Nel menu del portale di Azure selezionare **Crea una risorsa**. In Azure Marketplace selezionare **Rete** > **Rete virtuale**.

1. Nella finestra di dialogo **Crea rete virtuale** immettere o selezionare le informazioni seguenti:

    |Impostazione          |Valore                                             |
    |-----------------|--------------------------------------------------|
    |Subscription     |Selezionare la propria sottoscrizione.                         |
    |Resource group   |Selezionare il gruppo di risorse o crearne uno nuovo.  |
    |Name             |Immettere **azure-spring-cloud-vnet**.                 |
    |Location         |Selezionare **Stati Uniti orientali**.                               |

1. Selezionare **Avanti: Indirizzi IP**.

1. Per lo spazio indirizzi IPv4, immettere **10.1.0.0/16**.

1. Selezionare **Aggiungi subnet**. Quindi immettere **service-runtime-subnet** per **Nome subnet** e **10.1.0.0/28** per **Intervallo di indirizzi della subnet**. Quindi selezionare **Aggiungi**.

1. Selezionare di nuovo **Aggiungi subnet** e quindi compilare i campi **Nome subnet** e **Intervallo di indirizzi subnet**. Ad esempio, immettere **apps-subnet** e **10.1.1.0/28**. Quindi selezionare **Aggiungi**.

1. Selezionare **Rivedi e crea**. Lasciare le altre impostazioni sui valori predefiniti e selezionare **Crea**.

## <a name="grant-service-permission-to-the-virtual-network"></a>Concedere al servizio l'autorizzazione per la rete virtuale
Azure Spring cloud richiede l'autorizzazione **owner** per la rete virtuale, in modo da concedere un'entità servizio dedicata e dinamica nella rete virtuale per un'ulteriore distribuzione e manutenzione.

Selezionare la rete virtuale **azure-spring-cloud-vnet** creata in precedenza.

1. Selezionare **Controllo di accesso (IAM)** e quindi **Aggiungi** > **Aggiungi assegnazione di ruolo**.

    ![Screenshot che mostra la schermata Controllo di accesso.](./media/spring-cloud-v-net-injection/access-control.png)

1. Nella finestra di dialogo **Aggiungi assegnazione di ruolo** immettere o selezionare le informazioni seguenti:

    |Impostazione  |valore                                             |
    |---------|--------------------------------------------------|
    |Ruolo     |Selezionare **Proprietario**.                                 |
    |Select   |Immettere **Provider di risorse Azure Spring Cloud**.   |

    Selezionare quindi **Provider di risorse Azure Spring Cloud** e poi **Salva**.

    ![Screenshot che mostra la sezione di Provider di risorse Azure Spring Cloud.](./media/spring-cloud-v-net-injection/grant-azure-spring-cloud-resource-provider-to-vnet.png)

È anche possibile eseguire questo passaggio con il comando seguente dell'interfaccia della riga di comando di Azure:

```azurecli
VIRTUAL_NETWORK_RESOURCE_ID=`az network vnet show \
    --name ${NAME_OF_VIRTUAL_NETWORK} \
    --resource-group ${RESOURCE_GROUP_OF_VIRTUAL_NETWORK} \
    --query "id" \
    --output tsv`

az role assignment create \
    --role "Owner" \
    --scope ${VIRTUAL_NETWORK_RESOURCE_ID} \
    --assignee e8de9221-a19c-4c81-b814-fd37c6caf9d2
```

## <a name="deploy-an-azure-spring-cloud-instance"></a>Distribuire un'istanza di Azure Spring Cloud

Per distribuire l'istanza di Azure Spring Cloud nella rete virtuale:

1. Aprire il [portale di Azure](https://portal.azure.com).

1. Nella casella di ricerca in alto cercare **Azure Spring Cloud**. Selezionare **Azure Spring Cloud** nei risultati.

1. Nella pagina **Azure Spring Cloud** fare clic su **+ Aggiungi**.

1. Compilare il modulo nella pagina di **creazione** di Azure Spring Cloud.

1. Selezionare lo stesso gruppo di risorse e la stessa area della rete virtuale.

1. Per **Nome** in **Dettagli servizio** selezionare **azure-spring-cloud-vnet**.

1. Selezionare la scheda **Rete** e quindi i valori seguenti:

    |Impostazione                                |valore                                             |
    |---------------------------------------|--------------------------------------------------|
    |Distribuisci nella rete virtuale personale     |Selezionare **Sì**.                                   |
    |Rete virtuale                        |Selezionare **azure-spring-cloud-vnet**.               |
    |Subnet del runtime servizio                 |Selezionare **service-runtime-subnet**.                |
    |Subnet delle app per microservizi Spring Boot   |Selezionare **app-subnet**.                           |

    ![Screenshot che mostra la scheda Rete nella pagina di creazione dell'istanza di Azure Spring Cloud.](./media/spring-cloud-v-net-injection/creation-blade-networking-tab.png)

1. Selezionare **Rivedi e crea**.

1. Verificare le specifiche, quindi selezionare **Crea**.

    ![Screenshot che mostra la verifica delle specifiche.](./media/spring-cloud-v-net-injection/verify-specifications.png)

Dopo la distribuzione, nella sottoscrizione verranno creati due gruppi di risorse aggiuntivi in cui ospitare le risorse di rete per l'istanza di Azure Spring Cloud. Passare a **Home**, quindi selezionare **Gruppi di risorse** tra le voci del menu superiore per trovare i nuovi gruppi di risorse seguenti.

Il gruppo di risorse denominato **ap-svc-rt_{nome istanza servizio}_{area istanza servizio}** contiene le risorse di rete per il runtime del servizio dell'istanza del servizio.

  ![Screenshot che mostra il runtime del servizio.](./media/spring-cloud-v-net-injection/service-runtime-resource-group.png)

Il gruppo di risorse denominato **ap-app_{nome istanza servizio}_{area istanza servizio}** contiene le risorse di rete per le applicazioni di microservizi Spring Boot dell'istanza del servizio.

  ![Screenshot che mostra il gruppo di risorse app.](./media/spring-cloud-v-net-injection/apps-resource-group.png)

Queste risorse di rete sono connesse alla rete virtuale creata in precedenza.

  ![Screenshot che mostra la rete virtuale con i dispositivi connessi.](./media/spring-cloud-v-net-injection/vnet-with-connected-device.png)

   > [!Important]
   > I gruppi di risorse sono completamente gestiti dal servizio Azure Spring Cloud. *Non* eliminare o modificare manualmente le risorse al loro interno.

## <a name="using-smaller-subnet-ranges"></a>Utilizzo di intervalli di subnet inferiori

Questa tabella mostra il numero massimo di istanze dell'app che Azure Spring cloud supporta l'uso di intervalli di subnet inferiori.

| CIDR delle subnet di app | Indirizzi IP totali | Indirizzi IP disponibili | Numero massimo di istanze di app                                        |
| --------------- | --------- | ------------- | ------------------------------------------------------------ |
| /28             | 16        | 8             | <p> App con 1 core:  96 <br/> App con 2 core: 48<br/>  App con 3 core: 32 <br/> App con 4 core: 24 </p> |
| /27             | 32        | 24            | <p> App con 1 core:  228<br/> App con 2 core: 144<br/>  App con 3 core: 96 <br/>  App con 4 core: 72</p> |
| /26             | 64        | 56            | <p> App con 1 core:  500<br/> App con 2 core: 336<br/>  App con 3 core: 224<br/>  App con 4 core: 168</p> |
| /25             | 128       | 120           | <p> App con 1 core:  500<br> App con 2 core:  500<br>  App con 3 core:  480<br>  App con 4 core: 360</p> |
| /24             | 256       | 248           | <p> App con 1 core:  500<br/> App con 2 core:  500<br/>  App con 3 core: 500<br/>  App con 4 core: 500</p> |

Per le subnet, cinque indirizzi IP sono riservati da Azure e almeno quattro indirizzi sono richiesti da Azure Spring Cloud. Sono necessari almeno nove indirizzi IP, quindi i blocchi /29 e /30 non sono operativi.

Per una subnet di runtime del servizio, la dimensione minima è /28. Questa dimensione non ha alcun effetto sul numero di istanze di app.

## <a name="bring-your-own-route-table"></a>Porta la tua tabella di route

Il cloud Spring di Azure supporta l'uso di subnet e tabelle di route esistenti.

Se le subnet personalizzate non contengono tabelle di route, Azure Spring cloud le crea per ciascuna subnet e aggiunge le relative regole nel ciclo di vita dell'istanza. Se le subnet personalizzate contengono tabelle di route, Azure Spring cloud riconosce le tabelle di route esistenti durante le operazioni di istanza e aggiunge/aggiorna e/o regole di conseguenza per le operazioni.

> [!Warning] 
> È possibile aggiungere regole personalizzate alle tabelle di route personalizzate e aggiornarle. Tuttavia, le regole vengono aggiunte dal cloud Spring di Azure e non devono essere aggiornate o rimosse. Regole come 0.0.0.0/0 devono sempre esistere in una determinata tabella di route ed eseguire il mapping alla destinazione del gateway Internet, ad esempio un appliance virtuale di rete o un altro gateway in uscita. Prestare attenzione durante l'aggiornamento delle regole quando vengono modificate solo le regole personalizzate.


### <a name="route-table-requirements"></a>Requisiti della tabella di route

Le tabelle di route a cui è associato il VNET personalizzato devono soddisfare i requisiti seguenti:

* È possibile associare le tabelle di route di Azure con la VNET solo quando si crea una nuova istanza del servizio cloud di Azure Spring. Non è possibile modificare l'uso di un'altra tabella di route dopo la creazione di Azure Spring cloud.
* Sia la subnet dell'applicazione del microservizio che la subnet di runtime del servizio devono essere associate a tabelle di route diverse o nessuna di esse.
* Le autorizzazioni devono essere assegnate prima della creazione dell'istanza. Assicurarsi di concedere l'autorizzazione Azure *Spring cloud Owner* alle tabelle di route.
* Non è possibile aggiornare la risorsa della tabella di route associata dopo la creazione del cluster. Anche se non è possibile aggiornare la risorsa della tabella di route, è possibile modificare le regole personalizzate nella tabella di route.
* Non è possibile riutilizzare una tabella di route con più istanze a causa di potenziali regole di routing in conflitto.

## <a name="next-steps"></a>Passaggi successivi

[Distribuire l'applicazione in Azure Spring Cloud nella rete virtuale](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/02-deploy-application-to-azure-spring-cloud-in-your-vnet.md)

## <a name="see-also"></a>Vedere anche

- [Risoluzione dei problemi di Azure Spring Cloud nella rete virtuale](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/05-troubleshooting-azure-spring-cloud-in-vnet.md)
- [Responsabilità del cliente per l'esecuzione di Azure Spring Cloud nella rete virtuale](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/06-customer-responsibilities-for-running-azure-spring-cloud-in-vnet.md)
