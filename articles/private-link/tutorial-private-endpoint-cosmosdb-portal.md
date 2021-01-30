---
title: 'Esercitazione: Connettersi a un account Azure Cosmos con un endpoint privato di Azure'
titleSuffix: Azure Private Link
description: Esercitazione introduttiva all'uso dell'endpoint privato di Azure per connettersi privatamente a un account Azure Cosmos.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: tutorial
ms.date: 9/25/2020
ms.openlocfilehash: 3a7e75641f6bb84b490231fcd06e04c3cbad06d3
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063468"
---
# <a name="tutorial-connect-to-an-azure-cosmos-account-using-an-azure-private-endpoint"></a>Esercitazione: Connettersi a un account Azure Cosmos con un endpoint privato di Azure

Un endpoint privato di Azure è il blocco costitutivo fondamentale del collegamento privato in Azure. Consente alle risorse di Azure, come le macchine virtuali (VM), di comunicare privatamente con le risorse collegamento privato.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare una rete virtuale e un host bastion.
> * Creare una macchina virtuale.
> * Creare un account Cosmos DB con un endpoint privato.
> * Testare la connettività con l'endpoint privato dell'account Cosmos DB.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="create-a-virtual-network-and-bastion-host"></a>Creare una rete virtuale e un host bastion

In questa sezione si creeranno una rete virtuale, una subnet e un host bastion. 

L'host bastion verrà usato per connettersi in modo sicuro alla macchina virtuale per testare l'endpoint privato.

1. In alto a sinistra nella schermata selezionare **Crea una risorsa > Rete > Rete virtuale** o cercare **Rete virtuale** nella casella di ricerca.

2. In **Crea rete virtuale** immettere o selezionare queste informazioni nella scheda **Generale**:

    | **Impostazione**          | **Valore**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Dettagli del progetto**  |                                                                 |
    | Subscription     | Selezionare la sottoscrizione ad Azure                                  |
    | Gruppo di risorse   | Selezionare **MyResourceGroup** |
    | **Dettagli istanza** |                                                                 |
    | Nome             | Immettere **myVNet**                                    |
    | Region           | Selezionare **Stati Uniti orientali**. |

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

8. Selezionare la scheda **Sicurezza**.

9. In **BastionHost** selezionare **Abilita**. Immettere le informazioni seguenti:

    | Impostazione            | valore                      |
    |--------------------|----------------------------|
    | Nome bastion | Immettere **myBastionHost** |
    | Spazio indirizzi della subnet AzureBastionSubnet | Immettere **10.1.1.0/24** |
    | Indirizzo IP pubblico | Selezionare **Crea nuovo**. </br> Per **Nome** immettere **myBastionIP**. </br> Selezionare **OK**. |


8. Selezionare la scheda **Rivedi e crea** oppure il pulsante **Rivedi e crea**.

9. Selezionare **Create** (Crea).

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

In questa sezione si creerà una macchina virtuale che verrà usata per testare l'endpoint privato.

1. Nell'angolo in alto a sinistra del portale selezionare **Crea una risorsa** > **Calcolo** > **Macchina virtuale** oppure cercare **Macchina virtuale** nella casella di ricerca.
   
2. In **Crea macchina virtuale** digitare o selezionare i valori nella scheda **Nozioni di base**:

    | Impostazione | Valore                                          |
    |-----------------------|----------------------------------|
    | **Dettagli del progetto** |  |
    | Subscription | Selezionare la sottoscrizione ad Azure |
    | Gruppo di risorse | Selezionare **MyResourceGroup** |
    | **Dettagli istanza** |  |
    | Nome macchina virtuale | Immettere **myVM** |
    | Area | Selezionare **Stati Uniti orientali**. |
    | Opzioni di disponibilità | Selezionare **La ridondanza dell'infrastruttura non è richiesta** |
    | Immagine | Selezionare **Windows Server 2019 Datacenter - Gen1** |
    | Istanza Spot di Azure | Selezionare **No** |
    | Dimensione | Scegliere le dimensioni della macchina virtuale o usare l'impostazione predefinita |
    | **Account amministratore** |  |
    | Username | Immettere un nome utente |
    | Password | Immettere una password |
    | Conferma password | Reimmettere la password |

3. Selezionare la scheda **Rete**, oppure selezionare **Avanti: Dischi**, quindi **Avanti: Rete**.
  
4. Nella scheda Rete selezionare o immettere:

    | Impostazione | Valore |
    |-|-|
    | **Interfaccia di rete** |  |
    | Rete virtuale | **myVNet** |
    | Subnet | **mySubnet** |
    | IP pubblico | Selezionare **Nessuno**. |
    | Gruppo di sicurezza di rete della scheda di interfaccia di rete | **Base**|
    | Porte in ingresso pubbliche | Selezionare **Nessuno**. |
   
5. Selezionare **Rivedi e crea**. 
  
6. Rivedere le impostazioni e quindi selezionare **Crea**.

## <a name="create-a-cosmos-db-account-with-a-private-endpoint"></a>Creare un account Cosmos DB con un endpoint privato

In questa sezione si creerà un account Cosmos DB e si configurerà l'endpoint privato.

1. Nel menu a sinistra selezionare **Crea una risorsa** > **Database** > **Account Cosmos DB** oppure immettere **Account Cosmos DB** nella casella di ricerca.

2. Nella scheda **Informazioni di base** della pagina **Crea account Cosmos DB** immettere o selezionare le informazioni seguenti:

    | Impostazione | Valore                                          |
    |-----------------------|----------------------------------|
    | **Dettagli del progetto** |  |
    | Subscription | Selezionare la sottoscrizione di Azure. |
    | Gruppo di risorse | Selezionare **myResourceGroup**. |
    | **Dettagli istanza** |  |
    | Nome account | Immettere **mycosmosdb**. Se il nome non è disponibile, immetterne un altro univoco. |
    | API | Selezionare **Core (SQL)**. |
    | Location | Selezionare **Stati Uniti orientali**. |
    | Modalità di capacità | Lasciare l'impostazione predefinita **Provisioning velocità effettiva**. |
    | Applica sconto per il livello gratuito | Lasciare l'impostazione predefinita **Non applicare**. |
    | Ridondanza geografica | Lasciare l'impostazione predefinita **Disabilita**. |
    | Scritture in più aree | Lasciare l'impostazione predefinita **Disabilita**. |
   
3. Selezionare la scheda **Rete** oppure il pulsante **Avanti: Rete**.

4. Nella scheda **Rete** immettere o selezionare le informazioni seguenti:

    | Impostazione | Valore |
    | ------- | ----- |
    | **Connettività di rete** | |
    | Metodo di connettività | Selezionare **Endpoint privato**. |
    | **Configurare il firewall** | |
    | Consenti l'accesso dal portale di Azure | Lasciare l'impostazione predefinita **Consenti**. |
    | Consenti l'accesso dal mio indirizzo IP | Lasciare l'impostazione predefinita **Nega**. |

5. In **Endpoint privato** selezionare **+ Aggiungi**.

6. In **Crea endpoint privato** immettere o selezionare le informazioni seguenti:

    | Impostazione | Valore                                          |
    |-----------------------|----------------------------------|
    | Subscription | Selezionare la sottoscrizione ad Azure |
    | Gruppo di risorse | Selezionare **MyResourceGroup** |
    | Location | Selezionare **Stati Uniti orientali**. |
    | Nome | Immettere **myPrivateEndpoint** |
    | Sottorisorsa di destinazione | Lasciare l'impostazione predefinita **Core (SQL)** |
    | **Rete** |  |
    | Rete virtuale | Selezionare **myVNet** |
    | Subnet | Selezionare **mySubnet** |
    | **Integrazione DNS privato** |
    | Integra con la zona DNS privato | Lasciare l'impostazione predefinita **Sì** |
    | Zona DNS privato | Lasciare l'impostazione predefinita (Nuovo) privatelink.documents.azure.com |

7. Selezionare **OK**.

8. Selezionare **Rivedi e crea**.

9. Selezionare **Crea**.

### <a name="add-a-database-and-a-container"></a>Aggiungere un database e un contenitore

1. Selezionare **Vai alla risorsa** oppure nel menu a sinistra del portale di Azure selezionare **Tutte le risorse** > **mycosmosdb**.

2. Nel menu a sinistra selezionare **Esplora dati**.

3. Nella finestra **Esplora dati** selezionare **Nuovo contenitore**.

4. In **Aggiungi contenitore** immettere o selezionare le informazioni seguenti:

    | Impostazione | Valore |
    | ------- | ----- |
    | ID database | Lasciare l'impostazione predefinita **Crea nuovo**. </br> Immettere **mydatabaseid** nella casella di testo. |
    | Velocità effettiva (400 - 100.000 UR/s) | Lasciare l'impostazione predefinita **Manuale**. </br> Immettere **400** nella casella di testo. |
    | ID contenitore | Immettere **mycontainerid** |
    | Chiave di partizione | Immettere **/mykey** |

5. Selezionare **OK**.

6. Nella sezione **Impostazioni** dell'account CosmosDB selezionare **Chiavi**.

7. Selezionare e copiare il valore di **STRINGA DI CONNESSIONE PRIMARIA**.

## <a name="test-connectivity-to-private-endpoint"></a>Testare la connettività con l'endpoint privato

In questa sezione si userà la macchina virtuale creata nel passaggio precedente per connettersi all'account Cosmos DB tramite l'endpoint privato.

1. Selezionare **Gruppi di risorse** nel riquadro di spostamento sinistro.

1. Selezionare **myResourceGroup**.

1. Selezionare **myVM**.

1. Nella pagina di panoramica di **myVM** selezionare **Connetti** e quindi **Bastion**.

1. Selezionare il pulsante blu **Usa Bastion**.

1. Immettere il nome utente e la password specificati durante la creazione della macchina virtuale.

1. Aprire Windows PowerShell nel server dopo la connessione.

1. Immettere `nslookup <cosmosdb-account-name>.documents.azure.com` e convalidare la risoluzione dei nomi. Sostituire **\<cosmosdb-account-name>** con il nome dell'account Cosmos DB creato nei passaggi precedenti. 

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mycosmosdb8675.privatelink.documents.azure.com
    Address:  10.1.0.5
    Aliases:  mycosmosdb8675.documents.azure.com
    ```
    Per il nome dell'account Cosmos DB viene restituito l'indirizzo IP privato **10.1.0.5**.  Questo indirizzo si trova nella subnet della rete virtuale creata in precedenza.
    
1. Ottenere la stringa di connessione primaria Azure Cosmos DB dal portale. Una stringa di connessione valida è nel formato seguente:
   
   Per gli account API SQL: `https://<accountName>.documents.azure.com:443/;AccountKey=<accountKey>;` per Azure Cosmos DB API per MongoDB: `mongodb://<accountName>:<accountKey>@cdbmongo36.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&retrywrites=false`

1. Installare [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows&toc=%2fazure%2fstorage%2fblobs%2ftoc.json) nella macchina virtuale.

1. Selezionare **Fine** dopo aver installato **Microsoft Azure Storage Explorer**.  Lasciare selezionata la casella per aprire l'applicazione.

1. Nella schermata **Connetti ad Archiviazione di Azure** selezionare **Annulla**.

1. In Storage Explorer fare clic con il pulsante destro del mouse su **Account Cosmos DB** e scegliere **Connetti a Cosmos DB**.

1. Lasciare l'impostazione predefinita **SQL** in **Seleziona API**.

1. Nella casella sotto **Stringa di connessione** incollare la stringa di connessione dell'account Cosmos DB copiata nei passaggi precedenti.

1. Selezionare **Avanti**.

1. Verificare se le impostazioni sono corrette in **Riepilogo connessione**.  

1. Selezionare **Connetti**.

1. Chiudere la connessione a **myVM**.


## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare questa applicazione, eliminare la rete virtuale, la macchina virtuale e l'account Cosmos DB seguendo questa procedura:

1. Nel menu a sinistra selezionare **Gruppi di risorse**.

2. Selezionare **myResourceGroup**.

3. Selezionare **Elimina gruppo di risorse**.

4. In **DIGITARE IL NOME DEL GRUPPO DI RISORSE** immettere **myResourceGroup**.

5. Selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati creati i componenti seguenti:

* Una rete virtuale e un host bastion.
* Una macchina virtuale.
* Un account Cosmos DB.

Informazioni su come creare un servizio Collegamento privato:
> [!div class="nextstepaction"]
> [Creare un servizio Collegamento privato](create-private-link-service-portal.md)
