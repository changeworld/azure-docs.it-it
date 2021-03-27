---
ms.openlocfilehash: 82e475c97aa2c68a33a48b04fe3e45fb13728b88
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2021
ms.locfileid: "105629360"
---

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Una risorsa attiva di Servizi di comunicazione.](../../create-communication-resource.md)

## <a name="get-a-phone-number"></a>Ottenere un numero di telefono

Per iniziare il provisioning dei numeri, passare alla risorsa di Servizi di comunicazione nel [portale di Azure](https://portal.azure.com).

:::image type="content" source="../../media/manage-phone-azure-portal-start.png" alt-text="Screenshot che mostra la pagina principale della risorsa di Servizi di comunicazione.":::

### <a name="search-for-available-phone-numbers"></a>Cerca i numeri di telefono disponibili

Passare al pannello **Numeri di telefono** nel menu della risorsa.

:::image type="content" source="../../media/manage-phone-azure-portal-phone-page.png" alt-text="Screenshot che mostra la pagina di numeri di telefono della risorsa di Servizi di comunicazione.":::

Premere il pulsante **Ottieni** per avviare la procedura guidata. La procedura guidata del pannello **Numeri di telefono** include una serie di domande che aiutano a scegliere il numero di telefono più adatto allo specifico scenario. 

Sarà prima di tutto necessario scegliere il **paese/area geografica** in cui effettuare il provisioning del numero di telefono. Dopo aver selezionato il paese/area geografica, è necessario selezionare il **Caso d'uso** più adatto alle esigenze. 

:::image type="content" source="../../media/manage-phone-azure-portal-get-numbers.png" alt-text="Screenshot che mostra la visualizzazione per ottenere i numeri di telefono.":::

### <a name="select-your-phone-number-features"></a>Selezionare le funzionalità dei numeri di telefono

La configurazione del numero di telefono è suddivisa in due passaggi: 

1. La selezione del [tipo di numero](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)
2. Selezione delle funzionalità del [numero](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services)

È possibile scegliere tra due tipi di numero di telefono: **geografico** e **verde**. Dopo aver selezionato un tipo di numero, è possibile scegliere la funzionalità.

In questo esempio è stato selezionato un tipo di **numero verde** con le funzionalità per le **chiamate in uscita** e per gli **SMS in ingresso e in uscita**.

:::image type="content" source="../../media/manage-phone-azure-portal-select-plans.png" alt-text="Screenshot che mostra la selezione delle funzionalità.":::

Da qui, fare clic sul pulsante **Avanti: Numeri** nella parte inferiore della pagina per personalizzare i numeri di telefono di cui effettuare il provisioning.

### <a name="customizing-phone-numbers"></a>Personalizzare i numeri di telefono

Nella pagina **Numeri** sarà possibile personalizzare i numeri di telefono di cui effettuare il provisioning.

:::image type="content" source="../../media/manage-phone-azure-portal-select-numbers-start.png" alt-text="Screenshot che mostra la pagina di selezione dei numeri.":::

> [!NOTE]
> Questa guida di avvio rapido mostra il flusso di personalizzazione del tipo di **numero verde**. Se si sceglie il tipo di numero **geografico**, l'esperienza potrebbe essere leggermente diversa, ma il risultato finale sarà lo stesso.

Scegliere il valore di **Prefisso** nell'elenco di quelli disponibili e immettere la quantità di cui effettuare il provisioning, quindi fare clic su **Cerca** per trovare i numeri che soddisfano i requisiti selezionati. Verranno visualizzati i numeri di telefono che soddisfano le esigenze insieme al relativo costo mensile.

:::image type="content" source="../../media/manage-phone-azure-portal-found-numbers.png" alt-text="Screenshot che mostra la pagina di selezione dei numeri con i numeri riservati.":::

> [!NOTE]
> La disponibilità varia a seconda delle selezioni effettuate per tipo di numero, località e funzionalità.
> I numeri sono riservati per un breve periodo di tempo prima della scadenza della transazione. Se la transazione scade, sarà necessario selezionarli di nuovo.

Per visualizzare il riepilogo dell'acquisto ed effettuare l'ordine, fare clic sul pulsante **Avanti: Riepilogo** nella parte inferiore della pagina.

### <a name="purchase-phone-numbers"></a>Acquistare i numeri di telefono

Nella pagina di riepilogo è possibile rivedere il tipo di numero, le funzionalità, i numeri di telefono e il costo mensile totale per il provisioning dei numeri di telefono.

> [!NOTE]
> I prezzi indicati sono gli **addebiti mensili ricorrenti** che coprono il costo del leasing del numero di telefono selezionato. In questa visualizzazione non sono inclusi i **costi con pagamento in base al consumo** che vengono addebitati quando si effettuano o si ricevono chiamate. I listini prezzi sono [disponibili qui](../../../concepts/pricing.md). Questi costi dipendono dal tipo di numero e dalle destinazioni chiamate. Ad esempio, il prezzo al minuto per una chiamata da un numero locale di Seattle a un numero locale di New York può essere diverso da quello per una chiamata dallo stesso numero a un numero di cellulare del Regno Unito.

Infine, fare clic su **Effettua ordine** nella parte inferiore della pagina per confermare.

:::image type="content" source="../../media/manage-phone-azure-portal-get-numbers-summary.png" alt-text="Screenshot che mostra la pagina di riepilogo con il tipo di numero, le funzionalità, i numeri di telefono e il costo mensile totale":::.

## <a name="find-your-phone-numbers-on-the-azure-portal"></a>Trovare i numeri di telefono nel portale di Azure

Passare alla risorsa di Servizi di comunicazione di Azure nel [portale di Azure](https://portal.azure.com):

:::image type="content" source="../../media/manage-phone-azure-portal-start.png" alt-text="Screenshot che mostra la pagina principale della risorsa di Servizi di comunicazione.":::

Selezionare il pannello Numeri di telefono nel menu per gestire i numeri di telefono.

:::image type="content" source="../../media/manage-phone-azure-portal-phones.png" alt-text="Screenshot che mostra la pagina di numeri di telefono della risorsa di Servizi di comunicazione.":::

> [!NOTE]
> Potrebbero essere necessari alcuni minuti prima che i numeri di cui è stato effettuato il provisioning vengano visualizzati in questa pagina.


### <a name="update-phone-number-capabilities"></a>Aggiorna le funzionalità del numero di telefono

Nella pagina **Numeri** è possibile selezionare un numero di telefono per configurarlo.

:::image type="content" source="../../media/manage-phone-azure-portal-capability-update.png" alt-text="Screenshot che mostra la pagina di aggiornamento delle funzionalità.":::

Selezionare le funzionalità nelle opzioni disponibili, quindi fare clic su **Conferma** per applicare la selezione.

### <a name="release-phone-number"></a>Numero di telefono versione

Nella pagina **numeri** è possibile rilasciare i numeri di telefono.

:::image type="content" source="../../media/manage-phone-azure-portal-release-number.png" alt-text="Screenshot che mostra la pagina numeri di telefono versione.":::

Selezionare il numero di telefono che si desidera rilasciare, quindi fare clic sul pulsante **rilascia** .