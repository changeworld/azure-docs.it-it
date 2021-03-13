---
title: Usare endpoint privati per integrare funzioni di Azure con una rete virtuale
description: Questa esercitazione illustra come connettere una funzione a una rete virtuale di Azure e bloccarla usando endpoint privati.
ms.topic: article
ms.date: 2/22/2021
ms.openlocfilehash: 3dd5e700b3081f1c1ef8e4601385c707a5738321
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "102630470"
---
# <a name="tutorial-integrate-azure-functions-with-an-azure-virtual-network-by-using-private-endpoints"></a>Esercitazione: integrare funzioni di Azure con una rete virtuale di Azure usando endpoint privati

Questa esercitazione illustra come usare funzioni di Azure per connettersi alle risorse in una rete virtuale di Azure usando endpoint privati. Si creerà una funzione usando un account di archiviazione bloccato dietro una rete virtuale. La rete virtuale usa un trigger della coda del bus di servizio.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un'app per le funzioni nel piano Premium.
> * Creare risorse di Azure, ad esempio il bus di servizio, l'account di archiviazione e la rete virtuale.
> * Bloccare l'account di archiviazione dietro un endpoint privato.
> * Bloccare il bus di servizio dietro un endpoint privato.
> * Distribuire un'app per le funzioni che usa sia il bus di servizio che i trigger HTTP.
> * Bloccare l'app per le funzioni dietro un endpoint privato.
> * Testare per verificare che l'app per le funzioni sia protetta all'interno della rete virtuale.
> * Pulire le risorse.

## <a name="create-a-function-app-in-a-premium-plan"></a>Creare un'app per le funzioni in un piano Premium

Si creerà un'app per le funzioni .NET nel piano Premium perché questa esercitazione USA C#. In Windows sono supportate anche altre lingue. Il piano Premium fornisce scalabilità senza server e supporta l'integrazione della rete virtuale.

1. Nel menu portale di Azure o nella **Home** page selezionare **Crea una risorsa**.

1. Nella **nuova** pagina selezionare **Compute**  >  **app per le funzioni**.

1. Nella pagina **nozioni di base** usare la tabella seguente per configurare le impostazioni dell'app per le funzioni.

    | Impostazione      | Valore consigliato  | Descrizione |
    | ------------ | ---------------- | ----------- |
    | **Sottoscrizione** | Sottoscrizione in uso | Sottoscrizione in cui viene creata questa nuova app per le funzioni. |
    | **[Gruppo di risorse](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Nome del nuovo gruppo di risorse in cui verrà creata l'app per le funzioni. |
    | **Nome dell'app per le funzioni** | Nome globalmente univoco | Nome che identifica la nuova app per le funzioni. I caratteri validi sono `a-z` (senza distinzione tra maiuscole e minuscole), `0-9` e `-`.  |
    |**Pubblica**| Codice | Scegliere di pubblicare i file di codice o un contenitore docker. |
    | **Stack di runtime** | .NET | Questa esercitazione USA .NET. |
    |**Area**| Area preferita | Scegliere un' [area](https://azure.microsoft.com/regions/) nelle vicinanze o vicino ad altri servizi a cui accedono le funzioni. |

1. Selezionare **Avanti: Hosting**. Nella pagina **Hosting** immettere le impostazioni seguenti.

    | Impostazione      | Valore consigliato  | Descrizione |
    | ------------ | ---------------- | ----------- |
    | **[Account di archiviazione](../storage/common/storage-account-create.md)** |  Nome globalmente univoco |  Creare un account di archiviazione usato dall'app per le funzioni. I nomi degli account di archiviazione devono avere una lunghezza compresa tra 3 e 24 caratteri. Possono contenere solo numeri e lettere minuscole. È anche possibile usare un account esistente, che deve soddisfare i [requisiti dell'account di archiviazione](./storage-considerations.md#storage-account-requirements). |
    |**Sistema operativo**| Windows | Questa esercitazione usa Windows. |
    | **[Piano](./functions-scale.md)** | Premium | Piano di hosting che definisce come vengono allocate le risorse all'app per le funzioni. Per impostazione predefinita, quando si seleziona **Premium**, viene creato un nuovo piano di servizio app. Lo **SKU e la dimensione** predefiniti sono **EP1**, dove *EP* è l'acronimo di _Elastic Premium_. Per ulteriori informazioni, vedere l'elenco degli [SKU Premium](./functions-premium-plan.md#available-instance-skus).<br/><br/>Quando si eseguono funzioni JavaScript in un piano Premium, scegliere un'istanza con un minor numero di vCPU. Per altre informazioni, vedere [Scegliere piani Premium con core singolo](./functions-reference-node.md#considerations-for-javascript-functions).  |

1. Selezionare **Avanti: Monitoraggio**. Nella pagina **Monitoraggio** immettere le impostazioni seguenti.

    | Impostazione      | Valore consigliato  | Descrizione |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](./functions-monitoring.md)** | Predefinito | Creare una risorsa Application Insights con lo stesso nome dell'app nell'area supportata più vicina. Espandere questa impostazione se è necessario modificare il **nome della nuova risorsa** o archiviare i dati in una **posizione** diversa in una [geografia di Azure](https://azure.microsoft.com/global-infrastructure/geographies/). |

1. Selezionare **Rivedi e crea** per rivedere le selezioni effettuate per la configurazione dell'app.

1. Nella pagina **Verifica e crea** verificare le impostazioni. Selezionare quindi **Crea** per eseguire il provisioning e distribuire l'app per le funzioni.

1. Nell'angolo in alto a destra del portale selezionare l'icona **notifiche** e controllare il messaggio **distribuzione riuscita** .

1. Selezionare **Vai alla risorsa** per visualizzare la nuova app per le funzioni. È anche possibile selezionare **Aggiungi al dashboard**. In questo modo è più facile tornare in questa risorsa di app per le funzioni dal dashboard.

Congratulazioni! La creazione dell'app per le funzioni Premium è stata completata.

## <a name="create-azure-resources"></a>Creare le risorse di Azure

Successivamente, verrà creato un account di archiviazione, un bus di servizio e una rete virtuale. 
### <a name="create-a-storage-account"></a>Creare un account di archiviazione

Le reti virtuali dovranno avere un account di archiviazione separato da quello creato con l'app per le funzioni.

1. Nel menu portale di Azure o nella **Home** page selezionare **Crea una risorsa**.

1. Nella **nuova** pagina cercare *account di archiviazione*. Quindi selezionare **Crea**

1. Nella scheda **nozioni di base** usare la tabella seguente per configurare le impostazioni dell'account di archiviazione. Tutte le altre impostazioni possono usare i valori predefiniti.

    | Impostazione      | Valore consigliato  | Descrizione      |
    | ------------ | ---------------- | ---------------- |
    | **Sottoscrizione** | Sottoscrizione in uso | Sottoscrizione in cui vengono create le risorse. | 
    | **[Gruppo di risorse](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Il gruppo di risorse creato con l'app per le funzioni. |
    | **Nome** | mysecurestorage| Nome dell'account di archiviazione a cui verrà applicato l'endpoint privato. |
    | **[Area](https://azure.microsoft.com/regions/)** | myFunctionRegion | Area in cui è stata creata l'app per le funzioni. |

1. Selezionare **Rivedi e crea**. Al termine della convalida, selezionare **Crea**.

### <a name="create-a-service-bus"></a>Creare un bus di servizio

1. Nel menu portale di Azure o nella **Home** page selezionare **Crea una risorsa**.

1. Nella pagina **nuovo** cercare *bus di servizio*. Quindi selezionare **Crea**

1. Nella scheda **nozioni di base** usare la tabella seguente per configurare le impostazioni del bus di servizio. Tutte le altre impostazioni possono usare i valori predefiniti.

    | Impostazione      | Valore consigliato  | Descrizione      |
    | ------------ | ---------------- | ---------------- |
    | **Sottoscrizione** | Sottoscrizione in uso | Sottoscrizione in cui vengono create le risorse. |
    | **[Gruppo di risorse](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Il gruppo di risorse creato con l'app per le funzioni. |
    | **Nome** | myServiceBus| Nome del bus di servizio a cui verrà applicato l'endpoint privato. |
    | **[Area](https://azure.microsoft.com/regions/)** | myFunctionRegion | Area in cui è stata creata l'app per le funzioni. |
    | **Piano tariffario** | Premium | Scegliere questo livello per usare endpoint privati con il bus di servizio di Azure. |

1. Selezionare **Rivedi e crea**. Al termine della convalida, selezionare **Crea**.

### <a name="create-a-virtual-network"></a>Crea rete virtuale

Le risorse di Azure in questa esercitazione si integrano con o vengono inserite in una rete virtuale. Gli endpoint privati verranno usati per contenere il traffico di rete all'interno della rete virtuale.

L'esercitazione crea due subnet:
- **impostazione predefinita**: subnet per endpoint privati. Gli indirizzi IP privati vengono forniti da questa subnet.
- **funzioni**: subnet per l'integrazione della rete virtuale di funzioni di Azure. Questa subnet è delegata all'app per le funzioni.

Creare la rete virtuale in cui si integra l'app per le funzioni:

1. Nel menu portale di Azure o nella **Home** page selezionare **Crea una risorsa**.

1. Nella **nuova** pagina cercare *rete virtuale*. Quindi selezionare **Crea**

1. Nella scheda **nozioni di base** usare la tabella seguente per configurare le impostazioni della rete virtuale.

    | Impostazione      | Valore consigliato  | Descrizione      |
    | ------------ | ---------------- | ---------------- |
    | **Sottoscrizione** | Sottoscrizione in uso | Sottoscrizione in cui vengono create le risorse. | 
    | **[Gruppo di risorse](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Il gruppo di risorse creato con l'app per le funzioni. |
    | **Nome** | myVirtualNet| Nome della rete virtuale a cui si connetterà l'app per le funzioni. |
    | **[Area](https://azure.microsoft.com/regions/)** | myFunctionRegion | Area in cui è stata creata l'app per le funzioni. |

1. Nella scheda **indirizzi IP** selezionare **Aggiungi Subnet**. Usare la tabella seguente per configurare le impostazioni della subnet.

    :::image type="content" source="./media/functions-create-vnet/1-create-vnet-ip-address.png" alt-text="Screenshot della visualizzazione configurazione della rete virtuale.":::

    | Impostazione      | Valore consigliato  | Descrizione      |
    | ------------ | ---------------- | ---------------- |
    | **Nome della subnet** | functions | Nome della subnet a cui si connetterà l'app per le funzioni. | 
    | **Intervallo di indirizzi subnet** | 10.0.1.0/24 | Intervallo di indirizzi della subnet. Nell'immagine precedente si noti che lo spazio degli indirizzi IPv4 è 10.0.0.0/16. Se il valore è 10.1.0.0/16, l'intervallo di indirizzi di subnet consigliato sarà 10.1.1.0/24. |

1. Selezionare **Rivedi e crea**. Al termine della convalida, selezionare **Crea**.

## <a name="lock-down-your-storage-account"></a>Bloccare l'account di archiviazione

Gli endpoint privati di Azure vengono usati per connettersi a risorse di Azure specifiche usando un indirizzo IP privato. Questa connessione garantisce che il traffico di rete rimanga entro la rete virtuale selezionata e che l'accesso sia disponibile solo per risorse specifiche. 

Creare gli endpoint privati per l'archiviazione File di Azure e l'archiviazione BLOB di Azure usando l'account di archiviazione:

1. Nel menu a sinistra del nuovo account di archiviazione selezionare **rete**.

1. Nella scheda **connessioni endpoint privato** selezionare **endpoint privato**.

    :::image type="content" source="./media/functions-create-vnet/2-navigate-private-endpoint-store.png" alt-text="Screenshot su come creare endpoint privati per l'account di archiviazione.":::

1. Nella scheda **nozioni di base** usare le impostazioni degli endpoint privati mostrate nella tabella seguente.

    | Impostazione      | Valore consigliato  | Descrizione      |
    | ------------ | ---------------- | ---------------- |
    | **Sottoscrizione** | Sottoscrizione in uso | Sottoscrizione in cui vengono create le risorse. | 
    | **[Gruppo di risorse](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Scegliere il gruppo di risorse creato con l'app per le funzioni. | |
    | **Nome** | endpoint di file | Nome dell'endpoint privato per i file dall'account di archiviazione. |
    | **[Area](https://azure.microsoft.com/regions/)** | myFunctionRegion | Scegliere l'area in cui è stato creato l'account di archiviazione. |

1. Nella scheda **risorsa** usare le impostazioni degli endpoint privati mostrate nella tabella seguente.

    | Impostazione      | Valore consigliato  | Descrizione      |
    | ------------ | ---------------- | ---------------- |
    | **Sottoscrizione** | Sottoscrizione in uso | Sottoscrizione in cui vengono create le risorse. | 
    | **Tipo di risorsa**  | Microsoft.Storage/storageAccounts | Il tipo di risorsa per gli account di archiviazione. |
    | **Risorsa** | mysecurestorage | L'account di archiviazione creato. |
    | **Sottorisorsa di destinazione** | file | Endpoint privato che verrà usato per i file dall'account di archiviazione. |

1. Nella scheda **configurazione** , per l'impostazione **Subnet** , scegliere **predefinito**.

1. Selezionare **Rivedi e crea**. Al termine della convalida, selezionare **Crea**. Le risorse nella rete virtuale possono ora comunicare con i file di archiviazione.

1. Creare un altro endpoint privato per i BLOB. Nella scheda **risorse** usare le impostazioni indicate nella tabella seguente. Per tutte le altre impostazioni, usare gli stessi valori usati per creare l'endpoint privato per i file.

    | Impostazione      | Valore consigliato  | Descrizione      |
    | ------------ | ---------------- | ---------------- |
    | **Sottoscrizione** | Sottoscrizione in uso | Sottoscrizione in cui vengono create le risorse. | 
    | **Tipo di risorsa**  | Microsoft.Storage/storageAccounts | Il tipo di risorsa per gli account di archiviazione. |
    | **Risorsa** | mysecurestorage | L'account di archiviazione creato. |
    | **Sottorisorsa di destinazione** | blob | Endpoint privato che verrà usato per i BLOB dall'account di archiviazione. |

## <a name="lock-down-your-service-bus"></a>Bloccare il bus di servizio

Creare l'endpoint privato per bloccare il bus di servizio:

1. Nel nuovo bus di servizio, nel menu a sinistra, selezionare **rete**.

1. Nella scheda **connessioni endpoint privato** selezionare **endpoint privato**.

    :::image type="content" source="./media/functions-create-vnet/3-navigate-private-endpoint-service-bus.png" alt-text="Screenshot di come passare agli endpoint privati per il bus di servizio.":::

1. Nella scheda **nozioni di base** usare le impostazioni degli endpoint privati mostrate nella tabella seguente.

    | Impostazione      | Valore consigliato  | Descrizione      |
    | ------------ | ---------------- | ---------------- |
    | **Sottoscrizione** | Sottoscrizione in uso | Sottoscrizione in cui vengono create le risorse. | 
    | **[Gruppo di risorse](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Il gruppo di risorse creato con l'app per le funzioni. |
    | **Nome** | SB-endpoint | Nome dell'endpoint privato per i file dall'account di archiviazione. |
    | **[Area](https://azure.microsoft.com/regions/)** | myFunctionRegion | Area in cui è stato creato l'account di archiviazione. |

1. Nella scheda **risorsa** usare le impostazioni degli endpoint privati mostrate nella tabella seguente.

    | Impostazione      | Valore consigliato  | Descrizione      |
    | ------------ | ---------------- | ---------------- |
    | **Sottoscrizione** | Sottoscrizione in uso | Sottoscrizione in cui vengono create le risorse. | 
    | **Tipo di risorsa**  | Microsoft.ServiceBus/namespaces | Il tipo di risorsa per il bus di servizio. |
    | **Risorsa** | myServiceBus | Il bus di servizio creato in precedenza nell'esercitazione. |
    | **Sottorisorsa di destinazione** | namespace | Endpoint privato che verrà usato per lo spazio dei nomi dal bus di servizio. |

1. Nella scheda **configurazione** , per l'impostazione **Subnet** , scegliere **predefinito**.

1. Selezionare **Rivedi e crea**. Al termine della convalida, selezionare **Crea**. 

Le risorse nella rete virtuale possono ora comunicare con il bus di servizio.

## <a name="create-a-file-share"></a>Creare una condivisione file

1. Nell'account di archiviazione creato, nel menu a sinistra, selezionare **condivisioni file**.

1. Selezionare **+ condivisioni file**. Ai fini di questa esercitazione, assegnare un nome ai *file* di condivisione file.

    :::image type="content" source="./media/functions-create-vnet/4-create-file-share.png" alt-text="Screenshot della creazione di una condivisione file nell'account di archiviazione.":::

1. Selezionare **Crea**.

## <a name="get-the-storage-account-connection-string"></a>Ottenere la stringa di connessione dell'account di archiviazione

1. Nell'account di archiviazione creato fare clic su **chiavi di accesso** nel menu a sinistra.

1. Selezionare **Mostra chiavi**. Copiare e salvare la stringa di connessione di **Key1**. Questa stringa di connessione sarà necessaria quando si configurano le impostazioni dell'app.

    :::image type="content" source="./media/functions-create-vnet/5-get-store-connection-string.png" alt-text="Screenshot su come ottenere una stringa di connessione dell'account di archiviazione.":::

## <a name="create-a-queue"></a>Creare una coda

Creare la coda in cui il trigger del bus di servizio di funzioni di Azure otterrà gli eventi:

1. Nel menu a sinistra del bus di servizio selezionare **Code**.

1. Selezionare **Criteri di accesso condiviso**. Ai fini di questa esercitazione, assegnare un nome alla *coda* dell'elenco.

    :::image type="content" source="./media/functions-create-vnet/6-create-queue.png" alt-text="Screenshot della creazione di una coda del bus di servizio.":::

1. Selezionare **Crea**.

## <a name="get-a-service-bus-connection-string"></a>Ottenere una stringa di connessione del bus di servizio

1. Nel menu a sinistra del bus di servizio selezionare **criteri di accesso condiviso**.

1. Selezionare **RootManageSharedAccessKey**. Copiare e salvare la **stringa di connessione primaria**. Questa stringa di connessione sarà necessaria quando si configurano le impostazioni dell'app.

    :::image type="content" source="./media/functions-create-vnet/7-get-service-bus-connection-string.png" alt-text="Screenshot su come ottenere una stringa di connessione del bus di servizio.":::

## <a name="integrate-the-function-app"></a>Integrare l'app per le funzioni

Per usare l'app per le funzioni con le reti virtuali, è necessario aggiungerla a una subnet. Si userà una subnet specifica per l'integrazione della rete virtuale di funzioni di Azure. Si userà la subnet predefinita per altri endpoint privati creati in questa esercitazione.

1. Nell'app per le funzioni, nel menu a sinistra, selezionare **rete**.

1. In **Integrazione rete virtuale** selezionare **Fare clic per configurare**.

    :::image type="content" source="./media/functions-create-vnet/8-connect-app-vnet.png" alt-text="Screenshot di come passare all'integrazione della rete virtuale.":::

1. Selezionare **Add VNet** (Aggiungi rete virtuale).

1. In **rete virtuale** selezionare la rete virtuale creata in precedenza.

1. Selezionare la subnet **Functions** creata in precedenza. L'app per le funzioni è ora integrata con la rete virtuale.

    :::image type="content" source="./media/functions-create-vnet/9-connect-app-subnet.png" alt-text="Screenshot su come connettere un'app per le funzioni a una subnet.":::

## <a name="configure-your-function-app-settings"></a>Configurare le impostazioni dell'app per le funzioni

1. Nel menu a sinistra dell'app per le funzioni selezionare **Configuration (configurazione**).

1. Per usare l'app per le funzioni con le reti virtuali, aggiornare le impostazioni dell'app mostrate nella tabella seguente. Per aggiungere o modificare un'impostazione, selezionare **+ nuova impostazione applicazione** o l'icona **modifica** nella colonna più a destra della tabella impostazioni app. Al termine, selezionare **Salva**.

    :::image type="content" source="./media/functions-create-vnet/10-configure-app-settings.png" alt-text="Screenshot di come configurare le impostazioni dell'app per le funzioni per gli endpoint privati.":::

    | Impostazione      | Valore consigliato  | Descrizione      |
    | ------------ | ---------------- | ---------------- |
    | **AzureWebJobsStorage** | mysecurestorageConnectionString | Stringa di connessione dell'account di archiviazione creato. Questa stringa di connessione di archiviazione si trova nella sezione [ottenere la stringa di connessione dell'account di archiviazione](#get-the-storage-account-connection-string) . Questa impostazione consente all'app per le funzioni di usare l'account di archiviazione protetto per le normali operazioni in fase di esecuzione. | 
    | **WEBSITE_CONTENTAZUREFILECONNECTIONSTRING**  | mysecurestorageConnectionString | Stringa di connessione dell'account di archiviazione creato. Questa impostazione consente all'app per le funzioni di usare l'account di archiviazione protetto per File di Azure, che viene usato durante la distribuzione. |
    | **WEBSITE_CONTENTSHARE** | files | Nome della condivisione file creata nell'account di archiviazione. Usare questa impostazione con WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. |
    | **SERVICEBUS_CONNECTION** | myServiceBusConnectionString | Creare questa impostazione dell'app per la stringa di connessione del bus di servizio. Questa stringa di connessione di archiviazione è dalla sezione [ottenere una stringa di connessione del bus di servizio](#get-a-service-bus-connection-string) .|
    | **WEBSITE_CONTENTOVERVNET** | 1 | Creare questa impostazione dell'app. Il valore 1 consente la scalabilità dell'app per le funzioni quando l'account di archiviazione è limitato a una rete virtuale. |
    | **WEBSITE_DNS_SERVER** | 168.63.129.16 | Creare questa impostazione dell'app. Quando l'app si integra con una rete virtuale, userà lo stesso server DNS della rete virtuale. L'app per le funzioni richiede questa impostazione in modo che possa funzionare con le zone private di DNS di Azure. È obbligatorio quando si usano endpoint privati. Questa impostazione e WEBSITE_VNET_ROUTE_ALL invieranno tutte le chiamate in uscita dall'app alla rete virtuale. |
    | **WEBSITE_VNET_ROUTE_ALL** | 1 | Creare questa impostazione dell'app. Quando l'app si integra con una rete virtuale, USA lo stesso server DNS della rete virtuale. L'app per le funzioni richiede questa impostazione in modo che possa funzionare con le zone private di DNS di Azure. È obbligatorio quando si usano endpoint privati. Questa impostazione e WEBSITE_DNS_SERVER invieranno tutte le chiamate in uscita dall'app alla rete virtuale. |

1. Nella visualizzazione **configurazione** selezionare la scheda **impostazioni runtime di funzione** .

1. Impostare il **monitoraggio della scala di runtime** **su on**. Selezionare quindi **Salva**. La scalabilità basata sul runtime consente di connettere funzioni trigger non HTTP ai servizi che vengono eseguiti all'interno della rete virtuale.

    :::image type="content" source="./media/functions-create-vnet/11-enable-runtime-scaling.png" alt-text="Screenshot su come abilitare il ridimensionamento basato su runtime per funzioni di Azure.":::

## <a name="deploy-a-service-bus-trigger-and-http-trigger"></a>Distribuire un trigger del bus di servizio e un trigger HTTP

1. In GitHub passare al repository di esempio seguente. Contiene un'app per le funzioni e due funzioni, un trigger HTTP e un trigger della coda del bus di servizio.

    <https://github.com/Azure-Samples/functions-vnet-tutorial>

1. Nella parte superiore della pagina selezionare **fork** per creare un fork di questo repository nell'account o nell'organizzazione di GitHub.

1. Nell'app per le funzioni, nel menu a sinistra selezionare **Deployment Center**. Quindi selezionare **Impostazioni**.

1. Nella scheda **Impostazioni** utilizzare le impostazioni di distribuzione illustrate nella tabella seguente.

    | Impostazione      | Valore consigliato  | Descrizione      |
    | ------------ | ---------------- | ---------------- |
    | **Origine** | GitHub | È necessario avere creato un repository GitHub per il codice di esempio nel passaggio 2. | 
    | **Organizzazione**  | myOrganization | Organizzazione in cui è archiviato il repository. È in genere l'account. |
    | **Repository** | myRepo | Repository creato per il codice di esempio. |
    | **Ramo** | main | Ramo principale del repository creato. |
    | **Stack di runtime** | .NET | Il codice di esempio è in C#. |

1. Selezionare **Salva**. 

    :::image type="content" source="./media/functions-create-vnet/12-deploy-portal.png" alt-text="Screenshot della distribuzione del codice di funzioni di Azure tramite il portale.":::

1. La distribuzione iniziale potrebbe richiedere alcuni minuti. Quando l'app viene distribuita correttamente, nella scheda **logs** viene visualizzato un messaggio di stato di **esito positivo (attivo)** . Se necessario, aggiornare la pagina.

Congratulazioni! La distribuzione dell'app per le funzioni di esempio è stata completata.

## <a name="lock-down-your-function-app"></a>Bloccare l'app per le funzioni

Creare ora l'endpoint privato per bloccare l'app per le funzioni. Questo endpoint privato connette l'app per le funzioni in modo privato e sicuro alla rete virtuale usando un indirizzo IP privato. 

Per ulteriori informazioni, vedere la [documentazione sull'endpoint privato](https://docs.microsoft.com/azure/private-link/private-endpoint-overview).

1. Nell'app per le funzioni, nel menu a sinistra, selezionare **rete**.

1. In **connessioni endpoint privato** selezionare **Configura le connessioni all'endpoint privato**.

    :::image type="content" source="./media/functions-create-vnet/14-navigate-app-private-endpoint.png" alt-text="Screenshot di come passare a un endpoint privato dell'app per le funzioni.":::

1. Selezionare **Aggiungi**.

1. Nel riquadro visualizzato utilizzare le seguenti impostazioni di endpoint privato:

    :::image type="content" source="./media/functions-create-vnet/15-create-app-private-endpoint.png" alt-text="Screenshot della creazione di un endpoint privato dell'app per le funzioni. Il nome è functionapp-endpoint. La sottoscrizione è' private test Sub CACHHAI '. La rete virtuale è MyVirtualNet-tutorial. La subnet è predefinita.":::

1. Selezionare **OK** per aggiungere l'endpoint privato. 
 
Congratulazioni! L'app per le funzioni, il bus di servizio e l'account di archiviazione sono stati protetti con l'aggiunta di endpoint privati.

### <a name="test-your-locked-down-function-app"></a>Testare l'app per le funzioni bloccata

1. Nell'app per le funzioni scegliere **funzioni** dal menu a sinistra.

1. Selezionare **ServiceBusQueueTrigger**.

1. Scegliere **Monitoraggio** dal menu a sinistra. 
 
Si noterà che non è possibile monitorare l'app. Il browser non ha accesso alla rete virtuale, quindi non può accedere direttamente alle risorse all'interno della rete virtuale. 
 
Ecco un modo alternativo per monitorare la funzione usando Application Insights:

1. Nell'app per le funzioni, nel menu a sinistra, selezionare **Application Insights**. Quindi selezionare **Visualizza dati Application Insights**.

    :::image type="content" source="./media/functions-create-vnet/16-app-insights.png" alt-text="Screenshot di come visualizzare Application Insights per un'app per le funzioni.":::

1. Nel menu a sinistra selezionare **metriche attive**.

1. Aprire una nuova scheda. Nel menu a sinistra del bus di servizio selezionare **Code**.

1. Selezionare la coda.

1. Nel menu a sinistra selezionare **Service Bus Explorer**. In **invio**, per **tipo di contenuto**, scegliere **testo/normale**. Quindi immettere un messaggio. 

1. Selezionare **Send (Invia** ) per inviare il messaggio.

    :::image type="content" source="./media/functions-create-vnet/17-send-service-bus-message.png" alt-text="Screenshot su come inviare messaggi del bus di servizio usando il portale.":::

1. Nella scheda **metriche attive** si noterà che il trigger della coda del bus di servizio è stato attivato. In caso contrario, inviare nuovamente il messaggio da **Service Bus Explorer**.

    :::image type="content" source="./media/functions-create-vnet/18-hello-world.png" alt-text="Screenshot su come visualizzare i messaggi usando le metriche in tempo reale per le app per le funzioni.":::

Congratulazioni! L'installazione dell'app per le funzioni è stata testata con endpoint privati.

## <a name="understand-private-dns-zones"></a>Informazioni sulle zone DNS private
È stato usato un endpoint privato per connettersi alle risorse di Azure. Si sta eseguendo la connessione a un indirizzo IP privato anziché all'endpoint pubblico. I servizi di Azure esistenti sono configurati per l'uso di un DNS esistente per la connessione all'endpoint pubblico. È necessario eseguire l'override della configurazione DNS per connettersi all'endpoint privato.

Viene creata una zona DNS privata per ogni risorsa di Azure configurata con un endpoint privato. Viene creato un record DNS per ogni indirizzo IP privato associato all'endpoint privato.

In questa esercitazione sono state create le zone DNS seguenti:

- privatelink.file.core.windows.net
- privatelink.blob.core.windows.net
- privatelink.servicebus.windows.net
- privatelink.azurewebsites.net

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati creati un'app per le funzioni Premium, un account di archiviazione e un bus di servizio. Tutte queste risorse sono state protette dietro gli endpoint privati. 

Per ulteriori informazioni sulle funzionalità di rete disponibili, utilizzare i collegamenti seguenti:

> [!div class="nextstepaction"]
> [Opzioni di rete in funzioni di Azure](./functions-networking-options.md)


> [!div class="nextstepaction"]
> [Piano Premium di funzioni di Azure](./functions-premium-plan.md)
