# Proposed Workflow for TDD on console

## Purpose

Outline the ['golden path'](https://engineering.atspotify.com/2020/08/17/how-we-use-golden-paths-to-solve-fragmentation-in-our-software-ecosystem/) for creating a new feature or component in the console ui
Provide step by step instuctions for how to create and test a new feature or component
Describe in detail how we approach building UIs at Hasura using Test driven development
what is a feature...
what is a component...

Shouldn't be considered 'set in stone', comments cristisms etc welcome..

## Audience

- Someone creating a new component or feature for the first time
- Also should be useful point of reference for everyone else
- Therefore, contains step by step and could seem overly exhuastive, but designed to be through in included every step of the development process

## Overall Philosophy 

* **Component Driven Design** - build up stories from small components into larger composed components and pages, each with simulated states and data mocked using `msw`
* **Stories are tests** - Use stories as main tests (isolate components and write stories to simulate as many states as feasible)
* **Use unit tests sparingly** Only use testing library to verify difficult to reach states - don't overuse
* **Automate visual testing workflow** - Chromatic to automate visual testing process (pick up regressions etc).
* **Limit user-flow testing** - Limit user-flow testing (cypress) sparingly and only to test key user flows

## Workflow

The workflow we use will lean on three aspects (elaborated below):

1. **Code gen**: Using code gen tools to create a consistent pattern of folders, files, and tests
2. **Component drvien design**: Building using a "component driven approach", starting with smaller components and building up to larger components and pages
3. **Collaboration**: Following a visual testing strategy that uses chromatic to automatically catch changes and capture snapshots and then reviewing these as a team online.

### Creating new components/features

**1. Code gen**

* When creating new components we should use the repositories in build codegen tools, this scaffolds the relevant files and folders for the new component or feature complete with stories and tests.

// TODO example to follow when tool is merged

* When the tool is run a file structure similar to this will be generated:

```
src/features/awesome-feature
|
+-- api         # exported API request declarations and api hooks related to a specific feature
|
+-- assets      # assets folder can contain all the static files for a specific feature
|
+-- components  # components scoped to a specific feature
|
|        MyComponent.tsx (no index.ts in here)
|
+-- hooks       # hooks scoped to a specific feature
|
+-- stores      # state stores for a specific feature
|
+-- types       # typescript types for ta specific feature domain
|
+-- utils       # utility functions for a specific feature
|
+-- index.ts    # entry point for the feature, it should serve as the public API of the given feature and exports everything that should be used outside the feature
```

**2. Build components in storybooks**

* Once the new files and been scaffolded using code gen tools the new feature should be build out and tested in storybooks.
* We should follow a "Component Driven Design" approach. This generally means 2 things:
  1. Start with the smallest components and build up to larger components and pages composed of the smaller components
  2. Stories are tests! When creating new components we should be following a Test Driven Development pattern, this doesn't neccessarily mean writing tests with `jest`/`react-testing-library` for every component. By following a iterative process and checking story output as we go we are naturally following Test Driven Development - our visual testing is the verify step.

From Storybook's visual testing handbook:
```
test
  setup
  execute 👈 Storybook renders stories
  verify 👈 you look at stories
  teardown
end
```

* We should only add tests using `react-testing-library` where components are particularly complicated inorder to get the component into a hard to reach state.
* As the components increase in size/complexity they should be composed from smalled components that have already been tested and any data they need mocked using `msw` (mock service worker)
* Larger components/pages should handle there own data flow using only the minimium number of props required to fetch that data with data fetching hooks that use `react-query` under the hook. (Again the data lifecycle can be mocked using `msw`).
* Userflow testing with Cypress should be limited to key user paths and be used sparingly.

**3. Collaboration**

* Use chromatic to automate visual testing for regressions and changes
* Multiple developers check changes, and approve or reject depending on output

### Step by step

Step by step guide aimed at developers creating a feature for the first time, 'golden path' for creating new features with Test Driven Development.
The guide is to create a Form feature, this also includes use of some of the librarys and components we use such as 'react-hook-form'

##### Create a feature

* Creating a new a feature: a form component

* Start by using code-gen 

```bash
example of code gen feature
```

##### Developing a new feature

* Once the code generation has completed there will be a new feature with the following file layout:

```
src/features/ExampleForm
|
+-- api         # exported API request declarations and api hooks related to a specific feature
|
+-- assets      # assets folder can contain all the static files for a specific feature
|
+-- components  # components scoped to a specific feature
|
|        MyComponent.tsx (no index.ts in here)
|
+-- hooks       # hooks scoped to a specific feature
|
+-- stores      # state stores for a specific feature
|
+-- types       # typescript types for ta specific feature domain
|
+-- utils       # utility functions for a specific feature
|
+-- index.ts    # entry point for the feature, it should serve as the public API of the given feature and exports everything that should be used outside the feature
```

* We are using Component Driven Design so will create small components and then build up to more complex components and the page as a whole.
* All sections will be mocked in Storybook including api requests

<hr />

1). Run storybook `npm run storybook` and go to `http://localhost:6006`

2). Create a new component in the components folder. The component is going to be a simple collection of user input fields

```ts
// components/OnOff.tsx
import React from 'react';
import { useFormContext } from 'react-hook-form';

export interface OnOffProps {
    disabled?: boolean;
}

export const OnOff = ({ disabled = false }: OnOffProps) => {
  const { register } = useFormContext();
  
  return (
     <label>
         <input
             type="checkbox"
             disabled={disabled}
             {/* register component with react hook form */}
             {...register('onOff')}
          />
          <p>
            Turn on or off
          </p>
     </label>
  );
}
```

3). Create a new story along side the component

```
// components/OnOff.stories.tsx

import React from 'react';
import { Story, Meta } from '@storybook/react';

import { OnOff, OnOffProps } from './BackendOnly';



```

Developing

-- create a component
-- create a story
-- build simplest implementation
-- check in storybooks including accessibility
-- iterate and create additional states

-- if the component has some difficult to reach states that might be difficult to snapshot maybe create some testing library tests 

#### Collaboration and testing

-- deploy storybook
-- run chromatic
-- check against baselines get others to check and comment

### Refs:

This document is based mainly on the following articles/documents:

##### Creating and testing UIs
* [Storybook visual testing handbook](https://storybook.js.org/tutorials/visual-testing-handbook)
* [The delightful storybook  - chromatic](https://www.chromatic.com/blog/the-delightful-storybook-workflow/)
* [Component driven](https://www.componentdriven.org/)
* [UI testing playbook](https://storybook.js.org/blog/ui-testing-playbook/)
* [Stories are tests](https://storybook.js.org/blog/stories-are-tests/)
* [How to actually test UIs](https://storybook.js.org/blog/how-to-actually-test-uis/)

##### Living documents
* [Rethinking docs](https://kathykorevec.medium.com/building-a-better-place-for-docs-197f92765409)

##### Golden Paths
* [Spotify: the golden path](https://engineering.atspotify.com/2020/08/17/how-we-use-golden-paths-to-solve-fragmentation-in-our-software-ecosystem/)
