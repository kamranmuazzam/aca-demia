-
- some num **=** some other-num: Number.equal(that num that other-num
- some num **<** some other-num: Number.less(that num that other-num
- some num **><** some other-num: Number.notequal(that num that other-num
- some num **>=** some other-num: Number.notless(that num that other-num
- some num **<=** some other-num: Number.notmore(that num that other-num
- some num **>** some other-num: Number.more(that num that other-num
-
- some value-1:
	- **+** some value-2: rather that value-1 plus that value-2
		- + etc.: that value-1 + that value-2) + etc.
		- - etc.: that value-1 + that value-2) - etc.
		- * some value-3:
			- + etc.: that value-1 + (that value-2 * that value-3) + etc.
			- - etc.: that value-1 + (that value-2 * that value-3) - etc.
			- * etc.: that value-1 + (that value-2 * that value-3) * etc.
			- / etc.: that value-1 + (that value-2 * that value-3) / etc.
			- ^ some value-4 etc.: that value-1 + that value-2 * (that value-3 ^ that value-4) etc.
		- / some value-3:
			- + etc.: that value-1 + (that value-2 / that value-3) + etc.
			- - etc.: that value-1 + (that value-2 / that value-3) - etc.
			- * etc.: that value-1 + (that value-2 / that value-3) * etc.
			- / etc.: that value-1 + (that value-2 / that value-3) / etc.
			- ^ some value-4 etc.: that value-1 + that value-2 / (that value-3 ^ that value-4) etc.
		- ^ some value-3 etc.: that value-1 + (that value-2 ^ that value-3) etc.
	- **plus** some value-2: sum of that value-1 and that value-2
- some **value** plus some **num**: num from that value) plus that num
- some **num**:
	- **+** some **other-num**: num from adding that num to that other-num
	- **plus**:
		- some **value**: that num plus (num from that value
		- some **other-num**: num from adding that num to that other-num
- **sum** of:
	- some **value** and some other-value: sum of (num from that value) and (num from that other-value
	- some **num** and some other-num: num from adding that num to that other-num
-
- some value-1:
	- **-** some value-2: rather that value-1 minus that value-2
		- + etc.: that value-1 - that value-2) + etc.
		- - etc.: that value-1 - that value-2) - etc.
		- * some value-3:
			- + etc.: that value-1 - (that value-2 * that value-3) + etc.
			- - etc.: that value-1 - (that value-2 * that value-3) - etc.
			- * etc.: that value-1 - (that value-2 * that value-3) * etc.
			- / etc.: that value-1 - (that value-2 * that value-3) / etc.
			- ^ some value-4 etc.: that value-1 - that value-2 * (that value-3 ^ that value-4) etc.
		- / some value-3:
			- + etc.: that value-1 - (that value-2 / that value-3) + etc.
			- - etc.: that value-1 - (that value-2 / that value-3) - etc.
			- * etc.: that value-1 - (that value-2 / that value-3) * etc.
			- / etc.: that value-1 - (that value-2 / that value-3) / etc.
			- ^ some value-4 etc.: that value-1 - that value-2 / (that value-3 ^ that value-4) etc.
		- ^ some value-3 etc.: that value-1 - (that value-2 ^ that value-3) etc.
	- **minus** some value-2: difference of that value-2 from that value-1
- some **num**:
	- **-** some **other-num**: num from subtracting that other-num from that num
	- **minus**:
		- some **value**: that num minus (num from that value
		- some **other-num**: num from subtracting that other-num from that num
- **difference** of:
	- some **value** from some other-value: difference of (num from that value) from (num from that other-value
	- some subtrahend-num from some minuend-num: num from subtracting that subtrahend from that minuend
-
- some **value**:
	- * some other-value: that-value times that other-value
	- **times** some other-value: num from that value) times that other-value
-
- some **num-1**:
	- * some **num-2**: num from multiplying that num-1 with that num-2
		- + etc.: that num-1 * that num-2) + etc.
		- - etc.: that num-1 * that num-2) - etc.
		- * etc.: that num-1 * that num-2) * etc.
		- / etc.: that num-1 * that num-2) / etc.
		- ^ some num-3 etc.: that num-1 * (that num-2 ^ that num-3) etc.
	- **times**:
		- some **value**: that num-1 times (num from that value
		- some **num-2**: num from multiplying that num-1 with that num-2
- **product** of:
	- some **value** and some other-value: product of (num from that value) and (num from that other-value
	- some **num** and some other-num: num from multiplying that num with that other-num
-
- some num **squared**: square of that num
- **square** of some num: that num ^ 2
- some base-num **^** some exponent-num: num from raising that base to that exponent
	- + etc.: that base ^ that exponent) + etc.
	- - etc.: that base ^ that exponent) - etc.
	- * etc.: that base ^ that exponent) * etc.
	- / etc.: that base ^ that exponent) / etc.
	- ^ etc.: that base ^ that exponent) ^ etc.
- **power** of:
	- some **value** to some other-value: power of (num from that value) to (num from that other-value
	- some base-num to some exponent-num: num from raising that base to that exponent
-
- **round** some num: Math.round(that num
- **random** num: Math.random()
-
- some value-1:
	- **/** some value-2: that value-1 per that value-2
		- + etc.: that value-1 / that value-2) + etc.
		- - etc.: that value-1 / that value-2) - etc.
		- * etc.: that value-1 / that value-2) * etc.
		- / etc.: that value-1 / that value-2) / etc.
		- ^ some value-3 etc.: that value-1 / (that value-2 ^ value-3) etc.
	- **per** some value-2: ratio of that value-1 per that value-2
- some **num**:
	- **/** some **other-num**: num from dividing that num by that other-num
	- **per**:
		- some **value**: that num per (num from that value
		- some **other-num**: num from dividing that num by that other-num
- **ratio** of:
	- some value per some other-value: ratio of (num from that value) per (num from that other-value
	- some dividend-num per some divisor-num: num from dividing that dividend by that divisor
-
- some value \ some other-value: quotient of that value divided by that other-value
- **quotient** of:
	- some value divided by some other-value: quotient of (num from that value) divided by (num from that other-value
	- some dividend-num divided by some divisor-num: Number.quotient(that dividend that divisor
-
- some value **%** some other-value: remainder of that value divided by that other-value
- **remainder** of:
	- some value divided by some other-value: remainder of (num from that value) divided by (num from that other-value
	- some dividend-num divided by some divisor-num: Number.remainder(that dividend that divisor
-
- num **from**:
	- some value: Number.from(that value
	- **adding** some num to some other-num: Number.sum(that num that other-num
	- **dividing** some dividend-num by some divisor-num: Number.ratio(that dividend that divisor
	- **multiplying** some num with some other-num: Number.product(that num that other-num
	- **raising** some base-num to some exponent-num: Math.pow(that base that exponent
	- **subtracting** some subtrahend-num from some minuend-num: Number.difference(that subtrahend that minuend
	- zero: 0
	- one: 1
	- two: 2
	- three: 3
	- four: 4
	- five: 5
	- six: 6
	- seven: 7
	- eight: 8
	- nine: 9
	- ten: 10
-
- $execute
  ```javascript
  logseq.Module.Math = Math
  const Is = Syn.Evaluation.is
  const lookup = Syn.Normal.prototype.evalIn.forType.lookup
  const valueOf = Syn.Type.valueOf
  const No = Syn.Word("no")
  const Yes = Syn.Word("yes")
  
  const Number = Syn.Number = Syn.setChild("Number")
  .setStatic(function addition(n1, n2){ return n1 + n2 })
  .setStatic(function byLookup(obj){
      return lookup.call(Number.From, this, [], [obj])
  })
  .setStatic(function difference(subtrahend, minuend){
      return from(minuend) - from(subtrahend)
  })
  .setStatic(function equal(n1, n2){
      return (n1 === n2) ? Yes : No
  })
  .setStatic(function from(obj){
      if (!Is(obj)) return 0
      if (Syn.Error.isNorm(obj)) return obj
  
      const num = top.Number(valueOf(obj)).valueOf()
      return (isNaN(num)) ? 1 : num
  })
  .setStatic(function less(n1, n2){
      return (n1 < n2) ? Yes : No
  })
  .setStatic(function multiplication(n1, n2){ return n1 * n2 })
  .setStatic(function notequal(n1, n2){
      return (n1 !== n2) ? Yes : No
  })
  .setStatic(function notless(n1, n2){
      return (n1 >= n2) ? Yes : No
  })
  .setStatic(function notmore(n1, n2){
      return (n1 <= n2) ? Yes : No
  })
  .setStatic(function more(n1, n2){
      return (n1 > n2) ? Yes : No
  })
  .setStatic(function product(factor1, factor2){
      return from(factor1) * from(factor2)
  })
  .setStatic(function quotient(dividend, divisor){
      dividend = from(dividend)
      divisor = from(divisor)
      return (dividend - dividend % divisor) / divisor
  })
  .setStatic(function ratio(dividend, divisor){
      return from(dividend) / from(divisor)
  })
  .setStatic(function remainder(dividend, divisor){
      return from(dividend) % from(divisor)
  })
  .setStatic(function sum(addend1, addend2){
      return from(addend1) + from(addend2)
  })
  
  .setStatic(function Product(...args){
      const func = (this.evalion) ? Number.byLookup.bind(this) : Number.from
      const last = Syn.Error.inListOrCallThis.bind(Product.fromArray)
      return Syn.List.mapThen.call(this, args, func, last)
  }
  .setStatic(function fromArray(arr){
      return arr.reduce(Number.multiplication, 1)
  })
  )
  
  .setStatic(function Sum(...args){
      const func = (this.evalion) ? Number.byLookup.bind(this) : Number.from
      const last = Syn.Error.inListOrCallThis.bind(Sum.fromArray)
      return Syn.List.mapThen.call(this, args, func, last)
  }
  .setStatic(function fromArray(arr){
      return arr.reduce(Number.addition, 0)
  })
  )
  
  Number.From = Syn.Normal("lookup", "num-from-.", ["num", "from", "."])
  const from = Number.from
  ```
- .