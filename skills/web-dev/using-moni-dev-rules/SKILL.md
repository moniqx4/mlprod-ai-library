---
name: using-moni-dev-rules
description: Use to apply the MONI development rules to your work - guides implementation of the rules in your development process to ensure consistency, quality, and maintainability of the codebase.
---

# Overview

Guide for organizing web applications in a monorepo structure using the MONI development rules. This includes structuring code into apps and packages, following naming conventions, managing shared types and local types and rules around creating types in general.

## MUST Rules

1. When creating a Node based web application, MUST always use Typescript.
2. Whe creating UI E2E tests, or API tests, MUST use Playwright.
3. When creating a Nextjs web application, MUST follow this file structure:
- /apps - contains the page and references a base-page for the actual page content. Only metadata and toggling of page visibility should be in the page file. Should not contain any component single components unless it wraps a base page, or if it needs to be outside of a base-page. Should not contain any state management logic, or any types.
- /base-pages - contains the actual page content, and can contain components, state management logic, and types. Should not contain any metadata or toggling of page visibility. These should be the Higher level components that the pages reference, using the components like building blocks to create the actual page content.
- /features - contains the all the files related to a specific feature, such as a dashboard, or a specific page. This should contain all the components, state management logic, and types related to that feature. These features may be globally used across the codebase, or they may be specific to a certain page or set of pages. 

- /components - contains all the reusable components that are used across the codebase. These should be as generic as possible, and should not contain any specific logic related to a certain feature or page. These should be the building blocks that the base-pages and features use to create the actual page content. Items in this folder are typically compromised of mutiple smaller components, and should be organized in a way that makes it easy to find and use the components. For example, you could have a folder for each type of component, such as layout, sections, headers, etc.
- /components/ui - contains the design system components, such as buttons, inputs, tabs, etc. Typically shadcn components are used and all of those components should be in this folder.
- /components/elements - contains the components that are not part of the design system, but are still reusable across the codebase, such as a header component, or a footer component. These should be more specific than the ui components, but still reusable across the codebase.
- /components/non-display - contains those that work as a component, but have a global usage, such as an SEO component, or Fonts, metadata component, etc.

- /data - contains all the shared items around data, such as shared types, constants, queries, mutations, etc.
- /data/shared-types - contains all the types that are shared across the codebase, such as a User type, or a Product type. These should be as generic as possible, and should not contain any specific logic related to a certain feature or page. These should be the building blocks that the features and base-pages use to create the actual page content.
- /data/constants - contains all the constants that are shared across the codebase, such as a list of countries, clientData, routes, etc.
- /data/enums - For larger projects or one with a lot of shared enums, it can be helpful to have a separate folder for enums. This can help keep the shared-types folder more focused on types, and can make it easier to find and manage enums. If only a few enums are shared across the codebase, putting them all in an enum.ts file is fine.
- /data/zod-types - contains all the zod types, schemas that are shared across the codebase, such as a User schema, or a Product schema. All Zod schemas should have a corresponding inferred type created and that type appended with 'Z' to differentiate it from typical types. Create these types for things that need granular type checking, such as API  request and respond structures, or form inputs, etc. If only a few zod types can have them all in one file, but if there are a lot of them, it can be helpful to have a separate file for each type or group of related types.

- /utils - contains all the utility functions that are used across the codebase, such as formatting functions, date functions, etc. These should be as generic as possible, and should not contain any specific logic related to a certain feature or page. These should be the building blocks that the features and base-pages use to create the actual page content.
- /utils/twUtils - contains all the utility functions related to tailwind, such as custom classes, formatting functions, etc.

- /hooks - contains all the custom hooks that are used across the codebase, such as a useAuth hook, or a useForm hook. These should be as generic as possible, and should not contain any specific logic related to a certain feature or page. These should be the building blocks that the features and base-pages use to create the actual page content.

- /libs - contains all the libraries that are used across the codebase, such as a library for handling authentication, or a library for handling payments. These should be as generic as possible, and should not contain any specific logic related to a certain feature or page.

All other folders will be those created by the Nextjs framework, such as /public, etc. These should not contain any logic, components, or types. They should only contain the files that are necessary for the Nextjs framework to function properly.

4. When creating types, MUST follow these rules:
- If a simple union type, prepend with T, such as TUser, or TProduct.
- If a more complex type, such as an interface, or a type that is used to define the shape of a component, prepend with I, such as IUser, or IProduct. Do NOT use the I prefix for component props types, as those should use the Props suffix, such as UserCardProps, or ProductCardProps, etc.
- For component props types, append with Props, such as UserCardProps, or ProductCardProps, etc. Use `type` keyword for these types, and do not use the `interface` keyword, as these types are not expected to be extended or have methods defined, they are simply defining the shape of the props for a component.
- If a basic custom type object, and using the 'type' keyword, append with the word 'Type', such as CharacterType, or SkillType, etc.
- If a type is expected to be extended or has methods defined, use an interface, otherwise use the type keyword.
- Use enums for values that control logic, or if there are a specific set of values that a type can be, such as a UserRole enum, or a ProductCategory enum, etc. Do NOT use enums for simple union types, such as a Status type that can be 'active', 'inactive', or 'pending', etc. In that case, use a simple union type with the T prefix, such as TStatus = 'active' | 'inactive' | 'pending', etc.

5. AVOID using classes in Typescript, using a Functional Programming approach. Only use classes if there is a benefit or requirement to do so, such as when working with a library that requires classes, or when implementing a specific design pattern that benefits from classes. In general, prefer using functions, objects, and other functional programming constructs over classes in Typescript. If a class is used, explain the reason why a class was chosen over a functional programming approach, and ensure that the class is well-structured, follows SOLID principles, and is properly documented. Also append the word 'Class' to the end of the class name, such as UserClass, or ProductClass, etc.


## Component building Style Guide

When building components, follow these guidelines to ensure consistency and maintainability across the codebase:

1. When using props in the component, do not destructure the props in the function signature, instead keep the props as a single object and reference the props using dot notation, such as props.name, or props.text, etc. This helps to keep the component organized and makes it easier to see all the props that are being passed to the component in one place. It also helps to avoid naming conflicts and makes it easier to refactor the component in the future if needed.

2. Ensure that the component is properly typed with the appropriate props type. This helps to ensure that the component is properly typed and that the props are being used correctly within the component. It also helps to catch any potential type errors early on in the development process. Example: 

```typescript


type MyComponentProps = {
  name: string
  text: string
}

const MyComponent = ({name, text}: MyComponentProps) => {
  return (
    <div>
      <h1>{name}</h1>
      <p>{text}</p>
    </div>
  )
}
```

3. Naming Conventions for Components:
- Use PascalCase for component names, such as MyComponent, UserCard, ProductList, etc.
- Use PascalCase for base-pages and append 'Page' to the end of the name of a base-page such as DashboardPage, or ProfilePage, etc.
- For feature support files, use PascalCase for feature component files.  For other support files within the feature folder, use lowercase with hyphens, such as feature-name.ts, or feature-name-utils.ts, etc.
