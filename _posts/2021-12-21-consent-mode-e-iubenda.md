---
title: "Consent Mode e Iubenda. Come configurare il consenso dei cookie"
last_modified_at: 2021-12-21T20:00:23+02:00
author: 'Paolo Bietolini'
tags: 
    - consent-mode
    - iubenda
categories:
    - tag-manager

toc: true
toc_label: "Tabella dei contenuti"
---
### Introduzione

Google ha introdotto la Consent Mode per aiutare gli inserzionisti a gestire i cookie a fini pubblicitari e statistici.  
Questa funzionalità consente di regolare il comportamento dei tag di Google (e non solo) in base alle preferenze di consenso degli utenti.

Con la Consent Mode i relativi cookie, pubblicitari o statistici, verranno salvati nel computer dell'utente solamente se il consenso sarà stato fornito.

Se ad esempio un' utente non fornisce il consenso per i cookie pubblicitari, ma solo per quelli statistici, potrai comunque misurare le conversioni tramite Analytics, in quanto l’impostazione analytics\_storage sarà attiva.

Tra le CMP supportarte da Google, per l'integrazione della modalità di consenso, troviamo Iubenda.  
In questo articolo vedremo come gestire la Consent Mode in Iubenda tramite Tag Manager o tramite il Tag Globale gTag.js.

Prima di iniziare voglio fare una piccola panoramica indicando tutte le soluzioni possibili:

*   Installazione interamente via codice e integrazione con gTag.js
    
*   Installazione via codice e integrazione con Google Tag Manager
    
*   Implementazione mista e gestione semi-automatizzata in Tag Manager
    
*   Implementazione mista e gestione interamente via Tag Manager
    

Per implementazione mista intendo che dovrai comunque accedere al codice del sito per inserire il banner.  
Come accennato [in questo post](https://analyticsitalia.it/tag-manager/consent-mode-come-configurare-il-consenso-dei-cookie/#1Installa_lo_script_del_consenso),  non consiglio l'utilizzo di un tag HTML personalizzato per caricare lo script di Iubenda.

Preparazione dello script di Iubenda

### **Opzioni preliminari nella Dashboard**

Dopo aver eseguito l'[iscrizione a Iubenda](https://www.iubenda.com/it/) accedi alla tua [Dashboard](https://www.iubenda.com/it/dashboard) e apri il sito per il quale vuoi generare il banner (se non avessi alcun sito nella dashboard, clicca _Inizia la generazione_):

[![Dashboard di Iubenda](https://analyticsitalia.it/wp-content/uploads/2021/12/dashboard-di-Iubenda-1-1024x761.png)](https://analyticsitalia.it/wp-content/uploads/2021/12/dashboard-di-Iubenda-1.png)


Sul lato destro troverai la voce Cookie Solution, clicca il pulsante Modifica e avrai di fronte questa schermata:

[![Schermata di modifica del banner di Iubenda](https://analyticsitalia.it/wp-content/uploads/2021/12/Screenshot-2021-12-23-14.19.51-1024x551.png)](https://analyticsitalia.it/wp-content/uploads/2021/12/Screenshot-2021-12-23-14.19.51.png)



Sul lato sinistro troverai delle opzioni con sfondo azzurro e l'etichetta GDPR, spuntale tutte in questo modo.

Ora, in alto a sinistra clicca _Opzioni Avanzate_ e cerca la voce _IMPOSTAZIONI RELATIVE AL CONSENSO PER CATEGORIA_.  
Nella barra per la personalizzazione aggiungi gli ID relativi alla categoria di cookies che vuoi utilizzare.

[![Impostazioni relative al consenso per categoria
](https://analyticsitalia.it/wp-content/uploads/2021/12/53PBSDe3fEbwNhc-1024x551.png)](https://analyticsitalia.it/wp-content/uploads/2021/12/53PBSDe3fEbwNhc.png)


Queste sono le categorie dei cookies presenti sul sito e saranno necessarie per comunicare a Tag Manger, quindi alla Consent Mode, quale cookie salvare in base al consenso.

In questo sito ho aggiunto 3,4,5 in quanto sto utilizzando cookies pubblicitari, di analisi e di miglioramento dell'esperienza utente.

### Consenso per categoria
----------------------

Le finalità sono raggruppate in 5 categorie (strettamente necessari, interazioni e funzionalità semplici, miglioramento dell’esperienza, misurazione, targeting e pubblicità), ciascuna identificata da un id (1, 2, 3, 4,5).

Per impostazione predefinita, le categorie da mostrare vengono rilevate automaticamente dalla cookie policy di Iubenda, ma puoi personalizzarle con il parametro `purposes` (ad esempio se usi la tua cookie policy anziché quella generata da Iubenda).

A seguire le finalità per ogni categoria:

#### Strettamente necessari (id 1). Finalità incluse:

*   Salvataggio e gestione di backup
*   Hosting ed infrastruttura backend
*   Gestione di landing page e pagine di invito
*   Servizi di piattaforma e hosting
*   Protezione dallo SPAM
*   Ottimizzazione e distribuzione del traffico
*   Monitoraggio dell’infrastruttura
*   Gestione dei pagamenti

#### Interazioni e funzionalità semplici (id 2). Finalità incluse:

*   Contattare l’Utente
*   Interazione con le piattaforme di live chat
*   Gestione di conferenze web e telefonia online
*   Gestione delle richieste di supporto e contatto
*   Interazione con le piattaforme di supporto e di feedback
*   Gestione dei tag
*   Registrazione ed autenticazione
*   Gestione dei database di Utenti

#### Miglioramento dell’esperienza (id 3). Finalità incluse:

*   Commento dei contenuti
*   Interazione con piattaforme di raccolta dati e altre terze parti
*   Visualizzazione di contenuti da piattaforme esterne
*   Interazione con social network e piattaforme esterne
*   Interazione con le piattaforme per sondaggi online
*   Gestione dei feed RSS
*   Funzionalità sociali

#### Misurazione (id 4). Finalità incluse:

*   Statistica
*   Beta testing
*   Test di performance di contenuti e funzionalità (A/B testing)
*   Heat mapping e registrazione sessioni
*   Gestione della raccolta dati e dei sondaggi online

#### Targeting e pubblicità (id 5). Finalità incluse:

*   Pubblicità
*   Infrastruttura al servizio pubblicitario
*   Affiliazione commerciale
*   Gestione contatti e invio di messaggi
*   Remarketing e behavioral targeting

Quindi, se per esempio devi mostrare tutte e 5 le categorie dovrai indicare `"purposes": "1, 2, 3, 4, 5"`, ma se non devi mostrare Misurazione (id 4) indicherai `"purposes": "1, 2, 3, 5"` e così via.

### Lo script
---------

Una volta scelte le impostazioni di Iubenda clicca _Salva_ in basso a destra. Nella schermata principale seleziona la voce Integra, qui troverai lo script.  
Questo script andrà incollato nella sezione `<head>` del tuo sito.

Nei prossimi paragrafi vedremo come integrarlo con Tag Manager o con lo script.  
Qui sotto troverai uno script d'esempio, il tuo dovrà essere più o meno simile. Accertati che ci siano le righe:

`"perPurposeConsent":true, "purposes":"1,3,4,5", "enableTcf":true, "googleAdditionalConsentMode":true`
```html
<script>
      var _iub = _iub || [];
      _iub.csConfiguration = {
        "siteId": 00000, //Nota come siteId e cookiePolicyId siano impostati a 0, il valore lo troverai nella dashboard di Iubenda
        "cookiePolicyId": 000000,
        "gdprAppliesGlobally": false,
        "countryDetection": true,
        "reloadOnConsent": true,
        "consentOnContinuedBrowsing": false,
        "perPurposeConsent": true,
        "purposes": "1,3,4,5",
        "enableTcf": true,
        "googleAdditionalConsentMode": true,
        "askConsentIfCMPNotFound": false,
        "lang": "it",
        "floatingPreferencesButtonDisplay": false,
        "banner": {
          "acceptButtonDisplay": true,
          "customizeButtonDisplay": true,
          "acceptButtonColor": "#0073CE",
          "acceptButtonCaptionColor": "white",
          "customizeButtonColor": "#212121",
          "customizeButtonCaptionColor": "white",
          "rejectButtonDisplay": true,
          "rejectButtonColor": "rgba(0, 115.1, 206.18, 0.71)",
          "rejectButtonCaptionColor": "white",
          "listPurposes": true,
          "position": "float-bottom-center",
          "textColor": "#353535",
          "backgroundColor": "#ffffff"
        }
      };
    </script>
    <script src="//cdn.iubenda.com/cs/tcf/stub-v2.js"></script>
    <script src="//cdn.iubenda.com/cs/iubenda_cs.js" charset="UTF-8" async>
    </script>
```

#### Installazione interamente via codice e integrazione con gTag.js
---------------------------------------------------------------

Copia nella sezione `<head>` del tuo sito questo script opportunamente modificato

```html
    <!-- Consent Mode -->
    <script>
      // Inizializzazione del dataLayer, obbligatoria
      window.dataLayer = window.dataLayer || [];
      function gtag() {
          dataLayer.push(arguments);
      }
    
      // La modalità di consenso predefinita è "denied" sia per ad_storage che per analytics_storage
      gtag("consent", "default", {
          ad_storage: "denied",
          analytics_storage: "denied",
          wait_for_update: 2000 // milliseconds
      });
    
      // Migliora la qualità del tracciamento dei clic sugli annunci (opzionale)
      gtag('set', 'url_passthrough', true);
      // Rimuove i dati degli annunci se lo ad_storage rimane su denied (opzionale)
      gtag("set", "ads_data_redaction", false);
    </script>
    
    <!-- Global site tag (gtag.js) - Google Analytics e/o Google Ads -->
    <script async src="https://www.googletagmanager.com/gtag/js?id=ID_ANALYTICS"></script>
    <script async src="https://www.googletagmanager.com/gtag/js?id=ID_ADS"></script>
    
    <script>   
      gtag('js', new Date());
      gtag('config', 'GOOGLE_ANALYTICS_ID');
      gtag('config', 'GOOGLE_ADS_ID');
    </script>
    
    <!-- Banner di Iubenda -->
    <script>
      var _iub = _iub || [];
      _iub.csConfiguration = {
        "siteId":00000,                     // ID di Iubenda
        "cookiePolicyId":000000,            // ID policy di Iubenda
        "gdprAppliesGlobally":false,
        "countryDetection":true,
        "reloadOnConsent":true,
        "consentOnContinuedBrowsing":false,
        "perPurposeConsent":true,
        "purposes":"1,3,4,5",
        "enableTcf":true,
        "googleAdditionalConsentMode":true,
        "askConsentIfCMPNotFound":false,
        "lang":"it",
        "floatingPreferencesButtonDisplay":false,
        "banner":{ 
          "acceptButtonDisplay":true,
          "customizeButtonDisplay":true,
          "acceptButtonColor":"#0073CE",
          "acceptButtonCaptionColor":"white",
          "customizeButtonColor":"#212121",
          "customizeButtonCaptionColor":"white",
          "rejectButtonDisplay":true,"rejectButtonColor":
          "rgba(0, 115.1, 206.18, 0.71)",
          "rejectButtonCaptionColor":"white",
          "listPurposes":true,
          "position":"float-bottom-center",
          "textColor":"#353535",
          "backgroundColor":"#ffffff" 
          }
        };
      </script>
      <script src="//cdn.iubenda.com/cs/tcf/stub-v2.js"></script>
      <script src="//cdn.iubenda.com/cs/iubenda_cs.js" charset="UTF-8" async>
  </script>
  ```

Incollato questo codice la modalità di consenso sarà pronta e dovrai solamente [eseguire il debugging](#debugging-e-test).

Nota che il comando `consent, update` verrà inviato automaticamente dal banner di Iubenda

#### Installazione via codice e integrazione con Tag Manager
-------------------------------------------------------

Copia nella sezione `<head>` del tuo sito questo script opportunamente modificato

   ```html
    <!-- Consent Mode -->
    <script>
      // Inizializzazione del dataLayer, obbligatoria
      window.dataLayer = window.dataLayer || [];
      function gtag() {
          dataLayer.push(arguments);
      }
      // La modalità di consenso predefinita è "denied" sia per gli ad_storage che per analytics_storage
      gtag("consent", "default", {
          ad_storage: "denied",
          analytics_storage: "denied",
          wait_for_update: 2000 // milliseconds
      });
      // Migliora la qualità del tracciamento dei clic sugli annunci (opzionale)
      gtag('set', 'url_passthrough', true);
      // Rimuove i dati degli annunci se lo ad_storage rimane su denied (opzionale)
      gtag("set", "ads_data_redaction", false);
    </script>
    
    <!-- Google Tag Manager -->
    <script>   
    (function (w, d, s, l, i) {
        w[l] = w[l] || [];
        w[l].push({'gtm.start': new Date().getTime(),event: 'gtm.js'});
        var f = d.getElementsByTagName(s)[0],j = d.createElement(s),dl = l != 'dataLayer' ? '&l=' +l : '';
        j.async = true;
        j.src ='https://www.googletagmanager.com/gtm.js?id='+i+dl;
        f.parentNode.insertBefore(j, f);
      })
      (window, document, 'script','dataLayer', 'GTM-000000'); // Il tuo ID di Tag Manager
    </script>
    <!-- Banner di Iubenda -->
    <script>
      var _iub = _iub || [];
      _iub.csConfiguration = {
        "siteId":00000,                     // ID di Iubenda
        "cookiePolicyId":000000,            // ID policy di Iubenda
        "gdprAppliesGlobally":false,
        "countryDetection":true,
        "reloadOnConsent":true,
        "consentOnContinuedBrowsing":false,
        "perPurposeConsent":true,
        "purposes":"1,3,4,5",
        "enableTcf":true,
        "googleAdditionalConsentMode":true,
        "askConsentIfCMPNotFound":false,
        "lang":"it",
        "floatingPreferencesButtonDisplay":false,
        "banner":{ 
          "acceptButtonDisplay":true,
          "customizeButtonDisplay":true,
          "acceptButtonColor":"#0073CE",
          "acceptButtonCaptionColor":"white",
          "customizeButtonColor":"#212121",
          "customizeButtonCaptionColor":"white",
          "rejectButtonDisplay":true,"rejectButtonColor":
          "rgba(0, 115.1, 206.18, 0.71)",
          "rejectButtonCaptionColor":"white",
          "listPurposes":true,
          "position":"float-bottom-center",
          "textColor":"#353535",
          "backgroundColor":"#ffffff" 
          }
        };
      </script>
      <script src="//cdn.iubenda.com/cs/tcf/stub-v2.js"></script>
      <script src="//cdn.iubenda.com/cs/iubenda_cs.js" charset="UTF-8" async></script>
```
Incollato questo codice la modalità di consenso sarà pronta e dovrai solamente [eseguire il debugging](#debugging-e-test).

Nota che il comando `consent, update` verrà inviato automaticamente dal banner di Iubenda

#### Implementazione mista e gestione semi-automatizzata in Tag Manager
------------------------------------------------------------------

In questa implementazione useremo il template per la Consent Mode di Simo Ahava per gestire la modalità `default`

*   Incolla questo script, opportunamente modificato, nella sezione `<head>` del tuo sito.
*   Clicca qui per [**scaricare** il contenitore](https://gist.githubusercontent.com/paolobtl/f1b699881b4fe95e0d5026292f6ac611/raw/9e29b2aab4b189b0e196ac7eb3623e48768bb1e7/Iubenda_ConsentMode_just_ConsentDefault.json)

 ```html
    <!-- Google Tag Manager -->
    <script>   
    (function (w, d, s, l, i) {
        w[l] = w[l] || [];
        w[l].push({'gtm.start': new Date().getTime(),event: 'gtm.js'});
        var f = d.getElementsByTagName(s)[0],j = d.createElement(s),dl = l != 'dataLayer' ? '&l=' +l : '';
        j.async = true;
        j.src ='https://www.googletagmanager.com/gtm.js?id='+i+dl;
        f.parentNode.insertBefore(j, f);
      })
      (window, document, 'script','dataLayer', 'GTM-000000'); // Il tuo ID di Tag Manager
    </script>
    <!-- Banner di Iubenda -->
    <script>
      var _iub = _iub || [];
      _iub.csConfiguration = {
        "siteId":00000,                     // ID di Iubenda
        "cookiePolicyId":000000,            // ID policy di Iubenda
        "gdprAppliesGlobally":false,
        "countryDetection":true,
        "reloadOnConsent":true,
        "consentOnContinuedBrowsing":false,
        "perPurposeConsent":true,
        "purposes":"1,3,4,5",
        "enableTcf":true,
        "googleAdditionalConsentMode":true,
        "askConsentIfCMPNotFound":false,
        "lang":"it",
        "floatingPreferencesButtonDisplay":false,
        "banner":{ 
          "acceptButtonDisplay":true,
          "customizeButtonDisplay":true,
          "acceptButtonColor":"#0073CE",
          "acceptButtonCaptionColor":"white",
          "customizeButtonColor":"#212121",
          "customizeButtonCaptionColor":"white",
          "rejectButtonDisplay":true,"rejectButtonColor":
          "rgba(0, 115.1, 206.18, 0.71)",
          "rejectButtonCaptionColor":"white",
          "listPurposes":true,
          "position":"float-bottom-center",
          "textColor":"#353535",
          "backgroundColor":"#ffffff" 
          }
        };
      </script>
      <script src="//cdn.iubenda.com/cs/tcf/stub-v2.js"></script>
      <script src="//cdn.iubenda.com/cs/iubenda_cs.js" charset="UTF-8" async></script>
```
*   In Tag Manager crea, dal menù a _Variabili_, una variabile Cookie propietario e inserisci questo valore nel campo _Nome cookie_: `_iub_cs-000000`.  
    00000 sarà il valore dell'attributo `cookiePolicyId` nel banner.

[![Iubenda Policy Cookie](https://analyticsitalia.it/wp-content/uploads/2021/12/Screenshot-2021-12-23-13.40.40-1024x414.png)](https://analyticsitalia.it/wp-content/uploads/2021/12/Screenshot-2021-12-23-13.40.40.png)


*   Spunta la casella _Decodifica in formato URI il cookie_ e rinomina la variabile _Iubenda Policy Cookie_,
*   Crea una variabile _Tabella delle espressioni regolari_ che prenda in input il cookie creato precedentemente, `Iubenda Policy Cookie`
    *   Nei campi pattern inserisci "#":false e "#":true, dove il simbolo # rappresenta la categoria dei cookies. In questo caso ho inserito _"5":false_ e _"5":true_ affinché vengano letti i valori per la categoria della pubblicità.
    *   Nei campi output inserisci rispettivamente `denied` e `granted`.
    *   Spunta la casella _Imposta valore predefinito_ e scrivi `denied`.
    *   In _Advanced Settings_ spunta solo la casella _Ignora maiuscole e minuscole_.
    *   Salva con il nome _Consent Mode ad\_storage_.
    *   Ripeti lo stesso procedimento per `analytics_storage` e `personalization_storage` utilizzando rispettivamente i numeri "4" e "3" nel campo pattern.

[![Variabile Tabella di ricerca](https://analyticsitalia.it/wp-content/uploads/2021/12/Screenshot-2021-12-23-13.43.14-1024x414.png)](https://analyticsitalia.it/wp-content/uploads/2021/12/Screenshot-2021-12-23-13.43.14.png)


*   Crea un nuovo tag e seleziona la barra azzurra in alto a destra, _Scopri più tipi di tag nella Galleria modelli della community_,
*   Aggiungi il tag **Consent Mode (Google tags)** di _gtm-templates-simo-ahava_,
    *   Nella voce _Consent command_ seleziona `default`
    *   Clicca il pulsante _Add Setting_ e seleziona per tutte le opzioni la voce `denied`

[![](https://analyticsitalia.it/wp-content/uploads/2021/12/Screenshot-2021-12-23-13.48.59-1024x511.png)](https://analyticsitalia.it/wp-content/uploads/2021/12/Screenshot-2021-12-23-13.48.59.png)


*   Seleziona l'attivatore, in basso cliccando sull'icona dei due insiemi
    *   Nella pagina di scelta dell'attivatore clicca il simbolo `'+'` in alto a destra
    *   Scegli _Inizializzazione del consenso_
    *   Clicca sull'opzione _Alcune pagine_ e dal menù a tendina scegli la variabile `Iubenda Policy Cookie`
    *   Imposta la regole su "_è uguale a_" e scrivi _undefined_ nel campo successivo, in questo modo lo script di default si attiverà solamente se l'utente non ha espresso alcuna preferenza

[![Attivatore del tag](https://analyticsitalia.it/wp-content/uploads/2021/12/Screenshot-2021-12-23-13.47.26-1024x414.png)](https://analyticsitalia.it/wp-content/uploads/2021/12/Screenshot-2021-12-23-13.47.26.png)


#### Implementazione mista e gestione interamente via Tag Manager
------------------------------------------------------------

In questa implementazione useremo il template per la Consent Mode di [Simo Ahava](https://www.simoahava.com/custom-templates/consent-mode/) per gestire la modalità `default`

*   Incolla questo script, opportunamente modificato, nella sezione del tuo sito.
*   Clicca qui per [**scaricare** il contenitore](https://gist.githubusercontent.com/paolobtl/caf749a256d12d56de899076fb7f05ad/raw/3685e48dd9708aa0b603350927cf83da182be5b1/Iubenda_ConsentMode.json)

```html
    <!-- Google Tag Manager --> 
    <script>
     (function (w, d, s, l, i) { w[l] = w[l] || []; w[l].push({'gtm.start': new Date().getTime(),event: 'gtm.js'}); 
     var f = d.getElementsByTagName(s)[0],j = d.createElement(s),dl = l != 'dataLayer' ? '&l=' +l : ''; j.async = true;
     j.src ='https://www.googletagmanager.com/gtm.js?id='+i+dl; f.parentNode.insertBefore(j, f); })
     (window, document, 'script','dataLayer', 'GTM-000000'); // Il tuo ID di Tag Manager 
    </script> 
    
    <!-- Banner di Iubenda --> 
    <script> 
     var _iub = _iub || []; _iub.csConfiguration = { 
     "siteId":00000, // ID di Iubenda 
     "cookiePolicyId":000000, // ID policy di Iubenda, copialo, ti servirà in Tag Manager 
     "gdprAppliesGlobally":false, 
     "countryDetection":true, 
     "reloadOnConsent":true, 
     "consentOnContinuedBrowsing":false, 
     "perPurposeConsent":true, 
     "purposes":"1,3,4,5", 
     "enableTcf":true, 
     "googleAdditionalConsentMode":true, 
     "askConsentIfCMPNotFound":false, 
     "lang":"it", 
     "floatingPreferencesButtonDisplay":false, 
     "banner":{ "acceptButtonDisplay":true, 
     "customizeButtonDisplay":true, 
     "acceptButtonColor":"#0073CE", 
     "acceptButtonCaptionColor":"white", 
     "customizeButtonColor":"#212121", 
     "customizeButtonCaptionColor":"white", 
     "rejectButtonDisplay":true,
     "rejectButtonColor": 
     "rgba(0, 115.1, 206.18, 0.71)", 
     "rejectButtonCaptionColor":"white", 
     "listPurposes":true, 
     "position":"float-bottom-center", 
     "textColor":"#353535", 
     "backgroundColor":"#ffffff" } }; 
    </script> 
    <script src="//cdn.iubenda.com/cs/tcf/stub-v2.js"></script> 
    <script src="//cdn.iubenda.com/cs/iubenda_cs.js" charset="UTF-8" async>
</script>
```
*   In Tag Manager crea, dal menù _Variabili_, una variabile Cookie proprietario e inserisci questo valore nel campo _Nome cookie_: `_iub_cs-000000` . 00000 sarà il valore dell'attributo `cookiePolicyId` nel banner.

[![Iubenda Policy Cookie](https://analyticsitalia.it/wp-content/uploads/2021/12/Screenshot-2021-12-23-13.40.40-1024x414.png)](https://analyticsitalia.it/wp-content/uploads/2021/12/Screenshot-2021-12-23-13.40.40.png)


*   Spunta la casella _Decodifica in formato URI il cookie_ e rinomina la variabile `Iubenda Policy Cookie`,
*   Crea una variabile _Tabella delle espressioni regolari_ che prenda in input il cookie creato precedentemente, `Iubenda Policy Cookie`
    *   Nei campi pattern inserisci "#":false e "#":true, dove il simbolo # rappresenta la categoria dei cookies. In questo caso ho inserito _"5":false_ e _"5":true_ affinché vengano letti i valori per la categoria della pubblicità.
    *   Nei campi output inserisci rispettivamente `denied` e `granted`.
    *   Spunta la casella _Imposta valore predefinito_ e scrivi `denied`.
    *   In _Advanced Settings_ spunta solo la casella _Ignora maiuscole e minuscole_.
    *   Salva con il nome _Consent Mode ad\_storage_.
    *   Ripeti lo stesso procedimento per `analytics_storage` e `personalization_storage` utilizzando rispettivamente i numeri "4" e "3" nel campo pattern.

[![Variabile Tabella di ricerca](https://analyticsitalia.it/wp-content/uploads/2021/12/Screenshot-2021-12-23-13.43.14-1024x414.png)](https://analyticsitalia.it/wp-content/uploads/2021/12/Screenshot-2021-12-23-13.43.14.png)

*   Crea un nuovo tag e seleziona la barra azzurra in alto a destra, _Scopri più tipi di tag nella Galleria modelli della community_,
*   Aggiungi il tag **Consent Mode (Google tags)** di _gtm-templates-simo-ahava_,
    *   Nella voce _Consent command_ seleziona `default`
    *   Clicca il pulsante _Add Setting_ e seleziona la variabile `Consent Mode ad_storage` per il menù Advertising, `Consent Mode analytics_storage` per Analytics e `Consent Mode personalization_storage` per Personalization
    *   Clicca su _Aggiungi_ in alto a destra

[![Tag Consent Default](https://analyticsitalia.it/wp-content/uploads/2021/12/Screenshot-2021-12-23-14.10.03-1024x507.png)](https://analyticsitalia.it/wp-content/uploads/2021/12/Screenshot-2021-12-23-14.10.03.png)

*   Seleziona l'attivatore, in basso e clicca sul logo dei due insiemi
    *   Nella pagina di scelta dell'attivatore clicca il simbolo `'+'` in alto a destra
    *   Scegli _Inizializzazione del consenso_
        *   Clicca sull'opzione _Alcune pagine_ e dal menù a tendina scegli la variabile `Iubenda Policy Cookie`
        *   Imposta la regole su "_è uguale a_" e scrivi nel campo successivo, _undefined_, in questo modo lo script di default si attiverà solamente se l'utente non ha espresso alcuna preferenza

[![Attivatore del tag Consent Default](https://analyticsitalia.it/wp-content/uploads/2021/12/Screenshot-2021-12-23-13.47.26-1024x414.png)](https://analyticsitalia.it/wp-content/uploads/2021/12/Screenshot-2021-12-23-13.47.26.png)

*   Esegui la stessa procedura dal punto per il _Tag Consent Default_ e nella voce _Consent command_ seleziona `update`
    *   Seleziona le variabili `Consent Mode ad\_storage`, `Consent Mode analytics_storage` e `Consent Mode personalization_storage` per i rispettivi campi

[![](https://analyticsitalia.it/wp-content/uploads/2021/12/Consent_update_tag-1024x507.png)](https://analyticsitalia.it/wp-content/uploads/2021/12/Consent_update_tag.png)


*   Seleziona l'attivatore, in basso e clicca sul logo dei due insiemi
    *   Nella pagina di scelta dell'attivatore clicca il simbolo `'+'` in alto a destra
    *   Scegli _Inizializzazione del consenso_
        *   Clicca sull'opzione _Alcune pagine_ e dal menù a tendina scegli la variabile `Iubenda Policy Cookie`
        *   Imposta la regole su "_non contiene_" e scrivi nel campo successivo, _undefined_, in questo modo lo script di default si attiverà **solamente** se l'utente ha espresso una preferenza

[![Attivatore Consent Update](https://analyticsitalia.it/wp-content/uploads/2021/12/Screenshot-2021-12-23-14.13.11-1024x507.png)](https://analyticsitalia.it/wp-content/uploads/2021/12/Screenshot-2021-12-23-14.13.11.png)

> Per scaricare il contenitore già pronto, [clicca qui](https://gist.githubusercontent.com/paolobtl/caf749a256d12d56de899076fb7f05ad/raw/3685e48dd9708aa0b603350927cf83da182be5b1/Iubenda_ConsentMode.json).

### Considerazioni e consigli
-------------------------

### Perché due implementazioni simili per Tag Manager?

Le ultime due implementazioni risultano piuttosto simili sebbene la seconda sia decisamente più complessa.  
Ho deciso di includerle entrambe in quanto l'ultima, quella gestita interamente da Tag Manager, permette maggiore flessibilità per i comandi `default` e `update`.

Il consiglio quindi è quello di utilizzare l'implementazione semi-automatizzata nella quale l'aspetto di aggiornamento dello stato del consenso è demandato interamente a Iubenda.

### Opzione url\_passthrough in Tag Manager

Come visto in [questo paragrafo](https://analyticsitalia.it/tag-manager/consent-mode-come-configurare-il-consenso-dei-cookie/#URL_Passthrough) il parametro `url_passthrough` aiuta a trasmettere le informazioni del clic sull'annuncio in caso di rifiuto dei cookies pubblicitari.  
In Tag Manager questa opzione è disponibile sia nel template di Simo Ahava, sotto la voce _Pass Ad Click Information Through URLs_, oppure nel tag [Conversion Linker](https://support.google.com/tagmanager/answer/7549390) nel check-box _Attiva il collegamento in tutti gli URL delle pagine_.  
Se già avessi un tag Conversion Linker spunta l'apposita opzione, altrimenti utilizza quella presente nel template di Simo Ahava.  
Maggiori informazioni sono disponibili, in inglese, [in questo articolo](https://developers.google.com/tag-platform/devguides/consent#ad_click_information).

### Debugging e test
----------------

Per capire se ho implementato correttamente la Consent Mode eseguo sempre un paio di test.  
Cerco il parametro `gcs` di Analytics nelle richieste di rete e/o in Tag Assistant  
  
In primis l’esistenza di questo parametro suggerisce che la Consent Mode è stata impostata correttamente e i suoi valori ci dicono qual è lo stato del consenso.

[![](https://analyticsitalia.it/wp-content/uploads/2021/12/parametro_gcs_chrome_console.png)](https://analyticsitalia.it/wp-content/uploads/2021/12/parametro_gcs_chrome_console.png)



Nella console di Chrome troverai il parametro GCS sotto la voce Network

*   G100: La modalità di consenso è stata impostata e Ads e Analytics storage sono impostati su `denied`
*   G110: Sono stati accettati i cookies di marketing e non di analisi
*   G111: Sono stati accettati i cookies di marketing e di analisi
*   G101: Sono stati accettati i cookies di analisi e non di marketing

L’altro test che eseguo è guardare dentro il `dataLayer` per vedere se sono presenti i parametri del consenso.  
Per controllare basta aprire la console JavaScript del tuo browser e digitare `dataLayer`, dovresti vedere una schermata simile a questa:

![I comandi della modalità di Consenso nel DataLayer](https://analyticsitalia.it/wp-content/uploads/2021/12/Schermata-2021-12-19-alle-22.17.16.png)


Se non dovessi trovare il parametro `gcs` e non vedere dati nel `dataLayer` allora dovrai ricontrollare l’implementazione del codice di default e assicurarti che sia stato inserito prima di tutti gli altri script (banner e Tag Manager).

