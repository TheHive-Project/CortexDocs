# User roles

In Cortex 2, 4 roles are defined:
 - `read`: the user can read all jobs information, including the report but he
can submit them. This role can't be used in `cortex` organization.
 - `analyze` (implies read role): the user can submit a new job using one of the
configured analyzer for its organization. This role can't be used in `cortex`
organization.
 - `orgAdmin` (implies analyze role): with this role, the user can manage users
of its organization. He can add user with role `read`, `analyze` and `orgAdmin`.
This role also permit to configure analyzers. This role can't be used in
`cortex` organization.
 - `superAdmin` (incompatible with other roles): this role only permit to manage
organizations and users. The first user is created with this role. Several users
can have this role but only on the `cortex` organization (which is automatically
created during installation).

| Actions\\Roles           | read | analyse | orgAdmin | SuperAdmin |
| ------------------------ | ---- | ------- | -------- | ---------- |
| Read reports             |  X   |    X    |    X     |            |
| Run jobs                 |      |    X    |    X     |            |
| Enable/Disable analyzer  |      |         |    X     |            |
| Configure analyzer       |      |         |    X     |            |
| Create org analyst       |      |         |    X     |     X      |
| Delete org analyst       |      |         |    X     |     X      |
| Create org admin         |      |         |    X     |     X      |
| Delete org admin         |      |         |    X     |     X      |
| Create Org               |      |         |          |     X      |
| Delete Org               |      |         |          |     X      |
| Create Cortex admin user |      |         |          |     X      |

# Organizations and Users
In version 2 of Cortex, users and analyzers are managed in organizations. The users (except superAdmins) can see only items hold by its organization. The analyzers are configured and enabled per organization. So that an analyzer can be configured with different API keys or rate limits in two organization.

## First access, configure Cortex administrator


## Create Organization

## Delete Organization

## Create user

## Delete user


# Analyzers

Analyzers can be configured only by `orgAdmin` users. In "Organization" menu,
the "Configurations" tab permits to define configuration item for all available
analyzers, including settings which is common to all analyzers (Global
Configuration). Some configurations affect several analyzers. Configuration of
analyzer doesn't use configuration file any longer. Now configuration is done
using the web interface.

In the "analyzer" tab, analyzers can be enable for the current organization. For
each of them, you can define a rate limit (maximum number of analyzes in the
specified period of time) and the maximum acceptable artifact TLP the analyzer
can process.
