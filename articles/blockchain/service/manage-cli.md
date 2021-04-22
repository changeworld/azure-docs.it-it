---
title: Gestire il servizio Azure Blockchain con l'interfaccia della riga di comando di Azure
description: Come gestire i servizio Azure Blockchain con l'interfaccia della riga di comando di Azure
ms.date: 07/23/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: 55df56274aa5baa946b60c27cf49723d59c928a1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865928"
---
# <a name="manage-azure-blockchain-service-using-azure-cli"></a>Gestire il servizio Azure Blockchain con l'interfaccia della riga di comando di Azure

Oltre al portale di Azure, è possibile usare l'interfaccia della riga di comando di Azure per gestire i membri della blockchain e i nodi di transazione per l'servizio Azure Blockchain.

## <a name="launch-azure-cloud-shell"></a>Avviare Azure Cloud Shell

Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account.

Per aprire Cloud Shell, basta selezionare **Prova** nell'angolo superiore destro di un blocco di codice. È anche possibile avviare Cloud Shell in una scheda separata del browser visitando [https://shell.azure.com/bash](https://shell.azure.com/bash). Selezionare **Copia** per copiare i blocchi di codice, incollarli in Cloud Shell e premere INVIO per eseguirli.

Se si preferisce installare e usare l'interfaccia della riga di comando in locale, vedere [installare l'interfaccia della riga di comando di Azure.](/cli/azure/install-azure-cli)

## <a name="prepare-your-environment"></a>Preparare l'ambiente

1. Accedere.

    Accedere usando il comando [az login](/cli/azure/reference-index#az_login) se si usa un'installazione locale dell'interfaccia della riga di comando.

    ```azurecli
    az login
    ```

    Seguire le istruzioni visualizzate nel terminale per completare il processo di autenticazione.

1. Installare l'estensione dell'interfaccia della riga di comando di Azure.

    Quando si usano riferimenti a estensioni per l'interfaccia della riga di comando di Azure, è prima di tutto necessario installare l'estensione.  Le estensioni dell'interfaccia della riga di comando di Azure offrono l'accesso a comandi sperimentali e non definitivi che non sono stati ancora distribuiti come parte dell'interfaccia della riga di comando di base.  Per altre informazioni sulle estensioni, incluse le procedure di aggiornamento e disinstallazione, vedere [Usare le estensioni con l'interfaccia della riga di comando di Azure](/cli/azure/azure-cli-extensions-overview).

    Installare l'[estensione del servizio Azure Blockchain](/cli/azure/blockchain) eseguendo il comando seguente:

    ```azurecli-interactive
    az extension add --name blockchain
    ```

## <a name="create-blockchain-member"></a>Creare un membro della blockchain

[Nell'esempio viene creato un membro](/cli/azure/blockchain/member#az_blockchain_member_create) della blockchain servizio Azure Blockchain che esegue il protocollo quorum ledger in un nuovo consorzio.

```azurecli
az blockchain member create \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName> \
                            --location <myBlockchainLocation> \
                            --password <strongMemberAccountPassword> \
                            --protocol "Quorum" \
                            --consortium <myConsortiumName> \
                            --consortium-management-account-password <strongConsortiumManagementPassword> \
                            --sku <skuName>
```

| Parametro | Descrizione |
|---------|-------------|
| **resource-group** | Nome del gruppo di risorse in cui vengono create le risorse del servizio Azure Blockchain. |
| **nome** | Nome univoco che identifica il membro della blockchain del servizio Azure Blockchain. Il nome viene usato per l'indirizzo dell'endpoint pubblico. Ad esempio: `myblockchainmember.blockchain.azure.com`. |
| **location** | Area di Azure in cui viene creato il membro della blockchain. Ad esempio: `eastus`. Scegliere la località più vicina agli utenti o alle altre applicazioni Azure. Alcune funzionalità potrebbero non essere disponibili in alcune aree. |
| **password** | La password per il nodo della transazione predefinito del membro. Usare la password per l'autenticazione di base quando ci si connette all'endpoint pubblico del nodo della transazione predefinito per il membro della blockchain. La password deve soddisfare tre dei quattro requisiti seguenti: la lunghezza deve essere compresa tra 12 & 72 caratteri, 1 carattere minuscolo, 1 carattere maiuscolo, 1 numero e 1 carattere speciale che non sia il simbolo di numero(#), percent(%), virgola(,), star(*), virgolette doppie("), virgolette \` singole('), trattino(-) e semicolonna(;)|
| **protocol** | Protocollo Blockchain. Attualmente è supportato il protocollo *Quorum*. |
| **consortium** | Nome del consorzio da creare o a cui eseguire l'aggiunta. Per altre informazioni sui consorzi, vedere [Consorzio del servizio Azure Blockchain](consortium.md). |
| **consortium-management-account-password** | La password dell'account del consorzio è nota anche come password dell'account del membro. La password dell'account del membro viene usata per crittografare la chiave privata per l'account Ethereum creato per il membro. L'account del membro account e la password dell'account del membro si usano per la gestione del consorzio. |
| **sku** | Tipo di livello di servizio. *Standard* o *Basic*. Usare il livello *Basic* per lo sviluppo, il test e i modelli di verifica. Usare il livello *Standard* per le distribuzioni di produzione. È necessario usare anche il livello *Standard* anche se si usa Blockchain Data Manager o si invia un volume elevato di transazioni private. Dopo la creazione di membri non è possibile passare dal piano tariffario Basic a quello Standard. |

## <a name="change-blockchain-member-passwords-or-firewall-rules"></a>Modificare le password dei membri della blockchain o le regole del firewall

L'esempio aggiorna la password di un membro della [blockchain,](/cli/azure/blockchain/member#az_blockchain_member_update)la password di gestione del consorzio e la regola del firewall.

```azurecli
az blockchain member update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --password <strongMemberAccountPassword> \
                     --consortium-management-account-password <strongConsortiumManagementPassword> \
                     --firewall-rules <firewallRules>
```

| Parametro | Descrizione |
|---------|-------------|
| **resource-group** | Nome del gruppo di risorse in cui vengono create le risorse del servizio Azure Blockchain. |
| **nome** | Nome che identifica il servizio Azure Blockchain membro. |
| **password** | La password per il nodo della transazione predefinito del membro. Usare la password per l'autenticazione di base quando ci si connette all'endpoint pubblico del nodo della transazione predefinito per il membro della blockchain. La password deve soddisfare tre dei quattro requisiti seguenti: la lunghezza deve essere compresa tra 12 & 72 caratteri, 1 carattere minuscolo, 1 carattere maiuscolo, 1 numero e 1 carattere speciale che non sia il simbolo di numero(#), percent(%), virgola(,), star(*), virgolette doppie("), virgolette \` singole('), trattino(-) e semicolonna(;)|
| **consortium-management-account-password** | La password dell'account del consorzio è nota anche come password dell'account del membro. La password dell'account del membro viene usata per crittografare la chiave privata per l'account Ethereum creato per il membro. L'account del membro account e la password dell'account del membro si usano per la gestione del consorzio. |
| **firewall-rules** | Indirizzo IP iniziale e finale per l'elenco indirizzi IP consentiti. |

## <a name="create-transaction-node"></a>Creare il nodo di transazioni

[Creare un nodo di transazione](/cli/azure/blockchain/transaction-node#az_blockchain_transaction_node_create) all'interno di un membro della blockchain esistente. Aggiungendo nodi di transazione, è possibile aumentare l'isolamento della sicurezza e distribuire il carico. Ad esempio, è possibile avere un endpoint del nodo della transazione per applicazioni client diverse.

```azurecli
az blockchain transaction-node create \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --password <strongTransactionNodePassword> \
                     --name <myTransactionNodeName>
```

| Parametro | Descrizione |
|---------|-------------|
| **resource-group** | Nome del gruppo di risorse in cui vengono create le risorse del servizio Azure Blockchain. |
| **location** | Area di Azure del membro della blockchain. |
| **member-name** | Nome che identifica il servizio Azure Blockchain membro. |
| **password** | Password per il nodo della transazione. Usare la password per l'autenticazione di base durante la connessione all'endpoint pubblico del nodo della transazione. La password deve soddisfare tre dei quattro requisiti seguenti: la lunghezza deve essere compresa tra 12 & 72 caratteri, 1 carattere minuscolo, 1 carattere maiuscolo, 1 numero e 1 carattere speciale che non sia simbolo di numero(#), percentuale(%), virgola(,), stella(*), virgolette doppie("), virgolette doppie("), virgolette \` singole('), trattino(-) e semicolonna(;)|
| **nome** | Nome del nodo di transazioni. |

## <a name="change-transaction-node-password"></a>Modificare la password del nodo della transazione

[L'esempio aggiorna una](/cli/azure/blockchain/transaction-node#az_blockchain_transaction_node_update) password del nodo della transazione.

```azurecli
az blockchain transaction-node update \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --password <strongTransactionNodePassword> \
                     --name <myTransactionNodeName>
```

| Parametro | Descrizione |
|---------|-------------|
| **resource-group** | Nome del gruppo di risorse servizio Azure Blockchain risorse esistenti. |
| **member-name** | Nome che identifica il servizio Azure Blockchain membro. |
| **password** | Password per il nodo della transazione. Usare la password per l'autenticazione di base durante la connessione all'endpoint pubblico del nodo della transazione. La password deve soddisfare tre dei quattro requisiti seguenti: la lunghezza deve essere compresa tra 12 & 72 caratteri, 1 carattere minuscolo, 1 carattere maiuscolo, 1 numero e 1 carattere speciale che non sia simbolo di numero(#), percentuale(%), virgola(,), stella(*), virgolette doppie("), virgolette doppie("), virgolette \` singole('), trattino(-) e semicolonna(;)|
| **nome** | Nome del nodo di transazioni. |

## <a name="list-api-keys"></a>Elencare le chiavi API

Le chiavi API possono essere usate per l'accesso ai nodi in modo simile a nome utente e password. Sono disponibili due chiavi API per supportare la rotazione delle chiavi. Usare il comando seguente per [elencare le chiavi API](/cli/azure/blockchain/member#az_blockchain_transaction_node_list-api-key).

```azurecli
az blockchain member list-api-key \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>
```

| Parametro | Descrizione |
|---------|-------------|
| **resource-group** | Nome del gruppo di risorse servizio Azure Blockchain risorse esistenti. |
| **nome** | Nome del membro servizio Azure Blockchain blockchain |

## <a name="regenerate-api-keys"></a>Rigenerare le chiavi API

Usare il comando seguente per [rigenerare le chiavi API](/cli/azure/blockchain/member#az_blockchain_transaction_node_regenerate-api-key).

```azurecli
az blockchain member regenerate-api-key \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName> \
                            [--key-name {<keyValue1>, <keyValue2>}]
```

| Parametro | Descrizione |
|---------|-------------|
| **resource-group** | Nome del gruppo di risorse servizio Azure Blockchain risorse esistenti. |
| **nome** | Nome del membro servizio Azure Blockchain blockchain. |
| **Keyname** | Sostituire \<keyValue\> con key1, key2 o entrambi. |

## <a name="delete-a-transaction-node"></a>Eliminare un nodo di transazione

[Nell'esempio viene eliminato un nodo di transazione membro della blockchain](/cli/azure/blockchain/transaction-node#az_blockchain_transaction_node_delete).

```azurecli
az blockchain transaction-node delete \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --name <myTransactionNode>
```

| Parametro | Descrizione |
|---------|-------------|
| **resource-group** | Nome del gruppo di risorse in servizio Azure Blockchain risorse. |
| **member-name** | Nome del servizio Azure Blockchain blockchain che include anche il nome del nodo della transazione da eliminare. |
| **nome** | Nome del nodo della transazione da eliminare. |

## <a name="delete-a-blockchain-member"></a>Eliminare un membro della blockchain

[Nell'esempio viene eliminato un membro della blockchain](/cli/azure/blockchain/member#az_blockchain_member_delete).

```azurecli
az blockchain member delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>

```

| Parametro | Descrizione |
|---------|-------------|
| **resource-group** | Nome del gruppo di risorse in servizio Azure Blockchain risorse. |
| **nome** | Nome del servizio Azure Blockchain blockchain da eliminare. |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Concedere l'accesso per Azure AD utente

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee <assignee> \
                            --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| Parametro | Descrizione |
|---------|-------------|
| **ruolo** | Nome del ruolo Azure AD ruolo. |
| **assignee** | Azure AD ID utente. Ad esempio: `user@contoso.com` |
| **ambito** | Ambito dell'assegnazione di ruolo. Può essere un membro della blockchain o un nodo di transazione. |

**Esempio:**

Concedere l'accesso al nodo per Azure AD utente a **un** membro della blockchain:

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**Esempio:**

Concedere l'accesso al nodo Azure AD'utente al nodo **della transazione** blockchain:

```azurecli
az role assignment create \
                            --role 'MyRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Concedere l'accesso al nodo per Azure AD gruppo o ruolo applicazione

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee-object-id <assignee_object_id>
```

| Parametro | Descrizione |
|---------|-------------|
| **ruolo** | Nome del ruolo Azure AD ruolo. |
| **assignee-object-id** | Azure AD ID gruppo o ID applicazione. |
| **ambito** | Ambito dell'assegnazione di ruolo. Può essere un membro della blockchain o un nodo di transazione. |

**Esempio:**

Concedere l'accesso al nodo per il **ruolo applicazione**

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee-object-id 22222222-2222-2222-2222-222222222222 \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Rimuovere l Azure AD ai nodi

```azurecli
az role assignment delete \
                            --role <myRole> \
                            --assignee <assignee> \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| Parametro | Descrizione |
|---------|-------------|
| **ruolo** | Nome del ruolo Azure AD servizio. |
| **assignee** | Azure AD'ID utente. Ad esempio: `user@contoso.com` |
| **ambito** | Ambito dell'assegnazione di ruolo. Può essere un membro della blockchain o un nodo di transazione. |

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [configurare i servizio Azure Blockchain transazione con il portale di Azure](configure-transaction-nodes.md).
