---
title: Distribuire OpenShift container Platform 4. x in Azure
description: Distribuire OpenShift container Platform 4. x in Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: e8650802b4add9b33664205367bb3242b32b9754
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101670392"
---
# <a name="deploy-openshift-container-platform-4x-in-azure"></a>Distribuire OpenShift container Platform 4. x in Azure

La distribuzione di OpenShift container Platform (OCP) 4,2 è ora supportata in Azure tramite il modello di infrastruttura Installer-Provisioned (IPI).  La pagina di destinazione per il tentativo di OpenShift 4 è [try.OpenShift.com](https://try.openshift.com/). Per installare OCP 4,2 in Azure, visitare la pagina [Red Hat OpenShift cluster Manager](https://cloud.redhat.com/openshift/install/azure/installer-provisioned) .  Per accedere a questo sito sono necessarie le credenziali di Red Hat.


## <a name="notes"></a>Note 

 - Per installare ed eseguire OCP 4. x in Azure è necessaria un'entità servizio (SP) Azure Active Directory (AAD)
     - A SP deve essere concessa l'autorizzazione API di **Application. ReadWrite. OwnedBy** per Azure Active Directory Graph
     - Un Amministratore tenant AAD deve concedere il consenso dell'amministratore per rendere effettiva l'autorizzazione dell'API
     - È necessario concedere a SP i ruoli **collaboratore** e **amministratore accesso utenti** alla sottoscrizione
 - Il modello di installazione per OCP 4. x è diverso da 3. x e non sono disponibili modelli di Azure Resource Manager per la distribuzione di OCP 4. x in Azure
 - Se si verificano problemi durante il processo di installazione, contattare la società appropriata (Microsoft o Red Hat)

| Descrizione del problema | Punto di contatto |
|-------------------|---------------|
| Problemi specifici di Azure (AAD, SP, sottoscrizione di Azure e così via)                              | Microsoft |
| Problemi specifici di OpenShift (errori di installazione/errori, sottoscrizione di Red Hat e così via) |  Red Hat  |




## <a name="next-steps"></a>Passaggi successivi

- [Getting started with OpenShift Container Platform](https://docs.openshift.com) (Introduzione a OpenShift Container Platform)
