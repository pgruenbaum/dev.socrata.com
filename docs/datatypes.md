---
layout: default
title: Data Types in SODA 2
---

# {{ page.title }}

## The Types

The Socrata platform has many types.  Many of the types in Socrata differ from each other based on how they should be
displayed rather than how they should be stored, sorted and filtered.  SODA2 breaks apart these concepts and focuses on simplifying
the types by only focusing on the base types needed for storage, sorting and filtering.

In addition, there are some types in Socrata (such as location and URL) that consist of more than one field.  In these cases,
the "sub-objects" can be accessed using a ".".  For example, `location.latitude` will access the latitude field.

#### Strings
A string is an arbitrary sequence of Unicode characters.  How they are encoded for response will be dependent on the the negotiated HTTP charset.
In the case there are characters in the string that cannot be represented in the negotiated charset, they will be replaced.
It is STRONGLY recommended that all clients use UTF-8 to prevent this from happening.

Operations on Strings are case insensitive.  This includes sorting as well as comparison operations.

When using SoQL, string literals are created using the single quote (`'`).
    myColumn='string literal'


|Operation|Description|
|---|---|
|<|True for strings that are before this string.|
|<=|True for strings that are before or equal to this string.|
|=|True for strings that are equal to this string.|
|!=|True for strings that are not equal to this string.|
|>|True for strings that are after this string.|
|>=|True for strings that are after or equal to this string.|
|upper(x)|Returns the uppercase version of this string|
|lower(x)|Returns the lowercase version of this string|
|starts_with(x, y)|Returns true if the string x starts with the string y|
|contains(x, y)|Returns true if the string x contains the string y|

#### Number
Numbers are arbitrary precision, arbitrary scale numbers.  They can represent any number exactly, except for some numbers that repeat infinitely.

Since, numbers can be larger than doubles allow and more precise, many formats need to serialize them as strings.

|Operation|Description|
|---|---|
|<|True for numbers less than this one.|
|<=|True for numbers that are less than or equal to this one.|
|=|True for numbers that are equal to this one.|
|!=|True for numbers that are not equal to this one.|
|>|True for numbers that are greater than this one.|
|>=|True for numbers that are greater than or equal to this one.|
|\*|Multiplies two numbers|
|/|Divides one number by another|
|+|Adds two numbers|
|-|subtracts one number from another|

#### Double
A double (used to be number) is an IEEE floating point double. They are easy to use and are represented as numbers in
all the response formats, however, they have less precision than the regular Number data type.

Currently, all numbers are stored as Numbers.  Doubles exist as a way of making responses easier to consume, in the case
the caller wants results that are easier to parse and can potentially give up precision to do so.

|Operation|Description|
|---|---|
|<|True for numbers less than this one.|
|<=|True for numbers that are less than or equal to this one.|
|=|True for numbers that are equal to this one.|
|!=|True for numbers that are not equal to this one.|
|>|True for numbers that are greater than this one.|
|>=|True for numbers that are greater than or equal to this one.|
|\*|Multiplies two numbers|
|/|Divides one number by another|
|+|Adds two numbers|
|-|subtracts one number from another|


#### Boolean
Booleans can be either true or false.

|Operator|Description|
|---|---|
|AND|The logical **and** of two expressions.|
|OR|The logical **or** of two expressions.|
|NOT|The logical **not** of an expression.|


#### Fixed Timestamp
Fixed timestamps represent an instant in time with millisecond precision.  These timestamps MUST have timezone offset information
associated with them, and will be stored in UTC.  Since, these have time offsets, the time represents a specific time for a specific
place.  For example, if a time is stored as 3:00pm on April 8th in Washington D.C..  That same time will show up as noon on April 8th in Seattle.

#### Floating Timestamp
Floating timestamps represent a logical day and time without reference to any particular time zone.  No matter where they are viewed, they will be seen as being the same time.
For example, if a time is stored as 3:00pm on April 8th in Washington D.C..  That same time will show up as 3:00pm on April 8th in Seattle.

#### Location
Location is a type that contains latitude, longitude and address. Location is accessed as an Array with 3 elements in it.  These elements are:

* The latitude of the location.  This MAY be null, if the object has been recently uploaded.  This MUST be decimal degress, e.g. 41.8657007325722
* The longitude of the location.  This MAY be null, if the object has been recently uploaded.  This MUST be in decimal degrees, e.g. -87.76110202195098
* A JSON object containing the U.S. address.  This MAY be null.  This has the following members:
    * address -- The street address of the location.
    * city -- The city this address is in
    * state -- The state this address is in
    * zip -- The zip code for this address

|Operator|Description|
|---|---|
|within_box|Returns true if the location is within the specified box|


#### Checkboxes
Checkbox is a non-intuitive datatype.  The reason is that it is really a datatype built for front-end web uis.  As a result, it treats
null and false the same.  This can be a little non-intuitive when creating queries against a Checkbox type.  The UI will treat `null` and
false as the same values.  This means that for:

1.  Datasets imported with `false` or `null` values, the imported value will be honored.
2.  If using the Socrata UI for setting checkbox values, `null` will be used for false.

This means that if you are using the Socrata UI for importing data, a query to check for false, should check for null.  E.g.

[https://sandbox.demo.socrata.com/resource/6cpn-3h7n.json?$where=confirmed=false+OR+confirmed+IS+NULL](https://sandbox.demo.socrata.com/resource/6cpn-3h7n.json?$where=confirmed=false+OR+confirmed+IS+NULL)


## Conversion Functions

In SODA2, there is no automatic type conversion.  Instead, all type conversion has to be done explicitly through conversion functions.  The
following are all the conversion functions:

|Function|Description|
|---|---|
|to_string(\*)|Any type will be convertable to a string.|
|to_number(String)|This will support the same format as a Java BigDecimal, and will return a numeric|
|to_number(Money)|This will convert Money to a Number.  This is a completely loss-less conversion.|
|to_number(Double)|This will convert a double to a number.  This is a completely loss-less conversion.|
|to_location(lat, long)|An latitude and longitude can be converted ot a location.|
|to_boolean(String)|The case-insensitive strings:<br/> **true, 1** -- will yield true.<br/> **false, 0** -- will yield false All else will yield an exception.|
|to_fixed_timestamp(String)|Casts to a fixed timestamp. A fixed timestamp will have offset from UTC information. Accepted Timestamps will be extended ISO-8601 strings and have a “Z” appended. If no timezone is set, then the DataSet default will be used. Calling this on anything that is not of the format 'yyyy-mm-ddThh:mm:ssZ', 'yyyy-mm-ddThh:mm:ss+offs', or 'yyyy-mm-ddThh:mm:ss-offs' is undefined behaviour.|
|to_floating_timestamp(String)|Casts to a floating timestamp.  This timestamp won’t contain any offset information to UTC. Calling this on anything that is not of the format 'yyyy-mm-ddThh:mm:ss' is undefined behaviour.|


## Trying out examples

If you want to play around with examples of all our datatypes, look at the **dataTypeTest** dataset in the sandbox domain.

*  [https://sandbox.demo.socrata.com/resource/dataTypeTest.json](https://sandbox.demo.socrata.com/resource/dataTypeTest.json)
*  [https://sandbox.demo.socrata.com/resource/dataTypeTest.xml](https://sandbox.demo.socrata.com/resource/dataTypeTest.xml)
*  [https://sandbox.demo.socrata.com/resource/dataTypeTest.csv](https://sandbox.demo.socrata.com/resource/dataTypeTest.csv)
*  [https://sandbox.demo.socrata.com/resource/dataTypeTest.rdf](https://sandbox.demo.socrata.com/resource/dataTypeTest.rdf)

