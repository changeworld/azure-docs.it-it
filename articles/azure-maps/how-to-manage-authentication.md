---
title: Gestire l'autenticazione
titleSuffix: Azure Maps
description: Acquisire familiarità con l'autenticazione di Azure maps. Vedere quale approccio funziona meglio nello scenario. Informazioni su come usare il portale per visualizzare le impostazioni di autenticazione.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 955b541bdb4ae38066f1eb4d2f09363ec51be1d2
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864075"
---
# <a name="manage-authentication-in-azure-maps"></a>Gestire l'autenticazione in Mappe di Azure

Dopo aver creato un account Azure Maps, vengono creati un ID client e le chiavi per supportare l'autenticazione Azure Active Directory (Azure AD) e l'autenticazione con chiave condivisa.

## <a name="view-authentication-details"></a>Visualizzare i dettagli di autenticazione

Dopo aver creato un account Azure Maps, vengono generate le chiavi primarie e secondarie. Si consiglia di usare una chiave primaria come chiave di sottoscrizione quando si [Usa l'autenticazione con chiave condivisa per chiamare Azure Maps](./azure-maps-authentication.md#shared-key-authentication). È possibile usare una chiave secondaria in scenari quali le modifiche delle chiavi in sequenza. Per altre informazioni, vedere [autenticazione in mappe di Azure](./azure-maps-authentication.md).

È possibile visualizzare i dettagli di autenticazione nel portale di Azure. Nell'account scegliere **autenticazione** dal menu **Impostazioni** .

> [!div class="mx-imgBorder"]
> ![Dettagli di autenticazione](./media/how-to-manage-authentication/how-to-view-auth.png)

## <a name="discover-category-and-scenario"></a>Individua categoria e scenario

A seconda delle esigenze dell'applicazione, esistono percorsi specifici per la protezione dell'applicazione. Azure AD definisce le categorie per supportare un'ampia gamma di flussi di autenticazione. Vedere [categorie di applicazioni](../active-directory/develop/authentication-flows-app-scenarios.md#application-categories) per comprendere la categoria in cui l'applicazione è più idonea.

> [!NOTE]
> Anche se si usa l'autenticazione con chiave condivisa, le informazioni sulle categorie e sugli scenari consentono di proteggere l'applicazione.

## <a name="determine-authentication-and-authorization"></a>Determinare l'autenticazione e l'autorizzazione

La tabella seguente descrive gli scenari di autenticazione e autorizzazione comuni nelle mappe di Azure. La tabella fornisce un confronto tra i tipi di protezione offerti da ogni scenario.

> [!IMPORTANT]
> Microsoft consiglia di implementare Azure Active Directory (Azure AD) con il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per le applicazioni di produzione.

| Scenario                                                                                    | Authentication | Autorizzazione | Lavoro di sviluppo | Lavoro operativo |
| ------------------------------------------------------------------------------------------- | -------------- | ------------- | ------------------ | ------------------ |
| [Daemon attendibile/applicazione client non interattiva](./how-to-secure-daemon-app.md)        | Chiave condivisa     | N/D           | Medio             | Alto               |
| [Daemon attendibile/applicazione client non interattiva](./how-to-secure-daemon-app.md)        | Azure AD       | Alto          | Basso                | Medio             |
| [Applicazione Web a pagina singola con accesso Single Sign-on interattivo](./how-to-secure-spa-users.md) | Azure AD       | Alto          | Medio             | Livello medio             |
| [Applicazione Web a pagina singola con accesso non interattivo](./how-to-secure-spa-app.md)      | Azure AD       | Alto          | Medio             | Livello medio             |
| [Applicazione Web con accesso Single Sign-on interattivo](./how-to-secure-webapp-users.md)          | Azure AD       | Alto          | Alto               | Medio             |
| [Dispositivo a cui è vincolato il dispositivo/input](./how-to-secure-device-code.md)                     | Azure AD       | Alto          | Medio             | Livello medio             |

I collegamenti nella tabella consentono di ottenere informazioni dettagliate sulla configurazione per ogni scenario.

## <a name="view-role-definitions"></a>Visualizzare le definizioni dei ruoli

Per visualizzare i ruoli di Azure disponibili per le mappe di Azure, passare a **controllo di accesso (IAM)**. Selezionare **Roles (ruoli**) e quindi cercare i ruoli che iniziano con *Maps di Azure*. Questi ruoli di Azure Maps sono i ruoli a cui è possibile concedere l'accesso.

> [!div class="mx-imgBorder"]
> ![Visualizzare i ruoli disponibili](./media/how-to-manage-authentication/how-to-view-avail-roles.png)

## <a name="view-role-assignments"></a>Visualizzare le assegnazioni di ruolo

Per visualizzare gli utenti e le app a cui è stato concesso l'accesso per le mappe di Azure, passare a **controllo di accesso (IAM)**. Selezionare **assegnazioni di ruolo**, quindi filtrare in base a **Maps di Azure**.

> [!div class="mx-imgBorder"]
> ![Visualizzare gli utenti e le app a cui è stato concesso l'accesso](./media/how-to-manage-authentication/how-to-view-amrbac.png)

## <a name="request-tokens-for-azure-maps"></a>Richiedere token per Mappe di Azure

Richiedere un token dall'endpoint del token Azure AD. Nella richiesta Azure AD usare i dettagli seguenti:

| Ambiente Azure      | Endpoint token Azure AD             | ID risorsa di Azure              |
| ---------------------- | ----------------------------------- | ------------------------------ |
| Cloud pubblico di Azure     | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Cloud di Azure per enti pubblici | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` |

Per ulteriori informazioni sulla richiesta di token di accesso da Azure AD per gli utenti e le entità servizio, vedere scenari di [autenticazione per Azure ad](../active-directory/develop/authentication-vs-authorization.md) e visualizzare scenari specifici nella tabella degli [scenari](./how-to-manage-authentication.md#determine-authentication-and-authorization).

## <a name="manage-and-rotate-shared-keys"></a>Gestire e ruotare le chiavi condivise

Le chiavi di sottoscrizione di Azure Maps sono simili a una password radice per l'account Azure maps. Prestare sempre attenzione a proteggere le chiavi di sottoscrizione. Usare Azure Key Vault per gestire e ruotare le chiavi in modo sicuro. Evitare di distribuire chiavi di accesso ad altri utenti, codificarle a livello di codice o salvarle ovunque in testo normale accessibile ad altri utenti. Ruotare le chiavi se si ritiene che potrebbero essere state compromesse.

> [!NOTE]
> Microsoft consiglia di usare Azure Active Directory (Azure AD) per autorizzare le richieste, se possibile, invece della chiave condivisa. Azure AD offre sicurezza e facilità d'uso superiori rispetto alla chiave condivisa.

### <a name="manually-rotate-subscription-keys"></a>Ruotare manualmente le chiavi della sottoscrizione

Microsoft consiglia di ruotare periodicamente le chiavi di sottoscrizione per proteggere l'account Azure maps. Se possibile, usare Azure Key Vault per gestire le chiavi di accesso. Se non si usa Key Vault, sarà necessario ruotare manualmente le chiavi.

Vengono assegnate due chiavi di sottoscrizione in modo che sia possibile ruotare le chiavi. La presenza di due chiavi garantisce che l'applicazione mantenga l'accesso alle mappe di Azure durante tutto il processo.

Per ruotare le chiavi di sottoscrizione di Azure Maps nel portale di Azure:

1. Aggiornare il codice dell'applicazione per fare riferimento alla chiave secondaria per l'account Azure Maps e distribuire.
2. Passare all'account Azure Maps nell' [portale di Azure](https://portal.azure.com/).
3. In **Impostazioni** selezionare **autenticazione**.
4. Per rigenerare la chiave primaria per l'account Azure Maps, selezionare il pulsante **Rigenera** accanto alla chiave primaria.
5. Aggiornare il codice dell'applicazione per fare riferimento alla nuova chiave primaria e distribuirla.
6. Rigenerare la chiave secondaria nello stesso modo.

> [!WARNING]
> Microsoft consiglia di usare solo una delle chiavi in tutte le applicazioni contemporaneamente. Se si usa la chiave 1 in alcune posizioni e la chiave 2 in altre, non sarà possibile ruotare le chiavi senza che alcune applicazioni perdano l'accesso.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere [Azure ad e Azure Maps Web SDK](./how-to-use-map-control.md).

Trovare le metriche di utilizzo dell'API per l'account Azure Maps:
> [!div class="nextstepaction"]
> [Visualizzare le metriche di utilizzo](how-to-view-api-usage.md)

Scopri gli esempi che illustrano come integrare Azure AD con mappe di Azure:

> [!div class="nextstepaction"]
> [Esempi di autenticazione Azure AD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)