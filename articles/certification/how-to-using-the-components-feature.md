---
title: Come usare la funzionalità componenti nel portale per i dispositivi Azure Certified
description: Una guida su come usare al meglio la funzionalità componenti della sezione dettagli dispositivo per descrivere in modo accurato il dispositivo
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: how-to
ms.date: 03/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 220a6c2107063734201064115898611c20cab650
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304461"
---
# <a name="add-components-on-the-portal"></a>Aggiungere componenti nel portale

Durante il completamento dell' [esercitazione per aggiungere i dettagli del dispositivo](tutorial-02-adding-device-details.md) al progetto di certificazione, sarà prevista la descrizione delle specifiche hardware del dispositivo. A tale scopo, gli utenti possono evidenziare più prodotti hardware distinti (detti **componenti**) che costituiscono il dispositivo. In questo modo è possibile promuovere meglio i dispositivi dotati di hardware aggiuntivo e consentire ai clienti di trovare il prodotto appropriato eseguendo una ricerca nel catalogo in base a tali funzionalità.

## <a name="prerequisites"></a>Prerequisiti

- È necessario aver eseguito l'accesso e avere un progetto per il dispositivo creato nel [portale per i dispositivi Azure Certified](https://certify.azure.com). Per ulteriori informazioni, vedere l' [esercitazione](tutorial-01-creating-your-project.md).

## <a name="how-to-add-components"></a>Come aggiungere componenti

Ogni progetto inviato per la certificazione includerà un componente **prodotto pronto** per il cliente, che in molti casi rappresenterà il prodotto olistico. Per comprendere meglio la distinzione di un tipo di componente prodotto pronto per il cliente, visualizzare il [Glossario di certificazione](./resources-glossary.md). Tutti i componenti aggiuntivi sono a propria discrezione da includere per acquisire accuratamente il dispositivo.

1. Selezionare `Add a component` nella scheda Dettagli prodotto.

    ![Aggiungi collegamento componente](./media/images/add-a-component-link.png)

1. Completare i campi del modulo pertinenti per il componente.

    ![Sezione Dettagli componente](./media/images/component-details-section.png)

1. Salvare le informazioni usando il `Save Product Details` pulsante nella parte inferiore della pagina:  

    ![Pulsante Salva dettagli prodotto](./media/images/save-product-details-button.png)

1. Dopo aver salvato il componente, è possibile personalizzare ulteriormente le funzionalità hardware supportate. Selezionare il `Edit` collegamento in base al nome del componente.  

    ![Pulsante modifica componente](./media/images/component-edit.png)

1. Fornire le informazioni rilevanti sulle funzionalità hardware laddove appropriato.  

    ![Immagine delle sezioni dei componenti modificabili](./media/images/component-selection-area.png)  

    I campi dei componenti modificabili (illustrati in precedenza) includono:

    - **Generale**: dettagli dell'hardware, ad esempio processori e hardware sicuro
    - **Connettività**: opzioni di connettività, protocolli e interfacce, ad esempio radio/e e GPIO
    - **Acceleratori**: specificare l'accelerazione hardware, ad esempio GPU e VPU
    - **Sensori**: specificare i sensori disponibili, ad esempio GPS e vibrazione
    - **Specifiche aggiuntive**: informazioni aggiuntive sul dispositivo, ad esempio le dimensioni fisiche e le informazioni su archiviazione/batteria

1. Selezionare `Save Product Details` nella parte inferiore della pagina dei dettagli sul prodotto.

## <a name="component-use-requirements-and-recommendations"></a>Requisiti e raccomandazioni per l'utilizzo dei componenti

Potrebbero esserci domande sul numero di componenti da includere o sul tipo di componente da usare. Di seguito sono riportati alcuni esempi di scenari di esempio di dispositivi che è possibile certificare e come usare la funzionalità dei componenti.

| Tipo prodotto                                       | No. Componenti | Componente 1/tipo di allegato      | Componenti 2 +/tipo di allegato                    |
|----------------------------------------------------|------------|----------------------------------|--------------------------------------------------|
| Prodotto finito                                   | 1          | Prodotto pronto per il cliente, discreto | N/D                                              |
| Prodotto finito con **periferiche scollegabili** | 2 o più  | Prodotto pronto per il cliente, discreto | Periferiche/discrete o integrate              |
| Prodotto finito con **componenti integrati**  | 2 o più  | Prodotto pronto per il cliente, discreto | Selezionare il tipo appropriato, discreto o integrato |
| Kit di sviluppo Solution-Ready                             | 2 o più  | Prodotto pronto per il cliente, discreto | Selezionare il tipo appropriato, discreto o integrato |

## <a name="example-component-usage"></a>Esempio di utilizzo del componente

Di seguito sono riportati alcuni esempi di come un OEM denominato contoso utilizzerebbe la funzionalità dei componenti per certificare il prodotto, denominato Falcon.

1. Falcon è un dispositivo autonomo completo che non si integra in un prodotto più grande.
    1. No. dei componenti: 1
    1. Tipo di dispositivo componente: prodotto pronto per il cliente
    1. Tipo di allegato: discreto

     ![Immagine del prodotto pronto per il cliente](./media/images/customer-ready-product.png)

1. Falcon è un dispositivo che include un modulo integrato di fotocamera periferico prodotto da INC Electronics che si connette tramite USB a Falcon.
    1. No. di componenti: 2
    1. Tipo di dispositivo componente: prodotto pronto per il cliente, periferica
    1. Tipo di allegato: discreto, integrato
    
    > [!Note]
    > Il componente periferico viene considerato integrato perché non è rimovibile.

     ![Immagine del componente di esempio della periferica](./media/images/peripheral.png)

1. Falcon è un dispositivo che include un sistema integrato sul modulo di INC Electronics che usa un processore incorporato Apollo52 della società Espressif e ha un'architettura ARM64.
    1. No. di componenti: 2
    1. Tipo di dispositivo componente: prodotto pronto per il cliente, sistema sul modulo
    1. Tipo di allegato: discreto, integrato

    > [!Note]
    > Il componente periferico viene considerato integrato perché non è rimovibile. Il componente SoM includerà anche le informazioni sul processore.

     ![Immagine del componente di esempio System on Module ](./media/images/system-on-module.png)

## <a name="additional-tips"></a>Suggerimenti aggiuntivi

Di seguito sono riportati altri chiarimenti relativi ai criteri di utilizzo dei componenti. In caso di domande sull'utilizzo appropriato dei componenti, contatta il nostro team all'indirizzo e saremo [iotcert@microsoft.com](mailto:iotcert@microsoft.com) lieti di aiutarti.

1. Un progetto deve contenere **solo** un componente prodotto pronto per il cliente. Se si certifica un progetto con due dispositivi indipendenti, tali dispositivi devono essere certificati separatamente.
1. Per promuovere le funzionalità del dispositivo a potenziali clienti, è prevalentemente necessario usare (o non usare) componenti.
1. Durante la revisione del dispositivo, il team di certificazione di Azure richiederà l'elenco di almeno un componente prodotto pronto per il cliente. Tuttavia, è possibile che vengano richieste modifiche alle informazioni sul componente se i dettagli non sono chiari o sembrano mancanti (ad esempio, il produttore del componente non viene fornito per un tipo di prodotto pronto per il cliente).

## <a name="next-steps"></a>Passaggi successivi

Ora che si è pronti per usare la funzionalità componenti, è ora possibile completare i dettagli del dispositivo o modificare il progetto per una maggiore chiarezza.

- [Esercitazione: aggiunta di dettagli sul dispositivo](tutorial-02-adding-device-details.md)
- [Modifica del dispositivo pubblicato](how-to-edit-published-device.md)

