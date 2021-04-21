---
title: Accedere alle origini dati in locale
description: Connettersi a origini dati locali da App per la logica di Azure creando una risorsa gateway dati in Azure
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: 49da5d7f045ed06ba16696ebd16ad212b9d140d8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763310"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Connettersi a origini dati locali da App per la logica di Azure

Dopo aver installato il [ *gateway*](../logic-apps/logic-apps-gateway-install.md) dati locale in un computer locale e prima di poter accedere alle origini dati locali dalle app per la logica, è necessario creare una risorsa gateway in Azure per l'installazione del gateway. È quindi possibile selezionare questa risorsa gateway nei trigger e nelle azioni da usare per i connettori locali [disponibili](../connectors/managed.md#on-premises-connectors) in App per la logica di Azure. App per la logica di Azure supporta le operazioni di lettura e scrittura tramite il gateway dati. Per queste operazioni sono però previsti [limiti di dimensioni del payload](/data-integration/gateway/service-gateway-onprem#considerations).

Questo articolo illustra come creare la risorsa gateway di Azure per un [gateway installato in precedenza nel computer locale.](../logic-apps/logic-apps-gateway-install.md) Per altre informazioni sul gateway, vedere [Funzionamento del gateway.](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service)

> [!TIP]
> Per accedere direttamente alle risorse locali nelle reti virtuali di Azure senza dover usare il gateway, prendere invece in considerazione la creazione di un ambiente [*del servizio di*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) integrazione. 

Per informazioni su come usare il gateway con altri servizi, vedere i seguenti articoli:

* [Gateway dati locale di Microsoft Power Automate](/power-automate/gateway-reference)
* [Gateway dati locale di Microsoft Power BI](/power-bi/service-gateway-onprem)
* [Gateway dati locale di Microsoft Power Apps](/powerapps/maker/canvas-apps/gateway-reference)
* [Gateway dati locale di Azure Analysis Services](../analysis-services/analysis-services-gateway.md)

<a name="supported-connections"></a>

## <a name="supported-data-sources"></a>Origini dati supportate

In App per la logica di Azure, il gateway dati locale supporta i [connettori locali](../connectors/managed.md#on-premises-connectors) per queste origini dati:

* BizTalk Server 2016
* File system
* IBM DB2  
* IBM Informix
* IBM MQ
* MySQL
* Oracle Database
* PostgreSQL
* SAP
* SharePoint Server
* SQL Server
* Teradata

È anche possibile creare [connettori personalizzati che](../logic-apps/custom-connector-overview.md) si connettono alle origini dati tramite HTTP o HTTPS tramite REST o SOAP. Anche se il gateway stesso non comporta costi aggiuntivi, il modello di determinazione dei prezzi di [App](../logic-apps/logic-apps-pricing.md) per la logica si applica a questi connettori e ad altre operazioni in App per la logica di Azure.

## <a name="prerequisites"></a>Prerequisiti

* Il gateway [dati locale è già stato installato in un computer locale.](../logic-apps/logic-apps-gateway-install.md) Questa installazione del gateway deve esistere prima di poter creare una risorsa gateway collegata a questa installazione.

* Si dispone dello [stesso account e sottoscrizione di Azure](../logic-apps/logic-apps-gateway-install.md#requirements) usati per l'installazione del gateway. Questo account Azure deve appartenere solo a un singolo [tenant o directory Azure Active Directory (Azure AD).](../active-directory/fundamentals/active-directory-whatis.md#terminology) È necessario usare lo stesso account e la stessa sottoscrizione di Azure per creare la risorsa gateway in Azure perché solo l'amministratore del gateway può creare la risorsa gateway in Azure. Le entità servizio attualmente non sono supportate.

  * Quando si crea una risorsa gateway in Azure, si seleziona un'installazione del gateway da collegare alla risorsa gateway e solo a tale risorsa gateway. Ogni risorsa gateway può essere collegata a una sola installazione del gateway. Non è possibile selezionare un'installazione del gateway già associata a un'altra risorsa gateway.

  * L'app per la logica e la risorsa gateway non devono esistere nella stessa sottoscrizione di Azure. Nei trigger e nelle azioni in cui è possibile usare la risorsa gateway è possibile selezionare una sottoscrizione di Azure diversa con una risorsa gateway, ma solo se tale sottoscrizione esiste nello stesso tenant o directory di Azure AD dell'app per la logica. È anche necessario disporre delle autorizzazioni di amministratore per il gateway, che un altro amministratore può configurare automaticamente. Per altre informazioni, vedere [Gateway dati: Automazione tramite PowerShell - Parte 1](https://community.powerbi.com/t5/Community-Blog/Data-Gateway-Automation-using-PowerShell-Part-1/ba-p/1117330) e [PowerShell: Gateway dati - Add-DataGatewayClusterUser.](/powershell/module/datagateway/add-datagatewayclusteruser)

    > [!NOTE]
    > Attualmente non è possibile condividere una risorsa gateway o l'installazione tra più sottoscrizioni. Per inviare commenti e suggerimenti sui [prodotti, Microsoft Azure Feedback Forum](https://feedback.azure.com/forums/34192--general-feedback).

<a name="create-gateway-resource"></a>

## <a name="create-azure-gateway-resource"></a>Creare una risorsa gateway di Azure

Dopo aver installato il gateway in un computer locale, creare la risorsa di Azure per il gateway.

1. Accedere al [portale di Azure](https://portal.azure.com) con lo stesso account Azure usato per installare il gateway.

1. Nella portale di Azure di ricerca immettere "gateway dati locale" e selezionare Gateway dati **locali**.

   ![Trovare "Gateway dati locale"](./media/logic-apps-gateway-connection/search-for-on-premises-data-gateway.png)

1. In **Gateway dati locali selezionare** **Aggiungi**.

   ![Aggiungere una nuova risorsa di Azure per il gateway dati](./media/logic-apps-gateway-connection/add-azure-data-gateway-resource.png)

1. In **Crea gateway di connessione** specificare queste informazioni per la risorsa gateway. Al termine, selezionare **Crea**.

   | Proprietà | Descrizione |
   |----------|-------------|
   | **Nome risorsa** | Specificare un nome per la risorsa gateway contenente solo lettere, numeri, trattini ( ), caratteri di sottolineatura `-` ( ), parentesi ( , ) o punti ( `_` `(` `)` `.` ). |
   | **Sottoscrizione** | Selezionare la sottoscrizione di Azure per l'account Azure usato per l'installazione del gateway. La sottoscrizione predefinita si basa sull'account di Azure usato per accedere. |
   | **Gruppo di risorse** | Gruppo [di risorse di Azure](../azure-resource-manager/management/overview.md) da usare |
   | **Località** | La stessa area o la stessa località selezionata per il servizio cloud gateway durante [l'installazione del gateway.](../logic-apps/logic-apps-gateway-install.md) In caso contrario, l'installazione del gateway non verrà visualizzata **nell'elenco Nome installazione.** La posizione dell'app per la logica può differire dalla posizione della risorsa del gateway. |
   | **Nome installazione** | Selezionare un'installazione del gateway, che viene visualizzata nell'elenco solo quando vengono soddisfatte queste condizioni: <p><p>- L'installazione del gateway usa la stessa area della risorsa gateway che si vuole creare. <br>- L'installazione del gateway non è collegata a un'altra risorsa gateway di Azure. <br>- L'installazione del gateway è collegata allo stesso account Azure che si sta usando per creare la risorsa gateway. <br>- L'account Azure appartiene a un [singolo tenant o directory Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md#terminology) ed è lo stesso account usato per l'installazione del gateway. <p><p>Per altre informazioni, vedere la [sezione Domande](#faq) frequenti. |
   |||

   Di seguito è riportato un esempio che mostra un'installazione del gateway che si trova nella stessa area della risorsa gateway ed è collegata allo stesso account Azure:

   ![Specificare i dettagli per creare la risorsa gateway dati](./media/logic-apps-gateway-connection/on-premises-data-gateway-create-connection.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Connettersi ai dati locali

Dopo aver creato la risorsa per il gateway e associato la sottoscrizione di Azure a questa risorsa, è possibile creare una connessione tra l'app per la logica e l'origine dati in locale usando il gateway.

1. Nel portale di Azure, creare o aprire l'app per la logica nella finestra di progettazione di app per la logica.

1. Aggiungere un connettore che supporta la connettività locale, ad esempio **SQL Server**.

1. Selezionare **Connect via on-premises data gateway** (Connetti tramite gateway dati locale).

1. In **Gateway** selezionare nell'elenco **Sottoscrizione** la sottoscrizione di Azure con la risorsa gateway desiderata.

   Non è necessario che l'app per la logica e la risorsa gateway esistano nella stessa sottoscrizione di Azure. È possibile scegliere tra altre sottoscrizioni di Azure che hanno ognuna una risorsa gateway, ma solo se queste sottoscrizioni sono presenti nello stesso tenant o directory di Azure AD dell'app per la logica e si hanno le autorizzazioni di amministratore per il gateway, che un altro amministratore può configurare automaticamente. Per altre informazioni, vedere Gateway dati: Automazione con [PowerShell - Parte 1](https://community.powerbi.com/t5/Community-Blog/Data-Gateway-Automation-using-PowerShell-Part-1/ba-p/1117330) e [PowerShell: Gateway dati - Add-DataGatewayClusterUser.](/powershell/module/datagateway/add-datagatewayclusteruser)
  
1. **Nell'elenco Gateway** di connessione, che mostra le risorse del gateway disponibili nella sottoscrizione selezionata, selezionare la risorsa gateway desiderata. Ogni risorsa gateway è collegata a un'installazione di gateway singolo.

   > [!NOTE]
   > L'elenco dei gateway include le risorse del gateway in altre aree perché la posizione dell'app per la logica può differire dalla posizione della risorsa gateway. 

1. Specificare un nome di connessione univoco e altre informazioni necessarie, che dipendono dalla connessione che si vuole creare.

   Un nome di connessione univoco consente di trovare facilmente tale connessione in un secondo momento, soprattutto se si creano più connessioni. Se applicabile, è necessario includere anche il dominio completo per il proprio nome utente.

   Ecco un esempio:

   ![Creare una connessione tra l'app per la logica e il gateway dati](./media/logic-apps-gateway-connection/logic-app-gateway-connection.png)

1. Al termine, selezionare **Crea**.

A questo punto, la connessione del gateway è pronta per l'uso nell'app per la logica.

## <a name="edit-connection"></a>Modificare la connessione

Per aggiornare le impostazioni per una connessione gateway, è possibile modificare la connessione.

1. Per trovare tutte le connessioni API solo per l'app per la logica, nel menu dell'app per la logica in Strumenti di sviluppo **selezionare** **Connessioni API**.

   ![Nel menu dell'app per la logica selezionare "Connessioni API"](./media/logic-apps-gateway-connection/logic-app-api-connections.png)

1. Selezionare la connessione gateway desiderata e quindi selezionare **Modifica connessione API**.

   > [!TIP]
   > Se gli aggiornamenti non hanno effetto, provare ad arrestare e [riavviare l'account](../logic-apps/logic-apps-gateway-install.md#restart-gateway) del servizio Windows gateway per l'installazione del gateway.

Per trovare tutte le connessioni API associate alla sottoscrizione di Azure:

* Dal menu portale di Azure selezionare Connessioni API Web tutti **i**  >    >  **servizi**.
* In caso contrario, dal menu portale di Azure selezionare **Tutte le risorse.** Impostare il **filtro Tipo** su **Connessione API**.

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Eliminare la risorsa del gateway

Per creare una risorsa gateway diversa, collegare l'installazione del gateway a un'altra risorsa gateway o rimuovere la risorsa gateway, è possibile eliminare la risorsa gateway senza influire sull'installazione del gateway.

1. Dal menu portale di Azure selezionare **Tutte le** risorse oppure cercare e selezionare Tutte **le risorse** da qualsiasi pagina. Individuare e selezionare la risorsa del gateway.

1. Se non è già selezionato, nel menu della risorsa del gateway selezionare **Gateway dati locale**. Sulla barra degli strumenti della risorsa del gateway selezionare **Elimina**.

   Ad esempio:

   ![Eliminare una risorsa gateway in Azure](./media/logic-apps-gateway-connection/delete-on-premises-data-gateway.png)

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Domande frequenti

**D:** Perché l'installazione del gateway non viene visualizzata quando si crea la risorsa gateway in Azure? <br/>
**R**: Questo problema può verificarsi per i motivi seguenti:

* L'account Azure non è lo stesso account usato per l'installazione del gateway nel computer locale. Verificare di aver eseguito l'accesso al portale di Azure con la stessa identità usata per l'installazione del gateway. Solo l'amministratore del gateway può creare la risorsa gateway in Azure. Le entità servizio attualmente non sono supportate.

* L'account Azure non appartiene a un solo [tenant o directory Azure AD singolo.](../active-directory/fundamentals/active-directory-whatis.md#terminology) Verificare di usare lo stesso tenant o directory Azure AD usato durante l'installazione del gateway.

* La risorsa gateway e l'installazione del gateway non esistono nella stessa area. Tuttavia, la posizione dell'app per la logica può differire dalla posizione delle risorse del gateway.

* L'installazione del gateway è già associata a un'altra risorsa gateway. Ogni risorsa del gateway può essere collegata a una sola installazione del gateway, che può essere collegata a un solo account e a una sola sottoscrizione di Azure. Non è quindi possibile selezionare un'installazione del gateway già associata a un'altra risorsa gateway. Queste installazioni non verranno visualizzate **nell'elenco Nome** installazione.

  Per esaminare le registrazioni del gateway nel portale di Azure, trovare tutte  le risorse di Azure con  il tipo di risorsa Gateway dati locale in tutte le sottoscrizioni di Azure. Per scollegare l'installazione del gateway dall'altra risorsa gateway, vedere [Eliminare la risorsa gateway.](#change-delete-gateway-resource)

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Proteggere le app per la logica](./logic-apps-securing-a-logic-app.md)
* [Esempi e scenari comuni per le app per la logica](./logic-apps-examples-and-scenarios.md)
