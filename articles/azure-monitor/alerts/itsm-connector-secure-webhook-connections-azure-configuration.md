---
title: IT Service Management Connector-esportazione sicura in monitoraggio di Azure-configurazioni di Azure
description: Questo articolo illustra come configurare Azure per connettere i prodotti/servizi ITSM con esportazione sicura in monitoraggio di Azure per monitorare e gestire centralmente gli elementi di lavoro ITSM.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 01/03/2021
ms.openlocfilehash: 62167b8fd72c660e50378a9492528aa5b70bbee7
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100620837"
---
# <a name="configure-azure-to-connect-itsm-tools-using-secure-export"></a>Configurare Azure per la connessione di strumenti ITSM tramite l'esportazione sicura

Questo articolo fornisce informazioni su come configurare Azure per usare "esportazione sicura".
Per usare "esportazione sicura", seguire questa procedura:

1. [Registrare l'app con Azure AD.](./itsm-connector-secure-webhook-connections-azure-configuration.md#register-with-azure-active-directory)
1. [Definire l'entità servizio.](./itsm-connector-secure-webhook-connections-azure-configuration.md#define-service-principal)
1. [Creare un gruppo di azione di Webhook sicuro.](./itsm-connector-secure-webhook-connections-azure-configuration.md#create-a-secure-webhook-action-group)
1. Configurare l'ambiente partner.
    L'esportazione protetta supporta le connessioni con gli strumenti di ITSM seguenti:
    * [ServiceNow](./itsmc-secure-webhook-connections-servicenow.md)
    * [Elica BMC](./itsmc-secure-webhook-connections-bmc.md)

## <a name="register-with-azure-active-directory"></a>Registra con Azure Active Directory

Per registrare l'applicazione con Azure AD, attenersi alla procedura seguente:

1. Eseguire la procedura descritta in [registrare un'applicazione con la piattaforma di identità Microsoft](../../active-directory/develop/quickstart-register-app.md).
2. In Azure AD selezionare **Expose Application**.
3. Selezionare **imposta** per **URI ID applicazione**.

   [![Screenshot dell'opzione per l'impostazione di U R I dell'applicazione i D.](media/itsm-connector-secure-webhook-connections-azure-configuration/azure-ad.png)](media/itsm-connector-secure-webhook-connections-azure-configuration/azure-ad-expand.png#lightbox)
4. Selezionare **Salva**.

## <a name="define-service-principal"></a>Definire l'entità servizio

Il servizio del gruppo di azioni è un'applicazione di prima entità, quindi è autorizzato ad acquisire i token di autenticazione dall'applicazione AAD per l'autenticazione con il servizio.
Come passaggio facoltativo, è possibile definire il ruolo applicazione nel manifesto dell'app creata, che consente di limitare ulteriormente l'accesso, in modo che solo determinate applicazioni con quel ruolo specifico possano inviare messaggi. Questo ruolo deve essere quindi assegnato all'entità servizio del gruppo di azioni (sono necessari i privilegi di amministratore del tenant).

Questo passaggio può essere eseguito tramite gli stessi [comandi di PowerShell](../alerts/action-groups.md#secure-webhook-powershell-script).

## <a name="create-a-secure-webhook-action-group"></a>Creare un gruppo di azione di Webhook sicuro

Dopo che l'applicazione è stata registrata con Azure AD, è possibile creare elementi di lavoro nello strumento ITSM in base agli avvisi di Azure, usando l'azione webhook sicura nei gruppi di azioni.

I gruppi di azioni forniscono un metodo modulare e riutilizzabile per attivare le azioni per gli avvisi di Azure. È possibile usare i gruppi di azioni con avvisi metrica, avvisi del log attività e avvisi di Azure Log Analytics nel portale di Azure.
Per altre informazioni sui gruppi di azione, vedere [Creare e gestire gruppi di azione nel portale di Azure](../alerts/action-groups.md).

Per aggiungere un webhook a un'azione, seguire queste istruzioni per il webhook sicuro:

1. Nel [portale di Azure](https://portal.azure.com/) cercare e selezionare **Monitoraggio**. Il riquadro **Monitoraggio** consolida tutte le impostazioni e i dati di monitoraggio in una vista.
2. Selezionare **avvisi**  >  **Gestisci azioni**.
3. Selezionare [Aggiungi gruppo di azione](../alerts/action-groups.md#create-an-action-group-by-using-the-azure-portal) e compilare i campi.
4. Immettere un nome nella casella **Nome gruppo di azione** e un nome nella casella **Nome breve gruppo di azione**. Il nome breve viene usato al posto del nome completo di un gruppo di azione quando le notifiche vengono inviate usando questo gruppo.
5. Selezionare il **webhook protetto**.
6. Selezionare i dettagli seguenti:
   1. Consente di selezionare l'ID oggetto dell'istanza di Azure Active Directory registrata.
   2. Per l'URI, incollare l'URL del webhook copiato dall' [ambiente dello strumento ITSM](#configure-the-itsm-tool-environment).
   3. Impostare **Abilita lo schema di avviso comune** su **Sì**. 

   La figura seguente illustra la configurazione di un'azione di Webhook sicura di esempio:

   ![Screenshot che mostra un'azione webhook sicura.](media/itsm-connector-secure-webhook-connections-azure-configuration/secure-webhook.png)

## <a name="configure-the-itsm-tool-environment"></a>Configurare l'ambiente dello strumento ITSM

La configurazione contiene due passaggi:

1. Ottenere l'URI per la definizione di esportazione protetta.
2. Definizioni in base al flusso dello strumento ITSM.

## <a name="next-steps"></a>Passaggi successivi

* [Configurazione esportazione protetta ServiceNow](./itsmc-secure-webhook-connections-servicenow.md)
* [Configurazione esportazione sicura BMC](./itsmc-secure-webhook-connections-bmc.md)
