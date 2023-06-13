# Run Dialyzer with Optional Dependencies

When running Dialyzer with optional dependencies, you may get errors related to the optional dependencies.
For example, if you have the following in your `mix.exs` file:

```elixir
defp deps do
  [
    {:jason, "~> 1.0", optional: true}
  ]
end
```
Then you may get the following error when running `mix dialyzer`:

```
:0:unknown_function
Function Jason.decode/1 does not exist.
```

To fix this, you can add the following to your `mix.exs` file:

```elixir
def project do
  [
    ...
    dialyzer: [
      plt_add_apps: [:jason]
    ]
  ]
end
```

## Links

- [Confusing behavior of `optional` deps in `mix.exs`](https://elixirforum.com/t/confusing-behavior-of-optional-deps-in-mix-exs/17719)
