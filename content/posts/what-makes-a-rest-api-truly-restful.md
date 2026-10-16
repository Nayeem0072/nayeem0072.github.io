---
title: "What Makes a REST API Truly RESTful?"
date: 2026-10-16T16:00:00+06:00
description: "Beyond HTTP verbs: REST constraints, common API design mistakes, and how to review a REST API"
tags: [rest, api, http, spring-boot, backend, architecture]
draft: false
---

The most important distinction is this: a REST API is not simply an HTTP API that uses `GET`, `POST`, `PUT`, and `DELETE`. REST is an architectural style for designing networked systems, defined by Roy Fielding.

An API can use all the correct HTTP methods and still fail to follow important REST principles.

Let's look at what actually matters, with practical examples from a typical Spring Boot backend.

## 1. The six REST architectural constraints

### Client–server separation

The client handles the user interface; the server handles business logic and data. They should evolve independently through a defined interface.

*Example:* React calls a Spring Boot API without needing to know how the backend stores users.

### Statelessness

Each request contains the information needed to process it. The server must not depend on conversational or session state retained from an earlier request.

*Example:* Each authenticated request carries its access token rather than relying on a server-side login session.

### Cacheability

Responses should indicate whether they can be cached, for how long, and under what conditions.

*Example:* Public course data can use `Cache-Control: public, max-age=300` when appropriate.

### Uniform interface

This is the defining REST constraint. Resources, representations, HTTP semantics, and links provide a consistent way to interact with the system.

*Example:* `/orders/123` identifies an order, and HTTP methods express what the client wants to do with it.

### Layered system

A client doesn't need to know whether it is communicating directly with the application or through gateways, proxies, caches, and other intermediaries.

*Example:* A request passes through an API gateway, load balancer, and Spring Boot service.

### Code on demand (optional)

A server can optionally send executable code to a client.

This is uncommon in modern JSON APIs. The other five constraints still apply.

The uniform interface deserves particular attention because it is where many developers misunderstand REST.

## 2. Design around resources, not actions

Consider an API for managing meeting participants.

**Less RESTful — RPC-style endpoints**

```http
POST /getMeetingParticipants
POST /addParticipantToMeeting
POST /removeParticipantFromMeeting
```

**More RESTful — resource-oriented endpoints**

```http
GET    /meetings/42/participants
POST   /meetings/42/participants
DELETE /meetings/42/participants/7
```

Why is the second design better?

* Resources are nouns: meetings, participants, orders, users.
* HTTP methods express intent: retrieve, create, replace, delete.
* URLs identify resources, rather than encoding every operation as a function name.
* HTTP status codes and headers communicate outcomes using shared conventions.

This does not mean action-oriented endpoints are always forbidden. Some operations are genuinely commands, such as starting a live broadcast or approving a payment. You can model these as resources or explicit transitions when appropriate. REST does not require awkwardly forcing every business action into CRUD.

## 3. What most developers get wrong

These are the mistakes I see most often in REST API design.

### Mistake 1: Thinking that using HTTP verbs makes an API RESTful

Consider this Spring Boot endpoint:

```http
POST /api/users/123/delete
```

It uses HTTP, but the URL models an operation rather than a resource.

A better design is:

```http
DELETE /api/users/123
```

The second version uses HTTP semantics consistently, so clients, proxies, monitoring tools, and API consumers can reason about the request more predictably.

**The catch:** changing the URL alone doesn't make the entire API RESTful. REST is about the architecture as a whole.

### Mistake 2: Using `POST` for everything

A common API design looks like this:

```http
POST /api/getUsers
POST /api/createUser
POST /api/updateUser
POST /api/deleteUser
```

This ignores important differences between HTTP methods.

| Method | Intended semantics                                          | Idempotent?     |
| ------ | ----------------------------------------------------------- | --------------- |
| GET    | Retrieve a representation                                   | Yes             |
| POST   | Submit data for processing or create a subordinate resource | Not necessarily |
| PUT    | Create or replace the state at a known resource URI         | Yes             |
| PATCH  | Apply partial modifications                                 | Not guaranteed  |
| DELETE | Remove a resource                                           | Yes             |

An operation is **idempotent** when repeating the same request has the same intended effect on server state as performing it once.

For example, deleting user 123 twice should not delete two different users. The second request might return `404 Not Found`, but the intended state change remains the same.

Important distinctions:

* `GET` should be safe: it must not request a business-state change.
* `PUT` is generally used when the client knows the target resource URI and supplies its desired representation.
* `PATCH` applies partial changes; its idempotency depends on the patch semantics.
* `POST` is appropriate for many operations, including creating resources when the server assigns their IDs.

**What developers get wrong:** they choose verbs based on which controller method is easiest to implement, rather than on the semantics the client should expect.

### Mistake 3: Confusing statelessness with having no server-side state

REST statelessness does not mean your backend cannot store users, sessions in the general sense, database records, or ongoing business processes.

It means each request must be independently understandable without relying on conversational context retained by the server from previous requests.

For example:

```http
GET /api/orders/123
Authorization: Bearer <access-token>
```

The server can retrieve order 123 from PostgreSQL and validate the access token. That's compatible with REST.

However, imagine the server requires a previous request to establish a hidden, temporary context before it can understand the current request. That introduces dependence on session state.

**A crucial nuance:** authentication state and application state are not the same thing. A server can maintain persistent business state while keeping the interaction stateless.

### Mistake 4: Returning `200 OK` for everything

This is a frequent problem in production APIs.

Bad:

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "success": false,
  "message": "User not found"
}
```

Better:

```http
HTTP/1.1 404 Not Found
Content-Type: application/problem+json

{
  "title": "User not found",
  "status": 404,
  "detail": "No user exists with ID 123."
}
```

Useful status codes include:

* `200 OK` — successful request with a response representation.
* `201 Created` — a resource was created; ideally include a `Location` header.
* `204 No Content` — success with no response body.
* `400 Bad Request` — malformed or otherwise invalid request.
* `401 Unauthorized` — authentication is missing or invalid.
* `403 Forbidden` — the caller lacks permission.
* `404 Not Found` — the requested resource cannot be found.
* `409 Conflict` — the request conflicts with the current resource state.
* `422 Unprocessable Content` — the content is understood but cannot be processed semantically, where this convention is appropriate.

Returning the right status codes allows clients to handle errors without parsing arbitrary application-specific messages.

### Mistake 5: Treating `PUT` and `PATCH` as interchangeable

Suppose a user resource is:

```json
{
  "name": "Rahim",
  "email": "rahim@example.com",
  "role": "USER"
}
```

A client wants to change only the name.

With `PATCH`, the request might be:

```http
PATCH /api/users/123
Content-Type: application/merge-patch+json

{
  "name": "Karim"
}
```

The intended result preserves the email and role.

With `PUT`, the client generally supplies the complete desired representation of the resource. If omitted fields are treated as absent from that representation, they may be cleared or reset according to the API's contract.

The exact update behavior must be documented. Developers often implement `PUT` as a partial update simply because it's convenient, leaving clients unsure what happens to omitted fields.

### Mistake 6: Believing JSON and plural URLs are enough

These conventions are useful:

```http
GET /api/products
GET /api/products/42
```

But neither JSON nor plural nouns is a formal REST requirement.

An API can return JSON, use perfectly named resources, and still lack REST's more distinctive properties.

The most significant example is **HATEOAS — Hypermedia as the Engine of Application State**.

In a hypermedia-driven REST API, responses expose links or other controls that tell the client which transitions are available.

For example:

```json
{
  "id": 42,
  "status": "PENDING",
  "_links": {
    "self": {
      "href": "/api/orders/42"
    },
    "cancel": {
      "href": "/api/orders/42/cancellation"
    }
  }
}
```

The client can discover the cancellation link from the response instead of assuming every order can always be cancelled.

This is a core part of REST's uniform-interface constraint. Most APIs commonly called "REST APIs" do not implement full hypermedia-driven interactions; they're more accurately described as REST-like HTTP APIs.

That doesn't automatically make them bad APIs. It just means the label is being used more loosely than Fielding's original architectural definition.

## 4. The subtle mistakes that cause production problems

Beyond the usual naming and verb mistakes, these deserve particular attention.

**Authorization is not implied by resource URLs**

`GET /users/123` must not allow every authenticated user to read user 123. The backend must verify whether the caller is authorized to access that specific resource. Knowing its ID is not permission.

**Retries can duplicate business operations**

If a payment request times out, the client might not know whether the server processed it. Retrying a `POST` can create a second payment. Use idempotency keys or equivalent deduplication for operations that require safe retries.

**Error responses are inconsistent**

One endpoint returns a string, another returns `{ "error": "..." }`, and another exposes a Java exception. Define a consistent error contract, such as [RFC 9457 Problem Details](https://www.rfc-editor.org/rfc/rfc9457).

**Collection endpoints don't scale**

`GET /api/users` that returns a million records is a poor production design. Support pagination, filtering, sorting, sensible limits, and consistent behavior for empty results.

**Resource representations become accidental contracts**

Returning database entities directly couples the API to the persistence model. Use explicit request and response DTOs, define compatibility expectations, and handle API evolution deliberately.

## 5. How I'd assess a REST API in a code review

For a Spring Boot + React project, I'd review these dimensions:

- [ ] **Resource modeling** — Do URLs identify resources, and are relationships represented coherently?
- [ ] **HTTP semantics** — Are methods, status codes, headers, and idempotency used correctly?
- [ ] **Stateless interactions** — Can each request be understood and processed independently of hidden conversational context?
- [ ] **Representations and errors** — Are DTOs, validation, content types, and error formats consistent?
- [ ] **Security** — Are authentication, resource-level authorization, input validation, and sensitive data exposure handled correctly?
- [ ] **Operational quality** — Are pagination, caching, rate limiting, observability, and retry behavior designed appropriately?
- [ ] **Evolution and discoverability** — Are changes backward-compatible where required, and are API contracts documented?
- [ ] **REST architectural fidelity** — Does the API implement the uniform interface, including hypermedia controls if claiming strict REST conformance?

## The bottom line

I would separate two questions when evaluating an API:

* **Is it a well-designed HTTP API?** Correct methods, status codes, security, caching, error handling, predictable resource representations, and reliable behavior under retries.
* **Is it strictly RESTful?** Does its architecture satisfy REST's constraints, especially the uniform interface and hypermedia-driven discoverability?

Many production APIs do very well on the first question without satisfying every constraint of the second.

My biggest practical takeaway: don't optimize for making URLs *look* RESTful. Optimize for making the API's behavior predictable, consistent, secure, and evolvable. Correct HTTP semantics are an essential starting point, but they are not the whole architecture.
