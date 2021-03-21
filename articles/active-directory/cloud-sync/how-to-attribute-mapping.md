---
title: Mapping degli attributi in Azure AD Connect sincronizzazione cloud
description: Questo articolo descrive come usare la funzionalità di sincronizzazione cloud di Azure AD Connect per eseguire il mapping degli attributi.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/21/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdb043374cf6252da3929c8f0cda6c0a4be558b7
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555211"
---
# <a name="attribute-mapping-in-azure-ad-connect-cloud-sync"></a>Mapping degli attributi in Azure AD Connect sincronizzazione cloud

È possibile usare la funzionalità di sincronizzazione cloud di Azure Active Directory (Azure AD) Connect per eseguire il mapping degli attributi tra gli oggetti utente o gruppo locale e gli oggetti in Azure AD. Questa funzionalità è stata aggiunta alla configurazione di sincronizzazione cloud.

È possibile personalizzare (modificare, eliminare o creare) i mapping degli attributi predefiniti in base alle esigenze aziendali. Per un elenco di attributi sincronizzati, vedere [attributi sincronizzati con Azure Active Directory](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md).

## <a name="understand-types-of-attribute-mapping"></a>Informazioni sui tipi di mapping degli attributi
Con il mapping degli attributi è possibile controllare la modalità di popolamento degli attributi in Azure AD. Azure AD supporta quattro tipi di mapping:

- **Direct**: l'attributo di destinazione viene popolato con il valore di un attributo dell'oggetto collegato in Active Directory.
- **Costante**: l'attributo di destinazione viene popolato con una stringa specifica specificata.
- **Espressione**: l'attributo di destinazione viene popolato in base al risultato di un'espressione simile a uno script. Per ulteriori informazioni, vedere [scrittura di espressioni per i mapping degli attributi in Azure Active Directory](reference-expressions.md).
- **None**: l'attributo di destinazione viene lasciato invariato. Tuttavia, se l'attributo di destinazione è sempre vuoto, viene popolato con il valore predefinito specificato.

Insieme a questi tipi di base, i mapping degli attributi personalizzati supportano il concetto di assegnazione di un valore *predefinito* facoltativo. L'assegnazione del valore predefinito assicura che un attributo di destinazione venga popolato con un valore se Azure AD o se l'oggetto di destinazione non dispone di un valore. Nella maggior parte delle configurazioni questo campo viene lasciato vuoto.

## <a name="understand-properties-of-attribute-mapping"></a>Informazioni sulle proprietà del mapping degli attributi

Insieme alla proprietà Type, i mapping degli attributi supportano i seguenti attributi:

- **Attributo di origine**: attributo utente del sistema di origine, ad esempio: Active Directory.
- **Attributo di destinazione**: attributo utente nel sistema di destinazione, ad esempio Azure Active Directory.
- **Valore predefinito se null (facoltativo)**: valore che verrà passato al sistema di destinazione se l'attributo di origine è null. Il provisioning di questo valore verrà eseguito solo quando viene creato un utente. Non verrà eseguito il provisioning quando si aggiorna un utente esistente.  
- **Applica questo mapping**:
  - **Sempre**: applica il mapping sia alle azioni di creazione che di aggiornamento dell'utente.
  - **Solo durante la creazione**: applicare questo mapping solo alle azioni di creazione dell'utente.

> [!NOTE]
> Questo articolo descrive come usare la portale di Azure per eseguire il mapping degli attributi.  Per informazioni sull'uso di Microsoft Graph, vedere [trasformazioni](how-to-transformation.md).

## <a name="add-an-attribute-mapping"></a>Aggiungere un mapping di attributi

Per utilizzare la nuova funzionalità, attenersi alla seguente procedura:

1.  Nel portale di Azure selezionare **Azure Active Directory**.
2.  Selezionare **Azure AD Connect**.
3.  Selezionare **Gestisci sincronizzazione cloud**.

    ![Screenshot che mostra il collegamento per la gestione della sincronizzazione cloud.](media/how-to-install/install-6.png)

4. In **configurazione** selezionare la configurazione.
5. Selezionare **fare clic per modificare i mapping**.  Questo collegamento apre la schermata **mapping attributi** .

    ![Screenshot che mostra il collegamento per l'aggiunta di attributi.](media/how-to-attribute-mapping/mapping-6.png)

6.  Selezionare **Aggiungi attributo**.

    ![Screenshot che mostra il pulsante per l'aggiunta di un attributo, insieme a elenchi di attributi e tipi di mapping.](media/how-to-attribute-mapping/mapping-1.png)

7. Selezionare il tipo di mapping. Per questo esempio viene usata l' **espressione**.
8. Immettere l'espressione nella casella. Per questo esempio viene usato `Replace([mail], "@contoso.com", , ,"", ,)` .
9. Immettere l'attributo di destinazione. Per questo esempio viene usato **ExtensionAttribute15**.
10. Selezionare quando applicare questo mapping, quindi selezionare **applica**.

    ![Screenshot che mostra le caselle compilate per la creazione di un mapping di attributi.](media/how-to-attribute-mapping/mapping-2a.png)

11. Tornando alla schermata **mapping attributi** , verrà visualizzato il nuovo mapping degli attributi.  
12. Selezionare **Salva schema**.

    ![Screenshot che mostra il pulsante Salva schema.](media/how-to-attribute-mapping/mapping-3.png)

## <a name="test-your-attribute-mapping"></a>Testare il mapping degli attributi

Per testare il mapping degli attributi, è possibile usare [il provisioning su richiesta](how-to-on-demand-provision.md): 

1. Nel portale di Azure selezionare **Azure Active Directory**.
2. Selezionare **Azure AD Connect**.
3. Selezionare **Gestisci provisioning**.
4. In **configurazione** selezionare la configurazione.
5. In **convalida** selezionare il pulsante Esegui il **provisioning di un utente** . 
6. Nella schermata **provisioning on demand** immettere il nome distinto di un utente o un gruppo e selezionare il pulsante **provision (provisioning** ). 

   La schermata indica che è in corso il provisioning.

   ![Screenshot che mostra il provisioning in corso.](media/how-to-attribute-mapping/mapping-4.png)

8. Al termine del provisioning, viene visualizzata una schermata operazione completata con quattro segni di spunta verdi. 

   In **Esegui azione** selezionare **Visualizza dettagli**. A destra dovrebbe essere visualizzato il nuovo attributo Synchronized e l'espressione applicato.

   ![Screenshot che mostra i dettagli relativi a esito positivo ed esportazione.](media/how-to-attribute-mapping/mapping-5.png)

## <a name="next-steps"></a>Passaggi successivi

- [Che cos'è Azure AD Connect sincronizzazione cloud?](what-is-cloud-sync.md)
- [Scrittura di espressioni per i mapping degli attributi](reference-expressions.md)
- [Attributi sincronizzati con Azure Active Directory](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md)
