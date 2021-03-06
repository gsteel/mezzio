# Emitters

To simplify the usage of Mezzio, we added the `run()` method, which handles
the incoming request, and emits a response.

The latter aspect, emitting the response, is the responsibility of an
[emitter](https://docs.laminas.dev/laminas-diactoros/emitting-responses/).
An emitter accepts a response instance, and then does something with it, usually
sending the response back to a browser.

Diactoros defines an `EmitterInterface`, and — as of the time we write this — a
single emitter implementation, `Laminas\Diactoros\Response\SapiEmitter`, which
sends headers and output using PHP's standard SAPI mechanisms (the `header()`
method and the output buffer).

We recognize that there are times when you may want to use alternate emitter
implementations; for example, if you use [React](http://reactphp.org), the SAPI
emitter will likely not work for you.

To facilitate alternate emitters, we offer two facilities:

- First, `Application` composes an emitter, and you can specify an alternate
  emitter during instantiation, or via the `Laminas\Diactoros\Response\EmitterInterface`
  service when using the container factory.
- Second, we provide `Mezzio\Emitter\EmitterStack`, which allows you to
  compose multiple emitter strategies; the first to return a value other than
  boolean `false` will cause execution of the stack to short-circuit.
  `Application` composes an `EmitterStack` by default, with an `SapiEmitter`
  composed at the bottom of the stack.

## EmitterStack

The `EmitterStack` is an `SplStack` extension that implements
`EmitterInterface`. You can add emitters to the stack by pushing them on:

```php
$stack->push($emitterInstance);
```

As a stack, execution is in LIFO (last in, first out) order; the first emitter
on the stack will be evaluated last.

> ### Deprecated with version 2.2
>
> Starting in version 2.2, the `EmitterStack` is deprecated, and moved, along with the
> laminas-diactoros `EmitterInterface` and implementations, to a new package,
> [laminas-httphandlerrunner](https://docs.laminas.dev/laminas-httphandlerrunner).
>
> The interface and the `EmitterStack` are roughly identical to what is present in
> version 2; if you are defining a `Laminas\Diactoros\Emitter\EmitterInterface`
> service of your own, you will need to update it in that version.
