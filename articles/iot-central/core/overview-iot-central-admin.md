---
title: Guida dell'amministratore di Azure IoT Central
description: Azure IoT Central è una piattaforma di applicazioni IoT che semplifica la creazione di soluzioni Internet. Questo articolo fornisce una panoramica del ruolo amministratore in IoT Central.
author: TheJasonAndrew
ms.author: v-anjaso
ms.date: 03/25/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 16a8aecae70d73399acb3878d7088e5086c053a1
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110492"
---
# <a name="iot-central-administrator-guide"></a>Guida dell'amministratore di IoT Central

*Questo articolo si applica agli amministratori.*

Un'applicazione IoT Central consente di monitorare e gestire milioni di dispositivi durante il relativo ciclo di vita. Questa guida è destinata agli amministratori che gestiscono IoT Central applicazioni.

In IoT Central, un amministratore:

- Gestisce utenti e ruoli nell'applicazione.
- Gestisce la sicurezza, ad esempio l'autenticazione del dispositivo.
- Configura le impostazioni dell'applicazione.
- Aggiorna le applicazioni.
- Esporta e condivide le applicazioni.
- Monitora l'integrità dell'applicazione.

## <a name="users-and-roles"></a>Utenti e ruoli

IoT Central usa un sistema di controllo degli accessi in base al ruolo per gestire le autorizzazioni utente all'interno di un'applicazione. IoT Central dispone di tre ruoli predefiniti per gli amministratori, i generatori di soluzioni e gli operatori. Un amministratore può creare ruoli personalizzati con set di autorizzazioni specifici. Un amministratore è responsabile dell'aggiunta di utenti a un'applicazione e della loro assegnazione ai ruoli.

Per altre informazioni, vedere [gestire utenti e ruoli nell'applicazione IoT Central](howto-manage-users-roles.md).

## <a name="application-security"></a>Sicurezza dell'applicazione

I dispositivi che si connettono all'applicazione IoT Central usano in genere certificati X. 509 o firme di accesso condiviso (SAS) come credenziali. L'amministratore gestisce i certificati o le chiavi di gruppo da cui derivano le credenziali del dispositivo.

Per altre informazioni, vedere registrazione del [gruppo x. 509](concepts-get-connected.md#x509-group-enrollment), [registrazione del gruppo SAS](concepts-get-connected.md#sas-group-enrollment)e [come eseguire il Rolling dei certificati del dispositivo x. 509](how-to-roll-x509-certificates.md).

L'amministratore può inoltre creare e gestire i token API utilizzati da un'applicazione client per eseguire l'autenticazione con l'applicazione IoT Central. Le applicazioni client usano l'API REST per interagire con IoT Central.

## <a name="configure-an-application"></a>Configurare un'applicazione

L'amministratore può configurare il comportamento e l'aspetto di un'applicazione IoT Central. Per altre informazioni, vedere:

- [Modificare il nome e l'URL dell'applicazione](howto-administer.md#change-application-name-and-url)
- [Personalizzare l'interfaccia utente](howto-customize-ui.md)
- [Spostare un'applicazione in piani tariffari diversi](howto-view-bill.md)
- [Configurare i caricamenti di file](howto-configure-file-uploads.md)

## <a name="export-an-application"></a>Esportare un'applicazione

Un amministratore può:

- Creare una copia di un'applicazione se è necessaria solo una copia duplicata dell'applicazione. Ad esempio, potrebbe essere necessaria una copia duplicata per i test.
- Creare un modello di applicazione da un'applicazione esistente se si prevede di creare più copie.

Per altre informazioni, vedere [esportare l'applicazione Azure](howto-use-app-templates.md).

## <a name="migrate-to-a-new-version"></a>Eseguire la migrazione a una nuova versione

Un amministratore può eseguire la migrazione di un'applicazione a una versione più recente. Attualmente, un'applicazione appena creata è un'applicazione v3. Un amministratore potrebbe dover eseguire la migrazione di un'applicazione V2 a un'applicazione v3.

Per altre informazioni, vedere [eseguire la migrazione dell'applicazione IoT Central V2 a V3](howto-migrate.md).

## <a name="monitor-application-health"></a>Monitorare l'integrità delle applicazioni

Un amministratore può usare IoT Central metrica per valutare l'integrità dei dispositivi connessi e l'integrità delle esportazioni di dati in esecuzione.

Per visualizzare le metriche, un amministratore può usare i grafici nell'portale di Azure, un'API REST o query di PowerShell o dell'interfaccia della riga di comando di Azure.

Per altre informazioni, vedere [monitorare l'integrità complessiva di un'applicazione IoT Central](howto-monitor-application-health.md).

## <a name="tools"></a>Strumenti

Molti degli strumenti usati come amministratore sono disponibili nella sezione **Amministrazione** di ogni applicazione IoT Central. Per completare alcune attività amministrative, è inoltre possibile utilizzare gli strumenti seguenti:

- [Interfaccia della riga di comando di Azure](howto-manage-iot-central-from-cli.md)
- [Azure PowerShell](howto-manage-iot-central-from-powershell.md)
- [Azure portal](howto-manage-iot-central-from-portal.md)

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come amministrare l'applicazione IoT Central di Azure, il passaggio successivo suggerito consiste nell'acquisire familiarità con la [gestione di utenti e ruoli](howto-manage-users-roles.md) in Azure IOT Central.
