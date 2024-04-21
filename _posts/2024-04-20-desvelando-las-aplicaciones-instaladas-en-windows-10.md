---
layout: post
title: Desvelando las aplicaciones instaladas en Windows 10
---

`appwiz.cpl` es el elemento del Panel de Control que todos conocen como **Programas y características**. Es la herramienta que permite desinstalar aplicaciones desde que se implementó en Windows 98 (hoy es el 26º aniversario del aquel famoso BSOD, felicítenlos!). Técnicamente, este elemento interactúa con el registro de Windows, específicamente con las claves relacionadas con la información de instalación de programas. Los valores de estas claves dependen de muchos factores como el tipo de arquitectura, tipo de instalación o donde se haya instalado la aplicación que informa el registro.

Con algo de ingeniería inversa, podemos llegar a entender casi completamente cómo este elemento del Panel de Control recupera la información sobre los programas instalados, y así descubrir que puede faltar mucha información que podría ser relevante por ejemplo para administradores de sistemas o usuarios mas exigentes. Conociendo las fuentes de datos, podemos intentar interrogarlas nosotros mismos mediante Powershell.

## Consultando el registro

La primera fuente es la clave de registro `HKLM\Software\Microsoft\Windows\CurrentVersion\Uninstall`. Dentro de esta clave, cada subclave representa una aplicación instalada en el sistema. Cada una de estas subclaves contiene información sobre la aplicación, como su `DisplayName`, `DisplayVersion`, `Publisher` y más. Veamos como sacar la información con `Get-ItemProperty`. 

{% highlight powershell %}
Get-ItemProperty "HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\*" | Select-Object DisplayName, DisplayVersion, Publisher
{% endhighlight %}

Hemos utilizado `Select-Object` para que el output apareciera inteligible, pero se puede incluso omitir para revisar los valores de cada subclave. Es posible también utilizar `Format-Table -AutoSize` si no se requieren valores especificos.

Otra fuente es la clave de registro `HKLM:\Software\Wow6432node\Microsoft\Windows\CurrentVersion\Uninstall` donde podemos encontrar las aplicaciones de 32bits. En esta ocasión hemos creado un array y unificado los outputs de las dos claves de registro.

{% highlight powershell %}
# creamos un array para unificar los outputs
$output = @()

# informamos el array con los valores del registro
$output += Get-ItemProperty "HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\*"
$output += Get-ItemProperty "HKLM:\Software\Wow6432node\Microsoft\Windows\CurrentVersion\Uninstall\*"

# filtramos, ordenamos y mostramos los resultados sin duplicados
$output | Select-Object DisplayName, DisplayVersion, Publisher | Sort-Object DisplayName -Unique
{% endhighlight %}

Hay otras fuentes como la `HKLM\Software\Classes\Installer\Products`, donde se almacenan los valores de las aplicaciones instaladas mediante MSI, o `HKCU\Software\Microsoft\Windows\CurrentVersion\Uninstall` y `HKCU\Software\Microsoft\Installer\Products`, donde podemos encontrar información sobre las aplicaciones instaladas solamente para el current user.

## Namespaces WMI

## Notas
