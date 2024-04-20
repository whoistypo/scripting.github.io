---
layout: post
title: Desvelando todas las aplicaciones instaladas en Windows 10
---

`appwiz.cpl` es el elemento del Panel de Control que todos conocen como **Programas y características**. Es la herramienta que permite desinstalar aplicaciones desde que se implementó en Windows 98 (hoy es el 26º aniversario del aquel famoso BSOD, felicítenlos!). Técnicamente, este elemento interactúa con el registro de Windows, específicamente con las claves relacionadas con la información de instalación de programas. Los valores de estas claves dependen de muchos factores como el tipo de arquitectura, tipo de instalación o donde se haya instalado la aplicación que informa el registro.

Con algo de ingeniería inversa, podemos llegar a entender casi completamente cómo este elemento del Panel de Control recupera la información sobre los programas instalados, y así descubrir que puede faltar mucha información que podría ser relevante por ejemplo para administradores de sistemas o usuarios mas exigentes. Conociendo las fuentes de datos, podemos intentar interrogarlas nosotros mismos mediante Powershell.

### HKLM\Software\Microsoft\Windows\CurrentVersion\Uninstall

La primera fuente es la clave de registro `HKLM\Software\Microsoft\Windows\CurrentVersion\Uninstall`. Dentro de esta clave, cada subclave representa una aplicación instalada en el sistema. Cada una de estas subclaves contiene una serie de valores y subclaves que contienen información sobre la aplicación, como su `DisplayName`, `DisplayVersion`, `Publisher` y más. Veamos como sacar la información con `Get-ItemProperty` y con `Get-ChildItem`. 

{% highlight python %}
Get-ItemProperty "HKLM\Software\Microsoft\Windows\CurrentVersion\Uninstall\*"
Get-ChildItem -Path "HKLM\Software\Microsoft\Windows\CurrentVersion\Uninstall"
{% endhighlight %}


Lanyon is an unassuming [Jekyll](http://jekyllrb.com) theme that places content first by tucking away navigation in a hidden drawer. It's based on [Poole](http://getpoole.com), the Jekyll butler.


Poole is the Jekyll Butler, serving as an upstanding and effective foundation for Jekyll themes by [@mdo](https://twitter.com/mdo). Poole, and every theme built on it (like Lanyon here) includes the following:

* Complete Jekyll setup included (layouts, config, [404](/404), [RSS feed](/atom.xml), posts, and [example page](/about))
* Mobile friendly design and development
* Easily scalable text and component sizing with `rem` units in the CSS
* Support for a wide gamut of HTML elements
* Related posts (time-based, because Jekyll) below each post
* Syntax highlighting, courtesy Pygments (the Python-based code snippet highlighter)

### Lanyon features

In addition to the features of Poole, Lanyon adds the following:

* Toggleable sliding sidebar (built with only CSS) via **☰** link in top corner
* Sidebar includes support for textual modules and a dynamically generated navigation with active link support
* Two orientations for content and sidebar, default (left sidebar) and [reverse](https://github.com/poole/lanyon#reverse-layout) (right sidebar), available via `<body>` classes
* [Eight optional color schemes](https://github.com/poole/lanyon#themes), available via `<body>` classes

[Head to the readme](https://github.com/poole/lanyon#readme) to learn more.

### Browser support

Lanyon is by preference a forward-thinking project. In addition to the latest versions of Chrome, Safari (mobile and desktop), and Firefox, it is only compatible with Internet Explorer 9 and above.

### Download

Lanyon is developed on and hosted with GitHub. Head to the <a href="https://github.com/poole/lanyon">GitHub repository</a> for downloads, bug reports, and features requests.

Thanks!
