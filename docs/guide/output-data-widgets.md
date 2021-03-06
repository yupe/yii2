Data widgets
============

> Note: This section is under development.

ListView
--------



DetailView
----------

DetailView displays the detail of a single data [[yii\widgets\DetailView::$model|model]].
 
It is best used for displaying a model in a regular format (e.g. each model attribute is displayed as a row in a table).
The model can be either an instance of [[\yii\base\Model]] or an associative array.
 
DetailView uses the [[yii\widgets\DetailView::$attributes]] property to determines which model attributes should be displayed and how they
should be formatted.
 
A typical usage of DetailView is as follows:
 
```php
echo DetailView::widget([
    'model' => $model,
    'attributes' => [
        'title',             // title attribute (in plain text)
        'description:html',  // description attribute in HTML
        [                    // the owner name of the model
            'label' => 'Owner',
            'value' => $model->owner->name,
        ],
    ],
]);
```


GridView
--------

Data grid or GridView is one of the most powerful Yii widgets. It is extremely useful if you need to quickly build admin
section of the system. It takes data from [data provider](data-providers.md) and renders each row using a set of columns
presenting data in a form of a table.

Each row of the table represents the data of a single data item, and a column usually represents an attribute of
the item (some columns may correspond to complex expression of attributes or static text).

Grid view supports both sorting and pagination of the data items. The sorting and pagination can be done in AJAX mode
or normal page request. A benefit of using GridView is that when the user disables JavaScript, the sorting and pagination
automatically degrade to normal page requests and are still functioning as expected.

The minimal code needed to use GridView is as follows:

```php
use yii\grid\GridView;
use yii\data\ActiveDataProvider;

$dataProvider = new ActiveDataProvider([
    'query' => Post::find(),
    'pagination' => [
        'pageSize' => 20,
    ],
]);
echo GridView::widget([
    'dataProvider' => $dataProvider,
]);
```

The above code first creates a data provider and then uses GridView to display every attribute in every row taken from
data provider. The displayed table is equipped with sorting and pagination functionality.

### Grid columns

Yii grid consists of a number of columns. Depending on column type and settings these are able to present data differently.

These are defined in the columns part of GridView config like the following:

```php
echo GridView::widget([
    'dataProvider' => $dataProvider,
    'columns' => [
        ['class' => 'yii\grid\SerialColumn'],
        // A simple column defined by the data contained in $dataProvider.
        // Data from model's column1 will be used.
        'id',
        'username',
        // More complex one.
        [
            'class' => 'yii\grid\DataColumn', // can be omitted, default
            'value' => function ($data) {
                return $data->name;
            },
        ],
    ],
]);
```

Note that if columns part of config isn't specified, Yii tries to show all possible data provider model columns.

### Column classes

Grid columns could be customized by using different column classes:

```php
echo GridView::widget([
    'dataProvider' => $dataProvider,
    'columns' => [
        [
            'class' => 'yii\grid\SerialColumn', // <-- here
            // you may configure additional properties here
        ],
```

Additionally to column classes provided by Yii that we'll review below you can create your own column classes.

Each column class extends from [[\yii\grid\Column]] so there some common options you can set while configuring
grid columns.

- `header` allows to set content for header row.
- `footer` allows to set content for footer row.
- `visible` is the column should be visible.
- `content` allows you to pass a valid PHP callback that will return data for a row. The format is the following:

  ```php
  function ($model, $key, $index, $column) {
      return 'a string';
  }
  ```

You may specify various container HTML options passing arrays to:

- `headerOptions`
- `contentOptions`
- `footerOptions`
- `filterOptions`

#### Data column

Data column is for displaying and sorting data. It is default column type so specifying class could be omitted when
using it.

The main setting of the data column is its format. It could be specified via `format` attribute. Its values are
corresponding to methods in `format` application component that is [[\yii\base\Formatter|Formatter]] by default:

```php
<?= GridView::widget([
    'columns' => [
        [
            'attribute' => 'name',
            'format' => 'text'
        ],
        [
            'attribute' => 'birthday',
            'format' => ['date', 'Y-m-d']
        ],
    ],
]); ?>
```

In the above `text` corresponds to [[\yii\base\Formatter::asText()]]. The value of the column is passed as the first
argument. In the second column definition `date` corresponds to [[\yii\base\Formatter::asDate()]]. The value of the
column is, again, passed as the first argument while 'Y-m-d' is used as the second argument value.

Here's the bundled formatters list:

- [[\yii\base\Formatter::asRaw()|raw]] - the value is outputted as is.
- [[\yii\base\Formatter::asText()|text]] - the value is HTML-encoded. This format is used by default.
- [[\yii\base\Formatter::asNtext()|ntext]] - the value is formatted as an HTML-encoded plain text with newlines converted
  into line breaks.
- [[\yii\base\Formatter::asParagraphs()|paragraphs]] - the value is formatted as HTML-encoded text paragraphs wrapped
  into `<p>` tags.
- [[\yii\base\Formatter::asHtml()|html]] - the value is purified using [[HtmlPurifier]] to avoid XSS attacks. You can
  pass additional options such as `['html', ['Attr.AllowedFrameTargets' => ['_blank']]]`.
- [[\yii\base\Formatter::asEmail()|email]] - the value is formatted as a mailto link.
- [[\yii\base\Formatter::asImage()|image]] - the value is formatted as an image tag.
- [[\yii\base\Formatter::asUrl()|url]] - the value is formatted as a hyperlink.
- [[\yii\base\Formatter::asBoolean()|boolean]] - the value is formatted as a boolean. You can set what's rendered for
  true and false values by calling `Yii::$app->formatter->booleanFormat = ['No', 'Yes'];` before outputting GridView.
- [[\yii\base\Formatter::asDate()|date]] - the value is formatted as date.
- [[\yii\base\Formatter::asTime()|time]] - the value is formatted as time.
- [[\yii\base\Formatter::asDatetime()|datetime]] - the value is formatted as datetime.
- [[\yii\base\Formatter::asInteger()|integer]] -  the value is formatted as an integer.
- [[\yii\base\Formatter::asDouble()|double]] - the value is formatted as a double number.
- [[\yii\base\Formatter::asNumber()|number]] - the value is formatted as a number with decimal and thousand separators.
- [[\yii\base\Formatter::asSize()|size]] - the value that is a number of bytes is formatted as a human readable size.
- [[\yii\base\Formatter::asRelativeTime()|relativeTime]] - the value is formatted as the time interval between a date
  and now in human readable form.

#### Action column

Action column displays action buttons such as update or delete for each row.

```php
echo GridView::widget([
    'dataProvider' => $dataProvider,
    'columns' => [
        [
            'class' => 'yii\grid\ActionColumn',
            // you may configure additional properties here
        ],
```

Available properties you can configure are:

- `controller` is the ID of the controller that should handle the actions. If not set, it will use the currently active
  controller.
- `template` the template used for composing each cell in the action column. Tokens enclosed within curly brackets are
  treated as controller action IDs (also called *button names* in the context of action column). They will be replaced
  by the corresponding button rendering callbacks specified in [[yii\grid\ActionColumn::$buttons|buttons]]. For example, the token `{view}` will be
  replaced by the result of the callback `buttons['view']`. If a callback cannot be found, the token will be replaced
  with an empty string. Default is `{view} {update} {delete}`.
- `buttons` is an array of button rendering callbacks. The array keys are the button names (without curly brackets),
  and the values are the corresponding button rendering callbacks. The callbacks should use the following signature:

```php
function ($url, $model) {
    // return the button HTML code
}
```

In the code above `$url` is the URL that the column creates for the button, and `$model` is the model object being
rendered for the current row.

- `urlCreator` is a callback that creates a button URL using the specified model information. The signature of
  the callback should be the same as that of [[yii\grid\ActionColumn::createUrl()]]. If this property is not set,
  button URLs will be created using [[yii\grid\ActionColumn::createUrl()]].

#### Checkbox column

CheckboxColumn displays a column of checkboxes.

To add a CheckboxColumn to the [[yii\grid\GridView]], add it to the [[yii\grid\GridView::$columns|columns]] configuration as follows:

```php
echo GridView::widget([
    'dataProvider' => $dataProvider,
    'columns' => [
        // ...
        [
            'class' => 'yii\grid\CheckboxColumn',
            // you may configure additional properties here
        ],
    ],
```

Users may click on the checkboxes to select rows of the grid. The selected rows may be obtained by calling the following
JavaScript code:

```javascript
var keys = $('#grid').yiiGridView('getSelectedRows');
// keys is an array consisting of the keys associated with the selected rows
```

#### Serial column

Serial column renders row numbers starting with `1` and going forward.

Usage is as simple as the following:

```php
echo GridView::widget([
    'dataProvider' => $dataProvider,
    'columns' => [
        ['class' => 'yii\grid\SerialColumn'], // <-- here
        // ...
```


### Sorting data

- https://github.com/yiisoft/yii2/issues/1576

### Filtering data

For filtering data the GridView needs a [model](model.md) that takes the input from the filtering
form and adjusts the query of the dataProvider to respect the search criteria.
A common practice when using [active records](active-record.md) is to create a search Model class
that provides needed functionality (it can be generated for you by Gii). This class defines the validation 
rules for the search and provides a `search()` method that will return the data provider.

To add search capability for the `Post` model we can create `PostSearch` like in the following example:

```php
<?php

namespace app\models;

use Yii;
use yii\base\Model;
use yii\data\ActiveDataProvider;

class PostSearch extends Post
{
    public function rules()
    {
        // only fields in rules() are searchable
        return [
            [['id'], 'integer'],
            [['title', 'creation_date'], 'safe'],
        ];
    }

    public function scenarios()
    {
        // bypass scenarios() implementation in the parent class
        return Model::scenarios();
    }

    public function search($params)
    {
        $query = Post::find();

        $dataProvider = new ActiveDataProvider([
            'query' => $query,
        ]);

        // load the seach form data and validate
        if (!($this->load($params) && $this->validate())) {
            return $dataProvider;
        }

        // adjust the query by adding the filters
        $query->andFilterWhere(['id' => $this->id]);
        $query->andFilterWhere(['like', 'title', $this->name])
              ->andFilterWhere(['like', 'creation_date', $this->creation_date]);

        return $dataProvider;
    }
}

```

You can use this function in the controller to get the dataProvider for the GridView:

```php
$searchModel = new PostSearch();
$dataProvider = $searchModel->search(Yii::$app->request->get());

return $this->render('myview', [
    'dataProvider' => $dataProvider,
    'searchModel' => $searchModel,
]);
```

And in the view you then assign the `$dataProvider` and `$searchModel` to the GridView:

```php
echo GridView::widget([
    'dataProvider' => $dataProvider,
    'filterModel' => $searchModel,
]);
```


### Working with model relations

When displaying active records in a GridView you might encounter the case where you display values of related
columns such as the post's author's name instead of just his `id`.
You do this by defining the attribute name in columns as `author.name` when the `Post` model
has a relation named `author` and the author model has an attribute `name`.
The GridView will then display the name of the author but sorting and filtering are not enabled by default.
You have to adjust the `PostSearch` model that has been introduced in the last section to add this functionality.

To enable sorting on a related column you have to join the related table and add the sorting rule
to the Sort component of the data provider:

```php
$query = Post::find();
$dataProvider = new ActiveDataProvider([
    'query' => $query,
]);

// join with relation `author` that is a relation to the table `users`
// and set the table alias to be `author`
$query->joinWith(['author' => function($query) { $query->from(['author' => 'users']); }]);
// enable sorting for the related column
$dataProvider->sort->attributes['author.name'] = [
    'asc' => ['author.name' => SORT_ASC],
    'desc' => ['author.name' => SORT_DESC],
];

// ...
```

Filtering also needs the joinWith call as above. You also need to define the searchable column in attributes and rules like this:

```php
public function attributes()
{
    // add related fields to searchable attributes
    return array_merge(parent::attributes(), ['author.name']);
}

public function rules()
{
    return [
        [['id'], 'integer'],
        [['title', 'creation_date', 'author.name'], 'safe'],
    ];
}
```

In `search()` you then just add another filter condition with:

```php
$query->andFilterWhere(['LIKE', 'author.name', $this->getAttribute('author.name')]);
```

> Info: In the above we use the same string for the relation name and the table alias, however when your alias and relation name
> differ, you have to pay attention on where to use the alias and where to use the relation name.
> A simple rule for this is to use the alias in every place that is used to build the database query and the
> relation name in all other definitions like in `attributes()` and `rules()` etc.
>
> For example you use the alias `au` for the author relation table, the joinWith statement looks like the following:
>
> ```php
> $query->joinWith(['author' => function($query) { $query->from(['au' => 'users']); }]);
> ```
> It is also possible to just call `$query->joinWith(['author']);` when the alias is defined in the relation definition.
>
> The alias has to be used in the filter condition but the attribute name stays the same:
>
> ```php
> $query->andFilterWhere(['LIKE', 'au.name', $this->getAttribute('author.name')]);
> ```
>
> Same is true for the sorting definition:
>
> ```php
> $dataProvider->sort->attributes['author.name'] = [
>      'asc' => ['au.name' => SORT_ASC],
>      'desc' => ['au.name' => SORT_DESC],
> ];
> ```
>
> Also when specifying the [[yii\data\Sort::defaultOrder|defaultOrder]] for sorting you need to use the relation name
> instead of the alias:
>
> ```php
> $dataProvider->sort->defaultOrder = ['author.name' => SORT_ASC];
> ```

> Info: For more information on `joinWith` and the queries performed in the background, check the
> [active record docs on eager and lazy loading](active-record.md#lazy-and-eager-loading).

#### Using sql views for filtering, sorting and displaying data

There is also other approach that can be faster and more useful - sql views. So for example if we need to show gridview 
with users and their profiles we can do it in this way:

```php
CREATE OR REPLACE VIEW vw_user_info AS
    SELECT user.*, user_profile.lastname, user_profile.firstname
    FROM user, user_profile
    WHERE user.id = user_profile.user_id
```

Then you need to create ActiveRecord that will be representing this view:

```php

namespace app\models\views\grid;

use yii\db\ActiveRecord;

class UserView extends ActiveRecord
{

    /**
     * @inheritdoc
     */
    public static function tableName()
    {
        return 'vw_user_info';
    }

    public static function primaryKey()
    {
        return ['id'];
    }

    /**
     * @inheritdoc
     */
    public function rules()
    {
        return [
            // define here your rules
        ];
    }

    /**
     * @inheritdoc
     */
    public static function attributeLabels()
    {
        return [
            // define here your attribute labels
        ];
    }


}
```

After that you can youse this UserView active record with search models, without additional specifying of sorting and filtering attributes.
All attributes will be working out of the box. Note that this approach has several pros and cons:

- you dont need to specify different sorting and filtering conditions and other things. Everything works out of the box;
- it can be much faster because of data size, count of sql queries performed (for each relation you will need additional query);
- since this is a just simple mupping UI on sql view it lacks of some domain logic that is in your entities, so if you will have some methods like `isActive`,
`isDeleted` or other that will influence on UI you will need to duplicate them in this class too.


### Multiple GridViews on one page

You can use more than one GridView on a single page but some additional configuration is needed so that
they do not interfere.
When using multiple instances of GridView you have to configure different parameter names for
the generated sort and pagination links so that each GridView has its individual sorting and pagination.
You do so by setting the [[yii\data\Sort::sortParam|sortParam]] and [[yii\data\Pagination::pageParam|pageParam]]
of the dataProviders [[yii\data\BaseDataProvider::$sort|sort]] and [[yii\data\BaseDataProvider::$pagination|pagination]]
instance.

Assume we want to list `Post` and `User` models for which we have already prepared two data providers
in `$userProvider` and `$postProvider`:

```php
use yii\grid\GridView;

$userProvider->pagination->pageParam = 'user-page';
$userProvider->sort->sortParam = 'user-sort';

$postProvider->pagination->pageParam = 'post-page';
$postProvider->sort->sortParam = 'post-sort';

echo '<h1>Users</h1>';
echo GridView::widget([
    'dataProvider' => $userProvider,
]);

echo '<h1>Posts</h1>';
echo GridView::widget([
    'dataProvider' => $postProvider,
]);
```

### Using GridView with Pjax

TBD
