
Manual testing & QA
===================

Accessibility (A11y) Testing
----------------------------

Inclusive design benefits all users, and we strive to make Kolibri accessible for all. Testing for accessibility can be challenging, but there are a few features you should check for before submitting your PR:

* Working **keyboard navigation** - everything that user can do with mouse or by touch must also work with the `keyboard alone <http://webaim.org/techniques/keyboard/>`_.
* Sufficient `color contrast <http://a11yproject.com/posts/what-is-color-contrast/>`_ between foreground text/elements and the background.
* Meaningful **text alternative** for all non-decorative images, or an empty ``ALT`` attribute in case of decorative ones.
* Meaningful **labels** on ALL `form or button elements <http://accessibility.psu.edu/forms/>`_.
* Page has one main **heading** (H1) and `consecutive lower heading levels <http://accessiblehtmlheadings.com/>`_.


Here are a few tools that we use in testing for accessibility:

* WAVE Evaluation Tool - `Firefox Add-on <https://addons.mozilla.org/en-US/firefox/addon/wave-accessibility-tool/>`__ and `Chrome extension <https://chrome.google.com/webstore/detail/wave-evaluation-tool/jbbplnpkjmmeebjpijfedlgcdilocofh>`__.
* `tota11y <http://khan.github.io/tota11y/>`_ accessibility visualization toolkit - bookmarklet for Firefox and Chrome.
* `Accessibility Developer Tools <https://chrome.google.com/webstore/detail/accessibility-developer-t/fpkknkljclfencbdbgkenhalefipecmb>`_ - Chrome extension.
* aXe Accessibility Engine - `Firefox Add-on <https://addons.mozilla.org/en-us/firefox/addon/axe-devtools/>`__ and `Chrome extension <https://chrome.google.com/webstore/detail/axe/lhdoppojpmngadmnindnejefpokejbdd>`__.

There is a much longer list on our `Kolibri Accessibility Tools Wiki page <https://github.com/learningequality/kolibri/wiki/Accessibility-Resources-(Tools)>`_ if you want to go deeper, but these four should be enough to help you avoid the most important accessibility pitfalls.


Cross-browser and OS Testing
----------------------------

It's vital to ensure that our app works across a wide range of browsers and operating systems, particularly older versions of Windows and Android that are common on old and cheap devices.

In particular, we want to ensure that Kolibri runs on major browsers that match any of `the following criteria <http://browserl.ist/?q=%3E+1%25%2C+last+2+versions%2C+ie+%3E%3D+9%2C+Firefox+ESR>`_:

 * within the last two versions
 * IE 9+ on Windows XP and up
 * has at least 1% of global usage stats

Here are some useful options, in order of simplicity:

**BrowserStack**

`BrowserStack <https://www.browserstack.com/>`_ is an incredibly useful tool for cross-browser and OS testing. In particular, it's easy to install plugin which forwards ``localhost`` to a VM running on their servers, which in turn is displayed in your browser.

**Amazon Workspaces**

In some situations, simply having a browser is not enough. For example, a developer may need to test Windows-specific backend or installer code from another OS. In many situations, a virtual machine is appropriate - however these can be slow to download and run.

Amazon's `AWS Workspaces <https://aws.amazon.com/workspaces/>`_ provides a faster alternative. They run Windows VMs in their cloud, and developers can RDP in.

**Local Virtual Machines**

Workspaces is very useful, but it has limitations: only a small range of OSes are available, and connectivity and provisioning are required.

An alternative is to run the guest operating system inside a virtual machine using e.g. `VirtualBox <https://www.virtualbox.org/wiki/Downloads>`_. This also gives more developer flexibility, including e.g. shared directories between the guest and host systems. `This tutorial <https://docs.google.com/document/d/1CG4Z0hofN0ipsny9mDf1xr2C_eY5c-T2nUbhlz2eZjA/edit>`_ shows how to test Kolibri in a VM.

**Hardware**

There are some situations where actual hardware is necessary to test the application. This is particularly true when virtualization might prohibit or impede testing features, such as lower-level driver interactions.


Responsiveness to Varying Screen Sizes
--------------------------------------

We want to ensure that the app looks and behaves reasonably across a wide range of typical screen sizes, from small tablets to large, HD monitors. It is highly recommended to constantly be testing functionality at a range of sizes.

Chrome and Firefox's Developer Tools both have some excellent functionality to simulate arbitrary screen resolutions.


Slow Network Connection Speeds
------------------------------

It's important to simulate end-users network conditions. This will help identify real-world performance issues that may not be apparent on local development machines.

Chrome's Developer Tools have functionality to simulate a variety of network connections, including Edge, 3G, and even offline. An app can be loaded into multiple tabs, each with its own custom network connectivity profile. This will not affect traffic to other tabs.

Within the Chrome Dev Tools, navigate to the Network panel. Select a connection from the drop-down to apply network throttling and latency manipulation. When a Throttle is enabled the panel indicator will show a warning icon. This is to remind you that throttling is enabled when you are in other panels.

For Kolibri, our target audience's network condition can be mimicked by setting connectivity to Regular 3G (100ms, 750kb/s, 250 kb/s).


Performance Testing with Django Debug Panel
-------------------------------------------

We have built in support for Django Debug Panel (a Chrome extension that allows tracking of AJAX requests to Django).

To use this, ensure that you have development dependencies installed, and install the `Django Debug Panel Chrome Extension <https://chrome.google.com/webstore/detail/django-debug-panel/nbiajhhibgfgkjegbnflpdccejocmbbn>`_. You can then run the development or production servers with the following environment variable set::

  DJANGO_SETTINGS_MODULE=kolibri.deployment.default.settings.debug_panel

This will activate the debug panel, and will display in the Dev tools panel of Chrome. This panel will track all page loads and API requests. However, all data bootstrapping into the template will be disabled, as our data bootstrapping prevents the page load request from being profiled, and also does not profile the bootstrapped API requests.


Generating User Data
--------------------

For manual testing, it is sometimes helpful to have generated user data, particularly for Coach and Admin facing functionality.

In order to do this, a management command is available::

  kolibri manage generateuserdata

This will generate user data for the each currently existing channel on the system. Use the `--help` flag for options.
