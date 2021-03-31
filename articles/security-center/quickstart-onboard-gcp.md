---
title: Connettere l'account GCP al Centro sicurezza di Azure
description: Monitoraggio delle risorse di GCP dal Centro sicurezza di Azure
author: memildin
ms.author: memildin
ms.date: 02/08/2021
ms.topic: quickstart
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 94c7a800fc551faf6650b8e30fe7c2188f7d2dbb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100008384"
---
#  <a name="connect-your-gcp-accounts-to-azure-security-center"></a>Connettere gli account GCP al Centro sicurezza di Azure

I carichi di lavoro cloud si estendono in genere su più piattaforme cloud, quindi anche i servizi di sicurezza cloud devono adottare lo stesso approccio.

Il Centro sicurezza di Azure protegge i carichi di lavoro in Azure, Amazon Web Services (AWS) e Google Cloud Platform (GCP).

Onboarding degli account GCP nel centro sicurezza, integra il comando di sicurezza di GCP e il Centro sicurezza di Azure. Il Centro sicurezza assicura quindi visibilità e protezione in entrambi gli ambienti cloud per offrire:

- Rilevamento degli errori di configurazione della sicurezza
- Un'unica visualizzazione che mostra le raccomandazioni del Centro sicurezza e i risultati di GCP Security Command Center
- Incorporamento delle risorse di GCP nei calcoli del punteggio di sicurezza del Centro sicurezza
- Integrazione delle raccomandazioni di GCP Security Command Center basate sullo standard CIS nel dashboard di conformità alle normative del Centro sicurezza

Lo screenshot seguente mostra i progetti GCP visualizzati nel dashboard di panoramica del Centro sicurezza.

:::image type="content" source="./media/quickstart-onboard-gcp/gcp-account-in-overview.png" alt-text="3 progetti GCP elencati nel dashboard di panoramica del Centro sicurezza" lightbox="./media/quickstart-onboard-gcp/gcp-account-in-overview.png":::


## <a name="availability"></a>Disponibilità

|Aspetto|Dettagli|
|----|:----|
|Stato della versione:|Disponibilità generale (GA)|
|Prezzi:|Richiede [Azure Defender per server](defender-for-servers-introduction.md)|
|Autorizzazioni e ruoli obbligatori:|**Proprietario** o **Collaboratore** nella sottoscrizione di Azure rilevante|
|Cloud:|![Sì](./media/icons/yes-icon.png) Cloud commerciali<br>![No](./media/icons/no-icon.png) Cloud nazionali/sovrani (US Gov, governo cinese, altri governi)|
|||

## <a name="connect-your-gcp-account"></a>Connettere l'account GCP

Creare un connettore per ogni organizzazione che si vuole monitorare dal centro sicurezza.

Quando si connettono gli account GCP a specifiche sottoscrizioni di Azure, considerare la [gerarchia di risorse di Google Cloud](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy#resource-hierarchy-detail) e le linee guida seguenti:

- È possibile connettere gli account GCP a ASC a livello di *organizzazione*
- È possibile connettere più organizzazioni a una sottoscrizione di Azure
- È possibile connettere più organizzazioni a più sottoscrizioni di Azure
- Quando si connette un'organizzazione, tutti i *progetti* all'interno dell'organizzazione vengono aggiunti al centro sicurezza

Seguire la procedura seguente per creare il connettore cloud GCP. 

### <a name="step-1-set-up-gcp-security-command-center-with-security-health-analytics"></a>Passaggio 1. Configurare GCP Security Command Center con Security Health Analytics

Per tutti i progetti GCP nell'organizzazione occorre anche:

1. Configurare **GCP Security Command Center** seguendo [queste istruzioni della documentazione di GCP](https://cloud.google.com/security-command-center/docs/quickstart-scc-setup).
1. Abilitare **Security Health Analytics** seguendo [queste istruzioni della documentazione di GCP](https://cloud.google.com/security-command-center/docs/how-to-use-security-health-analytics).
1. Verificare che sia presente un flusso di dati verso Security Command Center.

Le istruzioni per la connessione dell'ambiente GCP per la configurazione della sicurezza seguono le raccomandazioni di Google per l'utilizzo della sicurezza. L'integrazione sfrutta Google Security Command Center e utilizzerà risorse aggiuntive che potrebbero influire sulla fatturazione.

Quando si abilita Security Health Analytics per la prima volta, per la disponibilità dei dati potrebbe essere necessario attendere alcune ore.


### <a name="step-2-enable-gcp-security-command-center-api"></a>Passaggio 2. Abilitare l'API di GCP Security Command Center

1. Dalla **libreria API della console Cloud** di Google, selezionare ogni progetto dell'organizzazione che si vuole connettere al centro sicurezza di Azure.
1. Nella libreria di API trovare e selezionare l'**API Security Command Center**.
1. Nella pagina dell'API selezionare **ABILITA**.

Altre informazioni sull'API [Security Command Center](https://cloud.google.com/security-command-center/docs/reference/rest/).


### <a name="step-3-create-a-dedicated-service-account-for-the-security-configuration-integration"></a>Passaggio 3. Creare un account del servizio dedicato per l'integrazione della configurazione della sicurezza

1. Nella **console di GCP** selezionare un progetto dall'organizzazione in cui si sta creando l'account del servizio richiesto. 

    > [!NOTE]
    > Quando questo account del servizio viene aggiunto a livello di organizzazione, verrà usato per accedere ai dati raccolti dal centro comandi di sicurezza da tutti gli altri progetti abilitati nell'organizzazione. 

1. In **Menu di navigazione**, nelle opzioni di **IAM e amministratore**, selezionare **Account di servizio**.
1. Selezionare **CREA ACCOUNT DI SERVIZIO**.
1. Immettere un nome per l'account e selezionare **Crea**.
1. In **Ruolo** selezionare **Visualizzatore amministratore Centro sicurezza** e quindi selezionare **Continua**.
1. La sezione **Concedi agli utenti l'accesso a questo account di servizio** è facoltativa. Selezionare **Fine**.
1. Copiare il valore dell'**indirizzo di posta elettronica** dell'account del servizio creato e salvarlo per poterlo usare successivamente.
1. In **Menu di navigazione**, nelle opzioni di **IAM e amministratore**, selezionare **IAM**.
    1. Passare al livello di organizzazione.
    1. Selezionare **AGGIUNGI**.
    1. Nel campo **Nuovi membri** incollare il valore dell'**indirizzo di posta elettronica** copiato in precedenza.
    1. Specificare il ruolo come **Visualizzatore di amministrazione del Centro sicurezza** e quindi selezionare **Salva**.
        :::image type="content" source="./media/quickstart-onboard-gcp/iam-settings-gcp-permissions-admin-viewer.png" alt-text="Impostazione delle autorizzazioni di GCP pertinenti":::


### <a name="step-4-create-a-private-key-for-the-dedicated-service-account"></a>Passaggio 4. Creare una chiave privata per l'account del servizio dedicato
1. Passare al livello di progetto.
1. In **Menu di navigazione**, nelle opzioni di **IAM e amministratore**, selezionare **Account di servizio**.
1. Aprire l'account del servizio dedicato e selezionare Modifica.
1. Nella sezione **Chiavi** selezionare **AGGIUNGI CHIAVE** e quindi **Crea nuova chiave**.
1. Nella schermata Crea chiave privata selezionare **JSON** e quindi selezionare **CREA**.
1. Salvare il file JSON per usarlo in seguito.


### <a name="step-5-connect-gcp-to-security-center"></a>Passaggio 5. Connettere GCP al Centro sicurezza
1. Nel menu del Centro sicurezza selezionare **Connettori cloud**.
1. Selezionare Aggiungi un account GCP.
1. Nella pagina di onboarding eseguire le operazioni seguenti e quindi selezionare **Avanti**.
    1. Convalidare la sottoscrizione scelta.
    1. Nel campo **Nome visualizzato** immettere un nome visualizzato per il connettore.
    1. Nel campo **ID organizzazione** immettere l'ID dell'organizzazione. Se non si conosce l'ID, vedere [Creating and managing organizations](https://cloud.google.com/resource-manager/docs/creating-managing-organization) (Creazione e gestione di organizzazioni).
    1. Nella casella **File chiave privata** passare al file JSON scaricato in [Passaggio 4. Creare una chiave privata per l'account del servizio dedicato](#step-4-create-a-private-key-for-the-dedicated-service-account).


### <a name="step-6-confirmation"></a>Passaggio 6. Conferma

Dopo aver creato correttamente il connettore e aver configurato GCP Security Command Center:

- Gli standard CIS per GCP verranno visualizzati nel dashboard di conformità alle normative del Centro sicurezza.
- Le raccomandazioni sulla sicurezza per le risorse di GCP verranno visualizzate nel portale del Centro sicurezza e nel dashboard di conformità alle normative 5-10 minuti dopo il completamento dell'onboarding:   :::image type="content" source="./media/quickstart-onboard-gcp/gcp-resources-in-recommendations.png" alt-text="Risorse di GCP e raccomandazioni nella pagina delle raccomandazioni del Centro sicurezza":::


## <a name="monitoring-your-gcp-resources"></a>Monitoraggio delle risorse di GCP

Come illustrato sopra, la pagina delle raccomandazioni sulla sicurezza del Centro sicurezza di Azure mostra le risorse di GCP insieme alle risorse di Azure e AWS per offrire una visualizzazione effettiva per più cloud.

Per visualizzare tutte le raccomandazioni attive per le risorse in base al tipo di risorsa, usare la pagina di inventario delle risorse del Centro sicurezza per applicare un filtro basato sul tipo di risorsa di GCP a cui si è interessati:

:::image type="content" source="./media/quickstart-onboard-gcp/gcp-resource-types-in-inventory.png" alt-text="Filtro basato sul tipo di risorsa della pagina di inventario delle risorse che mostra le opzioni di GCP"::: 


## <a name="faq-for-connecting-gcp-accounts-to-azure-security-center"></a>Domande frequenti per la connessione di account GCP al centro sicurezza di Azure

### <a name="can-i-connect-multiple-gcp-organizations-to-security-center"></a>È possibile connettere più organizzazioni GCP al centro sicurezza?
Sì. Il connettore GCP del Centro sicurezza connette le risorse di Google Cloud a livello di *organizzazione* . 

Creare un connettore per ogni organizzazione GCP che si vuole monitorare dal centro sicurezza. Quando si connette un'organizzazione, tutti i progetti all'interno dell'organizzazione vengono aggiunti al centro sicurezza.

Informazioni sulla gerarchia di risorse di Google Cloud nella [documentazione online di Google](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy).


### <a name="is-there-an-api-for-connecting-my-gcp-resources-to-security-center"></a>Esiste un'API per connettere le risorse di GCP al centro sicurezza?
Sì. Per creare, modificare o eliminare i connettori cloud del Centro sicurezza con un'API REST, vedere i dettagli dell' [API dei connettori](/rest/api/securitycenter/connectors).

## <a name="next-steps"></a>Passaggi successivi

La connessione dell'account GCP è inclusa nell'esperienza per più cloud disponibile nel Centro sicurezza di Azure. Per informazioni correlate, vedere la pagina seguente:

- [Connettere gli account AWS a Centro sicurezza di Azure](quickstart-onboard-aws.md)
- [Gerarchia di risorse di Google Cloud](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy): informazioni sulla gerarchia di risorse di Google Cloud nella documentazione online di Google