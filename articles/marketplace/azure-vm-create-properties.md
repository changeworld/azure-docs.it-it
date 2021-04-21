---
title: Configurare le proprietà dell'offerta di macchina virtuale Azure Marketplace
description: Informazioni su come configurare le proprietà dell'offerta di macchina virtuale Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: mingshen
ms.date: 10/19/2020
ms.openlocfilehash: 5942368ba1709127b815a35676b716955c1bee8f
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819089"
---
# <a name="configure-virtual-machine-offer-properties"></a>Configurare le proprietà dell'offerta di macchina virtuale

Nella  pagina Proprietà (selezionare dal menu di spostamento a sinistra) definire le categorie usate per raggruppare l'offerta di macchina virtuale (VM) in Azure Marketplace, la versione dell'applicazione e i contratti legali che supportano l'offerta.

## <a name="select-a-category"></a>Selezionare una categoria

Selezionare categorie e sottocategorie per inserire l'offerta nelle aree di Azure Marketplace di ricerca appropriate. Assicurarsi di descrivere più avanti nella descrizione dell'offerta come l'offerta supporta queste categorie.

- Selezionare una categoria Primaria.
- Per aggiungere una seconda categoria facoltativa (Secondaria), selezionare il **collegamento +Categorie.**
- Selezionare fino a due sottocategorie per la categoria Primaria e/o Secondaria. Se all'offerta non è applicabile alcuna sottocategoria, selezionare **Non applicabile.** Premere CTRL+clic per selezionare una seconda sottocategoria.

Vedere l'elenco completo delle categorie e delle sottocategorie in Procedure consigliate per [l'inserzione dell'offerta.](gtm-offer-listing-best-practices.md) Le offerte di macchine virtuali vengono sempre visualizzate nella **categoria** Calcolo Azure Marketplace.

## <a name="provide-terms-and-conditions"></a>Specificare termini e condizioni

In **Note legali** specificare i termini e le condizioni per l'offerta. Sono disponibili due opzioni:

- [Usare il contratto standard con modifiche facoltative](#use-the-standard-contract)
- [Usare termini e condizioni personalizzati](#use-your-own-terms-and-conditions)

Per informazioni sul contratto standard e sulle modifiche facoltative, vedere contratto standard [per il marketplace commerciale Microsoft.](standard-contract.md) È possibile scaricare [il contratto standard](https://go.microsoft.com/fwlink/?linkid=2041178) PDF (assicurarsi che il blocco popup sia disattivato).

### <a name="use-the-standard-contract"></a>Usare il contratto standard

Per semplificare il processo di approvvigionamento per i clienti e ridurre la complessità legale per i fornitori di software, Microsoft offre un contratto standard che è possibile usare per le offerte nel marketplace commerciale. Quando si offre il software con il contratto standard, i clienti devono leggerlo e accettarlo una sola volta e non è necessario creare termini e condizioni personalizzati.

1. Selezionare la **casella di controllo Use the contratto standard for Microsoft's commercial marketplace** (Usa il contratto standard per il marketplace commerciale Di Microsoft).

   ![Illustra la casella di controllo Usa contratto standard per il marketplace commerciale di Microsoft.](partner-center-portal/media/use-standard-contract.png)

1. Nella finestra **di** dialogo Conferma selezionare **Accetta**. A seconda delle dimensioni dello schermo, potrebbe essere necessario scorrere verso l'alto per visualizzarlo.
1. Prima di continuare, selezionare **Salva bozza**.

   > [!NOTE]
   > Dopo aver pubblicato un'offerta usando il contratto standard per il marketplace commerciale, non è possibile usare termini e condizioni personalizzati. Offrire la soluzione nel contratto standard con modifiche facoltative o in base alle proprie condizioni.

#### <a name="add-amendments-to-the-standard-contract-optional"></a>Aggiungere modifiche al contratto standard (facoltativo)

Sono disponibili due tipi di modifiche: *universale* e *personalizzato.*

##### <a name="add-universal-amendment-terms"></a>Aggiungere termini di modifica universali

Nella casella **Termini di modifica universale per il contratto standard per il marketplace** commerciale di Microsoft immettere le condizioni per la modifica universale. In questa casella è possibile immettere un numero illimitato di caratteri. Queste condizioni vengono visualizzate dai clienti in AppSource, Azure Marketplace e/o nel portale di Azure durante il flusso di individuazione e acquisto.

##### <a name="add-one-or-more-custom-amendments"></a>Aggiungere una o più modifiche personalizzate

1. In **Custom amendments terms to the contratto standard for Microsoft's commercial marketplace** selezionare il collegamento Add custom amendment term **(Max 10) (Aggiungi** termine di modifica personalizzato (massimo 10).
2. Immettere i **termini della modifica personalizzata** nella casella.
3. Immettere **l'ID tenant** nella casella . Solo i clienti associati agli ID tenant specificati per queste condizioni personalizzate verranno visualizzati nel flusso di acquisto dell'offerta nel portale di Azure.

   > [!TIP]
   > Un ID tenant identifica il cliente in Azure. È possibile chiedere questo ID al cliente e trovarlo selezionando Azure Active Directory [**https://portal.azure.com**](https://portal.azure.com)  >    >  **proprietà**. Il valore dell'ID directory è l'ID tenant , ad esempio `50c464d3-4930-494c-963c-1e951d15360e` . È anche possibile cercare l'ID tenant dell'organizzazione del cliente usandone l'URL del nome di dominio in [What is my Microsoft Azure and Office 365 tenant ID?](https://www.whatismytenantid.com/).

4. Facoltativamente, immettere una **descrizione descrittiva per** l'ID tenant. Questa descrizione consente di identificare il cliente di destinazione della modifica.
5. Per aggiungere un altro ID tenant, selezionare il collegamento Aggiungi ID tenant di un cliente **(massimo 10)** e ripetere i passaggi 3 e 4. È possibile aggiungere fino a 20 ID tenant.
6. Per aggiungere un altro termine di modifica, ripetere i passaggi da 1 a 5. È possibile specificare fino a dieci condizioni di modifica personalizzate per offerta.
7. Prima di continuare, selezionare **Salva bozza**.

### <a name="use-your-own-terms-and-conditions"></a>Usare termini e condizioni personalizzati

È possibile fornire termini e condizioni personalizzati anziché usare il contratto standard. I clienti devono accettare questi termini prima di poter provare l'offerta.

1. In **Legal**(Legale) **deselezionare la casella di controllo Use the contratto standard for Microsoft's commercial marketplace** (Usa il contratto standard per il marketplace commerciale Di Microsoft).
1. Nella casella **Termini e condizioni** immettere fino a 10.000 caratteri di testo.

   > [!NOTE]
   > Se è necessaria una descrizione più lunga, immettere un singolo indirizzo Web che punti a dove si trovano i termini e le condizioni. che verrà visualizzato dai clienti come collegamento attivo.

1. Selezionare **Salva bozza** prima di continuare con la scheda successiva nel menu di spostamento a sinistra, **Presentazione dell'offerta.**

## <a name="next-steps"></a>Passaggi successivi

- [Configurare l'inserzione dell'offerta di VM](azure-vm-create-listing.md)
