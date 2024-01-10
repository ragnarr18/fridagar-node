# Frídagar

Small, fast JavaScript/TypeScript library for looking up Icelandic public 
holidays, and resolving business days before/after a given day. It also 
provides info about other commonly observed "special" days such as 
"Bolludagur", etc.

"Half day" holidays (such as Christmas and New Year's Eve) are marked as
such, and can be optionally treated as either "work days" or "non-work days",
depending on need.

All day names/descriptions are in Icelandic, but each day has a stable `key`
prop that can be used when translating its name into other languages.

All returned dates are in the UTC timezone and set to midnight.

```
npm install fridagar
```

<!-- prettier-ignore-start -->

- [Methods](#methods)
  - [`getHolidays`](#getholidays)
  - [`getAllDays`](#getalldays)
  - [`getOtherDays`](#getotherdays)
  - [`isHoliday`](#isholiday)
  - [`isSpecialDay`](#isspecialday)
  - [`workdaysFromDate`](#workdaysfromdate)
- [Exported types](#exported-types)
  - [type `Holiday`](#type-holiday)
  - [type `SpecialDay`](#type-specialday)
  - [type `HolidayKey, SpecialDayKey`](#type-holidaykey-specialdaykey)
- [Contributing](#contributing)
- [Change Log](#change-log)

<!-- prettier-ignore-end -->

---

## Methods

---

### `getHolidays`

**Syntax:** `getHolidays(year?: number, month?: number): Array<Holiday>`

Returns all Icelandic public holidays and commonly celebrated "special" days
for a given year — optionally narrowed down to a single month.

```ts
import { getHolidays } from "fridagar";

const holidays2018 = getHolidays(2018);
const holidaysInDecember2018 = getHolidays(2018, 12);

// If year is omitted the current year is used
const holidaysThisYear = getHolidays();
const holidaysInDecemberThisYear = getHolidays(undefined, 12);
```

---

### `getAllDays`

**Syntax:**
`getAlldays(year?: number, month?: number): Array<Holiday | SpecialDay>`

Returns all official Icelandic public holidays (non-working days) for a
given year — optionally narrowed down to a single month.

```ts
import { getAllDays } from "fridagar";

const allDays2018 = getAllDays(2018);
const allDaysInDecember2018 = getAllDays(2018, 12);

// If year is omitted the current year is used
const allDaysThisYear = getAllDays();
const allDaysInDecemberThisYear = getAllDays(undefined, 12);
```

---

### `getOtherDays`

**Syntax:** `getOtherDays(year?: number, month?: number): Array<SpecialDay>`

Returns only unofficial, commonly celebrated "special days" (that are still
workdays) for a given year — optionally narrowed down to a single month.

```ts
import { getOtherDays } from "fridagar";

const otherdays2018 = getOtherDays(2018);
const otherdaysInDecember2018 = getOtherDays(2018, 12);

// If year is omitted the current year is used
const otherdaysThisYear = getOtherDays();
const otherdaysInDecemberThisYear = getOtherDays(undefined, 12);
```

---

### `isHoliday`

**Syntax:** `isHoliday(date: Date): Holiday | undefined`

Checks if a given date is an Icelandic public holiday, and if so, returns
its info object.

```ts
import { isHoliday } from "fridagar";

const res1 = isHoliday(new Date("2018-12-24"));
console.log(res1);
// Logs the `Holiday` object for Aðfangadagur

const res2 = isHoliday(new Date("2018-12-23"));
console.log(res2);
// Logs `undefined` (as Þorláksmessa is not a holiday.)
```

---

### `isSpecialDay`

**Syntax:** `isSpecialDay(date: Date): Holiday | SpecialDay | undefined`

Checks if a given date is either an Icelandic public holiday or a commonly
celebrated "special" day, and if so, returns its info object.

```ts
import { isSpecialDay } from "fridagar";

const res1 = isSpecialDay(new Date("2018-12-24"));
console.log(res1);
// Logs the `Holiday` object for Aðfangadagur

const res2 = isSpecialDay(new Date("2018-12-23"));
console.log(res2);
// Logs the `SpecialDay` object for Þorláksmessa

const res3 = isSpecialDay(new Date("2018-12-19"));
console.log(res3);
// Logs `undefined`  (Because Dec. 19th is just a normal day.)
```

---

### `workdaysFromDate`

**Syntax:**
`workdaysFromDate(days: number, refDate?: Date, includeHalfDays?: boolean): Date`

Returns the `days`-th business-day before/after the reference date.

Defaults to counting half-day holidays as "non-work" days.

```ts
import { workdaysFromDate } from "fridagar";

const dec23th2018 = new Date("2018-12-23"); // Thursday
const jan1st2025 = new Date("2024-01-01"); // Tuesday

// Treats Aðfangadagur as a non-work day by default
const secondWorkDay = workdaysFromDate(2, dec23th2018);
// new Date('2021-12-28') // Tuesday

// Optionally treats Aðfangadagur as a work day
const secondWorkDayInclHalfDay = workdaysFromDate(2, dec23th2018, true);
// new Date('2021-12-27') // Monday

// One business days before New Year's day of 2024
const prevDay = workdaysFromDate(-1, jan1st2024);
// new Date('2023-12-29') // Friday
```

If the `refDate` is omitted, the current (today) date is used.

```ts
const thirdWorkDayFromToday = workdaysFromDate(3);
```

NOTE: The returned date is always set to 00:00:00 UTC.

---

## Exported types

---

### type `Holiday`

An object describing an Icelandic public holiday.

```ts
import type { Holiday } from "fridagar";

// Example
const xmasEve2017: Holiday = {
  date: new Date("2017-12-24T00:00:00.000Z"),
  description: "Aðfangadagur",
  key: "adfanga", // stable identifier for this holiday
  holiday: true,
  halfDay: true,
};
```

NOTE: All dates are set to 00:00:00 UTC

---

### type `SpecialDay`

An object describing an Icelandic commonly celebrated "special" day, such as
"Bolludagur", etc.

```ts
import type { SpecialDay } from "fridagar";

// Example
const sovereignDay2017: SpecialDay = {
  date: new Date("2017-12-01T00:00:00.000Z"),
  description: "Fullveldisdagurinn",
  key: "fullv", // stable identifier for this special day
  holiday: false, // not a public holiday
};
```

NOTE: All dates are set to 00:00:00 UTC

---

### type `HolidayKey, SpecialDayKey`

String union types of all possible `key` values for `Holiday` and
`SpecialDay` objects. This is mainly useful when translating the day
names/descriptions into other languages.

```ts
import type { HolidayKey, SpecialDayKey } from "fridagar";

const dayNamesPolish: Record<HolidayKey | SpecialDayKey, string> = {
  nyars: "Nowy Rok",
  adfanga: "Wigilia",
  jola: "Boże Narodzenie",
  // Etc...
};
```

---

## Contributing

This project uses the [Bun runtime](https://bun.sh) for development (tests,
build, etc.)

PRs are welcoms!

---

## Change Log

See [CHANGELOG.md](https://github.com/gaui/fridagar-node/blob/dev/CHANGELOG.md)
