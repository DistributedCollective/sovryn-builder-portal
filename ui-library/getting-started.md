# Getting Started

### Installation

To install the [Sovryn UI Library](https://www.npmjs.com/package/@sovryn/ui) follow these steps:

1. Install [the package](https://www.npmjs.com/package/@sovryn/ui) via `npm` or `yarn`:

```bash
npm install @sovryn/ui
```

or

```bash
yarn add @sovryn/ui
```

2. Import the desired components from the Sovryn UI Library:

```jsx
import { Button, Form, Modal } from '@sovryn/ui';
```

### Basic Usage

The Sovryn UI Library provides a wide range of reusable UI components that can be easily integrated into your application. Here are a few examples of basic usage:

1. Using Buttons:

```jsx
import { Button, FormGroup, Dialog } from '@sovryn/ui';

const App = () => (
  <>
    <Button
      style={ButtonStyle.primary}
      text="Click Me"
    />
    <FormGroup>
      {/* Form fields */}
    </FormGroup>
    <Dialog isOpen={true}>
      {/* Dialog content */}
    </Modal>
  </>
);
```

2. Working with Inputs:

```jsx
import { AmountInput } from '@sovryn/ui';

const App = () => (
  <AmountInput
    label="Advanced settings"
    value="0.5"
    unit="%"
    className="max-w-full"
    decimalPrecision={2}
  />
);
```

### Helpful Links

To make the most of the UI Library and access additional resources, refer to the following helpful links:

1. Storybook: Explore the interactive Storybook, a playground showcasing the UI Library's components with live examples and configurations, allowing you to experiment and understand their behavior. [Sovryn Storybook](https://sovryn-storybook.netlify.app/?path=/story/atoms-accordion--default)
2. Sovryn-dapp: Monorepo that contains the UI Library, you can find all sorts of useful information there, including the overview of all the Sovryn packages. [Sovryn-dapp repository](https://github.com/DistributedCollective/Sovryn-dapp)
3.  Tailwind CSS: If you want to customize the styles of the Sovryn UI components or learn more about the underlying CSS utility classes, refer to the official Tailwind CSS documentation.

    [Tailwind CSS Documentation](https://tailwindcss.com/docs)

###
