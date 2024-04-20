---
layout: post
title: Desvelando todas las aplicaciones instaladas en Windows 10
---

`appwiz.cpl` es el elemento del Panel de Control que todos conocen como **Programas y características**. Es la herramienta que permite desinstalar aplicaciones desde que se implementó en Windows 98 (hoy es el 26º aniversario del aquel famoso BSOD, felicítenlos!). Técnicamente, este elemento interactúa con el registro de Windows, específicamente con las claves relacionadas con la información de instalación de programas. Los valores de estas claves dependen de muchos factores como el tipo de arquitectura, tipo de instalación o donde se haya instalado la aplicación que informa el registro.

Con algo de ingeniería inversa, podemos llegar a entender casi completamente cómo este elemento del Panel de Control recupera la información sobre los programas instalados, y así descubrir que puede faltar mucha información que podría ser relevante por ejemplo para administradores de sistemas o usuarios mas exigentes. Conociendo las fuentes de datos, podemos intentar interrogarlas nosotros mismos mediante Powershell.

### HKLM\Software\Microsoft\Windows\CurrentVersion\Uninstall

La primera fuente es la clave de registro `HKLM\Software\Microsoft\Windows\CurrentVersion\Uninstall`. Dentro de esta clave, cada subclave representa una aplicación instalada en el sistema. Cada una de estas subclaves contiene una serie de valores y subclaves que contienen información sobre la aplicación, como su `DisplayName`, `DisplayVersion`, `Publisher` y más. Veamos como sacar la información con `Get-ItemProperty` y con `Get-ChildItem`. 

{% highlight *.ps1 %}
Get-ItemProperty "HKLM\Software\Microsoft\Windows\CurrentVersion\Uninstall\*"
{% endhighlight %}

{% highlight *.ps1 %}
Get-ChildItem -Path "HKLM\Software\Microsoft\Windows\CurrentVersion\Uninstall"
{% endhighlight %}

{% highlight *.js %}
const numbers = [102, -1, 2];
numbers.sort((a, b) => a - b);
console.log(numbers);
{% endhighlight %}
