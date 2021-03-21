---
title: Limitare l'accesso alle risorse PaaS - Esercitazione - Portale di Azure
description: In questa esercitazione si apprenderà come limitare l'accesso di rete alle risorse di Azure, ad esempio Archiviazione di Azure e il database SQL di Azure, con gli endpoint servizio di rete virtuale usando il portale di Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/11/2020
ms.author: kumud
ms.openlocfilehash: cb3a4b6a726ee9163582b15586c65fc750712c63
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97368294"
---
# <a name="tutorial-restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-portal"></a>Esercitazione: Limitare l'accesso di rete alle risorse PaaS con gli endpoint servizio di rete virtuale usando il portale di Azure

Gli endpoint servizio di rete virtuale consentono di limitare l'accesso di rete ad alcune risorse dei servizi di Azure a una subnet della rete virtuale. È anche possibile rimuovere l'accesso Internet alle risorse. Gli endpoint di servizio forniscono la connessione diretta dalla rete virtuale ai servizi di Azure supportati, consentendo di usare lo spazio indirizzi privato della rete virtuale per accedere ai servizi di Azure. Il traffico destinato alle risorse di Azure tramite gli endpoint di servizio rimane sempre nella rete backbone di Microsoft Azure. In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare una rete virtuale con una subnet
> * Aggiungere una subnet e abilitare un endpoint di servizio
> * Creare una risorsa di Azure e consentire l'accesso di rete alla risorsa da una sola subnet
> * Distribuire una macchina virtuale (VM) in ogni subnet
> * Verificare che venga consentito l'accesso a una risorsa da una subnet
> * Verificare che venga rifiutato l'accesso a una risorsa da una subnet e da Internet

Se si preferisce, è possibile completare questa esercitazione usando l'[interfaccia della riga di comando di Azure](tutorial-restrict-network-access-to-resources-cli.md) oppure [Azure PowerShell](tutorial-restrict-network-access-to-resources-powershell.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="create-a-virtual-network"></a>Crea rete virtuale

1. Selezionare **+ Crea una risorsa** nell'angolo in alto a sinistra del portale di Azure.
2. Selezionare **Rete** e quindi **Reti virtuali**.
3. Fare clic su **+ Aggiungi** e immettere le informazioni seguenti: 

   |Impostazione|Valore|
   |----|----|
   |Subscription| Selezionare la propria sottoscrizione|
   |Resource group | Selezionare **Crea nuovo** e immettere *myResourceGroup*.|
   |Name| Immettere *myVirtualNetwork* |
   |Area| Selezionare **(Stati Uniti) Stati Uniti orientali** |

   ![Immettere le informazioni di base sulla rete virtuale](./media/tutorial-restrict-network-access-to-resources/create-virtual-network.png)

4. Fare clic su **Avanti: Indirizzi IP >**
   
   |Impostazione|valore|
   |----|----|
   |Spazio indirizzi IPv4| Lasciare il valore predefinito |
   |Nome della subnet| Fare clic su **predefinito** e cambiare il nome da "predefinito" a "Pubblico"|
   |Intervallo di indirizzi subnet| Lasciare il valore predefinito|

5. Fare clic su **Avanti: Sicurezza >** 
   
   |Impostazione|valore|
   |----|----|   
   |BastionHost| Disabilita|
   |Protezione DDoS| Disabilita|
   |Firewall| Disabilita|

6. Al termine, fare clic su **Rivedi e crea**.
7. Se i controlli di convalida vengono superati, fare clic su **Crea**.
8. Attendere il completamento della distribuzione, quindi fare clic su **Vai alla risorsa** o passare alla sezione successiva. 

## <a name="enable-a-service-endpoint"></a>Abilitare un endpoint di servizio

Gli endpoint di servizio sono abilitati per servizio e per subnet. Per creare una subnet e abilitare un endpoint di servizio:

1. Se non è già aperta la pagina della risorsa rete virtuale, è possibile immettere **myVirtualNetwork** nella casella *Cerca risorse, servizi e documentazione* e selezionare la rete appena creata nell'elenco.
2. Nel menu **Impostazioni** a sinistra selezionare **Subnet**, quindi selezionare **+ Subnet** come illustrato di seguito:

    ![Aggiungi subnet](./media/tutorial-restrict-network-access-to-resources/add-subnet.png) 

3. In **Aggiungi subnet** selezionare o immettere le informazioni seguenti e quindi fare clic su **OK**:

    |Impostazione|Valore|
    |----|----|
    |Nome| Privato |
    |Intervallo di indirizzi| Lasciare il valore predefinito|
    |Endpoint di servizio| Selezionare **Microsoft.Storage**|
    |Criteri degli endpoint di servizio | Lasciare il valore predefinito 0 |

> [!CAUTION]
> Prima di abilitare un endpoint di servizio per una subnet esistente che contiene risorse, vedere [Cambiare le impostazioni della subnet](virtual-network-manage-subnet.md#change-subnet-settings).

4. Fare clic su **Salva**, quindi chiudere la finestra Subnet a destra. La subnet appena creata verrà visualizzata nell'elenco.

## <a name="restrict-network-access-for-a-subnet"></a>Limitare l'accesso di rete per una subnet

Per impostazione predefinita, tutte le istanze di macchine virtuali in una subnet possono comunicare con tutte le risorse. È possibile limitare le comunicazioni verso e da tutte le risorse in una subnet creando un gruppo di sicurezza di rete e associandolo alla subnet:

1. Nell'angolo in alto a sinistra del portale di Azure selezionare **Tutti i servizi**.
2. Selezionare **Rete** e quindi selezionare o cercare **Gruppi di sicurezza di rete**.
3. Nella pagina **Gruppi di sicurezza di rete** fare clic su **+ Aggiungi**.
4. Immettere le informazioni seguenti 

    |Impostazione|Valore|
    |----|----|
    |Subscription| Selezionare la propria sottoscrizione|
    |Gruppo di risorse | Selezionare *myResourceGroup* nell'elenco|
    |Name| Immettere **myNsgPrivate** |
    |Location| Selezionare **Stati Uniti orientali**. |

5. Fare clic su **Rivedi e crea** e, quando il controllo di convalida viene superato, fare clic su **Crea**.
6. Dopo aver creato il gruppo di sicurezza di rete, fare clic su **Vai alla risorsa** o cercare *myNsgPrivate*.
7. In **Impostazioni** a sinistra selezionare **Regole di sicurezza in uscita**.
8. Selezionare **+ Aggiungi**.
9. Creare una regola che consenta le comunicazioni in uscita al servizio Archiviazione di Azure. Immettere o selezionare le informazioni seguenti e quindi fare clic su **Aggiungi**:

    |Impostazione|Valore|
    |----|----|
    |Source (Sorgente)| Selezionare **VirtualNetwork** |
    |Intervalli di porte di origine| * |
    |Destination | Selezionare **Service Tag** (Tag del servizio)|
    |Tag del servizio di destinazione | Selezionare **Archiviazione**|
    |Intervalli di porte di destinazione| Lasciare il valore predefinito *8080* |
    |Protocollo|Qualsiasi|
    |Azione|Allow|
    |Priorità|100|
    |Nome|Assegnare il nuovo nome **Allow-Storage-All**|

10. Creare un'altra regola di sicurezza in uscita che neghi la comunicazione in Internet. Questa regola esegue l'override di una regola predefinita in tutti i gruppi di sicurezza di rete che consente le comunicazioni Internet in uscita. Completare i passaggi 6-9 precedenti usando i valori seguenti:

    |Impostazione|Valore|
    |----|----|
    |Source (Sorgente)| Selezionare **VirtualNetwork** |
    |Intervalli di porte di origine| * |
    |Destination | Selezionare **Service Tag** (Tag del servizio)|
    |Tag del servizio di destinazione| Selezionare **Internet**|
    |Intervalli di porte di destinazione| * |
    |Protocollo|Qualsiasi|
    |Azione|**Cambiare l'impostazione predefinita in *Nega*** |
    |Priorità|110|
    |Nome|Cambiare il nome in *Deny-Internet-All*|

11. Creare una *regola di sicurezza in ingresso* che consenta il traffico RDP (Remote Desktop Protocol) verso la subnet da qualsiasi posizione. La regola esegue l'override di una regola di sicurezza predefinita che rifiuta tutto il traffico in ingresso da Internet. Sono consentite le connessioni Desktop remoto alla subnet per poter testare la connettività in un passaggio successivo. 
12. In **Impostazioni** selezionare **Regole di sicurezza in ingresso**.
13. Selezionare **+ Aggiungi** e usare i valori seguenti:

    |Impostazione|Valore|
    |----|----|
    |Source (Sorgente)| Qualsiasi |
    |Intervalli di porte di origine| * |
    |Destination | Selezionare **VirtualNetwork**|
    |Intervalli di porte di destinazione| Cambiare il valore in *3389* |
    |Protocollo|Qualsiasi|
    |Azione|Allow|
    |Priorità|120|
    |Nome|Cambiare il nome in *Allow-RDP-All*|

   >[!WARNING] 
   > La porta RDP 3389 è esposta a Internet. Questa opzione è consigliata solo per i test. Per gli *ambienti di produzione* è consigliabile usare una connessione privata o VPN.

1.  In **Impostazioni** selezionare **Subnet**.
2.  Fare clic su **+ Associa**.
3.  In **Rete virtuale** selezionare **myVirtualNetwork**.
4.  In **Subnet** selezionare **Privata** e quindi fare clic su **OK**.

## <a name="restrict-network-access-to-a-resource"></a>Limitare l'accesso di rete a una risorsa

I passaggi necessari per limitare l'accesso di rete alle risorse create tramite i servizi di Azure abilitati per gli endpoint di servizio variano a seconda dei servizi. Vedere la documentazione relativa ai singoli servizi per i passaggi specifici. La parte rimanente di questa esercitazione include, a titolo di esempio, i passaggi da eseguire per limitare l'accesso di rete per un account di archiviazione di Azure.

### <a name="create-a-storage-account"></a>Creare un account di archiviazione

1. Selezionare **+ Crea una risorsa** nell'angolo in alto a sinistra del portale di Azure.
2. Immettere "account di archiviazione" nella barra di ricerca e selezionarlo nel menu a discesa.
3. Fare clic su **+ Aggiungi**.
4. Immettere le seguenti informazioni:

    |Impostazione|Valore|
    |----|----|
    |Subscription| Selezionare la propria sottoscrizione|
    |Gruppo di risorse| Selezionare *MyResourceGroup*|
    |Nome account di archiviazione| Immettere un nome univoco per tutte le località di Azure, di lunghezza compresa tra 3 e 24 caratteri e costituito solo da numeri e lettere minuscole.|
    |Posizione| Selezionare **(Stati Uniti) Stati Uniti orientali** |
    |Prestazioni|Standard|
    |Tipologia account| Archiviazione V2 (utilizzo generico v2)|
    |Replica| Archiviazione con ridondanza locale|

5. Selezionare **Rivedi e crea** e, quando i controlli di convalida vengono superati, fare clic su **Crea**. 

>[!NOTE] 
> Il completamento della distribuzione può richiedere un paio di minuti.

6. Dopo aver creato l'account di archiviazione, fare clic su **Vai alla risorsa**

### <a name="create-a-file-share-in-the-storage-account"></a>Creare una condivisione file nell'account di archiviazione

1. Passare alla pagina di panoramica dell'account di archiviazione.
2. Selezionare l'icona dell'app **Condivisioni file**, quindi fare clic su **+ Condivisione file**.

    |Impostazione|Valore|
    |----|----|
    |Nome| my-file-share|
    |Quota| 'Imposta su massimo' |

   ![Account di archiviazione](./media/tutorial-restrict-network-access-to-resources/storage-account.png) 

3. Scegliere **Crea**.
4. La condivisione file dovrebbe essere visualizzata nella finestra di Azure; in caso contrario, fare clic su **Aggiorna**

### <a name="restrict-network-access-to-a-subnet"></a>Limitare l'accesso di rete a una subnet

Per impostazione predefinita, gli account di archiviazione accettano connessioni di rete dai client in qualsiasi rete, inclusa la rete Internet. È possibile limitare l'accesso di rete da Internet e da tutte le altre subnet in tutte le reti virtuali, tranne per la subnet *Privata* nella rete virtuale *myVirtualNetwork*. Per limitare l'accesso di rete a una subnet:

1. In **Impostazioni** per il proprio account di archiviazione (con un nome univoco) selezionare **Rete**.
2. Selezionare **Reti selezionate**.
3. Selezionare **+ Aggiungi rete virtuale esistente**.
4. In **Aggiungi reti** selezionare i valori seguenti e quindi selezionare **Aggiungi**:

    |Impostazione|Valore|
    |----|----|
    |Subscription| Selezionare la propria sottoscrizione|
    |Reti virtuali| **myVirtualNetwork**|
    |Subnet| **Privata**|

    ![Screenshot che mostra il riquadro Aggiungi reti in cui è possibile immettere i valori specificati.](./media/tutorial-restrict-network-access-to-resources/virtual-networks.png)

5. Fare clic su **Aggiungi** e quindi sull'icona **Salva** per salvare le modifiche.
6. In **Impostazioni** per l'account di archiviazione selezionare **Chiavi di accesso**, come illustrato nell'immagine seguente:

      ![Screenshot mostra la voce Chiavi di accesso selezionata In Impostazioni per ottenere una chiave.](./media/tutorial-restrict-network-access-to-resources/storage-access-key.png)

7. Fare clic su **Mostra chiavi** e prendere nota del valore di **Chiave**, perché sarà necessario immetterlo manualmente in un passaggio successivo quando si eseguirà il mapping della condivisione file a una lettera di unità in una macchina virtuale.

## <a name="create-virtual-machines"></a>Creare macchine virtuali

Per testare l'accesso di rete a un account di archiviazione, distribuire una VM in ogni subnet.

### <a name="create-the-first-virtual-machine"></a>Creare la prima macchina virtuale

1. Sulla barra "Cerca risorse . . ." cercare **Macchine virtuali**.
2. Selezionare **+ Aggiungi > Macchina virtuale**. 
3. Immettere le informazioni seguenti:

   |Impostazione|Valore|
   |----|----|
   |Subscription| Selezionare la propria sottoscrizione|
   |Gruppo di risorse| Selezionare **myResourceGroup, creato in precedenza.|
   |Nome macchina virtuale| Immettere *myVmPublic*|
   |Area | (Stati Uniti) Stati Uniti orientali
   |Opzioni di disponibilità| Zona di disponibilità|
   |Zona di disponibilità | 1 |
   |Immagine | Windows Server 2019 Datacenter - Gen1 |
   |Dimensione | Selezionare le dimensioni dell'istanza di VM da usare |
   |Username|Immettere un nome utente a scelta.|
   |Password| Immettere una password a scelta. La password deve contenere almeno 12 caratteri e soddisfare i [requisiti di complessità definiti](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
   |Porte in ingresso pubbliche | Consenti porte selezionate |
   |Selezionare le porte in ingresso | Lasciare il valore predefinito *RDP (3389)* |

   ![Selezionare una rete virtuale](./media/tutorial-restrict-network-access-to-resources/virtual-machine-settings.png)
  
4. Selezionare la scheda **Rete** e quindi **myVirtualNetwork**. 
5. Selezionare la subnet *Pubblica*.
6. In **Gruppo di sicurezza di rete** selezionare **Avanzate**. Il portale crea automaticamente un gruppo di sicurezza di rete per l'utente che consente la porta 3389, che sarà necessario aprire per la connessione alla macchina virtuale in un passaggio successivo. 

   ![Immettere le informazioni di base relative a una macchina virtuale](./media/tutorial-restrict-network-access-to-resources/virtual-machine-basics.png)

7. Selezionare **Rivedi e crea**, quindi **Crea** e attendere il completamento della distribuzione.
8. Fare clic su **Vai alla risorsa** oppure aprire la pagina **Home > Macchine virtuali** e selezionare la VM *myVmPublic* appena creata, che dovrebbe essere avviata.

### <a name="create-the-second-virtual-machine"></a>Creare la seconda macchina virtuale

1. Completare di nuovo i passaggi 1-8, ma nel passaggio 3 assegnare alla macchina virtuale il nome *myVmPrivate* e impostare **Porta in ingresso pubblica** su "Nessuna". 
2. Nei passaggi 4-5 selezionare la subnet **Privata**.

>[!NOTE]
> Le impostazioni di **Gruppo di sicurezza di rete** e **Porte in ingresso pubbliche** dovranno rispecchiare l'immagine seguente, inclusa la finestra di conferma blu indicante che tutto il traffico Internet pubblico verrà bloccato per impostazione predefinita.

   ![Creare macchina virtuale privata](./media/tutorial-restrict-network-access-to-resources/create-private-virtual-machine.png)

3. Selezionare **Rivedi e crea**, quindi **Crea** e attendere il completamento della distribuzione. 

>[!WARNING]
> Continuare con il passaggio successivo solo dopo il completamento della distribuzione.

4. Attendere che venga visualizzata la finestra di conferma mostrata di seguito e fare clic su **Vai alla risorsa**.

   ![Finestra di conferma della creazione di una macchina virtuale privata](./media/tutorial-restrict-network-access-to-resources/create-vm-confirmation-window.png)

## <a name="confirm-access-to-storage-account"></a>Verificare che venga consentito l'accesso a un account di archiviazione

1. Una volta creata la VM *myVmPrivate*, fare clic su **Vai alla risorsa**. 
2. Connettersi alla VM selezionando **Connetti > RDP**.
3. Dopo aver selezionato il pulsante **Connetti**, viene creato un file Remote Desktop Protocol con estensione rdp. Fare clic su **Scarica file RDP** per scaricare il file nel computer.  
4. Aprire il file con estensione rdp scaricato. Quando richiesto, selezionare **Connetti**. Immettere il nome utente e la password specificati al momento della creazione della VM. Può essere necessario selezionare **Altre opzioni**, quindi **Usa un altro account** per specificare le credenziali immesse al momento della creazione della VM. Per il campo dell'indirizzo di posta elettronica, immettere le credenziali specificate in precedenza per "Account amministratore: nome utente". 
5. Selezionare **OK**.
6. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Se viene visualizzato l'avviso, selezionare **Sì** o **Continua** per procedere con la connessione. Verrà visualizzata anche la VM in esecuzione, come illustrato di seguito:

   ![Macchina virtuale privata in esecuzione](./media/tutorial-restrict-network-access-to-resources/virtual-machine-running.png)

7. Nella finestra della VM aprire un'istanza dell'interfaccia della riga di comando di PowerShell.
8. Usando lo script seguente, eseguire il mapping della condivisione file di Azure all'unità Z usando PowerShell. Prima di eseguire i comandi seguenti, sostituire i valori dei campi `<storage-account-key>` e `<storage-account-name>` con i valori specificati e recuperati in [Creare un account di archiviazione](#create-a-storage-account).

   ```powershell
   $acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
   $credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
   New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
   ```

   L'output restituito da PowerShell è simile all'output di esempio seguente:

   ```powershell
   Name           Used (GB)     Free (GB) Provider      Root
   ----           ---------     --------- --------      ----
   Z                                      FileSystem    \\vnt.file.core.windows.net\my-f...
   ```

   Il mapping della condivisione file di Azure all'unità Z è stato eseguito correttamente.

9.   Chiudere la sessione Desktop remoto alla macchina virtuale *myVmPrivate*.

## <a name="confirm-access-is-denied-to-storage-account"></a>Verificare che venga rifiutato l'accesso a un account di archiviazione

1. Immettere *myVmPublic* nella casella **Cerca risorse, servizi e documentazione** nella parte superiore del portale.
2. Selezionare **myVmPublic** quando viene visualizzato nei risultati della ricerca.
3. Completare i passaggi 1-8 della sezione [Verificare che venga consentito l'accesso a un account di archiviazione](#confirm-access-to-storage-account) per la VM *myVmPublic*.

   Dopo una breve attesa verrà visualizzato un errore `New-PSDrive : Access is denied`. L'accesso viene rifiutato perché la VM *myVmPublic* è distribuita nella subnet *Public*. La subnet *pubblica* non ha un endpoint di servizio abilitato per Archiviazione di Azure. L'account di archiviazione consente l'accesso di rete solo dalla subnet *privata* e non dalla subnet *pubblica*.

4. Chiudere la sessione Desktop remoto alla VM *myVmPublic*.
5. Tornare nel portale di Azure e passare all'account di archiviazione con un nome univoco creato in precedenza.
6. In Servizio file selezionare **Condivisioni file** e quindi la condivisione file *my-file-share* creata in precedenza.
7. Dovrebbe essere visualizzato il seguente messaggio di errore:

   ![Errore Accesso negato](./media/tutorial-restrict-network-access-to-resources/access-denied-error.png)
   
>[!NOTE] 
> L'accesso viene rifiutato perché il computer non è nella subnet *Privata* della rete virtuale *MyVirtualNetwork*.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare il gruppo di risorse e tutte le risorse in esso contenute:

1. Immettere *myResourceGroup* nella casella di **ricerca** nella parte superiore del portale. Selezionare **myResourceGroup** quando viene visualizzato nei risultati della ricerca.
2. Selezionare **Elimina gruppo di risorse**.
3. Immettere *myResourceGroup* in **DIGITARE IL NOME DEL GRUPPO DI RISORSE** e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato abilitato un endpoint di servizio per una subnet della rete virtuale. È stato illustrato che è possibile abilitare gli endpoint di servizio per le risorse distribuite da più servizi di Azure. È stato creato un account di archiviazione di Azure ed è stato limitato l'accesso di rete all'account di archiviazione alle sole risorse in una subnet della rete virtuale. Per altre informazioni sugli endpoint servizio, vedere [Panoramica degli endpoint servizio](virtual-network-service-endpoints-overview.md) e [Gestire le subnet](virtual-network-manage-subnet.md).

Se nell'account sono presenti più reti virtuali, può essere necessario connettere due reti virtuali per consentire alle risorse di ogni rete virtuale di comunicare tra loro. Passare all'esercitazione successiva per informazioni su come connettere le reti virtuali.

> [!div class="nextstepaction"]
> [Connettere reti virtuali](./tutorial-connect-virtual-networks-portal.md)
