# PRD: Angular Pixi Wrapper (`ngx-pixi`)

## 1. Project Overview

The goal is to create a lightweight, easy-to-use library that lets developers use **PixiJS** inside **Angular** using a declarative style (HTML tags). This brings the ease of `@pixi/react` to the Angular ecosystem, ensuring high performance by respecting how Angular handles change detection.

## 2. Objectives

- **Declarative UI:** Define 2D scenes using Angular components instead of writing long imperative scripts.
- **Performance first**: Use signals, computed and effects to react.
- **Developer Experience:** Provide a clean API that feels natural to Angular developers (using Signals, Inputs, and Outputs).

## 3. Core Design Principles

- **Humility in Code:** Don't over-engineer. Wrap what is necessary, but allow users to access the raw Pixi instance if they need advanced features.
- **Pragmatism:** Focus on the features people actually use (Sprites, Containers, Graphics) before adding complex edge cases.
- **Efficiency:** Automatically clean up memory when components are destroyed to prevent leaks.

---

## 4. Functional Requirements (MVP)

### Basic Components

| Component          | Description                                                                   |
| ------------------ | ----------------------------------------------------------------------------- |
| `<pixi-app>`       | The main container. Initializes the Pixi Application and provides the canvas. |
| `<pixi-container>` | A grouping element to manage positions and visibility of multiple children.   |
| `<pixi-sprite>`    | Renders images or textures.                                                   |
| `<pixi-graphics>`  | Used for drawing basic shapes (circles, rectangles, lines).                   |
| `<pixi-text>`      | Renders text using Pixi's internal engine.                                    |

### Key Features

- **Signal-based Inputs:** Use Angular Signals for properties like `x`, `y`, `rotation`, and `alpha` for reactive updates.
- **Automatic Cleanup:** Every component must call `.destroy()` on its internal Pixi object during `ngOnDestroy`.
- **Event Mapping:** Standard Pixi interactions (pointerdown, pointerover, etc.) should be available as Angular `output signal` emitters.

---

## 5. Technical Specifications

### Change Detection Strategy

To avoid slowing down the browser, the Pixi `ticker` (the loop that renders the game) should run **outside** of Angular’s. We only trigger Angular's change detection when a specific event (like a click) needs to update the UI state.

### Dependency Injection

Use a `PIXI_APP_TOKEN` so child components (like Sprites) can easily find and attach themselves to the main stage without manual "parent-to-child" plumbing.

---

## 6. User Stories

1. **Simple Setup:** As a developer, I want to add a `<pixi-app>` tag to my HTML and see a canvas rendered immediately.
2. **Reactive Movement:** As a developer, I want to bind a variable to the `[x]` input of a sprite so it moves when my data changes.
3. **Low Overhead:** As a developer, I want the library to handle the "boring" parts (like resizing the canvas or destroying textures) so I can focus on my logic.

---

## 7. Roadmap & Milestones

- **Milestone 1:** Basic Bootstrap. Create the library workspace and the `<pixi-app>` component.
- **Milestone 2:** Core Elements. Support for `<pixi-container>` and `<pixi-sprite>`.
- **Milestone 3:** Interaction Layer. Implement event handling for mouse/touch inputs.
- **Milestone 4:** Documentation. A simple "Getting Started" guide with a few practical examples.

---

## 8. MCP (Model Context Protocol) Integration

### 8.1 Purpose

The `ngx-pixi-mcp` server acts as a bridge between the AI assistant and the codebase. Its goal is to provide the AI with the specific context of the library's declarative syntax, helping it generate correct Angular templates and troubleshoot PixiJS scene hierarchies.

### 8.2 Proposed Tools (Capabilities)

The MCP server will expose the following tools to the AI:

| Tool Name                 | Description                                                                         | Practical Use Case                                                                   |
| ------------------------- | ----------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------ |
| `generate-pixi-component` | Creates a new Angular component with the `ngx-pixi` boilerplate.                    | Quick-start a new game level or UI overlay.                                          |
| `inspect-scene-graph`     | Analyzes the current Angular template to identify Pixi parent-child relationships.  | Debugging why a Sprite isn't appearing or is out of bounds.                          |
| `get-pixi-docs`           | Provides a localized reference for `ngx-pixi` inputs (x, y, scale, tint).           | Ensures the AI doesn't hallucinate standard Pixi properties that aren't wrapped yet. |
| `validate-assets`         | Checks if the assets being used in a `<pixi-sprite>` exist in the `assets/` folder. | Catching "file not found" errors before running the app.                             |

### 8.3 Implementation Strategy

- **Simple Setup:** Build the MCP server using the official TypeScript SDK.
- **Context Injection:** The server will read the `angular.json` and the library's public API to stay updated with the latest changes in the wrapper.
- **Developer Workflow:** Use the MCP to allow the AI to "live-edit" scenes. For example: _"Move the 'player' sprite 50px to the right and wrap it in a container."_

### 8.4 Constraints

- **Read-Only by Default:** For safety, the MCP should mainly provide context and code suggestions rather than directly modifying the file system unless explicitly requested.
- **No Overhead:** The MCP server should be a separate utility that doesn't bloat the core `ngx-pixi` library.

---

## 9. Scaffolding & Generators (Angular Schematics)

### 9.1 Purpose

To provide a seamless "getting started" experience and maintain architectural consistency across projects. The generators will automate the installation of peer dependencies (PixiJS) and the creation of boilerplate code for common scene elements.

### 9.2 Schematic Types

| Command                   | Action              | Outcome                                                                                                              |
| ------------------------- | ------------------- | -------------------------------------------------------------------------------------------------------------------- |
| `ng add ngx-pixi`         | **Initial Setup**   | Installs `pixi.js`, adds it to `package.json`, and imports the necessary modules/providers into the root of the app. |
| `ng g ngx-pixi:component` | **Item Generator**  | Creates a basic Angular component pre-configured with a `<pixi-container>` or `<pixi-sprite>` template.              |
| `ng g ngx-pixi:scene`     | **Scene Blueprint** | Generates a full-screen component with a `<pixi-app>` and a basic resize-listener logic.                             |

### 9.3 Technical Goals

- **Peer Dependency Management:** Since PixiJS is a heavy library, the generator should ensure the version installed is compatible with the wrapper version.
- **Asset Configuration:** Automatically update `angular.json` to include a default `assets/pixi/` folder if the user chooses, helping with asset management.
- **Boilerplate Reduction:** A "Senior" approach focuses on making the library "invisible." The generator should produce clean, readable code that follows the library's best practices (e.g., using `OnPush` change detection by default).

### 9.4 User Story

- **Standardized Workflow:** As a developer, I want to run a single command to have a working Pixi canvas in my Angular app without manually hunting for installation steps in the documentation.

---

## 10. Success Metrics (KPIs)

- **Time to First Render:** A new user should be able to go from `npm install` to a moving sprite on the screen in less than 3 minutes.
- **Bundle Impact:** The library itself should add minimal overhead (less than 10kb gzipped, excluding PixiJS).
- **Adoption Ease:** Zero manual configuration required for standard Angular CLI projects.
