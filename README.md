# Luxon Business Days

![npm](https://img.shields.io/npm/v/luxon-business-days)
[![CircleCI](https://circleci.com/gh/amaidah/luxon-business-days/tree/master.svg?style=svg)](https://circleci.com/gh/amaidah/luxon-business-days/tree/master)
[![codecov](https://codecov.io/gh/amaidah/luxon-business-days/branch/master/graph/badge.svg)](https://codecov.io/gh/amaidah/luxon-business-days)
![luxon-business-days](https://badgen.net/bundlephobia/minzip/luxon-business-days)

Luxon Business Days is a [Luxon](https://github.com/moment/luxon) plugin for calculating and manipulating business days.

Inspired by [moment-business-days](https://github.com/kalmecak/moment-business-days).

## Features

- Add business days to a standard luxon `DateTime` instance. For instance, to calculate the arrival date of a shipment.
- Customizable and extendable. Configure the business working days and holidays of your business.
- Uses holiday "matcher functions" avoiding the need to manually maintain and update holiday date configs every year.

## Install

Already using luxon:

```bash
yarn add luxon-business-days
```

```diff
- import { DateTime } from 'luxon';
+ import { DateTime } from 'luxon-business-days';
// Use DateTime as normal
```

Not already using luxon:

```bash
yarn add luxon luxon-business-days
```

```javascript
import { DateTime } from 'luxon-business-days';
// Use DateTime as normal
```

## Config

Default business days:

- Monday
- Tuesday
- Wednesday
- Thursday
- Friday

Default holidays (aka shipping holidays via UPS):

- New Year's Day
- Easter Day
- Memorial Day
- Independance Day
- Labor Day
- Thanksgiving Day
- Christmas Day

#### Configure business days

```javascript
import { DateTime } from 'luxon-business-days';

const dt = DateTime.local();

// [Mon, Thur, Fri, Sun]
const awesomeFourDayBusinessWeek = [1, 4, 5, 7];

dt.setupBusiness({ businessDays: awesomeFourDayBusinessWeek });
```

#### Configure holidays

Pick from available holiday matchers:

```javascript
import { DateTime } from 'luxon-business-days';

const dt = DateTime.local();
const { availableHolidayMatchers } = dt;
const myCompanyIsNoFun = [
  availableHolidayMatchers.isNewYearsDay,
  availableHolidayMatchers.isChristmasDay,
];

dt.setupBusiness({ holidayMatchers: myCompanyIsNoFun });
```

No holidays:

```javascript
import { DateTime } from 'luxon-business-days';

const dt = DateTime.local();

dt.setupBusiness({ holidayMatchers: [] });
// Congrats, you will successfuly get everyone to quit
```

Custom holiday matchers:

A holiday matcher is simply a function that takes in a `DateTime` instance and returns a boolean. This allows you to algorithmically determine what is considered a holiday without requiring a hardcoded list of dates that are updated and maintained annually.

It is easy to write a basic matcher:

```javascript
import { DateTime } from 'luxon-business-days';

/**
 * @param {DateTime} - An instance of DateTime.
 * @returns {boolean}
 */
const isCelebratingKobe = function(inst) {
  // Celebrate the day Kobe died
  const kobeRIP = DateTime.fromObject({ month: 1, day: 26 });

  // Celebrate Kobe Day
  const kobeDay = DateTime.fromObject({ month: 8, day: 24 });

  // Matches the following two days regardless of year
  return +inst === +kobeRIP || +inst === +kobeDay;
};

const dt = DateTime.local();
const myHolidays = [
  dt.availableHolidayMatchers.isNewYearsDay,
  dt.availableHolidayMatchers.isChristmasDay,
  isCelebratingKobe,
];

dt.setupBusiness({ holidayMatchers: myHolidays });
// Congrats, now your business will consider New Years, 
// Christmas, and the two Kobe days as holidays.
```

Tip: When writing custom holiday matchers, it is probably better to avoid harcoding years or dates and instead programatically generating holidays. Take a look at the provided [matchers](https://github.com/amaidah/luxon-business-days/blob/master/src/holidays.js) for ideas.

## Usage

Basic:

```javascript
import { DateTime } from 'luxon-business-days';

// Day before July 4
let dt = DateTime.local(2019, 7, 3);

dt = dt.plusBusiness(); // 7/5/19 - Friday
dt = dt.plusBusiness({ days: 2 }); // 7/9/19 - Tuesday (Skipped through Saturday/Sunday)

// Now do what you normally would with a DateTime instance.
```

## Examples

* [Display a range of delivery dates for a shipment](https://codesandbox.io/s/luxon-business-days-range-example-tmb1d).

## API

## Members

<dl>
<dt><a href="#availableHolidayMatchers">availableHolidayMatchers</a> : <code>Object</code></dt>
<dd><p>All built-in holiday matchers.</p>
</dd>
</dl>

## Functions

<dl>
<dt><a href="#setupBusiness">setupBusiness([businessDays], [holidayMatchers])</a> ⇐ <code>DateTime</code></dt>
<dd><p>Sets up business days and holiday matchers globally for all DateTime instances.</p>
</dd>
<dt><a href="#clearBusinessSetup">clearBusinessSetup()</a> ⇐ <code>DateTime</code></dt>
<dd><p>Clears business setup globally from all DateTime instances.</p>
</dd>
<dt><a href="#isHoliday">isHoliday()</a> ⇒ <code>boolean</code></dt>
<dd><p>Checks if DateTime instance is a holiday by checking against all holiday matchers.</p>
</dd>
<dt><a href="#isBusinessDay">isBusinessDay()</a> ⇒ <code>boolean</code></dt>
<dd><p>Checks if DateTime instance is a business day.</p>
</dd>
<dt><a href="#plusBusiness">plusBusiness([days])</a> ⇒ <code>DateTime</code></dt>
<dd><p>Adds business days to an existing DateTime instance.</p>
</dd>
</dl>

<a name="availableHolidayMatchers"></a>

## availableHolidayMatchers : <code>Object</code>
All built-in holiday matchers.

**Kind**: global variable  
**Extends**: <code>DateTime</code>  
**Properties**

| Name | Type | Description |
| --- | --- | --- |
| isNewYearsDay | <code>function</code> | A provided holiday matcher. |
| isMLKDay | <code>function</code> | A provided holiday matcher. |
| isEasterDay | <code>function</code> | A provided holiday matcher. |
| isMemorialDay | <code>function</code> | A provided holiday matcher. |
| isIndependanceDay | <code>function</code> | A provided holiday matcher. |
| isLaborDay | <code>function</code> | A provided holiday matcher. |
| isColumbusDay | <code>function</code> | A provided holiday matcher. |
| isThanksgivingDay | <code>function</code> | A provided holiday matcher. |
| isChristmasDay | <code>function</code> | A provided holiday matcher. |

<a name="setupBusiness"></a>

## setupBusiness([businessDays], [holidayMatchers]) ⇐ <code>DateTime</code>
Sets up business days and holiday matchers globally for all DateTime instances.

**Kind**: global function  
**Extends**: <code>DateTime</code>  

| Param | Type | Default | Description |
| --- | --- | --- | --- |
| [businessDays] | <code>Array.&lt;number&gt;</code> | <code>DEFAULT_BUSINESS_DAYS</code> | The working business days for the business. |
| [holidayMatchers] | <code>Array.&lt;function()&gt;</code> | <code>DEFAULT_HOLIDAY_MATCHERS</code> | The holiday matchers used to check if a particular day is a holiday for the business. |

<a name="clearBusinessSetup"></a>

## clearBusinessSetup() ⇐ <code>DateTime</code>
Clears business setup globally from all DateTime instances.

**Kind**: global function  
**Extends**: <code>DateTime</code>  
<a name="isHoliday"></a>

## isHoliday() ⇒ <code>boolean</code>
Checks if DateTime instance is a holiday by checking against all holiday matchers.

**Kind**: global function  
**Extends**: <code>DateTime</code>  
<a name="isBusinessDay"></a>

## isBusinessDay() ⇒ <code>boolean</code>
Checks if DateTime instance is a business day.

**Kind**: global function  
**Extends**: <code>DateTime</code>  
<a name="plusBusiness"></a>

## plusBusiness([days]) ⇒ <code>DateTime</code>
Adds business days to an existing DateTime instance.

**Kind**: global function  
**Extends**: <code>DateTime</code>  

| Param | Type | Default | Description |
| --- | --- | --- | --- |
| [days] | <code>number</code> | <code>1</code> | The number of business days to add. |

