# Which identity provider should we use

## Context and Problem Statement

> **Warning**: this ADR was written many months after the decision and the implementation. I am writing it today to allow the team to understand why we took this decision and how it will impact the choice of the new identity provider.  

At that time, we did not have user identity/account management. The solution was stateless; once you refreshed your page, you lost your work. So we needed to implement account management in the app. This was not the main goal back then, but account management was required for what we wanted to achieve. The project was also at its beginning and didn’t have a dedicated budget. Therefore, the implementation time needed to be very short, free, and with the least possible management overhead.  

## Considered Options

* ClerkJS  
* Keycloak  
* BetterAuth  

## Decision Outcome

We chose ClerkJS.  

The free tier allows 10,000 users, no credit card required, etc. A plugin to integrate it easily with Nuxt-based applications was available. It does not require any management except creating the users via the interface—no instance, containers, or other hosting constraints. It is a fully managed SaaS. It also comes with a set of pre-developed sign-in/sign-up frontend pages.  

BetterAuth was not mature enough and did not support Nuxt well at that time.  

Keycloak requires deploying infrastructure as well as incurring hosting costs. The instance then needs to be managed, patched, and kept running. We did not have the time for that. The integration with our frontend and backend would also have required significantly more developer effort (based on our estimates).  

### Consequences

* Positive: we had our account/identity system ready within a single 3-week sprint.  

> **Notes**: today, this choice does not fit IroCO2 at all. Back then, we aimed to develop IroCO2 as a SaaS, so ClerkJS was not a problem. But since we are going open source, it may block adoption of the solution. Clerk is not open source and is a third-party licensed product.  
