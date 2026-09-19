DressCode Rules for Dibi and Texy
=================================

[![Latest Stable Version](https://poser.pugx.org/dresscode/rules-deegee/v/stable)](https://packagist.org/packages/dresscode/rules-deegee)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](https://packagist.org/packages/dresscode/rules-deegee)

 <!---->

<h3>

✅ Upgrades code for [Dibi from 4.1 and Texy from 3.0](#versions-covered) to today<br>
✅ Knows [what each variable is](#what-gets-upgraded), so it rewrites only the right calls<br>
✅ Reports [what needs a human](#what-is-left-to-you), with what to write instead<br>
✅ From the author of Dibi and Texy, [written from every commit](#where-the-data-come-from) of both

</h3>

 <!---->

**Upgrade code using Dibi and Texy without hunting for renames.** Install this package, run `dresscode fix`, and
code written for older versions of [Dibi](https://github.com/dg/dibi) and [Texy](https://github.com/dg/texy) is
rewritten to the API of the versions your project stands on:

```diff
- $id = $this->db->insertId();
+ $id = $this->db->getInsertId();

- $result->setType('price', Type::FLOAT);
+ $result->setType('price', Type::Float);

- $slug = Texy::webalize($title);
+ $slug = Helpers::webalize($title);

- $el = new \TexyHtml('p');
+ $el = new \Texy\HtmlElement('p');
```

Four changes, the imports rewritten along with them, and not a byte more of the file touched. The package holds the
data, what each version of Dibi and Texy renamed, moved or retired; the rewriting is done by
[DressCode](https://dresscode.run), the PHP coding standard and upgrade tool, which comes with it.

 <!---->

Installation and first run
==========================

**1️⃣ Install it: `composer require --dev dresscode/rules-deegee`**<br>
**2️⃣ Turn the upgrade on in `dresscode.neon`**<br>
**3️⃣ Look at the changes and make them: `vendor/bin/dresscode check --diff`, `vendor/bin/dresscode fix`**

The package brings DressCode along, and DressCode finds the package by itself. The upgrade is the set
`deprecations`, and most of its rewrites need to know what a variable is, which DressCode asks your PHPStan:

```neon
typeAnalysis: phpstan

use:
	- deprecations
```

If your project already has a `dresscode.neon`, add the two keys to it. If it does not, these lines are the whole
file: with no coding standard named, DressCode upgrades the code and leaves its style alone. Then run your tests and
PHPStan, read the diff, and commit.

 <!---->

What gets upgraded
==================

None of it is search and replace. DressCode asks PHPStan what each variable is, so `insertId()` is rewritten where it
is called on a connection of Dibi and nowhere else. What the package rewrites:

- **constants out of upper case**: `Type::FLOAT` to `Type::Float`, `Fluent::REMOVE` to `Fluent::Remove`,
  `HtmlElement::INNER_TEXT` to `HtmlElement::InnerText`,
- **renamed methods** of Dibi: `affectedRows()` to `getAffectedRows()`, `insertId()` to `getInsertId()`, on a
  connection and on the static `dibi` alike,
- **the helpers of Texy** moved to `Texy\Helpers`: `webalize()`, `outdent()`, `normalize()`, and `Utf::strtolower()`
  and `Utf::utf2ascii()` to `Helpers::toLower()` and `Helpers::toAscii()`,
- **the classes of Texy** moved into its namespace, `TexyHtml` to `Texy\HtmlElement`, and a module that moved,
  `$texy->cleaner` to `$texy->htmlOutputModule`,
- **renamed classes and parameters**: `DibiExtension22` to `DibiExtension3`, `new Modifier(mod: ...)` to
  `new Modifier(s: ...)`.

The rewritten code takes the shape of the file around it, or of your coding standard, if the configuration names one.

 <!---->

What is left to you
===================

Where there is no replacement, DressCode does not guess. It reports the place and says what to do:

```
Property `Texy\Texy::$encoding` is forbidden: the input and the output are always UTF-8; drop the setting
Constant `Texy\Texy::XHTML1_STRICT` is forbidden: there is no replacement, XHTML is not generated any more and the output is always HTML5
```

An upgrade is a rewrite plus a list of what remains. Every such sentence is written as an instruction, so the list
can be worked through by you or handed to an AI coding agent as it is.

 <!---->

Where the data come from
========================

The data come from the author of Dibi and Texy. Both libraries first got an upgrading guide, `docs/upgrading.md` in
their repositories, written from their whole history, commit by commit, and every entry was then checked against the
code of the release that made the change. The data are also checked against the installed libraries, so a
replacement that does not exist cannot get in, and each library has a sample of old code that the data must turn
into the expected new code.

 <!---->

Versions covered
================

Dibi from its version 4.1 and Texy from its version 3.0 to the current ones. The data of each library apply only when
your project has it, and only the sections of the versions it stands on: the lowest version the constraint in
`composer.json` allows, or the version the key `targets` of the configuration names. The
[manual](https://dresscode.run/upgrading-deegee) has the details.

 <!---->

Limits
======

- **Most rewrites need PHPStan.** Without `typeAnalysis: phpstan`, only classes, functions and annotations are rewritten.
- **DressCode runs on PHP 8.4 to 8.6.** The code it upgrades may be written for PHP 8.0 and newer, but the package is
  installed into the project, so the project has to install on PHP 8.4 or newer.
- **The data fix the code, not its meaning.** A change of behavior is not in the data; the upgrading guides of the
  libraries describe it. Run your tests after a fix.

 <!---->

Other ecosystems
================

| package | upgrades |
|---|---|
| [`dresscode/rules-nette`](https://github.com/dg/dresscode-rules-nette) | every Nette library, from 3.0 |
| [`dresscode/rules-symfony`](https://github.com/dg/dresscode-rules-symfony) | the components and bridges of Symfony, from 6.0 |
| [`dresscode/rules-laravel`](https://github.com/dg/dresscode-rules-laravel) | the Laravel framework from 6, and the attributes of Laravel 13 |
| [`dresscode/rules-deegee`](https://github.com/dg/dresscode-rules-deegee) | Dibi and Texy |

A package like these can be written for any library; the [manual](https://dresscode.run/upgrading-data) says how.

 <!---->

Development
===========

The libraries the data are about are in `require-dev`, so the data are checked against their installed versions:

- `php tests/check.php <library>` lints `upgrading/<library>.neon` and runs its sample,
  `tests/samples/<library>.code`, comparing the result with `.expected` and `.violations`,
- `php tests/check.php <library> --update` writes those two from the run; read the diff, it is what the data do,
- `vendor/bin/tester tests` runs all of it.
