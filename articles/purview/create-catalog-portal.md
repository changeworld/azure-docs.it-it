---
title: 'Avvio rapido: Creare un account Azure Purview nel portale di Azure (anteprima)'
description: Questo argomento di avvio rapido illustra come creare un account Azure Purview e configurare le autorizzazioni per iniziare a usarlo.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: quickstart
ms.date: 10/23/2020
ms.openlocfilehash: fe83425230a157be245e3aa1ad088c32596ea5ed
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2020
ms.locfileid: "97693380"
---
# <a name="quickstart-create-an-azure-purview-account-in-the-azure-portal"></a>Avvio rapido: Creare un account Azure Purview nel portale di Azure

> [!IMPORTANT]
> Azure Purview è attualmente in anteprima. Le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) includono termini legali aggiuntivi che si applicano a funzionalità di Azure in versione beta, in anteprima o in altro modo non ancora disponibili a livello generale.

In questo argomento di avvio rapido si crea un account Azure Purview.

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Un [tenant di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant).

* L'account deve avere l'autorizzazione per creare risorse nella sottoscrizione

* Se sono impostati **Criteri di Azure** che impediscono a tutte le applicazioni di creare un **account di archiviazione** e uno **spazio dei nomi EventHub**, è necessario creare un'eccezione dei criteri usando un tag, che può essere immesso durante il processo di creazione di un account Purview. Il motivo principale è che per ogni account Purview creato occorre creare un gruppo di risorse gestito e, all'interno di tale gruppo, un account di archiviazione e uno spazio dei nomi EventHub.

    > [!important]
    > Non è necessario seguire questo passaggio se il servizio Criteri di Azure non è installato o se non è presente un criterio di Azure che impedisce la creazione dell'**account di archiviazione** e dello **spazio dei nomi EventHub**.

    1. Passare al portale di Azure e cercare **Criteri**.
    1. Seguire la procedura in [Creare una definizione di criteri personalizzata](https://docs.microsoft.com/azure/governance/policy/tutorials/create-custom-policy-definition) o modificare i criteri esistenti aggiungendo due eccezioni con l'operatore `not` e il tag `resourceBypass`:

        ```json
        {
          "mode": "All",
          "policyRule": {
            "if": {
              "anyOf": [
              {
                "allOf": [
                {
                  "field": "type",
                  "equals": "Microsoft.Storage/storageAccounts"
                },
                {
                  "not": {
                    "field": "tags['<resourceBypass>']",
                    "exists": true
                  }
                }]
              },
              {
                "allOf": [
                {
                  "field": "type",
                  "equals": "Microsoft.EventHub/namespaces"
                },
                {
                  "not": {
                    "field": "tags['<resourceBypass>']",
                    "exists": true
                  }
                }]
              }]
            },
            "then": {
              "effect": "deny"
            }
          },
          "parameters": {}
        }
        ```
        
        > [!Note]
        > È possibile usare qualsiasi tag oltre a `resourceBypass` e spetta all'utente definirne il valore durante la creazione dell'account Purview nei passaggi successivi, purché i criteri possano rilevare il tag.

        :::image type="content" source="./media/create-catalog-portal/policy-definition.png" alt-text="Screenshot che illustra come creare una definizione di criteri.":::

    1. [Creare un'assegnazione di criteri](https://docs.microsoft.com/azure/governance/policy/assign-policy-portal) usando i criteri personalizzati creati.

        [ ![Screenshot che illustra come creare un'assegnazione di criteri](./media/create-catalog-portal/policy-assignment.png)](./media/create-catalog-portal/policy-assignment.png#lightbox)

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com) con il proprio account Azure.

## <a name="configure-your-subscription"></a>Configurare la sottoscrizione

Se necessario, seguire questa procedura per configurare la sottoscrizione in modo da consentire l'esecuzione di Azure Purview nella sottoscrizione:

   1. Nel portale di Azure cercare e selezionare **Sottoscrizioni**.

   1. Nell'elenco delle sottoscrizioni selezionare quella che si vuole usare. È necessaria l'autorizzazione di accesso amministrativo per la sottoscrizione.

      :::image type="content" source="./media/create-catalog-portal/select-subscription.png" alt-text="Screenshot che illustra come selezionare una sottoscrizione nel portale di Azure.":::

   1. Selezionare **Provider di risorse** per la propria sottoscrizione. Nel riquadro **Provider di risorse** cercare e registrare tutti e tre i provider di risorse: 
       1. **Microsoft.Purview**
       1. **Microsoft.Storage**
       1. **Microsoft.EventHub** 
      
      Se non sono registrati, registrarli selezionando **Registra**.

      :::image type="content" source="./media/create-catalog-portal/register-purview-resource-provider.png" alt-text="Screenshot che illustra come registrare il provider di risorse Microsoft.Purview nel portale di Azure.":::

## <a name="create-an-azure-purview-account-instance"></a>Creare un'istanza dell'account Azure Purview

1. Passare alla pagina **Account Purview** nel portale di Azure e quindi selezionare **Aggiungi** per creare un nuovo account Azure Purview. In alternativa, è possibile cercare **Account Purview** nel marketplace e selezionare **Crea**. Si noti che è possibile aggiungere un solo account Azure Purview per volta.

   :::image type="content" source="./media/create-catalog-portal/add-purview-instance.png" alt-text="Screenshot che illustra come creare un'istanza dell'account Azure Purview nel portale di Azure.":::

1. Nella scheda **Informazioni di base** eseguire le operazioni seguenti:
    1. Selezionare un **gruppo di risorse**.
    1. Immettere un **nome di account Purview** per il catalogo. Spazi e simboli non sono consentiti.
    1. Selezionare una **località**, quindi selezionare **Avanti: Configurazione**.
1. Nella scheda **Configurazione** selezionare le **dimensioni della piattaforma** desiderate. I valori consentiti sono 4 unità di capacità e 16 unità di capacità. Selezionare **Avanti: Tag**.
1. Nella scheda **Tag** è possibile aggiungere uno o più tag, se si vuole. Questi tag possono essere usati solo nel portale di Azure, non in Azure Purview. 

    > [!Note] 
    > Se sono impostati **Criteri di Azure** ed è necessario aggiungere un'eccezione come illustrato in **Prerequisiti**, occorre aggiungere il tag corretto. Ad esempio, è possibile aggiungere il tag `resourceBypass`: :::image type="content" source="./media/create-catalog-portal/add-purview-tag.png" alt-text="Aggiunta di tag all'account Purview.":::

1. Selezionare **Rivedi e crea** e quindi selezionare **Crea**. Il completamento della creazione richiede alcuni minuti. L'istanza dell'account Azure Purview appena creata viene visualizzata nell'elenco nella pagina **Account Purview**.
1. Al termine del provisioning del nuovo account, selezionare **Vai alla risorsa**.

    > [!Note]
    > Se il provisioning non riesce con stato `Conflict`, significa che sono impostati criteri di Azure che impediscono a Purview di creare un **account di archiviazione** e uno **spazio dei nomi EventHub**. È necessario eseguire la procedura descritta in **Prerequisiti** per aggiungere eccezioni.
    > :::image type="content" source="./media/create-catalog-portal/purview-conflict-error.png" alt-text="Messaggio di errore che indica un conflitto di Purview":::

1. Selezionare **Launch purview account** (Avvia account Purview).

   :::image type="content" source="./media/use-purview-studio/launch-from-portal.png" alt-text="Screenshot dell'opzione da selezionare per avviare il catalogo dell'account Azure Purview.":::

## <a name="add-a-security-principal-to-a-data-plane-role"></a>Aggiungere un'entità di sicurezza a un ruolo del piano dati

Per consentire al team di iniziare a usare Azure Purview, è necessario aggiungere una o più entità di sicurezza a uno dei ruoli dei piano dati predefiniti: **Lettore dati di Purview**, **Curatore dei dati di Purview** o **Amministratore delle origini dati di Purview**. Per altre informazioni su Azure Purview Data Catalog, vedere [Autorizzazioni del catalogo](catalog-permissions.md).

Per aggiungere un'entità di sicurezza al ruolo del piano dati **Lettore dati di Purview** in un account Azure Purview:

1. Passare alla pagina [**Account Purview**](https://aka.ms/purviewportal) nel portale di Azure.

1. Selezionare l'account Azure Purview da modificare.

1. Nella pagina **Account Purview** selezionare la scheda **Controllo di accesso (IAM)**

1. Fare clic su **+ Aggiungi**

Se dopo avere fatto clic su Aggiungi vengono visualizzate due opzioni entrambe disabilitate, significa che non si hanno le autorizzazioni necessarie per aggiungere utenti a un ruolo del piano dati nell'account Azure Purview. Occorre trovare un utente con il ruolo di Proprietario o di Amministratore accessi utente o con l'autorità di assegnazione di ruolo nell'account Azure Purview. È possibile cercare le persone corrette selezionando la scheda **Assegnazioni di ruolo** e quindi scorrendo verso il basso per cercare un utente con il ruolo Proprietario o Amministratore accessi utente e contattando tale utente.

1. Selezionare **Aggiungi un'assegnazione di ruolo**.

1. Come tipo di ruolo selezionare **Curatore dei dati di Purview** o **Amministratore delle origini dati di Purview** in base all'entità di sicurezza per cui verrà usato (per altre informazioni, vedere [Autorizzazioni per il catalogo](catalog-permissions.md) e [Oggetti applicazione e oggetti entità servizio in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)).

1. In **Assegna accesso a** lasciare l'impostazione predefinita **Utente, gruppo o entità servizio**.

1. In **Seleziona** immettere il nome dell'utente, del gruppo di Azure Active Directory o dell'entità servizio che si vuole assegnare e quindi fare clic sul nome corrispondente nel riquadro dei risultati.

1. Fare clic su **Save**.

## <a name="clean-up-resources"></a>Pulire le risorse

Se questo account Azure Purview non è più necessario, eliminarlo seguendo questa procedura:

1. Passare alla pagina **Account Purview** nel portale di Azure.

2. Selezionare l'account Azure Purview creato all'inizio di questo avvio rapido. Selezionare **Elimina**, immettere il nome dell'account e quindi selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come creare un account Azure Purview.

Passare all'articolo successivo per informazioni su come consentire agli utenti di accedere all'account Azure Purview. 

> [!div class="nextstepaction"]
> [Aggiungere utenti all'account Azure Purview](catalog-permissions.md)
