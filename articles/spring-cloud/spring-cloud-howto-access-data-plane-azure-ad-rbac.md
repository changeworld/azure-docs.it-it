---
title: Accedere al piano dati con Azure AD, RBAC
description: Come accedere al piano dati con Azure Active Directory il controllo degli accessi in base al ruolo.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 02/04/2021
ms.custom: devx-track-java
ms.openlocfilehash: 2909d40fbc7cb5b310ea17f17a6e683ce47638ce
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102220134"
---
# <a name="access-the-data-plane-with-azure-active-directory-and-role-based-access-control"></a>Accedere al piano dati con Azure Active Directory e il controllo degli accessi in base al ruolo

Questo articolo illustra come accedere al server di configurazione cloud di Azure Spring e agli endpoint del registro di sistema del servizio usando il controllo degli accessi in base al ruolo (RBAC) di Azure Active Directory (AAD).

## <a name="assign-role-to-aad-usergroup-msi-or-service-principal"></a>Assegnare il ruolo a un utente/gruppo, MSI o entità servizio di AAD

Per usare AAD e RBAC, è necessario assegnare il ruolo di *lettore dati della molla cloud di Azure* a un utente, un gruppo o un'entità servizio attenendosi alla procedura seguente:

1. Passare alla pagina Panoramica del servizio dell'istanza del servizio.

2. Fare clic su **controllo di accesso (IAM)** per aprire il pannello controllo di accesso.

3. Fare clic sul pulsante **Aggiungi** e **aggiungere assegnazioni di ruolo** . potrebbe essere necessario l'autorizzazione per aggiungere.

4. Trovare e selezionare *Azure Spring cloud Data Reader* in **Role**.
5. Assegnare l'accesso a `User, group, or service principal` o in `User assigned managed identity` base al tipo di utente. Cercare e selezionare User.  
6. Fare clic su`Save`.

   ![assign-role](media/access-data-plane-aad-rbac/assign-data-reader-role.png)

## <a name="access-data-plane"></a>Accedere al piano dati

Quando a un utente di AAD viene assegnato il ruolo di *lettore dati di Azure Spring cloud* , i clienti possono accedere all'interfaccia della riga di comando di Azure con l'utente, l'entità servizio o l'identità gestita.  Vedere [autenticare l'interfaccia](https://docs.microsoft.com/cli/azure/authenticate-azure-cli) della riga di comando di Azure per ottenere un token di accesso.  Usare quindi i comandi seguenti.

```azurecli
az login
az account get-access-token
```

Attualmente l'interfaccia della riga di comando supporta gli endpoint predefiniti del server di configurazione e del registro di sistema del servizio. Per altre informazioni, vedere [endpoint pronti](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)per la produzione. 

I clienti possono anche ottenere un elenco completo degli endpoint supportati del server di configurazione e del registro dei servizi accedendo agli endpoint:
* *https://SERVICE_NAME.Root_Endpoint/eureka/actuator/*
* *https://SERVICE_NAME.Root_Endpoint/config/actuator/* 

Il token di accesso nell'intestazione fornisce l'autorizzazione. È supportato solo il metodo "GET".

Ad esempio, accedere a un endpoint come *https://SERVICE_NAME.Root_Endpoint/eureka/actuator/health* per visualizzare lo stato di integrità di Eureka.

Di seguito sono riportati diversi endpoint radice in base ai diversi tipi di cloud.

| Cloud          | Endpoint radice              |
| -------------- | -------------------------- |
| Pubblico         | svc.azuremicroservices.io  |
| Mooncake/Cina | svc.microservices.azure.cn |

Se la risposta è *401 non autorizzata*, verificare che il ruolo sia stato assegnato correttamente.  Saranno necessari alcuni minuti per rendere effettivo il ruolo o verificare che il token di accesso non sia scaduto.

## <a name="next-steps"></a>Passaggi successivi
* [Autenticazione dell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)
* [Endpoint pronti per la produzione](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)

## <a name="see-also"></a>Vedi anche
* [Creare ruoli e autorizzazioni](spring-cloud-howto-permissions.md)