# Sovryn UI Library Usage

We use the UI library as a basis for all UX components in sovryn.app, with few exceptions. General guidelines we follow when using the UI Library:

* Keep all components unopinionated in UI Library - don't hardcode things and keep business logic in our dapp code
* Reuse components as much as possible - minimal changes should be applied in dapp code
* When new functionality is needed in dapp, and can be generalized - build it into the UI library component in a way that can be extended by other users
* dapp specific UI components follow same design methodology as UI library - Atomic Design: modular as possible for greater reuse
* Follow brand and design guidelines from design system
