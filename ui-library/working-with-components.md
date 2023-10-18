# Working with Components

## Component Development Guide

This document provides instructions on how to work with components, including implementation, composition, and testing. It also covers guidelines for writing unit tests, minimum requirements for tests, code structure style choices, and following the development team's design principles. Please follow the instructions below.

## Overview of Steps to Create a Component

To create a component, follow these steps:

1. Determine the purpose and functionality of the component.
2. Based on [the atomic design methodology](https://github.com/DistributedCollective/sovryn-dapp/wiki/Design), classify the component into one of the atomic design categories: atoms, molecules, organisms, templates, or pages.
3. Create a new directory for the component - <mark style="color:red;">`ComponentName`</mark>, using the appropriate atomic design category as the directory name.
4. Create necessary files for the component, such as the main component file, styling files (<mark style="color:red;">`ComponentName.module.css`</mark>), an optional utility <mark style="color:red;">`ComponentName.utils.ts`</mark> file, and a <mark style="color:red;">`ComponentName.types.ts`</mark> file for TypeScript type definitions.
5. Create an <mark style="color:red;">`index.ts`</mark> file within the component directory to export all relevant components, functions, and types from the component.
6. Ensure the component adheres to the development team's design principles and style choices (refer to the "_Style Choices for Code Structure_" section below).
7. Test the component (refer to the "_How to Write Unit Tests_" section below).
8. In the "_UI Library_" section of GitBook, provide concise documentation for the component's usage, API, and key implementation details. This ensures users have a clear understanding of how to use the component effectively in their projects.
9. If the component is reusable and can be extended, consider creating documentation on how to extend it.
10. Create a Storybook story for the component (refer to the "_Using Storybook_" section below).
11. To display the red asterisk for required props, for instance, `children` and show the prop type in Storybook, you can update the component's prop definition by adding a description using the JSDoc syntax.\


## Style Choices for Code Structure

To follow the Sovryn development team's [design principles and maintain code consistency](https://github.com/DistributedCollective/sovryn-dapp/wiki/Code-Style), adhere to the following style choices for code structure:

1. **Follow Coding Conventions**: Familiarize yourself with established conventions for naming, file organization, and code formatting.
2. **Use Meaningful Names**: Opt for descriptive names for variables, functions, and components.
3. **Break Down Complex Components**: Divide complex components into reusable sub-components with clear responsibilities. Store these sub-components in a separate `components` folder within the main component's directory. This promotes a modular and organized structure, facilitating better understanding, reusability, and maintenance. Each sub-component should serve a specific purpose and encapsulate distinct functions within the overall component.
4. **Keep Code Concise**: Avoid lengthy functions/components. Use smaller, focused units for better comprehension, testing, and maintenance.
5. **Maintain Proper Formatting**: Consistently apply indentation and formatting for improved readability.
6. **Document Code**: Use comments to explain the logic, provide context, and enhance collaboration.
7. **Styling**: For styling components, it is recommended to utilize separate <mark style="color:red;">`ComponentName.module.css`</mark> files to define component-specific styles. This practice ensures that only classes defined within the component are used, promoting encapsulation and preventing class name conflicts. However, developers can still incorporate Tailwind CSS styles by leveraging the **`@apply`** directive. You can refer to the provided documentation link for further details: [Styling Guidelines](https://github.com/DistributedCollective/sovryn-dapp/wiki/Code-Style#styling).\


## How to Write Unit Tests

Follow these guidelines when writing unit tests for components:

1. Create a separate test file for your component, typically named <mark style="color:red;">`ComponentName.test.tsx`</mark>.
2. Write individual test cases within the test file to cover different aspects of your component's functionality.
3. Utilize Jest's testing APIs, such as **`describe`**, **`test`**, **`expect`**, and others, to structure and assert the desired behavior of your component.
4. Run the tests using the Jest test runner with the command **`npm test`** or **`yarn test`**.
5. Review the test results in the terminal, and ensure that all tests pass <mark style="color:green;">successfully</mark>.
6. Tests should cover edge cases.
7. When testing components consisting of other components, you don't need to test the base functionality already covered in these child components.

By using Jest for unit testing, you can verify the correctness of your components, detect potential issues early on, and maintain a reliable codebase.\


### Minimum Requirements for Tests

To ensure code quality and minimize issues, adhere to the following minimum requirements for tests:

1. It is essential to ensure that every component has Jest tests in place.
2. Include tests for both positive and negative scenarios.
3. Cover the most critical and commonly used features of the component.
4. Update tests when modifying the component's behavior or functionality.
5. Regularly run the test suite during development and before committing changes.\


## Using Storybook

When creating a new component, it is recommended to use [Storybook](https://sovryn-storybook.netlify.app/), an interactive UI component development environment. Storybook allows you to develop and test components in isolation, providing a visual representation of each component's variations and states.\


To use Storybook for your component:

1. Create a new story file in your component's directory, typically named <mark style="color:red;">`ComponentName.stories.tsx`</mark>.
2. Import and define the stories for your component using Storybook's API. Each story represents a different variation or state of the component.
3. Within the story definition, add a property called **`argTypes`** and assign it an object. Each key in this object should correspond to a prop name, and the value should be an object containing the description for that prop. For example:

```javascript
argTypes: {
  prop1: {
    description: 'Description of prop1',
  },
  prop2: {
    description: 'Description of prop2',
  },
  // Add more prop descriptions as needed
}
```

Replace **`prop1`** and **`prop2`** with the actual names of your props, and provide the corresponding descriptions for each prop.



## Design Aids

* [Design Principles](../design-system/design-principles.md)
