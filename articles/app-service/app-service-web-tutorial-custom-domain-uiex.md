---
title: 'Esercitazione: Eseguire il mapping di un nome DNS personalizzato esistente'
description: Informazioni su come aggiungere un nome di dominio DNS personalizzato esistente, ad esempio il dominio personale, in un'app Web, nel back-end dell'app per dispositivi mobili o nell'app per le API del servizio app di Azure.
keywords: servizio app, servizio app di Azure, mapping del dominio, nome di dominio, dominio esistente, nome host, dominio Vanity
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/25/2020
ms.custom: mvc, seodec18
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ca1308c969227336bfb4970f7c5c77b9f2e0cc22
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102216531"
---
# <a name="tutorial-map-an-existing-custom-dns-name-to-azure-app-service"></a>Esercitazione: Eseguire il mapping di un nome DNS personalizzato esistente al Servizio app di Azure

Questa esercitazione illustra come eseguire il mapping di qualsiasi <abbr title="Un nome di dominio acquistato da un registrar di dominio, ad esempio GoDaddy, o un sottodominio del dominio acquistato.">nome di dominio DNS personalizzato</abbr> in <abbr title="Un servizio basato su HTTP per l'hosting di applicazioni Web, API REST e applicazioni back-end per dispositivi mobili.">Servizio app di Azure</abbr>.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Eseguire il mapping di un sottodominio usando <abbr title="Un record di nome canonico DNS esegue il mapping di un nome di dominio a un altro.">Record CNAME</abbr>.
> * Eseguire il mapping di un dominio radice usando un <abbr title="Un record di indirizzo in DNS esegue il mapping di un nome host a un indirizzo IP.">Record A</abbr>.
> * Eseguire il mapping di un dominio con caratteri jolly usando un record CNAME.
> * Reindirizzare l'URL predefinito a una directory personalizzata.

<hr/> 

## <a name="1-prepare-your-environment"></a>1. Preparare l'ambiente

* [Creare un'app del servizio app](./index.yml) oppure usare un'app creata per un'altra esercitazione.
* Assicurarsi che sia possibile modificare i record DNS per il dominio personalizzato. Se non si dispone ancora di un dominio personalizzato, è possibile [acquistare un dominio del servizio app](manage-custom-dns-buy-domain.md).

    <details>
        <summary>Cosa è necessario per modificare I record DNS?</summary>
        Richiede l'accesso al registro DNS per il provider di dominio, ad esempio GoDaddy. Ad esempio, per aggiungere le voci DNS per <code>contoso.com</code> e <code>www.contoso.com</code> è necessario essere autorizzati a configurare le impostazioni DNS per il dominio radice <code>contoso.com</code>.
    </details>

<hr/> 

## <a name="2-prepare-the-app"></a>2. preparare l'app

Per eseguire il mapping di un nome DNS personalizzato a un'app, l'app <abbr title="Specifica la posizione, le dimensioni e le funzionalità del server farm Web che ospita l'app.">Piano di servizio app</abbr> deve essere un livello a pagamento (non <abbr title="Un app Azure livello di servizio in cui l'app è in esecuzione sulle stesse VM di altre app, incluse le app di altri clienti. Questo livello è destinato a sviluppo e test.">**Gratuito (F1)**</abbr>). Per ulteriori informazioni, vedere [app Azure Panoramica del piano di servizio](overview-hosting-plans.md).

#### <a name="sign-in-to-azure"></a>Accedere ad Azure

Aprire il [portale di Azure](https://portal.azure.com) e accedere con l'account Azure.

#### <a name="select-the-app-in-the-azure-portal"></a>Selezionare l'app nel portale di Azure

1. Cercare e selezionare **Servizi app**.

   ![Screenshot che mostra la selezione di Servizi app.](./media/app-service-web-tutorial-custom-domain/app-services.png)

1. Nella pagina **Servizi app** selezionare il nome dell'app di Azure.

   ![Screenshot che mostra come spostarsi nel portale per accedere a un'app di Azure.](./media/app-service-web-tutorial-custom-domain/select-app.png)

    Viene visualizzata la pagina di gestione dell'app del servizio app.

<a name="checkpricing" aria-hidden="true"></a>

#### <a name="check-the-pricing-tier"></a>Scegliere il piano tariffario

1. Nel riquadro sinistro della pagina dell'app scorrere fino alla sezione **Impostazioni** e selezionare **Aumenta prestazioni (piano di servizio app)** .

   ![Screenshot che mostra il menu Aumenta prestazioni (piano di servizio app).](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

1. Il livello corrente dell'app è evidenziato da un bordo blu. Verificare che l'app non sia inclusa nel livello **F1**. Il DNS personalizzato non è supportato nel livello **F1**.

   ![Screenshot che mostra i piani tariffari consigliati.](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

1. Se il piano di servizio app non è nel livello **F1** , chiudere la pagina **scalabilità verticale** e passare a [3. Ottenere un ID di verifica del dominio](#3-get-a-domain-verification-id).

<a name="scaleup" aria-hidden="true"></a>

#### <a name="scale-up-the-app-service-plan"></a>Passare a un piano di servizio app di livello superiore

1. Selezionare uno dei livelli non gratuiti (**D1**, **B1**, **B2**, **B3** o uno qualsiasi dei livelli della categoria **Produzione**). Per altre opzioni, selezionare **Visualizza opzioni aggiuntive**.

1. Selezionare **Applica**.

   ![Screenshot che mostra la verifica dei piani tariffari.](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

   La visualizzazione della notifica seguente indica che l'operazione di passaggio al livello superiore è stata completata.

   ![Screenshot che mostra la conferma dell'operazione di aumento delle prestazioni.](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<hr/> 

<a name="cname" aria-hidden="true"></a>

## <a name="3-get-a-domain-verification-id"></a>3. ottenere un ID di verifica del dominio

Per aggiungere un dominio personalizzato all'app, è necessario verificare la proprietà del dominio aggiungendo un ID verifica come record TXT al provider di dominio. 

1. Nel riquadro sinistro della pagina dell'app selezionare **Domini personalizzati**. 
1. Nella pagina **Domini personalizzati** copiare l'ID nella casella **ID di verifica dominio personalizzato** per il passaggio successivo.

    ![Screenshot che mostra l'ID nella casella ID di verifica dominio personalizzato.](./media/app-service-web-tutorial-custom-domain/get-custom-domain-verification-id.png)

    <details>
        <summary>Perché è necessario?</summary>
        L'aggiunta dell'ID di verifica al dominio personalizzato può impedire voci DNS inesatte e consente di evitare l'acquisizione della proprietà dei sottodomini. È consigliabile proteggere dallo stesso rischio i domini personalizzati configurati in precedenza senza questo ID aggiungendo l'ID verifica al record DNS. Per altre informazioni su questa minaccia comune con gravità elevata, vedere <a href="/azure/security/fundamentals/subdomain-takeover">Acquisizione della proprietà dei sottodomini</a>.
    </details>
    
<a name="info"></a>

3. **(Solo record A)** Per eseguire il mapping di un oggetto <abbr title="Un record di indirizzo in DNS esegue il mapping di un nome host a un indirizzo IP.">Record A</abbr>, è necessario l'indirizzo IP esterno dell'app. Nella pagina **domini personalizzati** , copiare il valore di **indirizzo IP**.

   ![Screenshot che mostra come spostarsi nel portale per accedere a un'app di Azure.](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

<hr/> 

## <a name="4-create-the-dns-records"></a>4. creare i record DNS

1. Accedere al sito Web del provider di dominio.

    <details>
        <summary>È possibile gestire il DNS dal provider di dominio usando Azure?</summary>
        Se lo si desidera, è possibile usare DNS di Azure per gestire i record DNS per il dominio e configurare un nome DNS personalizzato per il servizio app Azure. Per altre informazioni, vedere <a href="https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns">esercitazione: ospitare il dominio in DNS di Azure></a>.
    </details>

1. Individuare la pagina relativa alla gestione dei record DNS. 

    <details>
        <summary>Ricerca per categorie trovare la pagina?</summary>
        <p>Poiché ogni provider di dominio ha una propria interfaccia per i record DNS, vedere la documentazione del provider. Cercare le aree del sito denominate <strong>Domain Name</strong> (Nome di dominio), <strong>DNS</strong> o <strong>Name Server Management</strong> (Gestione server dei nomi).</p>
        <p>È spesso possibile visualizzare la pagina dei record DNS visualizzando le informazioni dell'account e cercando un collegamento come <strong>My domains</strong> (Domini personali). Passare alla pagina e quindi cercare un collegamento con un titolo simile a <strong>File di zona</strong>, <strong>Record DNS</strong> o <strong>Configurazione avanzata</strong>.</p>
    </details>

   La schermata seguente è un esempio di pagina di record DNS:

   ![Screenshot che mostra una pagina di record DNS di esempio.](../../includes/media/app-service-web-access-dns-records-no-h/example-record-ui.png)

1. Selezionare **Aggiungi** o il widget appropriato per creare un record. 

1. Selezionare il tipo di record da creare e seguire le istruzioni. È possibile usare un <abbr title="Un record di nome canonico in DNS esegue il mapping di un nome di dominio (un alias) a un altro (il nome canonico).">Record CNAME</abbr> o un <abbr title="Un record di indirizzo in DNS esegue il mapping di un nome host a un indirizzo IP.">Record A</abbr> per eseguire il mapping di un nome DNS personalizzato al servizio app. 

    <details>
        <summary>Quale record scegliere?</summary>
        <div>
            <ul>
            <li>Per eseguire il mapping del dominio radice (ad esempio, <code>contoso.com</code> ), usare un record A. Non usare il record CNAME per il record radice (per informazioni, vedere la <a href="https://en.wikipedia.org/wiki/CNAME_record">voce di Wikipedia</a>).</li>
            <li>Per eseguire il mapping di un sottodominio (ad esempio, <code>www.contoso.com</code> ), usare un record CNAME.</li>
            <li>È possibile eseguire il mapping di un sottodominio all'indirizzo IP dell'app direttamente con un record A, ma è possibile che <a href="https://docs.microsoft.com/azure/app-service/overview-inbound-outbound-ips#when-inbound-ip-changes">l'indirizzo IP venga modificato</a>. Il record CNAME esegue il mapping al nome host dell'app, che è meno soggetto a modifiche.</li>
            <li>Per eseguire il mapping di un <a href="https://en.wikipedia.org/wiki/Wildcard_DNS_record">dominio con caratteri jolly</a> (ad esempio, <code>*.contoso.com</code> ), usare un record CNAME.</li>
            </ul>
        </div>
    </details>
    
# <a name="cname"></a>[CNAME](#tab/cname)

Per un sottodominio, ad esempio `www` `www.contoso.com` , creare due record in base alla tabella seguente:

| Tipo di record | Host | valore | Commenti |
| - | - | - |
| CNAME | `<subdomain>` (ad esempio, `www` ) | `<app-name>.azurewebsites.net` | Il mapping del dominio stesso. |
| TXT | `asuid.<subdomain>` (ad esempio, `asuid.www` ) | [ID verifica ottenuto in precedenza](#3-get-a-domain-verification-id) | Il servizio app accede al record TXT `asuid.<subdomain>` per verificare la proprietà del dominio personalizzato. |

![Screenshot che mostra come spostarsi nel portale per accedere a un'app di Azure.](./media/app-service-web-tutorial-custom-domain/cname-record.png)
    
# <a name="a"></a>[A](#tab/a)

Per un dominio radice, ad esempio `contoso.com` , creare due record in base alla tabella seguente:

| Tipo di record | Host | valore | Commenti |
| - | - | - |
| Una | `@` | Indirizzo IP ricavato da [Copiare l'indirizzo IP dell'app](#3-get-a-domain-verification-id) | Il mapping di dominio stesso (`@` rappresenta in genere il dominio radice). |
| TXT | `asuid` | [ID verifica ottenuto in precedenza](#3-get-a-domain-verification-id) | Il servizio app accede al record TXT `asuid.<subdomain>` per verificare la proprietà del dominio personalizzato. Per il dominio radice usare `asuid`. |

![Screenshot che mostra una pagina di record DNS.](./media/app-service-web-tutorial-custom-domain/a-record.png)

<details>
<summary>Cosa accade se si desidera eseguire il mapping di un sottodominio con un record A?</summary>
Per eseguire il mapping di un sottodominio, ad esempio un record `www.contoso.com` a anziché un record CNAME consigliato, il record a record e txt dovrebbe essere simile alla tabella seguente:

<div class="table-scroll-wrapper"><table class="table"><caption class="visually-hidden">Tabella 3</caption>
<thead>
<tr>
<th>Tipo di record</th>
<th>Host</th>
<th>valore</th>
</tr>
</thead>
<tbody>
<tr>
<td>Una</td>
<td><code>&lt;subdomain&gt;</code> (ad esempio, <code>www</code> )</td>
<td>Indirizzo IP ricavato da <a href="#info" data-linktype="self-bookmark">Copiare l'indirizzo IP dell'app</a></td>
</tr>
<tr>
<td>TXT</td>
<td><code>asuid.&lt;subdomain&gt;</code> (ad esempio, <code>asuid.www</code> )</td>
<td><a href="#3-get-a-domain-verification-id" data-linktype="self-bookmark">ID verifica ottenuto in precedenza</a></td>
</tr>
</tbody>
</table></div>
</details>

# <a name="wildcard-cname"></a>[Carattere jolly (CNAME)](#tab/wildcard)

Per un nome con caratteri jolly come `*` in `*.contoso.com` , creare due record in base alla tabella seguente:

| Tipo di record | Host | valore | Commenti |
| - | - | - |
| CNAME | `*` | `<app-name>.azurewebsites.net` | Il mapping del dominio stesso. |
| TXT | `asuid` | [ID verifica ottenuto in precedenza](#3-get-a-domain-verification-id) | Il servizio app accede al record TXT `asuid` per verificare la proprietà del dominio personalizzato. |

![Screenshot che mostra come spostarsi nel portale per accedere a un'app di Azure.](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)
    
-----

<details>
<summary>Le modifiche vengono cancellate dopo aver lasciato la pagina.</summary>
<p>Per alcuni provider, ad esempio GoDaddy, le modifiche ai record DNS vengono applicate solo dopo la selezione di un collegamento <strong>Salva modifiche</strong> separato.</p>
</details>

<hr/>

## <a name="5-enable-the-mapping-in-your-app"></a>5. abilitare il mapping nell'app

1. Nel riquadro sinistro della pagina dell'app nel portale di Azure selezionare **Domini personalizzati**.

    ![Screenshot che mostra il menu Domini personalizzati.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Selezionare **Aggiungi dominio personalizzato**.

    ![Screenshot che mostra l'elemento per l'aggiunta del nome host.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

# <a name="cname"></a>[CNAME](#tab/cname)

3. Digitare il nome di dominio completo per il quale è stato aggiunto un record CNAME, ad esempio `www.contoso.com`.

1. Selezionare **Convalida**. Viene visualizzata la pagina **Aggiungi dominio personalizzato**.

1. Assicurarsi che **Tipo di record del nome host** sia impostato su **CNAME (www\.example.com o qualsiasi sottodominio**). Selezionare **Aggiungi dominio personalizzato**.

    ![Screenshot che mostra il pulsante Aggiungi dominio personalizzato.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

    La visualizzazione del nuovo dominio personalizzato nella pagina **Domini personalizzati** dell'app potrebbe richiedere qualche minuto. Aggiornare il browser per visualizzare i dati più recenti.

    ![Screenshot che mostra l'aggiunta del record CNAME.](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    <details>
        <summary>Che cos'è l'etichetta di avviso <strong>non sicuro</strong> ?</summary>
        Un'etichetta di avviso indica che il dominio personalizzato non è ancora associato a un certificato TLS/SSL. In seguito a qualsiasi richiesta HTTPS inviata da un browser al dominio personalizzato si riceverà un errore o un avviso, a seconda del browser. Per aggiungere un'associazione TLS, vedere <a href="https://docs.microsoft.com/azure/app-service/configure-ssl-bindings">Proteggere un nome DNS personalizzato con un'associazione TLS/SSL nel Servizio app di Azure</a>.
    </details>

    Se è stato saltato un passaggio o è stato inserito un errore di digitazione, nella parte inferiore della pagina viene visualizzato un errore di verifica.

    ![Screenshot che mostra un errore di verifica.](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a" aria-hidden="true"></a>

<a name="enable-a" aria-hidden="true"></a>

# <a name="a"></a>[A](#tab/a)

3. Digitare il nome di dominio completo per il quale è stato configurato il record A, ad esempio `contoso.com`. 

1. Selezionare **Convalida**. Viene visualizzata la pagina **Aggiungi dominio personalizzato**.

1. Assicurarsi che **Tipo di record del nome host** sia impostato su **Record A (esempio.com).** Selezionare **Aggiungi dominio personalizzato**.

    ![Screenshot che mostra l'aggiunta di un nome DNS all'app.](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

    La visualizzazione del nuovo dominio personalizzato nella pagina **Domini personalizzati** dell'app potrebbe richiedere qualche minuto. Aggiornare il browser per visualizzare i dati più recenti.

    ![Screenshot che mostra l'aggiunta di un record A.](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

    <details>
        <summary>Che cos'è l'etichetta di avviso <strong>non sicuro</strong> ?</summary>
        Un'etichetta di avviso indica che il dominio personalizzato non è ancora associato a un certificato TLS/SSL. In seguito a qualsiasi richiesta HTTPS inviata da un browser al dominio personalizzato si riceverà un errore o un avviso, a seconda del browser. Per aggiungere un'associazione TLS, vedere <a href="https://docs.microsoft.com/azure/app-service/configure-ssl-bindings">Proteggere un nome DNS personalizzato con un'associazione TLS/SSL nel Servizio app di Azure</a>.
    </details>
    
    Se è stato saltato un passaggio o è stato inserito un errore di digitazione, nella parte inferiore della pagina viene visualizzato un errore di verifica.
    
    ![Screenshot che mostra un errore di verifica.](./media/app-service-web-tutorial-custom-domain/verification-error.png)
    
<a name="wildcard" aria-hidden="true"></a>

# <a name="wildcard-cname"></a>[Carattere jolly (CNAME)](#tab/wildcard)

3. Digitare un nome di dominio completo che corrisponda al dominio con caratteri jolly. Per l'esempio, ad esempio, `*.contoso.com` è possibile usare `sub1.contoso.com` , `sub2.contoso.com` , `*.contoso.com` o qualsiasi altra stringa che corrisponda al modello con caratteri jolly. Selezionare quindi **convalida**.

    Il pulsante **Aggiungi dominio personalizzato** viene attivato.

1. Assicurarsi che **Tipo di record del nome host** sia impostato su **CNAME (www\.example.com o qualsiasi sottodominio**). Selezionare **Aggiungi dominio personalizzato**.

    ![Screenshot che mostra l'aggiunta di un nome DNS all'app.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

    La visualizzazione del nuovo dominio personalizzato nella pagina **Domini personalizzati** dell'app potrebbe richiedere qualche minuto. Aggiornare il browser per visualizzare i dati più recenti.

    <details>
        <summary>Che cos'è l'etichetta di avviso <strong>non sicuro</strong> ?</summary>
        Un'etichetta di avviso indica che il dominio personalizzato non è ancora associato a un certificato TLS/SSL. In seguito a qualsiasi richiesta HTTPS inviata da un browser al dominio personalizzato si riceverà un errore o un avviso, a seconda del browser. Per aggiungere un'associazione TLS, vedere <a href="https://docs.microsoft.com/azure/app-service/configure-ssl-bindings">Proteggere un nome DNS personalizzato con un'associazione TLS/SSL nel Servizio app di Azure</a>.
    </details>

-----

<hr/> 

## <a name="6-test-in-a-browser"></a>6. eseguire il test in un browser

Individuare i nomi DNS configurati in precedenza.

![Screenshot che mostra come spostarsi nel portale per accedere a un'app di Azure.](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

<a name="resolve-404-not-found" aria-hidden="true"></a>
<details>
<summary>Viene ricevuto un errore HTTP 404 (non trovato).</summary>
<ul>
<li>Nel dominio personalizzato configurato manca un record A o un record CNAME.</li>
<li>Il browser client ha memorizzato nella cache l'indirizzo IP precedente del dominio. Cancellare la cache e testare nuovamente la risoluzione del DNS. In un computer Windows cancellare la cache con <code>ipconfig /flushdns</code>.</li>
</ul>
</details>

<hr/> 

## <a name="migrate-an-active-domain"></a>Eseguire la migrazione di un dominio attivo

Per eseguire la migrazione di un sito live e del relativo nome di dominio DNS al Servizio app senza tempi di inattività, vedere [Eseguire la migrazione di un nome DNS attivo al Servizio app di Azure](manage-custom-dns-migrate-domain.md).

<hr/> 

<a name="virtualdir" aria-hidden="true"></a>

## <a name="redirect-to-a-custom-directory"></a>Reindirizzare a una directory personalizzata

<details>
<summary>Questa operazione è necessaria?</summary>
<p>Dipende dall'app. Per impostazione predefinita, il servizio app indirizza le richieste Web alla directory radice del codice dell'app. Tuttavia, alcuni framework Web Don&#39;t iniziano nella directory radice. Ad esempio, <a href="https://laravel.com/">Laravel</a> viene avviato nella sottodirectory <code>public</code>. Per continuare con l'esempio relativo al DNS <code>contoso.com</code>, un'app di questo tipo è accessibile all'indirizzo <code>http://contoso.com/public</code>, ma si vuole indirizzare l'URL <code>http://contoso.com</code> alla directory <code>public</code>. </p>
</details>

Sebbene si tratta di uno scenario comune, non implica effettivamente il mapping del dominio personalizzato, ma sta per personalizzare la directory virtuale all'interno dell'app.

1. Selezionare **Impostazioni applicazione** nel riquadro sinistro della pagina dell'app Web.

1. Nella parte inferiore della pagina la directory virtuale radice `/` punta per impostazione predefinita a `site\wwwroot`, che è la directory radice del codice dell'app. Modificare il percorso in modo che la directory punti, ad esempio, a `site\wwwroot\public` e salvare le modifiche.

    ![Screenshot che mostra la personalizzazione di una directory virtuale.](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

1. Al termine dell'operazione, verificare passando al percorso radice dell'app nel browser (ad esempio, `http://contoso.com` o `http://<app-name>.azurewebsites.net` ).

<hr/> 

## <a name="automate-with-scripts"></a>Automatizzazione con gli script

È possibile automatizzare la gestione dei domini personalizzati con gli script, usando l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) o [Azure PowerShell](/powershell/azure/).

#### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Il seguente comando aggiunge un nome DNS personalizzato configurato a un'applicazione del servizio app.

```bash 
az webapp config hostname add \
    --webapp-name <app-name> \
    --resource-group <resource_group_name> \
    --hostname <fully_qualified_domain_name>
``` 

Per altre informazioni, vedere [Eseguire il mapping di un dominio personalizzato a un'app Web](scripts/cli-configure-custom-domain.md).

#### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Il seguente comando aggiunge un nome DNS personalizzato configurato a un'applicazione del servizio app.

```powershell  
Set-AzWebApp `
    -Name <app-name> `
    -ResourceGroupName <resource_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app-name>.azurewebsites.net")
```

Per ulteriori informazioni, vedere [Assegnazione di un dominio personalizzato a un’app Web](scripts/powershell-configure-custom-domain.md).

<hr/> 

## <a name="next-steps"></a>Passaggi successivi

Continuare con l'esercitazione successiva per informazioni su come associare un certificato TLS/SSL personalizzato a un'app Web.

> [!div class="nextstepaction"]
> [Proteggere un nome DNS personalizzato con un'associazione TLS/SSL nel Servizio app di Azure](configure-ssl-bindings.md)
