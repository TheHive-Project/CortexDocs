# User APIs

This is the set of APIs that allow creating, updating and listing users within an Organization

## User model

A User is defined by the following attributes

- `id`: (*readonly*) the user's id/login
- `name`: (*writable*) the user's name
- `roles`: (*writable*) the users' roles
- `status`: (*writable*) the user's status
- `organization`: (*writable*) the user's organization name
- `createdAt`: (*generated*) the creation date
- `createdBy`:  (*generated*) the user who created the user
- `updatedBy`:  (*generated*) the last update date
- `updatedAt`:  (*generated*) the latest user who updated the user
- `hasKey`: (*computed*) true when the user has a defined api key
- `hasPassword`: (*computed*) true if the user has a password