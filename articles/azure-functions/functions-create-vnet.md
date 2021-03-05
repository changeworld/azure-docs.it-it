---
title: Usare endpoint privati per integrare funzioni di Azure con una rete virtuale
description: Esercitazione dettagliata che illustra come connettere una funzione a una rete virtuale di Azure e bloccarla con endpoint privati
ms.topic: article
ms.date: 2/22/2021
ms.openlocfilehash: a7bad58167009b4089724165813eb061996f1e6b
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102200207"
---
# <a name="tutorial-integrate-azure-functions-with-an-azure-virtual-network-using-private-endpoints"></a>Esercitazione: integrare funzioni di Azure con una rete virtuale di Azure usando endpoint privati

Questa esercitazione illustra come usare funzioni di Azure per connettersi alle risorse in una rete virtuale di Azure con endpoint privati. Verrà creata una funzione con un account di archiviazione bloccato dietro una rete virtuale che usa un trigger della coda del bus di servizio.

> [!div class="checklist"]
> * Creare un'app per le funzioni nel piano Premium
> * Creare risorse di Azure (bus di servizio, account di archiviazione, rete virtuale)
> * Bloccare l'account di archiviazione dietro un endpoint privato
> * Bloccare il bus di servizio dietro un endpoint privato
> * Distribuire un'app per le funzioni con il bus di servizio e i trigger HTTP.
> * Bloccare l'app per le funzioni dietro un endpoint privato
> * Testare per verificare che l'app per le funzioni sia protetta dietro la rete virtuale
> * Pulire le risorse

## <a name="create-a-function-app-in-a-premium-plan"></a>Creare un'app per le funzioni in un piano Premium

Prima di tutto, si crea un'app per le funzioni .NET nel [piano Premium] , perché in questa esercitazione verrà usato C#. In Windows sono supportate anche altre lingue. Questo piano offre una scalabilità senza server per supportare l'integrazione della rete virtuale.

1. Nel menu del portale di Azure o dalla pagina **Home** selezionare **Crea una risorsa**.

1. Nella pagina **Nuovo**, selezionare **Calcolo** > **App per le funzioni**.

1. Nella pagina **Informazioni di base** usare le impostazioni dell'app per le funzioni specificate nella tabella seguente:

    | Impostazione      | Valore consigliato  | Descrizione |
    | ------------ | ---------------- | ----------- |
    | **Sottoscrizione** | Sottoscrizione in uso | Sottoscrizione in cui viene creata questa nuova app per le funzioni. |
    | **[Gruppo di risorse](../azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Nome del nuovo gruppo di risorse in cui creare l'app per le funzioni. |
    | **Nome dell'app per le funzioni** | Nome globalmente univoco | Nome che identifica la nuova app per le funzioni. I caratteri validi sono `a-z` (senza distinzione tra maiuscole e minuscole), `0-9` e `-`.  |
    |**Pubblica**| Codice | Opzione per la pubblicazione di file di codice o di un contenitore Docker. |
    | **Stack di runtime** | .NET | Questa esercitazione USA .NET |
    |**Area**| Area preferita | Scegliere un'[area](https://azure.microsoft.com/regions/) nelle vicinanze o vicino ad altri servizi a cui accedono le funzioni. |

1. Selezionare **Avanti: Hosting**. Nella pagina **Hosting** immettere le impostazioni seguenti:

    | Impostazione      | Valore consigliato  | Descrizione |
    | ------------ | ---------------- | ----------- |
    | **[Account di archiviazione](../storage/common/storage-account-create.md)** |  Nome globalmente univoco |  Creare un account di archiviazione usato dall'app per le funzioni. I nomi degli account di archiviazione devono avere una lunghezza compresa tra 3 e 24 caratteri e possono contenere solo numeri e lettere minuscole. È anche possibile usare un account esistente, che deve soddisfare i [requisiti dell'account di archiviazione](./storage-considerations.md#storage-account-requirements). |
    |**Sistema operativo**| Windows | Questa esercitazione usa Windows |
    | **[Piano](./functions-scale.md)** | Premium | Piano di hosting che definisce come vengono allocate le risorse all'app per le funzioni. Selezionare **Premium**. Per impostazione predefinita, viene creato un nuovo piano di servizio app. Il valore predefinito di **SKU e dimensioni** è **EP1**, dove EP è l'acronimo di _Elastic Premium_, ovvero elastico Premium. Per altre informazioni, vedere l'[elenco di SKU Premium](./functions-premium-plan.md#available-instance-skus).<br/>Quando si eseguono funzioni JavaScript in un piano Premium, è necessario scegliere un'istanza con un minor numero di vCPU. Per altre informazioni, vedere [Scegliere piani Premium con core singolo](./functions-reference-node.md#considerations-for-javascript-functions).  |

1. Selezionare **Avanti: Monitoraggio**. Nella pagina **Monitoraggio** immettere le impostazioni seguenti:

    | Impostazione      | Valore consigliato  | Descrizione |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](./functions-monitoring.md)** | Predefinito | Crea una risorsa di Application Insights con lo stesso *nome di app* nell'area più vicina supportata. Espandendo questa impostazione, è possibile cambiare il valore di **Nome nuova risorsa** oppure scegliere un valore per **Località** in un'[area geografica di Azure](https://azure.microsoft.com/global-infrastructure/geographies/) per archiviare i dati. |

1. Selezionare **Rivedi e crea** per rivedere le selezioni effettuate per la configurazione dell'app.

1. Nella pagina **Rivedi e crea** esaminare le impostazioni e quindi selezionare **Crea** per effettuare il provisioning e distribuire l'app per le funzioni.

1. Selezionare l'icona **Notifiche** nell'angolo superiore destro del portale e attendere che venga visualizzato il messaggio **La distribuzione è riuscita**.

1. Selezionare **Vai alla risorsa** per visualizzare la nuova app per le funzioni. È anche possibile selezionare **Aggiungi al dashboard**. In questo modo è più facile tornare in questa risorsa di app per le funzioni dal dashboard.

1. Congratulazioni! La creazione dell'app per le funzioni Premium è stata completata.

## <a name="create-azure-resources"></a>Creare le risorse di Azure

### <a name="create-a-storage-account"></a>Creare un account di archiviazione

Per le reti virtuali è necessario un account di archiviazione separato da quello creato nella creazione iniziale dell'app per le funzioni.

1. Nel menu del portale di Azure o dalla pagina **Home** selezionare **Crea una risorsa**.

1. Nella nuova pagina cercare **account di archiviazione** e selezionare **Crea**

1. Nella scheda **nozioni di base** impostare le impostazioni come specificato nella tabella seguente. Il resto può essere lasciato come predefinito:

    | Impostazione      | Valore consigliato  | Descrizione      |
    | ------------ | ---------------- | ---------------- |
    | **Sottoscrizione** | Sottoscrizione in uso | Sottoscrizione in cui vengono create le risorse. | 
    | **[Gruppo di risorse](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Scegliere il gruppo di risorse creato con l'app per le funzioni. |
    | **Nome** | mysecurestorage| Nome dell'account di archiviazione a cui verrà applicato l'endpoint privato. |
    | **[Area](https://azure.microsoft.com/regions/)** | myFunctionRegion | Scegliere l'area in cui è stata creata l'app per le funzioni. |

1. Selezionare **Rivedi e crea**. Al termine della convalida selezionare **Crea**.

### <a name="create-a-service-bus"></a>Creare un bus di servizio

1. Nel menu del portale di Azure o dalla pagina **Home** selezionare **Crea una risorsa**.

1. Nella nuova pagina cercare **bus di servizio** e selezionare **Crea**.

1. Nella scheda **nozioni di base** impostare le impostazioni come specificato nella tabella seguente. Il resto può essere lasciato come predefinito:

    | Impostazione      | Valore consigliato  | Descrizione      |
    | ------------ | ---------------- | ---------------- |
    | **Sottoscrizione** | Sottoscrizione in uso | Sottoscrizione in cui vengono create le risorse. |
    | **[Gruppo di risorse](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Scegliere il gruppo di risorse creato con l'app per le funzioni. |
    | **Nome** | myServiceBus| Nome del bus di servizio a cui verrà applicato l'endpoint privato. |
    | **[Area](https://azure.microsoft.com/regions/)** | myFunctionRegion | Scegliere l'area in cui è stata creata l'app per le funzioni. |
    | **Piano tariffario** | Premium | Scegliere questo livello per usare gli endpoint privati con il bus di servizio. |

1. Selezionare **Rivedi e crea**. Al termine della convalida selezionare **Crea**.

### <a name="create-a-virtual-network"></a>Crea rete virtuale

Le risorse di Azure in questa esercitazione si integrano con o vengono inserite in una rete virtuale. Si utilizzeranno endpoint privati per proteggere il traffico di rete contenuto con la rete virtuale.

L'esercitazione crea due subnet:
- **impostazione predefinita**: subnet per endpoint privati. Gli indirizzi IP privati vengono forniti da questa subnet.
- **funzioni**: subnet per l'integrazione della rete virtuale di funzioni di Azure. Questa subnet è delegata all'app per le funzioni.

A questo punto, creare la rete virtuale in cui l'app per le funzioni si integra.

1. Nel menu del portale di Azure o dalla pagina Home selezionare **Crea una risorsa**.

1. Nella nuova pagina cercare **rete virtuale** e selezionare **Crea**.

1. Nella scheda **nozioni di base** usare le impostazioni della rete virtuale come specificato di seguito:

    | Impostazione      | Valore consigliato  | Descrizione      |
    | ------------ | ---------------- | ---------------- |
    | **Sottoscrizione** | Sottoscrizione in uso | Sottoscrizione in cui vengono create le risorse. | 
    | **[Gruppo di risorse](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Scegliere il gruppo di risorse creato con l'app per le funzioni. |
    | **Nome** | myVirtualNet| Nome della rete virtuale a cui si connetterà l'app per le funzioni. |
    | **[Area](https://azure.microsoft.com/regions/)** | myFunctionRegion | Scegliere l'area in cui è stata creata l'app per le funzioni. |

1. Nella scheda **indirizzi IP** selezionare **Aggiungi Subnet**. Usare le impostazioni come specificato di seguito durante l'aggiunta di una subnet:

    :::image type="content" source="./media/functions-create-vnet/1-create-vnet-ip-address.png" alt-text="Screenshot della visualizzazione configurazione della rete virtuale.":::

    | Impostazione      | Valore consigliato  | Descrizione      |
    | ------------ | ---------------- | ---------------- |
    | **Nome della subnet** | functions | Nome della subnet a cui si connetterà l'app per le funzioni. | 
    | **Intervallo di indirizzi subnet** | 10.0.1.0/24 | Si noti che lo spazio degli indirizzi IPv4 nell'immagine precedente è 10.0.0.0/16. Se sopra è 10.1.0.0/16, l'intervallo di *indirizzi di subnet* consigliato sarà 10.1.1.0/24. |

1. Selezionare **Rivedi e crea**. Al termine della convalida selezionare **Crea**.

## <a name="lock-down-your-storage-account-with-private-endpoints"></a>Bloccare l'account di archiviazione con endpoint privati

Gli endpoint privati di Azure vengono usati per connettersi a risorse di Azure specifiche usando un indirizzo IP privato. Questa connessione garantisce che il traffico di rete rimanga all'interno della rete virtuale scelta e che l'accesso sia disponibile solo per risorse specifiche. A questo punto, creare gli endpoint privati per archiviazione file di Azure e l'archiviazione BLOB di Azure con l'account di archiviazione.

1. Nel nuovo account di archiviazione selezionare **rete** nel menu a sinistra.

1. Selezionare la scheda **connessioni endpoint privato** e selezionare **endpoint privato**.

    :::image type="content" source="./media/functions-create-vnet/2-navigate-private-endpoint-store.png" alt-text="Screenshot di come spostarsi per creare endpoint privati per l'account di archiviazione.":::

1. Nella scheda **nozioni di base** usare le impostazioni dell'endpoint privato come specificato di seguito:

    | Impostazione      | Valore consigliato  | Descrizione      |
    | ------------ | ---------------- | ---------------- |
    | **Sottoscrizione** | Sottoscrizione in uso | Sottoscrizione in cui vengono create le risorse. | 
    | **[Gruppo di risorse](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Scegliere il gruppo di risorse creato con l'app per le funzioni. | |
    | **Nome** | endpoint di file | Nome dell'endpoint privato per i file dall'account di archiviazione. |
    | **[Area](https://azure.microsoft.com/regions/)** | myFunctionRegion | Scegliere l'area in cui è stato creato l'account di archiviazione. |

1. Nella scheda **risorsa** usare le impostazioni dell'endpoint privato come specificato di seguito:

    | Impostazione      | Valore consigliato  | Descrizione      |
    | ------------ | ---------------- | ---------------- |
    | **Sottoscrizione** | Sottoscrizione in uso | Sottoscrizione in cui vengono create le risorse. | 
    | **Tipo di risorsa**  | Microsoft.Storage/storageAccounts | Questo è il tipo di risorsa per gli account di archiviazione. |
    | **Risorsa** | mysecurestorage | L'account di archiviazione appena creato |
    | **Sottorisorsa di destinazione** | file | Questo endpoint privato verrà usato per i file dall'account di archiviazione. |

1. Nella scheda **configurazione** scegliere **predefinito** per l'impostazione della subnet.

1. Selezionare **Rivedi e crea**. Al termine della convalida selezionare **Crea**. Le risorse nella rete virtuale possono ora comunicare con i file di archiviazione.

1. Creare un altro endpoint privato per i BLOB. Per la scheda **risorse** , usare le impostazioni seguenti. Per tutte le altre impostazioni, usare le stesse impostazioni dei passaggi di creazione dell'endpoint privato del file appena seguiti.

    | Impostazione      | Valore consigliato  | Descrizione      |
    | ------------ | ---------------- | ---------------- |
    | **Sottoscrizione** | Sottoscrizione in uso | Sottoscrizione in cui vengono create le risorse. | 
    | **Tipo di risorsa**  | Microsoft.Storage/storageAccounts | Questo è il tipo di risorsa per gli account di archiviazione. |
    | **Risorsa** | mysecurestorage | L'account di archiviazione appena creato |
    | **Sottorisorsa di destinazione** | blob | Questo endpoint privato verrà usato per i BLOB dall'account di archiviazione. |

## <a name="lock-down-your-service-bus-with-a-private-endpoint"></a>Bloccare il bus di servizio con un endpoint privato

A questo punto, creare l'endpoint privato per il bus di servizio di Azure.

1. Nel nuovo bus di servizio selezionare **rete** nel menu a sinistra.

1. Selezionare la scheda **connessioni endpoint privato** e selezionare **endpoint privato**.

    :::image type="content" source="./media/functions-create-vnet/3-navigate-private-endpoint-service-bus.png" alt-text="Screenshot su come passare agli endpoint privati per il bus di servizio.":::

1. Nella scheda **nozioni di base** usare le impostazioni dell'endpoint privato come specificato di seguito:

    | Impostazione      | Valore consigliato  | Descrizione      |
    | ------------ | ---------------- | ---------------- |
    | **Sottoscrizione** | Sottoscrizione in uso | Sottoscrizione in cui vengono create le risorse. | 
    | **[Gruppo di risorse](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Scegliere il gruppo di risorse creato con l'app per le funzioni. |
    | **Nome** | SB-endpoint | Nome dell'endpoint privato per i file dall'account di archiviazione. |
    | **[Area](https://azure.microsoft.com/regions/)** | myFunctionRegion | Scegliere l'area in cui è stato creato l'account di archiviazione. |

1. Nella scheda **risorsa** usare le impostazioni dell'endpoint privato come specificato di seguito:

    | Impostazione      | Valore consigliato  | Descrizione      |
    | ------------ | ---------------- | ---------------- |
    | **Sottoscrizione** | Sottoscrizione in uso | Sottoscrizione in cui vengono create le risorse. | 
    | **Tipo di risorsa**  | Microsoft.ServiceBus/namespaces | Questo è il tipo di risorsa per il bus di servizio. |
    | **Risorsa** | myServiceBus | Il bus di servizio creato in precedenza nell'esercitazione. |
    | **Sottorisorsa di destinazione** | namespace | Questo endpoint privato verrà usato per lo spazio dei nomi del bus di servizio. |

1. Nella scheda **configurazione** scegliere **predefinito** per l'impostazione della subnet.

1. Selezionare **Rivedi e crea**. Al termine della convalida selezionare **Crea**. Le risorse nella rete virtuale possono ora comunicare con il bus di servizio.

## <a name="create-a-file-share"></a>Creare una condivisione file

1. Nell'account di archiviazione creato selezionare **condivisioni file** nel menu a sinistra.

1. Selezionare **+ condivisioni file**. Specificare i **file** come nome per la condivisione file ai fini di questa esercitazione.

    :::image type="content" source="./media/functions-create-vnet/4-create-file-share.png" alt-text="Screenshot della creazione di una condivisione file nell'account di archiviazione.":::

## <a name="get-storage-account-connection-string"></a>Ottenere la stringa di connessione dell'account di archiviazione

1. Nell'account di archiviazione creato selezionare chiavi di **accesso** nel menu a sinistra.

1. Selezionare **Mostra chiavi**. Copiare la stringa di connessione di key1 e salvarla. Questa stringa di connessione sarà necessaria in un secondo momento quando si configurano le impostazioni dell'app.

    :::image type="content" source="./media/functions-create-vnet/5-get-store-connection-string.png" alt-text="Screenshot su come ottenere una stringa di connessione dell'account di archiviazione.":::

## <a name="create-a-queue"></a>Creare una coda

Si tratta della coda da cui il trigger del bus di servizio di funzioni di Azure otterrà gli eventi.

1. Nel bus di servizio selezionare **Code** nel menu a sinistra.

1. Selezionare **Criteri di accesso condiviso**. Specificare **queue** come nome per la coda ai fini di questa esercitazione.

    :::image type="content" source="./media/functions-create-vnet/6-create-queue.png" alt-text="Screenshot della creazione di una coda del bus di servizio.":::

## <a name="get-service-bus-connection-string"></a>Ottenere la stringa di connessione del bus di servizio

1. Nel bus di servizio selezionare **criteri di accesso condiviso** nel menu a sinistra.

1. Selezionare **RootManageSharedAccessKey**. Copiare la **stringa di connessione primaria** e salvarla. Questa stringa di connessione sarà necessaria in un secondo momento quando si configurano le impostazioni dell'app.

    :::image type="content" source="./media/functions-create-vnet/7-get-service-bus-connection-string.png" alt-text="Screenshot su come ottenere una stringa di connessione del bus di servizio.":::

## <a name="integrate-function-app-with-your-virtual-network"></a>Integrare l'app per le funzioni con la rete virtuale

Per usare l'app per le funzioni con le reti virtuali, è necessario aggiungerla a una subnet. Viene usata una subnet specifica per l'integrazione della rete virtuale di funzioni di Azure e la rete secondaria predefinita per tutti gli altri endpoint privati creati in questa esercitazione.

1. Nell'app per le funzioni selezionare **rete** nel menu a sinistra.

1. Selezionare **fare clic qui per configurare** l'integrazione con VNet.

    :::image type="content" source="./media/functions-create-vnet/8-connect-app-vnet.png" alt-text="Screenshot di come passare all'integrazione della rete virtuale.":::

1. Selezionare **Aggiungi VNet**

1. Nel pannello che si apre in **rete virtuale** selezionare la rete virtuale creata in precedenza.

1. Selezionare la **subnet** creata in precedenza **funzioni** chiamate. L'app per le funzioni è ora integrata con la rete virtuale.

    :::image type="content" source="./media/functions-create-vnet/9-connect-app-subnet.png" alt-text="Screenshot su come connettere un'app per le funzioni a una subnet.":::

## <a name="configure-your-function-app-settings-for-private-endpoints"></a>Configurare le impostazioni dell'app per le funzioni per gli endpoint privati

1. Nell'app per le funzioni selezionare **configurazione** dal menu a sinistra.

1. Per usare l'app per le funzioni con le reti virtuali, è necessario aggiornare le impostazioni dell'app seguenti. Selezionare **+ nuova impostazione dell'applicazione** o la matita per **modifica** nella colonna più a destra della tabella delle impostazioni dell'app in base alle esigenze. Al termine scegliere **Salva**.

    :::image type="content" source="./media/functions-create-vnet/10-configure-app-settings.png" alt-text="Screenshot di come configurare le impostazioni dell'app per le funzioni per gli endpoint privati.":::

    | Impostazione      | Valore consigliato  | Descrizione      |
    | ------------ | ---------------- | ---------------- |
    | **AzureWebJobsStorage** | mysecurestorageConnectionString | Stringa di connessione dell'account di archiviazione creato. Si tratta della stringa di connessione di archiviazione da [ottenere la stringa di connessione dell'account di archiviazione](#get-storage-account-connection-string). Cambiando questa impostazione, l'app per le funzioni userà ora l'account di archiviazione protetto per le normali operazioni in fase di esecuzione. | 
    | **WEBSITE_CONTENTAZUREFILECONNECTIONSTRING**  | mysecurestorageConnectionString | Stringa di connessione dell'account di archiviazione creato. Cambiando questa impostazione, l'app per le funzioni userà ora l'account di archiviazione protetto per File di Azure, che vengono usati durante la distribuzione. |
    | **WEBSITE_CONTENTSHARE** | files | Nome della condivisione file creata nell'account di archiviazione. Questa impostazione dell'app viene utilizzata insieme a WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. |
    | **SERVICEBUS_CONNECTION** | myServiceBusConnectionString | Creare un'impostazione dell'app per la stringa di connessione del bus di servizio. Si tratta della stringa di connessione di archiviazione da [ottenere la stringa di connessione del bus di servizio](#get-service-bus-connection-string).|
    | **WEBSITE_CONTENTOVERVNET** | 1 | Creare questa impostazione dell'app. Il valore 1 consente la scalabilità dell'app per le funzioni quando l'account di archiviazione è limitato a una rete virtuale. È consigliabile abilitare questa impostazione quando si limita l'account di archiviazione a una rete virtuale. |
    | **WEBSITE_DNS_SERVER** | 168.63.129.16 | Creare questa impostazione dell'app. Quando l'app si integra con una rete virtuale, userà lo stesso server DNS della rete virtuale. Si tratta di una delle due impostazioni necessarie perché l'app per le funzioni funzioni con le zone private di DNS di Azure e sono necessarie quando si usano gli endpoint privati. Queste impostazioni invieranno tutte le chiamate in uscita dall'app alla rete virtuale. |
    | **WEBSITE_VNET_ROUTE_ALL** | 1 | Creare questa impostazione dell'app. Quando l'app si integra con una rete virtuale, userà lo stesso server DNS della rete virtuale. Si tratta di una delle due impostazioni necessarie perché l'app per le funzioni funzioni con le zone private di DNS di Azure e sono necessarie quando si usano gli endpoint privati. Queste impostazioni invieranno tutte le chiamate in uscita dall'app alla rete virtuale. |

1. Nella visualizzazione **configurazione** selezionare la scheda **impostazioni runtime di funzione** .

1. Impostare il **monitoraggio della scala di runtime** **su on** e selezionare **Salva**. La scalabilità basata sul runtime consente di connettere funzioni trigger non HTTP ai servizi in esecuzione all'interno della rete virtuale.

    :::image type="content" source="./media/functions-create-vnet/11-enable-runtime-scaling.png" alt-text="Screenshot su come abilitare il ridimensionamento basato su runtime per funzioni di Azure.":::

## <a name="deploy-a-service-bus-trigger-and-http-trigger-to-your-function-app"></a>Distribuire un trigger del bus di servizio e un trigger http nell'app per le funzioni

1. In GitHub passare al repository di esempio seguente, che contiene un'app per le funzioni con due funzioni, un trigger HTTP e un trigger della coda del bus di servizio.

    <https://github.com/Azure-Samples/functions-vnet-tutorial>

1. Nella parte superiore della pagina selezionare il pulsante **fork** per creare un fork di questo repository nell'account o nell'organizzazione di GitHub.

1. Nell'app per le funzioni selezionare **centro distribuzione** dal menu a sinistra. Quindi, selezionare **Impostazioni**.

1. Nella scheda **Impostazioni** usare le impostazioni di distribuzione come specificato di seguito:

    | Impostazione      | Valore consigliato  | Descrizione      |
    | ------------ | ---------------- | ---------------- |
    | **Origine** | GitHub | È necessario avere creato un repository GitHub con il codice di esempio nel passaggio 2. | 
    | **Organizzazione**  | myOrganization | Si tratta dell'organizzazione in cui è archiviato il repository, in genere l'account. |
    | **Repository** | myRepo | Il repository creato con il codice di esempio. |
    | **Ramo** | main | Si tratta del repository appena creato, quindi usare il Branch principale. |
    | **Stack di runtime** | .NET | Il codice di esempio è in C#. |

1. Selezionare **Salva**. 

    :::image type="content" source="./media/functions-create-vnet/12-deploy-portal.png" alt-text="Screenshot della distribuzione del codice di funzioni di Azure tramite il portale.":::

1. La distribuzione iniziale potrebbe richiedere alcuni minuti. Quando l'app viene distribuita correttamente, nella scheda **logs** verrà visualizzato un messaggio di stato di **esito positivo (attivo)** . Se necessario, aggiornare la pagina. 

1. Congratulazioni! La distribuzione dell'app per le funzioni di esempio è stata completata.

## <a name="lock-down-your-function-app-with-a-private-endpoint"></a>Bloccare l'app per le funzioni con un endpoint privato

A questo punto, creare l'endpoint privato per l'app per le funzioni. Questo endpoint privato connette l'app per le funzioni in modo privato e sicuro alla rete virtuale usando un indirizzo IP privato. Per altre informazioni sugli endpoint privati, vedere la [documentazione relativa agli endpoint privati](https://docs.microsoft.com/azure/private-link/private-endpoint-overview).

1. Nell'app per le funzioni selezionare **rete** nel menu a sinistra.

1. Selezionare **fare clic qui per configurare** in connessioni a endpoint privati.

    :::image type="content" source="./media/functions-create-vnet/14-navigate-app-private-endpoint.png" alt-text="Screenshot su come passare a un endpoint privato app per le funzioni.":::

1. Selezionare **Aggiungi**.

1. Nel menu visualizzato, usare le impostazioni dell'endpoint privato come specificato di seguito:

    :::image type="content" source="./media/functions-create-vnet/15-create-app-private-endpoint.png" alt-text="Screenshot della creazione di un endpoint privato app per le funzioni.":::

1. Selezionare **OK** per aggiungere l'endpoint privato. Congratulazioni! L'app per le funzioni, il bus di servizio e l'account di archiviazione sono stati protetti con endpoint privati.

### <a name="test-your-locked-down-function-app"></a>Testare l'app per le funzioni bloccata

1. Nell'app per le funzioni selezionare **funzioni** dal menu a sinistra.

1. Selezionare il **ServiceBusQueueTrigger**.

1. Scegliere **monitoraggio** dal menu a sinistra. si noterà che non si è in grado di monitorare l'app. Questo perché il browser non ha accesso alla rete virtuale, quindi non può accedere direttamente alle risorse all'interno della rete virtuale. Verrà ora illustrato un altro metodo in base al quale è comunque possibile monitorare la funzione Application Insights.

1. Nell'app per le funzioni selezionare **Application Insights** dal menu a sinistra e selezionare **Visualizza dati Application Insights**.

    :::image type="content" source="./media/functions-create-vnet/16-app-insights.png" alt-text="Screenshot di come visualizzare Application Insights per un app per le funzioni.":::

1. Selezionare **metriche attive** dal menu a sinistra.

1. Aprire una nuova scheda. Nel bus di servizio selezionare **Code** dal menu a sinistra.

1. Selezionare la coda.

1. Selezionare **Service Bus Explorer** dal menu a sinistra. In **Invia** scegliere **testo/normale** come tipo di **contenuto** e immettere un messaggio. 

1. Selezionare **Send (Invia** ) per inviare il messaggio.

    :::image type="content" source="./media/functions-create-vnet/17-send-service-bus-message.png" alt-text="Screenshot su come inviare messaggi del bus di servizio tramite il portale.":::

1. Nella scheda con le **metriche attive** aperte si noterà che il trigger della coda del bus di servizio è stato attivato. In caso contrario, inviare nuovamente il messaggio da **Service Bus Explorer**

    :::image type="content" source="./media/functions-create-vnet/18-hello-world.png" alt-text="Screenshot su come visualizzare i messaggi usando le metriche in tempo reale per le app per le funzioni.":::

1. Congratulazioni! La configurazione dell'app per le funzioni con gli endpoint privati è stata completata.

### <a name="private-dns-zones"></a>Zone DNS privato
L'uso di un endpoint privato per connettersi alle risorse di Azure comporta la connessione a un indirizzo IP privato anziché all'endpoint pubblico. I servizi di Azure esistenti sono configurati per l'uso di DNS esistente per la connessione all'endpoint pubblico. Per la connessione all'endpoint privato è necessario eseguire l'override della configurazione DNS.

È stata creata una zona DNS privata per ogni risorsa di Azure configurata con un endpoint privato. Viene creato un record DNS A per ogni indirizzo IP privato associato all'endpoint privato.

In questa esercitazione sono state create le zone DNS seguenti:

- privatelink.file.core.windows.net
- privatelink.blob.core.windows.net
- privatelink.servicebus.windows.net
- privatelink.azurewebsites.net

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati creati un'app per le funzioni Premium, un account di archiviazione e un bus di servizio e sono stati protetti tutti gli endpoint privati. Altre informazioni sulle diverse funzionalità di rete disponibili di seguito:

> [!div class="nextstepaction"]
> [Altre informazioni sulle opzioni di rete in Funzioni](./functions-networking-options.md)

[Piano Premium]: functions-premium-plan.md
