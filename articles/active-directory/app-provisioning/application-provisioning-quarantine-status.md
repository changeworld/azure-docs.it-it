---
title: Stato del provisioning dell'applicazione della quarantena | Microsoft Docs
description: Quando è stata configurata un'applicazione per il provisioning utenti automatico, scoprire che cosa significa lo stato di provisioning della quarantena e come eliminarlo.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 03/18/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 49590c46588ad0d0f1c1b7b095679a3c3fce96eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104579502"
---
# <a name="application-provisioning-in-quarantine-status"></a>Provisioning dell'applicazione in stato di quarantena

Il servizio di provisioning Azure AD monitora lo stato della configurazione. Inserisce anche app non integre nello stato "quarantena". Se la maggior parte delle chiamate effettuate sul sistema di destinazione ha esito negativo, il processo di provisioning viene contrassegnato come in quarantena. Un esempio di errore è un errore ricevuto a causa di credenziali di amministratore non valide.

In quarantena:
- La frequenza dei cicli incrementali viene gradualmente ridotta a una volta al giorno.
- Il processo di provisioning viene rimosso dalla quarantena dopo che tutti gli errori sono stati risolti e viene avviato il ciclo di sincronizzazione successivo. 
- Se il processo di provisioning rimane in quarantena per più di quattro settimane, il processo di provisioning viene disabilitato (arresta l'esecuzione).

## <a name="how-do-i-know-if-my-application-is-in-quarantine"></a>Ricerca per categorie verificare se l'applicazione è in quarantena?

Esistono tre modi per verificare se un'applicazione è in quarantena:
  
- Nella portale di Azure passare a **Azure Active Directory**  >  **applicazioni aziendali**  >  &lt; *nome applicazione* &gt;  >  **provisioning** ed esaminare l'indicatore di stato per un messaggio di quarantena.   

  ![Barra di stato del provisioning che mostra lo stato della quarantena](./media/application-provisioning-quarantine-status/progress-bar-quarantined.png)

- Nella portale di Azure passare a **Azure Active Directory**  >  **log di controllo** > filtro per **attività: quarantena** e verificare la cronologia di quarantena. La visualizzazione nell'indicatore di stato, come descritto in precedenza, Mostra se il provisioning è attualmente in quarantena. I log di controllo mostrano la cronologia di quarantena per un'applicazione. 

- Usare la richiesta Microsoft Graph [Get synchronizationJob](/graph/api/synchronization-synchronizationjob-get?tabs=http&view=graph-rest-beta&preserve-view=true) per ottenere lo stato del processo di provisioning a livello di codice:

```microsoft-graph
        GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/
```

- Controllare la posta elettronica. Quando un'applicazione viene messa in quarantena, viene inviato un messaggio di posta elettronica di notifica monouso. Se il motivo della quarantena cambia, viene inviato un messaggio di posta elettronica aggiornato che mostra il nuovo motivo della quarantena. Se non viene visualizzato un messaggio di posta elettronica:

  - Assicurarsi di aver specificato un messaggio di **posta elettronica di notifica** valido nella configurazione del provisioning per l'applicazione.
  - Assicurarsi che non sia presente alcun filtro per la posta indesiderata nella cartella posta in arrivo della notifica.
  - Assicurarsi che non sia stata annullata la sottoscrizione ai messaggi di posta elettronica.
  - Cerca i messaggi di posta elettronica da `azure-noreply@microsoft.com`

## <a name="why-is-my-application-in-quarantine"></a>Perché l'applicazione è in quarantena?

|Descrizione|Azione consigliata|
|---|---|
|**Problema di conformità di SCIM:** È stata restituita una risposta HTTP/404 non trovata anziché la risposta HTTP/200 OK prevista. In questo caso, il servizio di provisioning Azure AD ha effettuato una richiesta all'applicazione di destinazione e ha ricevuto una risposta imprevista.|Controllare la sezione credenziali amministratore. Verificare se l'applicazione richiede la specifica dell'URL del tenant e che l'URL sia corretto. Se non viene visualizzato alcun problema, contattare lo sviluppatore dell'applicazione per assicurarsi che il servizio sia conforme a SCIM. https://tools.ietf.org/html/rfc7644#section-3.4.2 |
|**Credenziali non valide:** Quando si tenta di autorizzare, accedere all'applicazione di destinazione, è stata ricevuta una risposta dall'applicazione di destinazione che indica che le credenziali specificate non sono valide.|Passare alla sezione credenziali amministratore dell'interfaccia utente di configurazione del provisioning e autorizzare di nuovo l'accesso con credenziali valide. Se l'applicazione si trova nella raccolta, vedere l'esercitazione sulla configurazione dell'applicazione per più passaggi necessari.|
|**Ruoli duplicati:** I ruoli importati da alcune applicazioni, ad esempio Salesforce e Zendesk, devono essere univoci. |Passare al [manifesto](../develop/reference-app-manifest.md) dell'applicazione nella portale di Azure e rimuovere il ruolo duplicato.|

 Una richiesta di Microsoft Graph per ottenere lo stato del processo di provisioning Mostra il motivo seguente per la quarantena:
- `EncounteredQuarantineException` indica che sono state specificate credenziali non valide. Il servizio di provisioning non è in grado di stabilire una connessione tra il sistema di origine e il sistema di destinazione.
- `EncounteredEscrowProportionThreshold` indica che il provisioning ha superato la soglia del deposito. Questa condizione si verifica quando non è possibile eseguire più del 40% degli eventi di provisioning. Per ulteriori informazioni, vedere i dettagli della soglia di deposito di seguito.
- `QuarantineOnDemand` indica che è stato rilevato un problema con l'applicazione e che è stato impostato manualmente per la quarantena.

**Soglie di deposito**

Se viene raggiunta la soglia di deposito proporzionale, il processo di provisioning entra in quarantena. Questa logica è soggetta a modifiche, ma funziona approssimativamente come descritto di seguito: 

Un processo può entrare in quarantena, indipendentemente dal numero di errori per i problemi, ad esempio le credenziali di amministratore o la conformità SCIM. Tuttavia, in generale, gli errori 5.000 sono il requisito minimo per iniziare a valutare se mettere in quarantena a causa di un numero eccessivo di errori. Ad esempio, un processo con 4.000 errori non entra in quarantena. Un processo con 5.000 errori, tuttavia, attiverà una valutazione. Una valutazione usa i criteri seguenti:  
- Se più del 40% degli eventi di provisioning ha esito negativo o se sono presenti più di 40.000 errori, il processo di provisioning entra in quarantena. Gli errori di riferimento non verranno conteggiati come parte della soglia 40% o 40.000. Ad esempio, l'impossibilità di aggiornare un Manager o un membro del gruppo è un errore di riferimento.
- Un processo in cui 45.000 utenti con provisioning non riuscito porterebbe a una quarantena poiché supera la soglia di 40.000.
- Un processo in cui 30.000 utenti che non hanno superato il provisioning e 5.000 ha avuto esito positivo causa la quarantena poiché supera la soglia 40% e il valore minimo 5.000.
- Un processo con 20.000 errori e 100.000 esito positivo non entra in quarantena perché non supera la soglia di errore 40% o il numero massimo di errori 40.000.  
- Esiste una soglia assoluta di 60.000 errori che rappresenta gli errori di riferimento e non di riferimento. Ad esempio, non è stato possibile eseguire il provisioning degli utenti 40.000 e 21.000 aggiornamenti del gestore non riusciti. Il totale è di 61.000 errori e supera il limite di 60.000.


## <a name="how-do-i-get-my-application-out-of-quarantine"></a>Ricerca per categorie dismettere in quarantena l'applicazione?

Per prima cosa, risolvere il problema che ha causato la messa in quarantena dell'applicazione.

- Controllare le impostazioni di provisioning dell'applicazione per verificare di avere [immesso credenziali amministrative valide](../app-provisioning/configure-automatic-user-provisioning-portal.md#configuring-automatic-user-account-provisioning). Azure AD necessario stabilire una relazione di trust con l'applicazione di destinazione. Assicurarsi di aver immesso le credenziali valide e che l'account disponga delle autorizzazioni necessarie.

- Esaminare i [log di provisioning](../reports-monitoring/concept-provisioning-logs.md) per esaminare in modo approfondito gli errori che causano la quarantena e risolvere l'errore. Passare a **Azure Active Directory** &gt; i log di provisioning di **app aziendali** &gt; **(anteprima)** nella sezione **attività** .

Dopo aver risolto il problema, riavviare il processo di provisioning. Alcune modifiche apportate alle impostazioni di provisioning dell'applicazione, ad esempio i mapping degli attributi o i filtri di ambito, riavvieranno automaticamente il provisioning. L'indicatore di stato nella pagina di **provisioning** dell'applicazione indica il momento dell'ultimo avvio del provisioning. Se è necessario riavviare manualmente il processo di provisioning, usare uno dei metodi seguenti:  

- Usare il portale di Azure per riavviare il processo di provisioning. Nella pagina **provisioning** dell'applicazione selezionare Riavvia il **provisioning**. Questa azione Riavvia completamente il servizio di provisioning, operazione che può richiedere del tempo. Un ciclo iniziale completo verrà eseguito di nuovo, che cancella i limiti di deposito, rimuove l'app dalla quarantena e cancella tutte le filigrane. Il servizio valuterà quindi tutti gli utenti nel sistema di origine e determinerà se sono inclusi nell'ambito per il provisioning. Questa operazione può essere utile quando l'applicazione è attualmente in quarantena, come illustrato in questo articolo, oppure è necessario apportare una modifica ai mapping degli attributi. Si noti che il completamento del ciclo iniziale richiede più tempo rispetto al ciclo incrementale tipico a causa del numero di oggetti che devono essere valutati. Altre informazioni sulle prestazioni dei cicli iniziali e incrementali sono disponibili [qui](application-provisioning-when-will-provisioning-finish-specific-user.md).

- Usare Microsoft Graph per [riavviare il processo di provisioning](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta&preserve-view=true). Si avrà il controllo completo su ciò che viene riavviato. È possibile scegliere di cancellare i riconoscimenti (per riavviare il contatore del deposito per lo stato di quarantena), cancellare la quarantena (per rimuovere l'applicazione dalla quarantena) o cancellare le filigrane. Usare la richiesta seguente:
 
```microsoft-graph
        POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart
```

Sostituire "{ID}" con il valore dell'ID applicazione e sostituire "{jobId}" con l' [ID del processo di sincronizzazione](/graph/api/resources/synchronization-configure-with-directory-extension-attributes?tabs=http&view=graph-rest-beta&preserve-view=true#list-synchronization-jobs-in-the-context-of-the-service-principal).
