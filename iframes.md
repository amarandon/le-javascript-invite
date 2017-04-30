## Injection HTML

### Avantage

* s'intègre dans la page
* performance

### Inconvénients

* risques d'interactions indésirables avec la page \(JavaScript et CSS\)

---

## Injection HTML

### Script de chargement

* 2 éléments
* 1 élément
* SDK

### Chargement de données

* Same Origin Policy
* JSONP
* CORS

---

## Chargement en deux éléments

Page hôte :

```
<div class="toulousejs-widget-container"></div>
<!-- ... bla bla ... -->
<script src="http://example.com/widget.js"></script>
```

Notre script widget.js :

```
(function() {
    var container = document.getElementById("toulousejs-widget-container");
    container.innerHTML = "Le contenu provenant de notre serveur";
})();
```

Avantages :

* permet de placer l'élément `<script>` en fin de page
* code d'injection simple

Inconvénients :

* pas forcément évident pour tous les auteurs de sites

---

Page hôte :

## Chargement en un seul élément

Page hôte :

```
!html
<p>Contenu de la page hôte</p>
<script async src="http://example.com/widget.js"
        data-toulouse-js-id="42">
</script>
<p>Suite du contenu de la page hôte</p>
```

Notre script :

```
!js
(function() {
  var script = document.querySelector("script[data-toulouse-js-id]");
  var content = 
    "Contenu issu de notre script. " +
    "Paramètre : " + script.getAttribute("data-toulouse-js-id");
  script.insertAdjacentHTML("beforebegin", content);
})();
```

![](/assets/single-element.png)

---

## SDK

```
!html
<script>
    window.ToulouseJSWidget_ready = function() {
      ToulouseJSWidget.init({
        param1: "foo", param2: "bar"
      });
      ToulouseJSWidget.doSomething();
    };
    (function() {
      var script = document.createElement('script');
      script.async = true;
      script.src = 'http://example.com/widget.js';
      var entry = document.getElementsByTagName('script')[0];
      entry.parentNode.insertBefore(script, entry);
    })();
</script>
```

Note : la création dynamique d'élément `<script>` permet le chargement asynchrone avec les vieux navigateurs

---

## Chargement de scripts

```
!js
function loadScript(url, callback) {
  if(typeof callback === "undefined") { callback = function() {}; }
  var scriptTag = document.createElement('script');
  scriptTag.setAttribute("type", "text/javascript");
  scriptTag.setAttribute("src", url);
  if (scriptTag.readyState) {
    // Pour les vieux IE
    scriptTag.onreadystatechange = function () {
      if (this.readyState === 'complete' || this.readyState === 'loaded') {
        callback();
      }
    };
  } else {
    scriptTag.onload = callback;
  }
  document.documentElement.appendChild(scriptTag);
}
```

---

## Example : chargement de jQuery

```
!js
loadScript("http://example.com/jquery.js", function() {
    var $;
    // On rétabli window.$ et window.jQuery tels qu'ils étaient
    // auparavant et on garde des références à notre propre version
    window.$ToulouseJSWidget_jQuery = $ = window.jQuery.noConflict(true);
    continueWithTheRestOfTheCode($);
});
```

---

## Précautions de codage JavaScript

* réduire au maximum les variables globales
* nommer les variables globales de manière à réduire les conflits de nommage, eg. `window.nomDeMonOrganisationNomDuProjetFoo`.
* utiliser un linter \(ex: JSHint\) et `"use strict";` pour ne pas introduire de variables globales par erreur \(vérification de la présence du mot clé `var`\)



