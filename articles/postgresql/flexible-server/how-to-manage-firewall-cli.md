---
title: Gestire le regole del firewall - Interfaccia della riga di comando di Azure - Database di Azure per PostgreSQL - Server flessibile
description: Creare e gestire le regole del firewall per il server flessibile di Database di Azure per PostgreSQL tramite l'interfaccia della riga di comando di Azure.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 09/22/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: beed3dac1a2ca5bc6d2a87ba2a9044333e798fa9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778518"
---
# <a name="create-and-manage-azure-database-for-postgresql---flexible-server-firewall-rules-using-the-azure-cli"></a>Creare e gestire le regole del firewall del server flessibile di Database di Azure per PostgreSQL usando l'interfaccia della riga di comando di Azure

> [!IMPORTANT]
> Il server flessibile di Database di Azure per PostgreSQL è disponibile in anteprima

Database di Azure per PostgreSQL : il server flessibile supporta due tipi di metodi di connettività di rete che si escludono a vicenda per connettersi al server flessibile. Le due opzioni sono:

* Accesso pubblico (indirizzi IP consentiti)
* Accesso privato (integrazione rete virtuale)

In questo articolo verrà illustrata la creazione del server PostgreSQL con accesso pubblico **(indirizzi IP consentiti)** tramite l'interfaccia della riga di comando di Azure e verrà fornita una panoramica dei comandi dell'interfaccia della riga di comando di Azure che è possibile usare per creare, aggiornare, eliminare, elencare e visualizzare le regole del firewall dopo la creazione del server. Con *l'accesso pubblico (indirizzi IP consentiti),* le connessioni al server PostgreSQL sono limitate solo agli indirizzi IP consentiti. Gli indirizzi IP del client devono essere consentiti nelle regole del firewall. Per altre informazioni, vedere Accesso [pubblico (indirizzi IP consentiti).](./concepts-networking.md#public-access-allowed-ip-addresses) Le regole del firewall possono essere definite al momento della creazione del server (scelta consigliata), ma possono essere aggiunte anche in un secondo momento.

## <a name="launch-azure-cloud-shell"></a>Avviare Azure Cloud Shell

[Azure Cloud Shell](../../cloud-shell/overview.md) è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account.

Per aprire Cloud Shell, basta selezionare **Prova** nell'angolo superiore destro di un blocco di codice. È anche possibile aprire Cloud Shell in una scheda separata del browser visitando [https://shell.azure.com/bash](https://shell.azure.com/bash). Selezionare **Copia** per copiare i blocchi di codice, incollarli in Cloud Shell e premere **INVIO** per eseguirli.

Se si preferisce installare e usare l'interfaccia della riga di comando in locale, per questo argomento di avvio rapido è necessaria l'interfaccia della riga di comando di Azure versione 2.0 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Prerequisiti

È necessario accedere all'account con il comando [az login](/cli/azure/reference-index#az_login). Si noti la **proprietà ID,** che fa riferimento **all'ID sottoscrizione** per l'account Azure.

```azurecli-interactive
az login
```

Selezionare la sottoscrizione specifica nell'account tramite il comando [az account set](/cli/azure/account#az_account_set). Prendere nota del valore **ID** dell'output **di az login** da usare come valore per l'argomento **subscription** nel comando. Se si possiedono più sottoscrizioni, scegliere quella appropriata in cui verrà fatturata la risorsa. Per ottenere tutte le sottoscrizioni, usare [az account list](/cli/azure/account#az_account_list).

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-firewall-rule-during-flexible-server-create-using-azure-cli"></a>Creare una regola del firewall durante la creazione di server flessibili con l'interfaccia della riga di comando di Azure

È possibile usare il comando per creare il server flessibile con accesso pubblico (indirizzi IP consentiti) e configurare le regole del firewall durante la `az postgres flexible-server --public access` creazione di un server flessibile.  È possibile usare **l'opzione --public-access** per fornire gli indirizzi IP consentiti che potranno connettersi al server. È possibile specificare un singolo o un intervallo di indirizzi IP da includere nell'elenco di indirizzi IP consentiti. L'intervallo di indirizzi IP deve essere separato da trattini e non contiene spazi. Sono disponibili diverse opzioni per creare un server flessibile usando l'interfaccia della riga di comando, come illustrato nell'esempio seguente.

Vedere la documentazione di riferimento dell'interfaccia della riga di comando di Azure <!--FIXME --> per avere l'elenco completo dei parametri configurabili dell'interfaccia della riga di comando. Ad esempio, nei comandi seguenti è possibile specificare facoltativamente il gruppo di risorse.

- Creare un server flessibile con accesso pubblico e aggiungere l'indirizzo IP client per accedere al server
    ```azurecli-interactive
    az postgres flexible-server create --public-access <my_client_ip>
    ```
- Creare un server flessibile con accesso pubblico e aggiungere l'intervallo di indirizzi IP per accedere a questo server

    ```azurecli-interactive
    az postgres flexible-server create --public-access <start_ip_address-end_ip_address>
    ```
- Creare un server flessibile con accesso pubblico e consentire alle applicazioni dagli indirizzi IP di Azure di connettersi al server flessibile
    ```azurecli-interactive
    az postgres flexible-server create --public-access 0.0.0.0
    ```
    > [!IMPORTANT]
    > Questa opzione configura il firewall per consentire l'accesso pubblico dai servizi e dalle risorse di Azure in Azure a questo server, incluse le connessioni dalle sottoscrizioni di altri clienti. Quando si seleziona questa opzione, verificare che l'account di accesso e le autorizzazioni utente limitino l'accesso ai soli utenti autorizzati.
    >
- - Creare un server flessibile con accesso pubblico e consentire tutti gli indirizzi IP
    ```azurecli-interactive
    az postgres flexible-server create --public-access all
    ```
    >[!Note]
    > Il comando precedente creerà una regola del firewall con indirizzo IP iniziale=0.0.0.0, indirizzo IP finale=255.255.255.255 e nessun indirizzo IP verrà bloccato. Qualsiasi host su Internet può accedere a questo server. È consigliabile usare questa regola solo temporaneamente e solo nei server di test che non contengono dati sensibili.
- Creare un server flessibile con accesso pubblico e senza indirizzo IP
    ```azurecli-interactive
    az postgres flexible-server create --public-access none
    ```
    >[!Note]
    > Non è consigliabile creare un server senza regole del firewall. Se non si aggiungono regole del firewall, nessun client sarà in grado di connettersi al server.
## <a name="create-and-manage-firewall-rule-after-server-create"></a>Creare e gestire la regola del firewall dopo la creazione del server
Il **comando az postgres flexible-server firewall-rule** viene usato dall'interfaccia della riga di comando di Azure per creare, eliminare, elencare, visualizzare e aggiornare le regole del firewall.

Comandi:
- **create:** creare una regola del firewall del server flessibile.
- **list**: elencare le regole del firewall del server flessibili.
- **update:** aggiornare una regola del firewall del server flessibile.
- **show**: mostra i dettagli di una regola del firewall del server flessibile.
- **delete**: elimina una regola del firewall del server flessibile.

Vedere la documentazione di riferimento dell'interfaccia della riga di comando di Azure <!--FIXME --> per avere l'elenco completo dei parametri configurabili dell'interfaccia della riga di comando. Ad esempio, nei comandi seguenti è possibile specificare facoltativamente il gruppo di risorse.

### <a name="create-a-firewall-rule"></a>Creare una regola del firewall
Usare il `az postgres flexible-server firewall-rule create` comando per creare una nuova regola del firewall nel server.
Per consentire l'accesso a un intervallo di indirizzi IP, specificare l'indirizzo IP come Indirizzo IP iniziale e Indirizzo IP finale, come in questo esempio.
```azurecli-interactive
az postgres flexible-server firewall-rule create --name mydemoserver --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Per consentire l'accesso per un singolo indirizzo IP, è sufficiente specificare un singolo indirizzo IP, come in questo esempio.
```azurecli-interactive
az postgres flexible-server firewall-rule create --name mydemoserver --start-ip-address 1.1.1.1
```

Per consentire alle applicazioni da indirizzi IP di Azure di connettersi al server flessibile, specificare l'indirizzo IP 0.0.0.0 come INDIRIZZO IP iniziale, come in questo esempio.
```azurecli-interactive
az postgres flexible-server firewall-rule create --name mydemoserver --start-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Questa opzione configura il firewall per consentire l'accesso pubblico da servizi e risorse di Azure in Azure a questo server, incluse le connessioni dalle sottoscrizioni di altri clienti. Quando si seleziona questa opzione, verificare che l'account di accesso e le autorizzazioni utente limitino l'accesso ai soli utenti autorizzati.
> 
Al termine dell'operazione, l'output di ogni comando create elenca i dettagli della regola del firewall creata, in formato JSON (per impostazione predefinita). Se si verifica un errore, l'output visualizza invece il testo di un messaggio di errore.

### <a name="list-firewall-rules"></a>Elencare le regole del firewall 
Usare il `az postgres flexible-server firewall-rule list` comando per elencare le regole del firewall del server esistenti nel server. Si noti che l'attributo del nome del server è specificato **nell'opzione --name.** 
```azurecli-interactive
az postgres flexible-server firewall-rule list --name mydemoserver
```
L'output elenca le eventuali regole presenti in formato JSON (per impostazione predefinita). È possibile usare l'opzione --output table** per visualizzare i risultati in un formato di tabella più leggibile.
```azurecli-interactive
az postgres flexible-server firewall-rule list --name mydemoserver --output table
```

### <a name="update-a-firewall-rule"></a>Aggiornare una regola del firewall
Usare il `az postgres flexible-server firewall-rule update` comando per aggiornare una regola del firewall esistente nel server. Specificare il nome della regola del firewall esistente come input, nonché gli attributi dell'indirizzo IP iniziale e dell'indirizzo IP finale da aggiornare.
```azurecli-interactive
az postgres flexible-server firewall-rule update --name mydemoserver --rule-name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Al termine dell'operazione, l'output del comando elenca i dettagli della regola del firewall aggiornata, in formato JSON (per impostazione predefinita). Se si verifica un errore, l'output visualizza invece il testo di un messaggio di errore.

> [!NOTE]
> Se la regola del firewall non esiste, viene creata dal comando di aggiornamento.
### <a name="show-firewall-rule-details"></a>Visualizzare i dettagli di una regola del firewall
Usare il `az postgres flexible-server firewall-rule show` comando per visualizzare i dettagli delle regole del firewall esistenti dal server. Specificare il nome della regola del firewall esistente come input.
```azurecli-interactive
az postgres flexible-server firewall-rule show --name mydemoserver --rule-name FirewallRule1
```
Al termine dell'operazione, l'output del comando elenca i dettagli della regola del firewall specificata, in formato JSON (per impostazione predefinita). Se si verifica un errore, l'output visualizza invece il testo di un messaggio di errore.

### <a name="delete-a-firewall-rule"></a>Eliminare una regola del firewall
Usare il `az postgres flexible-server firewall-rule delete` comando per eliminare una regola del firewall esistente dal server. Specificare il nome della regola del firewall esistente.
```azurecli-interactive
az postgres flexible-server firewall-rule delete --name mydemoserver --rule-name FirewallRule1
```
Al completamento dell'operazione non verrà visualizzato alcun output. In caso di errore, viene visualizzato il testo di un messaggio di errore.

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulla rete nel server flessibile di Database di [Azure per PostgreSQL](./concepts-networking.md)
- Altre informazioni sulle regole del firewall del server flessibile di Database di Azure per [PostgreSQL](./concepts-networking.md#public-access-allowed-ip-addresses)
- Creare e gestire le regole del firewall del server flessibile di Database di Azure per [PostgreSQL usando portale di Azure](./how-to-manage-firewall-portal.md).
