==================
Write Data Modules
==================

.. important::
   This tutorial assumes familiarity with the :doc:`server_framework_101` tutorial and the
   :doc:`define_module_data` tutorial.

Although as developpers, we prefer to have the full power of Python to write our modules,
it is sometimes not possible to do so; typically on managed hosting solutions which do not
allow the deployment of custom Python code like the `Odoo.com <https://www.odoo.com/start>`_
platform.

However, the flexible nature of Odoo is meant to allow customizations out of the box. Whilst
a lot is possible with :doc:`Studio </applications/studio>`, it is also possible to define
models, fields and logic in :doc:`XLM Data File <define_module_data>`. This makes it easier
to develop, maintain and deploy these customizations.

In this tutorial, we will learn how to define models, fields and logic in XML data files.
We will also see the limitations of this approach to module development.

Problem Statement
=================

Like in the :doc:`server_framework_101` tutorial, we will be working on Real Estate concepts.

Our goal is to create a new application to manage Real Estate properties in a similar (albeit
simpler) way to the :doc:`server_framework_101` tutorial. We will define the models, fields and
logic in XML data files instead of Python files.

At the end of this tutorial, we will be able to achieve the following in our app:

- Manage Real Estate properties that are for sale
- Publish these properties on a website
- Accept offers online from the website
- Invoice the buyer when the property is sold

Module Structure
================

Like in any developmnet project, a clear structure makes it easier to manage and maintain the code.

Whilst in normal Odoo modules, you will have a mix of Python and XML files, in this case, we will
only have XML files. Therefore, it is expected that your work tree will look something like this:

.. code-block:: bash

  estate
  ├── actions
  │   └── *.xml
  ├── models
  │   └── *.xml
  ├── security
  │   └── ir.model.access.csv
  ├── views
  │   └── *.xml
  ├── __init__.py
  └── __manifest__.py

The only Python files you will have are the ``__init__.py`` and ``__manifest__.py`` files. The
``__manifest__.py`` file will be the same as for any Odoo module, but will also import its models
in the `data` list.

Don't forget that the files in the `data` list in the ``__manifest__.py`` file will be loaded in
the order they are listed, so you will typically start by loading the model files first (as most
other files will depend on them).

The ``__init__.py`` file is empty, but is required for Odoo to recognize the module.

Deploying the Module
====================

To deploy the module, you will need to create a zip file of the module and upload it to your
Odoo instance. Make sure that the module `base_import-module` is installed on your instance,
then go to the :menuselection:`Apps --> Import Module` and upload the zip file. You must be
in :ref:`developer mode <developer-mode>` to see the `Import Module` menu item.

If you modify the module, you will need to create a new zip file and upload it again, which
will reload all the data in the module. Note however that some operations are not possible,
like changing the type of a field you created previously. Note also that data that was created
by a previous version of the module will not be deleted (e.g. a field you decide to remove, etc.).
In general, the simplest way to handle this is to start with a fresh database or to uninstall
the module prior to uploading the new version.

When uploading a module, the wizard will accept 2 options:

- `Force init`: if your module is already installed and you upload it again, checking this
  option will force the update of all data marked as `noupdate="1"` in the XML files.
- `Import demo data`: self explanatory

It is also possible to deploy the module using the `odoo-bin` command line tool with the `deploy`
command:

.. code-block:: bash

  $ odoo-bin deploy <path_to_your_module> https://<your_odoo_instance> --login <your_login> --password <your_password>

This command also accepts the `--force` option, which is equivalent to the `Force init` option
in the wizard.

Note that the user you use to deploy the module must have `Administration/Settings` access rights.

.. exercise:: Create the required addon files and deploy it on your Odoo instance.

    Create the following folders and files:

    - ``/home/$USER/src/tutorials/estate/__init__.py``
    - ``/home/$USER/src/tutorials/estate/__manifest__.py``

    The ``__manifest__.py`` file should only define the name and the dependencies of our modules.
    The only necessary framework module for now is ``base``.

    Create a zip file of your module and upload it to your Odoo instance.


Models and Basic Fields
=======================

As you can imagine, defining models and fields in XML files is not as straightforward as in Python.

Since data files are read sequentially, you must define the elements in the right order.
For example, you must define a model before you can define a field on that model, and you
must define fields before adding them to a view.

In addition, XML is simply much more verbose than Python.

Let's start by defining a simple model to represent a Real Estate property in the `models`
directory of our module.

Odoo models are stored in database as `ir.model` records. Like any other record, they can be
defined in XML files:

.. code-block:: xml

    <?xml version="1.0" encoding="utf-8"?>
    <odoo>
        <record model="ir.model" id="model_real_estate_property">
            <field name="name">Real Estate Property</field>
            <field name="model">x_estate.property</field>
        </record>
    </odoo>

Note that all models and fields defined in data files must be prefixed with `x_`; this is
mandatory and is used to differentiate them from models and fields defined in Python files.

Like for classic models defined in Python, Odoo will automatically add several fields to the model:

- :attr:`~odoo.fields.Model.id` (:class:`~odoo.fields.Id`)
  The unique identifier for a record of the model.
- :attr:`~odoo.fields.Model.create_date` (:class:`~odoo.fields.Datetime`)
  Creation date of the record.
- :attr:`~odoo.fields.Model.create_uid` (:class:`~odoo.fields.Many2one`)
  User who created the record.
- :attr:`~odoo.fields.Model.write_date` (:class:`~odoo.fields.Datetime`)
  Last modification date of the record.
- :attr:`~odoo.fields.Model.write_uid` (:class:`~odoo.fields.Many2one`)
  User who last modified the record.

We can also add several fields to our new model. Let's add some simple fields, like a selling
price (float), a description (as html), and a postcode (as a char).

Like for models, fields are simply records of the `ir.model.fields` model and can be
defined as such in data files:

.. code-block:: xml

    <?xml version="1.0" encoding="utf-8"?>
    <odoo>
        <!-- ...model definition from before... -->
        <record model="ir.model.fields" id="field_real_estate_property_name">
            <field name="model_id" ref="estate.model_real_estate_property" />
            <field name="name">x_name</field>
            <field name="field_description">Name</field>
            <field name="ttype">char</field>
            <field name="required">True</field>
        </record>

        <record model="ir.model.fields" id="field_real_estate_property_selling_price">
            <field name="model_id" ref="estate.model_real_estate_property" />
            <field name="name">x_selling_price</field>
            <field name="field_description">Selling Price</field>
            <field name="ttype">float</field>
            <field name="required">True</field>
        </record>

        <record model="ir.model.fields" id="field_real_estate_property_description">
            <field name="model_id" ref="estate.model_real_estate_property" />
            <field name="name">x_description</field>
            <field name="field_description">Description</field>
            <field name="ttype">htlm</field>
        </record>

        <record model="ir.model.fields" id="field_real_estate_property_postcode">
            <field name="model_id" ref="estate.model_real_estate_property" />
            <field name="name">x_postcode</field>
            <field name="field_description">Postcode</field>
            <field name="ttype">char</field>
        </record>
    </odoo>

Quite a few attributes of your new field can be set this way; for basic fields,
the basic attributes are:

- `name`: the technical name of the field (must begin with `x_`)
- `field_description`: the label of the field
- `help`: a help text for the field, displayed in the interface
- `ttype`: the type of the field (e.g. `char`, `integer`, `float`, `html`, etc.)
- `required`: whether the field is required or not (default: `False`)
- `readonly`: whether the field is read-only or not (default: `False`)
- `index`: whether the field is indexed or not (default: `False`)
- `copied`: whether the field is copied when duplicating a record or not (default: `True`
  for scalar non-computed fields)
- `translate`: whether the field is translatable or not (default: `False`)

Attributes are also available to control HTML sanitization as well as other, more advanced
features; for a complete list, refer to the `ir.model.fields` model in the database available
in the :menuselection:`Settings --> Technical --> Database Structure --> Fields` menu or
see the `ir.model.fields` model definition in the `base` module.

.. exercise:: Add basic fields to the Real Estate Property model.

    Add the following basic fields to the table:

    ========================= ========================= =======================
    Field                     Type                       Required
    ========================= ========================= =======================
    x_date_availability       Date
    x_expected_price          Float                     True
    x_bedrooms                Integer
    x_living_area             Integer
    x_facades                 Integer
    x_garage                  Boolean
    x_garden                  Boolean
    x_garden_area             Integer
    x_garden_orientation      Selection
    ========================= ========================= =======================

    The ``x_garden_orientation`` field must have 4 possible values: 'North', 'South', 'East'
    and 'West'. The selection list must be created by first creating the `ir.model.fields`
    record for the field itself, then creating the `ir.model.fields.selection` records; these
    records take three fields: `field_id`, `name` (the name in the UI) and `value` (the value
    in the database). A `sequence` field can also be set, which controls the order in which
    the selections are displayed in the UI (lower sequence values are displayed first).

Default values
--------------

In Python, default values can be set on fields using the ``default`` argument in the field
declaration. In data modules, default values are set by creating an ``ir.default`` record
for each field. For example, it is possible to set the default value of the
``x_selling_price`` field to ``100000`` for all properties by creating the following record:

.. code-block:: xml

    <odoo>
        <!-- ...model definition from before... -->
        <record model="ir.default" id="default_real_estate_property_selling_price">
            <field name="field_id" ref="estate.field_real_estate_property_selling_price" />
            <field name="json_value">100000</field>
        </record>
    </odoo>

For more details, refer to the `ir.default` model in the database available in the
:menuselection:`Settings --> Technical --> Actions --> User-defined Defaults` menu or
see the `ir.default` model definition in the `base` module.

.. warning::

    These defaults are static but can be set by company and/or user using the ``user_id``
    and ``company_id`` fields of the ``ir.default`` record. This means that having a dynamic
    default value of "today" for the ``x_date_availability`` field is not possible, for example.

Security
========

Security in data modules is exactly the same as for Python modules and can be found
in :doc:`server_framework_101/04_securityintro`.

Refer to that tutorial for details.

.. exercise:: Add access rights.

    Create the ``ir.model.access.csv`` file in the appropriate folder and define it in the
    ``__manifest__.py`` file.

    Give the read, write, create and unlink permissions to the group ``base.group_user``.

    Tip: the warning message in the log gives you most of the solution ;-)

Views
=====

Views are the UI components that allow users to interact with the data. They are defined
in XML files and can be found in the `views` directory of your module.

Since views and actions are already defined in :doc:`server_framework_101/05_firstui` and
:doc:`server_framework_101/06_basicviews`, we will not go into details here.

.. exercise:: Add a basic UI to the `estate` module.

    Add a basic UI to the `estate` module to allow users to view, create, edit and delete
    Real Estate properties.

    - Create an action for the model ``x_estate.property``
    - Create a tree view for the model ``x_estate.property``
    - Create a form view for the model ``x_estate.property``
    - Add the views to the action
    - Add a menu item to the main menu to allow users to access the action

Relations
=========

The real power of relational systems like Odoo lies in the ability to link records together.
In a normal Python module, one could define new fields on a model to link it to other models
in a single line of code. In a data module, this is still possible but requires a bit more
legwork since we can't use the same syntax as in Python.

As in :doc:`server_framework_101/07_relations`, we will add some relations to our `estate`
module. We will add links to:

- the customer who bought the property
- the real estate agent who sold the property
- the property type: house, apartment, penthouse, castle...
- a list of tags characterizing the property: cozy, renovated...
- a list of the offers received

Many2one
--------

A many2one is a simple link to another object. For example, in order to define a link to the
``res.partner``, we can define a new field in our model:

.. code-block:: xml

    <odoo>
        <!-- ...model definition from before... -->
        <record model="ir.model.fields" id="field_real_estate_property_partner_id">
            <field name="model_id" ref="estate.model_real_estate_property" />
            <field name="name">x_partner_id</field>
            <field name="field_description">Customer</field>
            <field name="ttype">many2one</field>
            <field name="relation">res.partner</field>
      </record>
    </odoo>

In the case of many2one fields, several attributes can be set to detail the relation:

- `relation`: the name of the model to link to
- `ondelete`: the action to perform when the record is deleted (default: `set null`)
- `domain`: a domain filter to apply to the relation

.. exercise:: Add relational fields to the Real Estate Property model.

    - Create a new model ``x_estate.property.type`` with the following fields:

    ========================= ========================= =======================
    Field                     Type                       Required
    ========================= ========================= =======================
    name                      Char                       True
    ========================= ========================= =======================

    - Add an action, list view and menu item for the ``x_estate.property.type`` model

    - Create the following fields on the ``x_estate.property`` model:

    ========================= ====================================== =======================
    Field                     Type                                   Required
    ========================= ====================================== =======================
    x_property_type_id        Many2one (``x_estate.property.type``)  True
    x_partner_id (buyer)      Many2one (``res.partner``)
    x_user_id (salesperson)   Many2one (``res.users``)
    ========================= ====================================== =======================

Many2many
---------

A many2many is a relation to a list of objects. In our example, we will define a many2many
relation towards a new ``x_estate.property.tag`` model. This tag represent a characteristic
of the property, for example: renovated, cozy, etc.

A property can have many tags and a tag can be assigned to many properties - this is the
typical many2many relationship.

Many2many fields are defined in the same way as many2one fields, but with the `ttype`
set to `many2many`. The `relation` attribute is also set to the name of the model
to link to. Other attributes can be set to control the relation:

- `relation_table`: the name of the table to use for the relation
- `column1` and `column2`: the names of the columns to use for the relation

These attributes are optional, and should usually be specified only when there
are multiple many2many fields between two models to avoid conflict; in most cases,
the Odoo ORM will be able to determine the correct relation table and columns to use.


.. exercise:: Add a many2many relation to the Real Estate Property model.

    - Create a new model ``x_estate.property.tag`` with the following fields:

    ========================= ========================== =======================
    Field                     Type                       Required
    ========================= ========================== =======================
    name                      Char                       True
    ========================= ========================== =======================

    - Add an action, list view and menu item for the ``x_estate.property.tag`` model

    - Create the following fields on the ``x_estate.property`` model:

    ========================= ==========================
    Field                     Type                      
    ========================= ==========================
    x_property_tag_ids        Many2many (``x_estate.property.tag``)
    ========================= ==========================


One2many
--------

A one2many is a relation to a list of objects. In our example, we will define a one2many
relation towards a new ``x_estate.property.offer`` model. This offer represent an offer
made by a customer to buy a property.

One2many fields are defined in the same way as many2one fields, but with the `ttype`
set to `one2many`. The `relation` attribute is also set to the name of the model
to link to. Another attribute must be set to control the relation:

- `relation_field`: the name of the field on the related model that contains the
  reference to the parent model (many2one field). This is used to link the two models
  together.

.. exercise:: Add a one2many relation to the Real Estate Property model.

    - Create a new model ``x_estate.property.offer`` with the following fields:

    ========================= ================================== ======================= ===================
    Field                     Type                               Attributes              Values
    ========================= ================================== ======================= ===================
    x_price                   Float                              required
    x_status                  Selection                                                  Accepted, Refused
    x_partner_id              Many2one (``res.partner``)         required
    x_property_id             Many2one (``x_estate.property``)   required
    ========================= ================================== ======================= ===================

    - Create a tree view and a form view with the price, partner_id and status fields.
      No need to create an action or a menu.

    - Add the field ``x_offer_ids`` to your ``x_estate.property`` model and in its form view.

Computed and Related Fields
===========================

Computed Fields
---------------

Computed fields are an core concept in Odoo and are used to define fields that are computed
based on other fields. This is useful for fields that are derived from other fields, like a
sum of sub-records (adding up the price of all the items in a sale order).

**Reference**: the documentation related to this topic can be found in
:ref:`reference/fields/compute`.

Data modules can define computed fields of any type, but are quite limited compared to Python
modules. Indeed, since data modules are meant to be deployed on systems that do not allow arbitrary
code to run, the Python code that is allowed is very limited.

.. note::

    All Python code written for data modules is executed in a sandboxed environment that limits
    the operations that can be performed. For example, you cannot import libraries, you cannot access
    any OS files, and you cannot even print to the console. Some utilities are provided, but this
    varies with the type of sandboxed environment that is used.
    
    In the case of compute methods, the sandbox is very limited and only provide the bare minimum
    of utilities to allow the execution of the code. In addition to the Python builtins, you also
    have access to the `datetime`, `dateutil` and `time` modules (e.g. to help with date calculations).

    Note also that "dot assignation" is disabled in the sandbox, so you cannot write
    ``property.x_total_area = 1`` in the compute method. You have to use dictionary access:
    ``property['x_total_area'] = 1``. Dot notation for field *access* works normally:
    ``property.x_garden_area`` will return the value of the ``garden_area`` field.


We previously defined two "area" fields on our ``x_estate.property`` model: ``living_area``
and ``garden_area``. To define a computed field on the model that returns the sum of the two
areas, we can add the following code to our data module:

.. code-block:: xml

    <odoo>
        <!-- ...model definition from before... -->
        <record model="ir.model.fields" id="field_real_estate_property_total_area">
            <field name="model_id" ref="estate.model_real_estate_property" />
            <field name="name">x_total_area</field>
            <field name="field_description">Total Area</field>
            <field name="ttype">float</field>
            <field name="depends">living_area,garden_area</field>
            <field name="compute"><![CDATA[ 
    for property in self:
        property['x_total_area'] = property.x_living_area + property.x_garden_area
            ]]>
        </field>
        </record>
    </odoo>

The ``depends`` attribute is used to define the fields that the computed field depends on and 
the ``compute`` attribute is used to define the code that is executed to compute the field.

Computed fields are not stored in the database by default, however they can be stored by setting
the ``store`` attribute to ``True``.

The `CDATA` section is used to specify to XML parsers that the content is a string and not XML;
this prevents the parser from trying to interpret the Python code as XML, or the addition of
extra space, etc. when the code gets inserted into the database at module install time.

.. exercise:: Add a computed field to the Real Estate Property model.

    - Add a computed field to the ``x_estate.property`` model that returns the sum of the
      ``x_living_area`` and ``x_garden_area`` fields, as shown above
    - Include the field in the form view of the ``x_estate.property`` model


.. note::

    Unlike in Python modules, it is not possible to define an inverse or search method for
    computed fields.


Related Fields
--------------

Related fields are a simplified form of computed fields that mirror the value of another field
through a many2one relationship.

**Reference**: the documentation related to this topic can be found in
:ref:`reference/fields/related`.

Related fields can be of any type (the type of the field at the other end of the relation
traversal). They are defined as if one were adding the field directly to the model with the
addition of a ``related`` attribute that specifies the target field on the related model
that contains the value to be mirrored.

For example, if we want to access the country of the buyer directly from the
``x_estate.property`` model, we can add the following code to our data module:

.. code-block:: xml

    <odoo>
        <!-- ...model definition from before... -->
        <record model="ir.model.fields" id="field_real_estate_property_country_id">
            <field name="model_id" ref="estate.model_real_estate_property" />
            <field name="name">x_country_id</field>
            <field name="field_description">Buyer's Country</field>
            <field name="ttype">many2one</field>
            <field name="relation">res.country</field>
            <field name="related">x_partner_id.country_id</field>
        </record>
    </odoo>

The ``related`` attribute is used to specify the target field on the related model that
contains the value to be mirrored. This must be a dot-separated list of field names.

.. note::

    Like computed fields, related fields are not stored in the database by default.
    You can force the storage of the field in the database by setting the ``store``
    attribute to ``True``.


Server Actions
==============


In a Python module, you are free to define any method on your model. One common useage pattern
is to add so-called "actions" methods to your model then bind these methods to buttons in the UI (e.g
to confirm a quote, post an invoice, etc.).

In a data module, you can achieve the same effect by defining :ref:`Server Actions <reference/actions/server>`
bound to your model. Server actions represent pieces of logic that are run dynamically on the server. These actions
can be configured manually in the databse directly via the
:menuselection:`Settings --> Technical --> Actions --> Server Actiosn` menu and can take several forms; in our
case, we will use the ``code`` type which allows to run any Python code in a sandboxed environment.

This environment contains several utilities to help you interact with the Odoo database:

- ``self``: the record on which the action is executed
- ``env``: the environment of the record
- ``model``: the model of the record
- ``user`` and ``uid``: the current user and their id
- ``datetime``, ``dateutil``, ``timezone`` and ``time``: libraries to help with date/time calculations
- ``float_compare``: a utility function to compare two float values with a given precision
- ``b64encode`` and ``b64decode``: utility functions to encode and decode values in base64
- ``Command``: a utility class to help build complex expressions and commands (see the `Command` class in the :ref:`ORM reference <reference/fields/relational>`)

In addition, you are provided to the recordset on which the action is executed (typically a single record when
the action is executed from a form view, and a multiple records when the action is executed from a list view)
via the ``record`` and ``records`` variables.

.. note::

    If you action needs to return an action to the client (for example to redirect the user to
    another view), you can assign it the a semi-magical ``action`` variable inside your server action's
    code. The code sandbox will inspect the variables defined in your code after its execution and will
    automatically return it if it detects the presence of an ``action`` variable.

For example, we could define an action on the ``x_estate.property`` model that sets the ``x_status``
field of all its offers to ``Refused``:

.. code-block:: xml

    <record model="ir.actions.server" id="action_x_estate_property_refuse_all_offers">
        <field name="name">Refuse all offers</field>
        <field name="model_id" ref="estate.model_real_estate_property"/>
        <field name="state">code</field>
        <field name="code"><![CDATA[
    for property in records:
        property.x_offer_ids.write({'x_status': 'refused'})
        ]]></field>
    </record>

To include this action as a button in the form view of the ``x_estate.property`` model, we can
add the following :ref:`button <reference/view_architectures/form/button>` node in the header of our form view:

.. code-block:: xml

    <!-- form view definition from your code... -->
    <header>
        <button name="estate.action_x_estate_property_refuse_all_offers" type="action" string="Refuse all offers"/>
    </header>



.. exercise:: Add an action to the Real Estate Offer model.

    - Add a server action to the ``x_estate.property.offer`` model that sets the ``x_status``
      field of an offer to ``Accepted`` and update the selling price and buyer of the property
      to which the offer is attached accordingly
    - Include a button in the embedded list view of offers that allows to execute this action

Automation Rules
================

Automations Rules are a way to automatically execute actions on records in the database based on
specific triggers. They can be useful to tie behaviour to life-cycle events of records, for
example by sending an email when an offer is accepted.

**Documentation**: a more complete documentation related to this topic can be found in
:doc:`/applications/studio/automated_actions`.

.. note::

    Automation Rules are not part of the ``base`` module; they come with the ``base_automation``
    module; so if you define automation rules in your data module, you need to make sure that
    ``base_automation`` is part of your module's dependencies.

    Once installed, Automation Rules are managed via the
    :menuselection:`Settings --> Technical --> Automations --> Automation Rules` menu.

Automation Rules are particularly useful to tie a data module to an existing standard Odoo
module. Since data modules cannot override methods, tying automation to life-cycle changes
of standard models is a common way to extend standard modules. A typical example would be
an integration with the ``Sales`` app of Odoo. Let's imagine that your Real Estate module
instegrates with the Sales application so that when a specific product is sold (e.g. a quote
for managing the sale of a property), you want to automatically create a new property
record in your module.

In a typical Python module, you would override the ``action_confirm`` method of the
``sale.order`` model to add this logic. In a data module, you can't do this, but you can
create an automation rule that listens for the ``Sales Order`` model and creates a new
``x_estate.property`` record when a quote changes from the ``Draft`` or ``Sent`` state
to the ``Sales Order`` state and execute your logic in a server action that gets triggered
by the automation rule.

Automation Rules can trigger multiple actions of different types. This means that our module
should define first the action that will be executed by the automation rule, and then
the automation rule itself.

.. code-block:: xml

    <record model="ir.actions.server" id="action_x_estate_property_create_from_sale_order">
        <field name="name">Create property from sale order</field>
        <field name="model_id" ref="sale.model_sale_order"/>
        <field name="state">code</field>
        <field name="code"><![CDATA[
    for order in records:
        property = env['x_estate.property'].sudo().create({
            'x_name': sale_order.name,
            'x_expected_price': 0,
        })
        ]]></field>
    </record>

    <record model="ir.automation.rule" id="automation_rule_x_estate_property_create_from_sale_order">
        <field name="name">Create property from sale order</field>
        <field name="model_id" ref="sale.model_sale_order"/>
        <field name="trigger">on_state_set</field>
        <field name="trg_selection_field_id" ref="sale.selection__sale_order__state__sale"/>
        <field name="action_server_ids" eval="[(4, action_x_estate_property_create_from_sale_order.id)]"/>
    </record>

Note that the :ref:`XML IDs <tutorials/define_module_data/xml_id>` to standard Odoo models, fields,
selection values, etc. can be found in the Odoo instance itself by navigating to the appropriate record
in the technical menus and using the ``View Metadata`` menu entry of the debug menu.

Controllers
===========

TODO: explain how to use server actions with the website module to create custom controllers

Static Files
============

TODO: show assets and explain that everything is normal

Constraint is the mother of invention
=====================================

TODO: illustrate several common patterns with code snippets

- overriding a method on a standard model
    - with automation rule
    - with button switcheroo
- ?