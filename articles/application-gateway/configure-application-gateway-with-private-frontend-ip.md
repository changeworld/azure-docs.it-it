---
title: Configurare un endpoint del servizio di bilanciamento del carico interno (ILB)
titleSuffix: Azure Application Gateway
description: Questo articolo fornisce informazioni su come configurare un gateway applicazione con un indirizzo IP front-end privato
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: how-to
ms.date: 02/23/2021
ms.author: victorh
ms.openlocfilehash: 224cbe1e34e5915a7fa5fc1cf415c35f86c3abe4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101711655"
---
# <a name="configure-an-application-gateway-with-an-internal-load-balancer-ilb-endpoint"></a>Configurare un gateway applicazione con un endpoint del servizio di bilanciamento del carico interno (ILB)

Applicazione Azure gateway può essere configurato con un indirizzo VIP con connessione Internet o con un endpoint interno non esposto a Internet. Un endpoint interno usa un indirizzo IP privato per il front-end, noto anche come endpoint del servizio di *bilanciamento del carico interno (ILB)*.

La configurazione del gateway tramite un indirizzo IP privato front-end è utile per le applicazioni line-of-business interne che non sono esposte a Internet. È utile anche per i servizi e i livelli all'interno di un'applicazione multilivello che si trovano in un limite di sicurezza non esposto a Internet, ma:

- Richiedi comunque la distribuzione del carico Round Robin
- appiccicosità della sessione
- o Transport Layer Security terminazione (TLS) (precedentemente nota come Secure Sockets Layer (SSL)).

Questo articolo illustra i passaggi per configurare un gateway applicazione con un indirizzo IP privato front-end usando il portale di Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo <https://portal.azure.com>.

## <a name="create-an-application-gateway"></a>Creare un gateway applicazione

Per le comunicazioni tra le risorse create in Azure è necessaria una rete virtuale. Creare una nuova rete virtuale o utilizzarne una esistente. 

In questo esempio si creerà una nuova rete virtuale. È possibile creare una rete virtuale durante la creazione del gateway applicazione. Le istanze del gateway applicazione vengono create in subnet separate. In questo esempio sono presenti due subnet: una per il gateway applicazione e un'altra per i server back-end.

1. Espandere il menu del portale e selezionare **Crea una risorsa**.
2. Selezionare **Rete** e quindi **Gateway applicazione** nell'elenco In primo piano.
3. Immettere *myAppGateway* come nome del gateway applicazione e *myResourceGroupAG* come nuovo gruppo di risorse.
4. Per **area** selezionare **Stati Uniti centrali**.
5. Per **livello** selezionare **standard**.
6. In **Configura rete virtuale** selezionare **Crea nuovo** e quindi immettere i valori seguenti per la rete virtuale:
   - *myVNet* come nome della rete virtuale.
   - *10.0.0.0/16* come spazio indirizzi della rete virtuale.
   - *myAGSubnet* come nome della subnet.
   - *10.0.0.0/24* come spazio indirizzi della subnet.
   - *myBackendSubnet* : per il nome della subnet back-end.
   - *10.0.1.0/24* : per lo spazio di indirizzi della subnet back-end.

    ![Creare una rete virtuale](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-1.png)

6. Selezionare **OK** per creare la rete virtuale e le subnet.
7. Selezionare **Avanti: front-end**.
8. In **tipo di indirizzo IP** front-end selezionare **privato**.

   Per impostazione predefinita, si tratta di un'assegnazione di indirizzi IP dinamici. Il primo indirizzo disponibile della subnet configurata viene assegnato come indirizzo IP front-end.
   > [!NOTE]
   > Una volta allocato, il tipo di indirizzo IP (statico o dinamico) non può essere modificato in un secondo momento.
9. Selezionare **Avanti: backend**.
10. Selezionare **Aggiungi un pool back-end**.
11. Per **nome** digitare *appGatewayBackendPool*.
12. Per **Aggiungi pool back-end senza destinazioni**, selezionare **Sì**. Le destinazioni verranno aggiunte in un secondo momento.
13. Selezionare **Aggiungi**.
14. Selezionare **Avanti: configurazione**.
15. In **regole di routing** selezionare **Aggiungi una regola di routing**.
16. Per **Nome regola** digitare *RRULE-01*.
17. Per **nome listener** digitare *listener-01*.
18. Per **IP** front-end selezionare **privato**.
19. Accettare i valori predefiniti rimanenti e selezionare la scheda **destinazioni back-end** .
20. In **tipo di destinazione** selezionare **pool back-end** e quindi selezionare **appGatewayBackendPool**.
21. Per **impostazione http**, selezionare **Aggiungi nuovo**.
22. Per **Nome impostazione http** Digitare *http-setting-01*.
23. Per **protocollo backend** selezionare **http**.
24. Per la **porta back-end** Digitare *80*.
25. Accettare le impostazioni predefinite rimanenti e selezionare **Aggiungi**.
26. Nella pagina **Aggiungi una regola di routing** selezionare **Aggiungi**.
27. Selezionare **Avanti: Tag**.
28. Selezionare **Avanti: Rivedi e crea**.
29. Rivedere le impostazioni nella pagina Riepilogo e quindi selezionare **Crea** per creare le risorse di rete e il gateway applicazione. La creazione del gateway applicazione può richiedere diversi minuti. Attendere fino al termine della distribuzione prima di passare alla sezione successiva.

## <a name="add-backend-pool"></a>Aggiungere un pool back-end

Il pool back-end viene usato per instradare le richieste ai server back-end che gestiscono la richiesta. Il back-end può essere costituito da schede di rete, set di scalabilità di macchine virtuali, indirizzi IP pubblici, indirizzi IP interni, nomi di dominio completi (FQDN) e back-end multi-tenant come app Azure Service. In questo esempio, come back-end di destinazione si usano macchine virtuali. È possibile usare macchine virtuali esistenti o crearne di nuove. In questo esempio vengono create due macchine virtuali che Azure usa come server back-end per il gateway applicazione.

A tale scopo, eseguire l'operazione seguente:

1. Creare due nuove macchine virtuali, *myVM* e *myVM2*, usate come server back-end.
2. Installare IIS nelle macchine virtuali per verificare che il gateway applicazione sia stato creato correttamente.
3. Aggiungere i server back-end al pool back-end.

### <a name="create-a-virtual-machine"></a>Creare una macchina virtuale


1. Selezionare **Crea una risorsa**.
2. Selezionare **Calcolo** e quindi **Macchina virtuale**.
4. Immettere i valori seguenti per la macchina virtuale:
   - Selezionare la propria sottoscrizione.
   - Selezionare *myResourceGroupAG* per **gruppo di risorse**.
   - Digitare *myVM* per **nome macchina virtuale**.
   - Selezionare **Windows Server 2019 datacenter** per l' **immagine**.
   - Digitare un **nome utente** valido.
   - Digitare una **password** valida.
1. Accettare i valori predefiniti rimanenti e fare clic su **Avanti: dischi**.
1. Accettare le impostazioni predefinite e selezionare **Avanti: rete**.
1. Verificare che **myVNet** sia selezionato per la rete virtuale e che la subnet sia **myBackendSubnet**.
1. Accettare i valori predefiniti rimanenti e fare clic su **Avanti: gestione**.
1. Selezionare **Disabilita** per disabilitare la diagnostica di avvio.
1. Selezionare **Rivedi e crea**.
1. Verificare le impostazioni nella pagina di riepilogo e quindi selezionare **Crea**. La creazione della macchina virtuale può richiedere diversi minuti. Attendere fino al termine della distribuzione prima di passare alla sezione successiva.

### <a name="install-iis"></a>Installare IIS

1. Aprire il Cloud Shell e verificare che sia impostato su **PowerShell**.
    ![Screenshot mostra una finestra della console Azure Cloud Shell aperta che usa PowerShell.](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-3.png)
2. Eseguire questo comando per installare IIS nella macchina virtuale:

   ```azurepowershell
   Set-AzVMExtension `
        -ResourceGroupName myResourceGroupAG `
        -ExtensionName IIS `
        -VMName myVM `
        -Publisher Microsoft.Compute `
        -ExtensionType CustomScriptExtension `
        -TypeHandlerVersion 1.4 `
        -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
         -Location CentralUS 

   ```

3. Creare una seconda macchina virtuale e installare IIS seguendo la procedura appena completata. Usare myVM2 per il nome della macchina virtuale e per `VMName` in `Set-AzVMExtension` .

### <a name="add-backend-servers-to-backend-pool"></a>Aggiungere i server back-end al pool back-end

1. Fare clic su **Tutte le risorse** e quindi selezionare **myAppGateway**.
2. Selezionare **pool back-end** e quindi selezionare **appGatewayBackendPool**.
3. In **tipo di destinazione** selezionare **macchina virtuale**  e in **destinazione** selezionare il vNIC associato a myVM.
4. Ripetere l'aggiunta per aggiungere MyVM2.
   ![Modificare il riquadro pool back-end con i tipi di destinazione e le destinazioni evidenziati.](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-4.png)
5. Selezionare **Salva**.

## <a name="create-a-client-virtual-machine"></a>Creare una macchina virtuale client

La macchina virtuale client viene usata per connettersi al pool back-end del gateway applicazione.

- Creare una terza macchina virtuale usando i passaggi precedenti. Usare myVM3 per il nome della macchina virtuale.

## <a name="test-the-application-gateway"></a>Testare il gateway applicazione

1. Nella pagina myAppGateway selezionare **configurazioni IP** front-end per prendere nota dell'indirizzo IP privato front-end.
    ![Riquadro configurazioni IP front-end con il tipo privato evidenziato.](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-5.png)
2. Copiare l'indirizzo IP privato e quindi incollarlo nella barra degli indirizzi del browser in myVM3 per accedere al pool back-end del gateway applicazione.

## <a name="next-steps"></a>Passaggi successivi

Se si vuole monitorare l'integrità del pool back-end, vedere [log di diagnostica e integrità back-end per il gateway applicazione](application-gateway-diagnostics.md).
