---
name: frontend
description: Use for everthing frontend related.
license: Apache-2.0
---

# Use-Case-First Frontend Design

Design for the task a person needs to complete. Use visual choices to make that task easier. Keep the interface focused and clear. Add character where it fits. Avoid decoration that adds noise.

## 1. Understand the task

Inspect the relevant route, component, and nearby patterns before editing. Identify:

- **User and context:** Who is using this? On what device or under what constraints?
- **Goal:** What does the user need to accomplish? What does success look like?
- **Primary action:** What should the user do next? What information do they need?
- **Flow and states:** What happens before, during, and after the action? Include relevant loading, empty, error, success, and cancel states.

Use the brief, product behavior, real content, and project conventions as evidence. Make a reasonable, reversible assumption when a minor detail is missing. Ask only when the answer could change the flow, data, or visual direction. Do not ask about choices you can infer.

## 2. Respect the product

- Follow the existing framework, components, design tokens, typography, and interaction patterns.
- Reuse existing primitives and dependencies. Avoid competing systems and unnecessary packages.
- For a new surface, define a small set of choices for color, type, spacing, shape, and layout. Match them to the user and task.
- Keep changes scoped to the feature. Do not redesign unrelated areas unless a shared issue blocks the work.

## 3. Choose a useful visual direction

Let the use case shape the layout. A feature may need a compact form, readable table, focused editor, or clear sequence. Do not default to a hero, card grid, or marketing layout.

- Build hierarchy with grouping, alignment, spacing, and type. Add borders, shadows, color, and motion only when they help.
- Make the primary action easy to find. Keep secondary actions quieter.
- Give the interface a deliberate visual identity. Use emphasis sparingly.
- Use specific, task-appropriate content. Avoid filler, invented metrics, decorative labels, and fake data.
- Avoid generic defaults: centered headline-and-button layouts, repeated equal cards, gratuitous gradients, identical rounded boxes, arbitrary accent text, and unrelated animation. Use them only when they fit the brief.
- Keep the interface restrained, not bland. Preserve useful cues and personality.

## 4. Make interactions clear

Every control needs a clear purpose, state, and result.

- Use links to navigate and buttons to perform actions. Prefer native semantic elements over clickable generic containers.
- Label actions by their outcome. Use consistent terms throughout the flow. For example, use “Save changes” instead of “Submit.”
- Give clear feedback for important actions and async work. Preserve user input when a request or validation fails.
- Add relevant empty, loading, error, success, disabled, and permission states. Do not leave users with a blank area or unexplained spinner.
- Show validation feedback beside the field. Explain how to fix the issue. Associate the message with its field.
- Confirm destructive or hard-to-reverse actions. For routine actions, prefer undo over a confirmation dialog when practical.
- Keep navigation and state predictable. Put useful, shareable state in the URL when it fits the app.
- Do not invent functionality. Implement each visible control or explain why it is unavailable.

## 5. Build in accessibility

Use WCAG 2.2 AA as a practical target. Follow platform conventions. Treat accessibility as part of the design.

- Use semantic landmarks, logical headings, and native controls. Add ARIA only when HTML does not express the needed meaning or state.
- Support keyboard use in a sensible order. Show a visible focus indicator. Do not hide or trap focus. Custom widgets need expected keyboard behavior.
- Give every form control a persistent accessible name. Provide labels, instructions, required state, and associated errors. Do not rely on placeholders alone.
- Name icon-only controls. Give informative images useful alt text. Use empty alt text for decorative images. Hide decorative icons from assistive technology.
- Do not use color alone to convey meaning. Target contrast of at least 4.5:1 for normal text and 3:1 for large text and essential non-text controls.
- Support zoom and narrow screens. Avoid clipping and horizontal page scrolling at 320 CSS pixels, unless the content requires two-dimensional layout.
- Respect reduced-motion preferences. Keep motion brief and useful. Never require motion to understand or use the interface.
- Announce important async feedback when needed. Avoid noisy announcements for routine updates.
- Give dialogs and popovers accessible names. Manage focus, dismissal, and focus return.
- Aim for touch targets around 44 by 44 CSS pixels where practical. Leave space between nearby actions.

## 6. Make layouts robust

- Test with real content, long labels, user-generated text, empty values, and changing list lengths.
- Allow for longer translations. Do not design for a single perfect screenshot.
- Adapt layout and controls for narrow and wide screens. Do not just shrink the desktop view.
- Reserve space for media and async content when needed. This helps prevent layout shifts.
- Optimize heavy assets. Avoid animation or rendering work without user benefit.
- Keep text readable. Allow zoom, selection, copying, and pasting.

## 7. Implement and review

Work in short loops:

1. Plan the layout, hierarchy, key states, and interactions around the user’s goal.
2. Follow project conventions. Keep components and styles clear. Avoid broad rewrites for a small feature.
3. Run relevant checks. Use diagnostics, tests, lint, typecheck, or build as available. Exercise the main interaction path when possible.
4. Review desktop and mobile layouts when preview tools are available. Check focus, contrast, overflow, text wrapping, and state changes.
5. Compare the result with the user’s goal. Remove anything that does not help complete it. Fix the most important usability or accessibility issue first.

Do not claim checks you could not run. State relevant validation and remaining uncertainty.

## Reviewing existing UI

Prioritize issues by impact on the task and accessibility. Give concrete findings and identify the affected file or component. Suggest a fix. Separate confirmed failures from recommendations. Avoid subjective styling notes unless they affect hierarchy, meaning, or usability. If asked to fix issues, implement and validate the changes.

## Completion standard

Users should know where they are, what matters, and what to do next. The full feature flow should work across screen sizes. Keyboard and assistive technology users should be able to use it. Keep the design intentional and no busier than the task requires.
