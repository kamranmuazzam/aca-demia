-
- the **lower** case of some text: String.prototype.toLowerCase.$call(that text
- the **upper** case of some text: String.prototype.toUpperCase.$call(that text
-
- some text **plus** some other-text: Text(that text that other-text
- some text plus some **value**: that text plus (text from that value
- some **value** plus some text: text from that value) plus that text
-
- some num **times** some text:
  ```javascript
  function times(factor, value){
      value = Syn.Text.from(value)
      factor = valueOf(factor)
      if (factor < 0) {
          value = value.split("").reverse().join("")
          factor = -factor
      }
      return value.repeat(factor)
  }
  const valueOf = Syn.Type.valueOf
  return times
  ```
-
- text **from**:
	- some value: Text.from(that value
	- backslash: "\"
	- colon: ":"
	- comma: ","
	- dash: "-"
	- newline: "\n"
	- slash: "/"
	- space: " "
	- tab: "\t"
	- void: ""
- some base-text:
	- **begins** with some prefix-text: if String.prototype.startsWith.$call(that base, that prefix) then yes else no
	- **ends** with some suffix-text: if String.prototype.endsWith.$call(that base, that suffix) then yes else no
	- **first** some num: String.prototype.slice.$call(that base, 0, that num
	- **last** some num: String.prototype.slice.$call(that base, 0 minus that num
	- **sans**:
		- **prefix** some prefix-text: if (that base begins with that prefix) then this slice else that base
			- this slice: that base last ((length of that base) minus (length of that prefix)
		- **suffix** some suffix-text: if (that base ends with that suffix) then this slice else that base
			- this slice: that base first ((length of that base) minus (length of that suffix)
-
- $execute
  ```javascript
  logseq.Module.String = String
  const Normal = Syn.Normal
  const lookup = Normal.prototype.evalIn.forType.lookup
  const valueOf = Syn.Type.valueOf
  const Void = Syn.Word("void")
  
  const Print = Syn.Print = function Print(...args){
      const func = (this.evalion) ? Print.byLookup.bind(this) : Text
      return Syn.List.mapThen.call(this, args, func, (items)=>items.join("\n") )
  }
  .setStatic(function byLookup(obj){
      return lookup.call(Print.print, this, [], [obj])
  })
  Print.print = Normal("lookup", "print-.", ["print", "."])
  
  const Text = Syn.Text = function Text(...args){
      const func = (this.evalion) ? Text.byLookup.bind(this) : Text.from
      return Syn.List.mapThen.call(this, args, func, (items)=>items.join("") )
  }
  .setStatic(function byLookup(obj){
      return lookup.call(Text.From, this, [], [obj])
  })
  .setStatic(function from(obj){
      return (obj === Void || obj === undefined || obj === null) ? ""
           : String(valueOf(obj)).valueOf()
  })
  Text.From = Normal("lookup", "text-from-.", ["text", "from", "."])
  ```
- .