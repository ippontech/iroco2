# Use of authentication in a development environment

## Context and Problem Statement

* Currently we use _Clerk_ to authenticate in Iroco2
* In development environment, developers do not necessary need _Clerk_
* New developers do not have a _Clerk_ account

## Considered Options

* Replace _Clerk_ with an other solution like _Keycloak_
* Add an other solution like _Keycloak_
* Add an other way to avoid the use of _Clerk_
* Add an _auto_ connection with a _Demo_ user

## Decision Outcome

Chosen option: "Add an _auto_ connection with a _Demo_ user", because it is a quick win.

### Consequences

* Good, because developers can be auto-connected (they do not have to have a Clerk account and to connect) and can come back to _Clerk_ authentication
* Bad, because security problems could appear in next developments. Developers have to be careful.
