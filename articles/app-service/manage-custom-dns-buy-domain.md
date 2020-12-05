---
title: Acquistare un nome di dominio personalizzato
description: Informazioni su come acquistare un dominio del servizio app e usarlo come dominio personalizzato per il servizio app app Azure.
ms.assetid: 70fb0e6e-8727-4cca-ba82-98a4d21586ff
ms.topic: article
ms.date: 11/30/2020
ms.custom: seodec18
ms.openlocfilehash: 6f0ff7a54c2ad1fa1af649c8082498b442783c7e
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2020
ms.locfileid: "96608078"
---
# <a name="buy-a-custom-domain-name-for-azure-app-service"></a>Acquistare un nome di dominio personalizzato per Servizio app di Azure

I domini del servizio app sono domini personalizzati gestiti direttamente in Azure. Semplificano le operazioni di gestione dei domini personalizzati per il [Servizio app di Azure](overview.md). In questa esercitazione viene illustrato come acquistare un dominio del servizio app e come assegnare i nomi DNS al Servizio app di Azure.

Per una macchina virtuale di Azure o per Archiviazione di Azure, vedere [Assign App Service domain to Azure VM or Azure Storage](https://azure.github.io/AppService/2017/07/31/Assign-App-Service-domain-to-Azure-VM-or-Azure-Storage) (Assegnare il dominio del servizio app alla macchina virtuale di Azure o ad Archiviazione di Azure). Per i servizi cloud, vedere [Configurazione di un nome di dominio personalizzato per un servizio cloud di Azure](../cloud-services/cloud-services-custom-domain-name-portal.md).

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

* [Creare un'app del servizio app](./index.yml) oppure usare un'app creata per un'altra esercitazione. L'app deve trovarsi in un'area pubblica di Azure. Al momento, i cloud nazionali di Azure non sono supportati.
* [Rimuovere il limite di spesa per la sottoscrizione](../cost-management-billing/manage/spending-limit.md#remove). Non è possibile acquistare domini del servizio app con crediti della sottoscrizione gratuiti.

## <a name="buy-an-app-service-domain"></a>Acquistare un dominio del servizio app

Per informazioni sui prezzi per i domini del servizio app, visitare la [pagina dei prezzi del servizio app](https://azure.microsoft.com/pricing/details/app-service/windows/) e scorrere verso il basso fino al dominio del servizio app.

1. Aprire il [portale di Azure](https://portal.azure.com) e accedere con l'account Azure.

1. Nella barra di ricerca cercare e selezionare domini del **servizio app**.

    ![Esplorazione del portale per app Azure domini del servizio](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. Nella visualizzazione **domini del servizio app** fare clic su **Aggiungi**.

    ![Fare clic su Aggiungi in domini del servizio app](./media/app-service-web-tutorial-custom-domain/add-app-service-domain.png)

1. Selezionare **fare clic per provare la versione più recente dei domini del servizio app crea esperienza**.

    ![Creazione di un dominio del servizio app con nuova esperienza](./media/app-service-web-tutorial-custom-domain/select-new-create-experience.png)

### <a name="basics-tab"></a>Scheda Informazioni di base

1. Nella scheda **nozioni di base** configurare le impostazioni usando la tabella seguente:  

   | Impostazione  | Descrizione |
   | -------- | ----------- |
   | **Sottoscrizione** | Sottoscrizione da usare per acquistare il dominio. |
   | **Gruppo di risorse** | Gruppo di risorse in cui inserire il dominio. Ad esempio, il gruppo di risorse in cui si trova l'app. |
   | **Dominio** | Digitare il dominio desiderato. Ad esempio, **contoso.com**. Se il dominio desiderato non è disponibile, è possibile effettuare una selezione da un elenco di suggerimenti dei domini disponibili oppure provare un altro dominio. |

    > [!NOTE]
    > I [domini di primo livello](https://wikipedia.org/wiki/Top-level_domain) seguenti sono supportati dai domini del servizio app: _com_, _net_, _co.uk_, _org_, _nl_, _in_, _biz_, _org.uk_ e _co.in_.
    >
    >
    
2. Al termine, fare clic su **Avanti: informazioni di contatto**.

### <a name="contact-information-tab"></a>Scheda informazioni contatto

1. Fornire le informazioni richieste da [ICANN](https://go.microsoft.com/fwlink/?linkid=2116641) per la registrazione del dominio. 

    È molto importante compilare tutti i campi obbligatori con la massima correttezza possibile. I dati non corretti per le informazioni di contatto possono causare un errore di acquisto del dominio.

1. Al termine, fare clic su **Avanti: avanzate**.

### <a name="advanced-tab"></a>Avanzate - scheda

1. Nella scheda **Avanzate** configurare le impostazioni facoltative:  

   | Impostazione  | Descrizione |
   | -------- | ----------- |
   | **Rinnovo automatico** | Abilitato per impostazione predefinita. Il dominio del servizio app viene registrato per gli incrementi di un anno. Il rinnovo automatico garantisce che la registrazione del dominio non scada e che si mantenga la proprietà del dominio. Alla sottoscrizione di Azure viene addebitata automaticamente la tariffa annuale per la registrazione del dominio al momento del rinnovo. Per rifiutare esplicitamente, selezionare **Disabilita**. Se il rinnovo automatico è disabilitato, è possibile [rinnovarlo manualmente](#renew-the-domain). |
   | **Protezione della privacy** | Abilitato per impostazione predefinita. La protezione della privacy nasconde le informazioni di contatto per la registrazione del dominio dal database WHOIS. La protezione della privacy è già inclusa nella tariffa annuale per la registrazione dei domini. Per rifiutare esplicitamente, selezionare **Disabilita**. |

2. Al termine, fare clic su **Avanti: Tag**.

### <a name="finish"></a>Finish

1. Nella scheda **tag** impostare i tag desiderati per il dominio del servizio app. L'assegnazione di tag non è necessaria per l'uso di domini del servizio app, ma è una [funzionalità di Azure che consente di gestire le risorse](../azure-resource-manager/management/tag-resources.md).

1. Fare clic su **Avanti: verifica + crea**.

1. Nella scheda **revisione e creazione** esaminare l'ordine di dominio. Al termine, fare clic su **Crea**.

    > [!NOTE]
    > I domini del servizio app usano GoDaddy per la registrazione del dominio e DNS di Azure per ospitare i domini. Oltre alla tariffa annuale per la registrazione del dominio, vengono applicati gli addebiti per l'uso di DNS di Azure. Per altre informazioni, vedere la pagina relativa ai [prezzi del DNS di Azure](https://azure.microsoft.com/pricing/details/dns/).
    >
    >

1. Al termine della registrazione del dominio, viene visualizzato un pulsante **Vai a risorsa** . Selezionarlo per visualizzare la relativa pagina di gestione.

    ![Dominio del servizio app creato. Vai alla risorsa](./media/app-service-web-tutorial-custom-domain/deployment-complete.png)

A questo punto si è pronti per assegnare un'app del servizio app a questo dominio personalizzato.

## <a name="prepare-the-app"></a>Preparare l'app

Per eseguire il mapping di un nome DNS personalizzato a un'app Web, il [piano di servizio app](https://azure.microsoft.com/pricing/details/app-service/) dell'app Web sia un livello a pagamento (Condiviso, Basic, Standard, Premium o A consumo per Funzioni di Azure). In questo passaggio, verificare che l''app del servizio app si trovi nel piano tariffario supportato.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Passare all'app nel portale di Azure

1. Nella barra di ricerca superiore cercare e selezionare **Servizi app**.

    ![Cerca servizi app](./media/app-service-web-tutorial-custom-domain/app-services.png)

1. Selezionare il nome dell'app.

    ![Passaggio all'app di Azure nel portale](./media/app-service-web-tutorial-custom-domain/select-app.png)

    Viene visualizzata la pagina di gestione dell'app del servizio app.  

### <a name="check-the-pricing-tier"></a>Scegliere il piano tariffario

1. Nel riquadro di spostamento a sinistra della pagina dell'app scorrere fino alla sezione **Impostazioni** e selezionare **Aumenta (piano di servizio app)** .

    ![Menu di scalabilità verticale](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

1. Il livello corrente dell'app è evidenziato da un bordo blu. Verificare che l'app non sia inclusa nel livello **F1**. Il DNS personalizzato non è supportato nel livello **F1**. 

    :::image type="content" source="./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png" alt-text="Screenshot del menu di spostamento a sinistra della pagina dell'app con scalabilità verticale (piano di servizio app) selezionato.":::

1. Se il piano di servizio app non è **F1**, chiudere la pagina **Aumenta** e passare a [Acquista il dominio](#buy-an-app-service-domain).

### <a name="scale-up-the-app-service-plan"></a>Passare a un piano di servizio app di livello superiore

1. Selezionare uno dei livelli non gratuiti (**D1**, **B1**, **B2**, **B3** o uno qualsiasi dei livelli della categoria **Produzione**). Per altre opzioni, fare clic su **Visualizza opzioni aggiuntive**.

1. Fare clic su **Apply**.

    :::image type="content" source="./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png" alt-text="Screenshot dei piani tariffari del dominio personalizzato nella categoria produzione con la scheda produzione, il piano B1 e il pulsante Applica evidenziato.":::

    La visualizzazione della notifica seguente indica che l'operazione di passaggio al livello superiore è stata completata.

    ![Conferma operazione di scalabilità](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

## <a name="map-app-service-domain-to-your-app"></a>Eseguire il mapping del dominio del servizio app all'app

È facile eseguire il mapping di un nome host nel dominio del servizio app a un'app del servizio app, purché si trovi nella stessa sottoscrizione. Si esegue il mapping del dominio del servizio app o di uno dei relativi sottodomini direttamente nell'app e Azure crea automaticamente i record DNS necessari.

> [!NOTE]
> Se il dominio e l'app si trovano in sottoscrizioni diverse, eseguire il mapping del dominio del servizio app all'app esattamente come il [mapping di un dominio acquistato esternamente](app-service-web-tutorial-custom-domain.md). In questo caso, il servizio DNS di Azure è il provider di dominio esterno ed è necessario [aggiungere manualmente i record DNS necessari](#manage-custom-dns-records).
>

### <a name="map-the-domain"></a>Mappare il dominio

1. Nella finestra di spostamento a sinistra della pagina dell'app scorrere fino alla sezione **Impostazioni** e selezionare **domini personalizzati**.

    ![Screenshot che mostra il menu Domini personalizzati.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Selezionare **Aggiungi dominio personalizzato**.

    ![Screenshot che mostra l'elemento per l'aggiunta del nome host.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Digitare il dominio del servizio app (ad esempio **contoso.com**) o un sottodominio (ad esempio **www.contoso.com**) e fare clic su **convalida**.

    > [!NOTE]
    > Se è stato creato un errore di digitazione nel nome di dominio del servizio app, viene visualizzato un errore di verifica nella parte inferiore della pagina per indicare che mancano alcuni record DNS. Non è necessario aggiungere questi record manualmente per un dominio del servizio app. Assicurarsi di digitare il nome di dominio correttamente e fare di nuovo clic su **convalida** .
    >
    > ![Screenshot che mostra un errore di verifica.](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

1. Accettare il **tipo di record hostname** e fare clic su **Aggiungi dominio personalizzato**.

    ![Screenshot che mostra il pulsante Aggiungi dominio personalizzato.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

1. La visualizzazione del nuovo dominio personalizzato nella pagina **Domini personalizzati** dell'app potrebbe richiedere qualche minuto. Aggiornare il browser per visualizzare i dati più recenti.

    ![Screenshot che mostra l'aggiunta del record CNAME.](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    > [!NOTE]
    > Un'etichetta **non sicura** per il dominio personalizzato significa che non è ancora associata a un certificato TLS/SSL. In seguito a qualsiasi richiesta HTTPS inviata da un browser al dominio personalizzato si riceverà un errore o un avviso, a seconda del browser. Per aggiungere un'associazione TLS, vedere [Proteggere un nome DNS personalizzato con un'associazione TLS/SSL nel Servizio app di Azure](configure-ssl-bindings.md).
    
### <a name="test-the-custom-domain"></a>Testare il dominio personalizzato

Per testare il dominio personalizzato, accedervi nel browser.

## <a name="renew-the-domain"></a>Rinnovare il dominio

Il dominio del servizio app acquistato è valido per un anno dalla data di acquisto. Per impostazione predefinita, il dominio è configurato per il rinnovo automatico tramite l'addebito dell'importo per l'anno successivo in base al metodo di pagamento selezionato. È possibile rinnovare manualmente il nome di dominio.

Se si desidera disattivare il rinnovo automatico o se si desidera rinnovare manualmente il dominio, eseguire la procedura seguente.

1. Nella barra di ricerca cercare e selezionare domini del **servizio app**.

    ![Esplorazione del portale per app Azure domini del servizio](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. Nella sezione **Domini del servizio app** selezionare il dominio che si desidera configurare.

1. Nel riquadro di spostamento a sinistra del dominio selezionare **Rinnovo del dominio**. Per interrompere automaticamente il rinnovo del dominio, selezionare **disattivato**. L'impostazione ha effetto immediatamente.

    ![Screenshot che mostra l'opzione per rinnovare automaticamente il dominio.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-autorenew.png)

    > [!NOTE]
    > Quando si è lontani dalla pagina, ignorare l'errore "le modifiche non salvate verranno ignorate" facendo clic su **OK**.
    >

Per rinnovare manualmente il dominio, selezionare **Rinnova dominio**. Tuttavia, questo pulsante non è attivo fino a [90 giorni prima della scadenza del dominio](#when-domain-expires).

Se il rinnovo del dominio ha esito positivo, si riceverà una notifica tramite posta elettronica entro 24 ore.

## <a name="when-domain-expires"></a>Quando il dominio scade

Azure gestisce i domini del servizio app in scadenza o scaduti come indicato di seguito:

* Se il rinnovo automatico è disabilitato: 90 giorni prima della scadenza del dominio, viene inviato un messaggio di posta elettronica di notifica del rinnovo e il pulsante **rinnova dominio** viene attivato nel portale.
* Se il rinnovo automatico è abilitato: il giorno dopo la data di scadenza del dominio, Azure tenta di fatturare il rinnovo del nome di dominio.
* Se si verifica un errore durante il rinnovo automatico, ad esempio se la scheda è scaduta o se il rinnovo automatico è disabilitato e si consente la scadenza del dominio, Azure invia una notifica alla scadenza del dominio e parcheggia il nome di dominio. È possibile [rinnovare manualmente](#renew-the-domain) il dominio.
* Il giorno 4 e i 12 giorni dopo la scadenza, Azure invia messaggi di posta elettronica di notifica aggiuntivi. È possibile [rinnovare manualmente](#renew-the-domain) il dominio.
* Il 19 ° giorno dopo la scadenza, il dominio rimane in attesa, ma diventa soggetto a una tariffa di riscatto. È possibile contattare il supporto tecnico per rinnovare il nome di dominio, in base alle eventuali tariffe di rinnovo e riscatto applicabili.
* Il 25 ° giorno dopo la scadenza, Azure inserisce il dominio per l'asta a un servizio di vendita all'asta di un nome di dominio. È possibile contattare il supporto tecnico per rinnovare il nome di dominio, in base alle eventuali tariffe di rinnovo e riscatto applicabili.
* Il 30 ° giorno dopo la scadenza non sarà più possibile riscattare il dominio.

<a name="custom"></a>

## <a name="manage-custom-dns-records"></a>Gestire i record DNS personalizzati

In Azure, i record DNS per un dominio del servizio app vengono gestiti tramite [DNS di Azure](https://azure.microsoft.com/services/dns/). È possibile aggiungere, rimuovere e aggiornare i record DNS, esattamente come per un dominio acquistato esternamente. Per gestire i record DNS personalizzati:

1. Nella barra di ricerca cercare e selezionare domini del **servizio app**.

    ![Esplorazione del portale per app Azure domini del servizio](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. Nella sezione **Domini del servizio app** selezionare il dominio che si desidera configurare.

1. Dalla pagina **Panoramica** selezionare **Gestisci record DNS**.

    ![Screenshot che mostra dove accedere ai record DNS.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-dns-zone.png)

Per informazioni su come modificare i record DNS, vedere [Come gestire le zone DNS nel portale di Azure](../dns/dns-operations-dnszones-portal.md).

## <a name="cancel-purchase-delete-domain"></a>Annullare l'acquisto (elimina dominio)

Dopo aver acquistato il dominio del servizio app, si hanno a disposizione cinque giorni per annullare l'acquisto e ricevere un rimborso completo. Dopo cinque giorni, è possibile eliminare il dominio del servizio app, ma non è possibile ricevere un rimborso.

1. Nella barra di ricerca cercare e selezionare domini del **servizio app**.

    ![Esplorazione del portale per app Azure domini del servizio](./media/app-service-web-tutorial-custom-domain/view-app-service-domains.png)

1. Nella sezione **Domini del servizio app** selezionare il dominio che si desidera configurare.

1. Nel navigazione a sinistra del dominio selezionare **associazioni nome host**. Le associazioni nome host da tutti i servizi Azure sono elencate di seguito.

    ![Screenshot che mostra la pagina associazioni nome host.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostname-bindings.png)

1. Eliminare ogni binding hostname selezionando **...**  >  **Eliminare**. Dopo l'eliminazione di tutte le associazioni, selezionare **Salva**.

    <!-- ![Screenshot that shows where to delete the hostname bindings.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-delete-hostname-bindings.png) -->

1. Nella finestra di spostamento a sinistra del dominio, selezionare **Panoramica**. 

1. Se non è trascorso il periodo di cancellazione del dominio acquistato, selezionare **Annulla acquisto**. In caso contrario, viene visualizzato il pulsante **Elimina**. Per eliminare il dominio senza rimborso, selezionare **Elimina**.

    ![Screenshot che mostra dove eliminare o annullare un dominio acquistato.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-cancel.png)

1. Confermare l'operazione selezionando **Sì**.

    Al termine dell’operazione, il dominio viene rilasciato dalla sottoscrizione e può essere riacquistato dagli altri utenti. 

## <a name="direct-default-url-to-a-custom-directory"></a>Indirizzare l'URL predefinito a una directory personalizzata

Per impostazione predefinita, il servizio app indirizza le richieste Web alla directory radice del codice dell'app. Per indirizzarli a una sottodirectory, ad esempio `public` , vedere [reindirizzare a una directory personalizzata](app-service-web-tutorial-custom-domain.md#redirect-to-a-custom-directory).

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come associare un certificato TLS/SSL personalizzato al servizio app.

> [!div class="nextstepaction"]
> [Proteggere un nome DNS personalizzato con un'associazione TLS nel servizio app Azure](configure-ssl-bindings.md)
