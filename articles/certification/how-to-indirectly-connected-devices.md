---
title: Bundle di dispositivi Certifing e dispositivi connessi indirettamente
titleSuffix: Azure Certified
description: Vedere come inviare un dispositivo connesso indirettamente per la certificazione.
author: cbroad
ms.author: cbroad
ms.date: 02/23/2021
ms.topic: how-to
ms.service: certification
ms.openlocfilehash: 3a4fd2838c0ddf6d7d03d68f105fc59471b77dea
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304478"
---
# <a name="device-bundles-and-indirectly-connected-devices"></a>Bundle del dispositivo e dispositivi connessi indirettamente

Per supportare i dispositivi che interagiscono con Azure tramite un dispositivo, le offerte SaaS o PaaS, il portale https://certify.azure.com/) di invio (e il catalogo dei dispositivi) ( https://devicecatalog.azure.com) Abilita i concetti di aggregazione e dipendenze per promuovere e abilitare queste combinazioni di dispositivi accesso al programma Azure Certified Device.

A seconda della linea di prodotti e dei servizi offerti, la situazione potrebbe richiedere una combinazione di questi passaggi:


![Creare dipendenze del progetto](./media/indirect-connected-device/picture-1.png )
## <a name="sensors-and-indirect-devices"></a>Sensori e dispositivi indiretti
Molti sensori richiedono la connessione di un dispositivo ad Azure. Inoltre, potrebbero essere presenti più dispositivi compatibili che funzioneranno con il dispositivo del sensore. **Per supportare questi scenari, è necessario prima certificare i dispositivi prima di certificare il sensore che passerà le informazioni.**

Esempio di presentazione di combinazioni di matrici di invio ![](./media/indirect-connected-device/picture-2.png )

Per certificare il sensore, che richiede un dispositivo separato:
1.  Prima di tutto, [certificare il dispositivo](https://certify.azure.com) e pubblicarlo nel catalogo dei dispositivi Azure Certified
    - Se si dispone di più dispositivi compatibili con passthrough (come nell'esempio precedente), inviarli separatamente per la certificazione e pubblicare nel catalogo
2.  Con il sensore connesso tramite il dispositivo, inviare il sensore per la certificazione
    * Nella scheda "dipendenze" della sezione "dettagli dispositivo" impostare i valori seguenti
        * Tipo di dipendenza = "Gateway hardware"
        * Dependency URL = "collegamento URL al dispositivo nel catalogo dispositivi"
        * Usato durante il test = "Sì"
        * Aggiungere eventuali commenti per i clienti che devono essere forniti a un utente che visualizza la descrizione del prodotto nel catalogo dei dispositivi. (esempio: "i dispositivi serie 100 sono necessari per la connessione dei sensori ad Azure")

3.  Se si dispone di più dispositivi che si desidera aggiungere come facoltativi per questo dispositivo, è possibile selezionare "+ Aggiungi dipendenza aggiuntiva". Seguire quindi le stesse linee guida e tenere presente che non è stato usato durante i test. Nei commenti per i clienti, assicurarsi che i clienti sappiano che altri dispositivi sono associati a questo sensore sono disponibili (in alternativa al dispositivo usato durante il test).

![Testo alternativo](./media/indirect-connected-device/picture-3.png "Tipo di dipendenza hardware")

## <a name="paas-and-saas-offerings"></a>PaaS e offerte SaaS
Come parte del portfolio di prodotti, è possibile che siano presenti dispositivi certificati, ma il dispositivo richiede anche altri servizi della società o di altre società di terze parti. Per aggiungere questa dipendenza, attenersi alla seguente procedura:
1. Avviare il processo di invio del dispositivo
2. Nella scheda "dipendenze" impostare i valori seguenti
    - Tipo di dipendenza = "servizio software"
    - Nome servizio = "[nome prodotto]"
    - Dependency URL = "collegamento URL a una pagina del prodotto che descrive il servizio"
    - Aggiungere eventuali commenti per i clienti che devono essere forniti a un utente che visualizza la descrizione del prodotto nel catalogo dei dispositivi Azure Certified
3. Se si dispone di altri software, servizi o dipendenze hardware che si desidera aggiungere come facoltativi per questo dispositivo, è possibile selezionare "+ Aggiungi dipendenza aggiuntiva" e seguire le stesse linee guida.

![Tipo di dipendenza software](./media/indirect-connected-device/picture-4.png )

## <a name="bundled-products"></a>Prodotti in bundle
Gli elenchi di prodotti in bundle sono semplicemente la certificazione corretta di un dispositivo con altri componenti che verranno venduti come parte del bundle in un prodotto. Si ha la possibilità di inviare un dispositivo che include componenti aggiuntivi, ad esempio un sensore di temperatura e un sensore della fotocamera (#1), oppure è possibile inviare un sensore di tocco che include un dispositivo passthrough (#2). Grazie alla funzionalità "componente" è possibile aggiungere più componenti alla lista.

Se si intende eseguire questa operazione, è necessario formattare l'immagine dell'elenco di prodotti per indicare che questo prodotto è incluso in altri componenti.  Inoltre, se il Bundle richiede servizi aggiuntivi per la certificazione, sarà necessario identificarli tramite la dipendenza dei servizi.
Matrice di esempio di prodotti in bundle

![Esempio di invio del bundle](./media/indirect-connected-device/picture-5.png )

Per una descrizione più dettagliata di come usare la funzionalità componente nel portale per i dispositivi Azure Certified, vedere la [documentazione della Guida](./how-to-using-the-components-feature.md). 

Se un dispositivo è un dispositivo PassThrough con un sensore separato nello stesso prodotto, creare un componente per riflettere il dispositivo Passthrough e un altro componente per riflettere il sensore. I componenti possono essere aggiunti al progetto nella scheda Dettagli prodotto della sezione dettagli dispositivo:

![Aggiunta di componenti](./media/indirect-connected-device/picture-6.png )

Per il dispositivo passthrough, impostare il tipo di componente come prodotto pronto per il cliente e compilare gli altri campi come pertinente per il prodotto. Esempio:

![Dettagli del componente](./media/indirect-connected-device/picture-7.png )

Per il sensore, aggiungere un secondo componente, impostando come discreto il tipo di componente come metodo di collegamento e periferica. Esempio:

![Dettagli secondo componente](./media/indirect-connected-device/picture-8.png )

Una volta creato il componente del sensore, modificare i dettagli, passare alla scheda sensori e quindi aggiungere i dettagli del sensore. Esempio:

![Dettagli del sensore](./media/indirect-connected-device/picture-9.png )

Completare i dettagli dei progetti e inviare il dispositivo per la certificazione come di consueto.

