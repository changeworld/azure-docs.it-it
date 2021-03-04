---
title: Usare endpoint privati per un accesso sicuro alla competenza
description: Questo articolo descrive come è possibile configurare un endpoint privato per l'account di competenza
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 03/02/2021
ms.openlocfilehash: 3193c5c00789b793a5b5beaf662f94ab9525888a
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102107590"
---
# <a name="use-private-endpoints-for-your-purview-account"></a>Usare endpoint privati per l'account di competenza

È possibile usare endpoint privati per gli account di competenza per consentire a client e utenti in una rete virtuale (VNet) di accedere in modo sicuro al catalogo tramite un collegamento privato. L'endpoint privato usa un indirizzo IP dello spazio di indirizzi della VNet per l'account di competenza. Il traffico di rete tra i client nella VNet e l'account di competenza attraversa il VNet e un collegamento privato sulla rete dorsale Microsoft, eliminando l'esposizione dalla rete Internet pubblica.

## <a name="create-purview-account-with-a-private-endpoint"></a>Creare un account di competenza con un endpoint privato

1. Passare alla [portale di Azure](https://portal.azure.com) e quindi al proprio account di competenza.

1. Inserire le informazioni di base e impostare metodo di connettività sull'endpoint privato nella scheda **rete** . Configurare gli endpoint privati per l'inserimento fornendo i dettagli della **sottoscrizione, della VNET e della subnet** che si vuole associare all'endpoint privato.

    :::image type="content" source="media/catalog-private-link/create-pe-azure-portal.png" alt-text="Creazione di PE durante la creazione dell'account":::

1. Facoltativamente, è anche possibile scegliere di configurare una **zona DNS privato** per ogni endpoint privato di inserimento.

1. Fare clic su Aggiungi per aggiungere un endpoint privato per l'account di competenza.

1. Nel pannello Crea endpoint privato impostare la sottorisorsa di competenza su **account**, scegliere la rete virtuale e la subnet e selezionare l'area DNS privato in cui verrà registrato il DNS. è anche possibile usare i server DNS ottenuti o creare record DNS usando i file host nelle macchine virtuali.

    :::image type="content" source="media/catalog-private-link/create-pe-account.png" alt-text="Creazione di PE durante la creazione dell'account":::

1. Selezionare **OK**.

1. Selezionare **Rivedi e crea**. Si viene reindirizzati alla pagina Rivedi e crea dove Azure convalida la configurazione.

1. Quando viene visualizzato il messaggio Convalida superata, selezionare **Crea**.

    :::image type="content" source="media/catalog-private-link/validation-passed.png" alt-text="Convalida passata per la creazione dell'account":::

## <a name="create-a-private-endpoint-for-the-purview-web-portal"></a>Creare un endpoint privato per il portale Web di competenza

1. Passare all'account di competenza appena creato, selezionare le connessioni all'endpoint privato nella sezione Impostazioni.
    
1. Fare clic su + endpoint privato per creare un nuovo endpoint privato.

    :::image type="content" source="media/catalog-private-link/pe-portal.png" alt-text="Crea endpoint privato del portale":::

1. Immettere le informazioni di base.

1. Nella scheda risorsa selezionare il tipo di risorsa da **Microsoft. competenza/accounts**.

1. Selezionare la risorsa in modo che sia l'account di competenza appena creato e selezionare la risorsa secondaria di destinazione come **portale**.
    :::image type="content" source="media/catalog-private-link/pe-portal-details.png" alt-text="Dettagli per l'endpoint privato del portale":::

1. Selezionare la rete virtuale e la zona DNS privato nella scheda configurazione. Passare alla pagina di riepilogo e fare clic su **Crea** per creare l'endpoint privato del portale.

## <a name="enabling-access-to-azure-active-directory"></a>Abilitazione dell'accesso a Azure Active Directory

> [!NOTE]
> Se la macchina virtuale, il gateway VPN o il gateway di peering VNET ha accesso a Internet pubblico, può accedere al portale di competenza e all'account di competenza abilitato con endpoint privati e non è necessario seguire le istruzioni riportate di seguito. Se la rete privata dispone di regole del gruppo di sicurezza di rete impostate per negare tutto il traffico Internet pubblico, sarà necessario aggiungere alcune regole per abilitare l'accesso ad AAD. Per eseguire questa operazione, seguire le istruzioni riportate di seguito.

Le istruzioni riportate di seguito sono destinate ad accedere in modo sicuro da una macchina virtuale di Azure. Se si usa una VPN o altri gateway di peering VNET, è necessario seguire una procedura simile.

1. Passare alla macchina virtuale nell'portale di Azure, quindi selezionare scheda rete nella sezione Impostazioni. Selezionare quindi regole porta in uscita e fare clic su Aggiungi regola porta in uscita.

    :::image type="content" source="media/catalog-private-link/outbound-rule-add.png" alt-text="Aggiunta di una regola in uscita":::

2. Nel pannello Aggiungi regola porta in uscita selezionare *destinazione* da tag servizio, tag servizio di destinazione come **AzureActiveDirectory**, intervalli di porte di destinazione *, azione da consentire, **priorità maggiore della regola che ha negato tutto il traffico Internet**. Creare la regola.

    :::image type="content" source="media/catalog-private-link/outbound-rule-details.png" alt-text="Aggiunta dei dettagli della regola in uscita":::
 
3. Seguire gli stessi passaggi per creare un'altra regola per consentire il tag del servizio '**AzureResourceManager**'. Se è necessario accedere alla portale di Azure, è anche possibile aggiungere una regola per il tag del servizio '*AzurePortal*'.

4. Connettersi alla macchina virtuale, aprire il browser, passare alla console del browser (CTRL + MAIUSC + J) e passare alla scheda rete per monitorare le richieste di rete. Immettere web.purview.azure.com nella casella URL e provare ad accedere usando le credenziali di AAD. L'accesso potrebbe probabilmente non riuscire e nella scheda rete della console è possibile vedere AAD che tenta di accedere a aadcdn.msauth.net, ma che si sta bloccando.

    :::image type="content" source="media/catalog-private-link/login-fail.png" alt-text="Dettagli accesso non riuscito":::
 
5. In questo caso, aprire un prompt dei comandi nella macchina virtuale e ping this URL (aadcdn.msauth.net), ottenere il relativo indirizzo IP e quindi aggiungere una regola di porta in uscita per l'indirizzo IP nelle regole di sicurezza di rete della macchina virtuale. Impostare la destinazione su indirizzo IP e impostare gli indirizzi IP di destinazione sull'IP di aadcdn. A causa del bilanciamento del carico e di gestione traffico, l'indirizzo IP della rete CDN di AAD potrebbe essere dinamico. Una volta ottenuto il relativo IP, è preferibile aggiungerlo al file host della macchina virtuale per forzare il browser a visitare tale IP per ottenere la rete CDN di AAD.

    :::image type="content" source="media/catalog-private-link/ping.png" alt-text="Ping di test":::

    :::image type="content" source="media/catalog-private-link/aadcdn-rule.png" alt-text="Regola rete CDN di AAD":::
 
6. Dopo aver creato la nuova regola, tornare alla macchina virtuale e provare a eseguire di nuovo l'accesso con le credenziali di AAD. Se l'accesso ha esito positivo, il portale di competenza è pronto per l'uso. In alcuni casi, tuttavia, AAD reindirizza ad altri domini per l'accesso in base al tipo di conto del cliente. Ad esempio, per un account live.com, AAD reindirizza a live.com per l'accesso, quindi tali richieste verranno nuovamente bloccate. Per gli account dei dipendenti Microsoft, AAD accederà a msft.sts.microsoft.com per informazioni di accesso. Controllare le richieste di rete nella scheda rete del browser per vedere quali richieste del dominio sono bloccate, ripetere il passaggio precedente per ottenere l'indirizzo IP e aggiungere le regole della porta in uscita nel gruppo di sicurezza di rete per consentire le richieste per tale indirizzo IP (se possibile, aggiungere l'URL e l'IP al file host della macchina virtuale per correggere la risoluzione DNS). Se si conoscono gli intervalli IP del dominio di accesso esatto, è anche possibile aggiungerli direttamente alle regole di rete.

7. A questo punto, l'accesso ad AAD dovrebbe avere esito positivo. Il portale di competenza verrà caricato correttamente, ma l'elenco di tutti gli account di competenza non funzionerà perché può accedere solo a un account di competenza specifico. Immettere *Web. competenza. Azure. com/Resource/{PurviewAccountName}* per visitare direttamente l'account di competenza per cui è stato configurato correttamente un endpoint privato.

##  <a name="enable-private-endpoint-on-existing-purview-accounts"></a>Abilitare l'endpoint privato negli account di competenza esistenti

Sono disponibili 2 modi per aggiungere endpoint privati di competenza dopo aver creato l'account di competenza:
- Uso del portale di Azure (account di competenza)
- Uso del centro collegamenti privati

### <a name="using-the-azure-portal-purview-account"></a>Uso del portale di Azure (account di competenza)

1. Passare all'account di competenza dalla portale di Azure, selezionare le connessioni all'endpoint privato nella sezione **rete** di **Impostazioni**.

:::image type="content" source="media/catalog-private-link/pe-portal.png" alt-text="Crea endpoint privato del portale":::
    
2. Fare clic su + endpoint privato per creare un nuovo endpoint privato.

3. Immettere le informazioni di base.

4. Nella scheda risorsa selezionare il tipo di risorsa da **Microsoft. competenza/accounts**.

5. Selezionare la risorsa da usare come account di competenza e selezionare la sottorisorsa di destinazione da **tenere in considerazione**.

6. Selezionare la **rete virtuale** e la **zona DNS privato** nella scheda configurazione. Passare alla pagina di riepilogo e fare clic su **Crea** per creare l'endpoint privato del portale.

> [!NOTE]
> È necessario seguire la stessa procedura descritta in precedenza per la sottorisorsa di destinazione selezionata anche come **portale** .

### <a name="using-the-private-link-center"></a>Uso del centro collegamenti privati

1. Passare al [portale di Azure](https://portal.azure.com).

2. Nella barra di ricerca nella parte superiore della pagina cercare "collegamento privato" e passare al pannello del collegamento privato facendo clic sulla prima opzione.

3. Fare clic su "+ Aggiungi" e inserire i dettagli di base.

    :::image type="content" source="media/catalog-private-link/private-link-center.png" alt-text="Crea PE da Link Center privato":::

4. Selezionare la risorsa come account di competenza già creato e selezionare la sottorisorsa di destinazione come **account**.

5. Selezionare la rete virtuale e la zona DNS privato nella scheda configurazione. Passare alla pagina di riepilogo e fare clic su **Crea** per creare l'endpoint privato dell'account.

> [!NOTE]
> È necessario seguire la stessa procedura descritta in precedenza per la sottorisorsa di destinazione selezionata anche come **portale** .

## <a name="next-steps"></a>Passaggi successivi

- [Esplora il Data Catalog di competenza di Azure](how-to-browse-catalog.md)
- [Eseguire ricerche in Azure Purview Data Catalog](how-to-search-catalog.md)  