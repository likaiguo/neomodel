===============
Getting started
===============

Connecting
==========

Set the location of neo4j via an environment variable (default is http://localhost:7474/db/data/)::

    export NEO4J_REST_URL=http://user:password@localhost:7474/db/data/

Definition
==========

In the example below, there is one type of relationship present `IS_FROM`,
we are defining two different ways for traversing it
one accessible via Person objects and one via Country objects::

    from neomodel import (StructuredNode, StringProperty, IntegerProperty,
        RelationshipTo, RelationshipFrom)

    class Country(StructuredNode):
        code = StringProperty(unique_index=True, required=True)

        # traverse incoming IS_FROM relation, inflate to Person objects
        inhabitant = RelationshipFrom('Person', 'IS_FROM')


    class Person(StructuredNode):
        name = StringProperty(unique_index=True)
        age = IntegerProperty(index=True, default=0)

        # traverse outgoing IS_FROM relations, inflate to Country objects
        country = RelationshipTo(Country, 'IS_FROM')

We can use the `Relationship` class if we don't want to specify a direction.

Create, Save, Delete
====================

Using convenient methods::

    jim = Person(name='Jim', age=3).save()
    jim.age = 4
    jim.save() # validation happens here
    jim.delete()
    jim.refresh() # reload properties from neo

Relationships
=============

Working with relationships::

    germany = Country(code='DE').save()
    jim.country.connect(germany)

    if jim.country.is_connected(germany):
        print("Jim's from Germany")

    for p in germany.inhabitant.all()
        print(p.name) # Jim

    len(germany.inhabitant) # 1

    # Find people called 'Jim' in germany
    germany.inhabitant.search(name='Jim')

    jim.country.disconnect(germany)
