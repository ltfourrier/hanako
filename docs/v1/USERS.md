# Terminology

This document uses the following specific terminology:
* `User`: explained further below in the "Resource" section.
* `Entity`: a unique individual, company or organization.

# Resource

The resource represented by the `/users` set of endpoints is the user (or account) itself.

The user represents a set of identification and personal data belonging to an entity. As such, it will contain
identification data for the associated entity, as well as operational data (email, username, password, etc...).

A user is directly tied to an entity - while an entity is not necessarily tied to a user, meaning it can be expected
from an entity to own several users.

To comply with national and international law, all the data contained in a user belongs to the entity it is supposed to
represent, and thus the entity should be able to ask for the fetching or deletion of that data.

# Attributes

An user resource may contain the following attributes:
* `id` (required): A unique identifier for the user resource. Must be non-sequential and unique, and should be randomly
generated at creation time. Cannot be modified by the user.
* `username` (required): A username for the user resource. Must be unique and cannot contain more than 32 characters.
* `email` (required): The email associated with the user resource. Must be unique and cannot contain more than
512 characters.
* `civility` (optional): The non-legal civility that matches the entity associated with a user the most. This attribute
can have the following values:
  * `MR`: Corresponds to a male individual.
  * `MS`: Corresponds to a female individual.
  * `MO`: Corresponds to an individual identifying as neither male nor female.
  * `CI`: Corresponds to a one-person commercial entity (e.g. a freelance).
  * `CP`: Corresponds to a corporation or a business. In legal terms, this corresponds to a commercial partnership
(LP and derivatives), a limited liability company (LLC and derivatives) or a corporation (Corp., Inc. and derivatives).
  * `CO`: Corresponds to a non-profit organization, foundation or association.
  * `GV`: A governmental entity, belonging to a recognized or unrecognized state.
  * `GL`: An official or non-official entity with a legal purpose, such as a tribunal or auditor.
* `firstName` (optional): The first name of the individual, or the first name of the representative for other entities.
Cannot contain more than 512 characters.
* `lastName` (optional): The last name of the individual, or the last name of the representation for other entities.
Cannot contain more than 512 characters.
* `displayName` (optional): The full display name of the entity, if it is different than the `firstName + lastName`
concatenation. Cannot contain more than 2048 characters.
* `salt` (required): The hexadecimal representation of the salt that will be used for hashing the user's password.
Is randomly generated when creating a new user, or when the password is changed (see [annex A.1](#1.-Password-hashing)
for more details). Is not exposed in the API.
* `password` (required): The hash of the user's password, salted with the `salt` attribute. Stored as a hash in the
underlying store. Should not be accessible by the user.
* `status` (required): The status of the user, being one of the following:
  * `STD`: The user is a standard user.
  * `ADM`: The user is an administrator. This status means that the user will be able to bypass any user rights or
  groups system that might be used throughout the Tiwind Software ecosystem.
  * `DSB`: The user is disabled and cannot log in or do any operational action.

# Associated endpoints

- `GET /users`: Get a paginated list of users.
- `POST /users`: Create a new user.
- `GET /users/{userId}`: Get the user identified by the `{userId}` path parameter.
- `PATCH /users/{userId}`: Partly modifies the user identified by the `{userId}` path parameter.
- `PUT /users/{userId}`: Completely modifies the user identified by the `{userId}` path parameter, apart from
unmodifiable attributes.
- `DELETE /users/{userId}`: Deletes the user identified by the `{userId}` path parameter.

# Annexes

## A. Security

### 1. Password hashing

Passwords are hashed using the PBKDF2 key-derivation function with the following parameters:
- SHA-512 as the pseudorandom function.
- The bytes represented by the UTF-8 `password` attribute supplied by the user as the password.
- The bytes represented by the `salt` attribute generated by the server as the salt.
- 16,384 as the number of iterations.
- 512 as the bit-length of the resulting derived key.