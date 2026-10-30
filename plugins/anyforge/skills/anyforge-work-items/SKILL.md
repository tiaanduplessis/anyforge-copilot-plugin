---
name: anyforge-work-items
description: >
  Deliver AnyForge work from GitHub Copilot. Find a work item, read its
  description, acceptance criteria and initiative spec, implement it in this
  repository, and name its code in the branch and pull request so AnyForge
  links the pull request and moves the item. Also file a new work item, check
  a crew, or answer a pending approval. Use when the user mentions AnyForge,
  a work item code such as ENG-42, an initiative, a crew or an approval.
license: Apache-2.0
---

# Delivering AnyForge work from Copilot

AnyForge plans and tracks the work; you write the code. The AnyForge MCP
server this plugin adds gives you the `anyforge_*` tools. If they are missing,
ask the user to sign in to the `anyforge` MCP server (a browser sign-in, no
key) and stop.

The tools act as the signed-in person, within their role and the scopes they
approved. A refusal means they lack that access; say so and do not look for
another way round it.

## Choose the workspace

If `anyforge_list_workspaces` exists, call it first and pass the chosen
`workspaceId` on every other call. Ask the user when more than one workspace
could be meant. Never guess one from an id or an earlier call.

## Find the work

- A code the user gave (`ENG-42`): `anyforge_list_work_items` with
  `itemId: "ENG-42"`, then `anyforge_get_work_item`.
- Their own queue: `anyforge_list_work_items` with `assignee: "me"`, narrowed
  by `status` when they ask for open work.
- The wider plan: `anyforge_get_initiative` and `anyforge_get_initiative_spec`
  for the initiative the item belongs to.

Read the description and acceptance criteria before you plan. Work to them,
and tell the user when the item and the code disagree rather than quietly
changing the scope.

## Link the pull request

AnyForge links a pull request to every work item code it names and keeps the
link current as the pull request moves:

- Start the branch name with the code: `eng-42-refund-retries`.
- Put the code in the pull request title: `ENG-42: Retry failed refunds`.
- Write `Closes ENG-42` in the description when this pull request finishes
  the item, or `Part of ENG-42` when it is one step of it.

When the workspace syncs pull request status, marking the pull request ready
for review starts a not-started item, and merging it completes the item.
`Part of` links without moving the item. An item a crew is running is never
moved by your pull request.

## Write to AnyForge only when asked

Reading is always fine. Before anything that changes AnyForge, show the user
what you will send and wait for a clear yes:

- File new work with `anyforge_create_work_item` (title, type, priority, and a
  description with what you found). It lands in the backlog.
- Comment with `anyforge_post_work_item_comment`, or change fields with
  `anyforge_update_work_item`.
- Approvals: `anyforge_list_pending_approvals`, then
  `anyforge_get_approval_detail`. Submit a decision with
  `anyforge_submit_approval` only with the decision and reason the user gave.
- Crews: `anyforge_list_crews` and `anyforge_get_crew` to report progress.
  Halt, resume or message a crew only when the user asks for exactly that.

After a write, read the record back and report what changed, with its link.

## Never

- Put a key, token, password or customer data into a tool call, comment or
  work item.
- Claim a crew finished, a gate was approved, or code reached production
  because a tool call returned. Read the record and say what it shows.
