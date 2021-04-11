---
title: Accesso al server di configurazione e al registro del servizio
titleSuffix: Azure Spring Cloud
description: Come accedere agli endpoint del registro di sistema del server e del servizio di configurazione con Azure Active Directory controllo degli accessi in base al ruolo.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 02/04/2021
ms.custom: devx-track-java
ms.openlocfilehash: 16433d5b148d7bc441e375591c64af497cd7b8de
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106505333"
---
# <a name="access-config-server-and-service-registry"></a>Accesso al server di configurazione e al registro del servizio

Questo articolo illustra come accedere al server Spring cloud config e al registro di sistema Spring Cloud Services gestito dal cloud Spring di Azure usando il controllo degli accessi in base al ruolo Azure Active Directory (Azure AD).

## <a name="assign-role-to-azure-ad-usergroup-msi-or-service-principal"></a>Assegnare un ruolo a Azure AD utente/gruppo, MSI o entità servizio

Per usare il Azure AD e il controllo degli accessi in base al ruolo è necessario assegnare il ruolo di *lettore dati di Azure Spring cloud* a un utente, un gruppo o un'entità servizio attenendosi alla procedura seguente

1. Passare alla pagina Panoramica del servizio dell'istanza del servizio.

2. Fare clic su **controllo di accesso (IAM)** per aprire il pannello controllo di accesso.

3. Fare clic sul pulsante **Aggiungi** e **aggiungere assegnazioni di ruolo** . potrebbe essere necessario l'autorizzazione per aggiungere.

4. Trovare e selezionare *Azure Spring cloud Data Reader* in **Role**.
5. Assegnare l'accesso a `User, group, or service principal` o in `User assigned managed identity` base al tipo di utente. Cercare e selezionare User.  
6. Fare clic su`Save`.

   ![assign-role](media/access-data-plane-aad-rbac/assign-data-reader-role.png)

## <a name="access-config-server-and-service-registry-endpoints"></a>Accesso agli endpoint del registro di sistema e del server di configurazione

Dopo l'assegnazione del ruolo di lettore dati della molla cloud di Azure, i clienti possono accedere al server di configurazione Spring cloud e agli endpoint del registro di sistema Spring Cloud Service. Usare le procedure seguenti:

1. Ottenere un token di accesso. Dopo che un utente Azure AD viene assegnato al ruolo di lettore dati di Azure Spring cloud, i clienti possono usare i comandi seguenti per accedere all'interfaccia della riga di comando di Azure con l'utente, l'entità servizio o l'identità gestita per ottenere un token di accesso. Per informazioni dettagliate, vedere [autenticare l'interfaccia](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)della riga di comando di Azure. 

    ```azurecli
    az login
    az account get-access-token
    ```
2. Comporre l'endpoint. Sono supportati gli endpoint predefiniti del server Spring cloud config e del registro di sistema Spring Cloud Service gestiti dal cloud Spring di Azure. Per altre informazioni, vedere [endpoint pronti](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)per la produzione. I clienti possono anche ottenere un elenco completo degli endpoint supportati del server Spring cloud config e del registro di sistema del servizio cloud Spring gestito dal cloud Spring di Azure accedendo agli endpoint:

    * *https://SERVICE_NAME.svc.azuremicroservices.io/eureka/actuator/*
    * *https://SERVICE_NAME.svc.azuremicroservices.io/config/actuator/* 

>[!NOTE]
> Se si usa Azure per la Cina, sostituire `*.azuremicroservices.io` con `*.microservices.azure.cn` , [altre informazioni](https://docs.microsoft.com/azure/china/resources-developer-guide#check-endpoints-in-azure).

3. Accedere all'endpoint composto con il token di accesso. Inserire il token di accesso in un'intestazione per fornire l'autorizzazione.  È supportato solo il metodo "GET".

    Ad esempio, accedere a un endpoint come *https://SERVICE_NAME.svc.azuremicroservices.io/eureka/actuator/health* per visualizzare lo stato di integrità di Eureka.

    Se la risposta è *401 non autorizzata*, verificare che il ruolo sia stato assegnato correttamente.  Saranno necessari alcuni minuti per rendere effettivo il ruolo o verificare che il token di accesso non sia scaduto.

## <a name="next-steps"></a>Passaggi successivi
* [Autenticazione dell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)
* [Endpoint pronti per la produzione](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)

## <a name="see-also"></a>Vedi anche
* [Creare ruoli e autorizzazioni](how-to-permissions.md)
