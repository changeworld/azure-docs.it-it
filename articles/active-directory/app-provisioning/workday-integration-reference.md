---
title: Guida di riferimento all'integrazione Azure Active Directory e giorni lavorativi
description: Approfondimento tecnico sulla giornata lavorativa-provisioning basato sulle risorse umane
services: active-directory
author: cmmdesai
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 01/18/2021
ms.author: chmutali
ms.openlocfilehash: f260bca196839a091ae7d12be6d5f85912bf92db
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99255985"
---
# <a name="how-azure-active-directory-provisioning-integrates-with-workday"></a>Integrazione di Azure Active Directory provisioning con la giornata lavorativa

[Azure Active Directory il servizio di provisioning utenti](../app-provisioning/user-provisioning.md) si integra con il [giorno lavorativo HCM](https://www.workday.com) per gestire il ciclo di vita dell'identità degli utenti. Azure Active Directory offre tre integrazioni predefinite: 

* [Giorni lavorativi per il provisioning dell'utente Active Directory locale](../saas-apps/workday-inbound-tutorial.md)
* [Giorni lavorativi per Azure Active Directory provisioning degli utenti](../saas-apps/workday-inbound-cloud-only-tutorial.md)
* [Workday Writeback](../saas-apps/workday-writeback-tutorial.md)

Questo articolo illustra come funziona l'integrazione e come è possibile personalizzare il comportamento di provisioning per diversi scenari HR. 

## <a name="establishing-connectivity"></a>Creazione della connettività 

### <a name="restricting-workday-api-access-to-azure-ad-endpoints"></a>Limitazione dell'accesso dell'API per la giornata lavorativa agli endpoint Azure AD
Azure AD servizio di provisioning usa l'autenticazione di base per connettersi agli endpoint dell'API dei servizi Web di giorno lavorativo.  

Per proteggere ulteriormente la connettività tra Azure AD servizio di provisioning e la giornata lavorativa, è possibile limitare l'accesso in modo che l'utente del sistema di integrazione designato acceda solo alle API dei giorni lavorativi da intervalli IP consentiti Azure AD. Per completare la configurazione seguente nel tenant della giornata lavorativa, rivolgersi all'amministratore della giornata lavorativa. 

1. Scaricare gli [intervalli IP più recenti](https://www.microsoft.com/download/details.aspx?id=56519) per il cloud pubblico di Azure. 
1. Aprire il file e cercare il tag **AzureActiveDirectory** 

   >[!div class="mx-imgBorder"] 
   >![Azure AD intervallo IP](media/sap-successfactors-integration-reference/azure-active-directory-ip-range.png)

1. Copiare tutti gli intervalli di indirizzi IP elencati nell'elemento *addressPrefixes* e usare l'intervallo per compilare l'elenco di indirizzi IP.
1. Accedere al portale di amministrazione della giornata lavorativa. 
1. Accedere all'attività **Mantieni intervalli IP** per creare un nuovo intervallo di indirizzi IP per i Data Center di Azure. Specificare gli intervalli IP (usando la notazione CIDR) come elenco delimitato da virgole.  
1. Accedere all'attività **Gestisci criteri di autenticazione** per creare nuovi criteri di autenticazione. Nel criterio di autenticazione usare l'elenco Consenti autenticazione per specificare l'intervallo di indirizzi IP Azure AD e il gruppo di sicurezza a cui sarà consentito l'accesso da questo intervallo IP. Salvare le modifiche. 
1. Accedere all'attività **attiva tutte le modifiche ai criteri di autenticazione in sospeso** per confermare le modifiche.

### <a name="limiting-access-to-worker-data-in-workday-using-constrained-security-groups"></a>Limitazione dell'accesso ai dati del ruolo di lavoro durante la giornata lavorativa con gruppi di sicurezza vincolati

I passaggi predefiniti per [configurare l'utente del sistema di integrazione della giornata lavorativa](../saas-apps/workday-inbound-tutorial.md#configure-integration-system-user-in-workday) concedono l'accesso per recuperare tutti gli utenti nel tenant della giornata lavorativa. In alcuni scenari di integrazione, è possibile limitare l'accesso, in modo che gli utenti appartenenti solo a determinate organizzazioni di supervisione vengano restituiti dalla chiamata API di Get_Workers ed elaborati dal connettore Azure AD della giornata lavorativa. 

È possibile soddisfare questo requisito collaborando con l'amministratore della giornata lavorativa e configurando i gruppi di sicurezza del sistema di integrazione vincolata. Per altre informazioni su come eseguire questa operazione, fare riferimento a [questo articolo della community di giorni lavorativi](https://community.workday.com/forums/customer-questions/620393) .*per accedere a questo articolo sono necessarie le credenziali di accesso alla community*.

Questa strategia di limitazione dell'accesso tramite ISSG vincolati (gruppi di sicurezza del sistema di integrazione) è particolarmente utile negli scenari seguenti: 
* **Scenario di implementazione** in più fasi: si ha un tenant di grandi dimensioni e si prevede di eseguire un'implementazione graduale della giornata lavorativa per Azure ad il provisioning automatizzato. In questo scenario, anziché escludere gli utenti che non rientrano nell'ambito della fase corrente con i filtri di ambito Azure AD, è consigliabile configurare ISSG vincolati in modo che solo i thread di lavoro nell'ambito siano visibili ai Azure AD.
* **Scenario con più processi di provisioning**: si dispone di un tenant di grandi dimensioni e di più domini di Active Directory, ciascuno dei quali supporta un'unità aziendale/divisione/azienda diversa. Per supportare questa topologia, è consigliabile eseguire più giorni lavorativi per Azure AD il provisioning di processi con ogni processo che effettua il provisioning di un set specifico di ruoli di lavoro. In questo scenario, anziché usare i filtri di ambito Azure AD per escludere i dati del ruolo di lavoro, è consigliabile configurare ISSG vincolati in modo che solo i dati del ruolo di lavoro rilevanti siano visibili ai Azure AD. 

### <a name="workday-test-connection-query"></a>Query di connessione test giorni lavorativi

Per testare la connettività alla giornata lavorativa, Azure AD invia le seguenti richieste di servizi Web di *Get_Workers* giorni lavorativi. 

```XML
<!-- Test connection query tries to retrieve one record from the first page -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to the test connection event -->
<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Date_Range_Data>
        <p1:Updated_From>2021-01-19T02:28:50.1491022Z</p1:Updated_From>
        <p1:Updated_Through>2021-01-19T02:28:50.1491022Z</p1:Updated_Through>
      </p1:Transaction_Date_Range_Data>
    </p1:Transaction_Log_Criteria_Data>
    <p1:Exclude_Employees>true</p1:Exclude_Employees>
    <p1:Exclude_Contingent_Workers>true</p1:Exclude_Contingent_Workers>
    <p1:Exclude_Inactive_Workers>true</p1:Exclude_Inactive_Workers>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:28:50.1491022Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:28:50.1491022Z</p1:As_Of_Entry_DateTime>
    <p1:Page>1</p1:Page>
    <p1:Count>1</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
  </p1:Response_Group>
</Get_Workers_Request>
```

## <a name="how-full-sync-works"></a>Funzionamento della sincronizzazione completa

La **sincronizzazione completa** nel contesto del provisioning basato sulla giornata lavorativa si riferisce al processo di recupero di tutte le identità dalla giornata lavorativa e alla determinazione delle regole di provisioning da applicare a ogni oggetto di lavoro. La sincronizzazione completa si verifica quando si attiva il provisioning per la prima volta e anche quando si *Riavvia il provisioning* dal portale di Azure o usando le API Graph. 

Azure AD invia le seguenti richieste di servizi Web *Get_Workers* giorni lavorativi per recuperare i dati del ruolo di lavoro. La query cerca il log delle transazioni della giornata lavorativa per tutte le voci di lavoro con data di validità pari al tempo corrispondente all'esecuzione della sincronizzazione completa. 

```XML
<!-- Workday full sync query -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to full sync run -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Type_References>
        <p1:Transaction_Type_Reference>
          <p1:ID p1:type="Business_Process_Type">Hire Employee</p1:ID>
        </p1:Transaction_Type_Reference>
        <p1:Transaction_Type_Reference>
          <p1:ID p1:type="Business_Process_Type">Contract Contingent Worker</p1:ID>
        </p1:Transaction_Type_Reference>
      </p1:Transaction_Type_References>
    </p1:Transaction_Log_Criteria_Data>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:29:16.0094202Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:29:16.0094202Z</p1:As_Of_Entry_DateTime>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Transaction_Log_Data>1</p1:Include_Transaction_Log_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```
Il nodo *Response_Group* viene usato per specificare quali attributi di lavoro recuperare dalla giornata lavorativa. Per una descrizione di ogni flag nel nodo *Response_Group* , vedere la [documentazione relativa all'API Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v35.2/Get_Workers.html#Worker_Response_GroupType)della giornata lavorativa. 

Determinati valori di flag specificati nel nodo *Response_Group* vengono calcolati in base agli attributi configurati nell'applicazione di provisioning Azure ad giorni lavorativi. Per i criteri usati per impostare i valori dei flag, vedere la sezione relativa alle *entità supportate* . 

La risposta *Get_Workers* dalla giornata lavorativa per la query precedente include il numero di record di lavoro e il conteggio delle pagine.

```XML
  <wd:Response_Results>
    <wd:Total_Results>509</wd:Total_Results>
    <wd:Total_Pages>17</wd:Total_Pages>
    <wd:Page_Results>30</wd:Page_Results>
    <wd:Page>1</wd:Page>
  </wd:Response_Results>
```
Per recuperare la pagina successiva del set di risultati, la query *Get_Workers* successiva specifica il numero di pagina come parametro nell' *Response_Filter*.

```XML
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:29:16.0094202Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:29:16.0094202Z</p1:As_Of_Entry_DateTime>
    <p1:Page>2</p1:Page>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
```
Azure AD servizio di provisioning elabora ogni pagina ed esegue l'iterazione di tutti i ruoli di lavoro effettivi durante la sincronizzazione completa. Per ogni voce di lavoro importata dalla giornata lavorativa:
* L' [espressione XPath](workday-attribute-reference.md) viene applicata per recuperare i valori di attributo da giorni lavorativi.
* Viene applicato il mapping degli attributi e le regole di corrispondenza 
* Il servizio determina quale operazione eseguire nella destinazione (Azure AD/AD). 

Al termine dell'elaborazione, Salva il timestamp associato all'inizio della sincronizzazione completa come filigrana. Questa filigrana funge da punto di partenza per il ciclo di sincronizzazione incrementale. 

## <a name="how-incremental-sync-works"></a>Funzionamento della sincronizzazione incrementale

Dopo la sincronizzazione completa, Azure AD servizio di provisioning `LastExecutionTimestamp` lo gestisce e lo usa per creare query Delta per recuperare le modifiche incrementali. Durante la sincronizzazione incrementale, Azure AD invia ai giorni lavorativi i tipi di query seguenti: 

* [Query per aggiornamenti manuali](#query-for-manual-updates)
* [Query per aggiornamenti e terminazioni con data di validità](#query-for-effective-dated-updates-and-terminations)
* [Query per assunzioni con data successiva](#query-for-future-dated-hires)

### <a name="query-for-manual-updates"></a>Query per aggiornamenti manuali

Il *Get_Workers* seguente richiede query per gli aggiornamenti manuali che si sono verificati tra l'ultima esecuzione e il tempo di esecuzione corrente. 

```xml
<!-- Workday incremental sync query for manual updates -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to last execution and current execution time -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Date_Range_Data>
        <p1:Updated_From>2021-01-19T02:29:16.0094202Z</p1:Updated_From>
        <p1:Updated_Through>2021-01-19T02:49:06.290136Z</p1:Updated_Through>
      </p1:Transaction_Date_Range_Data>
    </p1:Transaction_Log_Criteria_Data>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:49:06.290136Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:49:06.290136Z</p1:As_Of_Entry_DateTime>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```

### <a name="query-for-effective-dated-updates-and-terminations"></a>Query per aggiornamenti e terminazioni con data di validità

Il *Get_Workers* seguente richiede query per gli aggiornamenti con data di validità che si sono verificati tra l'ultima esecuzione e il tempo di esecuzione corrente. 

```xml
<!-- Workday incremental sync query for effective-dated updates -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to last execution and current execution time -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Date_Range_Data>
        <p1:Effective_From>2021-01-19T02:29:16.0094202Z</p1:Effective_From>
        <p1:Effective_Through>2021-01-19T02:49:06.290136Z</p1:Effective_Through>
      </p1:Transaction_Date_Range_Data>
    </p1:Transaction_Log_Criteria_Data>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:49:06.290136Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:49:06.290136Z</p1:As_Of_Entry_DateTime>
    <p1:Page>1</p1:Page>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```

### <a name="query-for-future-dated-hires"></a>Query per assunzioni con data successiva

Se una delle query sopra riportate restituisce una data di assunzione successiva, viene usata la richiesta di *Get_Workers* seguente per recuperare informazioni su un nuovo assunta con data successiva. L'attributo *wid* del nuovo noleggio viene usato per eseguire la ricerca e la data di validità viene impostata sulla data e sull'ora di assunzione. 

```xml
<!-- Workday incremental sync query to get new hire data effective as on hire date/first day of work -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below hire date/first day of work -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_References>
    <p1:Worker_Reference>
      <p1:ID p1:type="WID">7bf6322f1ea101fd0b4433077f09cb04</p1:ID>
    </p1:Worker_Reference>
  </p1:Request_References>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-02-01T08:00:00+00:00</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-02-01T08:00:00+00:00</p1:As_Of_Entry_DateTime>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```

## <a name="retrieving-worker-data-attributes"></a>Recupero degli attributi dei dati di lavoro

L'API *Get_Workers* può restituire set di dati diversi associati a un thread di lavoro. A seconda delle [espressioni API XPath](workday-attribute-reference.md) configurate nello schema di provisioning, Azure ad servizio di provisioning determina i set di dati da recuperare dalla giornata lavorativa. Di conseguenza, i flag di *Response_Group* vengono impostati nella richiesta di *Get_Workers* . 

La tabella seguente fornisce indicazioni sulla configurazione di mapping da usare per recuperare un set di dati specifico. 

| \# | Entità giorni lavorativi                       | Inclusa per impostazione predefinita | Modello XPATH da specificare nel mapping per recuperare entità non predefinite             |
|----|--------------------------------------|---------------------|-------------------------------------------------------------------------------|
| 1  | Dati personali                        | Sì                 | WD: dati di lavoro \_ /WD: \_ dati personali                                             |
| 2  | Dati sull'utilizzo                      | Sì                 | WD: dati del ruolo di lavoro \_ /WD: dati di lavoro \_                                           |
| 3  | Dati aggiuntivi del processo                  | Sì                 | WD: Worker \_ Data/WD: Employment \_ Data/WD: Worker \_ Job \_ data \[ @wd:Primary \_ Job = 0\]|
| 4  | Dati dell'organizzazione                    | Sì                 | WD: dati di lavoro \_ /WD: \_ dati organizzazione                                         |
| 5  | Dati della catena di gestione                | Sì                 | WD: Worker \_ Data/WD: \_ dati della catena di gestione \_                                    |
| 6  | Organizzazione supervisione             | Sì                 | VIGILANZA                                                                 |
| 7  | Company                              | Sì                 | AZIENDA                                                                     |
| 8  | Business Unit                        | No                  | ' BUSINESS \_ unit '                                                              |
| 9  | Gerarchia business unit              | No                  | ' \_ gerarchia business unit \_ '                                                   |
| 10 | Gerarchia aziendale                    | No                  | ' \_ gerarchia aziendale '                                                          |
| 11 | Centro di costo                          | No                  | ' centro di costo \_ '                                                                |
| 12 | Gerarchia del centro di costo                | No                  | "gerarchia del centro di costo \_ \_ "                                                     |
| 13 | Fondo                                 | No                  | FONDO                                                                        |
| 14 | Gerarchia Fund                       | No                  | ' FUND \_ Hierarchy '                                                             |
| 15 | Regalo                                 | No                  | REGALO                                                                        |
| 16 | Gerarchia Gift                       | No                  | ' gerarchia dei regali \_ '                                                             |
| 17 | Concedi                                | No                  | Grant                                                                       |
| 18 | Concedi gerarchia                      | No                  | ' GRANT \_ Hierarchy '                                                            |
| 19 | Gerarchia di siti aziendali              | No                  | ' \_ gerarchia dei siti aziendali \_ '                                                   |
| 20 | Organizzazione Matrix                  | No                  | MATRICE                                                                      |
| 21 | Gruppo di pagamento                            | No                  | ' paga \_ gruppo '                                                                  |
| 22 | Programmi                             | No                  | PROGRAMMI                                                                    |
| 23 | Gerarchia di programmi                    | No                  | ' \_ gerarchia dei programmi '                                                          |
| 24 | Region                               | No                  | ' REGION \_ Hierarchy '                                                           |
| 25 | Gerarchia località                   | No                  | ' \_ gerarchia località'                                                         |
| 26 | Dati di provisioning dell'account            | No                  | WD: dati di lavoro \_ /WD: \_ dati di provisioning dell'account \_                                |
| 27 | Dati di controllo in background                | No                  | WD: Worker \_ Data/WD: \_ dati di controllo sfondo \_                                    |
| 28 | Dati di idoneità per i vantaggi             | No                  | WD: dati di lavoro \_ /WD: \_ dati di idoneità per il vantaggio \_                                 |
| 29 | Vantaggi dei dati di registrazione              | No                  | WD: Worker \_ Data/WD: \_ dati di registrazione del vantaggio \_                                  |
| 30 | Dati di carriera                          | No                  | WD: Worker \_ Data/WD: \_ dati di carriera                                               |
| 31 | Dati di compensazione                    | No                  | WD: dati di lavoro \_ /WD: \_ dati di compensazione                                         |
| 32 | Dati dell'autorità fiscale del lavoro condizionale | No                  | WD: Worker \_ Data/WD: condizionale \_ Worker \_ \_ \_ form Authority \_ dati di tipo Form \_       |
| 33 | Dati degli elementi di sviluppo                | No                  | WD: Worker \_ Data/WD: \_ dati elemento di sviluppo \_                                    |
| 34 | Dati contratti dipendenti              | No                  | dati di WD: Worker \_ Data/WD: Employee \_ Contracts \_                                  |
| 35 | Dati di revisione dei dipendenti                 | No                  | WD: dati di lavoro \_ /WD: \_ dati di revisione \_ dei dipendenti                                     |
| 36 | Dati ricevuti dal feedback               | No                  | WD: Worker \_ Data/WD: feedback \_ ricevuti \_ dati                                   |
| 37 | Dati obiettivo del ruolo di lavoro                     | No                  | WD: dati di lavoro \_ /WD: \_ \_ dati obiettivo del ruolo di lavoro                                         |
| 38 | Dati foto                           | No                  | WD: dati di lavoro \_ /WD: \_ dati foto                                                |
| 39 | Dati di qualificazione                   | No                  | WD: dati di lavoro \_ /WD: dati di qualificazione \_                                        |
| 40 | Dati relativi alle persone correlate                 | No                  | WD: dati di lavoro \_ /WD: \_ dati relativi alle persone correlate \_                                     |
| 41 | Dati del ruolo                            | No                  | WD: Worker \_ Data/WD: Role \_ Data                                                 |
| 42 | Dati sulle competenze                           | No                  | WD: dati di lavoro \_ /WD: \_ dati sulle competenze                                                |
| 43 | Dati del profilo di successione              | No                  | WD: Worker \_ Data/WD: \_ dati del profilo di successione \_                                  |
| 44 | Dati di valutazione del talento               | No                  | WD: dati di lavoro \_ /WD: \_ dati di valutazione del talento \_                                   |
| 45 | Dati dell'account utente                    | No                  | WD: Worker \_ Data/WD: \_ dati dell'account utente \_                                        |
| 46 | Dati del documento di lavoro                 | No                  | WD: dati del ruolo di lavoro \_ /WD: dati del documento di lavoro \_ \_                                     |

>[!NOTE]
>Ogni entità della giornata lavorativa elencata nella tabella è protetta da un **criterio di sicurezza del dominio** nella giornata lavorativa. Se non è possibile recuperare alcun attributo associato all'entità dopo aver impostato l'XPATH destro, rivolgersi all'amministratore della giornata lavorativa per assicurarsi che i criteri di sicurezza del dominio appropriati siano configurati per l'utente del sistema di integrazione associato all'app di provisioning. Per recuperare i dati sulle *competenze*, ad esempio, è necessario *avere* accesso ai dati di lavoro del dominio della giornata lavorativa *: Competenze ed esperienza*. 

Di seguito sono riportati alcuni esempi su come è possibile estendere l'integrazione della giornata lavorativa per soddisfare requisiti specifici. 

**Esempio 1**

Si immagini di voler recuperare i set di dati seguenti da giorni lavorativi e usarli nelle regole di provisioning:

* Centro di costo
* Gerarchia del centro di costo
* Gruppo di pagamento

I set di dati sopra indicati non sono inclusi per impostazione predefinita. Per recuperare questi set di dati:
1. Accedere al portale di Azure e aprire la giornata lavorativa nell'app di provisioning utenti AD/Azure AD. 
1. Nel pannello provisioning modificare i mapping e aprire l'elenco degli attributi della giornata lavorativa dalla sezione Avanzate. 
1. Aggiungere le definizioni degli attributi seguenti e contrassegnarle come "obbligatoria". Questi attributi non verranno mappati ad alcun attributo in AD o Azure AD. Vengono semplicemente usati come segnali al connettore per recuperare il centro di costo, la gerarchia del centro di costo e le informazioni sul gruppo di pagamento. 

     > [!div class="mx-tdCol2BreakAll"]
     >| Nome attributo | Espressione API XPATH |
     >|---|---|
     >| CostCenterHierarchyFlag  |  WD: Worker/WD: Worker_Data/WD.: Organization_Data/WD.: Worker_Organization_Data [WD: Organization_Data/WD.: Organization_Type_Reference/WD.: ID [ @wd:type =' Organization_Type_ID '] =' COST_CENTER_HIERARCHY ']/wd:Organization_Reference/@wd:Descriptor |
     >| CostCenterFlag  |  WD: Worker/WD: Worker_Data/WD.: Organization_Data/WD.: Worker_Organization_Data [WD: Organization_Data/WD.: Organization_Type_Reference/WD.: ID [ @wd:type =' Organization_Type_ID '] =' COST_CENTER ']/WD.: Organization_Data/WD.: Organization_Code/Text () |
     >| PayGroupFlag  |  WD: Worker/WD: Worker_Data/WD.: Organization_Data/WD.: Worker_Organization_Data [WD: Organization_Data/WD.: Organization_Type_Reference/WD.: ID [ @wd:type =' Organization_Type_ID '] =' PAY_GROUP ']/WD.: Organization_Data/WD.: Organization_Reference_ID/Text () |

1. Quando il set di dati del centro di costo e del gruppo di pagamento è disponibile nella risposta *Get_Workers* , è possibile usare i valori XPath seguenti per recuperare il nome del centro di costo, il codice del centro di costo e il gruppo di pagamento. 

     > [!div class="mx-tdCol2BreakAll"]
     >| Nome attributo | Espressione API XPATH |
     >|---|---|
     >| CostCenterName  | WD: Worker/WD: Worker_Data/WD.: Organization_Data/WD.: Worker_Organization_Data/WD.: Organization_Data [ wd:Organization_Type_Reference/@wd:Descriptor =' Cost Center ']/WD.: ORGANIZATION_NAME/Text () |
     >| CostCenterCode | WD: Worker/WD: Worker_Data/WD.: Organization_Data/WD.: Worker_Organization_Data/WD.: Organization_Data [ wd:Organization_Type_Reference/@wd:Descriptor =' Cost Center ']/WD.: Organization_Code/Text () |
     >| PayGroup | WD: Worker/WD: Worker_Data/WD.: Organization_Data/WD.: Worker_Organization_Data/WD.: Organization_Data [ wd:Organization_Type_Reference/@wd:Descriptor =' pay Group ']/WD.: ORGANIZATION_NAME/Text () |

**Esempio 2**

Supponiamo di voler recuperare le certificazioni associate a un utente. Queste informazioni sono disponibili come parte del set di *dati di qualificazione* . Per ottenere questo set di dati come parte della risposta *Get_Workers* , utilizzare il seguente XPath: 

`wd:Worker/wd:Worker_Data/wd:Qualification_Data/wd:Certification/wd:Certification_Data/wd:Issuer/text()`

**Esempio 3**

Supponiamo di voler recuperare i *gruppi di provisioning* assegnati a un ruolo di lavoro. Queste informazioni sono disponibili come parte del set di *dati di provisioning dell'account* . Per ottenere questo set di dati come parte della risposta *Get_Workers* , utilizzare il seguente XPath: 

`wd:Worker/wd:Worker_Data/wd:Account_Provisioning_Data/wd:Provisioning_Group_Assignment_Data[wd:Status='Assigned']/wd:Provisioning_Group/text()`

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come configurare la giornata lavorativa per il provisioning di Active Directory](../saas-apps/workday-inbound-tutorial.md)
* [Informazioni su come configurare il writeback per la giornata lavorativa](../saas-apps/workday-writeback-tutorial.md)
* [Altre informazioni sugli attributi di Workday supportati per il provisioning in ingresso](workday-attribute-reference.md)

