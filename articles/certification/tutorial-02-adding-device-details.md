---
title: Programma per dispositivi certificati di Azure-esercitazione-Aggiunta di dettagli sul dispositivo
description: Guida dettagliata per aggiungere i dettagli del dispositivo al progetto nel portale per i dispositivi Azure Certified
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial
ms.openlocfilehash: f4f3d045a2530fa54d22bec789918454cba80097
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310428"
---
# <a name="tutorial-add-device-details"></a>Esercitazione: aggiungere i dettagli del dispositivo

A questo punto è stato creato il progetto per il dispositivo e si è pronti per iniziare il processo di certificazione. Prima di tutto, aggiungere i dettagli del dispositivo. Sono incluse specifiche tecniche che i clienti potranno visualizzare nel catalogo dei dispositivi certificati di Azure e i dettagli di marketing che verranno usati per acquistare una volta presi una decisione.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Aggiungere i dettagli del dispositivo usando le funzionalità componenti e dipendenze
> * Caricare una guida introduttiva per il dispositivo
> * Specificare i dettagli di marketing per i clienti per l'acquisto del dispositivo
> * Facoltativamente, identificare eventuali certificazioni di settore

## <a name="prerequisites"></a>Prerequisiti

* È necessario aver eseguito l'accesso e avere un progetto per il dispositivo creato nel [portale per i dispositivi Azure Certified](https://certify.azure.com). Per ulteriori informazioni, vedere l' [esercitazione](tutorial-01-creating-your-project.md).
* Nel formato PDF dovrebbe essere presente una guida introduttiva per il dispositivo. Sono disponibili diversi modelli introduttivi che è possibile usare, a seconda del programma di certificazione e della lingua preferita. I modelli sono disponibili nel percorso di GitHub per i [modelli introduttivi](https://aka.ms/GSTemplate "Introduzione ai modelli") .

## <a name="adding-technical-device-details"></a>Aggiunta di dettagli tecnici sui dispositivi

La prima sezione della pagina del progetto, denominata "dettagli dispositivo di input", consente di fornire informazioni sulle funzionalità hardware principali del dispositivo, ad esempio il nome del dispositivo, la descrizione, il processore, il sistema operativo, le opzioni di connettività, le interfacce hardware, i protocolli di settore, le dimensioni fisiche e altro ancora. Sebbene molti dei campi siano facoltativi, la maggior parte di queste informazioni verrà resa disponibile per i potenziali clienti nel catalogo dei dispositivi certificati di Azure se si sceglie di pubblicare il dispositivo dopo che è stato certificato.

1. Fare clic `Add` nella sezione "dettagli dispositivo di input" della pagina di riepilogo del progetto per aprire la sezione dettagli dispositivo. Si vedranno cinque sezioni per il completamento.

![Immagine della pagina dei dettagli del progetto](./media/images/device-details-menu.png)

2. Esaminare le informazioni fornite in precedenza al momento della creazione del progetto nella `Basics` scheda.
1. Esaminare le certificazioni che si sta applicando al dispositivo nella `Certifications` scheda.
1. Aprire la `Product details` scheda e selezionare almeno un sistema operativo.
1. Aggiungere **almeno** un componente discreto che descrive il dispositivo. È possibile visualizzare altre informazioni aggiuntive sull'utilizzo del componente [qui](how-to-using-the-components-feature.md).
1. Fare clic su `Save`. Sarà quindi possibile modificare il dispositivo componente e aggiungere altri dettagli avanzati.
1. Elenca i dettagli aggiuntivi del dispositivo non acquisiti dai dettagli del componente in `Additional product details` .
1. Se è `Other` stato contrassegnato in uno dei campi del componente o si ha una circostanza speciale che si vuole contrassegnare con il team di certificazione di Azure, lasciare un commento chiarificante nella `Comments for reviewer` sezione.
1. Usare la `Dependencies` scheda per elencare le dipendenze se il dispositivo richiede hardware o servizi aggiuntivi per inviare i dati ad Azure. È possibile visualizzare altre linee guida per elencare le dipendenze [qui](how-to-indirectly-connected-devices.md).
1. Una volta soddisfatte le informazioni fornite, è possibile usare la `Review` scheda per una panoramica di sola lettura del set completo di dettagli del dispositivo immessi.
1. Fare clic `Project summary` nella parte superiore della pagina per tornare alla pagina di riepilogo.

![Pagina Verifica dettagli progetto](./media/images/sample-device-details.png)

## <a name="uploading-a-get-started-guide"></a>Caricamento di una guida introduttiva

La Guida introduttiva è un documento PDF che semplifica l'installazione e la configurazione e la gestione del prodotto. Lo scopo è quello di semplificare la connessione e il supporto dei dispositivi in Azure tramite il dispositivo. Nell'ambito del processo di certificazione, è necessario che i partner forniscano **una** Guida introduttiva per il programma di certificazione più pertinente.

1. Verificare di aver fornito tutte le informazioni richieste nella Guida introduttiva PDF in base ai [modelli](https://aka.ms/GSTemplate)forniti. Il modello usato deve essere determinato dalla notifica di certificazione a cui si sta applicando. (Ad esempio, un Plug and Play dispositivo utilizzerà il modello Plug and Play. I dispositivi che si applicano *solo* alla certificazione di base dei dispositivi certificati di Azure utilizzeranno il modello di dispositivo Azure Certified.
1. Fare clic `Add` nella sezione "Guida introduttiva" della pagina di riepilogo del progetto.

![Immagine del pulsante GSG](./media/images/gsg-menu.png)

2. Fare clic su "Scegli file" per caricare il file PDF.
1. Esaminare il documento nell'anteprima per la formattazione.
1. Salvare il caricamento facendo clic sul pulsante "Salva".
1. Fare clic `Project summary` nella parte superiore della pagina per tornare alla pagina di riepilogo.

## <a name="providing-marketing-details"></a>Fornire dettagli di marketing

In quest'area si forniranno informazioni di marketing pronte per il cliente per il dispositivo. Questi campi verranno presentati nel catalogo dei dispositivi Azure Certified se si sceglie di pubblicare il dispositivo certificato.

1. Fare clic `Add` nella sezione "Aggiungi dettagli marketing" per aprire la pagina Dettagli marketing.

![Immagine della sezione dettagli marketing](./media/images/marketing-details.png)

1. Caricare una foto di un prodotto in formato JPEG o PNG che verrà usato nel catalogo.
1. Scrivere una breve descrizione del dispositivo che verrà visualizzato nella pagina di descrizione del prodotto del catalogo.
1. Indicare la disponibilità geografica del dispositivo.
1. Fornire un collegamento alla pagina di marketing del produttore per questo dispositivo. Deve trattarsi di un collegamento a un sito che fornisce informazioni aggiuntive sul dispositivo.
    > [!Note]
    > Assicurarsi che tutti gli URL specificati siano validi o che saranno attivi al momento della pubblicazione dopo l'approvazione. *)

1. Indica fino a 3 settori di destinazione per i quali il dispositivo è ottimizzato.
1. Fornire informazioni per un massimo di 5 distributori del dispositivo. Questo può includere il sito del produttore.

    > [!Note]
    > Se non viene fornito alcun URL della pagina prodotto del server `Shop` di distribuzione, per impostazione predefinita, il pulsante nel catalogo utilizzerà il collegamento fornito per `Distributor page` , che potrebbe non essere specifico del dispositivo. Idealmente, l'URL del server di distribuzione deve condurre a una pagina specifica in cui un cliente può acquistare un dispositivo, ma non è obbligatorio. Se il server di distribuzione è lo stesso del produttore, questo URL potrebbe essere uguale a quello della pagina di marketing del produttore. *)

1. Fare clic `Save` per confermare le informazioni.
1. Fare clic `Project summary` nella parte superiore della pagina per tornare alla pagina di riepilogo.

## <a name="declaring-additional-industry-certifications"></a>Dichiarazione di certificazioni di settore aggiuntive

È anche possibile alzare di livello ulteriori certificazioni di settore eventualmente ricevute per il dispositivo. Queste certificazioni possono contribuire a offrire maggiore chiarezza sull'uso previsto del dispositivo e potranno essere ricercate nel catalogo dei dispositivi Azure Certified.

1. Fare clic `Add` nella sezione "fornire certificazioni di settore".
1. Fare clic `Add a certification` per effettuare una selezione da un elenco di programmi di certificazione per il settore comune. Se il prodotto ha raggiunto una certificazione non presente nell'elenco, è possibile specificare un valore stringa personalizzato selezionando `Other (please specify)` .
1. Facoltativamente, fornire una descrizione o note al revisore. Tuttavia, queste note non saranno disponibili pubblicamente per la visualizzazione nel catalogo.
1. Fare clic `Save` per confermare le informazioni.
1. Fare clic `Project summary` nella parte superiore della pagina per tornare alla pagina di riepilogo.

## <a name="next-steps"></a>Passaggi successivi

A questo punto è stato completato il processo di descrizione del dispositivo. Questo consentirà al team di revisione dei dispositivi Azure Certified e ai clienti di comprendere meglio il prodotto. Una volta soddisfatte le informazioni fornite, è ora possibile passare alla fase di test del processo di certificazione.
> [!div class="nextstepaction"]
> [Esercitazione: test del dispositivo](tutorial-03-testing-your-device.md)
