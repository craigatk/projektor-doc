---
title: 'AI test failure analysis'
date: 2025-03-05T19:30:08+10:00
draft: false
weight: 7
summary: AI test failure analysis
---

Fix your failing tests faster by getting hints as to the cause of your failing tests with AI assistance.
Projektor supports analyzing test failures with ChatGPT's help to give insights into possible causes of the test
failure.

## Test failure analysis

For example, in this failing Cypress browser test:

```
AssertionError: Timed out retrying after 5000ms: Expected to find element: `[role="dot-duration-8UNFGPZIGCHD"]`, but never found it.
    at Context.eval (webpack://functional-test/../cypress-common/support/commands.js:73:7)
    at wrapped (http://localhost:8080/__cypress/runner/cypress_runner.js:137189:43)
```

Clicking the "Analyze Failure" button will analyze the test failure:

![Failing Cypress test](/images/ai/cypress-failing-test-ai.png "Failing Cypress test")

And yield an analysis such as this:

```
The error message you're encountering indicates that a test in a Cypress-based test suite has failed due to a timeout when trying to find an element with the specified selector `[role="dot-duration-8UNFGPZIGCHD"]`. Here are some possible reasons why this test may have failed and some suggestions for troubleshooting:

1. **Element Not Present**:
- The most likely reason is that the element with the role `dot-duration-8UNFGPZIGCHD` does not exist in the DOM at the time the test is executed. This could be due to changes in the application’s state that prevent the element from rendering.

2. **Timing Issue**:
- The element may not be present yet because it takes longer than expected to load. This could be due to slow network conditions, slow server response, or heavy client-side processing. Consider increasing the timeout or using `cy.wait()` to pause the test before checking for the element.

3. **State of the Application**:
- Ensure that the application is in the correct state to display the element you’re testing. If this element is dependent on prior actions or specific states, verify they have been completed successfully.

4. **Selector Issues**:
- The selector `[role="dot-duration-8UNFGPZIGCHD"]` might be incorrect. Double-check the role attribute in the rendered application. If the role or any part of the selector is dynamic (changing on each render), your test may need to be updated accordingly.

5. **Visibility of Element**:
- The element might be present but not visible. Make sure that it is not hidden by CSS or conditional rendering.

6. **Test Environment**:
- Verify if the test is running in the correct environment and that all dependencies (like services the application relies on) are functioning.

7. **Error in the Application Code**:
- There might be a bug in the application that is preventing the element from rendering. Check the application logs or run the application manually to ensure there are no runtime errors.

### Suggestions for Resolution

- **Debugging**: Use `cy.debug()` or `cy.log()` to output useful information just before the assertion to understand the current state of the application and what’s in the DOM.

- **Increase Timeout**: If timing is an issue, consider increasing the default timeout for finding the element:
javascript
cy.get('[role="dot-duration-8UNFGPZIGCHD"]', { timeout: 10000 }).should('exist');


- **Add Waiting Mechanisms**: If there are asynchronous operations that load the element, consider using appropriate commands or waiting strategies:
javascript
cy.wait(2000); // For demonstration, not a best practice. Prefer to wait for specific conditions.


By investigating these areas, you should be able to identify why the test failed and take the necessary steps to correct it.
```

While AI can't immediately solve all of your failing tests for you, it can help with ideas on next steps.

This analysis yields some helpful hints, such as:

* Recommending adding `cy.debug()` or `cy.log()` to get more detailed information about the failure
* Recommended adding a longer timeout for the specific element to appear
* Recommended adding a wait, but also called out a static wait time isn't a great idea - wait for a condition instead
* And more!

## Configuration

AI test failure analysis with ChatGPT is available in Projektor server v5.2.0+ with the following environment variable set
to your OpenAI API key:

```
OPENAI_API_KEY=<your_openai_api_key>
```
