---
title: Glossario del programma per dispositivi certificati di Azure
description: Elenco dei termini comuni usati nel programma Azure Certified Device
author: nikuntjo
ms.author: nikuntjo
ms.service: certification
ms.topic: conceptual
ms.date: 03/03/2021
ms.custom: template-concept
ms.openlocfilehash: 7dfdd4a89535621e08db8a8924282febdafd1569
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969254"
---
# <a name="azure-certified-device-program-glossary"></a>Glossario del programma per dispositivi certificati di Azure

Questa guida fornisce le definizioni dei termini comunemente usati nel programma e nel portale di Azure Certified Device. Per chiarire il processo di certificazione, fare riferimento a questo glossario. Per praticità, questo glossario è suddiviso in categorie in base ai principali concetti relativi alla certificazione di cui si potrebbero avere domande.

## <a name="device-class"></a>Classe Device

Quando si crea il progetto di certificazione, verrà richiesto di specificare una classe di dispositivo. La classe Device si riferisce al fattore di forma o alla classificazione che meglio rappresenta il dispositivo.

- **Gateway**

    Un dispositivo che elabora i dati inviati tramite una rete Internet delle cose.

- **Sensor**

    Dispositivo che rileva e risponde alle modifiche apportate a un ambiente e si connette ai gateway per elaborare le modifiche.

- **Altri**

    Se si seleziona altro, aggiungere una descrizione della classe del dispositivo con le proprie parole. Nel corso del tempo, è possibile continuare ad aggiungere nuovi valori a questo elenco, in particolare quando si continua a monitorare il feedback dei partner.

## <a name="device-type"></a>Tipo di dispositivo

Verrà inoltre chiesto di selezionare uno dei due tipi di dispositivi durante il processo di certificazione.

- **Prodotto finito**

    Un dispositivo pronto per la distribuzione di produzione. In genere, in un fattore di forma completato con firmware e un sistema operativo. Potrebbero trattarsi di dispositivi di uso generico che richiedono personalizzazione aggiuntiva o dispositivi specializzati che non richiedono modifiche per l'utilizzo.
- **Kit di sviluppo pronto per la soluzione**

    Un kit di sviluppo contenente hardware e software ideale per semplificare la creazione di prototipi, in genere non in un fattore di forma terminato. Include in genere esempi di codice ed esercitazioni per abilitare la creazione rapida di prototipi.

## <a name="component-type"></a>Tipo di componente

Nella sezione dettagli dispositivo verrà descritto il dispositivo elencando i componenti in base al tipo di componente. [Qui](./how-to-using-the-components-feature.md)è possibile visualizzare altre linee guida sui componenti.

- **Prodotto pronto per il cliente**

    Rappresentazione di un componente del dispositivo globale o primario. Si tratta di un prodotto diverso da un **prodotto finito**, ovvero una classificazione del dispositivo come pronta per l'utilizzo da parte dei clienti senza ulteriori attività di sviluppo. Un prodotto finito conterrà un componente prodotto pronto per il cliente.
- **Scheda di sviluppo**

    Una lavagna integrata o scollegabile con microprocessore per semplificare la personalizzazione.
- **Periferica**

    Aggiunta o scollegabile di un prodotto (ad esempio un accessorio). Si tratta in genere di dispositivi che si connettono al dispositivo principale, ma non contribuiscono alle funzioni primarie del dispositivo. Ma fornisce funzioni aggiuntive. Memoria, RAM, archiviazione, dischi rigidi e CPU non sono considerati dispositivi periferici (devono invece essere elencati in specifiche aggiuntive del componente prodotto pronto per il cliente).
- **System-on-Module**  

    Circuito a livello di lavagna che integra una funzione di sistema in un unico modulo.

## <a name="component-attachment-method"></a>Metodo allegato componente

Il metodo di collegamento dei componenti è un altro dettaglio che informa il cliente sul modo in cui il componente è integrato nell'intero prodotto.

- **Integrato**
 
    Fa riferimento a quando un componente del dispositivo fa parte dello chassis principale del prodotto. Questo si riferisce in genere a un tipo di componente periferico che non può essere rimosso dal dispositivo.  
    Esempio: un sensore di temperatura integrato all'interno di uno chassis del gateway.

- **Discrete**

    Fa riferimento a quando un componente **non** fa parte dello chassis principale del prodotto.  
    Esempio: un sensore di temperatura esterno che deve essere collegato al dispositivo.


## <a name="next-steps"></a>Passaggi successivi

Questo glossario fornirà le istruzioni per il processo di certificazione del progetto nel portale. A questo punto si è pronti per iniziare il progetto.
- [Esercitazione: creazione del progetto](./tutorial-01-creating-your-project.md)
