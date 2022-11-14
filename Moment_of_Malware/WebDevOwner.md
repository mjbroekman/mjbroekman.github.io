# For Web Application Developers
1. Realize that "encoding" does nothing to "protect" your code. In order for your code to function, it has to be decoded in order to function. Simply encoding your app and slapping a "no reverse engineering" comment in it, does nothing to protect it. If you don't want unauthorized use of your app to happen, licensing and encryption keys are the way to go.
2. Adding multiple layers of compression and text encoding on the app simply makes it larger... and slower.

# For Web Site Owners
1. Trust your application developers, but verify that they aren't putting backdoors or other malicious code into the applications or components that you are using.
2. Do not use 'Nulled' components. It may be attractive because they are supposed to be 'free' _FULL_ versions of commercial software, but they often come with malicious code inserted into them.
3. Be aware of your billing model. Some application (plugin, theme, etc) developers will layer encoding and compression techniques on their code resulting in [larger and slower](Terminology/Encoding) code that consumes more CPU cycles. In a bill-by-use environment (Cloud environments, some shared hosting environments), this means higher bills for you.
3. Use security plugins, web application firewalls, and/or security providers to help scan your website and keep it free of malicious code.
