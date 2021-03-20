---
title: Gestire un cloud Confluent-soluzioni di partner Azure
description: Questo articolo descrive la gestione di un cloud Confluent nel portale di Azure. Come configurare Single Sign-On, eliminare un'organizzazione Confluent e ottenere supporto.
ms.service: partner-services
ms.topic: conceptual
ms.date: 02/08/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: f8a54096ecda4729f7070120a02be3055f933cea
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99989150"
---
# <a name="manage-the-confluent-cloud-resource"></a>Gestire la risorsa cloud Confluent

Questo articolo descrive come gestire l'istanza di Apache Kafka per il cloud Confluent in Azure. Viene illustrato come configurare Single Sign-On (SSO), eliminare un'organizzazione Confluent e creare una richiesta di supporto.

## <a name="single-sign-on"></a>Single sign-on

Per implementare SSO per l'organizzazione, l'amministratore tenant può importare l'applicazione della raccolta. Questo passaggio è facoltativo. Per informazioni sull'importazione di un'applicazione, vedere [Guida introduttiva: aggiungere un'applicazione al tenant di Azure Active Directory (Azure ad)](../../active-directory/manage-apps/add-application-portal.md). Quando l'amministratore tenant importa l'applicazione, gli utenti non devono fornire esplicitamente il consenso per consentire l'accesso al portale cloud Confluent.

Per abilitare SSO, attenersi alla seguente procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare alla **Panoramica** per l'istanza della risorsa cloud Confluent.
1. Selezionare il collegamento per **gestire il cloud in modo Confluent**.

   :::image type="content" source="media/sso-link.png" alt-text="Single Sign-On del portale confluenti.":::

1. Se l'amministratore tenant non ha importato l'applicazione della raccolta per il consenso SSO, concedere le autorizzazioni e il consenso. Questo passaggio è necessario solo la prima volta che si accede al collegamento per **gestire il cloud in modo Confluent**.
1. Scegliere un account Azure AD per Single Sign-On al portale cloud Confluent.
1. Una volta fornito il consenso, si verrà reindirizzati al portale cloud Confluent.

## <a name="set-up-cluster"></a>Configurare il cluster

Per informazioni sulla configurazione del cluster, vedere [la pagina relativa alla creazione di un cluster in una documentazione Confluent per il cloud](https://docs.confluent.io/cloud/current/clusters/create-cluster.html).

## <a name="delete-confluent-organization"></a>Elimina organizzazione Confluent

Quando non è più necessaria la risorsa cloud Confluent, eliminare la risorsa in Azure e nel cloud Confluent.

Per eliminare le risorse in Azure:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare **tutte le risorse** e **filtrare in base al nome** della risorsa cloud Confluent o al **tipo di risorsa** _organizzazione Confluent_. In alternativa, nella portale di Azure cercare il nome della risorsa.
1. Nella **Panoramica** della risorsa selezionare **Elimina**.

    :::image type="content" source="media/delete-resources-icon.png" alt-text="Icona di eliminazione delle risorse.":::

1. Per confermare l'eliminazione, digitare il nome della risorsa e selezionare **Elimina**.

    :::image type="content" source="media/delete-resources-prompt.png" alt-text="Richiede di confermare l'eliminazione delle risorse.":::

Per eliminare la risorsa nel cloud Confluent, vedere la documentazione per gli [ambienti cloud](https://docs.confluent.io/current/cloud/using/environments.html) Confluent, ovvero la documentazione Confluent e le [nozioni di base sul cloud](https://docs.confluent.io/current/cloud/using/cloud-basics.html).

Il cluster e tutti i dati nel cluster vengono eliminati definitivamente. Se il contratto include una clausola di conservazione dei dati, Confluent mantiene i dati per il periodo di tempo specificato nella [documentazione relativa alle condizioni](https://www.confluent.io/confluent-cloud-tos)per il servizio.

Viene addebitato l'utilizzo prorateato fino al momento dell'eliminazione del cluster. Dopo l'eliminazione definitiva del cluster, l'utente invia una conferma tramite posta elettronica.

## <a name="get-support"></a>Supporto

Per inviare una richiesta di supporto a Confluent, contattare il [supporto tecnico](https://support.confluent.io) o inviare una richiesta tramite il portale, come illustrato di seguito.

> [!NOTE]
> Per la prima volta, è necessario reimpostare la password prima di accedere al portale di supporto di Confluent. Se non si dispone di un account con Cloud Confluent, inviare un messaggio di posta elettronica a `cloud-support@confluent.io` per ulteriore assistenza.

Nel portale è possibile inviare una richiesta tramite la guida e il supporto tecnico di Azure oppure direttamente dall'istanza di Apache Kafka per il cloud Confluent in Azure.

Per inviare una richiesta tramite la guida e il supporto tecnico di Azure:

1. Selezionare **Guida e supporto**.
1. Selezionare **Crea una richiesta di supporto**.
1. Nel modulo selezionare **tecnico** per tipo di **problema**. Selezionare la propria sottoscrizione. Nell'elenco dei servizi selezionare **Fluent in Azure**.

    :::image type="content" source="media/support-request-help.png" alt-text="Creare una richiesta di supporto dalla guida.":::

Per inviare una richiesta dalla risorsa, seguire questa procedura:

1. Nella portale di Azure selezionare l'organizzazione più Fluent.
1. Dal menu sul lato sinistro della schermata selezionare **nuova richiesta di supporto**.
1. Per creare una richiesta di supporto, selezionare il collegamento al **portale Confluent**.

    :::image type="content" source="media/support-request.png" alt-text="Creare una richiesta di supporto dall'istanza di.":::

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sulla risoluzione dei problemi, vedere la pagina relativa alla [risoluzione dei problemi Apache Kafka per le soluzioni cloud confluenti](troubleshoot.md).
