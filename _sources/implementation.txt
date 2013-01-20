Implementation
==============

.. todo::
    1. Abstraction - implemented using extensions, passable into apps
        a. utilities (_, lodash)
        b. dom (jQuery, ender, etc)
        c. mvc (backbone, marionette)


History
-------

Originally coined `backbone-aura`, the code and API of aura has undergone
many reiterations and has, at least in theory, became agnostic from
backbone.

In the first iterations of Aura, a single sandbox, or instance of the Aura
app would control multiple widgets on a page. The widgets would inherit
`dom`, an abstraction of jQuery / zepto by the facade and use the mediator
as pubsub between widgets.

The behavior has since been fundamentally changed. Today:
  - before: aura termed `sandbox` as the instance of the aura app itself,
    now: every widget is a sandbox with its own context.
  - before: aura extensibility was cloudy
    now: aura has a clear, concise and tested extension architecture
  - permissions has been removed from core
  - `EventEmitter2` pubsub from hard coding
  - bower for browser libraries
  - jasmine -> mocha unit test
  - basic `code standards`_, `contribution guidelines`_, etc.

Overview
--------

Aura

`EventEmitter2`_ is the default pubsub
system. Permissions has been removed from core and will come back as an
extension.

Application Core
----------------

The Core has a number of responsibilities. Powered by the Mediator pattern,
it:

**Provides the ability to manage a widget's lifecycle (start, stop, cleanup)**.

    This is powered by work we've done to expand on top of RequireJS 2.0's `undef`
    feature for unloading modules. Aura works around RequireJS's limitation of
    not being able to resolve a module's dependencies to allow the easy
    unloading of an entire widget. Unloading a widget equates to removing it
    from the RequireJS caches, deleting instance references to them (which can
    lower memory) and of course, cleaning up any DOM elements the widget was
    using.

**Implements aliases for DOM manipulation, templating and other lower-level utilities that pipe back to a library of choice**.

    The idea here is that rather than interfacing with the libraries directly,
    accessing the Core aliases (through the Sandbox) allow developers to
    switch out the libraries they use at a later date with minimum impact to
    their application. We currently provide a bare-bones implementation of the
    jQuery library.

    The core uses AMD modules requiring dependencies to Aura's facade,
    which abstracts DOM libraries like jQuery so they can be later
    switched out with a smaller or faster library in the future.



**Exposes Publish/Subscribe functionality that can be used for decoupled communication between parts of an application**.

    Similar to the concept above, our Pub/Sub implementation can be easily
    replaced with that of another library and it should still work fine.


Sandbox
-------

Powered by the Facade pattern, the Sandbox:

**Provides a limited, lightweight API layer on top of the Core for the rest of an application to communicate through**.

    Rather than exposing say, the entire API for a JavaScript library, we
    instead only expose those parts that developers in the project will
    need or are safe to use. This is particularly useful when working in
    teams.

    The Sandbox includes a permissions layer, allowing you to configure
    permissions for widgets such as whether a specific widget has the right
    to render to the page etc.

Modules
-------

**All of the files and demo widgets in Aura use AMD as their module format of choice**

    These can of course be used with r.js for compilation and optimization
    if concerned about too many script files (compilation should always be
    used for production-level apps if using AMD in any situation)

    Whilst not an Aura feature, we also take advantage of RequireJS 2.0's
    `shim` capability to avoid the need to use patched versions of
    Backbone.js and Underscore.js (a concern with earlier versions of the
    project).

Widgets
-------

A Widget represents a complete *unit* of a page. It could be a calendar,
a news block, a todo list or anything else.

**In Backbone.js terms, widgets are composed of Models, Views, Collections and Routers as well as any templates needed for the widget to be rendered.**

    Widgets should be developed such that any number of instances of them
    could exist on a page in harmony.

**Publish/Subscribe can be used to communicate between widgets**.

    Alternatively, direct communication (as demonstrated by the `controls`
    widget in our examples) may be done, however this is discouraged where
    Pub/Sub can be used instead.

Sample Application
------------------

A demo application using Aura is included in the download featuring
Calendar, Todo list and control widgets. After you complete **Install &
Build section** (see below), run `grunt` to launch web server on
`http://localhost:8897` and go to the `src` directory to try out the demo
app.

.. _EventEmitter2: https://github.com/hij1nx/EventEmitter2
.. _code standards: https://github.com/aurajs/aura/wiki/Coding-Standards
.. _contribution guidelines: https://github.com/aurajs/aura/blob/master/contributing.md
