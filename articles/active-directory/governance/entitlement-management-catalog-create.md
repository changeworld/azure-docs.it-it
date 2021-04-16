---
title: Creare & gestire un catalogo di risorse nella gestione entitlement - Azure AD
description: Informazioni su come creare un nuovo contenitore di risorse e accedere ai pacchetti in Azure Active Directory entitlement.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: ajburnle
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8cea26bcb0926cd3af360a6489377767d681079
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532549"
---
# <a name="create-and-manage-a-catalog-of-resources-in-azure-ad-entitlement-management"></a>Creare e gestire un catalogo di risorse nella Azure AD entitlement

## <a name="create-a-catalog"></a>Creare un catalogo

Un catalogo è un contenitore di risorse e pacchetti di accesso. Si crea un catalogo quando si desidera raggruppare le risorse correlate e accedere ai pacchetti. Chiunque crei il catalogo diventa il primo proprietario del catalogo. Un proprietario del catalogo può aggiungere altri proprietari del catalogo.

**Ruolo prerequisito:** amministratore globale, Amministratore utenti o Autore del catalogo

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic **su Cataloghi.**

    ![Cataloghi di gestione entitlement nel portale di Azure](./media/entitlement-management-catalog-create/catalogs.png)

1. Fare clic **su Nuovo catalogo**.

1. Immettere un nome univoco per il catalogo e specificare una descrizione.

    Gli utenti visualizzano queste informazioni nei dettagli di un pacchetto di accesso.

1. Se si vuole che i pacchetti di accesso in questo catalogo siano disponibili per la richiesta da parte degli utenti non appena vengono creati, impostare **Abilitato** su **Sì.**

1. Se si vuole consentire agli utenti in directory esterne selezionate di richiedere pacchetti di accesso in questo catalogo, impostare Abilitato per gli utenti **esterni** su **Sì.**

    ![Riquadro Nuovo catalogo](./media/entitlement-management-shared/new-catalog.png)

1. Fare **clic su** Crea per creare il catalogo.

### <a name="creating-a-catalog-programmatically"></a>Creazione di un catalogo a livello di codice

È anche possibile creare un catalogo usando Microsoft Graph.  Un utente con un ruolo appropriato con un'applicazione che dispone dell'autorizzazione delegata può chiamare l'API per `EntitlementManagement.ReadWrite.All` [creare un accessPackageCatalog](/graph/api/accesspackagecatalog-post?view=graph-rest-beta&preserve-view=true).

## <a name="add-resources-to-a-catalog"></a>Aggiungere risorse a un catalogo

Per includere le risorse in un pacchetto di accesso, è necessario che le risorse esistano in un catalogo. I tipi di risorse che è possibile aggiungere sono gruppi, applicazioni e siti di SharePoint Online. I gruppi possono essere creati nel cloud Gruppi di Microsoft 365 o creati nel cloud Azure AD di sicurezza. Le applicazioni possono essere Azure AD aziendali, incluse le applicazioni SaaS e le applicazioni personalizzate federate Azure AD. I siti possono essere siti di SharePoint Online o raccolte siti di SharePoint Online.

**Ruolo prerequisito:** Vedere [Ruoli necessari per aggiungere risorse a un catalogo](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic **su Cataloghi** e quindi aprire il catalogo a cui si vogliono aggiungere risorse.

1. Nel menu a sinistra fare clic su **Risorse**.

1. Fare clic **su Aggiungi risorse**.

1. Fare clic su un tipo di risorsa: **Gruppi e Team,** **Applicazioni** o Siti **di SharePoint.**

    Se non viene visualizzata una risorsa che si vuole aggiungere o non è possibile aggiungere una risorsa, assicurarsi di disporre del ruolo di gestione della directory e dell'entitlement Azure AD obbligatorio. Potrebbe essere necessario avere un utente con i ruoli necessari per aggiungere la risorsa al catalogo. Per altre informazioni, vedere [Ruoli necessari per aggiungere risorse a un catalogo](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog).

1. Selezionare una o più risorse del tipo da aggiungere al catalogo.

    ![Aggiungere risorse a un catalogo](./media/entitlement-management-catalog-create/catalog-add-resources.png)

1. Al termine, fare clic **su Aggiungi**.

    Queste risorse possono ora essere incluse nei pacchetti di accesso all'interno del catalogo.

### <a name="add-a-multi-geo-sharepoint-site-preview"></a>Aggiungere un sito di SharePoint multi-geo (anteprima)

1. Se è [abilitata l'opzione Multi-Geo](/microsoft-365/enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-microsoft-365) per SharePoint, selezionare l'ambiente da cui si desidera selezionare i siti.
    
    :::image type="content" source="media/entitlement-management-catalog-create/sharepoint-multigeo-select.png" alt-text="Pacchetto di accesso - Aggiungi ruoli risorsa - Selezionare Siti multi-area geografica di SharePoint":::

1. Selezionare quindi i siti da aggiungere al catalogo. 

### <a name="adding-a-resource-to-a-catalog-programmatically"></a>Aggiunta di una risorsa a un catalogo a livello di codice

È anche possibile aggiungere una risorsa a un catalogo usando Microsoft Graph.  Un utente con un ruolo appropriato, o un catalogo e un proprietario di risorse, con un'applicazione con l'autorizzazione delegata può chiamare l'API per creare un `EntitlementManagement.ReadWrite.All` [oggetto accessPackageResourceRequest.](/graph/api/accesspackageresourcerequest-post?view=graph-rest-beta&preserve-view=true)

## <a name="remove-resources-from-a-catalog"></a>Rimuovere risorse da un catalogo

È possibile rimuovere risorse da un catalogo. Una risorsa può essere rimossa da un catalogo solo se non viene usata in uno dei pacchetti di accesso del catalogo.

**Ruolo prerequisito:** Vedere [Ruoli necessari per aggiungere risorse a un catalogo](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic **su Cataloghi** e quindi aprire il catalogo da cui si desidera rimuovere le risorse.

1. Nel menu a sinistra fare clic su **Risorse**.

1. Selezionare le risorse da rimuovere.

1. Fare **clic su** Rimuovi (o sui puntini di sospensione (**...**) e quindi su Rimuovi **risorsa**.


## <a name="add-additional-catalog-owners"></a>Aggiungere altri proprietari del catalogo

L'utente che ha creato un catalogo diventa il primo proprietario del catalogo. Per delegare la gestione di un catalogo, aggiungere utenti al ruolo di proprietario del catalogo. In questo modo è possibile condividere le responsabilità di gestione del catalogo. 

Seguire questa procedura per assegnare un utente al ruolo di proprietario del catalogo:

**Ruolo prerequisito:** amministratore globale, Amministratore utenti o Proprietario del catalogo

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic **su Cataloghi** e quindi aprire il catalogo a cui si vogliono aggiungere amministratori.

1. Nel menu a sinistra fare clic su **Ruoli e amministratori.**

    ![Ruoli e amministratori dei cataloghi](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. Fare **clic su Aggiungi** proprietari per selezionare i membri per questi ruoli.

1. Fare **clic su** Seleziona per aggiungere questi membri.

## <a name="edit-a-catalog"></a>Modificare un catalogo

È possibile modificare il nome e la descrizione di un catalogo. Gli utenti visualizzano queste informazioni nei dettagli di un pacchetto di accesso.

**Ruolo prerequisito:** amministratore globale, Amministratore utente o Proprietario del catalogo

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic **su Cataloghi** e quindi aprire il catalogo da modificare.

1. Nella pagina Panoramica **del** catalogo fare clic su **Modifica**.

1. Modificare il nome, la descrizione o le impostazioni abilitate del catalogo.

    ![Modificare le impostazioni del catalogo](./media/entitlement-management-shared/catalog-edit.png)

1. Fare clic su **Salva**.

## <a name="delete-a-catalog"></a>Eliminare un catalogo

È possibile eliminare un catalogo, ma solo se non dispone di pacchetti di accesso.

**Ruolo prerequisito:** amministratore globale, Amministratore utente o Proprietario del catalogo

1. Nel portale di Azure fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic **su Cataloghi** e quindi aprire il catalogo da eliminare.

1. Nella panoramica del catalogo **fare** clic su **Elimina.**

1. Nella finestra di messaggio visualizzata fare clic su **Sì**.

### <a name="deleting-a-catalog-programmatically"></a>Eliminazione di un catalogo a livello di codice

È anche possibile eliminare un catalogo usando Microsoft Graph.  Un utente con un ruolo appropriato con un'applicazione con l'autorizzazione delegata può chiamare l'API per `EntitlementManagement.ReadWrite.All` [eliminare un accessPackageCatalog](/graph/api/accesspackagecatalog-delete?view=graph-rest-beta&preserve-view=true).

## <a name="next-steps"></a>Passaggi successivi

- [Delegare la governance dell'accesso per accedere ai gestori di pacchetti](entitlement-management-delegate-managers.md)
