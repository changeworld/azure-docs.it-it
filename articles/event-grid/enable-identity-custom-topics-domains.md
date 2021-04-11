---
title: Abilitare l'identità gestita negli argomenti e nei domini personalizzati di griglia di eventi di Azure
description: Questo articolo descrive come abilitare l'identità del servizio gestito per un argomento o un dominio personalizzato di griglia di eventi di Azure.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: b93fd44282d9e19e7111dd52c73d8d4c01c67a10
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278219"
---
# <a name="assign-a-system-managed-identity-to-an-event-grid-custom-topic-or-domain"></a>Assegnare un'identità gestita dal sistema a un dominio o a un argomento personalizzato di griglia di eventi 
Questo articolo illustra come abilitare un'identità gestita dal sistema per un argomento personalizzato di griglia di eventi o un dominio. Per informazioni sulle identità gestite, vedere [che cosa sono le identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="enable-identity-at-the-time-of-creation"></a>Abilitare l'identità al momento della creazione

### <a name="using-azure-portal"></a>Uso del portale di Azure
È possibile abilitare l'identità assegnata dal sistema per un argomento personalizzato o un dominio durante la creazione nel portale di Azure. Nell'immagine seguente viene illustrato come abilitare un'identità gestita dal sistema per un argomento personalizzato. In pratica, selezionare l'opzione **Abilita identità assegnata dal sistema** nella pagina **Avanzate** della procedura guidata di creazione dell'argomento. Questa opzione verrà visualizzata nella pagina **Avanzate** della creazione guidata dominio. 

![Abilitare l'identità durante la creazione di un argomento personalizzato](./media/managed-service-identity/create-topic-identity.png)

### <a name="using-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure
È anche possibile usare l'interfaccia della riga di comando di Azure per creare un argomento o un dominio personalizzato con un'identità assegnata dal sistema. Usare il comando `az eventgrid topic create` con il parametro `--identity` impostato su `systemassigned`. Se non si specifica un valore per questo parametro, viene usato il valore predefinito `noidentity`. 

```azurecli-interactive
# create a custom topic with a system-assigned identity
az eventgrid topic create -g <RESOURCE GROUP NAME> --name <TOPIC NAME> -l <LOCATION>  --identity systemassigned
```

Analogamente, è possibile usare il comando `az eventgrid domain create` per creare un dominio con un'identità gestita dal sistema.

## <a name="enable-identity-for-an-existing-custom-topic-or-domain"></a>Abilitare l'identità per un argomento o un dominio personalizzato esistente
In questa sezione viene illustrato come abilitare un'identità gestita dal sistema per un argomento o un dominio personalizzato esistente. 

### <a name="using-azure-portal"></a>Uso del portale di Azure
La procedura seguente illustra come abilitare l'identità gestita dal sistema per un argomento personalizzato. I passaggi per l'abilitazione di un'identità per un dominio sono simili. 

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Cercare gli **argomenti di griglia di eventi** nella barra di ricerca nella parte superiore.
3. Selezionare l' **argomento personalizzato** per il quale si desidera abilitare l'identità gestita. 
4. Passare alla scheda **Identità**. 
5. Attivare l'opzione per **abilitare l'identità** . 
1. Selezionare **Salva** sulla barra degli strumenti per salvare l'impostazione. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-topic.png" alt-text="Pagina identità per un argomento personalizzato"::: 

È possibile utilizzare passaggi simili per abilitare un'identità per un dominio di griglia di eventi.

### <a name="use-the-azure-cli"></a>Utilizzare l’interfaccia della riga di comando di Azure
Usare il `az eventgrid topic update` comando con `--identity` impostato su `systemassigned` per abilitare l'identità assegnata dal sistema per un argomento personalizzato esistente. Se si vuole disabilitare l'identità, specificare il valore `noidentity`. 

```azurecli-interactive
# Update the topic to assign a system-assigned identity. 
az eventgrid topic update -g $rg --name $topicname --identity systemassigned --sku basic 
```

Il comando per l'aggiornamento di un dominio esistente è simile (`az eventgrid domain update`).


## <a name="next-steps"></a>Passaggi successivi
Aggiungere l'identità a un ruolo appropriato, ad esempio il mittente dei dati del bus di servizio, nella destinazione, ad esempio una coda del bus di servizio. Per i passaggi dettagliati, vedere [concedere a identità gestita l'accesso alla destinazione di griglia di eventi](add-identity-roles.md). 
