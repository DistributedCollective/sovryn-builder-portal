# Design

## Atomic Design

https://atomicdesign.bradfrost.com/chapter-2/

* Atoms - Simple standalone components, that are completely controlled from the outside and do not cause side effects. These atoms include basic HTML elements like form labels, inputs, buttons, and others that can’t be broken down any further without ceasing to be functional.
* Molecules - Simple standalone components, that can use other atoms or molecules and are completely controlled from the outside and do not cause side effects. They do one function and do it well.
* Organisms - Complex components that depend on other components and can include controlling logic and side effects. They form a standalone section of an interface.
* Templates - Layout components that connect multiple specific or dynamic components. Can be used for pages with same layout but different components and/or content.
* Pages - Page components that are reachable by their own route.

## Sovryn UI Library

Figma file with latest component designs and guidelines: https://www.figma.com/file/Ig2ZfR16Svs8In7yibukrO/Sovryn-UI-Library?t=iKL99z9G7GZDHNOV-0

Latest development build of Storybook: https://dev--sovryn-storybook.netlify.app/
