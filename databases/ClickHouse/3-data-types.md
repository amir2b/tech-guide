# ClickHouse - Data Types

## Int | UInt Types

```text
Int8   [-128 : 127]
Int16  [-32768 : 32767]
Int32  [-2147483648 : 2147483647]
Int64  [-9223372036854775808 : 9223372036854775807]
Int128 [-170141183460469231731687303715884105728 : 170141183460469231731687303715884105727]
Int256 [-57896044618658097711785492504343953926634992332820282019728792003956564819968 : 57896044618658097711785492504343953926634992332820282019728792003956564819967]

UInt8   [0 : 255]
UInt16  [0 : 65535]
UInt32  [0 : 4294967295]
UInt64  [0 : 18446744073709551615]
UInt128 [0 : 340282366920938463463374607431768211455]
UInt256 [0 : 115792089237316195423570985008687907853269984665640564039457584007913129639935]
```

## Float32 | Float64 | BFloat16 Types

```text
Float32 — float.
Float64 — double.
BFloat16 — is a 16-bit floating point data type with 8-bit exponent, sign, and 7-bit mantissa. It is useful for machine learning and AI applications.
```

### Using floating-point numbers

```sql
-- Infinity (inf)
SELECT 0.5 / 0;

-- Negative infinity (-inf)
SELECT -0.5 / 0;

-- Not a number (nan)
SELECT 0 / 0;

-- Computations with floating-point numbers might produce a rounding error:
SELECT 1 - 0.9;
```

## Decimal | Decimal32 | Decimal64 | Decimal128 | Decimal256 Types

Signed fixed-point numbers that keep precision during add, subtract and multiply operations. For division least significant digits are discarded (not rounded).

```text
Decimal       - the syntax Decimal is equivalent to Decimal(10, 0)
Decimal(P)    - is equivalent to Decimal(P, 0)
Decimal(P, S) - ( -1 * 10^(P - S), 1 * 10^(P - S) )
Decimal32(S)  - ( -1 * 10^(9 - S), 1 * 10^(9 - S) )
Decimal64(S)  - ( -1 * 10^(18 - S), 1 * 10^(18 - S) )
Decimal128(S) - ( -1 * 10^(38 - S), 1 * 10^(38 - S) )
Decimal256(S) - ( -1 * 10^(76 - S), 1 * 10^(76 - S) )
```

Parameters:

- P - precision. Valid range: [ 1 : 76 ]. Determines how many decimal digits number can have (including fraction). By default, the precision is 10.
- S - scale. Valid range: [ 0 : P ]. Determines how many decimal digits fraction can have.

For example, Decimal32(4) can contain numbers from -99999.9999 to 99999.9999 with 0.0001 step.

```sql
SELECT 1 - toDecimal32(0.9, 1)
```

## String | FixedString(N) Types

```text
String         - Strings of an arbitrary length. The length is not limited.
FixedString(N) - A fixed-length string of N bytes. Where N is a natural number.
```

## Date | Date32 Types

```text
Date   - [1970-01-01, 2149-06-06]
Date32 - [1900-01-01, 2299-12-31]
```

## Time | Time64 Types

```text
Time              - [-999:59:59, 999:59:59]
Time64(precision) - [-999:59:59, 999:59:59.999999999]
```

Tick size (precision): 10-precision seconds. Valid range: 0..9. Common choices are 3 (milliseconds), 6 (microseconds), and 9 (nanoseconds).

## DateTime

```text
DateTime([timezone])              - [1970-01-01 00:00:00, 2106-02-07 06:28:15]
DateTime64(precision, [timezone]) - [1900-01-01 00:00:00, 2299-12-31 23:59:59.999999999]
```

DateTime('UTC'), DateTime('Asia/Tehran')

Tick size (precision): 10-precision seconds. Valid range: [ 0 : 9 ]. Typically, are used - 3 (milliseconds), 6 (microseconds), 9 (nanoseconds).

## Bool

Type bool is internally stored as UInt8. Possible values are true (1), false (0).

## Other Types

- Enum      - Enumerated type consisting of named values.
- UUID      - A Universally Unique Identifier (UUID) is a 16-byte value used to identify records.
- IPv4      - IPv4 addresses. Stored in 4 bytes as UInt32.
- IPv6      - IPv6 addresses. Stored in 16 bytes as UInt128 big-endian.
- Array(T)  - An array of T-type items, with the starting array index as 1. T can be any data type, including an array.
- Tuple(T1, T2, ...) - A tuple of elements, each having an individual type.
- Map(K, V) - Data type Map(K, V) stores key-value pairs.
- Variant(T1, T2, ...) - This type represents a union of other data types.
- LowCardinality(T) - Changes the internal representation of other data types to be dictionary-encoded.
- Nullable(T) - Allows to store special marker (NULL) that denotes "missing value" alongside normal values allowed by T.
- ...

### Sample

```sql
CREATE TABLE test (
    `id` UInt16,
    StartDate Date,
    `B` Bool,
    v Variant(UInt64, String, Array(UInt64)),
    `strings` LowCardinality(String),
    `n` Nullable(UInt32)
)
ENGINE = MergeTree()
ORDER BY id;
```

## References

- <https://clickhouse.com/docs/sql-reference/data-types>
