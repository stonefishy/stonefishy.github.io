---
title: Exploring Java 21 New Features
date: 2025-12-06 10:21:13
categories: Backend
tags: [Java]
---

Java has been moving to a faster release cadence for a few years now, but the Long-Term Support (LTS) releases are still the ones most teams standardize on. Java 21, released on September 19, 2023, is the first LTS after Java 17 and brings a very solid set of language, library, and runtime improvements.

{% image /assets/images/java/java-21.png, width=600px, alt="Java 21" %}

Instead of listing every small change, this post focuses on a handful of JEPs that are likely to influence everyday application code:

- Record patterns (JEP 440)
- Pattern matching for `switch` (JEP 441)
- String templates (JEP 430)
- Virtual threads (JEP 444)
- Sequenced collections (JEP 431)
- Key Encapsulation Mechanism API (JEP 452)

We’ll walk through each feature with small, self‑contained examples that you can easily adapt into real projects.

## 1. Record Patterns (JEP 440)

Record patterns extend pattern matching so that you can *deconstruct* record instances directly in the pattern, instead of first binding a variable and then calling accessor methods.

Imagine a simple record representing a user:

```java
record User(String name, int age) {}

static String describeUser(Object value) {
    if (value instanceof User(String name, int age)) {
        return age >= 18
                ? "Adult user: " + name
                : "Minor user: " + name;
    }
    return "Not a user";
}
```

Here `User(String name, int age)` is a record pattern. The pattern both checks the type and exposes the components as local variables in one step.

Record patterns also compose nicely with other records, allowing nested deconstruction:

```java
record Address(String city, String country) {}
record Customer(User user, Address address) {}

static String formatCustomer(Object value) {
    if (value instanceof Customer(User(String name, int age),
                                  Address(String city, String country))) {
        return "Customer %s (%d) from %s, %s".formatted(name, age, city, country);
    }
    return "Unknown customer";
}
```

The structure of the pattern mirrors the structure of the data, which makes read‑only data processing code much easier to follow.

## 2. Pattern Matching for `switch` (JEP 441)

Pattern matching has also arrived for `switch` expressions and statements. You can now switch on almost any reference type and use type patterns, record patterns, and guards (`when`) directly in the `case` labels.

Consider a tiny command model:

```java
sealed interface Command permits Start, Stop, Pause {}

record Start(int port) implements Command {}
record Stop(String reason) implements Command {}
record Pause(java.time.Duration duration) implements Command {}
```

With Java 21, handling all commands in one place can look like this:

```java
static String handle(Command command) {
    return switch (command) {
        case null -> "No command provided";
        case Start(int port) -> "Starting on port " + port;
        case Stop(String reason) -> "Stopping: " + reason;
        case Pause(java.time.Duration d) when d.isZero() -> "Pause ignored";
        case Pause(java.time.Duration d) -> "Pausing for " + d.toSeconds() + "s";
    };
}
```

Key points:

- `case null` lets you handle `null` directly inside the `switch`.
- Record patterns in `case` labels (`Start(int port)`) deconstruct values on the fly.
- `when` clauses allow additional boolean checks, keeping complex conditions on the left of the arrow and business logic on the right.

This style reads much closer to a data‑driven dispatcher than a long `if/else if` chain.

## 3. String Templates (JEP 430)

Composing strings from variables is something every Java application does, and until now we mostly relied on `+`, `StringBuilder`, `String::format`, or `MessageFormat`. String templates introduce a new syntax that keeps the template and embedded expressions together, processed by a *template processor* such as `STR`.

Basic interpolation becomes much more readable:

```java
String endpoint = "/api/orders";
int status = 201;
String message = STR."[OK] \{endpoint} responded with \{status}";
// [OK] /api/orders responded with 201
```

Text blocks work as templates as well, which is great for JSON or SQL:

```java
long id = 42L;
String name = "Alice";

String json = STR."""
        {
          "id": \{id},
          "name": "\{name}"
        }
        """;
```

The `STR` processor is provided by the platform and automatically imported. Under the hood it receives a structured `StringTemplate` object (fragments + values), which allows custom processors to validate or transform data before producing a final result (for example, building a safe SQL `PreparedStatement` or a JSON object).

## 4. Virtual Threads (JEP 444)

Virtual threads are one of the headline features in recent Java versions, and Java 21 finally makes them a permanent part of the platform. They are *cheap* threads managed by the JVM, not by the OS, designed for high‑concurrency, blocking‑style code.

Running thousands of independent tasks becomes straightforward:

```java
static void fetchAll(java.util.List<java.net.URI> uris) throws Exception {
    try (var executor = java.util.concurrent.Executors.newVirtualThreadPerTaskExecutor()) {
        var futures = uris.stream()
                .<java.util.concurrent.Callable<String>>map(uri -> () ->
                        new String(uri.toURL().openStream().readAllBytes()))
                .map(executor::submit)
                .toList();

        for (var f : futures) {
            // Each fetch runs in its own virtual thread
            System.out.println("Downloaded: " + f.get().length() + " bytes");
        }
    }
}
```

Some highlights in Java 21:

- Virtual threads always support thread‑local variables.
- Threads created via `Thread.ofVirtual()` (the `Thread.Builder` API) are observable in the new style thread dump.

The main takeaway: you can keep the familiar "one request = one thread" model while scaling to far more concurrent operations than traditional platform threads allow.

## 5. Sequenced Collections (JEP 431)

Collections like `List`, `Deque`, `LinkedHashSet`, and sorted collections have always had an encounter order, but it wasn’t expressed by a common interface. Java 21 fills that gap with three new interfaces:

- `SequencedCollection<E>`
- `SequencedSet<E>`
- `SequencedMap<K, V>`

This gives us a uniform way to talk about "first", "last", and "reversed" across many collection types.

### 5.1 SequencedCollection

`List` and `Deque` now extend `SequencedCollection`, which adds operations such as `getFirst`, `getLast`, and `reversed()`:

```java
java.util.SequencedCollection<String> queue = new java.util.LinkedList<>();
queue.addLast("task-1");
queue.addLast("task-2");
queue.addFirst("urgent");

String first = queue.getFirst();    // "urgent"
String last  = queue.getLast();     // "task-2"

for (String name : queue.reversed()) {
    System.out.println(name);       // iterates from last to first
}
```

### 5.2 SequencedSet and SequencedMap

`LinkedHashSet` and `SortedSet` now participate in `SequencedSet`, while `LinkedHashMap` and `SortedMap` participate in `SequencedMap`.

For maps, you can explicitly put entries at the beginning or end and still iterate in order:

```java
java.util.SequencedMap<String, Integer> scores =
        (java.util.SequencedMap<String, Integer>) new java.util.LinkedHashMap<>();

scores.putLast("Alice", 10);
scores.putLast("Bob", 8);
scores.putFirst("System", Integer.MAX_VALUE);

var lastEntry = scores.lastEntry();      // The most recently added at the end

for (var e : scores.reversed().sequencedEntrySet()) {
    System.out.println(e.getKey() + " -> " + e.getValue());
}
```

The key benefit is a clean, common API for working with ordered data structures without giving up the specific collection type you need.

## 6. Key Encapsulation Mechanism API (JEP 452)

Modern cryptography often separates *how* you protect application data (symmetric crypto) from *how* you agree on the symmetric key (asymmetric crypto). A Key Encapsulation Mechanism (KEM) is a standard way to derive and transport a shared secret using public‑key cryptography.

Java 21 adds the `javax.crypto.KEM` API so applications and security providers can work with KEM algorithms in a consistent way.

At a high level, the flow looks like this:

```java
// Receiver side: generate and publish a public key
KeyPairGenerator generator = KeyPairGenerator.getInstance("DHKEM");
KeyPair receiverKeyPair = generator.generateKeyPair();
// receiverKeyPair.getPublic() is distributed to senders

// Sender side: encapsulate
javax.crypto.KEM kem = javax.crypto.KEM.getInstance("DHKEM");
var encapsulator = kem.newEncapsulator(receiverKeyPair.getPublic());
javax.crypto.KEM.Encapsulated encapsulated = encapsulator.encapsulate();

javax.crypto.SecretKey senderSecret = encapsulated.key();
byte[] encapsulationMessage = encapsulated.encapsulation();

// Receiver side: decapsulate
var decapsulator = kem.newDecapsulator(receiverKeyPair.getPrivate());
javax.crypto.SecretKey receiverSecret = decapsulator.decapsulate(encapsulationMessage);

// senderSecret and receiverSecret now represent the same shared secret
```

The `SecretKey` produced by KEM is then typically passed into a symmetric cipher (for example AES‑GCM) or a higher‑level protocol like HPKE or TLS.

## 7. Closing Thoughts

Java 21 doesn’t radically change how you write Java code, but it does smooth a lot of edges:

- Pattern‑oriented code becomes much clearer with record patterns and `switch` patterns.
- String templates reduce boilerplate around dynamic text.
- Virtual threads enable highly concurrent, blocking‑style code without switching to a reactive framework.
- Sequenced collections give us long‑missing, first‑class support for ordered data structures.
- The KEM API future‑proofs Java’s crypto stack for post‑quantum designs.

If you’re still on Java 8 or 11, Java 21 is a strong LTS target: you get years of runtime improvements and a much more expressive language, without throwing away your existing code.

By the way, the latest Java LTS is now Java 25 which was released in September 2025. Stay tuned for more details in the upcoming posts!