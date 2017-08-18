---
layout: post
title: "Creating and using UUID database columns and fields with Ecto"
date: 2017-08-18 19:55
comments: true
categories:
- elixir
- ecto
- phoenix
---
Recently in a Phoenix project I needed to use a UUID column to represent an external identifier. I had previously used UUID's with the `binary_id` field type but it wasn't immediately obvious how to use UUID's for regular columns.

The migration is simple enough - just specify a `:uuid` column type. In this case I set `null: false` in the options to prevent null entries. I also have a unique_index on the `:external_id` just in case. (UUID's can be specified for test data, as later mentioned. It's always best to have data integrity constraints at the database level.)

```
def change do
  create table(:teams) do
    add :external_id, :uuid, null: false
    add :name, :string, null: false

    timestamps()
  end

  create unique_index(:teams, [:external_id])
end
```

The schema definition is equally straight forward:

```
schema "teams" do
  field :external_id, Ecto.UUID, autogenerate: true
  field :name, :string

  timestamps()
end
```

We set the field type to `Ecto.UUID` and specify we want it automatically generated with the `autogenerate: true` option. This is required if you want automatically generated UUID's for each entry. Autogeneration happens before insertion if no value is already set. There is an important distinction between using the `:autogenerate` and `:default` options - the value given to `:default` is calculated at compilation time, so all entries would have the same value per compile, whereas `:autogenerate` is not.

Ecto handles the casting between a string representation of a UUID and database persistence (and vice versa). This is great because it means you can use specify UUID's as strings in factories or test data, without the need to use a UUID generator.
