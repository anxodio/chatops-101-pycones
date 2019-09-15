# Chatops 101

## with opsdroid

---

## Hello! 👋

I am Àngel, a.k.a. [@anxodio](https://twitter.com/anxodio)

_Python Developer / Data Engineer at [@HolaluzEng](https://twitter.com/holaluzeng)_

---

## What the f#!@ is chatops?

> ChatOps is the use of chat clients, chat-bots and real-time communication tools to facilitate how software development and operation tasks are communicated and executed.

_Abhinav Jain, works at Accenture and sometimes answers questions like this on Quora._

<!-- .element: class="small-text" -->

Note:
Que quiere decir Chatops? Es la conjunción de Chat y Operaciones, y lo que hace
es integrar procesos de desarrollo y operaciones en las herramientas de chat del equipo.
Dicho de otra forma, se materializa en la integración de un robot en Slack (o similar)
que colabora con el equipo y puede hacer deploys, reiniciar cosas, hacer backups,
levantar máquinas... hasta donde nos llegue la imaginación.

---

## So here is opsdroid

![Opsdroid logo](img/opsdroid_logo.png)

Opsdroid is an open source ChatOps bot framework with the moto:
**Automate boring things!**

---

## But why Opsdroid?

**Simple**: Easy to install, configure and deploy.

<!-- .element: class="fragment fade-in-then-semi-out" -->

**Powerful**: Works out of the box with Slack, Telegram, Facebook… and with various NLU platforms.

<!-- .element: class="fragment fade-in-then-semi-out" -->

**Extensible**: Add your custom skills in few Python lines.

<!-- .element: class="fragment fade-in-then-semi-out" -->

---

## Show me more

### Let's see how Opdroid works

---

## Skills

Skills are modules which define what actions opsdroid should perform based on different chat messages.

They’re modular and can be shared as plugins between differents opsdroid instances.

---

## Skills

```python
class HelloSkill(Skill):

  @match_regex(r'hi|hello|hey|hallo')
  async def hello(self, message: Message):
    text = random.choice(
      ["Hi {}", "Hello {}", "Hey {}"]
    ).format(message.user)
    await message.respond(text)

  @match_regex(r'bye( bye)?|see y(a|ou)|au revoir|I(\')?M off')
  async def goodbye(self, message: Message):
    text = random.choice(
      ["Bye {}", "See you {}", "Au revoir {}"]
    ).format(message.user)
    await message.respond(text)
```

---

## Parsers

Parsers match an incoming message to a skill.

Actual parsers:
_Regex, Parse_Format, Crontab, Webhook, Always and NLU parsers_

---

## Parsers

```python
class MyNameSkill(Skill):

  @match_regex(r'my name is (?P<name>\w+)')
  async def my_name_is(self, message: Message):
    name = message.regex.group('name')
    await message.respond(f'Wow, {name} is a nice name!')
```

<!-- .element: class="fragment fade-in-then-semi-out" -->

```python
class MyNameSkill(Skill):

  @match_parse('my name is {name}')
  async def my_name_is(self, message: Message):
    name = message.parse_result['name']
    await message.respond(f'Wow, {name} is a nice name!')
```

<!-- .element: class="fragment fade-in-then-semi-out" -->

---

## Parsers

```python
class ClockSkill(Skill):

    @match_crontab('0 * * * *')
    @match_regex(r'what time is it\?')
    async def speaking_clock(self, message: Message):
      connector = self.opsdroid.default_connector
      default_room = connector.default_room

      if message is None:
        message = Message('', None, default_room, connector)

      await message.respond(strftime("It's %H:%M", gmtime()))
```

---

## Connectors

Connectors are modules for connecting opsdroid to your specific chat service.

Actual connectors:
_Shell, Websocket, Slack, Telegram, Twitter, Facebook, Github, Ciscospark and Matrix_

---

## Databases

Database modules connect opsdroid to your chosen database and allow skills
to store information (outside memory) between messages.

Actual databases:
_Sqlite, Mongo and Redis_

---

## Constraints

Constraints are decorators for your skill functions which prevent the skill
from being called even if it is matched by a matcher.

Actual constraints:
_constrain_rooms, constrain_users, constrain_connectors_

---

## Constraints

```python
class MySkill(Skill):

  @match_regex(r'hi')
  @constrain_users(['alice', 'bob'])
  async def my_name_is(self, message: Message):
    await message.respond('Hey')
```

---

## Config

```yaml
parsers:
  - name: witai
    enabled: true
    access-token: "mysecretwittoken"
    min-score: 0.7

connectors:
  - name: slack
    token: "mysecretslacktoken"

skills:
  - name: hello
  - name: myawesomeskill
    repo: "https://github.com/username/myawesoneskill.git"
```

Note:

- Explicar que podriamos añadir BBDD (no hace falta)
- Explicar diferentes conectores a la vez
- Explicar skills oficiales y propias
- Explicar constraints siempre activas

---

## Yeah, but...

### You said something about NLU?

---

## What the heck is NLU?

> Natural language understanding (NLU) is a branch of artificial intelligence (AI) that uses computer software to understand input made in the form of sentences in text or speech format.

_Margaret Rouse in WhatIs.com_

<!-- .element: class="small-text" -->

---

## NLU parsers

Opsdroid connects with some NLU services:

- **Wit.ai** (Facebook service)
- **Dialogflow** (Google service)
- **Luis.AI** (Microsoft service)
- **Recast.AI** (SAP service)
- **Rasa** (Open Source)

---

## Wit.AI example

![Wit.AI example](img/wit.gif)

---

## Wit.AI example

A message "_restart production, please!_" is sent to Wit.ai

```json
{
  "confidence": 0.783,
  "intent": "restart",
  "_text": "restart production, please!",
  "entities": {
    "environment": [
      {
        "value": "production"
      }
    ]
  }
}
```

---

## Wit.AI example

```python
class RestartSkill(Skill):

  @match_witai('restart')
  async def restart(self, message: Message):
    entities = message.witai['entities']
    environments = entities['environment']
    if not environments:
      await message.respond('Please specify an environment.')
      return

    environment = environments['0']['value']
    await _do_restart(environment)
    await message.respond(f'{environment} restarted!.')
```

---

# Thanks! 🤗

Any questions?

_Keep in touch -> [@anxodio](https://twitter.com/anxodio)_

![Holaluz](img/holaluz_white.png) is looking for great people like you, join us! [holaluz.com/jobs](http://holaluz.com/jobs)

<!-- .element: class="small-text holaluz-jobs" -->
