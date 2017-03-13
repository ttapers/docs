---
title: Organizations
nav_sort: 1
autotoc: true
layout: guide.hbs
lunr: true
---

Hologram supports creating **organizations** so that multiple users can share
access to the same devices and billing account. Users can be members of multiple
organizations, and every device is owned either by an organization or directly
by a user.  This is similar to how organizations work on other collaborative
platforms such as GitHub.

### Creating an Organization

Click on the icon in the top left corner of the
[dashboard](https://dashboard.hologram.io) to expand the account
dropdown:

{{{ image src="/wp-content/uploads/2016/11/account-dropdown-no-orgs.png"
    alt="Account dropdown" }}}

If you are already a member of an organizations, it would be listed below
*API Key*. Since we need to create one, select *Add new organization*. This
opens a form:

{{{ image src="/wp-content/uploads/2016/11/create-organization-form2.png"
    alt="Create Organization form" }}}

* **Organization name**: Short name for the organization.
* **Migrate your existing account data**: Selecting this option will transfer
  ownership of all your devices and apps to the new organization. If you need
  to transfer data in a more granular way, please contact Hologram Support.
* **Payment method**: Choose either a payment method you've already set up for
  your personal account, or a new method. You may also set up payments later.

### Inviting collaborators

After creating the organization, you may invite others to join. Enter the email
addresses for people to add. If they do not already have a Hologram account,
they will receive a link to create one.

{{{ image src="/wp-content/uploads/2016/11/org-invite-collaborators.png"
    alt="Invite collaborators" }}}

Collaborators may be granted one of three access levels in an organization:

* **Editor:** View devices and cloud messages, manage device settings that don't
  affect costs, manage app integrations
* **Manager:** Same as Editor, plus managing device settings that could affect
  costs (e.g. activating devices and changing data plans)
* **Admin:** Full access--same as Manager, plus the ability to change billing
  settings

The creator of the organization has a special *Owner* access level, which grants
the same permissions as *Admin*.

### Switching between organizations

The dashboard shows information related to a single **context** (an organization or your
personal account) at a time.
You can tell the active context by the icon in the top left of the dashboard,
and the color of the left sidebar.

To change the context that you're viewing, click the top-left icon. The
dropdown displays the current context first, along with associated actions such
as account settings.

{{{ image src="/wp-content/uploads/2016/11/account-dropdown-orgs.png"
    alt="Account dropdown" }}}

Below that are links to switch context back to your personal account or to any
other organizations that you are a member of.


