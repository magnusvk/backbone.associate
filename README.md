Backbone.associate 
==================

Presumptionless model relations for Backbone in < 1kb.

[![Build Status](https://travis-ci.org/rjz/backbone-associate.png)](https://travis-ci.org/rjz/backbone-associate)


## Usage

Use `Backbone.associate` to define relationships between application models 
and collections.

    var Flag = Backbone.Model.Extend({ /* ... */ }),
        City = Backbone.Model.Extend({ /* ... */ }),
        Cities = Backbone.Collection.extend({ model: City }),
        Country = Backbone.Model.Extend({ /* ... */ });

    Backbone.associate(Country, {
      flag: { type: Flag },
      cities: { type: Cities }
    });

Here, we're associating a model (`Country`) with two relations: a `Flag`
model and a collection of `Cities`. The association keys can be anything,
but they should match the keys used in the data that will be passed into
the application's `parse` method.

    var canada = new Counry({
      flag: { 
        colors: ['red','white'] 
      },
      cities: [
        { name: 'Calgary' },
        { name: 'Regina' }
      ]
    });

When it's time to sync the parent resource back up with the server, 
child resources can be serialized and included in the request.

    canada.toJSON(); // { flag: { colors: ['red','white'] }, ...

Since associates are *just attributes*, they may be accessed at any 
time using the usual `get`. For the truly lazy, `associate` also extends
the base model with an accessor for each association:

    canada.flag().set({ colors: ['red','white'] });
    canada.cities().add([
      { name: 'Edmonton' },
      { name: 'Montreal' },
      { name: 'Ottawa' },
      { name: 'Vancouver' }
    ]);

That's handy for manipulating the relations, setting up eventing, or 
any of the many other things this plugin won't do for you. Speaking of
which,

## Things this plugin won't do...

...include configuring child URLs, identity mapping, and making any other 
kinds of presumptions about how it will be used. Fortunately, all of these 
can be implemented as needed 
([fiddle here](http://jsfiddle.net/rjzaworski/79T94/)):

#### Configure child URLs

    canada.cities().urlRoot = canada.url() + '/cities'
    canada.flag().url = canada.url() + '/flag'

#### Identity mapping

    var getCountry = function (id) {
      _countries = {};
      return (getCountry = function (id) {
        if (!_countries[id]) {
          _countries[id] = new Country({ id : id });
        }
        return _countries[id];
      })(id);
    };

#### Child events

    canada.onCityAdded = function (model) {
      console.log('city added!', model.get('name'));
    }

    canada.listenTo(canada.cities(), 'add', canada.onCityAdded);

## Testing

Specs are implemented with `jasmine-node`. After cloning this repo, 
install dependencies and test with npm.

    $ npm install
    $ npm test

## Contributing

Have something to add? Contributions are enormously welcome!

  1. Fork this repo
  2. Update the spec and implement the change
  3. Submit a [pull request](help.github.com/pull-requests/)

## License

Backbone.associate is released under the terms of the MIT license
