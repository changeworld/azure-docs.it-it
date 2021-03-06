---
title: Configurare i dati su larga scala per State Configuration di Automazione di Azure
description: Questo articolo descrive come configurare i dati su larga scala per State Configuration di Automazione di Azure
keywords: dsc,powershell,configurazione,installazione
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: bc55a4c5ab20cac041a00a0f924b207eb256ae8b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86186521"
---
# <a name="configure-data-at-scale-for-azure-automation-state-configuration"></a>Configurare i dati su larga scala per State Configuration di Automazione di Azure

> Si applica a: Windows PowerShell 5.1

La gestione di centinaia o migliaia di server può costituire un problema.
Secondo i clienti, l'aspetto più difficile è la gestione dei [dati di configurazione](/powershell/scripting/dsc/configurations/configdata) e
l'organizzazione delle informazioni tra costrutti logici come la posizione, il tipo e l'ambiente.

> [!NOTE]
> Questo articolo fa riferimento a una soluzione gestita dalla community open source.
> Il supporto è disponibile solo sotto forma di collaborazione con GitHub, non da Microsoft.

## <a name="community-project-datum"></a>Progetto della community: Datum

Per risolvere il problema, è stata creata una soluzione gestita dalla community denominata [Datum](https://github.com/gaelcolas/Datum).
Datum si basa su ottime idee di altre piattaforme di gestione della configurazione e implementa lo stesso tipo di soluzione per PowerShell DSC.
Le informazioni sono [organizzate in file di testo](https://github.com/gaelcolas/Datum#3-intended-usage) in base a idee logiche.
Alcuni esempi potrebbero essere:

- Impostazioni da applicare a livello globale
- Impostazioni da applicare a tutti i server in un percorso
- Impostazioni da applicare a tutti i server di database
- Impostazioni del singolo server

Queste informazioni sono organizzate nel formato di file preferito (JSON, Yaml o PSD1).
Vengono quindi forniti i cmdlet per generare file di dati di configurazione [consolidando le informazioni](https://github.com/gaelcolas/Datum#datum-tree) di ogni file in a vista singola di un server o ruolo server.

Dopo aver generato i file di dati, è possibile usarli con gli [script di configurazione DSC](/powershell/scripting/dsc/configurations/write-compile-apply-configuration) per generare file MOF e [caricarli in Automazione di Azure](./tutorial-configure-servers-desired-state.md#create-and-upload-a-configuration-to-azure-automation).
Registrare quindi i server da [locale](./automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines) o [in Azure](./automation-dsc-onboarding.md#enable-azure-vms) per eseguire il pull delle configurazioni.

Per provare Datum, visitare [PowerShell Gallery](https://www.powershellgallery.com/packages/datum/) e scaricare la soluzione oppure fare clic su "Sito di progetto" per visualizzare la [documentazione](https://github.com/gaelcolas/Datum#2-getting-started--concepts).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su PowerShell DSC, vedere [Panoramica della configurazione di Desired State Configuration per Windows PowerShell](/powershell/scripting/dsc/overview/overview).
- In [Risorse DSC](/powershell/scripting/dsc/resources/resources) sono disponibili informazioni sulle risorse di PowerShell DSC.
- Per altri dettagli sulla configurazione di Configuration Manager locale, vedere [Configurazione di Gestione configurazione locale](/powershell/scripting/dsc/managing-nodes/metaconfig).
