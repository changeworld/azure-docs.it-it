---
title: Configurare QnA Maker Service-QnA Maker
description: Questo documento descrive le configurazioni avanzate per le risorse QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 02/18/2021
ms.openlocfilehash: 3c6f75eafad51c99f60b78ce49862d2488d5926f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102220159"
---
# <a name="configure-qna-maker-resources"></a>Configurare le risorse di QnA Maker

L'utente può configurare QnA Maker per l'uso di una risorsa di ricerca cognitiva diversa. Possono anche configurare le impostazioni del servizio app se usano QnA Maker GA.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/v1)

### <a name="configure-qna-maker-to-use-different-cognitive-search-resource"></a>Configurare QnA Maker per l'uso di risorse ricerca cognitiva diverse

Se si crea un servizio QnA e le relative dipendenze (ad esempio la ricerca) tramite il portale, viene creato un servizio di ricerca e collegato al servizio QnA Maker. Dopo aver creato queste risorse, è possibile aggiornare l'impostazione del servizio app per usare un servizio di ricerca precedentemente esistente e rimuovere quello appena creato.

La risorsa del **servizio app** di QnA Maker usa la risorsa ricerca cognitiva. Per modificare la risorsa ricerca cognitiva utilizzata da QnA Maker, è necessario modificare l'impostazione nella portale di Azure.

1. Ottenere la **chiave di amministrazione** e il **nome** della risorsa ricerca cognitiva che si desidera QnA Maker usare.

1. Accedere al [portale di Azure](https://portal.azure.com) e trovare il **servizio app** associato alla risorsa QnA Maker. Entrambe le con hanno lo stesso nome.

1. Selezionare **Settings (impostazioni**) e quindi **Configuration (configurazione**). Vengono visualizzate tutte le impostazioni esistenti per il servizio app del QnA Maker.

    > [!div class="mx-imgBorder"]
    > ![Screenshot del portale di Azure che mostra le impostazioni di configurazione del servizio app](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-app-service-configuration.png)

1. Modificare i valori per le chiavi seguenti:

    * **AzureSearchAdminKey**
    * **AzureSearchName**

1. Per usare le nuove impostazioni, è necessario riavviare il servizio app. Selezionare **Panoramica**, quindi fare clic su **Riavvia**.

    > [!div class="mx-imgBorder"]
    > ![Screenshot della portale di Azure il riavvio del servizio app dopo la modifica delle impostazioni di configurazione](../media/qnamaker-how-to-upgrade-qnamaker/screenshot-azure-portal-restart-app-service.png)

Se si crea un servizio QnA tramite modelli di Azure Resource Manager, è possibile creare tutte le risorse e controllare la creazione del servizio app per l'uso di un servizio di ricerca esistente.

Altre informazioni su come configurare le [impostazioni dell'applicazione](../../../app-service/configure-common.md#configure-app-settings)del servizio app.

### <a name="get-the-latest-runtime-updates"></a>Ottenere gli aggiornamenti più recenti del runtime

Il runtime di QnAMaker fa parte dell'istanza del servizio app Azure distribuita quando si [Crea un servizio QnAMaker](./set-up-qnamaker-service-azure.md) nel portale di Azure. Vengono applicati aggiornamenti periodici al runtime. L'istanza del servizio app QnA Maker è in modalità di aggiornamento automatico dopo la versione dell'estensione del sito aprile 2019 (versione 5 +). Questo aggiornamento è stato progettato per gestire il tempo di inattività durante gli aggiornamenti.

È possibile controllare la versione corrente in https://www.qnamaker.ai/UserSettings . Se la versione è precedente alla versione 5. x, è necessario riavviare il servizio app per applicare gli aggiornamenti più recenti:

1. Passare al servizio QnAMaker (gruppo di risorse) nell' [portale di Azure](https://portal.azure.com).

    > [!div class="mx-imgBorder"]
    > ![Gruppo di risorse di Azure per QnA Maker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Selezionare l'istanza del servizio app e aprire la sezione **Panoramica** .

    > [!div class="mx-imgBorder"]
    > ![Istanza del servizio app QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)


1. Riavviare il servizio app. Il processo di aggiornamento dovrebbe terminare tra qualche secondo. Eventuali applicazioni dipendenti o bot che usano questo servizio QnAMaker non saranno disponibili per gli utenti finali durante questo periodo di riavvio.

    ![Riavvio dell'istanza del servizio app QnAMaker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

### <a name="configure-app-service-idle-setting-to-avoid-timeout"></a>Configurare l'impostazione di inattività del servizio app per evitare il timeout

Il servizio app, che serve il runtime di stima QnA Maker per una Knowledge base pubblicata, ha una configurazione del timeout di inattività, che per impostazione predefinita si timeout automaticamente se il servizio è inattivo. Per QnA Maker, ciò significa che l'API generateAnswer del runtime di stima scade occasionalmente dopo i periodi di assenza di traffico.

Per poter caricare l'app dell'endpoint di stima anche quando non è disponibile traffico, impostare inattivo su Always on.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Cercare e selezionare il servizio app della risorsa QnA Maker. Avrà lo stesso nome della risorsa QnA Maker ma avrà un **tipo** diverso di servizio app.
1. Individuare **le impostazioni** e quindi selezionare **configurazione**.
1. Nel riquadro Configurazione selezionare **Impostazioni generali**, quindi trova **Always on** e selezionare **on** come valore.

    > [!div class="mx-imgBorder"]
    > ![Nel riquadro Configurazione selezionare * * Impostazioni generali * *, quindi trova * * always on * * e selezionare * * on * * come valore.](../media/qnamaker-how-to-upgrade-qnamaker/configure-app-service-idle-timeout.png)

1. Selezionare **Save (Salva** ) per salvare la configurazione.
1. Viene chiesto se si vuole riavviare l'app per usare la nuova impostazione. Selezionare **Continua**.

Altre informazioni su come configurare le [Impostazioni generali](../../../app-service/configure-common.md#configure-general-settings)del servizio app.

### <a name="business-continuity-with-traffic-manager"></a>Continuità aziendale con gestione traffico

L'obiettivo primario del piano di continuità aziendale è creare un endpoint di Knowledge Base resiliente che garantisca l'assenza di tempi di inattività per il bot o l'applicazione che ne fa uso.

> [!div class="mx-imgBorder"]
> ![Piano di continuità aziendale QnA Maker](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

L'idea generale è la seguente:

1. Impostare due [servizi QnA Maker](set-up-qnamaker-service-azure.md) paralleli in [aree associate di Azure](../../../best-practices-availability-paired-regions.md).

1. [Eseguire il backup](../../../app-service/manage-backup.md) del servizio app QnA Maker primario e [ripristinarlo](../../../app-service/web-sites-restore.md) nell'installazione secondaria. In questo modo, entrambe le configurazioni funzioneranno con lo stesso nome host e le stesse chiavi.

1. Mantieni sincronizzati gli indici di ricerca di Azure primaria e secondaria. Usare l'esempio GitHub [qui](https://github.com/pchoudhari/QnAMakerBackupRestore) per informazioni su come eseguire il backup e ripristinare gli indici di Azure.

1. Eseguire il backup di Application Insights usando l'[esportazione continua](../../../azure-monitor/app/export-telemetry.md).

1. Una volta impostati gli stack primario e secondario, usare [Gestione traffico](../../../traffic-manager/traffic-manager-overview.md) per configurare i due endpoint e impostare un metodo di routing.

1. È necessario creare un Transport Layer Security (TLS), noto in precedenza come Secure Sockets Layer (SSL), certificato per l'endpoint di gestione traffico. [Associare il certificato TLS/SSL](../../../app-service/configure-ssl-bindings.md) nei servizi app.

1. Usare infine l'endpoint di Gestione traffico nel bot o nell'app.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/v2)

### <a name="configure-qna-maker-managed-preview-service-to-use-different-cognitive-search-resource"></a>Configurare QnA Maker servizio gestito (anteprima) per l'uso di risorse ricerca cognitiva diverse

Se si crea un servizio QnA gestito (anteprima) e le relative dipendenze (ad esempio la ricerca) tramite il portale, viene creato un servizio di ricerca e collegato al servizio QnA Maker gestito (anteprima). Dopo aver creato queste risorse, è possibile aggiornare il servizio di ricerca nella scheda **configurazione** .

1. Passare al servizio QnA Maker gestito (anteprima) nel portale di Azure.

1. Selezionare **configurazione** e selezionare il servizio ricerca cognitiva di Azure che si vuole collegare al servizio QnA Maker gestito (anteprima).

    ![Screenshot della pagina di configurazione QnA Maker gestita (anteprima)](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-configuration.png)

1. Fare clic su **Salva**.

> [!NOTE]
> Se si modifica il servizio ricerca di Azure associato a QnA Maker, si perderà l'accesso a tutte le Knowledge base già presenti. Assicurarsi di esportare le Knowledge base esistenti prima di modificare il servizio ricerca di Azure.

---
