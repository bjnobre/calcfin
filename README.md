# calc-fin

Financial functions for Calc 2.15.1, the C-style arbitrary-precision
calculator. The library adds common time-value-of-money and cash-flow
calculations and uses Calc's exact rational arithmetic where possible.

## Usage

Start an interactive Calc session with the library loaded:

```sh
./calc-fin
```

The existing launcher name is retained for compatibility. You can also load
the library from an existing Calc session:

```calc
read "/home/wrench/git/calc-fin/finance.cal";
```

Every function accepts an optional final `precision` argument and defaults to
2 decimal places. Intermediate calculations use at least 30 decimal places of
accuracy when approximation is necessary. Rates are decimals per period, so
5% is `.05`. For `pv`, `fv`, and `pmt`, use `type = 0` for end-of-period
payments or `type = 1` for beginning-of-period payments. Money paid and
received must use opposite signs.

```calc
/* Future value of $1,000 invested for 10 years at 5%. */
fv(.05, 10, 0, -1000, 0)

/* The same result with 6 decimal places. */
fv(.05, 10, 0, -1000, 0, 6)

/* Monthly payment on a $300,000, 30-year loan at 6% annually. */
pmt(.06 / 12, 30 * 12, 300000, 0, 0)
```

Calc evaluates `.06 / 12` exactly, so global display precision does not affect
arguments or intermediate rational calculations.

## Functions

```text
fv(rate, nper, payment, present, type [, precision])
pv(rate, nper, payment, future, type [, precision])
pmt(rate, nper, present, future, type [, precision])
nper(rate, payment, present, future, type [, precision])
effective_rate(nominal, periods [, precision])
nominal_rate(effective, periods [, precision])
npv(rate, cashflows, count [, precision])
irr(cashflows, count, guess [, precision])
mirr(cashflows, count, finance_rate, reinvest_rate [, precision])
```

Cash-flow matrices start at time zero:

```calc
mat c[] = {-1000, 400, 400, 400};

npv(.10, c, 4)
irr(c, 4, .10, 6)
```

`irr` uses Newton iteration. When cash flows change sign more than once, more
than one valid IRR can exist and the supplied guess can select a different
solution. Financial inputs must be within their normal mathematical domains;
for example, rates must be greater than -1 and `mirr` needs both positive and
negative cash flows.

## Test

```sh
make test
```
