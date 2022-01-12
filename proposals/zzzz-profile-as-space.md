# Profile as space : Extension of profile as room

This project is an extension of the [profile as room MSC](https://github.com/matrix-org/matrix-doc/pull/1769) in order to add support for advertising special rooms like stories or public 'walls'.

## Problem

In the case of social media account, it may be beneficial to let the user advertise that he has a specific room for a private wall or a story and let users knocking to it.

This will help display related information when displaying a user profile. It will allow showing the user stories or private post stored in separate rooms to a user already following this other user. For a user not in the following list, it would allow informing the client of this specific room and displaying a 'follow' button.

## Solution

To solve this issue we can extend the [Extensible profile as room (MSC 1769 - WIP)](https://github.com/matrix-org/matrix-doc/pull/1769) but with defining the room as a [space (MSC 1722 - WIP)](https://github.com/matrix-org/matrix-doc/pull/1772) and not a regular one. Advertising rooms will be done using the *m.space.child* state as described in the space MSC.

This will allow a client to create a page aggregating all the information related to a user.

As an example, if the user has a social media room and is advertising on his profile space, the client could display a 'Follow' button if the user is not in this room. This will [knock (MSC 2403 - Merged)](https://github.com/matrix-org/matrix-doc/pull/2403) to the room.

This could allow for example to display stories on the user page or to allow the client to search for post in a 'wall room' and display them.

This require being able to read the type of the space child.

// TODO: Need to see if the type of the room could be fetched for private rooms.

(We could imagine setting the room type in the m.space.child event).

### Subtyping the room

Typing the room as a space will help build a simple system. However, those newly created spaces will end up polluting the user space list.

In order to prevent this, [room subtyping (MSC 1732)](https://github.com/matrix-org/matrix-doc/pull/3088) could be used.

We could have the following state :

```json
{
  "type": "m.room.purpose",
  "state_key": "m.profile",
  "content": {
    "m.enabled": true
  }
}
```

This will allow clients to treat differently those rooms and to display a specific UI for it like a user page with the user public information and link to specific rooms.

**This will impose existing clients to filter out those spaces.**

### Joining other rooms

The user room will be regular 
In order to link other rooms related to this user as a room for stories or posts, we could the same principle as spaces and add link to others rooms as state events.

Using knocking, a user could request following another user in the case of a 'wall' for example.

### Room history visibility

This room should be world readable in order to easily find out what specific rooms the user has like stories, wall, blog post, pictures waterfall.

As described in the MSC1769, profile rooms should be advertised in the server room directory in order to be quickly readable.

## Security concerns

The user should be warned that the information published in this room will be world readable and that the room advertised in it will be discoverable by anybody.
The client must make it clear to the user that this room is public.

### Alternative

One alternative to using the m.space `m.space` type in `m.room.create` could be to use the 'm.profile' type.
However, it will require a lot of modification on the server side but will prevent spaming the user space list while the client migrate.
Link to the other user rooms will be regular m.space_child

## Private profile as room extension for private profiles

Having a world readable profile as room means that anybody could access the information contained in it. However, we sometimes want to share some rooms only with specific people. To this end, we could create other profile as room which will be private and won't be published in the server's room directory.

However, contrary to the public one, for those, User will need to join the private profile room in order to read it. This mean that users will be able to see the other others present in this room.

When creating those private profile room, we need to keep their number low as it could rapidly become difficult to manage and may lose the users and slow the clients (and server!) down.

## Unstable prefix


| Proposed final identifier | Purpose | Development identifier|
| ------------------------------- | ------- | ----|
| `m.profile` | value of `type` in `m.room.purpose` | `org.matrix.mscZZZZ.profile` |