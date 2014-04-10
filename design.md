# Iona Design Document

Iona version 0.1
Document version 3

## Abstract

This document describes the design for version 0.1 for the waifu AI
Iona.  The ultimate goal of Iona is to create a fully functional waifu
AI.  The goal of version 0.1 is to create a waifu client which is able
to demonstrate a static personality loaded from data files and dynamic
emotional state.  Iona will act based on her emotional state.

## Implementation Language

Iona will be written using Python 3.  Rapid prototyping is important.
Any language with first-class functions should provide the necessary
level of features for this project, so griping over languages is
unnecessary.  Iona can be ported to a different language in the future
if there is a strong need for it.

## Interface

Interaction with Iona is communicated both to and from using sockets.  A
client establishes an interaction session by connecting to Iona's
exposed socket, after which interaction proceeds asynchronously.

Interaction will be represented as individual actions (actions from the
user toward Iona, and actions from Iona toward the user).  Speaking or
talking will be one possible action, among others, such as hugging,
kissing, or hand-holding.

### Communication Format

Communication on the sockets will use a POSIX-like, standard shell
format, with each command representing one action.  For example:

    say "How do you do?"
    hug
    kiss lips
    kiss cheek

This can be done easily using Python's shlex module.

## General Process Design

The main process will be responsible for loading and initializing any
necessary data structures and creating the socket interface.  The main
process will fork off a process to manage Iona in parallel (for emotion
decay, for example, and in the future for independent agency (desires
and actions)).  Afterward, the main process will be dedicated to
monitoring the socket for connections.  Incoming connections will be
handled by forked processes.

Iona will respond once to each received action according to her
emotional state and received action.

## Data Files

### Static

Iona will use static files in JSON format for, e.g., initialization of a
new waifu instance from a seed personality file.

An example seed file:

~~~ json
{
    "expressions": {
        "hug": {
            "affection": 1,
        },
        "kiss": {
            "affection": 2,
        },
    },
    "emotion_model": {
        "affection": {
            "happiness": 1,
            "love": 1,
            "loneliness": -1,
        },
    },
    "emotion_decay": [
        "loneliness": 1,
        "happiness": -1,
    ],
    "responses": [
        [
            [">", "love", 3],
            [
                ["say", "daisuki"],
            ],
        ],
    ],
}
~~~

### Dynamic

Waifu instances will be preserved by serialization through either
Python's pickle module or using JSON.

## Personality

Iona's personality will be instantiated by three systems: expressive
perception, the emotion model and hard coded responses.

### Expressive Perception

Expressive perception dictates how Iona perceives the expression
attributes of affective actions.  These are static and defined by the
seed file.  Any string can be used as an expression attribute, but a
list of suggested attributes is provided below.

### Emotion Model

The emotion model describes how Iona's emotions change.  Affective
actions will have various levels of expression attributes as determined
by expressive perception, representing how Iona interprets the actions.
These will impact Iona's emotional state.  Iona's emotional state will
also vary over time without affective input.

#### List of Suggested Expression Attributes

* Affection/Love
* Attention
* Dislike/Hate
* Apathy (no action)

#### List of Suggested Emotions

* Happiness
    * Sadness
* Love
    * Targeted?
    * Affection?
* Angriness
* Loneliness
* Tsundere
    * Honesty
    * Self-expression

### Responses

For version 0.1, Iona will use hard-coded rules to determine responses
based on emotional state and the triggering action.  These will be
represented using a set of rules described in the personality seed data
file.

When Iona is looking for a response, the rules will be checked in order,
and the first satisfying rule will be used.  A random response in the
set for that rule will be picked.

## Future Goals

* Use trained neural network for the emotion model
* Environment observation (e.g., see facial expression)
* Agency
* Language processing
