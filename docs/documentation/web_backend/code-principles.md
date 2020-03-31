## Code Principles

### Filters

Filters are the main way to transform data that was received to data that can be used by 
graphs in the dashboard.

In general a filter has some inputs and one output. They do not have any dependencies 
outside of the Filter namespace. Since each filter only has simple inputs and outputs,
they can be easily chained together. For example a filter could create two other filters
and then combine their output to an output of its own. Filters are designed this way to
make them reusable and easier to maintain.

To better predict the inputs and outputs of a filter, interfaces are used. An
example use case for this is the `TimeFilterInterface`. This Interface forces
the Filter to have the inputs `startDate` and `endDate`. For each interface
there usually is a trait that implements the interface, so to make a filter that
accepts a start and end date the filter only needs to implement the interface and
use the trait. Filters are stored in `app/Filter`, interfaces and traits for filters 
are stored in `app\Filter\Contracts`.

The usual syntax to use a filter is as follows:
``` php
$filter = new Filter();
$result = $filter->input1('test')->input2('test2')->filter();
```
First a filter is instantiated. Then the filer is given some input and finally 
the `filter()` method is called. Each input function returns the filter to enable
chaining of inputs. The `filter()` method is always the last called function, it
executes the filter and returns the result. This syntax makes it possible to reuse
filters with ease, since input stay the same between `filter()` calls and inputs
can be changed by calling the `input()` function again. If a filter is missing a
required argument, a `MissingFilterArguemntExcetpion` is raised.

### Localization

The entire dashboard is localized for english and german. The translations files 
can be found in the `resources/lang/` directory.

Since the dashboard is split between plain Laravel Blade files and Vue Components, 
there are two ways to get localized strings.

In Blade files the standard Laravel localization function `__('localization-key')`
can be used within double curly braces. 

To localize strings in Vue [Lang.js](https://github.com/rmariuzzo/Lang.js/) is
used. Lang.js uses a global Vue namespace.  To access a localization in the
template, simply use `$lang.get('localization-key')`. If you want the use the
localization in your components methods, use `this.$lang.get('localization-key')`.

!!! info "The syntax for the `localization-key` is the same for both methods"
    The keys syntax is `localization-file.localization-key`, Where
    `localization-file` is the file in `/resources/lang/` and `localization-key`
    is the key specified in the file. More on this can be found in the
    [Laravel documentation for Localization](https://laravel.com/docs/6.x/localization).

### Authentification
The authentication is done through the Laravel Passport package. The documentation for
this can be found [here](https://laravel.com/docs/6.x/passport).

### Testing
Testing is done with `phpunit`. To execute `phpunit` for the whole project
run `./vendor/bin/phpunit`. Keep in mind that the test erases the database
so you might want to seed the database after running tests. Tests are 
located at `/tests`. More on this can be read in the 
[Laravel documentation](https://laravel.com/docs/6.x/testing).

### Code Style

To keep the code style the same over multiple files in the project we use 
[Phpinsights](https://phpinsights.com/) to locally analyse all files.

### Flow of Adding a New Graph with New Input Data

This will describe de flow of accepting new data from the app and then displaying
a chart with that new data in the home tab.

The first thing to do is create a model for the new data. The model should be
placed in `app/Models/Datamining/` directory. Since we use `laravel-mongo-db`, models
only need to define the `fillable` array and the relation to the user.
Be sure to also **add the relation to the new model on the user**.

Next you should delete model instances in the user repository. This has to be done
for privacy reasons, since models should only get deleted when the corresponding
user gets deleted.

!!! tip "Factory for the new model"
    A factory can create model instances with randomized data, which is very
    useful for testing the chart later. The documentation for factories can
    be found [here](https://laravel.com/docs/6.x/database-testing#writing-factories).

After adding the model you can write the mutation to create a new model. 
Since events are send in batches from the app, to reduce the number of requests made,
we have to create an input and a type for each model we want to create via a GraphQL
mutation. It is best to create a new GraphQL file for this, to separate the types for
this model from all the other models. 

This concludes receiving of data from the app. Next we have to write a filter
to transform the data so we can display it in a chart. The concept behind filters
and how to write them is already covered in the filter section. After writing the filter
you have to construct a GraphQL query to use the filter data in the dashboard. The query
should be placed in the same file as the type and the input that we created earlier.

!!! danger "Make sure you extend the root element Query in your new file"
    It can be done like this:
    ```json
    extend type Query {
        // your query
    }
    ```
    The same thing applies to mutations.

The final step is to create the chart. For this you can use the already existing chart as
a guide. First you have to decide on the chart type. Some chart types like a line chart are
already implemented by us. If none of the charts fit your data, you can also choose
any chart type that is provided by [chart.js](https://www.chartjs.org/samples/latest/). To use
a new chart type you should look up how to create a new chart in the 
[vue-chatjs documentation](https://vue-chartjs.org/guide/). The next thing is to create a new
Vue component that supplies the chart with data and executes the query to retrieve the data.
You can copy most of this process from an existing chart.

!!! info "Registering a new chart component"
    Vue chart components live in the `resources/js/components/charts/` directory. To add
    a new component, first add the `.vue` file in the directory. Then export the component 
    in the `index.js` file in the same directory. The last step is to import the new 
    component in the `app.js` file and add the new component to Vue via the 
    `Vue.component()` function. **Note** that all components created by you should 
    have the prefix `dk-`.

After crating the chart component and registering it with Vue, the only thing left to do is
adding the chart to the `home.blade.php` file. Note that you should encapsule your chart
component in a `dk-chart-container` to give it a title and align it with the grid layout.

That should be it. You should now have a new chart in you dashboard that displays the new
data from the app. If you chart is not showing any data, that is most likely due to your
data not being in the correct format. You should look into the `chartjs` documentation 
for the correct format and then try to debug your data.
:rocket:

