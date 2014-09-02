# addressfield.json [![Build Status](https://travis-ci.org/tableau-mkt/addressfield.json.svg?branch=master)](https://travis-ci.org/tableau-mkt/addressfield.json)

This is a JSON configuration representing the way an address form should be
rendered on a country-by-country basis, conforming roughly to xNAL standards. It
encodes:
* Field labels, mapped to their corresponding xNAL field names,
* Field order, according to the norms of the country,
* (Optional) field options for fields with a limited, known values list.
* (Optional) field format definitions (regex pattern matches) for free text
  fields with a known, well-defined format.

Why? Because every country writes their address differently, and there's no
reason to reinvent the wheel every time your software asks for those details.

## Configuration schema
The JSON config file can be described in terms of four configuration objects,
defined below.  The top / outer-most level represents the configuration for a
"country" select field, with a label and options list.
```json
{
  "label" : "Country",
  "options" : []
}
```
The "options" key is a list of country objects, defined below.

#### Country objects
Every country object consists of three mandatory properties:
- __label__: A string representing the English/anglicized name of the country.
- __iso__: The ISO-3166 alpha-2 shortcode associated with the country.
- __fields__: An array of field wrappers, defined below.
```json
{
  "label": "United Kingdom",
  "iso": "GB",
  "fields": []
}
```

#### Field wrapper objects
Every field wrapper object consists of a single property whose name represents
the xNAL field that the contained field represents, for instance `localityname`
or `thoroughfare`. The value of the property is either:
- __A field object__, representing the field to be rendered (defined below).
```json
{
  "thoroughfare": {}
}
```
- Or __a JSON array__, which contains further field wrappers. In this case, the
  field wrapper should be rendered as a fieldset / container. The most common
  case for this is the `locality` fieldset.
```json
{
  "locality": []
}
```

#### Field objects
Every field object consists of at least a `label` property, but can also contain
any number of additional properties specific to the field itself.
- __label__: A string representing the English/anglicized label for the given
  field.
- __options__: (Optional) An array of option objects (defined below). If an
  options property is provided on a field, the field should be rendered as a
  select list, rather than a text field.
- __format__: (Optional) A string representing a regex pattern against which
  user input can be matched / validated.
```json
{
  "label": "State",
  "options: [],
  "format": "^[a-zA-Z]{2}$"
}
```

#### Option objects
Options consist of a single property, whose name represents the "stored" or
"passed" value for a given field, and whose value is a string representing the
label to be shown to the end-user.
```json
{
  "WA": "Western Australia"
}
```

### Sample form configuration
```json
{
  "label": "Canada",
  "iso": "CA",
  "fields": [{
    "thoroughfare": {
      "label": "Address 1"
    }
  }, {
    "premise": {
      "label": "Address 2"
    }
  }, {
    "locality": [{
      "localityname": {
        "label": "City"
      }
    }, {
      "administrativearea": {
        "label": "Province",
        "options": [{
          "": "--"
        }, {
          "AB": "Alberta"
        }, {
          "BC": "British Columbia"
        }]
      }
    }, {
      "postalcode": {
        "label": "Postal code"
      }
    }]
  }]
}
```

## Software implementation
In the above sample configuration, the address form should render five fields
for Canada in the order specified above:
- First, the xNAL `thoroughfare` field as text input labeled "Address 1"
- Next, the xNAL `premise` field as text input labeled "Address 2"
- Finally, a fieldset or wrapper of some kind, representing `locality` and
  containing:
  - First, the xNAL `localityname` field as a text input labeled "City"
  - Next, The xNAL `administrativearea` field as a select list labeled
    "Province."
    - The `options` array contains objects that map the respective provinces'
      shortcodes to their full names; the form should pass shortcodes on submit,
      but display the full names to the end-user.
  - Finally, the xNAL `postalcode` field as a text input labeled "Postal code"

For a sample implementation, in JavaScript, see
[jquery.addressfield](https://github.com/tableau-mkt/jquery.addressfield).
