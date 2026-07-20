# Contribution [1]: Dependency software-properties-common not available anymore on Debian 13

**Contribution Number:** 1
**Student:** Anthony Truong  
**Issue:** [#1931](https://github.com/wazuh/wazuh-ansible/issues/1931)  
**Status:** Phase IV Complete

Resubmitting for Phase III and Phave IV Grading. Please take a look of changes.

---

## Why I Chose This Issue

I chose this issue as I personally use Wazuh and it's a great tool. I will boot up a Debian VM and try to recreate the issue and solve the problem.

---

## Understanding the Issue

### Problem Description

The Wazuh Ansible role for the wazuh-indexer lists `software-properties-common` as a required apt dependency. On Debian 13 (Trixie), this package has been removed from the stable repositories due to an unresolved upstream bug. As a result, running the role against a Debian 13 host fails when Ansible tries to install the package.

### Expected Behavior

The wazuh-indexer Ansible role should install successfully on Debian 13 without failing on missing apt dependencies. Debian 12 doesnt have this issue after testing.

### Current Behavior

The role fails at the "Install Wazuh indexer dependencies" task with: "no available installation candidate for software-properties-common"

### Affected Components

- `roles/wazuh/wazuh-indexer/tasks/Debian.yaml` — the task that installs apt dependencies
- Perhaps Debian task files in `wazuh-manager`, `wazuh-agent`, and `wazuh-dashboard` roles, which may carry the same dependency list which can cause the same issue.

---

## Reproduction Process

### Environment Setup

I used Proxmox and then installed portainer and downloaded the Debian 13 ISO image. I then booted up a container containing Debian 13.3 to download Wazuh and try to recreate the issue. The main friction was getting my Git Authentication via tokens. After creating the Personal Access Token, it allowed me to push my work and test it out.

### Steps to Reproduce

1. Provision a fresh Debian 13.3 VM with SSH access.
2. Clone the wazuh-ansible repository and check out the `v4.14.2` tag.
3. Configure an Ansible inventory pointing at the Debian 13 VM.
4. Run the wazuh-indexer against the VM.
5. Observe the playbook fails at the "Install Wazuh indexer dependencies" task with the message `no available installation candidate for software-properties-common`.
6. Confirm the root cause by SSH-ing into the VM and running `apt-get install software-properties-common` directly.

### Reproduction Evidence

- **Commit showing reproduction:** https://github.com/ATruong24/wazuh-ansible/tree/fix-issue-1931
- **Screenshots/logs:** [If applicable]
- **My findings:** The package is genuinely missing from the Debian 13 stable repos, not just renamed.

---

## Solution Approach

### Analysis

The root cause is that the role hardcodes a dependency that no longer exists on Debian 13. 

### Proposed Solution

We can audit the indexer role for any usage of 'add-apt-respository' or related tooling. If none, we can remove it from the dependency list.  If it is used, we can wrap it in a 'wehn' statement so Debian 13 can skio it.

### Implementation Plan

Using UMPIRE framework (adapted):

**Understand:** [Wazuh's Ansible roles depend on `software-properties-common`, which is unavailable on Debian 13 stable. The indexer playbook fails at the dependency install task, blocking all Wazuh indexer deployments on Debian 13.

**Match:** Ansible roles in the repo handle distro-version differences using `when: ansible_distribution_major_version` conditionals. I'll look for that pattern in the manager and agent roles. The Debian-specific task files in each role are the analogous code paths.

**Plan:** [Step-by-step implementation plan]
1. [Grep the wazuh-indexer role for any usage of `add-apt-repository` or features provided by `software-properties-common`.
2. If unused, remove `software-properties-common` from the dependency list in `roles/wazuh/wazuh-indexer/tasks/Debian.yaml`.
2. If used, split it out into a separate task with `when: ansible_distribution_major_version | int < 13`.
3. Check the wazuh-manager, wazuh-agent, and wazuh-dashboard roles for the same pattern and apply the same fix.

**Implement:** (https://github.com/ATruong24/wazuh-ansible/tree/fix-issue-1931)

**Update after investigation:** Confirmed via code search that `add-apt-repository` and `apt_repository` module usage do not depend on `software-properties-common` anywhere in the codebase (Wazuh's repo is added via Ansible's native `apt_repository` module). This confirmed Option 1 (full removal) was correct rather than the conditional fallback — no `when:` clause was needed.

**Review:** I'll read `CONTRIBUTING.md`, follow the commit message format used in recent merged PRs, and ensure ansible-lint passes before submitting.

**Evaluate:** Re-run on a Debian 12 VM to confirm no regression for older releases.

---

## Testing Strategy

### Unit Tests

- [X] Test case 1: Run the indexer playbook on Debian 13
- [X] Test case 2: Run the indexer playbook on Debian 12 
- [X] Test case 3: Verify the indexer service starts and runs after install

### Integration Tests

- [X] Integration scenario 1
- [X] Integration scenario 2

### Manual Testing

Debian 12 works but Debian 13 does not.

---

## Implementation Notes
**Update after investigation:** Confirmed via code search that `add-apt-repository` and `apt_repository` module usage do not depend on `software-properties-common` anywhere in the codebase (Wazuh's repo is added via Ansible's native `apt_repository` module). This confirmed Option 1 (full removal) was correct rather than the conditional fallback — no `when:` clause was needed.

### Week [3] Progress

- Reviewed CONTRIBUTING.md and recent merged PRs for commit style conventions
- Investigated whether software-properties-common is actually used by the indexer role by grepping for add-apt-repository and apt_repository across all roles
- Found that the package is unused (no add-apt-repository or apt_repository references anywhere in the 4.14.7 branch), so it could be safely removed entirely rather than made conditional
- Applied the fix to wazuh-indexer, then audited wazuh-manager, wazuh-agent, and wazuh-dashboard for the same pattern

---

## Pull Request

**PR Link:** (https://github.com/wazuh/wazuh-ansible/pull/2176)

**PR Description:** Removed the unused `software-properties-common` dependency from the wazuh-indexer Ansible role to fix installation failures on Debian 13, where the package is no longer available in stable repositories.

**Maintainer Feedback:**
- [Date]: [Summary of feedback received]
- [Date]: [How you addressed it]

**Status:** Awaiting review

---

## Learnings & Reflections

### Technical Skills Gained
I learned how to navigate a large, unfamiliar Ansible codebase by using GitHub's code search to trace whether a dependency (`software-properties-common`) was actually used anywhere before touching it, rather than assuming. I also learned that open-source projects often maintain multiple active release branches (main/6.0.0 vs the 4.14.7 maintenance branch) and that bug fixes need to target the branch where the bug actually lives, a distinction I initially missed. I practiced authenticating with GitHub via Personal Access Tokens instead of password auth.

### Challenges Overcome
The biggest challenge was realizing the repository's `main` branch had already been restructured for a 6.0.0 release, so the file path referenced in the original issue no longer existed there. I had to identify the correct maintenance branch (4.14.7) and re-verify my investigation and fix against that branch specifically.

### What I'd Do Differently Next Time
I'd check which branch an issue applies to before starting any investigation, rather than defaulting to `main`. This would have saved time I spent grepping the wrong branch. I'd also try to submit a narrower first commit and get early feedback before doing a full audit of all four roles, since maintainers may have opinions on scope.

### Technical Skills Gained

I learned how to help open source project and help them despite not knowing the whole database.

### Challenges Overcome

It was hard to understand what each folder and file do but after a couple of hours looking, it has been faster.

### What I'd Do Differently Next Time

I will create a more documented path and try to submit PR as soon as possible so I can get feedback and improve on it.

**Maintainer Feedback:**
- 2026-06-29: PR submitted and @[maintainer] tagged for review in a PR comment. No response yet.

---

## Resources Used

- [Link to helpful documentation]
- [Tutorial or Stack Overflow post that helped]
- [GitHub issues or discussions that helped]
