# Sequelize with Views & Materialized Views

[![NPM version](https://img.shields.io/npm/v/sequelize-mv-support.svg)](https://www.npmjs.com/package/sequelize-mv-support)
[![Build Status](https://cloud.drone.io/api/badges/sugarandmagic/sequelize-mv-support/status.svg)](https://cloud.drone.io/sugarandmagic/sequelize-mv-support)
[![semantic-release](https://img.shields.io/badge/%20%20%F0%9F%93%A6%F0%9F%9A%80-semantic--release-e10079.svg)](https://github.com/semantic-release/semantic-release)

* * *

This package adds support for **Views** and **Materialized Views** in **Sequelize**.

**NOTE:** Currently it's only supported in **PostgreSQL**.

## Motivation

We're heavy users of views and materialized views, but we use Sequelize a lot for bootstrapping and testing database schema. This module was based on Abel Osorio's [sequelize-views-support](https://github.com/abelosorio/sequelize-views-support). In addition to the view support in Abel's module, this module adds support for both views and materialized views to Sequelize, as well as properly exporting typescript declarations. I also added integration tests for views and materialized views. 

**Read**

-   <https://stackoverflow.com/questions/48407329/cant-able-to-create-views-in-mysql-using-sequelize-orm>
-   <https://github.com/sequelize/sequelize/issues/7197>
-   <https://github.com/sequelize/sequelize/issues/3078>

## Install

    npm install --save sequelize-mv-support

## How to use

All the original Sequelize methods and options still work with this module, you can read about those in the [sequelize api docs](https://sequelize.org/master/identifiers.html). New and updated methods are documented in the [api docs](#api) section of this readme.

First, when creating the `sequelize` instance, you have to do it using this package and not the Sequelize's:

`sequelize.js`:

```javascript
const Sequelize = require('sequelize-mv-support');
// or
const { Sequelize, DataTypes } = require('sequelize-mv-support');
// or
import Sequelize from 'sequelize-mv-support';
// or
import { Sequelize } from 'sequelize-mv-support';

const sequelize = new Sequelize(
  // Use the same construction call you've been using so far
);
```

Then, when creating your view models you have to set two more options (let's call this view `Foo`):

`models/foo.js`:

```javascript
import Sequelize from 'sequelize-mv-support';

// create an instance of sequelize
const sequelize = new Sequelize(
  // Use the same construction call you've been using so far
);

const Foo = sequelize.define('foo', {
    field1: Sequelize.DATE,
    field2: Sequelize.STRING,
    // etc...
  },
  {
    treatAsView: true,
    viewDefinition: 'SELECT field1, field2 from items'
  }
);

(async () = {
  // sync your models
  await sequelize.sync();

  // query the model as usual
  const fooItems = await Foo.findAll();
})();
```

Then, when creating materialized view models you have to set two more options (let's call this materialized view `Bar`):

`models/bar.js`:

```javascript
import Sequelize from 'sequelize-mv-support';

// create an instance of sequelize
const sequelize = new Sequelize(
  // Use the same construction call you've been using so far
);

// define your materialized view model
const Bar = sequelize.define('bar', {
    field1: Sequelize.DATE,
    field2: Sequelize.STRING,
    // etc...
  },
  {
    treatAsMaterializedView: true,
    materializedViewDefinition: 'SELECT field1, field2 from items'
  }
);

(async () => {
  // sync your models
  await sequelize.sync();

  // refresh your materialized views as needed
  await Bar.refreshMaterializedView();

  // query the model as usual
  const barItems = await Bar.findAll();
})();
```

That's it. Take into account that views will be created after syncing all your models. This is because your views may depend on models.

## API

<!-- Generated by documentation.js. Update this documentation by updating the source code. -->

#### Table of Contents

-   [ModelOptionsWithViews](#modeloptionswithviews)
-   [Model](#model)
    -   [options](#options)
    -   [QueryInterface](#queryinterface)
    -   [drop](#drop)
        -   [Parameters](#parameters)
    -   [sync](#sync)
        -   [Parameters](#parameters-1)
    -   [syncView](#syncview)
    -   [syncMaterializedView](#syncmaterializedview)
    -   [getViewDefinition](#getviewdefinition)
    -   [getMaterializedViewDefinition](#getmaterializedviewdefinition)
    -   [refreshMaterializedView](#refreshmaterializedview)
-   [QueryInterfaceWithViews](#queryinterfacewithviews)
-   [Sequelize](#sequelize)
    -   [getQueryInterface](#getqueryinterface)
    -   [define](#define)
        -   [Parameters](#parameters-2)
        -   [Examples](#examples)
    -   [sync](#sync-1)
        -   [Parameters](#parameters-3)
    -   [syncViews](#syncviews)
    -   [getViews](#getviews)
    -   [syncMaterializedViews](#syncmaterializedviews)
    -   [getMaterializedViews](#getmaterializedviews)

### ModelOptionsWithViews

[src/ModelWithViews.ts:17-23](https://github.com/sugarandmagic/sequelize-mv-support/blob/77475d4d4a8bf56144d71764a4760633b45e38f5/src/ModelWithViews.ts#L17-L23 "Source code on GitHub")

**Extends ModelOptions**

Interface describing the options property on a model

### Model

[src/ModelWithViews.ts:42-128](https://github.com/sugarandmagic/sequelize-mv-support/blob/77475d4d4a8bf56144d71764a4760633b45e38f5/src/ModelWithViews.ts#L42-L128 "Source code on GitHub")

**Extends ModelOrig**

Model with view support added

#### options

[src/ModelWithViews.ts:44-44](https://github.com/sugarandmagic/sequelize-mv-support/blob/77475d4d4a8bf56144d71764a4760633b45e38f5/src/ModelWithViews.ts#L44-L44 "Source code on GitHub")

Type: OptionsType

#### QueryInterface

[src/ModelWithViews.ts:47-47](https://github.com/sugarandmagic/sequelize-mv-support/blob/77475d4d4a8bf56144d71764a4760633b45e38f5/src/ModelWithViews.ts#L47-L47 "Source code on GitHub")

Type: [QueryInterfaceWithViews](#queryinterfacewithviews)

#### drop

[src/ModelWithViews.ts:50-58](https://github.com/sugarandmagic/sequelize-mv-support/blob/77475d4d4a8bf56144d71764a4760633b45e38f5/src/ModelWithViews.ts#L50-L58 "Source code on GitHub")

##### Parameters

-   `options` **DropOptionsType**  (optional, default `{}`)

Returns **any** 

#### sync

[src/ModelWithViews.ts:61-66](https://github.com/sugarandmagic/sequelize-mv-support/blob/77475d4d4a8bf56144d71764a4760633b45e38f5/src/ModelWithViews.ts#L61-L66 "Source code on GitHub")

##### Parameters

-   `options` **SyncOptions** 

Returns **any** 

#### syncView

[src/ModelWithViews.ts:75-80](https://github.com/sugarandmagic/sequelize-mv-support/blob/77475d4d4a8bf56144d71764a4760633b45e38f5/src/ModelWithViews.ts#L75-L80 "Source code on GitHub")

Executes the query to create a view

Returns **[Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)&lt;\[[Array](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array)&lt;unknown>, unknown]>** Result of the create view request

#### syncMaterializedView

[src/ModelWithViews.ts:89-94](https://github.com/sugarandmagic/sequelize-mv-support/blob/77475d4d4a8bf56144d71764a4760633b45e38f5/src/ModelWithViews.ts#L89-L94 "Source code on GitHub")

Executes the query to create a materialized view

Returns **[Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)&lt;\[[Array](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array)&lt;unknown>, unknown]>** Result of the create materialized view request

#### getViewDefinition

[src/ModelWithViews.ts:103-105](https://github.com/sugarandmagic/sequelize-mv-support/blob/77475d4d4a8bf56144d71764a4760633b45e38f5/src/ModelWithViews.ts#L103-L105 "Source code on GitHub")

Gets the sql definition for this view

Returns **[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)** SQL query string to create a view

#### getMaterializedViewDefinition

[src/ModelWithViews.ts:114-116](https://github.com/sugarandmagic/sequelize-mv-support/blob/77475d4d4a8bf56144d71764a4760633b45e38f5/src/ModelWithViews.ts#L114-L116 "Source code on GitHub")

Gets the sql definition for this materialized view

Returns **[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)** SQL query string to create the materialized view

#### refreshMaterializedView

[src/ModelWithViews.ts:125-127](https://github.com/sugarandmagic/sequelize-mv-support/blob/77475d4d4a8bf56144d71764a4760633b45e38f5/src/ModelWithViews.ts#L125-L127 "Source code on GitHub")

Refreshes the materialized view in the database

Returns **[Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)&lt;\[[Array](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array)&lt;unknown>, unknown]>** 

### QueryInterfaceWithViews

[src/SequelizeWithViews.ts:37-55](https://github.com/sugarandmagic/sequelize-mv-support/blob/77475d4d4a8bf56144d71764a4760633b45e38f5/src/SequelizeWithViews.ts#L37-L55 "Source code on GitHub")

**Extends SequelizeOrig.QueryInterface**

Extended query interface including support for creating and dropping views

### Sequelize

[src/SequelizeWithViews.ts:63-338](https://github.com/sugarandmagic/sequelize-mv-support/blob/77475d4d4a8bf56144d71764a4760633b45e38f5/src/SequelizeWithViews.ts#L63-L338 "Source code on GitHub")

**Extends SequelizeOrig.Sequelize**

Sequelize class with view support

#### getQueryInterface

[src/SequelizeWithViews.ts:106-161](https://github.com/sugarandmagic/sequelize-mv-support/blob/77475d4d4a8bf56144d71764a4760633b45e38f5/src/SequelizeWithViews.ts#L106-L161 "Source code on GitHub")

Returns **[QueryInterfaceWithViews](#queryinterfacewithviews)** 

#### define

[src/SequelizeWithViews.ts:248-260](https://github.com/sugarandmagic/sequelize-mv-support/blob/77475d4d4a8bf56144d71764a4760633b45e38f5/src/SequelizeWithViews.ts#L248-L260 "Source code on GitHub")

Define a new model, representing a table in the database.

The table columns are defined by the object that is given as the second argument. Each key of the object represents a column

##### Parameters

-   `modelName` **[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)** The name of the model. The model will be stored in `sequelize.models` under this name
-   `attributes` **[Object](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)** An object, where each attribute is a column of the table. Each column can be either a DataType, a string or a type-description object, with the properties described below:
    -   `attributes.column` **([string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String) | DataTypes | [Object](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object))** The description of a database column
        -   `attributes.column.type` **([string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String) | DataTypes)** A string or a data type
        -   `attributes.column.allowNull` **[boolean](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Boolean)** If false, the column will have a NOT NULL constraint, and a not null validation will be run before an instance is saved. (optional, default `true`)
        -   `attributes.column.defaultValue` **any** A literal default value, a JavaScript function, or an SQL function (see `sequelize.fn`) (optional, default `null`)
        -   `attributes.column.unique` **([string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String) \| [boolean](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Boolean))** If true, the column will get a unique constraint. If a string is provided, the column will be part of a composite unique index. If multiple columns have the same string, they will be part of the same unique index (optional, default `false`)
        -   `attributes.column.primaryKey` **[boolean](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Boolean)** If true, this attribute will be marked as primary key (optional, default `false`)
        -   `attributes.column.field` **[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)** If set, sequelize will map the attribute name to a different name in the database (optional, default `null`)
        -   `attributes.column.autoIncrement` **[boolean](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Boolean)** If true, this column will be set to auto increment (optional, default `false`)
        -   `attributes.column.autoIncrementIdentity` **[boolean](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Boolean)** If true, combined with autoIncrement=true, will use Postgres `GENERATED BY DEFAULT AS IDENTITY` instead of `SERIAL`. Postgres 10+ only. (optional, default `false`)
        -   `attributes.column.comment` **[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)** Comment for this column (optional, default `null`)
        -   `attributes.column.references` **([string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String) \| [Model](#model))** An object with reference configurations (optional, default `null`)
            -   `attributes.column.references.model` **([string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String) \| [Model](#model))?** If this column references another table, provide it here as a Model, or a string
            -   `attributes.column.references.key` **[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)** The column of the foreign table that this column references (optional, default `'id'`)
        -   `attributes.column.onUpdate` **[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)?** What should happen when the referenced key is updated. One of CASCADE, RESTRICT, SET DEFAULT, SET NULL or NO ACTION
        -   `attributes.column.onDelete` **[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)?** What should happen when the referenced key is deleted. One of CASCADE, RESTRICT, SET DEFAULT, SET NULL or NO ACTION
        -   `attributes.column.get` **[Function](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/function)?** Provide a custom getter for this column. Use `this.getDataValue(String)` to manipulate the underlying values.
        -   `attributes.column.set` **[Function](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/function)?** Provide a custom setter for this column. Use `this.setDataValue(String, Value)` to manipulate the underlying values.
        -   `attributes.column.validate` **[Object](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)?** An object of validations to execute for this column every time the model is saved. Can be either the name of a validation provided by validator.js, a validation function provided by extending validator.js (see the `DAOValidator` property for more details), or a custom validation function. Custom validation functions are called with the value of the field and the instance itself as the `this` binding, and can possibly take a second callback argument, to signal that they are asynchronous. If the validator is sync, it should throw in the case of a failed validation; if it is async, the callback should be called with the error text.
-   `options` **[Object](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)** These options are merged with the default define options provided to the Sequelize constructor
    -   `options.sequelize` **[Object](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)** Define the sequelize instance to attach to the new Model. Throw error if none is provided.
    -   `options.modelName` **[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)?** Set name of the model. By default its same as Class name.
    -   `options.defaultScope` **[Object](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)** Define the default search scope to use for this model. Scopes have the same form as the options passed to find / findAll (optional, default `{}`)
    -   `options.scopes` **[Object](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)?** More scopes, defined in the same way as defaultScope above. See `Model.scope` for more information about how scopes are defined, and what you can do with them
    -   `options.omitNull` **[boolean](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Boolean)?** Don't persist null values. This means that all columns with null values will not be saved
    -   `options.timestamps` **[boolean](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Boolean)** Adds createdAt and updatedAt timestamps to the model. (optional, default `true`)
    -   `options.paranoid` **[boolean](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Boolean)** Calling `destroy` will not delete the model, but instead set a `deletedAt` timestamp if this is true. Needs `timestamps=true` to work (optional, default `false`)
    -   `options.underscored` **[boolean](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Boolean)** Add underscored field to all attributes, this covers user defined attributes, timestamps and foreign keys. Will not affect attributes with explicitly set `field` option (optional, default `false`)
    -   `options.freezeTableName` **[boolean](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Boolean)** If freezeTableName is true, sequelize will not try to alter the model name to get the table name. Otherwise, the model name will be pluralized (optional, default `false`)
    -   `options.name` **[Object](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)?** An object with two attributes, `singular` and `plural`, which are used when this model is associated to others.
        -   `options.name.singular` **[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)** Singular name for model (optional, default `Utils.singularize(modelName)`)
        -   `options.name.plural` **[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)** Plural name for model (optional, default `Utils.pluralize(modelName)`)
    -   `options.indexes` **[Array](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array)&lt;[Object](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)>?** indexes definitions
    -   `options.createdAt` **([string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String) \| [boolean](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Boolean))?** Override the name of the createdAt attribute if a string is provided, or disable it if false. Timestamps must be true. Underscored field will be set with underscored setting.
    -   `options.updatedAt` **([string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String) \| [boolean](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Boolean))?** Override the name of the updatedAt attribute if a string is provided, or disable it if false. Timestamps must be true. Underscored field will be set with underscored setting.
    -   `options.deletedAt` **([string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String) \| [boolean](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Boolean))?** Override the name of the deletedAt attribute if a string is provided, or disable it if false. Timestamps must be true. Underscored field will be set with underscored setting.
    -   `options.tableName` **[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)?** Defaults to pluralized model name, unless freezeTableName is true, in which case it uses model name verbatim
    -   `options.schema` **[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)** schema (optional, default `'public'`)
    -   `options.engine` **[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)?** Specify engine for model's table
    -   `options.charset` **[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)?** Specify charset for model's table
    -   `options.comment` **[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)?** Specify comment for model's table
    -   `options.collate` **[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)?** Specify collation for model's table
    -   `options.initialAutoIncrement` **[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)?** Set the initial AUTO_INCREMENT value for the table in MySQL.
    -   `options.hooks` **[Object](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)?** An object of hook function that are called before and after certain lifecycle events. The possible hooks are: beforeValidate, afterValidate, validationFailed, beforeBulkCreate, beforeBulkDestroy, beforeBulkUpdate, beforeCreate, beforeDestroy, beforeUpdate, afterCreate, beforeSave, afterDestroy, afterUpdate, afterBulkCreate, afterSave, afterBulkDestroy and afterBulkUpdate. See Hooks for more information about hook functions and their signatures. Each property can either be a function, or an array of functions.
    -   `options.validate` **[Object](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)?** An object of model wide validations. Validations have access to all model values via `this`. If the validator function takes an argument, it is assumed to be async, and is called with a callback that accepts an optional error.
    -   `options.treatAsMaterializedView` **[boolean](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Boolean)** Whether to treat this model as a materialised view (optional, default `false`)
    -   `options.treatAsView` **[boolean](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Boolean)** Whether to treat this model as a view (optional, default `false`)
    -   `options.viewDefinition` **[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)?** The query to be represented by a view
    -   `options.materializedViewDefinition` **[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)?** The query to be represented by a materialized view
-   `string` **[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)** \[].name] The name of the index. Defaults to model name + \_ + fields concatenated
-   `string` **[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)** \[].type] Index type. Only used by mysql. One of `UNIQUE`, `FULLTEXT` and `SPATIAL`
-   `string` **[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)** \[].using] The method to create the index by (`USING` statement in SQL). BTREE and HASH are supported by mysql and postgres, and postgres additionally supports GIST and GIN.
-   `string` **[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)** \[].operator] Specify index operator.
-   `boolean` **[boolean](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Boolean)** \[].unique=false] Should the index by unique? Can also be triggered by setting type to `UNIQUE`
-   `boolean` **[boolean](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Boolean)** \[].concurrently=false] PostgresSQL will build the index without taking any write locks. Postgres only

##### Examples

```javascript
sequelize.define(
   viewName,
   {
     id: {
       type: Sequelize.INTEGER,
       primaryKey: true,
     },
     name: Sequelize.STRING,
   },
   {
     freezeTableName: true,
     treatAsView: true,
     viewDefinition: 'SELECT id, name from items',
   }
 );

sequelize.models.modelName // The model will now be available in models under the name given to define
```

Returns **[Model](#model)** Newly defined model

#### sync

[src/SequelizeWithViews.ts:277-283](https://github.com/sugarandmagic/sequelize-mv-support/blob/77475d4d4a8bf56144d71764a4760633b45e38f5/src/SequelizeWithViews.ts#L277-L283 "Source code on GitHub")

Sync all defined models to the DB.

##### Parameters

-   `options` **[Object](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object)** sync options (optional, default `{}`)
    -   `options.force` **[boolean](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Boolean)** If force is true, each Model will run `DROP TABLE IF EXISTS`, before it tries to create its own table (optional, default `false`)
    -   `options.match` **[RegExp](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/RegExp)?** Match a regex against the database name before syncing, a safety check for cases where force: true is used in tests but not live code
    -   `options.logging` **([boolean](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Boolean) \| [Function](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/function))** A function that logs sql queries, or false for no logging (optional, default `console.log`)
    -   `options.schema` **[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)** The schema that the tables should be created in. This can be overridden for each table in sequelize.define (optional, default `'public'`)
    -   `options.searchPath` **[string](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String)** An optional parameter to specify the schema search_path (Postgres only) (optional, default `DEFAULT`)
    -   `options.hooks` **[boolean](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Boolean)** If hooks is true then beforeSync, afterSync, beforeBulkSync, afterBulkSync hooks will be called (optional, default `true`)
    -   `options.alter` **([boolean](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Boolean) \| [Object](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object))** Alters tables to fit models. Provide an object for additional configuration. Not recommended for production use. If not further configured deletes data in columns that were removed or had their type changed in the model. (optional, default `false`)
        -   `options.alter.drop` **[boolean](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Boolean)** Prevents any drop statements while altering a table when set to `false` (optional, default `true`)

Returns **[Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)** 

#### syncViews

[src/SequelizeWithViews.ts:291-295](https://github.com/sugarandmagic/sequelize-mv-support/blob/77475d4d4a8bf56144d71764a4760633b45e38f5/src/SequelizeWithViews.ts#L291-L295 "Source code on GitHub")

Executes the create view query for each of the view definitions

Returns **[Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)&lt;[Array](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array)&lt;any>>** The results of the create view queries

#### getViews

[src/SequelizeWithViews.ts:303-309](https://github.com/sugarandmagic/sequelize-mv-support/blob/77475d4d4a8bf56144d71764a4760633b45e38f5/src/SequelizeWithViews.ts#L303-L309 "Source code on GitHub")

Gets all the defined models which represent views

Returns **[Array](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array)&lt;any>** 

#### syncMaterializedViews

[src/SequelizeWithViews.ts:317-323](https://github.com/sugarandmagic/sequelize-mv-support/blob/77475d4d4a8bf56144d71764a4760633b45e38f5/src/SequelizeWithViews.ts#L317-L323 "Source code on GitHub")

Executes the create materialized view query for each of the definitions

Returns **[Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)&lt;[Array](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array)&lt;any>>** The results of the create view queries

#### getMaterializedViews

[src/SequelizeWithViews.ts:331-337](https://github.com/sugarandmagic/sequelize-mv-support/blob/77475d4d4a8bf56144d71764a4760633b45e38f5/src/SequelizeWithViews.ts#L331-L337 "Source code on GitHub")

Gets all the defined models which represent materialized views

Returns **[Array](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array)&lt;any>** 

## Maintainers

-   **[Carrie Beesley](https://github.com/sugarandmagic)**
-   **[Bill Beesley](https://github.com/bbeesley)**
