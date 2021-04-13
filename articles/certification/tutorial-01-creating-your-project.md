---
title: Programma Azure Certified Device-esercitazione-Creazione del progetto
description: Guida alla creazione di un progetto nel portale per i dispositivi Azure Certified
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: tutorial
ms.date: 03/01/2021
ms.custom: template-tutorial
ms.openlocfilehash: 11d72847209a2e706d4aa32d38af1b2c8af3dfa0
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313998"
---
# <a name="tutorial-create-your-project"></a>Esercitazione: creare il progetto

Congratulazioni per aver scelto di certificare il dispositivo tramite il programma Azure Certified Device. A questo punto è stato selezionato il programma di certificazione appropriato per il dispositivo e si è pronti per iniziare a usare il portale.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Accedere al portale per i [dispositivi Azure Certified](https://certify.azure.com/)
> * Creare un nuovo progetto di certificazione per il dispositivo
> * Specificare i dettagli del dispositivo di base del progetto

## <a name="prerequisites"></a>Prerequisiti

- È necessario un [account di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)aziendale/dell'Istituto di istruzione valido.
- È necessario un account verificato Microsoft Partner Network (MPN). Se non si dispone di un account MPN, [partecipare alla rete partner](https://partner.microsoft.com/) prima di iniziare.

## <a name="signing-into-the-azure-certified-device-portal"></a>Accesso al portale per i dispositivi Azure Certified

Per iniziare, è necessario accedere al portale, in cui verranno fornite le informazioni sul dispositivo, il completamento del test di certificazione e la gestione delle pubblicazioni del dispositivo nel catalogo dei dispositivi Azure Certified.

1. Passare al portale per i [dispositivi Azure Certified](https://certify.azure.com).
1. Selezionare `Company profile` sul lato sinistro e aggiornare le informazioni del produttore.
   ![Sezione profilo società](./media/images/company-profile.png)
1. Accettare il contratto di programma per iniziare il progetto.

## <a name="creating-your-project-on-the-portal"></a>Creazione del progetto nel portale

Ora che sono state configurate tutte le impostazioni nel portale, è possibile iniziare il processo di certificazione. Per prima cosa, è necessario creare un progetto per il dispositivo.

1. Nella schermata iniziale selezionare `Create new project` . Verrà aperta una finestra per aggiungere informazioni sul dispositivo di base nella sezione successiva.

 ![Immagine del pulsante Crea nuovo progetto](./media/images/create-new-project.png)

## <a name="identifying-basic-device-information"></a>Identificazione delle informazioni di base sul dispositivo

Quindi, è necessario fornire le informazioni di base sul dispositivo. È possibile modificare queste informazioni in un secondo momento.

1. Completare i campi richiesti nella `Basics` sezione. Per chiarimenti sui campi **obbligatori** , vedere la tabella seguente:

    | Campi                  | Descrizione                                                                                                                         |
    |------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
    | Project name (Nome progetto)           | Nome interno che non sarà visibile nel catalogo dei dispositivi Azure Certified                                                        |
    | Nome del dispositivo            | Nome pubblico del dispositivo                                                                                                |
    | Tipo di dispositivo            | Specifica del prodotto finito o del kit di sviluppo Solution-Ready.     Per ulteriori informazioni sulla terminologia, vedere [Glossario della certificazione](./resources-glossary.md).                                                                     |
    | Classe Device           | Gateway, sensore o altro.  Per ulteriori informazioni sulla terminologia, vedere [Glossario della certificazione](./resources-glossary.md).                                                                    |
    | URL del codice sorgente del dispositivo | Obbligatorio se si sta certificando un Solution-Ready Dev Kit, in caso contrario, facoltativo. L'URL deve essere in un percorso di GitHub per il codice del dispositivo. |
1. Selezionare il `Next` pulsante per passare alla `Certifications` scheda.

    ![Immagine del modulo Crea nuovo progetto, scheda certificazioni](./media/images/create-new-project-certificationswindow.png)

1. Specificare le certificazioni che si desidera ottenere per il dispositivo.
1. Selezionare `Create` e il nuovo progetto verrà salvato e visualizzato nella Home page del portale.

    ![Immagine della tabella Project](./media/images/project-table.png)

1. Selezionare il nome del progetto nella tabella. Verrà avviata la pagina di riepilogo del progetto in cui è possibile aggiungere e visualizzare altri dettagli sul dispositivo.

    ![Immagine della pagina dei dettagli del progetto](./media/images/device-details-section.png)

## <a name="next-steps"></a>Passaggi successivi

A questo punto è possibile aggiungere i dettagli del dispositivo ed eseguire il test del dispositivo con il servizio di certificazione. Passare all'articolo successivo per informazioni su come modificare i dettagli del dispositivo.
> [!div class="nextstepaction"]
> [Esercitazione: aggiunta di dettagli sul dispositivo](tutorial-02-adding-device-details.md)
