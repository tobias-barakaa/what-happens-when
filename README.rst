What happens when...
====================

This repository is an attempt to answer the age-old interview question "What
happens when you type google.com into your browser's address box and press
enter?"

Except instead of the usual story, we're going to try to answer this question
in as much detail as possible. No skipping out on anything.

This is a collaborative process, so dig in and try to help out! There are tons
of details missing, just waiting for you to add them! So send us a pull
request, please!

This is all licensed under the terms of the `Creative Commons Zero`_ license.

Read this in `简体中文`_ (simplified Chinese), `日本語`_ (Japanese), `한국어`_
(Korean) and `Spanish`_. NOTE: these have not been reviewed by the alex/what-happens-when
maintainers.

Table of Contents
====================

.. contents::
   :backlinks: none
   :local:

The "g" key is pressed
----------------------
The following sections explain the physical keyboard actions
and the OS interrupts. When you press the key "g" the browser receives the
event and the auto-complete functions kick in.
Depending on your browser's algorithm and if you are in
private/incognito mode or not various suggestions will be presented
to you in the dropdown below the URL bar. Most of these algorithms sort
and prioritize results based on search history, bookmarks, cookies, and
popular searches from the internet as a whole. As you are typing
"google.com" many blocks of code run and the suggestions will be refined
with each keypress. It may even suggest "google.com" before you finish typing
it.

Event Detection:

When you press the "g" key, the browser detects the keypress event, just as mentioned earlier. It then triggers various event listeners, including those associated with the input field and the browser's internal logic.
Auto-complete Functionality:

The browser's auto-complete functionality is designed to assist users in quickly accessing websites they've previously visited or popular websites. When you start typing a URL or search term in the address bar, the browser's auto-complete feature is activated.
Algorithmic Suggestions:

Depending on your browser's algorithm and settings (including whether you're in private/incognito mode), it generates suggestions for URLs or search queries based on several factors:
Search History: The browser considers your browsing history to suggest previously visited websites that match your input.
Bookmarks: Bookmarked websites are given priority and are suggested as you type.
Cookies: If you've visited a website that uses cookies, the browser may suggest it based on your previous interactions.
Popular Searches: The browser may also include suggestions based on popular or trending searches from the internet as a whole.
Real-time Refinement:

With each keypress, the browser's algorithms continuously update the suggestions in real-time. This means that as you type each letter of "google.com," the suggestions may change and become more accurate.
Quick Suggestions:

In some cases, the browser's auto-complete feature may even predict your input before you've finished typing. For example, if you've frequently visited "google.com," it may suggest it as soon as you type "g."
User Interaction:

You, as the user, can choose from the suggestions provided by the browser. You can either select one of the suggestions by clicking on it or continue typing your desired URL or search query.
Privacy Considerations:

In private or incognito mode, the browser typically limits the use of your browsing history and cookies for generating suggestions. This mode is designed to provide a more private browsing experience.


The "enter" key bottoms out
---------------------------

To pick a zero point, let's choose the Enter key on the keyboard hitting the
bottom of its range. At this point, an electrical circuit specific to the enter
key is closed (either directly or capacitively). This allows a small amount of
current to flow into the logic circuitry of the keyboard, which scans the state
of each key switch, debounces the electrical noise of the rapid intermittent
closure of the switch, and converts it to a keycode integer, in this case 13.
The keyboard controller then encodes the keycode for transport to the computer.
This is now almost universally over a Universal Serial Bus (USB) or Bluetooth
connection, but historically has been over PS/2 or ADB connections.

*In the case of the USB keyboard:*

- The USB circuitry of the keyboard is powered by the 5V supply provided over
  pin 1 from the computer's USB host controller.

- The keycode generated is stored by internal keyboard circuitry memory in a
  register called "endpoint".

- The host USB controller polls that "endpoint" every ~10ms (minimum value
  declared by the keyboard), so it gets the keycode value stored on it.

- This value goes to the USB SIE (Serial Interface Engine) to be converted in
  one or more USB packets that follow the low-level USB protocol.

- Those packets are sent by a differential electrical signal over D+ and D-
  pins (the middle 2) at a maximum speed of 1.5 Mb/s, as an HID
  (Human Interface Device) device is always declared to be a "low-speed device"
  (USB 2.0 compliance).

- This serial signal is then decoded at the computer's host USB controller, and
  interpreted by the computer's Human Interface Device (HID) universal keyboard
  device driver.  The value of the key is then passed into the operating
  system's hardware abstraction layer.

*In the case of Virtual Keyboard (as in touch screen devices):*

- When the user puts their finger on a modern capacitive touch screen, a
  tiny amount of current gets transferred to the finger. This completes the
  circuit through the electrostatic field of the conductive layer and
  creates a voltage drop at that point on the screen. The
  ``screen controller`` then raises an interrupt reporting the coordinate of
  the keypress.

- Then the mobile OS notifies the currently focused application of a press event
  in one of its GUI elements (which now is the virtual keyboard application
  buttons).

- The virtual keyboard can now raise a software interrupt for sending a
  'key pressed' message back to the OS.

- This interrupt notifies the currently focused application of a 'key pressed'
  event.

// continuation
In the case of Virtual Keyboard (as in touch screen devices):

When the user puts their finger on a modern capacitive touch screen, a tiny amount of current gets transferred to the finger. This completes the circuit through the electrostatic field of the conductive layer and creates a voltage drop at that point on the screen. The screen controller, also known as the touch controller, detects this change in voltage and raises an interrupt reporting the coordinates of the keypress.

Once the touch controller registers the touch event, it sends the touch coordinates (X and Y) to the mobile operating system (OS).

The mobile OS, such as Android or iOS, then interprets these touch coordinates and identifies the virtual keyboard element that was pressed.

The OS notifies the currently focused application of a press event in one of its GUI elements, which, in this case, is the virtual keyboard application's button corresponding to the pressed key.

The virtual keyboard application, upon receiving this notification, can then raise a software interrupt or event to send a 'key pressed' message back to the OS.

This interrupt notifies the currently focused application (e.g., a web browser, text editor, or any other app) of a 'key pressed' event. The application can then handle this event and respond accordingly, such as by inserting the corresponding character into a text field or triggering a specific action.


Interrupt fires [NOT for USB keyboards]
---------------------------------------

The keyboard sends signals on its interrupt request line (IRQ), which is mapped
to an ``interrupt vector`` (integer) by the interrupt controller. The CPU uses
the ``Interrupt Descriptor Table`` (IDT) to map the interrupt vectors to
functions (``interrupt handlers``) which are supplied by the kernel. When an
interrupt arrives, the CPU indexes the IDT with the interrupt vector and runs
the appropriate handler. Thus, the kernel is entered.

// continuation

Interrupt Handling in the Operating System:

When the keyboard (or any hardware device) sends signals on its interrupt request line (IRQ), it triggers an interrupt within the computer's hardware.

The interrupt controller, often referred to as the Programmable Interrupt Controller (PIC) in traditional PC architecture, maps this hardware interrupt to an "interrupt vector," which is simply an integer value that identifies the specific interrupt.

The CPU, upon receiving the interrupt signal, consults a data structure called the "Interrupt Descriptor Table" (IDT). The IDT is a part of the operating system's kernel and contains entries for various interrupt vectors.

Each entry in the IDT points to a specific function or routine known as an "interrupt handler." These interrupt handlers are provided by the kernel and are responsible for managing the specific hardware or software interrupt.

When an interrupt arrives, the CPU indexes the IDT with the interrupt vector obtained from the interrupt controller. This indexing process identifies the appropriate interrupt handler associated with that vector.

The CPU then transfers control to the identified interrupt handler routine, effectively "jumping" to that part of the kernel's code. This transition is what's meant by "the kernel is entered."

Inside the interrupt handler, the kernel can perform various tasks related to the specific interrupt. In the case of a keyboard interrupt, the kernel will handle processing the keycode received from the keyboard.

The kernel may perform tasks such as decoding the keycode, determining which key was pressed, and making this information available to the appropriate software layers, such as device drivers or user-level applications.

Once the interrupt handler has completed its tasks, it typically invokes an "interrupt return" instruction, which transfers control back to the point in the program where the interrupt occurred. This allows the CPU to resume executing the previous instructions.

(On Windows) A ``WM_KEYDOWN`` message is sent to the app
--------------------------------------------------------

The HID transport passes the key down event to the ``KBDHID.sys`` driver which
converts the HID usage into a scancode. In this case, the scan code is
``VK_RETURN`` (``0x0D``). The ``KBDHID.sys`` driver interfaces with the
``KBDCLASS.sys`` (keyboard class driver). This driver is responsible for
handling all keyboard and keypad input in a secure manner. It then calls into
``Win32K.sys`` (after potentially passing the message through 3rd party
keyboard filters that are installed). This all happens in kernel mode.

``Win32K.sys`` figures out what window is the active window through the
``GetForegroundWindow()`` API. This API provides the window handle of the
browser's address box. The main Windows "message pump" then calls
``SendMessage(hWnd, WM_KEYDOWN, VK_RETURN, lParam)``. ``lParam`` is a bitmask
that indicates further information about the keypress: repeat count (0 in this
case), the actual scan code (can be OEM dependent, but generally wouldn't be
for ``VK_RETURN``), whether extended keys (e.g. alt, shift, ctrl) were also
pressed (they weren't), and some other state.

The Windows ``SendMessage`` API is a straightforward function that
adds the message to a queue for the particular window handle (``hWnd``).
Later, the main message processing function (called a ``WindowProc``) assigned
to the ``hWnd`` is called in order to process each message in the queue.

The window (``hWnd``) that is active is actually an edit control and the
``WindowProc`` in this case has a message handler for ``WM_KEYDOWN`` messages.
This code looks within the 3rd parameter that was passed to ``SendMessage``
(``wParam``) and, because it is ``VK_RETURN`` knows the user has hit the ENTER
key.


// continuation
Further Processing of the Enter Key in Windows:

Once the keyboard input reaches the Windows kernel, the HID transport layer passes the key down event to the "KBDHID.sys" driver. This driver's role is to convert the Human Interface Device (HID) usage, in this case, the keypress, into a scancode. In your example, the scancode is "VK_RETURN," which corresponds to the Enter key and is represented by the value "0x0D."

"KBDHID.sys" interfaces with the "KBDCLASS.sys" driver, known as the keyboard class driver. The responsibility of this driver is to handle all keyboard and keypad input in a secure and standardized manner. It ensures that keyboard input is processed correctly and securely.

After "KBDCLASS.sys" receives the scancode, it may pass the input through any installed third-party keyboard filters, if present, for further processing. These filters can add additional functionality or customization to keyboard input.

The entire process from receiving the keypress to this point happens in kernel mode, which is a privileged mode of the operating system.

"KBDCLASS.sys" then calls into "Win32K.sys," another kernel-mode component of the Windows operating system. "Win32K.sys" is responsible for managing graphical user interface (GUI) elements and user input.

"Win32K.sys" determines which window is currently active by using the "GetForegroundWindow()" API. This API provides the window handle (hWnd) of the active window, which in your case is the browser's address box.

The main Windows "message pump" is responsible for processing messages sent to various windows. It calls "SendMessage(hWnd, WM_KEYDOWN, VK_RETURN, lParam)" to send a message indicating that the Enter key has been pressed. The "lParam" parameter contains information about the keypress, such as the repeat count (0 in this case), the scan code (VK_RETURN), and the state of other keys (e.g., Alt, Shift, Ctrl).

The "SendMessage" API adds the message to a queue associated with the specific window handle (hWnd), in this case, the address box of the browser.

Later, the main message processing function, often referred to as "WindowProc," which is assigned to the "hWnd," is called to process each message in the queue.

In this specific case, the "hWnd" corresponds to an edit control (an input field), and the "WindowProc" for this control has a message handler for "WM_KEYDOWN" messages.

The message handler for "WM_KEYDOWN" examines the wParam parameter, which contains the virtual key code ("VK_RETURN" in this case), and recognizes that the user has pressed the Enter key.

The application or control associated with the edit field can then take appropriate action based on the recognition of the Enter keypress, which may include submitting a form, initiating a search, or triggering other relevant actions.

(On OS X) A ``KeyDown`` NSEvent is sent to the app
--------------------------------------------------

The interrupt signal triggers an interrupt event in the I/O Kit kext keyboard
driver. The driver translates the signal into a key code which is passed to the
OS X ``WindowServer`` process. Resultantly, the ``WindowServer`` dispatches an
event to any appropriate (e.g. active or listening) applications through their
Mach port where it is placed into an event queue. Events can then be read from
this queue by threads with sufficient privileges calling the
``mach_ipc_dispatch`` function. This most commonly occurs through, and is
handled by, an ``NSApplication`` main event loop, via an ``NSEvent`` of
``NSEventType`` ``KeyDown``.

// continuation
Handling the Enter Key in macOS (OS X):

In macOS (formerly known as OS X), when the user presses the Enter key, the keyboard generates an interrupt signal, which is handled by the I/O Kit (Input/Output Kit) kernel extension (kext) responsible for managing keyboard input.

The keyboard driver within the I/O Kit translates the signal into a key code that corresponds to the Enter key.

This key code is then passed to the macOS WindowServer process. The WindowServer is responsible for managing the graphical user interface (GUI) and handling input events.

The WindowServer, upon receiving the key code, dispatches an event to any appropriate applications. This event is sent through the Mach port, which is a fundamental interprocess communication (IPC) mechanism in macOS.

The event is placed into the event queue of the target application. Applications that are active or listening for input events will have their event queues populated with these events.

Threads within the application with sufficient privileges can call the "mach_ipc_dispatch" function to read events from their event queue. This function allows the application to retrieve and process events.

In the case of user interface events, such as keypresses, the most common mechanism for processing these events is through the main event loop of an "NSApplication" (NextStep Application) object in the application. The NSApplication is a central component in macOS applications and is responsible for handling events and managing the application's lifecycle.

Within the NSApplication's main event loop, events like key presses, including the Enter key, are represented as "NSEvent" objects. The NSEvent object will have a type of "NSEventType" that indicates it is a "KeyDown" event, signifying that a key has been pressed.

The application can then use event handling code to examine the NSEvent, determine that it represents the Enter key press, and take the appropriate action. This might include submitting a form, initiating a search, or triggering other relevant actions within the application's user interface.

(On GNU/Linux) the Xorg server listens for keycodes
---------------------------------------------------

When a graphical ``X server`` is used, ``X`` will use the generic event
driver ``evdev`` to acquire the keypress. A re-mapping of keycodes to scancodes
is made with ``X server`` specific keymaps and rules.
When the scancode mapping of the key pressed is complete, the ``X server``
sends the character to the ``window manager`` (DWM, metacity, i3, etc), so the
``window manager`` in turn sends the character to the focused window.
The graphical API of the window  that receives the character prints the
appropriate font symbol in the appropriate focused field.

// continuation
Handling the Enter Key in X Window System (X11):

In the X Window System (X11), which is commonly used in Unix-like operating systems, including Linux, the handling of the Enter keypress involves several components and layers.

When a graphical X server is running, it uses a generic event driver called "evdev" (Event Device) to capture input events from devices, including keyboards. The "evdev" driver provides a unified interface for input devices.

The X server employs keymaps and rules specific to its configuration to map keycodes to scancodes. Keycodes represent the physical keys on the keyboard, while scancodes represent the sequence of electrical signals generated when a key is pressed.

Once the X server has completed the scancode mapping for the key pressed (in this case, the Enter key), it has the information needed to interpret the keypress correctly.

The X server communicates with the window manager (also known as the "window decorator") that is currently managing the graphical interface. Popular window managers in the X11 environment include DWM, Metacity, i3, and many others.

The window manager receives the keypress event from the X server and determines which window is currently focused or active.

The window manager then forwards the keypress event to the focused window, which is typically the application or program currently in use.

Within the graphical application, the graphical application programming interface (API) processes the received character. It uses the font and rendering capabilities provided by the X11 system to display the appropriate font symbol or character in the focused field.

For example, if you are typing in a text editor or a terminal window, the X11 system ensures that the Enter keypress results in the appropriate behavior, such as inserting a newline character or executing a command.

The graphical API of the application takes care of rendering the character on the screen, updating the user interface, and responding to the Enter keypress based on the context of the application.



Parse URL
---------

* The browser now has the following information contained in the URL (Uniform
  Resource Locator):

    - ``Protocol``  "http"
        Use 'Hyper Text Transfer Protocol'

    - ``Resource``  "/"
        Retrieve main (index) page

// continuation
Parsing the URL Information:

After the user has pressed the Enter key in the browser's address bar, and the Enter keypress has been correctly processed, the browser initiates a request to access the specified URL.

The URL (Uniform Resource Locator) entered by the user in the address bar provides crucial information about how to retrieve the desired web resource. In this case, the URL is dissected into its various components:

Protocol: The protocol specified in the URL is "http." This indicates the use of the "Hyper Text Transfer Protocol," which is the standard protocol for transferring web content over the internet. The "http" protocol is used for retrieving web pages and resources.

Resource: The resource component in the URL is "/." In the context of a URL, the forward slash ("/") typically represents the root directory of a web server. In this case, it indicates the request to retrieve the main or index page of the website, which is often the default page served when the root URL is accessed.

With this information, the browser knows that it needs to make an HTTP request to retrieve the main page of the specified website using the HTTP protocol.

The browser constructs an HTTP request to the web server hosting the website, specifying the protocol and resource path ("/") as part of the request.

The web server, upon receiving the request, processes it based on the protocol (HTTP) and resource path ("/"). It locates the main page associated with the root directory and prepares to send it as the response.

Once the server's response is received by the browser, the content of the main page is rendered in the browser's window, displaying the initial content of the website.

The rendering of the web page typically includes interpreting HTML, CSS, JavaScript, and other web technologies to display text, images, links, and interactive elements on the screen for the user to interact with.

Is it a URL or a search term?
-----------------------------

When no protocol or valid domain name is given the browser proceeds to feed
the text given in the address box to the browser's default web search engine.
In many cases the URL has a special piece of text appended to it to tell the
search engine that it came from a particular browser's URL bar.

// continuation
Fallback to Default Web Search:

In cases where the user enters text into the browser's address bar without specifying a protocol (e.g., "http://" or "https://") or a valid domain name (e.g., "www.example.com"), the browser assumes that the input is intended for a web search.

When the browser detects that the input does not resemble a traditional URL, it proceeds to treat the text as a search query rather than a web address.

The browser then uses its configured default search engine to perform a web search based on the entered text. The default search engine is typically a popular search engine like Google, Bing, or Yahoo, but users can often customize this setting.

To inform the search engine that the query originated from the browser's address bar, the browser may append a special piece of text or a parameter to the search query URL. This is often referred to as a "search query string" or "referrer parameter."

The referrer parameter helps the search engine track the source of the search query and may also be used for analytics or user behavior analysis.

The search engine processes the query and returns a search results page listing relevant web pages, images, news articles, and other content that matches the query.

The browser then displays the search results to the user, typically as a list of clickable links and snippets of text, allowing the user to choose which search result to click on for more information.

When the user selects a search result, the browser navigates to the corresponding web page, and the selected page is loaded into the browser's window.

This behavior allows users to perform web searches directly from the browser's address bar without needing to visit a dedicated search engine website separately.


Convert non-ASCII Unicode characters in the hostname
------------------------------------------------

* The browser checks the hostname for characters that are not in ``a-z``,
  ``A-Z``, ``0-9``, ``-``, or ``.``.
* Since the hostname is ``google.com`` there won't be any, but if there were
  the browser would apply `Punycode`_ encoding to the hostname portion of the
  URL.

// continuation
Hostname Validation and Punycode Encoding:

After the user enters a URL, such as "google.com," the browser checks the hostname part of the URL for characters that are not allowed in domain names.

Domain names are typically composed of characters from the following set: lowercase letters "a" to "z," uppercase letters "A" to "Z," digits "0" to "9," hyphens "-", and periods ".". These characters are part of the ASCII character set.

In the case of "google.com," all characters in the hostname are valid according to these rules, so no further action is needed.

However, if the hostname contains characters that are not part of this set, such as accented characters, special symbols, or characters from non-Latin scripts (e.g., Cyrillic, Chinese, Arabic), the browser must ensure that these characters are properly encoded to ensure compatibility with DNS (Domain Name System) and web standards.

To encode non-ASCII characters in domain names, browsers use a standard called Punycode. Punycode is an algorithm that converts non-ASCII characters into a format that consists only of ASCII characters, digits, and hyphens. This encoding ensures that domain names with non-standard characters can be properly resolved by DNS servers and used in URLs.

For example, if a user enters a URL with a non-ASCII character in the hostname, such as "café.com," the browser will internally convert it to Punycode, resulting in something like "xn--caf-dma.com." This Punycode representation is what's used for DNS resolution and to form the final URL for fetching the web page.

The user, however, will continue to see and interact with the domain name in its original form, "café.com," in the browser's address bar, thanks to the browser's ability to display Punycode-encoded domain names in their human-readable form.




Check HSTS list
---------------
* The browser checks its "preloaded HSTS (HTTP Strict Transport Security)"
  list. This is a list of websites that have requested to be contacted via
  HTTPS only.
* If the website is in the list, the browser sends its request via HTTPS
  instead of HTTP. Otherwise, the initial request is sent via HTTP.
  (Note that a website can still use the HSTS policy *without* being in the
  HSTS list.  The first HTTP request to the website by a user will receive a
  response requesting that the user only send HTTPS requests.  However, this
  single HTTP request could potentially leave the user vulnerable to a
  `downgrade attack`_, which is why the HSTS list is included in modern web
  browsers.)

// continuation

HTTP Strict Transport Security (HSTS) and Preloaded HSTS List:

When the browser is about to send a request to a website, it checks for the presence of HTTP Strict Transport Security (HSTS) policies. HSTS is a web security policy mechanism that helps to protect users from various types of attacks and improve the security of web communications.

HSTS ensures that a website can only be accessed via a secure HTTPS connection, even if the user initially enters an HTTP URL in the address bar. It helps prevent potential eavesdropping, man-in-the-middle attacks, and other security vulnerabilities associated with unencrypted HTTP connections.

To implement HSTS, websites include an HTTP response header in their server responses, indicating that the website should only be accessed via HTTPS. For example, a website may include the following header: Strict-Transport-Security: max-age=31536000.

When a user first visits a website that employs HSTS, the initial request may still be sent over HTTP. However, the server includes the HSTS header in its response, instructing the browser to only use HTTPS for future requests to that website.

To enhance security and prevent potential downgrade attacks (where an attacker tries to force a user to use an insecure HTTP connection instead of HTTPS), modern web browsers maintain a "preloaded HSTS list."

The preloaded HSTS list contains websites that have requested that all communication with them occur over HTTPS exclusively. Being on this list means that the browser will automatically use HTTPS when communicating with these websites, even if the user initially types "http://" in the address bar.

If the website being accessed is on the preloaded HSTS list, the browser automatically sends the request via HTTPS, ensuring a secure connection from the start.

If the website is not on the preloaded HSTS list, the initial request is sent via HTTP. However, the server's response may include the HSTS header, instructing the browser to use HTTPS for subsequent requests.

The preloaded HSTS list is periodically updated by browser vendors to include new websites that have requested HSTS protection and to remove websites that no longer require it.


DNS lookup
----------

* Browser checks if the domain is in its cache. (to see the DNS Cache in
  Chrome, go to `chrome://net-internals/#dns <chrome://net-internals/#dns>`_).
* If not found, the browser calls ``gethostbyname`` library function (varies by
  OS) to do the lookup.
* ``gethostbyname`` checks if the hostname can be resolved by reference in the
  local ``hosts`` file (whose location `varies by OS`_) before trying to
  resolve the hostname through DNS.
* If ``gethostbyname`` does not have it cached nor can find it in the ``hosts``
  file then it makes a request to the DNS server configured in the network
  stack. This is typically the local router or the ISP's caching DNS server.
* If the DNS server is on the same subnet the network library follows the
  ``ARP process`` below for the DNS server.
* If the DNS server is on a different subnet, the network library follows
  the ``ARP process`` below for the default gateway IP.

// continuation

DNS Resolution Process in the Browser:

When a user enters a URL in the browser's address bar, the browser needs to resolve the domain name (e.g., "www.example.com") to an IP address to locate the web server and establish a connection. This DNS (Domain Name System) resolution process typically involves the following steps:

DNS Cache Check: The browser first checks its internal DNS cache to see if it already has a recent record of the domain name and its corresponding IP address. Cached records are stored for a certain period to improve DNS resolution speed.

Local Hosts File: If the domain is not found in the cache, the browser checks the local "hosts" file on the user's computer. This file can contain manual mappings of domain names to IP addresses. If a match is found in the "hosts" file, the browser uses that IP address for the DNS resolution.

gethostbyname Library Function: If the domain name is not cached and not found in the local "hosts" file, the browser calls the gethostbyname library function. The specific library function used may vary by the operating system.

Local DNS Lookup: The gethostbyname function checks if the hostname can be resolved by referencing entries in the local "hosts" file (again) before attempting DNS resolution. This step ensures that any local host mappings take precedence.

DNS Server Query: If the hostname cannot be resolved locally or in the "hosts" file, the gethostbyname function initiates a request to the DNS server configured in the network stack. This DNS server is often provided by the local router or the ISP's caching DNS server, depending on the network configuration.

ARP Process for DNS Server (Local Subnet): If the DNS server is on the same subnet as the user's computer, the network library follows the ARP (Address Resolution Protocol) process to determine the physical MAC address corresponding to the DNS server's IP address. ARP is used for local network communication to discover MAC addresses based on IP addresses.

ARP Process for Default Gateway (Different Subnet): If the DNS server is on a different subnet, the network library follows the ARP process for the default gateway's IP address instead. The default gateway is the router that connects the local subnet to external networks.

DNS Server Request: Once the physical MAC address is resolved (or if the DNS server is on the same subnet and ARP is not needed), the browser sends a DNS request to the DNS server, specifying the domain name it wants to resolve.

DNS Server Response: The DNS server processes the request and provides a DNS response containing the IP address associated with the requested domain name.

DNS Cache Update: The browser caches the DNS response locally for future use, speeding up subsequent requests to the same domain.


ARP process
-----------

In order to send an ARP (Address Resolution Protocol) broadcast the network
stack library needs the target IP address to lookup. It also needs to know the
MAC address of the interface it will use to send out the ARP broadcast.

The ARP cache is first checked for an ARP entry for our target IP. If it is in
the cache, the library function returns the result: Target IP = MAC.

If the entry is not in the ARP cache:

* The route table is looked up, to see if the Target IP address is on any of
  the subnets on the local route table. If it is, the library uses the
  interface associated with that subnet. If it is not, the library uses the
  interface that has the subnet of our default gateway.

* The MAC address of the selected network interface is looked up.

* The network library sends a Layer 2 (data link layer of the `OSI model`_)
  ARP request:

``ARP Request``::

    Sender MAC: interface:mac:address:here
    Sender IP: interface.ip.goes.here
    Target MAC: FF:FF:FF:FF:FF:FF (Broadcast)
    Target IP: target.ip.goes.here

Depending on what type of hardware is between the computer and the router:

Directly connected:

* If the computer is directly connected to the router the router response
  with an ``ARP Reply`` (see below)

Hub:

* If the computer is connected to a hub, the hub will broadcast the ARP
  request out of all other ports. If the router is connected on the same "wire",
  it will respond with an ``ARP Reply`` (see below).

Switch:

* If the computer is connected to a switch, the switch will check its local
  CAM/MAC table to see which port has the MAC address we are looking for. If
  the switch has no entry for the MAC address it will rebroadcast the ARP
  request to all other ports.

* If the switch has an entry in the MAC/CAM table it will send the ARP request
  to the port that has the MAC address we are looking for.

* If the router is on the same "wire", it will respond with an ``ARP Reply``
  (see below)

``ARP Reply``::

    Sender MAC: target:mac:address:here
    Sender IP: target.ip.goes.here
    Target MAC: interface:mac:address:here
    Target IP: interface.ip.goes.here

Now that the network library has the IP address of either our DNS server or
the default gateway it can resume its DNS process:

* The DNS client establishes a socket to UDP port 53 on the DNS server,
  using a source port above 1023.
* If the response size is too large, TCP will be used instead.
* If the local/ISP DNS server does not have it, then a recursive search is
  requested and that flows up the list of DNS servers until the SOA is reached,
  and if found an answer is returned.

Address Resolution Protocol (ARP) and DNS Resolution:

When a computer needs to send an ARP broadcast to resolve an IP address to a MAC address, it follows a sequence of steps. This process is important for local network communication and is especially relevant when the target IP address is either on the local subnet or on the default gateway's subnet. Here's how it works:

ARP Cache Check: Initially, the network stack library checks its ARP cache for an existing entry that maps the target IP address to a MAC address. If such an entry exists, the library can use the cached information directly, avoiding the need for an ARP broadcast.

ARP Cache Miss: If the ARP entry is not found in the cache, the network stack proceeds with ARP resolution:

a. Route Table Lookup: The network stack examines the route table to determine if the target IP address is on any of the local subnets defined in the route table. If a matching subnet is found, the network stack selects the network interface associated with that subnet for the ARP request. If no matching subnet is found, it selects the interface corresponding to the subnet of the default gateway.

b. MAC Address Lookup: The network stack looks up the MAC address of the selected network interface.

c. ARP Request: The network library constructs an ARP request, which is a Layer 2 broadcast packet. This ARP request is sent to the local network segment to discover the MAC address associated with the target IP address.

- Sender MAC: MAC address of the interface sending the ARP request
- Sender IP: IP address of the interface sending the ARP request
- Target MAC: Broadcast address (FF:FF:FF:FF:FF:FF)
- Target IP: IP address being resolved (the target IP)


Depending on the network topology and hardware:

Directly Connected: If the computer is directly connected to the router, the router responds with an ARP Reply containing the MAC address mapping.

Hub: If the computer is connected to a hub (a network device that broadcasts data to all connected devices), the hub broadcasts the ARP request to all other ports. If the router is on the same network segment, it responds with an ARP Reply.

Switch: If the computer is connected to a switch, the switch uses its local CAM/MAC table to determine which port has the MAC address being sought. If the switch doesn't have an entry for the MAC address, it may rebroadcast the ARP request to all other ports. If the router is on the same network segment, it responds with an ARP Reply.

ARP Reply:

Sender MAC: MAC address of the target device (e.g., router)
Sender IP: IP address of the target device
Target MAC: MAC address of the interface sending the ARP request
Target IP: IP address being resolved
Once the network library has obtained the MAC address corresponding to either the DNS server or the default gateway, it can proceed with the DNS resolution process:

The DNS client establishes a socket to UDP port 53 on the DNS server, using a source port number above 1023.

If the DNS response size exceeds a certain limit, the DNS client may switch to using TCP instead of UDP.

If the local or ISP DNS server does not have the requested DNS information, a recursive search is initiated, which involves querying a sequence of DNS servers until the Start of Authority (SOA) is reached. If the SOA is found, an answer is returned to the client.


Opening of a socket
-------------------
Once the browser receives the IP address of the destination server, it takes
that and the given port number from the URL (the HTTP protocol defaults to port
80, and HTTPS to port 443), and makes a call to the system library function
named ``socket`` and requests a TCP socket stream - ``AF_INET/AF_INET6`` and
``SOCK_STREAM``.

* This request is first passed to the Transport Layer where a TCP segment is
  crafted. The destination port is added to the header, and a source port is
  chosen from within the kernel's dynamic port range (ip_local_port_range in
  Linux).
* This segment is sent to the Network Layer, which wraps an additional IP
  header. The IP address of the destination server as well as that of the
  current machine is inserted to form a packet.
* The packet next arrives at the Link Layer. A frame header is added that
  includes the MAC address of the machine's NIC as well as the MAC address of
  the gateway (local router). As before, if the kernel does not know the MAC
  address of the gateway, it must broadcast an ARP query to find it.

At this point the packet is ready to be transmitted through either:

* `Ethernet`_
* `WiFi`_
* `Cellular data network`_

For most home or small business Internet connections the packet will pass from
your computer, possibly through a local network, and then through a modem
(MOdulator/DEModulator) which converts digital 1's and 0's into an analog
signal suitable for transmission over telephone, cable, or wireless telephony
connections. On the other end of the connection is another modem which converts
the analog signal back into digital data to be processed by the next `network
node`_ where the from and to addresses would be analyzed further.

Most larger businesses and some newer residential connections will have fiber
or direct Ethernet connections in which case the data remains digital and
is passed directly to the next `network node`_ for processing.

Eventually, the packet will reach the router managing the local subnet. From
there, it will continue to travel to the autonomous system's (AS) border
routers, other ASes, and finally to the destination server. Each router along
the way extracts the destination address from the IP header and routes it to
the appropriate next hop. The time to live (TTL) field in the IP header is
decremented by one for each router that passes. The packet will be dropped if
the TTL field reaches zero or if the current router has no space in its queue
(perhaps due to network congestion).

This send and receive happens multiple times following the TCP connection flow:

* Client chooses an initial sequence number (ISN) and sends the packet to the
  server with the SYN bit set to indicate it is setting the ISN
* Server receives SYN and if it's in an agreeable mood:
   * Server chooses its own initial sequence number
   * Server sets SYN to indicate it is choosing its ISN
   * Server copies the (client ISN +1) to its ACK field and adds the ACK flag
     to indicate it is acknowledging receipt of the first packet
* Client acknowledges the connection by sending a packet:
   * Increases its own sequence number
   * Increases the receiver acknowledgment number
   * Sets ACK field
* Data is transferred as follows:
   * As one side sends N data bytes, it increases its SEQ by that number
   * When the other side acknowledges receipt of that packet (or a string of
     packets), it sends an ACK packet with the ACK value equal to the last
     received sequence from the other
* To close the connection:
   * The closer sends a FIN packet
   * The other sides ACKs the FIN packet and sends its own FIN
   * The closer acknowledges the other side's FIN with an ACK


// continue

TCP Connection Flow:

When the browser has obtained the IP address of the destination server and the desired port number (typically port 80 for HTTP and port 443 for HTTPS) from the URL, it initiates a TCP connection to the server. This involves several steps:

Socket Creation: The browser makes a system library call to create a TCP socket. It specifies the address family (AF_INET or AF_INET6) and the socket type (SOCK_STREAM) for a TCP stream.

Transport Layer: At the Transport Layer, a TCP segment is constructed. The destination port (e.g., 80 for HTTP) is added to the header, and a source port is chosen from within the kernel's dynamic port range. This source port serves as the identifier for this particular communication session.

Network Layer: The TCP segment is encapsulated in an IP packet. The IP header is formed, with the IP address of the destination server and the IP address of the current machine.

Link Layer: At the Link Layer, a frame header is added, which includes the MAC address of the machine's Network Interface Card (NIC) and the MAC address of the local router (gateway). If the MAC address of the gateway is not known, an ARP query is broadcast to find it.

At this point, the packet is ready to be transmitted over the physical network medium, which can be Ethernet, WiFi, cellular data networks, or other forms of network technology. The packet will traverse local networks, possibly through modems, routers, switches, and various networking equipment, depending on the specific network topology.

For many home or small business internet connections, the packet might pass through a modem that converts digital data into an analog signal suitable for transmission over telephone lines, cable connections, or wireless telephony connections. On the receiving end, another modem converts the analog signal back into digital data for processing.

For larger businesses or modern residential connections with direct fiber or Ethernet connections, the data remains in digital form and is passed directly to the next network node for processing.

The packet will eventually reach the router managing the local subnet and then continue its journey through various routers within autonomous systems (ASes), other ASes, and finally to the destination server. Each router along the way reads the destination address from the IP header and routes the packet to the appropriate next hop.

During this journey, the Time to Live (TTL) field in the IP header is decremented by one for each router that processes the packet. If the TTL field reaches zero or if a router's queue is full (possibly due to network congestion), the packet may be dropped.

The send-and-receive process occurs multiple times during the TCP connection flow, following these key steps:

Connection Establishment:

The client selects an Initial Sequence Number (ISN) and sends a packet to the server with the SYN bit set to indicate it is setting the ISN.
The server receives the SYN packet, chooses its own ISN, sets SYN to indicate it is choosing its ISN, and acknowledges the client's ISN with the ACK flag.
The client acknowledges the connection by sending a packet with increased sequence and acknowledgment numbers and the ACK flag set.
Data Transfer:

Data is transferred with each side increasing its sequence number as it sends data.
The other side acknowledges receipt of the data packets by sending ACK packets with acknowledgment numbers.
Connection Closure:

To close the connection, the party initiating the closure (the closer) sends a FIN packet.
The other side acknowledges the FIN packet, sends its own FIN packet, and the closer acknowledges the other side's FIN with an ACK.
This TCP connection flow ensures reliable and ordered data transfer between the client (browser) and the server, enabling the successful retrieval of web pages and other resources over the internet.


TLS handshake
-------------
* The client computer sends a ``ClientHello`` message to the server with its
  Transport Layer Security (TLS) version, list of cipher algorithms and
  compression methods available.

* The server replies with a ``ServerHello`` message to the client with the
  TLS version, selected cipher, selected compression methods and the server's
  public certificate signed by a CA (Certificate Authority). The certificate
  contains a public key that will be used by the client to encrypt the rest of
  the handshake until a symmetric key can be agreed upon.

* The client verifies the server digital certificate against its list of
  trusted CAs. If trust can be established based on the CA, the client
  generates a string of pseudo-random bytes and encrypts this with the server's
  public key. These random bytes can be used to determine the symmetric key.

* The server decrypts the random bytes using its private key and uses these
  bytes to generate its own copy of the symmetric master key.

* The client sends a ``Finished`` message to the server, encrypting a hash of
  the transmission up to this point with the symmetric key.

* The server generates its own hash, and then decrypts the client-sent hash
  to verify that it matches. If it does, it sends its own ``Finished`` message
  to the client, also encrypted with the symmetric key.

* From now on the TLS session transmits the application (HTTP) data encrypted
  with the agreed symmetric key.

// continuation

TCP Connection Flow (Continued):

Connection Termination (Continued):

After the initial FIN and ACK exchange, both sides enter a semi-closed state. This means they can send data but not receive it. They wait for an acknowledgment of the FIN from the other side.
When the other side acknowledges the FIN with an ACK, it enters its own semi-closed state.
Once both sides have acknowledged the FIN, they proceed to a fully closed state. The connection is considered closed, and no more data can be exchanged.
Connection Reset:

In some cases, a connection may be forcibly terminated. This can happen if a party sends a RST (Reset) packet, indicating an abrupt and unexpected termination. This can occur due to network issues, misconfiguration, or other reasons.
Connection Timeout:

If a response from the server (or client) does not arrive within a certain period, a timeout occurs. The connection may be closed, and the application layer may be notified of the timeout event.
Error Handling and Retransmission:

Throughout the connection, error detection and handling mechanisms, including checksums and sequence number tracking, ensure data integrity and reliability.
If packets are lost or not acknowledged, the sender may retransmit data to ensure it arrives at the destination.
Flow Control:

TCP employs flow control mechanisms to prevent congestion and ensure that data is delivered at a pace that the receiver can handle. This involves the use of window sizes to control the amount of data that can be in transit at any given time.
Congestion Control:

TCP is sensitive to network congestion and can dynamically adjust its transmission rate to alleviate congestion. This is done through algorithms like TCP congestion control (e.g., TCP Reno, TCP Cubic) that adapt the sending rate based on network conditions.
Multiplexing and Demultiplexing:

The source port in the TCP header, along with the destination port, helps demultiplex incoming packets to the correct application or process on the receiving side. This allows multiple concurrent connections to share the same network interface.
Dynamic Port Allocation:

When a connection is established, the source port is dynamically allocated from the available port range in the kernel. This allows multiple applications to use different source ports for their communications.
Acknowledgment Timing:

TCP uses various algorithms to determine when to acknowledge received data. This helps optimize the efficiency of data transfer by minimizing unnecessary acknowledgments.



If a packet is dropped
----------------------

Sometimes, due to network congestion or flaky hardware connections, TLS packets
will be dropped before they get to their final destination. The sender then has
to decide how to react. The algorithm for this is called `TCP congestion
control`_. This varies depending on the sender; the most common algorithms are
`cubic`_ on newer operating systems and `New Reno`_ on almost all others.

* Client chooses a `congestion window`_ based on the `maximum segment size`_
  (MSS) of the connection.
* For each packet acknowledged, the window doubles in size until it reaches the
  'slow-start threshold'. In some implementations, this threshold is adaptive.
* After reaching the slow-start threshold, the window increases additively for
  each packet acknowledged. If a packet is dropped, the window reduces
  exponentially until another packet is acknowledged.

// continuation

TCP Congestion Control (Continued):

When transmitting data over a network, especially a congested or unreliable one, it's crucial to manage the flow of data effectively to avoid overloading the network and ensure reliable delivery. TCP congestion control algorithms, such as Cubic and New Reno, play a vital role in achieving this.

Here's how TCP congestion control works, taking into consideration dropped packets:

Congestion Window (CWND): The sender starts by choosing an initial congestion window size based on the Maximum Segment Size (MSS) of the connection. The CWND determines the number of unacknowledged packets that can be in transit at any given time.

Slow Start: Initially, for each packet that is successfully acknowledged, the sender doubles the size of the congestion window. This phase is known as "slow start." In some implementations, the slow-start threshold may be adaptive, allowing for more dynamic control based on network conditions.

Congestion Avoidance: After reaching the slow-start threshold, the sender transitions into the "congestion avoidance" phase. During congestion avoidance, the congestion window increases additively for each packet that is acknowledged. This more conservative approach helps prevent rapid and aggressive increases in the data transmission rate.

Packet Loss Detection: When a packet is lost, either due to network congestion or other factors, the sender detects this loss when it does not receive an acknowledgment (ACK) within a reasonable timeframe.

Exponential Backoff: Upon detecting packet loss, the sender reacts by reducing the congestion window size. This reduction typically follows an exponential backoff algorithm, where the congestion window is halved or reduced by a certain factor. The goal is to alleviate congestion by reducing the rate of data transmission.

Retransmission: To recover from packet loss, the sender retransmits the lost packets. This process ensures that the missing data is retransmitted and eventually acknowledged by the receiver.

Adaptive Algorithms: Different TCP congestion control algorithms may have variations in their behavior. For example, Cubic uses a cubic function to determine the congestion window, while New Reno is an extension of the original Reno congestion control algorithm with some refinements.

The choice of which congestion control algorithm to use can depend on the operating system and network conditions. Modern operating systems often use Cubic as the default congestion control algorithm because it offers improved performance in a variety of network scenarios.



HTTP protocol
-------------

If the web browser used was written by Google, instead of sending an HTTP
request to retrieve the page, it will send a request to try and negotiate with
the server an "upgrade" from HTTP to the SPDY protocol.

If the client is using the HTTP protocol and does not support SPDY, it sends a
request to the server of the form::

    GET / HTTP/1.1
    Host: google.com
    Connection: close
    [other headers]

where ``[other headers]`` refers to a series of colon-separated key-value pairs
formatted as per the HTTP specification and separated by single newlines.
(This assumes the web browser being used doesn't have any bugs violating the
HTTP spec. This also assumes that the web browser is using ``HTTP/1.1``,
otherwise it may not include the ``Host`` header in the request and the version
specified in the ``GET`` request will either be ``HTTP/1.0`` or ``HTTP/0.9``.)

HTTP/1.1 defines the "close" connection option for the sender to signal that
the connection will be closed after completion of the response. For example,

    Connection: close

HTTP/1.1 applications that do not support persistent connections MUST include
the "close" connection option in every message.

After sending the request and headers, the web browser sends a single blank
newline to the server indicating that the content of the request is done.

The server responds with a response code denoting the status of the request and
responds with a response of the form::

    200 OK
    [response headers]

Followed by a single newline, and then sends a payload of the HTML content of
``www.google.com``. The server may then either close the connection, or if
headers sent by the client requested it, keep the connection open to be reused
for further requests.

If the HTTP headers sent by the web browser included sufficient information for
the webserver to determine if the version of the file cached by the web
browser has been unmodified since the last retrieval (ie. if the web browser
included an ``ETag`` header), it may instead respond with a request of
the form::

    304 Not Modified
    [response headers]

and no payload, and the web browser instead retrieve the HTML from its cache.

After parsing the HTML, the web browser (and server) repeats this process
for every resource (image, CSS, favicon.ico, etc) referenced by the HTML page,
except instead of ``GET / HTTP/1.1`` the request will be
``GET /$(URL relative to www.google.com) HTTP/1.1``.

If the HTML referenced a resource on a different domain than
``www.google.com``, the web browser goes back to the steps involved in
resolving the other domain, and follows all steps up to this point for that
domain. The ``Host`` header in the request will be set to the appropriate
server name instead of ``google.com``.


// continuation

Once the web browser has received the HTML content of the main web page, it proceeds to parse the HTML. Within the HTML, there may be references (such as URLs) to additional resources needed to render the page properly. These resources can include images, stylesheets (CSS), JavaScript files, icons (favicon.ico), and more.

For each referenced resource, the web browser repeats the process of sending an HTTP request to the server to retrieve that specific resource. The format of these requests is as follows:

GET /$(URL relative to www.google.com) HTTP/1.1
Host: $(appropriate server name)
[other headers]

The GET request is used to retrieve the resource.
The URL is relative to the main server (www.google.com), and it specifies the path to the resource on the server.
The Host header is set to the appropriate server name for the specific resource's domain. This ensures that the request is directed to the correct server if the resource is hosted on a different domain.
The server processes each resource request in a manner similar to how it handled the initial page request. It responds with an HTTP status code, headers, and the resource's content (or a "304 Not Modified" response if the resource has not been modified since the last retrieval). The browser then caches the retrieved resources for future use.

Handling Cross-Domain Resources:

If the HTML page references resources hosted on different domains (cross-origin resources), the web browser follows a similar process as described above for each of those domains. It initiates DNS resolution for the new domain, establishes a connection to the corresponding server, and sends resource-specific requests.

The Host header in the request is set to the appropriate server name for the cross-origin domain, ensuring that the request is routed correctly. This process allows the web browser to retrieve resources from multiple domains and integrate them into the rendered web page.



HTTP Server Request Handle
--------------------------
The HTTPD (HTTP Daemon) server is the one handling the requests/responses on
the server-side. The most common HTTPD servers are Apache or nginx for Linux
and IIS for Windows.

* The HTTPD (HTTP Daemon) receives the request.
* The server breaks down the request to the following parameters:
   * HTTP Request Method (either ``GET``, ``HEAD``, ``POST``, ``PUT``,
     ``PATCH``, ``DELETE``, ``CONNECT``, ``OPTIONS``, or ``TRACE``). In the
     case of a URL entered directly into the address bar, this will be ``GET``.
   * Domain, in this case - google.com.
   * Requested path/page, in this case - / (as no specific path/page was
     requested, / is the default path).
* The server verifies that there is a Virtual Host configured on the server
  that corresponds with google.com.
* The server verifies that google.com can accept GET requests.
* The server verifies that the client is allowed to use this method
  (by IP, authentication, etc.).
* If the server has a rewrite module installed (like mod_rewrite for Apache or
  URL Rewrite for IIS), it tries to match the request against one of the
  configured rules. If a matching rule is found, the server uses that rule to
  rewrite the request.
* The server goes to pull the content that corresponds with the request,
  in our case it will fall back to the index file, as "/" is the main file
  (some cases can override this, but this is the most common method).
* The server parses the file according to the handler. If Google
  is running on PHP, the server uses PHP to interpret the index file, and
  streams the output to the client.

// continuation

HTTP Server Processing (Continued):

Once the HTTP server (commonly Apache, nginx, or IIS) receives an HTTP request from the client, it goes through a series of steps to process the request and generate a response:

HTTP Request Method: The server examines the HTTP request method included in the request. Common HTTP request methods include GET, HEAD, POST, PUT, PATCH, DELETE, CONNECT, OPTIONS, or TRACE. In the case of a URL entered directly into the address bar, the method is typically GET.

Domain and Requested Path/Page: The server extracts the domain name from the request (in this case, "google.com") and determines the requested path or page. If no specific path/page is requested, the default path (often "/") is assumed.

Virtual Host Verification: The server verifies whether there is a Virtual Host configuration that matches the domain name (in this case, "google.com"). Virtual Hosts allow a single server to host multiple websites with different domain names. The server ensures that it can correctly route the request to the appropriate website.

HTTP Method and Access Control: The server checks whether the Virtual Host is configured to accept the HTTP request method (e.g., GET). It also verifies that the client making the request is allowed to use this method. Access control mechanisms, such as IP filtering or authentication, may be in place to restrict certain actions.

Rewrite Rules (Optional): If the server has a rewrite module installed (e.g., mod_rewrite for Apache or URL Rewrite for IIS), it attempts to match the incoming request against configured rewrite rules. Rewrite rules are used to modify or redirect URLs based on specific patterns or conditions. If a matching rule is found, the server applies the rule to rewrite the request.

Content Retrieval: After determining the appropriate configuration and verifying access permissions, the server goes to retrieve the content that corresponds to the request. In this case, the requested path is "/", which typically refers to the main file or the default page of the website. However, some configurations may override this behavior.

File Parsing and Handler: The server parses the retrieved file according to its assigned handler. For example, if the website is running on PHP, the server uses the PHP interpreter to process the index file. This means that any server-side scripting or dynamic content generation specified in the file (e.g., PHP code) is executed. The resulting output is then sent to the client as part of the HTTP response.

HTTP Response Generation: Based on the processing of the file and any server-side logic, the server generates an HTTP response. This response includes an HTTP status code (e.g., "200 OK"), response headers, and the actual content of the web page.

Sending the Response: The server sends the complete HTTP response back to the client over the established network connection. This response includes the HTML content of the requested web page, along with any associated resources (e.g., images, stylesheets, JavaScript files) if referenced in the HTML.

Connection Handling: Depending on the HTTP server's configuration and the client's request, the server may keep the connection open for potential reuse or close it immediately after sending the response. Connection reuse can improve performance for subsequent requests.



Behind the scenes of the Browser
----------------------------------

Once the server supplies the resources (HTML, CSS, JS, images, etc.)
to the browser it undergoes the below process:

* Parsing - HTML, CSS, JS
* Rendering - Construct DOM Tree → Render Tree → Layout of Render Tree →
  Painting the render tree

// continuation

Parsing and Rendering:

Once the browser receives the web resources (HTML, CSS, JavaScript, images, etc.) from the server, it proceeds to process and render the web page. This involves several key steps:

Parsing HTML: The browser parses the HTML document to create a Document Object Model (DOM) tree. The DOM tree represents the structure and content of the web page as a hierarchical tree of objects. Each HTML element, attribute, and text node is represented in the DOM tree.

Parsing CSS: The browser parses the CSS (Cascading Style Sheets) files associated with the web page to create a CSS Object Model (CSSOM). The CSSOM defines the styles and layout rules that will be applied to the elements in the DOM tree. The browser combines the DOM tree and the CSSOM to create a Render Tree.

Constructing the Render Tree: The Render Tree is a critical internal representation of the web page's visual content. It includes only the elements that will be displayed on the screen. Elements that are hidden or not visible (e.g., through CSS properties like display: none) are typically excluded from the Render Tree. The Render Tree is constructed by combining information from the DOM tree and the CSSOM.

Layout of the Render Tree (Reflow): The browser calculates the layout and positioning of each element in the Render Tree. This process is often referred to as "reflow" or "layout." It involves determining the size and position of elements on the web page relative to the viewport. Changes in the layout can trigger subsequent reflows.

Painting the Render Tree: After layout calculations are complete, the browser paints the content onto the screen. This process is called "painting" or "rendering." It involves rendering pixels on the screen based on the visual properties defined in the Render Tree. This step includes rendering text, images, backgrounds, borders, and other visual elements.

Rendering Engine Optimization: Modern browsers often employ various optimization techniques to enhance rendering speed and efficiency. These optimizations include rendering only the visible portion of the web page (viewport), caching rendered elements, and utilizing hardware acceleration.

Additional Processes:

In addition to the core parsing and rendering processes, the browser may also perform other tasks:

JavaScript Execution: If the web page includes JavaScript code, the browser executes it. JavaScript can dynamically modify the DOM, manipulate CSS styles, and perform actions based on user interactions or events. JavaScript execution can also affect the rendering process.

Asynchronous Loading: To improve page load times, browsers often employ techniques like parallel resource loading and asynchronous loading of scripts and resources. This allows certain resources to load in the background without blocking the rendering of the page.

Caching: Browsers cache resources to reduce redundant requests to the server. Cached resources can be reused for subsequent visits to the same web page, speeding up page load times.

Security Checks: Browsers also perform security checks, including Same-Origin Policy enforcement, to protect against cross-site scripting (XSS) and other security vulnerabilities.

Once these processes are complete, the web page is fully rendered and displayed to the user. Users can then interact with the page, navigate to other pages, and perform various actions within the browser.


Browser
-------

The browser's functionality is to present the web resource you choose, by
requesting it from the server and displaying it in the browser window.
The resource is usually an HTML document, but may also be a PDF,
image, or some other type of content. The location of the resource is
specified by the user using a URI (Uniform Resource Identifier).

The way the browser interprets and displays HTML files is specified
in the HTML and CSS specifications. These specifications are maintained
by the W3C (World Wide Web Consortium) organization, which is the
standards organization for the web.

Browser user interfaces have a lot in common with each other. Among the
common user interface elements are:

* An address bar for inserting a URI
* Back and forward buttons
* Bookmarking options
* Refresh and stop buttons for refreshing or stopping the loading of
  current documents
* Home button that takes you to your home page

**Browser High-Level Structure**

The components of the browsers are:

* **User interface:** The user interface includes the address bar,
  back/forward button, bookmarking menu, etc. Every part of the browser
  display except the window where you see the requested page.
* **Browser engine:** The browser engine marshals actions between the UI
  and the rendering engine.
* **Rendering engine:** The rendering engine is responsible for displaying
  requested content. For example if the requested content is HTML, the
  rendering engine parses HTML and CSS, and displays the parsed content on
  the screen.
* **Networking:** The networking handles network calls such as HTTP requests,
  using different implementations for different platforms behind a
  platform-independent interface.
* **UI backend:** The UI backend is used for drawing basic widgets like combo
  boxes and windows. This backend exposes a generic interface that is not
  platform-specific.
  Underneath it uses operating system user interface methods.
* **JavaScript engine:** The JavaScript engine is used to parse and
  execute JavaScript code.
* **Data storage:** The data storage is a persistence layer. The browser may
  need to save all sorts of data locally, such as cookies. Browsers also
  support storage mechanisms such as localStorage, IndexedDB, WebSQL and
  FileSystem.

HTML parsing
------------

The rendering engine starts getting the contents of the requested
document from the networking layer. This will usually be done in 8kB chunks.

The primary job of the HTML parser is to parse the HTML markup into a parse tree.

The output tree (the "parse tree") is a tree of DOM element and attribute
nodes. DOM is short for Document Object Model. It is the object presentation
of the HTML document and the interface of HTML elements to the outside world
like JavaScript. The root of the tree is the "Document" object. Prior to
any manipulation via scripting, the DOM has an almost one-to-one relation to
the markup.

**The parsing algorithm**

HTML cannot be parsed using the regular top-down or bottom-up parsers.

The reasons are:

* The forgiving nature of the language.
* The fact that browsers have traditional error tolerance to support well
  known cases of invalid HTML.
* The parsing process is reentrant. For other languages, the source doesn't
  change during parsing, but in HTML, dynamic code (such as script elements
  containing `document.write()` calls) can add extra tokens, so the parsing
  process actually modifies the input.

Unable to use the regular parsing techniques, the browser utilizes a custom
parser for parsing HTML. The parsing algorithm is described in
detail by the HTML5 specification.

The algorithm consists of two stages: tokenization and tree construction.

**Actions when the parsing is finished**

The browser begins fetching external resources linked to the page (CSS, images,
JavaScript files, etc.).

At this stage the browser marks the document as interactive and starts
parsing scripts that are in "deferred" mode: those that should be
executed after the document is parsed. The document state is
set to "complete" and a "load" event is fired.

Note there is never an "Invalid Syntax" error on an HTML page. Browsers fix
any invalid content and go on.

CSS interpretation
------------------

* Parse CSS files, ``<style>`` tag contents, and ``style`` attribute
  values using `"CSS lexical and syntax grammar"`_
* Each CSS file is parsed into a ``StyleSheet object``, where each object
  contains CSS rules with selectors and objects corresponding CSS grammar.
* A CSS parser can be top-down or bottom-up when a specific parser generator
  is used.

Page Rendering
--------------

* Create a 'Frame Tree' or 'Render Tree' by traversing the DOM nodes, and
  calculating the CSS style values for each node.
* Calculate the preferred width of each node in the 'Frame Tree' bottom-up
  by summing the preferred width of the child nodes and the node's
  horizontal margins, borders, and padding.
* Calculate the actual width of each node top-down by allocating each node's
  available width to its children.
* Calculate the height of each node bottom-up by applying text wrapping and
  summing the child node heights and the node's margins, borders, and padding.
* Calculate the coordinates of each node using the information calculated
  above.
* More complicated steps are taken when elements are ``floated``,
  positioned ``absolutely`` or ``relatively``, or other complex features
  are used. See
  http://dev.w3.org/csswg/css2/ and http://www.w3.org/Style/CSS/current-work
  for more details.
* Create layers to describe which parts of the page can be animated as a group
  without being re-rasterized. Each frame/render object is assigned to a layer.
* Textures are allocated for each layer of the page.
* The frame/render objects for each layer are traversed and drawing commands
  are executed for their respective layer. This may be rasterized by the CPU
  or drawn on the GPU directly using D2D/SkiaGL.
* All of the above steps may reuse calculated values from the last time the
  webpage was rendered, so that incremental changes require less work.
* The page layers are sent to the compositing process where they are combined
  with layers for other visible content like the browser chrome, iframes
  and addon panels.
* Final layer positions are computed and the composite commands are issued
  via Direct3D/OpenGL. The GPU command buffer(s) are flushed to the GPU for
  asynchronous rendering and the frame is sent to the window server.

GPU Rendering
-------------

* During the rendering process the graphical computing layers can use general
  purpose ``CPU`` or the graphical processor ``GPU`` as well.

* When using ``GPU`` for graphical rendering computations the graphical
  software layers split the task into multiple pieces, so it can take advantage
  of ``GPU`` massive parallelism for float point calculations required for
  the rendering process.


Window Server
-------------

Post-rendering and user-induced execution
-----------------------------------------

After rendering has been completed, the browser executes JavaScript code as a result
of some timing mechanism (such as a Google Doodle animation) or user
interaction (typing a query into the search box and receiving suggestions).
Plugins such as Flash or Java may execute as well, although not at this time on
the Google homepage. Scripts can cause additional network requests to be
performed, as well as modify the page or its layout, causing another round of
page rendering and painting.

.. _`Creative Commons Zero`: https://creativecommons.org/publicdomain/zero/1.0/
.. _`"CSS lexical and syntax grammar"`: http://www.w3.org/TR/CSS2/grammar.html
.. _`Punycode`: https://en.wikipedia.org/wiki/Punycode
.. _`Ethernet`: http://en.wikipedia.org/wiki/IEEE_802.3
.. _`WiFi`: https://en.wikipedia.org/wiki/IEEE_802.11
.. _`Cellular data network`: https://en.wikipedia.org/wiki/Cellular_data_communication_protocol
.. _`analog-to-digital converter`: https://en.wikipedia.org/wiki/Analog-to-digital_converter
.. _`network node`: https://en.wikipedia.org/wiki/Computer_network#Network_nodes
.. _`TCP congestion control`: https://en.wikipedia.org/wiki/TCP_congestion_control
.. _`cubic`: https://en.wikipedia.org/wiki/CUBIC_TCP
.. _`New Reno`: https://en.wikipedia.org/wiki/TCP_congestion_control#TCP_New_Reno
.. _`congestion window`: https://en.wikipedia.org/wiki/TCP_congestion_control#Congestion_window
.. _`maximum segment size`: https://en.wikipedia.org/wiki/Maximum_segment_size
.. _`varies by OS` : https://en.wikipedia.org/wiki/Hosts_%28file%29#Location_in_the_file_system
.. _`简体中文`: https://github.com/skyline75489/what-happens-when-zh_CN
.. _`한국어`: https://github.com/SantonyChoi/what-happens-when-KR
.. _`日本語`: https://github.com/tettttsuo/what-happens-when-JA
.. _`downgrade attack`: http://en.wikipedia.org/wiki/SSL_stripping
.. _`OSI Model`: https://en.wikipedia.org/wiki/OSI_model
.. _`Spanish`: https://github.com/gonzaleztroyano/what-happens-when-ES
