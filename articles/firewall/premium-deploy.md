---
title: Distribuire e configurare Azure Firewall Premium Preview
description: Informazioni su come distribuire e configurare Azure Firewall Premium.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: ec8fc4473669b0c056d0b22ff44e5818b87ba3fa
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100549742"
---
# <a name="deploy-and-configure-azure-firewall-premium-preview"></a>Distribuire e configurare Azure Firewall Premium Preview

> [!IMPORTANT]
> Azure Firewall Premium è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

 Azure Firewall Premium Preview è un firewall di nuova generazione con funzionalità necessarie per ambienti altamente sensibili e regolamentati. Esso comprende le seguenti funzionalità:

- **Ispezione TLS** : consente di decrittografare il traffico in uscita, elaborare i dati e quindi crittografare i dati e inviarli alla destinazione.
- **IDP** : un sistema di rilevamento e prevenzione delle intrusioni nella rete consente di monitorare le attività di rete per attività dannose, registrare informazioni su questa attività, segnalarle e, facoltativamente, provare a bloccarlo.
- **Filtro URL** : estende la funzionalità di filtro FQDN del firewall di Azure per considerare un intero URL. Ad esempio, `www.contoso.com/a/c` anziché `www.contoso.com` .
- **Categorie Web** : gli amministratori possono consentire o negare l'accesso utente alle categorie di siti Web, ad esempio siti Web per il gioco d'azzardo, social media e altri.

Per altre informazioni, vedere [funzionalità Premium del firewall di Azure](premium-features.md).

Si userà un modello per distribuire un ambiente di test con una VNet centrale (10.0.0.0/16) con tre subnet:
- una subnet di lavoro (10.0.10.0/24)
- una subnet di Azure Bastion (10.0.20.0/24)
- una subnet firewall (10.0.100.0/24)

Per semplicità, in questo ambiente di test viene usato un solo VNet centrale. Ai fini della produzione, una [topologia hub e spoke](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) con reti virtuali con peering è più comune.

:::image type="content" source="media/premium-deploy/premium-topology.png" alt-text="Topologia VNet centrale":::

La macchina virtuale di lavoro è un client che invia richieste HTTP/S attraverso il firewall.

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="deploy-the-infrastructure"></a>Distribuire l'infrastruttura

Il modello distribuisce un ambiente di test completo per Azure Firewall Premium abilitato con IDP, l'ispezione TLS, il filtro URL e le categorie Web:

- un nuovo criterio di Firewall Premium e firewall di Azure con impostazioni predefinite per consentire una semplice convalida delle funzionalità principali (IDP, ispezione TLS, filtro URL e categorie Web)
- distribuisce tutte le dipendenze, tra cui Key Vault e un'identità gestita. In un ambiente di produzione queste risorse possono essere già state create e non necessarie nello stesso modello.
- genera un'autorità di certificazione radice autofirmata e la distribuisce nella Key Vault generata
-  genera una CA intermedia derivata e la distribuisce in una macchina virtuale di test di Windows (WorkerVM)
- viene anche distribuito un host Bastion (BastionHost) che può essere usato per connettersi al computer di testing Windows (WorkerVM)



[![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-premium%2Fazuredeploy.json)

## <a name="test-the-firewall"></a>Testare il firewall

Ora è possibile testare i IDP, l'ispezione TLS, il filtro Web e le categorie Web.

### <a name="add-firewall-diagnostics-settings"></a>Aggiungere le impostazioni di diagnostica del firewall

Per raccogliere i log del firewall, è necessario aggiungere le impostazioni di diagnostica per raccogliere i log del firewall.

1. Selezionare il **DemoFirewall** e in **monitoraggio** selezionare **impostazioni di diagnostica**.
2. Selezionare **Aggiungi impostazione di diagnostica**.
3. Per **nome dell'impostazione di diagnostica**, digitare *FW-diag*.
4. In **log** selezionare **AzureFirewallApplicationRule** e **AzureFirewallNetworkRule**.
5. In **Dettagli destinazione** selezionare **Invia a log Analytics area di lavoro**.
6. Selezionare **Salva**.

### <a name="idps-tests"></a>Test IDP

Per testare gli IDP, è necessario distribuire il server Web interno con un certificato server appropriato. Per altre informazioni sui requisiti dei certificati per Azure Firewall Premium Preview, vedere [certificati di anteprima Premium del firewall di Azure](premium-certificates.md).

È possibile utilizzare `curl` per controllare varie intestazioni HTTP e simulare traffico dannoso.

#### <a name="to-test-idps-for-http-traffic"></a>Per testare i IDP per il traffico HTTP:

1. Nella macchina virtuale WorkerVM aprire una finestra del prompt dei comandi dell'amministratore.
2. Al prompt dei comandi digitare il comando seguente:

   `curl -A "BlackSun" <your web server address>`
3. Verrà visualizzata la risposta del server Web.
4. Passare ai registri della regola di rete del firewall nel portale di Azure per trovare un avviso simile al messaggio seguente:

   :::image type="content" source="media/premium-deploy/alert-message.png" alt-text="Messaggio di avviso":::

   > [!NOTE]
   > La visualizzazione dei dati nei log potrebbe richiedere del tempo. Assegnare almeno 20 minuti per consentire ai log di iniziare a visualizzare i dati.
5. Aggiungere una regola di firma per la firma 2008983:

   1. Selezionare il **DemoFirewallPolicy** e in **Impostazioni** Selezionare **IDP (anteprima)**.
   1. Selezionare la scheda **regole di firma** .
   1. In **ID firma** Digitare *2008983* nella casella di testo Apri.
   1. In **modalità** selezionare **Nega**.
   1. Selezionare **Salva**.
   1. Attendere il completamento della distribuzione prima di procedere.



6. In WorkerVM eseguire di `curl` nuovo il comando:

   `curl -A "BlackSun" <your web server address>`

   Poiché la richiesta HTTP è ora bloccata dal firewall, dopo la scadenza del timeout della connessione verrà visualizzato l'output seguente:

   `read tcp 10.0.100.5:55734->10.0.20.10:80: read: connection reset by peer`

7. Passare ai log di monitoraggio nella portale di Azure e trovare il messaggio per la richiesta bloccata.
<!---8. Now you can bypass the IDPS function using the **Bypass list**.

   1. On the **IDPS (preview)** page, select the **Bypass list** tab.
   2. Edit **MyRule** and set **Destination** to *10.0.20.10, which is the ServerVM private IP address.
   3. Select **Save**.
1. Run the test again: `curl -A "BlackSun" http://server.2020-private-preview.com` and now you should get the `Hello World` response and no log alert. --->

#### <a name="to-test-idps-for-https-traffic"></a>Per testare i IDP per il traffico HTTPS

Ripetere i test curl usando HTTPS anziché HTTP. Ad esempio:

`curl --ssl-no-revoke -A "BlackSun" <your web server address>`

Verranno visualizzati gli stessi risultati ottenuti con i test HTTP.

### <a name="tls-inspection-with-url-filtering"></a>Ispezione TLS con filtro URL

Usare la procedura seguente per testare l'ispezione TLS con il filtro URL.

1. Modificare le regole dell'applicazione per i criteri firewall e aggiungere una nuova regola denominata `AllowURL` alla `AllowWeb` raccolta regole. Configurare l'URL di destinazione `www.nytimes.com/section/world` , l'indirizzo IP di origine **\* *_, il tipo di destinazione _* URL (anteprima)**, selezionare **TLS Inspection (Preview)** e protocolli **http, HTTPS**.

3. Al termine della distribuzione, aprire un browser in WorkerVM e passare a `https://www.nytimes.com/section/world` e verificare che la risposta HTML venga visualizzata come previsto nel browser.
4. Nella portale di Azure è possibile visualizzare l'intero URL nei log di monitoraggio delle regole dell'applicazione:

      :::image type="content" source="media/premium-deploy/alert-message-url.png" alt-text="Messaggio di avviso che mostra l'URL":::

Alcune pagine HTML potrebbero sembrare Incomplete perché fanno riferimento ad altri URL negati. Per risolvere questo problema, è possibile adottare l'approccio seguente:

- Se la pagina HTML contiene collegamenti ad altri domini, è possibile aggiungere questi domini a una nuova regola dell'applicazione con Consenti l'accesso a questi FQDN.
- Se la pagina HTML contiene collegamenti a URL secondari, è possibile modificare la regola e aggiungere un asterisco all'URL. ad esempio `targetURLs=www.nytimes.com/section/world*`

   In alternativa, è possibile aggiungere un nuovo URL alla regola. Ad esempio: 

   `www.nytimes.com/section/world, www.nytimes.com/section/world/*`

### <a name="web-categories-testing"></a>Test delle categorie Web

Viene ora creata una regola dell'applicazione per consentire l'accesso a siti Web sportivi.
1. Dal portale aprire il gruppo di risorse e selezionare **DemoFirewallPolicy**.
2. Selezionare **regole applicazione**, quindi **aggiungere una raccolta regole**.
3. Per **nome** digitare *GeneralWeb*, **Priority** *103*, **gruppo di raccolta regole** selezionare **DefaultApplicationRuleCollectionGroup**.
4. In **regole** per **nome** digitare *AllowSports*, **origine** *\** , **protocollo** *http, HTTPS*, selezionare **controllo TLS**, **tipo destinazione** selezionare *categorie Web (anteprima)*, **destinazione** selezionare *sport*.
5. Selezionare **Aggiungi**.

      :::image type="content" source="media/premium-deploy/web-categories.png" alt-text="Categoria Web Sports":::
6. Al termine della distribuzione, passare a  **WorkerVM** e aprire un Web browser e passare a `https://www.nfl.com` .

   Dovrebbe essere visualizzata la pagina Web NFL e il log delle regole dell'applicazione mostra che è stata trovata una corrispondenza per una **categoria Web: regola sportiva** e la richiesta è stata consentita.

## <a name="next-steps"></a>Passaggi successivi

- [Anteprima di Azure Firewall Premium nella portale di Azure](premium-portal.md)
