# Time format ticks

Given a list of timestamps, it recognizes the time interval necessary to represent the dates (_15 seconds_, _minute_, _30 minutes_, _hourly_, _daily_, _weekly_, _monthly_, _quarterly_ or _yearly_) and formats the ticks accordingly.

Two possible formats (_primary_ and _secondary_) are associated with each time interval which can be used to format the current tick.
_Primary_ format was designed to represent the value (the date corresponding to the tick) in a more exhaustive and detailed way, while the _secondary_ format represents the value in a concise way.\
_Primary_ tick format is used for the first tick and when something happens between the last and the current ticks based on the time interval.

For example if time interval is _daily_, I'm probably interested to see a primary tick on the first tick and every time a ticks rapresents a date which month or year is changed respet to the previous tick.
The remaining ticks are formatted using the _secondary_ format.

So I can describe this logic using this schema:

    daily:
      primary: "MMM d"
      secondary: "d"

      primary:
        * first tick
        * change month
        * change year
      secondary:
        * otherwise

What does it mean?\
Exactly the same described above: if time interval is _daily_, the primary format tick is `MMM d`, the secondary format tick is `d` so the primary tick actually describes the date more precisely because it shows the month and day, while the secondary format tick shows only the day.\
The primary format tick is used on the first tick and when there is a change of month or year, in all the other cases it's being used the secondary format tick.

Below there is a short diagram on how ticks are formatted:

    15seconds:
      primary: "MMM d, pp"
      secondary: "pp"

      primary:
        * first tick
        * change day of month
        * change month
        * change year
      secondary:
        * otherwise

    minute:
      primary: "MMM d, p"
      secondary: "p"

      primary:
        * first tick
        * change day of month
        * change month
        * change year
      secondary:
        * otherwise

    30minutes:
      primary: "MMM d, p"
      secondary: "p"

      primary:
        * first tick
        * change day of month
        * change month
        * change year
      secondary:
        * otherwise

    hourly:
      primary: "MMM d, hh a"
      secondary: "hh a"

      primary:
        * first tick
        * change day of month
        * change month
        * change year
      secondary:
        * otherwise

    daily:
      primary: "MMM d"
      secondary: "d"

      primary:
        * first tick
        * change month
        * change year
      secondary:
        * otherwise

    weekly:
      primary: "eee, MMM d"
      secondary: "eee"

      primary:
        * first tick
        * change week
        * change year
      secondary:
        * otherwise

    monthly:
      primary: "MMM yyyy"
      secondary: "MMM"

      primary:
        * first tick
        * change year
      secondary:
        * otherwise

    quarterly:
      primary: "QQQ ''yy"
      secondary: "QQQ"

      primary:
        * first tick
        * first quarter
      secondary:
        * otherwise

    yearly:
      primary: "yyyy"
      secondary: "yyyy"

      primary:
        * never
      secondary:
        * otherwise

**Note**: as you can see in the above diagram, the _weekly_ time interval doesn't exist. It works as _daily_ but it shows the day names instead of the day numbers and user can choose the format using the `showDayName` option.

**Note**: if the dataset has only one datum, we show the tick in the most detailed way possible using _15 seconds_ time interval.

Where:

- `MMM`: short name of the month (ie. _Jan-Dec_)
- `d`: day of the month (ie. _1-31_)
- `pp`: long localized time (ie. _12:00:00 AM_)
- `p`: long localized time (ie. _12:00 AM_)
- `hh`: hour [1-12] (ie. _01, 02, ..., 11, 12_)
- `a`: _AM_, _PM_
- `eee`: local day of week (ie. _Mon, Tue, Wed, ..., Su_)
- `yyyy`: calendar year (ie. _2010, 2017_)
- `yy`: calendar year (ie. _10, 17_)
- `QQQ`: quarter (ie. _Q1, ..., Q4_).

`primary` and `secondary` specify the format of the formatted tick. The format of this string is based on [Unicode Technical Standard #35](https://www.unicode.org/reports/tr35/tr35-dates.html#Date_Field_Symbol_Table).\
More info on the [`date-fns` documentation](https://date-fns.org/v2.8.1/docs/format).

## Customization

You can customize both the primary and the secondary format. You can also, as said before, chose to show the day as number or as day name. Also, you can change the Locale.
All of those things are possible using the `Options` object.

### Change the formats

If you prefer to show hours using 24h and not 12h as default, you can descbribe the formats in this way:

    const options = {
      formats: {
        'hourly': { primary: 'MMM d, HH:mm', secondary: 'HH:mm' },
      }
    }

If you prefer to show years as 2-digit instead of 4 for _yearly_ time interval:

    const options = {
      formats: {
        'yearly': { primary: 'yy', secondary: 'yy' },
      }
    }

An infinte number of combinations are possible, the important thing is to use the [Unicode Technical Standard #35](https://www.unicode.org/reports/tr35/tr35-dates.html#Date_Field_Symbol_Table).

### Choose how to show days

If you prefer to show day as day name instead of day number, you can set the `showDayName` option to `true` and `[1, 2, ..., 31]` become `[Monday, Tuesday, ..., Sunday]`.

### Change Locale

Default locale is _english-US_ but the user can choose the one he prefers. The available locale objects are those supported by `date-fns`.\
[Here](https://github.com/date-fns/date-fns/tree/master/src/locale) is the list.

Basically by changing the locale only the language of the labels change.
To change the formats of the labels according to a locale the only possibility is to add a new `formats`. This is an open problem.

Each option value is optional.

## Screenshots

#### 15seconds

    const ticks = [
      new Date(2020, 11, 10, 23, 59, 15),
      new Date(2020, 11, 10, 23, 59, 30),
      new Date(2020, 11, 10, 23, 59, 45),
      new Date(2020, 11, 11, 0, 0, 0),
      new Date(2020, 11, 11, 0, 0, 15),
      new Date(2020, 11, 11, 0, 0, 30),
      new Date(2020, 11, 11, 0, 0, 45)
    ].map(d => d.getTime())
    const formatter = getFormatter(ticks)

![15seconds]()

#### minute

    const ticks = [
      new Date(2020, 4, 21, 23, 47, 0),
      new Date(2020, 4, 21, 23, 58, 0),
      new Date(2020, 4, 21, 23, 59, 0),
      new Date(2020, 4, 22, 0, 0, 0),
      new Date(2020, 4, 22, 0, 1, 0),
      new Date(2020, 4, 22, 0, 2, 0),
      new Date(2020, 4, 22, 0, 3, 0),
      new Date(2020, 4, 22, 11, 58, 0),
      new Date(2020, 4, 22, 11, 59, 0),
      new Date(2020, 4, 22, 12, 0, 0),
      new Date(2020, 4, 22, 12, 1, 0, 0),
      new Date(2020, 4, 22, 12, 2, 0)
    ].map(d => d.getTime())
    const formatter = getFormatter(ticks)

![minute]()

#### 30minutes

    const ticks = [
      new Date(2020, 11, 10, 22, 30),
      new Date(2020, 11, 10, 23, 0),
      new Date(2020, 11, 10, 23, 30),
      new Date(2020, 11, 11, 0, 0),
      new Date(2020, 11, 11, 0, 30),
      new Date(2020, 11, 11, 1, 0),
      new Date(2020, 11, 11, 1, 30)
    ].map(d => d.getTime())
    const formatter = getFormatter(ticks)

![30minutes]()

#### hourly

    const ticks = [
      new Date(2020, 11, 10, 22, 0),
      new Date(2020, 11, 10, 23, 0),
      new Date(2020, 11, 11, 0, 0),
      new Date(2020, 11, 11, 1, 0),
      new Date(2020, 11, 11, 2, 0),
      new Date(2020, 11, 11, 3, 0),
      new Date(2020, 11, 11, 4, 0),
      new Date(2020, 11, 11, 11, 0),
      new Date(2020, 11, 11, 12, 0),
      new Date(2020, 11, 11, 13, 0),
      new Date(2020, 11, 11, 14, 0),
      new Date(2020, 11, 11, 15, 0)
    ].map(d => d.getTime())
    const formatter = getFormatter(ticks)

![hourly]()

#### hourly with custom format

    const ticks = [
      new Date(2020, 11, 10, 22, 0),
      new Date(2020, 11, 10, 23, 0),
      new Date(2020, 11, 11, 0, 0),
      new Date(2020, 11, 11, 1, 0),
      new Date(2020, 11, 11, 2, 0),
      new Date(2020, 11, 11, 3, 0),
      new Date(2020, 11, 11, 4, 0),
      new Date(2020, 11, 11, 11, 0),
      new Date(2020, 11, 11, 12, 0),
      new Date(2020, 11, 11, 13, 0),
      new Date(2020, 11, 11, 14, 0),
      new Date(2020, 11, 11, 15, 0)
    ].map(d => d.getTime())
    const options = { formats: {'hourly': { primary: 'MMM d, HH:mm', secondary: 'HH:mm' } }
    const formatter = getFormatter(ticks, options)

![hourly custom]()

#### daily

    const ticks = [
      new Date(2019, 11, 30),
      new Date(2019, 11, 31),
      new Date(2020, 0, 1),
      new Date(2020, 0, 2),
      new Date(2020, 0, 3),
      new Date(2020, 0, 4),
      new Date(2020, 0, 5),
      new Date(2020, 0, 30),
      new Date(2020, 0, 31),
      new Date(2020, 1, 1),
      new Date(2020, 1, 2),
      new Date(2020, 1, 3)
    ].map(d => d.getTime())
    const formatter = getFormatter(ticks)

![daily]()

#### weekly

    const ticks = [
      new Date(2020, 0, 27),
      new Date(2020, 0, 28),
      new Date(2020, 0, 29),
      new Date(2020, 0, 30),
      new Date(2020, 0, 31),
      new Date(2020, 1, 1),
      new Date(2020, 1, 2),
      new Date(2020, 1, 3),
      new Date(2020, 1, 4),
      new Date(2020, 1, 5),
      new Date(2020, 1, 6),
      new Date(2020, 1, 7)
    ].map(d => d.getTime())
    const formatter = getFormatter(ticks)

![weekly]()

#### monthly

    const ticks = [
      new Date(2018, 9),
      new Date(2018, 10),
      new Date(2018, 11),
      new Date(2019, 0),
      new Date(2019, 1),
      new Date(2019, 2),
      new Date(2019, 3),
      new Date(2019, 10),
      new Date(2019, 11),
      new Date(2020, 0),
      new Date(2020, 1),
      new Date(2020, 2)
    ].map(d => d.getTime())
    const formatter = getFormatter(ticks)

![monthly]()

#### monthly with custom Locale

    const ticks = [
      new Date(2018, 9),
      new Date(2018, 10),
      new Date(2018, 11),
      new Date(2019, 0),
      new Date(2019, 1),
      new Date(2019, 2),
      new Date(2019, 3),
      new Date(2019, 10),
      new Date(2019, 11),
      new Date(2020, 0),
      new Date(2020, 1),
      new Date(2020, 2)
    ].map(d => d.getTime())
    const options = { localeObject: frLocaleObject }
    const formatter = getFormatter(ticks, options)

![monthly custom]()

#### quarterly

    const ticks = [
      new Date(2017, 0),
      new Date(2017, 3),
      new Date(2017, 6),
      new Date(2017, 9),
      new Date(2018, 1),
      new Date(2018, 4),
      new Date(2018, 7),
      new Date(2019, 0),
      new Date(2019, 5),
      new Date(2019, 7),
      new Date(2019, 10),
      new Date(2020, 0)
    ].map(d => d.getTime())
    const formatter = getFormatter(ticks)

![quarterly]()

#### yearly

    const ticks = [
      new Date(1977, 0),
      new Date(1978, 0),
      new Date(1979, 0),
      new Date(1980, 0),
      new Date(1981, 0),
      new Date(1982, 0),
      new Date(1983, 0),
      new Date(2015, 0),
      new Date(2016, 0),
      new Date(2017, 0),
      new Date(2018, 0),
      new Date(2019, 0)
    ].map(d => d.getTime())
    const formatter = getFormatter(ticks)

![yearly]()

## Demo page

A demo page is avaible, you can run it using `yarn start:demo`.

## API

#### `getFormatter(ticks: Tick[], options?: Options): Formatter`

It returns a formatter that you can pass as input to `axis.tickFormat(formatter)`. \
For more information see the [offical d3 documentation](https://github.com/d3/d3-axis#axis_tickFormat).

Params are:

- `ticks`: is an array of timestamps
- `options` (_optional_): is an object with this structure, each attribute is optional:

      {
        showDayName: false,
        localeObject: enUSLocaleObject,
        formats: {
          '15seconds': { primary: 'MMM d, pp', secondary: 'pp' },
          'minute': { primary: 'MMM d, p', secondary: 'p' },
          '30minutes': { primary: 'MMM d, p', secondary: 'p' },
          'hourly': { primary: 'MMM d, hh a', secondary: 'hh a' },
          'daily': { primary: 'MMM d', secondary: 'd' },
          'weekly': { primary: 'eee, MMM d', secondary: 'eee' },
          'monthly': { primary: 'MMM yyyy', secondary: 'MMM' },
          'quarterly': { primary: `QQQ ''yy`, secondary: 'QQQ' },
          'yearly': { primary: 'yyyy', secondary: 'yyyy' },
        }
      }

#### `computeTimeIntervalName(ticks: Tick[]): TimeInterval)`

Given the timestamps array `ticks`, it returns a string representing the time interval necessary to show in the best way your dates.
The possible time interval values ​​are:

- `15seconds`
- `minute`
- `30minutes`
- `hourly`
- `daily`
- `monthly`
- `quarterly`
- `yearly`

#### `isTickPrimary(tick: Tick, tickIndex: number, interval: TimeInterval, showDayName: boolean): boolean`

Returns `true` if the tick is a primary tick, `false` otherwise.\

Params are:

- `tick: Tick`: a single timestamp value
- `tickIndex: number`: the index of the tick
- `interval: TimeInterval`: the time insterval that suits your data
- `showDayName: boolean`: if interval is `daily`, you can set `showDayName` as `true` if you want to show days as string, `false` otherwise. In all the other cases, doesn't matter what the value of this variable is. Probably I'll work on it.

This function is useful if you want, for example, set a different style to primary and secondary ticks:

![15secondsRed](https://raw.githubusercontent.com/ilariaventurini/time-format-ticks/master/assets/15secondsRed.png?token=AKRICQMZFJVQUVEPRAKX47S6P6HJQ)

## Example

    // create an array of timestamps
    const dates = [
      new Date(2020, 11, 10, 23, 59, 15),
      new Date(2020, 11, 10, 23, 59, 30),
      new Date(2020, 11, 10, 23, 59, 45),
      new Date(2020, 11, 11, 0, 0, 0),
      new Date(2020, 11, 11, 0, 0, 15),
      new Date(2020, 11, 11, 0, 0, 30),
      new Date(2020, 11, 11, 0, 0, 45)]

    // transform dates to timestamps
    const ticks = dates.map(d => d.getTime())

    // set width and height of your axis
    const width = 800
    const height = 100
    const margins = { top: 10, right: 40, bottom: 0, left: 60 }

    // create the time scale
    const domain = extent(dataset)
    const range = [0, width - margins.left - margins.right]
    const scale = scaleTime().domain(domain).range(range)

    // get the formatter
    const formatter = getFormatter(ticks)

    // create the bottom axis generator
    const axisGenerator = axisBottom(scale).tickFormat(formatter)

    // append axis to the DOM
    const holder = select('#app')
    holder
      .append('svg')
      .attr('width', width)
      .attr('height', height)
      .append('g')
      .attr('transform', `translate(${margins.left}, ${margins.top})`)
      .append('g')
      .attr('width', width - margins.left - margins.right)
      .attr('height', height - margins.top - margins.bottom)
      .call(axisGenerator)
      .selectAll('text')
      .attr('transform', 'rotate(-45)')
      .attr('text-anchor', 'end')

The result is:

![15seconds](https://raw.githubusercontent.com/ilariaventurini/time-format-ticks/master/assets/15seconds.png?token=AKRICQPJFFTKFNYTN6VNIE26P52U6)

## License

[MIT](https://github.com/ilariaventurini/time-format-ticks/blob/master/LICENSE) © [Ilaria Venturini](https://github.com/ilariaventurini)

<!-- ### TODO

- [ ] badge test
- [ ] demo page / codesandbox / esempio stile isprimary tick
- [ ] logo
- [x] esempi con titolo e dati come testo non immagini -->
