# Code Style

## Cleanliness

| Name                | Rule                                                                                                                                                                                                                                                                                                                                                     |
| ------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Dead Code           | Remove old and unused code. Don't just create the replacement, also cleanup the replaced and check for newly unused assets. If something should specifically not be removed it should be marked with a comment.                                                                                                                                          |
| Out Commented Code  | Remove out commented code before merging. If something should be specifically not be removed it should be marked with a comment.                                                                                                                                                                                                                         |
| ESLint Warnings     | Fix ESLint Warnings before merging. If you are sure the warning is wrong you can use eslint-ignore comments, but you need to specify your reasoning with an adjacent comment. Failure to resolve warnings will prevent builds from being deployed to production environments.                                                                            |
| Comments            | <p>Comments should provide value by explaining the reasoning for the code or complex parts. If the comment does not, remove it!<br><br>JSDoc style comments (/** description */) can be used to add valuable information to functions/classes/types/properties/constants, e.g. the type may be string, but its value is expected to be a wei string.</p> |
| console.log         | Remove console.log (warn, info, error) calls, that were intended for development/debugging. console.error calls are acceptable where used in exception handling.                                                                                                                                                                                         |
| Breaking Changes    | When you change a component/function/class in a way that changes it's usage, check the whole project for usages and update them. Compile-time warnings will indicate direct violations of this, but diligence is advised when making changes that may have unintended side effects.                                                                      |
| Inline Hard-Coding  | Avoid using inline defined constants. Define a named constant instead. Instead of iterating until you reach 5, iterate until MAX\_ENTRY\_COUNT.                                                                                                                                                                                                          |
| Magic Values        | Define Enums for function parameters, instead of defining it as number/string and expecting specific values.                                                                                                                                                                                                                                             |
| Named Exports       | Use named exports.                                                                                                                                                                                                                                                                                                                                       |
| No Wildcard Imports | Never use wildcard imports.                                                                                                                                                                                                                                                                                                                              |
| Generalize          | Use existing functions and components that fit your use. Extract embedded code to be used in multiple locations. Try to create functions and components in a generalized way, so it can be used in other places.                                                                                                                                         |
| No inline ifs       | Do not use inline ifs.                                                                                                                                                                                                                                                                                                                                   |

## Naming

| Name                                              | Rule                                                                                                    | Example                                                                                                                                           |
| ------------------------------------------------- | ------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| Global/File Constants                             | UPPERCASE                                                                                               | CONSTANTS\_ARE\_UNCHANGEABLE                                                                                                                      |
| Variables, Local Constants, Parameters, Functions | camelCase                                                                                               | variableNames, functionNames                                                                                                                      |
| Components, Classes, Interfaces, Types, Enums     | UpperCamelCase                                                                                          | ComponentsAndClass, InterfacesTypesAndEnums                                                                                                       |
| Enum keys                                         | camelCase                                                                                               | keyCase                                                                                                                                           |
| Module CSS class names                            | camelCase or kebab-case                                                                                 | .inputWrapper tw-text-primary-dark                                                                                                                |
| Readable Names                                    | Use appropriate full names and not abbreviations                                                        |                                                                                                                                                   |
| Boolean Variables                                 | Boolean variable names should indicate their boolean nature, expect for component props.                | isValid, hasRendered, shouldReload                                                                                                                |
| React Hooks                                       | Use the prefix “use”, but only if it is an actual React Hook (function with state/memoization/caching). | useCalculator                                                                                                                                     |
| HOC (Higher Order Components)                     | Use the prefix “with”.                                                                                  | withTransaction                                                                                                                                   |
| React.Context                                     | Use the postfix "Context" for React.Context instances.                                                  | WalletContext                                                                                                                                     |
| Data Attribute Prop                               | "dataAttribute"                                                                                         | Any prop for applying UI data attributes should be named dataAttribute to avoid having to adjust this in future (previously we used dataLayoutId) |

## Typescript

| Name               | Rule                                                                                                                                                              |
| ------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Use Types          | Always define types for function parameters, props and uninitialized variables. Never use any, unknown, undefined for something where you expect a specific type. |
| Avoid Any          | Only use the type "any" if you actually intend it to be of any type.                                                                                              |
| Avoid Unknown      | Only use type “unknown” if the type is actually unknown.                                                                                                          |
| Undefined & Null   | Undefined is meant to mean not (yet) set, while null is to be used as an intentional way of signifying that it's a NULL value.                                    |
| Object Definitions | Use named type definition for object types with attributes.                                                                                                       |
| Generics           | Use generics for functions and classes, when a type can vary and especially if the output type is dependent on the input type.                                    |
| Export             | Only export types when necessary.                                                                                                                                 |
| Type Checking      | Use type predicates to check for object types, especially for external data.                                                                                      |

## React

* Use functional components with arrow functions and type React.FC
* Imports should be separated into two blocks: external dependencies first, followed by internal imports
* If the component has props, define them as an typescript type above named “ComponentNameProps”, or where an interface is necessary then it should be prefixed with an "I" (capital I), e.g. “IComponentNameProps”.
* De-structure props in function header. Do not use defaultProps.
* Do not declare sub-components within another function.
* use forwardRef for interactive components and components that wraps interactive component. Expose to ref primary element if there is multiple of them (ex. on dropdown which has trigger button and buttons as dropdown items, forwardRef of trigger button)
* Use correct component code order:
  * state and data hook calls, e.g. useState, useContext, useTranslation
  * functional hook calls, e.g. useCallback, useMemo, useEffect, useLayoutEffect, …
  * define and return jsx output
* Where data attribute props are needed, use naming as described in table above

## NPM Packages

When adding a new package, don't use `^` or `~`, in order to have 100% control over versions of packages.

## GraphQL Queries

Use only queries generated by graphql-codegen.

Sometimes, you may have a problem when calling the same hook/query multiple times (for instance if you want to query historic and current data and compare them) that the result is still the same, even though you're calling it with the right parameters. In this case, try to disable caching for that particular query. See https://www.apollographql.com/docs/react/data/queries/#setting-a-fetch-policy for more details.

### Naming

* Use the following naming patter - `get[entityName].graphql` (e.g. getLimitOrderCreated.graphql).
* If a query is used only for a specific feature (e.g. Treasury) and will never be used for anything else, we can use the following pattern - `get[featurePart].graphql` (e.g. getTreasuryDashboardData).
* If it's only used for a specific part for now but can be used somewhere else in the future, we can name it without the feature name, e.g. `getDashboardData`.

### Schemas/generated typescript definition files for multiple environments

* `ApolloProvider` can handle only one client URI so yes but there are multiple options for how to achieve it - one of them is to use multiple `ApolloProvider` instances as we do in the current dapp (for perps). Another one is to pass a custom `client` to a `query`.
* You can read more about this problem in [this article](https://medium.com/open-graphql/apollo-multiple-clients-with-react-b34b571210a5%60), however, we decided to pass a custom `client` to queries if needed (if it presents a significant overhead, there is no problem to use multiple `ApolloProvider` instances in the future).
* In the future, we'll have multiple subgraphs - one for Sovryn Core Protocol (AMMs, Margin trading, Governance/Vesting/Staking), one for Babelfish and one for Zero. It's not 100% sure we won't have even more subgraphs but it's not a big problem as we'll have queries (operations), schemas and generated queries in separate folders and we can adjust `codegen.yml` and `codegen.fetch.yml` to handle multiple schemas.
* PR example with multiple subgraphs configuration: https://github.com/DistributedCollective/sovryn-dapp/pull/99

### Batching queries to handle large requests (current max limit of 1000)

* Batching of Apollo requests is not something that is usually needed and recommended - for more details see https://www.apollographql.com/blog/apollo-client/performance/batching-client-graphql-queries/ .
* The majority of our requests will fall into following 2 categories:
  * Requests that need paginating - we don’t need to handle batching here as we’ll retrieve only a small subset of items. If we will need to query all of the pages at once, it’s possible by defining a [field policy](https://www.apollographql.com/docs/react/pagination/core-api/#defining-a-field-policy).
  * Requests that can be optimized on the server - e.g. sum of an event. In this case, it’s much better to have some aggregate object that already contains everything summed up so we don’t need to handle that on FE side.
  * If there is some other type of request, it will need to be handled on case-to-case basis (discuss with Subgraph owner).

## Styling

* Use tailwind classes, either directly passed as string in className (expect for ui component package), or used with @apply in .module.css files
  * When you require the element to accept a styling from the outside, use a module.css class plus the props.className, as otherwise the tailwind classNames conflict with each other.
  * use only classes defined in .module.css when creating components for ui library (see: https://sovryn.monday.com/docs/2733202752?blockId=aebe8f4b-5e4a-4ef6-8967-503ce7a1bb30\&blockId=bda294cc-3a5c-4cbd-a10a-7d682bc53030).
* use the classNames utility function to combine classNames
* never create tailwind class with string concatinations or similar, as this breaks tailwinds automatic unused class removal. (Can result in the required classes not being there after building.)
* Make sure to not use open css selector, always try to use the direct children `>` modifier or use custom classNames. This is especially important on any component that accepts children or ReactNode props.
* Components should follow our Design Guidelines

### Component stylings

Use only custom classes from css modules for components:

Wrong usage:

```tsx
// Comp.tsx
const Comp = () => <p className="p-3">example</p>
```

Correct usage:

```css
/* Comp.module.css */
.custom {
  @apply p-3;
}
```

```tsx
// Comp.tsx
import styles from './Comp.module.css';
const Comp = () => <p className={styles.custom}>example</p>
```

Using tailwindcss classname directly on the component would work on development but not on production bundle imported from npm to the app without extra configuration on the app.

Bundling these classes also would mean on bigger bundle size and likehood of ui library overwriting classes defined in the app.

## Localisations (Translations)

* Each page, complex component, or other logical group of code, should use a separate entry in `apps/frontend/src/locales/en/translation.json`, only the most basic texts should be translated via translations.common.
* Values should not be hard-coded into the translations, but rather be provided in the t function for [interpolation](https://www.i18next.com/translation-function/interpolation).
* Use [plurals](https://www.i18next.com/translation-function/plurals) when using numerals and [context](https://www.i18next.com/translation-function/context) with translations that are dependent on some component state.
* Use [Trans Component](https://react.i18next.com/latest/trans-component) with components attribute for translations that need html element or components within the text.
* DO NOT use hard-coded text.
* DO NOT use translation nesting.
* Text copy should follow Sovryn's Text Style Guide.

## Number Localisations

## Formatting

General formatting is defined, applied and enforced by [prettier](https://prettier.io/).

.prettierrc

```json
{
  "printWidth": 80,
  "tabWidth": 2,
  "useTabs": false,
  "semi": true,
  "singleQuote": true,
  "trailingComma": "all",
  "arrowParens": "avoid",
  "endOfLine": "lf",
  "importOrder": [
    "./wdyr",
    "^react$",
    "^(?!.*app/)\\w",
    "^(@sovryn)(/.*|$)",
    "./config/chains",
    "^app/",
    "^[./|~/]"
  ],
  "importOrderSeparation": true,
  "overrides": [
    {
      "files": ["apps/frontend/src/locales/**/*.json"],
      "options": {
        "tabWidth": 4
      }
    }
  ]
}
```

The last two properties in `importOrder` make use of the `prettier-plugin-sort-imports` plugin to organize package imports within our source files.

## File Structure

### Project Structure

Within `apps/frontend`:

* `src/`
  * `__tests__/`
  * `app/`
    * `0_meta` - Standalone utility components, that have no visible effect but provide functionality or data to parent and/or child components, e.g. Provider, HOC, Portal, …
    * `1_atoms/` - Simple standalone components, that are completely controlled from the outside and do not cause side effects.
    * `2_molecules/` - Simple standalone components, that can use other atoms or molecules and are completely controlled from the outside and do not cause side effects.
    * `3_organisms/` - Complex components that depend on other components and can include controlling logic and side effects.
    * `4_templates/` - Layout components that connect multiple specific or dynamic components. Can be used for pages with same layout but different components and/or content.
    * `5_pages/` - Page components that are reachable by an own route.
  * `assets/` - Images or other assets.
  * `config/` - Configuration data objects.
  * `contexts/` - React Contexts.
  * `hooks/` - Custom react hooks.
  * `lib/` - Connector libraries.
  * `locales/` - Localizations.
  * `store/` - Global state stores.
  * `styles/` - Global styling.
  * `types/` - Global types.
  * `utils/` - Utility logic and GraphQL queries.

Within `packages/ui`:

* `src/`
  * `0_meta` - Standalone utility components, that have no visible effect but provide functionality or data to parent and/or child components, e.g. Provider, HOC, Portal, …
  * `1_atoms/` - Simple standalone components, that are completely controlled from the outside and do not cause side effects.
  * `2_molecules/` - Simple standalone components, that can use other atoms or molecules and are completely controlled from the outside and do not cause side effects.
  * `3_organisms/` - Complex components that depend on other components and can include controlling logic and side effects.
  * `4_templates/` - Layout components that connect multiple specific or dynamic components. Can be used for components with same layout but different components and/or content.
  * `hooks/` - Custom react hooks.
  * `types/` - Global types.
  * `utils/` - Utility logic and GraphQL queries.

### Component Structure

* `ComponentName/`
  * `ComponentName.tsx` - Contains and exports main component, named `ComponentName`.
  * `ComponentName.module.css` - optional - Contains custom CSS styles for the main component. Should only be used when tailwind is not enough.
  * `ComponentName.stories.tsx` - Contains storybook examples. Only needed for atoms, molecules and organisms.
  * `ComponentName.test.tsx` - Contains Jest tests. Each component should have at least some basic test coverage so we're sure it's working the way is supposed to even after refactorings.
  * `ComponentName.utils.ts` or `utils.ts`- optional - Contains component specific helper functions. May be used by sub-components.
  * `ComponentName.types.ts` or `types.ts` - optional - Contains component enums/types/interfaces (the only exception that doesn't belong there is a component's interface/type). May be used by sub-components. May also be placed into `types/` folder if multiple types files are required.
  * `hooks/` - optional - Contains component specific hooks. May be used by sub-components.
  * `components/` - optional - Contains sub-component, that are only used by the parent component. Sub-components should follow the same Component Structure.
