# Odoo Guidelines

# [Introduction](#toc)

The page describes the coding standards for Odoo projects.  
These rules are in addition to the [Odoo Guidelines](https://www.odoo.com/documentation/13.0/reference/guidelines.html).
Our adaptations are designed to improve the quality of code.
Please follow these coding guidelines.  
Note: some rules were taken from
[OCA Guidelines](https://github.com/OCA/odoo-community.org/blob/master/website/Contribution/CONTRIBUTING.rst).

## [Version numbers](#toc)

The version number in the module manifest should be the Odoo major
version (e.g. `13.0`) followed by the module `x.y.z` version numbers.
For example: `13.0.1.0.0` is expected for the first release of an 13.0
module. 
The `x.y.z` version numbers follow the semantics `breaking.feature.fix`:

* `x` increments when the data model or the views had significant
  changes. Data migration might be needed, or depending modules might be affected.
* `y` increments when non-breaking new features are added. A module
  upgrade will probably be needed.
* `z` increments when bugfixes were made. Usually a server restart
  is needed for the fixes to be made available.
  
(**Aram NOTE:** use 13.1.0.0, use only z - migration)


## [Directories](#toc)

A module is organized in a few directories:

* `controllers/`: contains controllers (http routes)
* `data/`: data xml
* `demo/`: demo xml
* `models/`: model definitions
* `reports/`: reporting models (BI/analysis), Webkit/RML print report templates, QWeb report print templates
* `static/src/`: contains the web assets, separated into `css/`, `js/`, `img/`, `lib/`, `xml/` (js qweb templates)...
* `views/`: contains the views and templates (only backend), also assets.xml
* `wizards/`: wizard model and views
* ~`libs/`: for classes are not bound with odoo~

## [File naming](#toc)

For `models`, `views` and `data` declarations, split files by the model
involved, either created or inherited. When they are view or template XML files, a suffix should
be included with its category. For example, demo data for res.partner should go
in a file named `demo/res_partner.xml` and a view for partner should go in
a file named `views/res_partner_view.xml`.

For model named `<main_model>` the following files may be created:

* `models/<main_model>.py`
* `data/<main_model>.xml`
* `demo/<main_model>.xml`
* `views/<main_model>_view.xml`
* `views/<main_model>_template.xml`

(TODO: more details about _template.xml.)
e.g. where <main_model> - ir_cron.xml, email_template.xml

For `controller`, if there is only one file it should be named `main.py`.
If there are several controller classes or functions you can split them into
several files.

JS file names must match module names defined in these files.
For example, js file start with `odoo.define("web.list_controller"...` then file name
must be `static/js/list_controller.js`.  e.g.: https://github.com/arammeem/odoo-modules-hd-12/blob/0c82b6a3d6fbd2196946b96b445a89d59fedc921/modules/aram_color_tag/static/src/js/color_tag_widget.js

Names of xml files with QWeb templates should represent the objects that describe.
Example: `static/xml/some_button.xml`  

For other `static files`, the name pattern is `<module_name>.ext`. Examples: `static/css/im_chat.css`,
`static/less/im_chat.less`.  

Don't link data (image, libraries) outside Odoo: don't use an url to an
image but copy it in our codebase instead.

Filenames should use only `[a-z0-9_]`

## [Complete structure](#toc)

The complete tree of our module therefore looks like this:

```
addons/<my_module_name>/
|-- __init__.py
|-- __manifest__.py
|-- hooks.py
|-- controllers/
|   |-- __init__.py
|   `-- main.py
|-- data/
|   `-- <main_model>.xml
|-- demo/
|   `-- <inherited_model>.xml
|-- models/
|   |-- __init__.py
|   |-- <main_model>.py
|   `-- <inherited_model>.py
|-- report/
|   |-- __init__.py
|   |-- report.xml
|   |-- <bi_reporting_model>.py
|   |-- report_<rml_report_name>.rml
|   |-- report_<rml_report_name>.py
|   |-- <webkit_report_name>.mako
|   `-- <qweb_report>_report.xml
|-- security/
|   |-- ir.model.access.csv
|   `-- res_groups.xml
|   `-- ir_rule_<main_model>.xml (e.g.: ir_rule_sale_order.xml)
|-- static/
|   |-- img/
|   |   |-- my_little_kitten.png
|   |   `-- troll.jpg
|   |-- lib/
|   |   `-- external_lib/
|   `-- src/
|       |-- js/
|       |   `-- <js_module_name>.js
|       |-- css/
|       |   `-- <my_module_name>.css
|       |-- less/
|       |   `-- <my_module_name>.less
|       `-- xml/
|           `-- <some_object>.xml
|-- tests/
|   |-- __init__.py
|   |-- <test_file>.py
|   |-- fixtures/...
|-- views/
|   |-- <main_model>_view.xml
|   |-- <inherited_main_model>_view.xml
|   |-- <main_model>_template.xml
|   `-- <inherited_main_model>_template.xml
`-- wizards/
    |-- __init__.py
    |-- <wizard_model>.py
    `-- <wizard_model>.xml
```

(TODO: fixtures - add description)
Use correct file permissions: folders 755 and files 644.

## [Installation hooks](#toc)

TODO: example of hooks

When **`pre_init_hook`**, **`post_init_hook`**, **`uninstall_hook`** and **`post_load`** are
used, they should be placed in **`hooks.py`** located at the root of module
directory structure and keys in the manifest file keeps the same as the following

```python
{
    "pre_init_hook": "pre_init_hook",
    "post_init_hook": "post_init_hook",
    "uninstall_hook": "uninstall_hook",
    "post_load": "post_load",
}
```

Remember to add into the **`__init__.py`** the following imports as
needed. For example:

```python
from .hooks import pre_init_hook
from .hooks import post_init_hook
from .hooks import uninstall_hook
from .hooks import post_load
```

For applying monkey patches use post_load hook.
In order to apply them just if the module is installed.

## [External dependencies](#toc)

### [Manifest](#toc)

`__manifest__.py`

If your module uses extra dependencies of python or binaries you should add
the `external_dependencies` section to `__manifest__.py`.

```python
{
    "name": "Example Module",
    "external_dependencies": {
        "bin": [
            "external_dependency_binary_1",
            "external_dependency_binary_2",
        ],
        "python": [
            "external_dependency_python_1",
            "external_dependency_python_2",
        ],
    },
    "installable": True,
}
```

An entry in `bin` needs to be in `PATH`, check by running
`which external_dependency_binary_N`.

An entry in `python` needs to be in `PYTHONPATH`, check by running
`python -c "import external_dependency_python_N"`.

### [ImportError](#toc)

In python files where you use external dependencies, you will
need to add `try-except` with a debug log.

```python
try:
    import external_dependency_python_N
except ImportError:
    _logger.debug("Cannot import `external_dependency_python_N`.")
```

This rule doesn't apply to the test files since these files are loaded only when
running tests and in such a case your module and their external dependencies are installed.

This rule doesn't apply neither to Odoo >= v12, as an unmet dependency in an
uninstalled module doesn't block the service.

### [README](#toc)
If your module uses extra dependencies of python or binaries, please explain
how to install them in the `README.rst` file in the section `Installation`.

# [Access rules](#toc)

Definition of access rules in a file "ir.model.access.csv" must comply with the following rules.

| Column name | id                               | name                                               | model_id:id                            | group_id:id                  | perm_read      | perm_write      | perm_create      | perm_unlink      |
| ----------- | -------------------------------- | -------------------------------------------------- | -------------------------------------- | -----------------------------| -------------- | --------------- | ---------------- | ---------------- |
| Description | ID                               | Rule name                                          | Model to which a rule applies          | Group that a rule applies to | Access to read | Access to write | Access to create | Access to unlink |
| Format      | **access**_model_name_group_name | **access**_model_module_name_model_name group_name | model_module_name.**model**_model_name | group_module_name.group_name | 0 or 1         | 0 or 1          | 0 or 1           | 0 or 1           |

* If you use a model from a current module, you don't need to write **model_module_name** in the **model_id:id** column.
* If a group is not specified for the access rule, then **all** is written in the **id** and **name** columns instead
  of **group_name**.
* To check whether a user is in a group, use the `has_group(<xmlid_of_group>)` method.  
  For Odoo <= 8.0 use `sudo()` to check specific user otherwise the method will check
  a current user (gotten from a context). Example:

```python
self.sudo(user.id).has_group("base.warehouse_user")
```

# [XML files](#toc)

## [Format](#toc)

When declaring a record in XML:

* Place `id` attribute before `model`
* For field declarations, the `name` attribute is first. Then place the `value`
  either in the `field` tag, either in the `eval` attribute, and finally other
  attributes (widget, options, ...) ordered by importance. Options should be in the end.
* Try to group the records by model. In case of dependencies between
  action/menu/views, the convention may not be applicable.
* Use naming convention defined at the next point
* The tag `<data>` is only used to set not-updatable data with `noupdate=1`
* Do not prefix the xmlid by the current module's name
  (`<record id="view_id"...`, not `<record id="current_module.view_id"...`)
* If there are more than 3 attributes, the `name` attribute should be placed on the first string,
  the others should be placed on the next strings.
* Using `"` preferable than `'`
* If an attribute value consists of several items, then each item must be on a separate line.
  Also, in this case, each attribute must be on a separate line, regardless of their number.
  ```xml
  <record id="view_id" model="ir.ui.view">
      <field name="name">view.name</field>
      <field name="model">object_name</field>
      <field name="priority" eval="16"/>
      <field name="arch" type="xml">
          <tree>
              <field name="my_field_1"
                     string="First Label"
                     widget="statusbar"
                     statusbar_visible="draft,sent,progress,done"
              />
              <field name="my_field_2" string="Second Label" domain="[('customer', '=', True)]"/>
              <field name="my_field_3"
                     domain="[('customer', '=', True)]"
                     attrs="{
                         'invisible': [
                             ('customer', '=', 'True'),
                             ('condition_select', '!=', 'python'),
                         ],
                         'required': [
                             ('condition_select', '=', 'python'),
                         ],
                     }"
              />
          </tree>
      </field>
  </record>
  ```

## [Records](#toc)

* For records of model `ir.filters` use explicit `user_id` field.

```xml
<record id="filter_id" model="ir.filters">
    <field name="name">Filter name</field>
    <field name="model_id">filter.model</field>
    <field name="user_id" eval="False"/>
</record>
```

More info [here](https://github.com/odoo/odoo/pull/8218)

## [Attributes order](#toc)

* For buttons.

```xml
<button name="action_or_function_name"
        type="object"
        string="Button name for users"
        context="{...}"
        options="{...}"
        class="..."
        style="..."
        ...
/>
</record>
```

* For menuitem.

```xml
<menuitem id="model_name_menu"
          action="model_name_action"
          parent="parent_menu_id"
          name="Menu item name"
          groups="..."
          sequence="..."
          ...
/>
</record>
```

* For field.

```xml
<field name="field_name"
       string="Field Label"
       ...
/>
</record>
```

## [Naming xml_id](#toc)

### [Security, view and action](#toc)

Use the following pattern:

* For a menu: `<model_name>_menu`
* For a view: `<model_name>_view_<view_type>`, where `view_type` is kanban,
  form, tree, search, ...
* For an action: the main action respects `<model_name>_action`. Others are
  suffixed with `_<detail>`, where `detail` is an underscore lowercase string
  explaining the action (should not be long). This is used only if
  multiple actions are declared for the model.
* For a group: `group_<group_name>` where `group_name` is the
  name of the group, generally 'user', 'manager', ...
* For a rule: `<model_name>_rule_<short_description>`.

```xml
<!-- views and menus -->
<record id="model_name_menu" model="ir.ui.menu">
    ...
</record>

<record id="model_name_view_form" model="ir.ui.view">
    ...
</record>

<record id="model_name_view_kanban" model="ir.ui.view">
    ...
</record>

<!-- actions -->
<record id="model_name_action" model="ir.actions.act_window">
    ...
</record>

<record id="model_name_action_child_list" model="ir.actions.act_window">
    ...
</record>

<!-- security -->
<record id="group_user" model="res.groups">
    ...
</record>

<record id="model_name_rule_public" model="ir.rule">
    ...
</record>

<record id="model_name_rule_company" model="ir.rule">
    ...
</record>
```

### [Inherited XML](#toc)

A module can extend a view only one time.

The naming rules should be followed even when a view is inherited, the module
name prevents xid conflicts. In the case where an inherited view has a name
which does not follow the guidelines set above, prefer naming the inherited
view after the original over using a name which follows the guidelines. This
eases looking up the original view and other inheritance if they all have the
same name.

```xml
<record id="original_id" model="ir.ui.view">
    <field name="inherit_id" ref="original_module.original_id"/>
    ...
</record>
```

# [Python](#toc)

## [Imports](#toc)

The imports are ordered as

1. Standard library imports
2. Known third party imports (One per line sorted and split in python stdlib)
3. Odoo imports (`odoo`)
4. Imports from Odoo modules (rarely, and only if necessary)
5. Local imports in the relative form
6. Unknown third party imports (One per line sorted and split in python stdlib)

Inside these 6 groups, the imported lines are alphabetically sorted.

```python
# 1: imports of python lib
import base64
import logging
import re
import time

# 2: import of known third party lib
import lxml

# 3:  imports of odoo
import odoo
from odoo import api, fields, models  # alphabetically ordered
from odoo.tools.safe_eval import safe_eval
from odoo.tools.translate import _

# 4:  imports from odoo modules
from odoo.addons.website.models.website import slug
from odoo.addons.web.controllers.main import login_redirect

# 5: local imports
from . import utils

# 6: Import of unknown third party lib
_logger = logging.getLogger(__name__)
try:
    import external_dependency_python_N
except ImportError:
    _logger.debug("Cannot import `external_dependency_python_N`.")
```

 * Note:
   * You can use [isort](https://pypi.python.org/pypi/isort/) to automatically
     sort imports.
   * Install with `pip install isort` and use with `isort myfile.py`.

## [Idioms](#toc)

* Limit all lines to a maximum of 120 characters.
* For Python 2 (Odoo < 11.0), all python files should contain
  ``# coding: utf-8`` or ``# -*- coding: utf-8 -*-`` as first line.
* For Python 3 (Odoo >= 11.0), no need for utf-8 coding line as this is
  implicit.
* Prefer `.format()` over `%`, prefer `{name}` instead of positional.
  Guide: https://docs.python.org/2/library/string.html#format-examples
* Always favor **Readability** over **conciseness** or using the language
  features or idioms.
* Use list comprehension, dict comprehension, and basic manipulation using
  `map`, `filter`, `sum`, ... They make the code more pythonic, easier to read
   and are generally more efficient
* The same applies for recordset methods: use `filtered`, `mapped`, `sorted`,
  ...
* Exceptions: Use `from odoo.exceptions import Warning as UserError` (v8)
  or `from odoo.exceptions import UserError` (v9)
  or find a more appropriate exception in `odoo.exceptions`
* Document your code
    * Docstring on methods should explain the purpose of a function,
      not a summary of the code
    * Simple comments for parts of code which do things which are not
      immediately obvious
    * Too many comments are usually a sign that the code is unreadable and
      needs to be refactored
* All functions must contain a docstring.
* Use meaningful variable/class/method names
* If a function is too long or too indented due to loops, this is a sign
  that it needs to be refactored into smaller functions
* If a function call, dictionary, list or tuple is broken into two lines,
  break it at the opening symbol. This adds a four space indent to the next
  line instead of starting the next line at the opening symbol.
  Example:
  ```python
  partner_id = fields.Many2one(
      "res.partner",
      required=True,
  )
  ```
* When making a comma separated list, dict, tuple, ... with one element per
  line, append a comma to the last element. This makes it so the next element
  added only changes one line in the changeset instead of changing the last
  element to simply add a comma.
* If an argument to a function call is not immediately obvious, prefer using
  named parameter.
* Use English variable names and write comments in English. Strings which need
  to be displayed in other languages should be translated using the translation
  system
* Using `"` preferable than `'`
* Don't use variable name less than two symbols if it will be used in two and more lines. You can use that variables only in one line.
* If you iterate over `self` or other recordset/object, then try to avoid `record` or `rec` variable names, use recordset/object name/entity: `picking`, `move` and so on.
* If we copy-paste function and update some code inside it, then you should add before and after this block next lines respectively:
  ```python
  # Arammeem: Changes start

  ... Added/Changed lines ...

  # Arammeem: Changes end
  ```
## [Symbols](#toc)

### [Odoo python classes](#toc)

Use lowercase notation for model name using template "model.name -> model_name".

```python
class account_invoice(models.Model):
    _name = "account.invoice"
    ...
```

### [Variable names](#toc)

* Use underscore lowercase notation for common variables (snake_case).
* If a field is some kind of `ID` and it's type is not  `Many2one`, then such field should have **prefix** `id_`.
  Example: `id_external`
* Since new API works with records or recordsets instead of id lists, don't
  suffix variable names with `_id` or `_ids` if they do not contain an ids or
  lists of ids.
  ```python
  res_partner_obj = self.env["res.partner"]
  partners = res_partner_obj.browse(ids)
  partner_id = partners[0].id
  ```
* Use underscore uppercase notation for global variables or constants on the module level.
  ```python
  CONSTANT_VAR1 = "Value"
  ```

## [Models](#toc)

### [Model attribute order and method conventions](#toc)

1. Private attributes (`_name`, `_description`, `_inherit`, `_order`, ...)
2. Alphabetically order functions:
    1. if function with same prefix doesn't exist
        1. Constrains methods (method pattern is `_check_<field_name>`)
        1. Compute methods (method pattern is `_compute_<field_name>`) (assign in field as string)
        1. Special`default_get` method and default methods (method pattern is `_default_<field_name>`) (assign in field as lambda)
        1. Domain methods (method pattern is `_domain_<field_name>`) (assign in field as lambda)
        1. Inverse methods (method pattern is `_inverse_<field_name>`) (assign in field as string)
        1. Onchange methods (method pattern is `_onchange_<field_name>`)
        1. Search methods (method pattern is `_search_<field_name>`) (assign in field as string)
    1. if function exists with same prefix, then place it under the last function with the same prefix.
1. Fields declarations
1. CRUD methods
1. Action methods (method prefix is `action_`).  
   Its decorator is `@api.multi`, but since it use only one record,
   add `self.ensure_one()` at the beginning of the method.
1. Other business methods

Compute, search, inverse, domain, onchange and constrains methods must be placed in the same order as
the fields declarations.

## [Fields](#toc)

* `Many2one` fields should have `_id` as suffix. Example: partner_id, user_id
* `One2many` and `Many2many` fields should always have `_ids` as suffix. Example: sale_order_line_ids
* Explicitly set `Many2many` relation fields. Example:
  ```python
  job_ids = fields.Many2many(
      "hr.job",
      "res_partner_hr_job_rel",
      "partner_id",
      "job_id",
  )
  ```
* If the technical name of the field (the variable name) is the same to the
  string of the label, don't put `string` parameter for new API fields, because
  it's automatically taken. If your variable name contains "_" in the name,
  they are converted to spaces when creating the automatic string and each word
  is capitalized. For relation fields `_id` and `_ids` suffixes will be discarded.
* If you define own field's string, then all words should be begin with upper case, but not short prepositions like `of`, `by` and so on.
  For example: `Sale Order`, `Team Leader`, but `Unit of Measure`, `Send by Post`.
* Default and domain functions should be declared with a lambda call on self. The reason
  for this is so the functions can be inherited. Assigning a function
  pointer directly to the `default` or `domain` parameter does not allow for inheritance.
  ```python
  partner_id = fields.Many2one(
      "res.partner",
      domain=lambda self: self._domain_partner_id(),
      default=lambda self: self._default_partner_id(),
  )
  ```
* Use square brackets instead of `False` to reset x2many fields.
  Otherwise, there will be errors in the existing odoo code.
  For example, if we set `move_finished_ids = False` then we will get TypeError in odoo mrp module:
  ```python
  values['move_finished_ids'] = values.get('move_finished_ids', []) + values['move_byproduct_ids']
  ```

## [Constraints](#toc)

We never use @api.constrains to check the value on uniqueness. For more information visit pages:
* [http://wiki.itlibertas.com/wiki/Odoo/_sql_constraints]
* [http://wiki.itlibertas.com/wiki/Odoo/\_sql_constraints_vs\_api.constrains]

## [Logging](#toc)

Always use `logging` over `print`.
To log both an exception message and a trace log use:

```python
import logging
_logger = logging.getLogger(__name__)
try:
    1/0
except ZeroDivisionError as e:
    _logger.exception("message")
```

or

```python
import logging
_logger = logging.getLogger(__name__)
try:
    1/0
except ZeroDivisionError as e:
    _logger.error("message", exc_info=True)
```

Setting `exc_info` to `True` will cause the logging to include the full stack trace exactly like
`logger.exception()` does. The only difference is that you can easily change the log level to something other
than error: Just replace `logger.error` with `logger.warn`, for example.

Useful resources:
* [https://docs.python.org/2/howto/logging.html] - Official documentation
* [https://docs.python.org/2.7/howto/logging-cookbook.html#logging-cookbook] - Logging cookbook
* [http://victorlin.me/posts/2012/08/26/good-logging-practice-in-python] - Good logging practice
* [https://www.loggly.com/blog/exceptional-logging-of-exceptions-in-python/] - Logging patterns

## [Exceptions](#toc)

The `pass` into block except is not a good practice!
By including the `pass` we assume that our algorithm can continue to function after the exception occurred
If you really need to use the `pass` consider logging that exception

```python
try:
    sentences
except Exception:
    _logger.debug("Why the exception is safe....", exc_info=True)
```

# [Javascript](#toc)

* `use strict;` is recommended for all javascript files
* Use a linter (jshint, ...)
* Never add minified Javascript libraries
* Use UpperCamelCase for class declarations
* Using `"` preferable than `'`

# [CSS](#toc)

* Prefix all your classes with `o_<module_name>` where `module_name` is the
  technical name of the module (`sale`, `im_chat`, ...) or the main route
  reserved by the module (for website module mainly,
  i.e. `o_forum` for website_forum module). The only exception for this rule is
  the webclient: it simply use `o_` prefix.
* Avoid using ids
* Use bootstrap native classes
* Use underscore lowercase notation to name classes

# [Git](#toc)

## [Tags](#toc)

Tags are used to prefix your commit. They should be one of the following

* **[NEW]** for adding new module;
* **[REF]** for refactoring: when a feature is heavily rewritten;
* **[FIX]** for bug fixes;
* **[IMP]** for improvements, adding new features;
* **[VIEW]** for improvements views: adding, changing (assets, qweb, views etc.);
* **[DEMO]** for demo data;
* **[DATA]** for changes in the `data` directory;
* **[ACC]** for access rights;
* **[DOC]** for documentation (*.rst files, static/description, manifest);
* **[PRICE]** for updating a price in a manifest file;
* **[I18N]** for changes in translation files;
* **[TEST]** for tests;
* **[LINT]** for fixes Lint errors;
* **[PORT]** for porting a module;
* **[DEL]** for removing a module;
* **[CI]** for changes in CI;

## [Commits](#toc)
- Source branch name must be the same as the related issue on the Gitlab. Example: `common#10`
- Use following format for commit message:  
  `[<TAG>] <name_of_change_module> : <name_of_related_issue> <commit_message with capital letter in the beginning>`.  
  Examples:
  - `[NEW] some_new_module : common#10 Add new module`
  - `[ACC] some_new_account_module : common#11 Add new access rights`
  -
```
[FIX] some_new_account_module : common#12 Fix errors on recordsets

- Also remove unneeded code
```

- For more details about commits you can read:
  - https://habr.com/ru/post/416887/
  - https://github.com/RomuloOliveira/commit-messages-guide/blob/master/README_ru-RU.md

- You don't need write a big commit message but write it in one style described as above.
- If you want to write more about your commit then write it in body of commit(look at the above example about FIX commit), but not in title
- Keep clean title: use only `:` to divide module name and issue number from commit message subtitle. Don't use other non word characters if you can write commit title without it.
- Write issue number with its repository, don't write just `#21` but write `common#21`.

## [Merge requests](#toc)
- In MR title add all modules that you commited with comma without spaces. Example:
```
[FIX] product,stock : common#12 Fix errors on recordsets

- Also remove unneeded code
```
- Group commits in MR by module. All changes in module in MR should be **in only one commit**.
- Pay attention when you are fixing commits in MR: we don't create new commits with the same module to fix comments - just squash them by modules. If you rebase and/or resolve conflicts, then pay **more and more** attention to not add commits thouse are not yours.
- Thread can be resolved only by user who started thread (author of thread).


# [Contributing](#toc)
Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

If you have any questions, you can contact the CODEOWNERS.
