---
description: Work with Components
---

# Components

## Component Development Guide

This document provides instructions on how to work with components, including implementation, composition, and testing. It also covers guidelines for writing unit tests, minimum requirements for tests, code structure style choices, and following the development team's design principles. Please follow the instructions below.

## Overview of Steps to Create a Component

To create a component, follow these steps:

1. Determine the purpose and functionality of the component.
2. Based on the atomic design methodology, classify the component into one of the atomic design categories: atoms, molecules, organisms, templates, or pages.
3. Create a new directory for the component - `ComponentName`, using the appropriate atomic design category as the directory name.
4. Create necessary files for the component, such as the main component file, styling files (`ComponentName.module.css`), an optional utility `ComponentName.utils.ts` file, and a ComponentName.types.ts file for TypeScript type definitions.
5. Create an `index.ts` file within the component directory to export all relevant components, functions, and types from the component.
6. Implement the component's functionality according to the project requirements.
7. Ensure the component adheres to the development team's design principles and style choices (refer to the "_Style Choices for Code Structure_" section below).
8. Test the component (refer to the "_How to Write Unit Tests_" section below).
9. Document the component's usage, API, and any important implementation details.
10. If the component is reusable and can be extended, consider creating documentation on how to extend it.
11. Create a Storybook story for the component (refer to the "_Using Storybook_" section below).\


## Style Choices for Code Structure

To follow the Sovryn development team's [design principles and maintain code consistency](https://sovryn.monday.com/docs/2126716352), adhere to the following style choices for code structure:

1. **Follow Coding Conventions**: Familiarize yourself with established conventions for naming, file organization, and code formatting.
2. **Use Meaningful Names**: Opt for descriptive names for variables, functions, and components.
3. **Break Down Complex Components**: Divide complex components into reusable sub-components with clear responsibilities. Store these sub-components in a separate `components` folder within the main component's directory. This promotes a modular and organized structure, facilitating better understanding, reusability, and maintenance. Each sub-component should serve a specific purpose and encapsulate distinct functions within the overall component.
4. **Keep Code Concise**: Avoid lengthy functions/components. Use smaller, focused units for better comprehension, testing, and maintenance.
5. **Maintain Proper Formatting**: Consistently apply indentation and formatting for improved readability.
6. **Document Code**: Use comments to explain the logic, provide context, and enhance collaboration.
7. **Styling**: When styling components, use separate `ComponentName.module.css` files to define the component-specific styles. This approach allows for scoped styles, preventing class name conflicts and promoting encapsulation.
8. **Utilize Tailwind CSS**: When necessary, leverage the utility classes provided by Tailwind CSS to complement your component-specific styles defined in `ComponentName.module.css` files. This combination enables efficient and responsive styling.\


## How to Write Unit Tests

Follow these guidelines when writing unit tests for components:

1. Create a separate test file for your component, typically named `ComponentName.test.tsx`.
2. Write individual test cases within the test file to cover different aspects of your component's functionality.
3. Utilize Jest's testing APIs, such as **describe**, **test**, **expect**, and others, to structure and assert the desired behavior of your component.
4. Run the tests using the Jest test runner with the command **npm test** or **yarn test**.
5. Review the test results in the terminal, and ensure that all tests pass successfully.

By using Jest for unit testing, you can verify the correctness of your components, detect potential issues early on, and maintain a reliable codebase.\


### Minimum Requirements for Tests

To ensure code quality and minimize issues, adhere to the following minimum requirements for tests:

1. Aim for minimum test coverage of 80% for your components.
2. Include tests for both positive and negative scenarios.
3. Cover the most critical and commonly used features of the component.
4. Update tests when modifying the component's behavior or functionality.
5. Regularly run the test suite during development and before committing changes.\


## Using Storybook

When creating a new component, it is recommended to use Storybook, an interactive UI component development environment. Storybook allows you to develop and test components in isolation, providing a visual representation of each component's variations and states.\


To use Storybook for your component:

1. Create a new story file in your component's directory, typically named `ComponentName.stories.tsx`.
2. Import and define the stories for your component using Storybook's API. Each story represents a different variation or state of the component.\


## Design Aids

* [SOVRYN UI Library](https://www.figma.com/file/Ig2ZfR16Svs8In7yibukrO/Sovryn-UI-Library?type=design\&t=V7QGo0NCIsufEREH-0)
