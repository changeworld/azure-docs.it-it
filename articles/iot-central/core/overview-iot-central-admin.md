---
title: Azure IoT Central amministratore
description: Azure IoT Central è una piattaforma di applicazioni IoT che semplifica la creazione di soluzioni Internet. Questo articolo offre una panoramica del ruolo di amministratore in IoT Central.
author: philmea
ms.author: philmea
ms.date: 03/25/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 658ebe027565a3acaf427a7b3dbf3963701069d8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868646"
---
# <a name="iot-central-administrator-guide"></a>IoT Central amministratore

*Questo articolo si applica agli amministratori.*

Un'applicazione IoT Central consente di monitorare e gestire milioni di dispositivi durante il relativo ciclo di vita. Questa guida è per gli amministratori che gestiscono IoT Central applicazioni.

In IoT Central un amministratore:

- Gestisce utenti e ruoli nell'applicazione.
- Gestisce la sicurezza, ad esempio l'autenticazione del dispositivo.
- Configura le impostazioni dell'applicazione.
- Aggiorna le applicazioni.
- Esporta e condivide le applicazioni.
- Monitora l'integrità dell'applicazione.

## <a name="users-and-roles"></a>Utenti e ruoli

IoT Central un sistema di controllo degli accessi in base al ruolo per gestire le autorizzazioni utente all'interno di un'applicazione. IoT Central ha tre ruoli predefiniti per amministratori, generatori di soluzioni e operatori. Un amministratore può creare ruoli personalizzati con set specifici di autorizzazioni. Un amministratore è responsabile dell'aggiunta di utenti a un'applicazione e dell'assegnazione ai ruoli.

Per altre informazioni, vedere [Gestire utenti e ruoli nell'IoT Central.](howto-manage-users-roles.md)

## <a name="application-security"></a>Sicurezza dell'applicazione

I dispositivi che si connettono all'applicazione IoT Central usano in genere certificati X.509 o firme di accesso condiviso (SAS) come credenziali. L'amministratore gestisce i certificati o le chiavi del gruppo da cui derivano le credenziali del dispositivo.

Per altre informazioni, vedere Registrazione del gruppo [](concepts-get-connected.md#sas-group-enrollment) [X.509,](concepts-get-connected.md#x509-group-enrollment)registrazione del gruppo di firma di accesso condiviso e Come eseguire [il roll-to-roll dei certificati del dispositivo X.509.](how-to-roll-x509-certificates.md)

L'amministratore può anche creare e gestire i token API che un'applicazione client usa per eseguire l'autenticazione con l IoT Central applizione. Le applicazioni client usano l'API REST per interagire con IoT Central.

## <a name="configure-an-application"></a>Configurare un'applicazione

L'amministratore può configurare il comportamento e l'aspetto di un'IoT Central applicazione. Per altre informazioni, vedere:

- [Modificare il nome e l'URL dell'applicazione](howto-administer.md#change-application-name-and-url)
- [Personalizzare l'interfaccia utente](howto-customize-ui.md)
- [Spostare un'applicazione in un piano tariffario diverso](howto-view-bill.md)
- [Configurare i caricamenti di file](howto-configure-file-uploads.md)

## <a name="export-an-application"></a>Esportare un'applicazione

Un amministratore può:

- Creare una copia di un'applicazione se è sufficiente una copia duplicata dell'applicazione. Ad esempio, potrebbe essere necessaria una copia duplicata per il test.
- Creare un modello di applicazione da un'applicazione esistente se si prevede di creare più copie.

Per altre informazioni, vedere [Esportare l'Azure IoT appalto.](howto-use-app-templates.md)

## <a name="migrate-to-a-new-version"></a>Eseguire la migrazione a una nuova versione

Un amministratore può eseguire la migrazione di un'applicazione a una versione più recente. Attualmente, un'applicazione appena creata è un'applicazione V3. Un amministratore potrebbe dover eseguire la migrazione di un'applicazione V2 a un'applicazione V3.

Per altre informazioni, vedere [Eseguire la migrazione dell'applicazione IoT Central V2 alla versione 3.](howto-migrate.md)

## <a name="monitor-application-health"></a>Monitorare l'integrità delle applicazioni

Un amministratore può usare le IoT Central per valutare l'integrità dei dispositivi connessi e l'integrità delle esportazioni di dati in esecuzione.

Per visualizzare le metriche, un amministratore può usare i grafici nel portale di Azure, in un'API REST o nelle query di PowerShell o dell'interfaccia della riga di comando di Azure.

Per altre informazioni, vedere [Monitorare l'integrità complessiva di un'IoT Central applicazione](howto-monitor-application-health.md).

## <a name="tools"></a>Strumenti

Molti degli strumenti che si usano come amministratore sono disponibili nella **sezione Amministrazione** di ogni IoT Central applicazione. È anche possibile usare gli strumenti seguenti per completare alcune attività amministrative:

- [Interfaccia della riga di comando di Azure](howto-manage-iot-central-from-cli.md)
- [Azure PowerShell](howto-manage-iot-central-from-powershell.md)
- [Azure portal](howto-manage-iot-central-from-portal.md)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso come amministrare l'applicazione Azure IoT Central, il passaggio successivo consigliato consiste nell'apprendere come gestire utenti e ruoli [in](howto-manage-users-roles.md) Azure IoT Central.
