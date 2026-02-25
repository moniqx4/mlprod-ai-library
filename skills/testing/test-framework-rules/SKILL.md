---
name: test-framework-rules
description: Use to apply the test framework rules to your work - guides implementation of the rules when working with a test automation framework, ensuring tests are reliable, maintainable, and aligned with best practices
---

# Overview

When working with a test automation framework, these rules define the coding syntax to use, the best practices to follow, and the guidelines to ensure consistency, and maintainability of the framework and the tests generated.

## MUST Rules
- If this is a UI E2E test, MUST use Playwright as the testing framework.
- If this is an API test, MUST use Playwright as the testing framework.
- When using Playwright, use the Node version of Playwright and MUST use Typescript.
- Always use a functional programming approach for writing all code, including test code. Avoid using classes in the test code, and instead use functions, objects, and other functional programming constructs to organize the test code.
- Use the syntax of the latest version of Playwright installed in package.json
- Use the agent defined for Playwright, if using Claude, use the agent in .claude/agents, if vscode, using the mcp server defined in .vscode/mcp.json

**Testing Strategy & Setup:**
- Design comprehensive E2E test suites with proper page object patterns
- Configure Playwright for different browsers and environments
- Set up CI/CD integration for automated testing
- Implement visual regression testing strategies

**Test Development:**
- Write maintainable, reliable Playwright tests using TypeScript
- Create reusable page objects and test utilities
- Implement proper wait strategies and element selectors
- Handle complex user interactions and form submissions

**Debugging & Optimization:**
- Analyze test failures and provide detailed debugging insights
- Optimize test performance and reduce flakiness
- Configure proper timeouts and retry mechanisms
- Generate and analyze test reports

**Best Practices:**
- Use data-testid attributes for stable selectors
- Implement proper test isolation and cleanup
- Create comprehensive assertions for UI and API validation
- Follow Playwright's recommended patterns for TypeScript projects

When invoked, always:
1. Analyze the current testing setup and identify gaps
2. Provide specific, runnable Playwright code
3. Include proper TypeScript types and error handling
4. Suggest improvements for test reliability and maintainability
5. Run tests proactively when code changes are detected
