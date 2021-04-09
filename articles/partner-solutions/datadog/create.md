---
title: Creare Datadog-soluzioni partner di Azure
description: Questo articolo descrive come usare la portale di Azure per creare un'istanza di Datadog.
ms.service: partner-services
ms.topic: quickstart
ms.date: 02/19/2021
author: tfitzmac
ms.author: tomfitz
ms.custom: references_regions
ms.openlocfilehash: 7af8b82c5da6c60527b45b6e8e292b9f067016ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101748637"
---
# <a name="quickstart-get-started-with-datadog"></a>Guida introduttiva: Introduzione a Datadog

In questa Guida introduttiva verrà creata un'istanza di Datadog. È possibile creare una nuova organizzazione Datadog o collegarsi a un'organizzazione Datadog esistente.

## <a name="pre-requisites"></a>Prerequisiti

Per configurare l'integrazione di Azure Datadog, è necessario avere l'accesso al **proprietario** nella sottoscrizione di Azure. Assicurarsi di disporre dell'accesso appropriato prima di avviare l'installazione.

Per usare la funzionalità Single Sign-On (SSO) Security Assertion Markup Language (SSO) nella risorsa Datadog, è necessario configurare un'applicazione aziendale. Per aggiungere un'applicazione aziendale, è necessario disporre di uno dei ruoli seguenti: amministratore globale, amministratore dell'applicazione cloud, amministratore dell'applicazione o proprietario dell'entità servizio.

Per configurare l'applicazione aziendale, attenersi alla procedura seguente:

1. Passare a [portale di Azure](https://portal.azure.com). Selezionare **Azure Active Directory**.
1. Nel riquadro sinistro selezionare **Applicazioni aziendali**.
1. Selezionare **nuova applicazione**.
1. In **Aggiungi dalla raccolta** cercare *Datadog*. Selezionare il risultato della ricerca, quindi selezionare **Aggiungi**.

   :::image type="content" source="media/create/datadog-azure-ad-app-gallery.png" alt-text="Applicazione Datadog in AAD Enterprise Gallery." border="true":::

1. Una volta creata l'app, passare a proprietà dal pannello laterale. Impostare **assegnazione utente obbligatoria?** su **No** e selezionare **Salva**.

   :::image type="content" source="media/create/user-assignment-required.png" alt-text="Impostare le proprietà per l'applicazione Datadog" border="true":::

1. Passare a **Single Sign-on** dal pannello laterale. Quindi selezionare **SAML**.

   :::image type="content" source="media/create/saml-sso.png" alt-text="Autenticazione SAML." border="true":::

1. Quando viene richiesto di **salvare Single Sign-on impostazioni**, selezionare **Sì** .

   :::image type="content" source="media/create/save-sso.png" alt-text="Salvare l'accesso Single Sign-on per l'app Datadog" border="true":::

1. Il programma di installazione di Single Sign-On è ora completo.

## <a name="find-offer"></a>Trova l'offerta

Usare il portale di Azure per trovare Datadog.

1. Aprire il [portale di Azure](https://portal.azure.com/) e accedere.

1. Se il **Marketplace** è stato visitato in una sessione recente, selezionare l'icona dalle opzioni disponibili. In caso contrario, cercare _Marketplace_.

    :::image type="content" source="media/create/marketplace.png" alt-text="Icona del Marketplace.":::

1. Nel Marketplace cercare **Datadog**.

1. Nella schermata panoramica piano selezionare **imposta + Sottoscrivi**.

   :::image type="content" source="media/create/datadog-app.png" alt-text="Applicazione Datadog in Azure Marketplace.":::

## <a name="create-a-datadog-resource-in-azure"></a>Creare una risorsa Datadog in Azure

Il portale Visualizza un modulo per la creazione della risorsa Datadog.

:::image type="content" source="media/create/datadog-create-resource.png" alt-text="Creare una risorsa Datadog" border="true":::

Specificare i valori seguenti.

|Proprietà | Descrizione
|:-----------|:-------- |
| Subscription | Selezionare la sottoscrizione di Azure che si vuole usare per creare la risorsa Datadog. È necessario disporre dell'accesso al proprietario. |
| Resource group | Specificare se si vuole creare un nuovo gruppo di risorse o usarne uno esistente. Un [gruppo di risorse](../../azure-resource-manager/management/overview.md#resource-groups) è un contenitore con risorse correlate per una soluzione di Azure. |
| Nome risorsa | Specificare un nome per la risorsa Datadog. Questo nome sarà il nome della nuova organizzazione Datadog durante la creazione di una nuova organizzazione Datadog. |
| Location | Selezionare Stati Uniti occidentali 2. Attualmente, gli Stati Uniti occidentali 2 sono l'unica area supportata. |
| Organizzazione Datadog | Per creare una nuova organizzazione Datadog, selezionare **nuovo**. Per eseguire il collegamento a un'organizzazione Datadog esistente, selezionare **esistente**. |
| Piano tariffario | Quando si crea una nuova organizzazione, selezionarla dall'elenco dei piani Datadog disponibili. |
| Periodo di fatturazione | Mensile. |

Se si sta eseguendo il collegamento a un'organizzazione Datadog esistente, vedere la sezione successiva. In caso contrario, selezionare **Avanti: metriche e registri** e ignorare la sezione successiva.

## <a name="link-to-existing-datadog-organization"></a>Collegamento all'organizzazione Datadog esistente

È possibile collegare la nuova risorsa Datadog in Azure a un'organizzazione Datadog esistente.

Selezionare **esistente** per organizzazione dati, quindi selezionare **collega a Datadog org**.

:::image type="content" source="media/create/link-to-existing.png" alt-text="Collegamento all'organizzazione Datadog esistente." border="true":::

Il collegamento apre una finestra di autenticazione Datadog. Accedere a Datadog.

Per impostazione predefinita, Azure collega l'organizzazione Datadog corrente alla risorsa Datadog. Se si vuole eseguire il collegamento a un'organizzazione diversa, selezionare l'organizzazione appropriata nella finestra di autenticazione, come illustrato di seguito.

:::image type="content" source="media/create/select-datadog-organization.png" alt-text="Selezionare l'organizzazione Datadog appropriata da collegare" border="true":::

## <a name="configure-metrics-and-logs"></a>Configurare metriche e log

Usare i tag delle risorse di Azure per configurare le metriche e i log da inviare a Datadog. È possibile includere o escludere le metriche e i log per le risorse specifiche.

Le regole tag per l'invio delle **metriche** sono:

- Per impostazione predefinita, le metriche vengono raccolte per tutte le risorse, ad eccezione di macchine virtuali, set di scalabilità di macchine virtuali e piani di servizio app.
- Le macchine virtuali, i set di scalabilità di macchine virtuali e i piani di servizio app con tag di *inclusione* inviano metriche a Datadog.
- Le macchine virtuali, i set di scalabilità di macchine virtuali e i piani di servizio app con tag di *esclusione* non inviano metriche a Datadog.
- Se si verifica un conflitto tra le regole di inclusione ed esclusione, l'esclusione avrà la priorità

Le regole tag per l'invio dei **log** sono:

- Per impostazione predefinita, i log vengono raccolti per tutte le risorse.
- Le risorse di Azure con tag di *inclusione* inviano i log a Datadog.
- Le risorse di Azure con tag  *Exclude* non inviano log a Datadog.
- Se si verifica un conflitto tra le regole di inclusione ed esclusione, l'esclusione avrà la priorità.

Ad esempio, la schermata seguente mostra una regola tag in cui solo le macchine virtuali, i set di scalabilità di macchine virtuali e i piani di servizio app contrassegnati come *Datadog = true* inviano le metriche a Datadog.

:::image type="content" source="media/create/config-metrics-logs.png" alt-text="Configurare i log e le metriche." border="true":::

Esistono due tipi di log che possono essere emessi da Azure a Datadog.

1. **Log a livello di sottoscrizione** : forniscono informazioni dettagliate sulle operazioni sulle risorse sul [piano di controllo](../../azure-resource-manager/management/control-plane-and-data-plane.md). Sono inclusi anche gli aggiornamenti sugli eventi di integrità del servizio. Usare il log attività per determinare l'elemento, chi e quando per qualsiasi operazione di scrittura (PUT, POST, DELETE). È disponibile un singolo log attività per ogni sottoscrizione di Azure.

1. **Log delle risorse di Azure** : fornisce informazioni dettagliate sulle operazioni eseguite su una risorsa di Azure nel [piano dati](../../azure-resource-manager/management/control-plane-and-data-plane.md). Il recupero di un segreto da un Key Vault è ad esempio un'operazione del piano dati. In alternativa, l'esecuzione di una richiesta a un database è anche un'operazione del piano dati. Il contenuto dei log delle risorse varia in base al servizio di Azure e al tipo di risorsa.

Per inviare i log a livello di sottoscrizione a Datadog, selezionare **Invia log attività di sottoscrizione**. Se questa opzione è deselezionata, nessuno dei log a livello di sottoscrizione viene inviato a Datadog.

Per inviare i log delle risorse di Azure a Datadog, selezionare **Invia log delle risorse di Azure per tutte le risorse definite**. I tipi di log delle risorse di Azure sono elencati nelle [categorie del log delle risorse di monitoraggio di Azure](../../azure-monitor/essentials/resource-logs-categories.md).  Per filtrare il set di risorse di Azure che inviano log a Datadog, usare i tag delle risorse di Azure.  

I log inviati a Datadog verranno addebitati da Azure. Per altre informazioni, vedere i [prezzi dei log della piattaforma](https://azure.microsoft.com/pricing/details/monitor/) inviati ai partner di Azure Marketplace.

Dopo aver completato la configurazione delle metriche e dei log, selezionare **Avanti: Single Sign-on**.

## <a name="configure-single-sign-on"></a>Configura accesso Single Sign-On

Se l'organizzazione USA Azure Active Directory come provider di identità, è possibile stabilire Single Sign-On dal portale di Azure a Datadog. Se l'organizzazione usa un provider di identità diverso o non si vuole stabilire Single Sign-On al momento, è possibile ignorare questa sezione.

Se si sta collegando la risorsa Datadog a un'organizzazione Datadog esistente, non è possibile configurare Single Sign-On in questo passaggio. Si configurano invece Single Sign-On dopo la creazione della risorsa Datadog. Per ulteriori informazioni, vedere [riconfigurare Single Sign-on](manage.md#reconfigure-single-sign-on).

:::image type="content" source="media/create/linking-sso.png" alt-text="Single Sign-on per il collegamento a un'organizzazione Datadog esistente." border="true":::

Per stabilire Single Sign-On tramite Azure Active Directory, selezionare la casella di controllo **abilita Single Sign-on tramite Azure Active Directory**.

Il portale di Azure recupera l'applicazione Datadog appropriata da Azure Active Directory. L'app corrisponde all'app aziendale fornita in un passaggio precedente.

Selezionare il nome dell'app Datadog.

:::image type="content" source="media/create/sso.png" alt-text="Abilitare l'accesso Single Sign-on a Datadog." border="true":::

Selezionare **Avanti: Tag**.

## <a name="add-custom-tags"></a>Aggiungi tag personalizzati

È possibile specificare tag personalizzati per la nuova risorsa Datadog. Fornire le coppie di nome e valore per i tag da applicare alla risorsa Datadog.

:::image type="content" source="media/create/tags.png" alt-text="Aggiungere tag personalizzati per la risorsa Datadog." border="true":::

Al termine dell'aggiunta di tag, selezionare **Avanti: verifica + crea**.

## <a name="review--create-datadog-resource"></a>Esaminare + creare una risorsa Datadog

Verificare le selezioni e le condizioni per l'utilizzo. Al termine della convalida selezionare **Crea**.

:::image type="content" source="media/create/review-create.png" alt-text="Esaminare e creare la risorsa Datadog." border="true":::

Azure distribuisce la risorsa Datadog.

Al termine del processo, selezionare **Vai alla risorsa** per visualizzare la risorsa Datadog.

:::image type="content" source="media/create/go-to-resource.png" alt-text="Distribuzione delle risorse Datadog." border="true":::

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Gestire la risorsa Datadog](manage.md)
