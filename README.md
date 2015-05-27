# Introduction #

Spynner is a stateful programmatic web browser module for Python. It is based upon [Qt](http://www.qtsoftware.com/) and [WebKit](http://webkit.org/), so it supports Javascript, AJAX, and every other technology that WebKit is able to handle (Flash, SVG, ...). Spynner takes advantage of [jQuery](http://jquery.com), a powerful Javascript library that makes the interaction with pages and event simulation straightforward.

Using Spynner you would able to simulate a web browser with no GUI (though a browsing window can be opened for debugging purposes), so it may be used to implement crawlers or acceptance testing tools.

# Dependencies #

  * [Python](http://www.python.org) (>=2.5)
  * [PyQt](http://www.riverbankcomputing.co.uk/software/pyqt/download) (>=4.4.3): Python wrappers for the [Qt](http://www.qtsoftware.com/) framework.

# Install #

  * A [release](http://code.google.com/p/spynner/downloads/list) version (recommended):

```
$ wget http://spynner.googlecode.com/files/spynner-VERSION.tgz
$ tar xvzf spynner-VERSION.tgz
$ cd spynner-VERSION
$ sudo python setup.py install
```

or

```
$ sudo easy_install spynner
```

  * The bleeding edge version:

```
$ svn checkout http://spynner.googlecode.com/svn/trunk/ spynner-trunk
$ cd spynner-trunk
$ sudo python setup.py install
```

# API #

[Browser API](http://download.zaudera.com/public/spynner/api/)

You can generate the API locally (will create docs/api directory):

```
$ python setup.py gen_doc
```

# Usage #

A basic example:

```
import spynner

browser = spynner.Browser()
browser.load("http://www.wordreference.com")
browser.runjs("console.log('I can run Javascript')")
browser.runjs("console.log('I can run jQuery: ' + jQuery('a:first').attr('href'))")
browser.select("#esen")
browser.fill("input[name=enit]", "hola")
browser.click("input[name=b]")
browser.wait_page_load()
print browser.url, browser.html
browser.close()
```

See more examples in the repository:

http://code.google.com/p/spynner/source/browse/#svn/trunk/examples

# Interactive browsing #

Sometimes you'll want to see what is going on:

```
browser = spynner.Browser()
browser.debug_level = spynner.DEBUG
browser.create_webview()
browser.show()
...
```

You can also run interactive browsing with [ipython](http://ipython.scipy.org/):

```
$ ipython

In []: browser = spynner.Browser()
In []: browser.create_webview()
In []: browser.show()
...
```

# Running Javascript #

Spynner uses jQuery to make Javascript interface easier. By default, two modules are injected to every loaded page:

  * [jQuery core](http://docs.jquery.com/Downloading_jQuery): Amongst other things, it adds the powerful [jQuery selectors](http://docs.jquery.com/Selectors), which are used internally by some Spynner methods (_fill_, _select_, _click_, _check_, ...). Of course you can also use jQuery when you inject your own code into a page.

  * [jQuery \_simulate\_ plugin](http://code.google.com/p/jqueryjs/source/browse/trunk/plugins/simulate): Makes it possible to simulate mouse and keyboard events (for now spynner uses it only in the _click_ action). Look up the library code to see which kind of events you can fire.

Note that you must use _jQuery(...) instead of jQuery(...)  or the common shortcut $(...). That prevents name clashing with the jQuery library used by the page._

# Cooking your soup: parse the HTML #

You can parse the HTML of a webpage with your favorite parsing library ([BeautifulSoup](http://www.crummy.com/software/BeautifulSoup), [lxml](http://codespeak.net/lxml/), ...). Since we are already using Jquery for Javascript, it feels just natural to work with [pyquery](http://pypi.python.org/pypi/pyquery), its Python counterpart:

```
import spynner
import pyquery

browser = spynner.Browser()
...
d = pyquery.Pyquery(browser.html)
d.make_links_absolute(browser.get_url())
href = d("#somelink").attr("href")
browser.download(href, open("/path/outputfile", "w"))
```

# Running Spynner without X11 #

Spynner needs a X11 server to run so if you are using it in a server without X11 you should install the virtual [Xvfb server](http://en.wikipedia.org/wiki/Xvfb). Debian users can use the small wrapper (_xvfb-run_) shipped with the package. If you are not using Debian, you can always [download](http://www.mail-archive.com/debian-x@lists.debian.org/msg69632/x-run) it:

```
$ xvfb-run python myscript_using_spynner.py
```

If you are planning to take snapshots of websites, make sure you run the fake server with a higher color depth (default is 8 bits):

```
$ xvfb-run -s "-screen 0 640x480x24" python my_fancy_snapshotter.py
```

# Feedback #

Open an [issue](http://code.google.com/p/spynner/issues/list) to report a bug or request a new feature. Other comments and suggestions can be directly emailed to me: [tokland@gmail.com](mailto://tokland@gmail.com).

If there is something you can do with you browser which you cannot simulate with Spynner, that's certainly a bug.