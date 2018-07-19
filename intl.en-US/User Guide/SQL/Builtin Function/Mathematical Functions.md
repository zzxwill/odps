# Mathematical Functions {#concept_ubn_3kb_5db .concept}

## ABS {#section_i1v_5lm_vdb .section}

**Function definition:**

```
Double abs(Double number)
Bigint abs(Bigint number)
Decimal abs(Decimal number)
```

**Usage:**

Returns an absolute value.

**Parameter description**:

number: When number is of Type Double, Bigint, or Decimal.

-   If the input is Bigint and return Bigint.
-   If the input is Double and return Double. 
-   If the input is Decimal and return Decimal. 

If the input is String, it is converted to Double by implicit conversion. If the input is another type, an error occurs.

**Return value**:

Returns the Double, Bigint or Decimal type, which depends on the type of input parameter.  If the input is null, return null.

**Note:** When the value of input Bigint type is over the maximum value of Bigint, return Double type. In this case, the precision may be lost.

**Examples:**

```
abs(null) = null
abs(-1) = 1
abs(-1.2) = 1.2
abs("-2") = 2.0
abs(122320837456298376592387456923748) = 1.2232083745629837e32
```

The following is a completed ABS function example used in SQL. The use methods of other built-in functions \(except Window Function and Aggregation Function\) are similar.

```
select abs(id) from tbl1;
-- Take the absolute value of the id field in tbl1.
```

## ACOS {#section_cfp_qmm_vdb .section}

**Function definition:**

```
Double acos(Double number)
Decimal acos(Decimal number)
```

**Usage:**

This function is used to calculate the inverse cosine of a specific number.

**Parameter description**:

number: Double or Decima type, -1<=number <=1.  If the input is String or Bigint, it is converted to Double by implicit conversion. If the input is another type, an error occurs.

**Return value**:

Returns the Double or Decimal type, the value is between 0 to π. If number is null, return null.

**For example**: 

```
acos("0.87") = 0.5155940062460905
acos(0) = 1.5707963267948966
```

## ASIN { .section}

**Function definition:**

```
Double asin(Double number)
Decimal asin(Decimal number)
```

**Usage:**

This function is used to calculate the inverse sine function of number.

**Parameter description**:

number: Double or Decima type, -1<=number <=1.  If the input is String or Bigint, it is converted to Double by implicit conversion. If the input is another type, an error occurs.

**Return value**:

Returns the Double or Decimal type, the value is between -π/2 to π/2. If the number is null, return null.

**For example**:

```
asin(1) = 1.5707963267948966
asin(-1) = -1.5707963267948966
```

## ATAN {#section_odw_jnm_vdb .section}

**Function definition:**

```
Double atan(Double number)
```

**Usage:**

This function is used to calculate the back-cut function of number.

**Parameter description**:

Number: Double type, If the input is String or Bigint, it is converted to Double by implicit conversion. If the input is another type, an error occurs.

**Return value**:

Returns the Double type, the value is between -π/2 to π/2. If the number is null, return null.

**For example**: 

```
atan(1) = 0.7853981633974483
atan(-1) = -0.7853981633974483
```

## CEIL {#section_ugm_k4m_vdb .section}

**Function definition:**

```
Bigint ceil(Double value)
Bigint ceil(Decimal value)
```

**Usage:**

This function returns the smallest integral value not less than the argument.

**Parameter description**:

value: Double or Decimal type, If the input is String or Bigint, it is converted to Double by implicit conversion. If the input is another type, an error occurs.  

**Return value**:

Returns the Bigint type. If the number is null, return null.

**For example**:

```
ceil(1.1) = 2
ceil(-1.1) = -1
```

## CONV {#section_tkx_q4m_vdb .section}

**Function definition:**

```
String conv(String input, Bigint from_base, Bigint to_base)
```

**Usage:**

This function is Hexadecimal conversion function.

**Parameter description**:

-   input: an integer to be converted, represented by String. Accept the implicit conversion of Bigint and Double.
-   from\_base, to\_base: Decimal value, the acceptable values can be 2, 8, 10 and 16.  Accept the implicit conversion of String and Double.

**Return value**: 

Return the String type.  If the number is null, return null. The conversion process runs at a 64-bit precision. An exception is thrown when overflow occurs. If the input is a negative value \(begin with ‘-’\), an exception is reported.  If the input value is a decimal, it is converted to an integer before hex conversion. The decimal part is left out.

**For example**:

```
conv('1100', 2, 10) = '12'
conv('1100', 2, 16) = 'c'
conv('ab', 16, 10) = '171'
conv('ab', 16, 16) = 'ab'
```

## COS {#section_tpy_z4m_vdb .section}

**Function definition:**

```
Double cos(Double number)
Decimal cos(Decimal number)
```

**Usage:**

This function is Cosine function, input is the radian value.

**Parameter description**:

number: Double or Decimal type. If the input is String, it is converted to Double by implicit conversion. If the input is another type, an error occurs.

**Return value**:

Returns the Double or Decimal type. If the number is NULL, return NULL.

**For example**: 

```
cos(3.1415926/2)=2.6794896585028633e-8
cos(3.1415926)=-0.9999999999999986
```

## COSH {#section_tnp_gpm_vdb .section}

**Function definition:**

```
Double cosh(Double number)
Decimal cosh(Decimal number)
```

**Usage:**

This function is Hyperbolic cosine function.

**Parameter description**:

number: Double or Decimal type. If the input is String, it is converted to Double by implicit conversion. If the input is another type, an error occurs.

**Return value**:

Returns the Double or Decimal type. If the number is NULL, return NULL.

## COT {#section_hhz_lpm_vdb .section}

**Function definition:**

```
Double cot(Double number)
Decimal cot(Decimal number)
```

**Usage:**

This function is Cotangent function, input is the radian value.

**Parameter description**:

number: Double or Decimal type.  If the input is String or Bigint, it is converted to Double by implicit conversion. If the input is another type, an error occurs.

**Return value**:

Returns the Double or Decimal type. If the number is NULL, return NULL.

## EXP {#section_q1n_rpm_vdb .section}

**Function definition:**

```
Double exp(Double number)
Decimal exp(Decimal number)
```

**Usage:**

This function is Exponential function. 

**Return value**:

Returns the exponent value of number.

**Parameter description**:

number: Double or Decimal type.  If the input is String or Bigint, it is converted to Double by implicit conversion. If the input is another type, an error occurs.

**Return value**:

Returns the Double or Decimal type. If the number is NULL, return NULL.

## FLOOR {#section_yrw_wpm_vdb .section}

**Function definition:**

```
Bigint floor(Double number)
Bigint floor(Decimal number)
```

**Usage:**

This function returns the largest integral value not greater than the argument.

**Parameter description**:

number: Double or Decimal type. If the input is String or Bigint type, it is converted to Double by implicit conversion. If the input is another type, an error occurs.

**Return value**:

Returns the Bigint type.  If the input is null, return null.

**For example**: 

```
floor(1.2)=1
floor(1.9)=1
floor(0.1)=0
floor(-1.2)=-2
floor(-0.1)=-1
floor(0.0)=0
Floor (-0.0) = 0
```

## LN {#section_pdm_fqm_vdb .section}

**Function definition:**

```
Double ln(Double number)
Decimal ln(Decimal number)
```

**Usage:**

Returns the natural logarithm of the number.

**Parameter description**:

number: Double or Decimal type.

-   If the input is String or Bigint type, it is converted to Double by implicit conversion. If the input is another type, an error occurs.
-    If the number is null, return null. If number is negative or 0, an exception is thrown.

**Return value**:

Returns the Double or Decimal type.

## LOG {#section_iwc_4qm_vdb .section}

**Function definition:**

```
Double log(Double base, Double x)
Decimal log (decimal base, decimal X)
```

**Usage:**

 Returns the logarithm of x whose base number is base.

**Parameter description**:

-   base: Double or Decimal type. If the input is String or Bigint, it is converted to Double by implicit conversion. If the input is another type, an error occurs.
-   x: Double or Decimal type. If the input is String or Bigint, it is converted to Double by implicit conversion. If the input is another type, an error occurs.

**Return value**:

Returns the logarithm value of Double or Decimal type. 

-    If base or x is null, return null. 
-   If one of base or x is negative or zero, it causes abnormality.
-   If base is 1, it also causes abnormality.

## POW {#section_gmv_wqm_vdb .section}

**Function definition:**

```
Double pow(Double x, Double y)
Decimal pow(Decimal x, Decimal y)
```

**Usage:**

Return x to the yth power, that is x^y.

**Parameter description**:

-   X: Double or Decimal type. If the input is String or Bigint, it is converted to Double by implicit conversion. If the input is another type, an error occurs.
-   Y: Double or Decimal type. If the input is String or Bigint, it is converted to Double by implicit conversion. If the input is another type, an error occurs.

**Return value**:

Returns the Double or Decimal type. If X or Y is null, return null.

## RAND {#section_qlv_2rm_vdb .section}

**Function definition:**

```
Double rand(Bigint seed)
```

**Usage:**

Return a random number \(that changes from row to row\), Specifying the seed makes sure the generated random number sequence is deterministic, Return value range is from 0 to 1.

**Parameter description**:

seed: Bigint type, random number seed, to determine starting values of the random number sequence.

**Return Value:**

Returns the Double type.

**For example**:

```
select rand() from dual;
select rand(1) from dual;
```

## ROUND {#section_ocf_jrm_vdb .section}

**Function definition:**

```
Double round(Double number, [Bigint Decimal_places])
Decimal round(Decimal number, [Bigint Decimal_places])
```

**Usage:**

Four to five homes to the specific decimal point position.

**Parameter description**:

-   number: Double or Decimal type.  If the input is String or Bigint, it is converted to Double by implicit conversion. If the input is another type, an error occurs.
-   Decimal\_place: A Bigint type constant, four to five homes to the decimal point position. If it is other type, an exception is thrown.  If you omit it, it indicates four to five homes into the single digit. The default value is zero.

**Return value**:

Returns the Double or Decimal type.  If number or Decimal\_places is null, return null.

**Note:** Decimal\_places can be negative. The negative is counted from decimal point to left and do not keep the decimal part.  If decimal\_places is greater than the length of the integer part, return 0.

**For example**:

```
round(125.315) = 125.0
round(125.315, 0) = 125.0
Round (125.315, 1) = 125.3
round(125.315, 2) = 125.32
round(125.315, 3) = 125.315
round(-125.315, 2) = -125.32
round(123.345, -2) = 100.0
round(null) = null
round(123.345, 4) = 123.345
round(123.345, -4) = 0.0
```

## SIN {#section_tky_gvm_vdb .section}

**Function definition**:

```
Double sin(Double number)
Decimal sin(Decimal number)
```

**Usage:**

This function is used to calculate the sine function of number, the input is the radian value.

**Parameter description**:

number: Double or Decimal type.  If the input is String or Bigint, it is converted to Double by implicit conversion. If the input is another type, an error occurs.

**Return value**:

Returns the Double or Decimal type. If the number is NULL, return NULL.

## SINH {#section_ccf_gym_vdb .section}

**Function definition:**

```
Double sinh(Double number)
Decimal sinh(Decimal number)
```

**Usage:**

This function is used to calculate the hyperbolic sine function of number.

**Parameter description**:

number: Double or Decimal type.  If the input is String or Bigint, it is converted to Double by implicit conversion. If the input is another type, an error occurs.

**Return value**:

Returns the Double or Decimal type. If the number is NULL, return NULL.

## SQRT {#section_nns_lym_vdb .section}

**Function definition:**

```
Double sqrt(Double number)
Decimal sqrt(Decimal number)
```

**Usage:**

This function is used to calculate the square root of number.

**Parameter description**:

number: Double or Decimal type, must be greater than zero, if it is less than zero, an exception is caused.   If the input is String or Bigint, it is converted to Double by implicit conversion. If the input is another type, an error occurs.

**Return value**:

Returns the Double or Decimal type. If the number is NULL, return NULL.

## TAN {#section_ibd_rym_vdb .section}

**Function definition**:

```
Double tan(Double number)
Decimal tan(Decimal number)
```

**Usage:**

This function is used to calculate the tangent function of number, the input is the radian value.

**Parameter description**:

number: Double or Decimal type.  If the input is String or Bigint, it is converted to Double by implicit conversion. If the input is another type, an error occurs.

**Return value**:

Returns the Double or Decimal type. If the number is NULL, return NULL.

## TANH {#section_pfh_wym_vdb .section}

**Function definition:**

```
Double tanh(Double number)
Decimal tanh(Decimal number)
```

**Usage:**

This function is used to calculate the hyperbolic tangent function of number.

**Parameter description**:

number: Double or Decimal type.  If the input is String or Bigint, it is converted to Double by implicit conversion. If the input is another type, an error occurs.

**Return value**:

Returns the Double or Decimal type. If the number is NULL, return NULL.

## TRUNC {#section_yly_1zm_vdb .section}

**Function definition:**

```
Double trunc(Double number[, Bigint Decimal_places])
Decimal trunc(Decimal number[, Bigint Decimal_places])
```

**Usage:**

This function is used to intercept the input number to a specified decimal point place.

**Parameter description**:

-   number: Double or Decimal type. If the input is String or Bigint, it is converted to Double by implicit conversion. If the input is another type, an error occurs.
-   Decimal\_places: a Bigint type constant, the decimal point place to intercept the number. Other types are converted to Bigint. If this parameter is omitted, default to intercept to single digit.

**Return value**:

Returns the  Double or Decimal type. If the number or Decimal\_places is NULL, return NULL.

**Note:** 

-   If the Double type is returned, the display of the returned result may not be as expected, such as `trunc(125.815, 1)` \(this problem exists in any system\).
-   The part to be truncated is supplemented with zero.
-   Decimal\_places can be negative. The negative is truncated from the decimal point to the left and does not keep the decimal part.  If Decimal\_places are greater than the length of the integer, return zero.

**For example**:

```
trunc(125.815) = 125.0
trunc(125.815, 0) =125.0
trunc(125.815, 1) = 125.80000000000001
trunc(125.815, 2) = 125.81
trunc(125.815, 3) = 125.815
trunc(-125.815, 2) = -125.81
trunc(125.815, -1) = 120.0
trunc(125.815, -2) = 100.0
trunc(125.815, -3) = 0.0
trunc(123.345, 4) = 123.345
trunc(123.345, -4) = 0.0
```

## Maxcomputerte2.0 New Extended Mathematical Functions {#section_izz_qzm_vdb .section}

With the upgrade to MaxCompute 2.0, some mathematical functions have been added to the product. If a new function uses a new data type, you must add the following set statement before using the new functions SQL statement:

```
set odps.sql.type.system.odps2=true;
```

The new extended functions are described as follows.

## LOG2 {#section_dh3_tzm_vdb .section}

**Function definition:**

```
Double log2(Double number)
Double log2(Decimal number)
```

**Note:** Before the SQL statement that uses the function you should add `set odps.sql.type.system.odps2=true;`  for normal use of the new data type.

**Usage:**

This function is used to return the log base 2 of a specific number.

**Parameter description**:

number: Double or Decimal type.

**Return Value:**

Returns the Double type. If the input is zero or null, the returned value is null.

**The example is as follows**:

```
log2(null)=null
log2(0)=null
log2(8)=3.0
```

## LOG10 {#section_bjc_zzm_vdb .section}

**Function definition:**

```
Double log10(Double number)
Double log10(Decimal number)
```

**Note:** Before the SQL statement that uses the function you should add `set odps.sql.type.system.odps2=true;` for normal use of the new data type.

**Usage:**

This function is used to returns the log base 10 of the specific number.

**Parameter description**:

number: Double or Decimal type.

**Return Value:**

Returns the Double type. If the input is zero or null, the returned value is null.

**The example is as follows**:

```
log10(null)=null
log10(0)=null
log10(8)=0.9030899869919435
```

## BIN {#section_ucn_21n_vdb .section}

**Function definition:**

```
String bin(Bigint number)
```

**Note:** Before the SQL statement that uses the function you should add `set odps.sql.type.system.odps2=true;` for normal use of the new data type.

**Usage:**

This function is used to returns the binary code expression for the specific number.

**Parameter description**:

number: Bigint type.

**Return value**: 

String type.  If the input is zero, then zero is returned; if the input is null, null is returned.

**For example**:

```
bin(0)='0'
bin(null)='null'
bin(12)='1100'
```

## HEX {#section_nxv_j1n_vdb .section}

**Function definition:**

```
String hex(Bigint number) 
String hex(String number)
String hex (binary number)
```

**Note:** Before the SQL statement that uses the function you should add `set odps.sql.type.system.odps2=true;` for normal use of the new data type.

**Usage:**

This function is used to converts integers or characters to hexadecimal format.

**Parameter description**:

number: If number is of the Bigint type, the hexadecimal format of the number is returned. If this variable is a String type, the hexadecimal format of the string is returned.

**Return value**: 

Returns the String type. If the input is zero, then zero is returned; if the input is null, an exception is returned.

**For example**:

```
hex(0)=0
hex('abc')='616263'
hex(17)='11'
hex('17')='3137'
hex(null) results in an exception and returns failed.
```

**Note:** When the input parameter is a Binary type, add `set  odps.sql.type.system.odps2=true;`, and submit it with SQL to use the new data type normally.

## UNHEX {#section_k5x_51n_vdb .section}

**Function definition:**

```
BINARY unhex(String number)
```

**Note:** Before the SQL statement that uses the function you should add `set odps.sql.type.system.odps2=true;` for normal use of the new data type.

**Usage:**

This function is used to returns the string represented by a given hexadecimal string.

**Parameter description**:

number: A hexadecimal string.

**Return value**:

Returns the Binary type. If the input is zero, failed is returned. If the input is null, null is returned.

**For example**:

```
Unhex ('616263') = 'abc'
unhex(616263)='abc'
```

## RADIANS {#section_dwg_1bn_vdb .section}

**Function definition:**

```
Double radians(Double number)
```

**Note:** Before the SQL statement that uses the function you should add `set odps.sql.type.system.odps2=true;` for normal use of the new data type.

**Usage:**

This function is used to converts degrees to radians.

**Parameter description**:

number: Double type.

**Return value**:

Returns the Double type, if the input is null, null is returned.

**For example**:

```
radians(90)=1.5707963267948966
radians(0)=0.0
radians(null)=null
```

## DEGREES {#section_adl_hbn_vdb .section}

**Function definition:**

```
Double degrees(Double number) 
Double degrees(Decimal number)
```

**Note:** Before the SQL statement that uses the function you should add `set odps.sql.type.system.odps2=true;` for normal use of the new data type.

**Usage:**

This function is used to converts radians to degrees.

**Parameter description**:

number: Double or Decimal type.

**Return value**:

Returns Double data type. If the input is null, null is returned.

**For example**:

```
degrees(1.5707963267948966)=90.0
degrees(0)=0.0
Degrees (null) = NULL
```

## SIGN {#section_gq5_lbn_vdb .section}

**Function definition:**

```
Double sign(Double number)
Double sign(Decimal number)
```

**Note:** Before the SQL statement that uses the function you should add `set odps.sql.type.system.odps2=true;` for normal use of the new data type.

**Usage:**

This function is used to takes the sign of the input data. 1.0 indicates positive and -1.0 indicates negative. Otherwise, 0.0 is returned.

**Parameter description**:

number: Double or Decimal type.

**Return value**:

Returns Double data type. If the input is 0, 0.0 is returned. If the input is null, null is returned.

**For example**:

```
sign(-2.5)=-1.0
Sign (2.5) = 1.0
sign(0)=0.0
sign(null)=null
```

## E {#section_yfc_zbn_vdb .section}

**Function definition:**

```
Double e()
```

**Note:** Before the SQL statement that uses the function you should add `set odps.sql.type.system.odps2=true;` for normal use of the new data type.

**Usage:**

This function is used to return the e value.

**Return Value:**

Returns the Double type.

**For example**:

```
e()=2.718281828459045
```

## PI {#section_hhc_dcn_vdb .section}

**Function definition:**

```
Double pi()
```

**Note:** Before the SQL statement that uses the function you should add `set odps.sql.type.system.odps2=true;` for normal use of the new data type.

**Usage:**

This function is used to return the π value.

**Return Value:**

Returns the Double type.

**For example**:

```
pi()=3.141592653589793
```

## FACTORIAL {#section_umk_gcn_vdb .section}

**Function definition:**

```
Bigint factorial(Int number)
```

**Note:** Before the SQL statement that uses the function you should add `set odps.sql.type.system.odps2=true;` for normal use of the new data type.

**Usage:**

This function is used to return the factorial for the specific number.

**Parameter description**:

number: Int-type data, range: \[0 –20\].

**Return value**:

Returns the Bigint type, if the input is zero, one is returned. If the input is null or outside the range \[0 –20\], null is returned.

**For example**:

```
factorial(5)=120 --5! = 5*4*3*2*1 = 120
```

## CBRT {#section_frl_lcn_vdb .section}

**Function definition:**

```
Double cbrt(Double number)
```

**Note:** Before the SQL statement that uses the function you should add `set odps.sql.type.system.odps2=true;` for normal use of the new data type.

**Usage:**

This function is used to return the cube root.

**Parameter description**:

number: Double type.

**Return value**:

Returns Double data type. If the input is null, null is returned.

**For example**:

```
cbrt(8)=2
cbrt(null)=null
```

## SHIFTLEFT {#section_k4z_pcn_vdb .section}

**Function definition:**

```
Int shiftleft(Tinyint|Smallint|Int number1, Int number2)
Bigint shiftleft(Bigint number1, Int number2)
```

**Note:** Before the SQL statement that uses the function you should add `set odps.sql.type.system.odps2=true;` for normal use of the new data type.

**Usage:**

This function is used for shifts left by a given number of places \(<<\).

**Parameter description**:

-   number1: Tinyint|Smallint|Int|Bigint integer.
-   number2: An Int integer.

**Return value**:

Returns the Int or Bingint type.

**For example**:

```
shiftleft(1,2)=4  --Shifts the binary value of 1 two places to the left (1<<2,0001 shifted to 0100)
shiftleft(4,3)=32  --Shifts the binary value of 4 three places to the left (4<<3,0100 shifted to 10,0000)
```

## SHIFTRIGHT {#section_iyl_vcn_vdb .section}

**Function definition:**

```
Int shiftright(Tinyint|Smallint|Int number1, Int number2)
Bigint shiftright(Bigint number1, Int number2)
```

**Note:** Before the SQL statement that uses the function you should add `set odps.sql.type.system.odps2=true;` for normal use of the new data type.

**Usage:**

This function is used for shifts right by a given number of places \(\>\>\).

**Parameter description**:

-   number1: Tinyint|Smallint|Int|Bigint integer.
-   number2: An Int integer.

**Return value**:

Returns the Int or Bigint type.

**For example**:

```
shiftright(4,2)=1 -- Shifts the binary value of 4 two places to the right (4>>2,0100 shifted to 0001)
shiftright(32,3)=4 -- Shifts the binary value of 32 three places to the right (32>>3,100000 shifted to 0100)
```

## SHIFTRIGHTUNSIGNED {#section_h2f_1dn_vdb .section}

**The command format is as follows**:

```
Int shiftrightunsigned(Tinyint|Smallint|Int number1, Int number2)
Bigint shiftrightunsigned(Bigint number1, Int number2)
```

**Note:** Before the SQL statement that uses the function you should add `set odps.sql.type.system.odps2=true;` for normal use of the new data type.

**The command description is as follows**:

This function is used for unsigned right shift by a given number of places \(\>\>\>\).

**Parameter description**:

-   number1: Tinyint|Smallint|Int|Bigint integer.
-   number2: An Int integer.

**Return value**:

Returns the Int or Bigint type.

**For example**:

```
shiftrightunsigned(8,2)=2 -- Shifts the unsigned binary value of 8 two places to the right (8>>>2,1000 shifted to 0010)
shiftrightunsigned(-14,2)=1073741820  -- Shifts the unsigned binary value of -14 two places to the right (-14>>>2, 11111111 11111111 11111111 11110010 shifted to 00111111 11111111 11111111 11111100)
```

