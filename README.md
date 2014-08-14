# addressfield.json [![Build Status](https://travis-ci.org/tableau-mkt/addressfield.json.svg?branch=master)](https://travis-ci.org/tableau-mkt/addressfield.json)

This is a JSON configuration representing the way an address form should be
rendered on a country-by-country basis, conforming roughly to xNAL standards.
It encodes:
* Field labels, mapped to their corresponding xNAL field name,
* Field order, according to the norms of the country,
* And (optionally) field options for fields with a limited, known values list.

Why? Because every country writes their address differently, and there's no
reason to reinvent the wheel every time your software asks for those details.

## Configuration format
The top level represents the configuration for a "country" select field.
```json
{
  "label" : "Country",
  "options" : {
    "CA" : {
      "label" : "Canada"
    }
  }
}
```
...Where the "options" key is a list of field configurations, keyed by country
ISO-3166 alpha-2 shortcodes. Each field configuration object also has a
"label" key, representing the name of the country.

Field configuration objects have a recursive structure, which can contain any
number of xNAL field names with special keys `label` and `options`. For
instance:

```json
{
  "CA" : {
    "label" : "Canada",
    "locality" : {
      "localityname" : {
        "label" : "City"
      },
      "administrativearea" : {
        "label" : "Province",
        "options" : {
          "AB" : "Alberta",
          "BC" : "British Columbia"
        }
      }
    }
  }
}
```

## Software implementation
In the above example, the address form should render two fields for Canada:
* The xNAL "localityname" field, as text input, labled "City"
* The xNAL "administrativearea" field, as a select list, labeled "Province."
  * The `options` object maps the respective provinces' shortcodes to their full
    names. Some countries do not use shortcodes for their administrative areas;
    in those cases, the key should be the "stored" value while the value is that
    shown to the end-user.
* Both fields should be nested within a "locality" fieldset or wrapper
of some kind.

Also note that the defined fields as well as the defined options should be
rendered in the order specified.

For a sample implementation, in JavaScript, see
[jquery.addressfield](https://github.com/tableau-mkt/jquery.addressfield).
