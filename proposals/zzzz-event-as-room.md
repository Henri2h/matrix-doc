# MSC ZZZZ : Calendar event as room

Most modern communication systems offer a way to organize events. This
proposal try to find a way about how we could add such a functionality into matrix.

This proposal take inspiration from 
[MSC1235: Proposal for Calendar Events](https://github.com/matrix-org/matrix-doc/issues/1235)
and especially from last example from
[@aran4 gist - matrixCal: a saner(?) mapping of iCal into JSON than jCal](https://gist.github.com/ara4n/2ee771eb69580125e5b5756872a842be).


## Proposal

We propose to define a custom room to store data about an event. This
would enable the event organizer to invite people, inform them about the
detail of the event: description, date, location and so on. It will also
make them able to let every member send his attendance status.


**Room type:**
```
m.calendar_event
```

Then, for the calendar event details, the goal is to reuse most of the event
already defined states.

| state           | description               |
| --------------- | ------------------------- |
| `m.room.name`   | The name of the event     |
| `m.room.avatar` | The thumnail of the event |
| `m.room.topic`  | The event description     |

However, we need to define custom states which will be
specifically used in our context.


| type                         | description                                                                                                                                              |
| ---------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `m.calendar.event`           | The specific calendar data about the event. Like date ect…                                                                                               |
| `m.calendar.location`        | The calendar event location as defined in [MSC3488: Extending events with location data]([**NB:**](https://github.com/matrix-org/matrix-doc/pull/3488)). |
| `m.calendar.attendance_poll` |


**NB:** We have decided to prefix all the custom states by `m.calendar.` instead of `m.room.` to make it clear that those states are specific to this room type.

### Custom states

#### `m.calendar.event`


**Example**:
```json
{
    "date_start_ts": 1645367974972,
    "date_end_ts": 1645439465556
}
```

#### `m.calendar.location`

Store the actual location of the event.

**Example**
```json
{
    "uri": "geo:51.5008,0.1247;u=35",
    "description": "Our destination",
    "zoom_level": 15,   
}
```

#### `m.calendar.attendance_poll`

This state will be used to link to the actual attendance poll.

**Example**
```json
{
    "id": <the matrix eventID>
}
```

### Event attendance

In order to let the user indicate if they will come or not (or if they
are interested), we can create a poll
([MSC3381: Polls (mk II)](https://github.com/matrix-org/matrix-doc/pull/3381))
and pinned it with the `m.room.attendance_poll` state.

However, the poll start event must contain (exactly ?) the 3 followings
answers ids : `going`, `interseted` and `declined`.

**Example:**
```json
{
    "org.matrix.msc3381.poll.start": {
        "kind": "org.matrix.msc3381.poll.disclosed",
        "question": {
        "org.matrix.msc1767.text": "Do you come ?"
        },
        "answers": [
        {
            "id": "going",
            "org.matrix.msc1767.text": "Going"
        },
        {
            "id": "interested",
            "org.matrix.msc1767.text": "Interested"
        },
        {
            "id": "declined",
            "org.matrix.msc1767.text": "Declined"
        }
        ],
        "max_selections": 1
    }
}
```

The `m.calendar.attendance_poll` will be used to store the event id link to
the actual poll start event. This will allow using server side aggregation
([MSC2675: Serverside aggregations of message relationships](https://github.com/matrix-org/matrix-doc/pull/2675))
to render the poll start directly and not having to parse the calendar
event room timeline.

## Unstable types

// TODO
