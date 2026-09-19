# dresscode/rules-deegee

What the versions of [Dibi](https://github.com/dg/dibi) and [Texy](https://github.com/dg/texy) renamed, moved and
retired, and what to write instead, as data for [DressCode](https://dresscode.run). With it installed,
`dresscode fix` rewrites code written for older versions to the API of the versions the project stands on, and
reports what has to be rewritten by hand, with what to write instead.

It covers Dibi from its version 4.1 on and Texy from its version 3.0 on: constants renamed from upper case, methods
moved to `Texy\Helpers`, the classes of Texy moved into its namespace, renamed parameters, and the API removed without
a replacement.


Installation
------------

```shell
composer require --dev dresscode/rules-deegee
```

DressCode finds the package by itself. The data apply in the rules of the group `deprecations`, most of which need the
types of the code from PHPStan:

```neon
types: phpstan

groups:
	- deprecations
```

The data of a library apply only when the project has it, and only the sections of the versions the project stands on:
the lowest version its constraint in `composer.json` allows, or the version the key `packages` of the configuration
names.


What it holds
-------------

`upgrading/dibi.neon` and `upgrading/texy.neon`, each a map of rules in sections `since <version>`, written newest
first. What a library only deprecated silently and what changed its behavior are not in the data.


Development
-----------

The libraries the data are about are in `require-dev`, so the data are checked against their installed versions:

- `php tests/check.php <library>` lints `upgrading/<library>.neon` against the installed library and runs its sample,
  `tests/samples/<library>.code`, comparing the result with `.expected` and `.violations`,
- `php tests/check.php <library> --update` writes those two from the run; read the diff, it is what the data do,
- `vendor/bin/tester tests` runs all of it.

How the keys and values are written is described on the page "Maps of replacements" of the DressCode manual.
