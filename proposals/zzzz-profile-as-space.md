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

This requires being able to read the type of the space child.

// TODO: Need to see if the type of the room could be fetched for private rooms.
// Seems that private room are not displayed in room hierarchy.

The [room hierarchy endpoint](https://spec.matrix.org/v1.2/client-server-api/#get_matrixclientv1roomsroomidhierarchy) can then be used to get the spaces childs without joining.

### Subtyping the room

Typing the room as a space will help build a simple system. However, those newly created spaces will end up polluting the user space list.

In order to prevent this, [room types (MSC 1840)](https://github.com/jfrederickson/matrix-doc/blob/proposal/room-types/proposals/1840-room-types.md) could be used.

We could have the following state :

```json
{
  "type": "m.room.type",
  "content": {
    "m.type": "m.profile"
  }
}
```

This will allow clients to treat differently those rooms and to display a specific UI for it like a user page with the user public information and link to specific rooms.

**This will impose existing clients to filter out those spaces.**

## Proposal

So we propose to use spaces. In order to be able to read the space content without joining the room we need

* define the history visibility as `world_readable`
* define a predictable room alias to allow searching for this space.

### Room history visibility

This room should be world readable in order to easily find out what specific rooms the user has like stories, wall, blog post, pictures waterfall.



### Room alias

The space should be published in the server room directory.
We propose the following alias:

```
#m.profile.@henri2h:carnot.cc
```



## Security considerations

The user should be warned that the information published in this room will be world readable and that the room advertised in it will be discoverable by anybody.
The client must make it clear to the user that this room is public.

We need a way to prevent other user from taking this specific alias. This should be enforced on server side.
However, client should check if the space they are viewing has been created by the right user.
### Alternative

One alternative to using the m.space `m.space` type in `m.room.create` could be to use the 'm.profile' type. This could help indicate more clearly the goal of
this room. This could be an answer to prevent the space to interfere with the 'regular' spaces.
However, it will require a lot of modification on the server side and new ways
to distinguish chat and non chat rooms should be added as matrix is tending
increasingly to have non chat usage. (Beyond Chat ? :D)

## Private profile as room extension for private profiles

Currently, this proposal is only focused on public, world_readable rooms.
However, nothing prevent an actual user to put is profile space as private.
One need to watch out for the complexity that will bring multiple profile spaces.

## Unstable prefix


| Proposed final identifier | Purpose                             | Development identifier       |
| ------------------------- | ----------------------------------- | ---------------------------- |
| `m.profile`               | value of `type` in `m.room.type` | `org.matrix.mscZZZZ.profile` |