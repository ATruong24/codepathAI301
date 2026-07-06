# Contribution 2: Deleting a string redirects to main component page instead of the next string

**Contribution Number:** 2
**Student:** Anthony Truong
**Issue:**  https://github.com/WeblateOrg/weblate/issues/6812
**Status:** Phase 1

---

## Why I Chose This Issue

I chose issue #6812 because it's a well-scoped UX fix with clear maintainer buy-in nijel, Weblate's lead maintainer, confirmed in the very first comment that the current behavior is unintentional and should change. The fix is contained to the delete-string flow in Weblate's translation editor, which fits my Python/Django background.
---

## Understanding the Issue

### Problem Description
When a user deletes a translation string while working through a project, 
Weblate redirects them back to the main component/language overview page 
instead of continuing to the next string in the translation queue.

### Expected Behavior

After deleting a string, the user should be redirected to the next 
string/unit in the queue the same behavior as after translating or 
saving a string.


### Current Behavior

The user is redirected to the component's main language page, interrupting 
their workflow and forcing them to navigate back manually.

### Affected Components

Likely the string/unit deletion view and its redirect logic in Weblate's 
translation editor (Django views layer). The exact file(s) will be 
confirmed in Phase II once I can trace the code path.

---

## Reproduction Process

### Environment Setup

[Notes on setting up your local development environment - challenges you faced, how you solved them]

### Steps to Reproduce

1. [Step 1]
2. [Step 2]
3. [Observed result]

### Reproduction Evidence

- **Commit showing reproduction:** [Link to commit in your fork]
- **Screenshots/logs:** [If applicable]
- **My findings:** [What you discovered during reproduction]

---

## Solution Approach

### Analysis

[Your analysis of the root cause - what's causing the issue?]

### Proposed Solution

[High-level description of your fix approach]

### Implementation Plan

Using UMPIRE framework (adapted):

**Understand:** [Restate the problem]

**Match:** [What similar patterns/solutions exist in the codebase?]

**Plan:** [Step-by-step implementation plan]
1. [Modify file X to do Y]
2. [Add function Z]
3. [Update tests]

**Implement:** [Link to your branch/commits as you work]

**Review:** [Self-review checklist - does it follow the project's contribution guidelines?]

**Evaluate:** [How will you verify it works?]

---

## Testing Strategy

### Unit Tests

- [ ] Test case 1: [Description]
- [ ] Test case 2: [Description]
- [ ] Test case 3: [Description]

### Integration Tests

- [ ] Integration scenario 1
- [ ] Integration scenario 2

### Manual Testing

[What you tested manually and results]

---

## Implementation Notes

### Week [X] Progress

[What you built this week, challenges faced, decisions made]

### Week [Y] Progress

[Continue documenting as you work]

### Code Changes

- **Files modified:** [List]
- **Key commits:** [Links to important commits]
- **Approach decisions:** [Why you chose certain approaches]

---

## Pull Request

**PR Link:** [GitHub PR URL when submitted]

**PR Description:** [Draft or final PR description - much of the content above can be adapted]

**Maintainer Feedback:**
- [Date]: [Summary of feedback received]
- [Date]: [How you addressed it]

**Status:** [Awaiting review / Iterating / Approved / Merged]

---

## Learnings & Reflections

### Technical Skills Gained

[What you learned technically]

### Challenges Overcome

[What was hard and how you solved it]

### What I'd Do Differently Next Time

[Reflection on your process]

---

## Resources Used

- [Link to helpful documentation]
- [Tutorial or Stack Overflow post that helped]
- [GitHub issues or discussions that helped]
