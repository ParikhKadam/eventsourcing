[![Build Status](https://github.com/pyeventsourcing/eventsourcing/actions/workflows/runtests.yaml/badge.svg?branch=9.1)](https://github.com/pyeventsourcing/eventsourcing/tree/9.1)
[![Coverage Status](https://coveralls.io/repos/github/johnbywater/eventsourcing/badge.svg?branch=9.1)](https://coveralls.io/github/johnbywater/eventsourcing?branch=9.1)
[![Documentation Status](https://readthedocs.org/projects/eventsourcing/badge/?version=v9.1.4)](https://eventsourcing.readthedocs.io/en/v9.1.4/)
[![Latest Release](https://badge.fury.io/py/eventsourcing.svg)](https://pypi.org/project/eventsourcing/)


# Event Sourcing in Python

A library for event sourcing in Python.

*"totally amazing and a pleasure to use"*

Please [read the docs](https://eventsourcing.readthedocs.io/). See also [extension projects](https://github.com/pyeventsourcing).

[![Event sourcing in Python](images/Cupid-foot-686x343.jpeg)](https://eventsourcing.readthedocs.io/)


## Installation

Use pip to install the [stable distribution](https://pypi.org/project/eventsourcing/)
from the Python Package Index. Please note, it is recommended to
install Python packages into a Python virtual environment.

    $ pip install eventsourcing


## Synopsis

Use the library's `Aggregate` base class to define event-sourced aggregates.
Use the `@event` decorator on command methods to define aggregate events.

```python
from eventsourcing.domain import Aggregate, event


class World(Aggregate):
    @event('Created')
    def __init__(self, name):
        self.name = name
        self.history = []

    @event('SomethingHappened')
    def make_it_so(self, what):
        self.history.append(what)
```

Use the library's `Application` class to define an event-sourced application.
Add command and query methods that use event-sourced aggregates.

```python
from eventsourcing.application import Application


class Universe(Application):
    def create_world(self, name):
        world = World(name)
        self.save(world)
        return world.id

    def make_it_so(self, world_id, what):
        world = self.repository.get(world_id)
        world.make_it_so(what)
        self.save(world)

    def get_history(self, world_id):
        world = self.repository.get(world_id)
        return world.history
```

Construct an application object by calling the application class.

```python
application = Universe()
```

Evolve the state of the application by calling the
application command methods.

```python
world_id = application.create_world('Earth')
application.make_it_so(world_id, 'dinosaurs')
application.make_it_so(world_id, 'trucks')
application.make_it_so(world_id, 'internet')
```

Access the state of the application by calling the
application query methods.

```python
history = application.get_history(world_id)
assert history == ['dinosaurs', 'trucks', 'internet']
```

Configure an application by setting environment variables.

```python
application = Universe(
    env={
        'FACTORY_TOPIC': 'eventsourcing.sqlite:Factory',
        'SQLITE_DBNAME': ':memory:',
    }
)
```

See the library's [documentation](https://eventsourcing.readthedocs.io/)
for more information.

## Features

**Aggregates and applications** — base classes for event-sourced aggregates
and applications. Suggests how to structure an event-sourced application. All
classes are fully type-hinted to guide developers in using the library.

**Flexible event store** — flexible persistence of aggregate events. Combines
an event mapper and an event recorder in ways that can be easily extended.
Mapper uses a transcoder that can be easily extended to support custom
model object types. Recorders supporting different databases can be easily
substituted and configured with environment variables.

**Application-level encryption and compression** — encrypts and decrypts events inside the
application. This means data will be encrypted in transit across a network ("on the wire")
and at disk level including backups ("at rest"), which is a legal requirement in some
jurisdictions when dealing with personally identifiable information (PII) for example
the EU's GDPR. Compression reduces the size of stored aggregate events and snapshots, usually
by around 25% to 50% of the original size. Compression reduces the size of data
in the database and decreases transit time across a network.

**Snapshotting** — reduces access-time for aggregates that have many events.

**Versioning** - allows changes to be introduced after an application
has been deployed. Both aggregate events and aggregate snapshots can be versioned.

**Optimistic concurrency control** — ensures a distributed or horizontally scaled
application doesn't become inconsistent due to concurrent method execution. Leverages
optimistic concurrency controls in adapted database management systems.

**Notifications and projections** — reliable propagation of application
events with pull-based notifications allows the application state to be
projected accurately into replicas, indexes, view models, and other applications.
Supports materialized views and CQRS.

**Event-driven systems** — reliable event processing. Event-driven systems
can be defined independently of particular persistence infrastructure and mode of
running.

**Detailed documentation** — documentation provides general overview, introduction
of concepts, explanation of usage, and detailed descriptions of library classes.
All code is annotated with type hints.

**Worked examples** — includes examples showing how to develop aggregates, applications
and systems.



## Extensions

The GitHub organisation
[Event Sourcing in Python](https://github.com/pyeventsourcing)
hosts extension projects for the Python eventsourcing library.
There are projects that support ORMs such as [Django](https://github.com/pyeventsourcing/eventsourcing-django) and [SQLAlchemy](https://github.com/pyeventsourcing/eventsourcing-sqlalchemy).
There are projects supporting databases such as AxonDB, DynamoDB,
EventStoreDB, and Apache Kafka. Another project supports
transcoding domain events with Protocol Buffers rather than JSON.
There are also projects that provide examples of using the
library with such things as [FastAPI](https://github.com/pyeventsourcing/example-fastapi),
Flask, and serverless.

## Project

This project is [hosted on GitHub](https://github.com/pyeventsourcing/eventsourcing).

Please register questions, requests and
[issues on GitHub](https://github.com/pyeventsourcing/eventsourcing/issues),
or post in the project's Slack channel.

There is a [Slack channel](https://join.slack.com/t/eventsourcinginpython/shared_invite/enQtMjczNTc2MzcxNDI0LTJjMmJjYTc3ODQ3M2YwOTMwMDJlODJkMjk3ZmE1MGYyZDM4MjIxODZmYmVkZmJkODRhZDg5N2MwZjk1YzU3NmY)
for this project, which you are [welcome to join](https://join.slack.com/t/eventsourcinginpython/shared_invite/enQtMjczNTc2MzcxNDI0LTJjMmJjYTc3ODQ3M2YwOTMwMDJlODJkMjk3ZmE1MGYyZDM4MjIxODZmYmVkZmJkODRhZDg5N2MwZjk1YzU3NmY).

Please refer to the [documentation](https://eventsourcing.readthedocs.io/) for installation and usage guides.
