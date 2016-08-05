---
layout: page
title: Δοκιμές
category: basics
order: 12
lang: gr
---

Οι δοκιμές είναι ένα σημαντικό μέρος της ανάπτυξης λογισμικού.  Σε αυτό το μάθημα θα δούμε πως να δοκιμάζουμε των κωδικά μας σε Elixir με το ExUnit και μερικές από τις καλύτερες πρακτικές για να το κάνουμε.

{% include toc.html %}

## ExUnit

Το ενσωματωμένο περιβάλλον δοκιμών της Elixir είναι το ExUnit και περιλαμβάνει ότι χρειαζόμαστε για να δοκιμάσουμε τον κώδικά μας διεξοδικά.  Πριν προχωρήσουμε, είναι σημαντικό να σημειώσουμε ότι οι δοκιμές εφαρμόζονταο σαν Elixir scripts, έτσι πρέπει να χρησιμοποιήσουμε την επέκταση αρχείου `.exs`.  Πριν μπορέσουμε να τρέξουμε τις δοκιμές μας πρέπει να ξεκινήσουμε το ExUnit με το `ExUnit.start()`, το οποίο συνήθως γίνεται στο `test/test_helper.exs`.

Όταν δημιουργήσαμε το δοκιμαστικό project μας στο προηγούμενο μάθημα, το mix ήταν αρκετά βολικό στο να μας δημιουργήσει μία απλή δοκιμή, την οποία βρίσκουμε στο `test/example_test.exs`:

```elixir
defmodule ExampleTest do
  use ExUnit.Case
  doctest Example

  test "the truth" do
    assert 1 + 1 == 2
  end
end
```

Μπορούμε να τρέξουμε τις δοκιμές του project μας με την εντολή `mix test`.  Αν το κάνουμε τώρα θα πρέπει να δούμε μια παρόμοια έξοδο:

```shell
Finished in 0.03 seconds (0.02s on load, 0.01s on tests)
1 tests, 0 failures
```

### assert

Αν έχετε γράψει δοκιμές στο παρελθόν τότε θα είστε εξοικειομένοι με την `assert`.  σε μερικά περιβάλλοντα θα βρείτε τις `should` ή `expect` να παίρνουν την θέση της.

Χρησιμοποιούμε την μακροεντολή `assert` για να δοκιμάσουμε ότι η έκφραση είναι αληθής.  Στην περίπτωση που δεν είναι, ένα σφάλμα θα σηκωθεί και οι δοκιμές μας θα αποτύχουν.  Για να δοκιμάσουμε μια αποτυχία ας αλλάξουμε το δείγμα μας και τότε να τρέξουμε την `mix test`:

```elixir
defmodule ExampleTest do
  use ExUnit.Case
  doctest Example

  test "the truth" do
    assert 1 + 1 == 3
  end
end
```

Τώρα θα πρέπει να δούμε μια πολύ διαφορετική έξοδο:

```shell
  1) test the truth (ExampleTest)
     test/example_test.exs:5
     Assertion with == failed
     code: 1 + 1 == 3
     lhs:  2
     rhs:  3
     stacktrace:
       test/example_test.exs:6

......

Finished in 0.03 seconds (0.02s on load, 0.01s on tests)
1 tests, 1 failures
```

Το ExUnit θα μας πει που ακριβώς οι εκτιμήσεις μας αποτυγχάνουν, ποιά ήταν η προσδωκόμενη τιμή και ποιά ήταν στην πραγματικότητα.

### refute

Η `refute` είναι για την `assert` ότι η `unless` για την `if`.  Χρησιμοποιήστε την `refute` όταν θέλετε να βεβαιωθείτε ότι μια έκφραση είναι πάντα ψευδής.

### assert_raise

Μερικές φορές μπορεί να είναι απαραίτητο να βεβαιωθούμε ότι σηκώθηκε κάποιο σφάλμα.  Μπορούμε να το κάνουμε αυτό με την `assert_raise`.  Θα δούμε ένα παράδειγμα της `assert_raise` στο επόμενο μάθημα για το Plug.

## Ρυθμίσεις Δοκιμών

Σε μερικές περιπτώσεις μπορεί να είναι απαραίτητο να κάνουμε κάποιες ρυθμίσεις πριν τις δοκιμές μας.  Για να το πετύχουμε αυτό μπορούμε να χρησιμοποιήσουμε τις μακροεντολές `setup` και `setup_all`.  Η `setup` θα τρέξει πριν από κάθε δοκιμή και η `setup_all` μια φορά πριν από την σουίτα.  Αναμένεται να επιστρέψουν μια τούπλα του στυλ `{:ok, state}`, με την κατάσταση να είναι διαθέσιμη για τις δοκιμές μας.

Για χάρη του παραδείγματος, θα αλλάξουμε τον κώδικά μας να χρησιμοποιεί την `setup_all`:

```elixir
defmodule ExampleTest do
  use ExUnit.Case
  doctest Example

  setup_all do
    {:ok, number: 2}
  end

  test "the truth", state do
    assert 1 + 1 == state[:number]
  end
end
```

## Μίμηση

Η απλή απάντηση στην μίμηση στην Elixir: μην το κάνετε.  Μπορεί ενστικτωδώς να ψάχνετε για μιμήσεις αλλά η κοινότητα της Elixir τις αποθαρρύνει και για καλό λόγο.  Αν ακολουθείτε καλές πρακτικές σχεδιασμού, ο κώδικας που θα προκύπτει θα είναι εύκολος να δοκιμαστεί σαν αυτόνομα μέρη.

Αντισταθείτε στην επιθυμία.