# Proposed Workflow for TDD on console

This document is based mainly on the following articles/documents:

* [Storybook visual testing handbook](https://storybook.js.org/tutorials/visual-testing-handbook)
* [The delightful storybook  - chromatic](https://www.chromatic.com/blog/the-delightful-storybook-workflow/)
* [Component driven](https://www.componentdriven.org/)
* [UI testing playbook](https://storybook.js.org/blog/ui-testing-playbook/)
* [Stories are tests](https://storybook.js.org/blog/stories-are-tests/)
* [How to actually test UIs](https://storybook.js.org/blog/how-to-actually-test-uis/)

## Aim

### What it is

Set out how we approach building UIs at Hasura using TDD
How we approach testing in a way that allows us to release things regularly that are well tested but don't have tests that are difficult to maintain and slow us down
Act as a guide for the workflow someone should follow when adding/upgrading a UI component/feature

### What it's not

A "this is the correct way to do things"
Set in stone, disagree, update, improve etc..

## Overall Philosophy

* Component driven design
* TDD using visual testing as primary testing mechanism
* Use stories as main tests (isolate components and write stories to simulate as many states as feasible)
* Only use testing library to verify difficult to reach states don't overuse
* Build up stories from small components into larger composed components and pages, each with simulated states and data mocked using msw
* Chromatic to automate visual testing process (pick up regressions etc).
* Limit user-flow testing (cypress) sparingly and only to test key user flows

## Workflow

### Creating new components

* use code-gen
* start with smallest components and build to largest

### Build components in storybooks

* use TDD with storybooks
* stories should be the main tests
* only write unit tests for difficult to reach states

### Collaboration

* Use chromatic to automate visual testing for regressions and changes
* Multiple developers check changes, use deployed storybook for single source of truth

### Full workflow example

Developing

-- create a component
-- create a story
-- build simplest implementation
-- check in storybooks including accessibility
-- iterate and create additional states

-- if the component has some difficult to reach states that might be difficult to snapshot maybe create some testing library tests 

Collaboration and testing

-- deploy storybook
-- run chromatic
-- check against baselines get others to check and comment
