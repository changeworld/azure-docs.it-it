---
title: Configurare le chiavi gestite dal cliente per l'API di Azure per FHIR
description: La funzionalità Bring Your Own Key (BYOK) è supportata nell'API di Azure per FHIR tramite Cosmos DB
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 09/28/2020
ms.author: ginle
ms.openlocfilehash: ae78aa80594e46b02d77adcafed961e801780d4f
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430260"
---
# <a name="configure-customer-managed-keys-at-rest"></a>Configurare chiavi gestite dal cliente inattive

Quando si crea un nuovo account dell'API di Azure per FHIR, per impostazione predefinita i dati vengono crittografati usando chiavi gestite da Microsoft. A questo punto, è possibile aggiungere un secondo livello di crittografia per i dati usando una chiave personalizzata che si sceglie e si gestisce in autonomia.

In Azure questa operazione viene in genere eseguita usando una chiave di crittografia nel Azure Key Vault del cliente. SQL di Azure, Archiviazione di Azure e Cosmos DB sono alcuni esempi che attualmente forniscono questa funzionalità. L'API di Azure per FHIR sfrutta questo supporto da Cosmos DB. Quando si crea un account, è possibile specificare un URI chiave Azure Key Vault. Questa chiave verrà passata al Cosmos DB quando viene effettuato il provisioning dell'account di database. Quando viene inviata una richiesta a FHIR, Cosmos DB recupera la chiave e la usa per crittografare/decrittografare i dati. Per iniziare, vedere i collegamenti seguenti:

- [Registrare il provider di risorse Azure Cosmos DB per la sottoscrizione di Azure](../cosmos-db/how-to-setup-cmk.md#register-resource-provider) 
- [Configurare l'istanza di Azure Key Vault](../cosmos-db/how-to-setup-cmk.md#configure-your-azure-key-vault-instance)
- [Aggiungere un criterio di accesso all'istanza di Azure Key Vault](../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance)
- [Generare una chiave in Azure Key Vault](../cosmos-db/how-to-setup-cmk.md#generate-a-key-in-azure-key-vault)

## <a name="specify-the-azure-key-vault-key"></a>Specificare la chiave di Azure Key Vault

Quando si crea l'account dell'API di Azure per FHIR in portale di Azure, è possibile visualizzare un'opzione di configurazione "crittografia dei dati" in "Impostazioni database" nella scheda "impostazioni aggiuntive". Per impostazione predefinita, verrà scelta l'opzione chiave gestita dal servizio. 

È possibile scegliere la chiave dal tasto di scelta:

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-keypicker.png" alt-text="Selezione chiavi":::

In alternativa, è possibile specificare la chiave di Azure Key Vault selezionando l'opzione "chiave gestita dal cliente". È possibile immettere l'URI della chiave qui:

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-create.png" alt-text="Creare l'API di Azure per FHIR":::

Per gli account FHIR esistenti, è possibile visualizzare la scelta relativa alla chiave di crittografia (chiave gestita dal servizio o dal cliente) nel pannello "Database" come illustrato di seguito. Non è possibile modificare l'opzione di configurazione una volta scelta. Sarà tuttavia possibile modificare e aggiornare la chiave.

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-database.png" alt-text="Database":::

È inoltre possibile creare una nuova versione della chiave specificata. Successivamente i dati verranno crittografati con la nuova versione senza interruzioni del servizio. Si può anche rimuovere l'accesso alla chiave per rimuovere l'accesso ai dati. Quando la chiave è disabilitata, le query generano un errore. Se la chiave è riabilitata, le query risulteranno riuscite.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come configurare le chiavi gestite dal cliente inattive. Successivamente, è possibile consultare la sezione relativa alle domande frequenti Azure Cosmos DB: 
 
>[!div class="nextstepaction"]
>[Cosmos DB: come configurare CMK](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk#frequently-asked-questions)
