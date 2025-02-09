-
- $include Synthesis/Collections, Synthesis/Number, Synthesis/Text
-
- some value **=** some other-value:
  ```javascript
  function identical(v1, v2){
      return (v1 === v2) ? Yes : No
  }
  const No = Syn.Word("no")
  const Yes = Syn.Word("yes")
  return identical
  ```
-
- **type** of some value: Word(Type.of(that value)'s body;
- **value** of some value: Type.valueOf(that value
-
- some value **as** some word: that word from that value
- some **word** from some value:
  ```javascript
  function wordFrom(typechain, value){
      const converted = valueOf(value)
      const type = fromChain(typechain.body || "NaT")
      return Syn.Typed(type.body, converted, type.head)
  }
  const fromChain = Syn.Type.fromChain
  const valueOf = Syn.Type.valueOf
  return wordFrom
  ```
-
- **the** etc.: etc.
-
- **func** from some value: Evaluation.Context.prototype.getFunc(that value
-
- **ask** some word: ask that word void
	- some value: Evaluation.Context.prototype.say(that word that value)
- **give**:
	- some value: give (?what OR given) that value
	- some word some value: Evaluation.Context.prototype.say(that word that value); void
-
- **log** some value: JS.console.log(that value
- **output** some value: Evaluation.Context.prototype.logValue(that value
-
- **if** some cond-value **then** some is-value: Evaluation.Context.prototype.branch(that cond, that is
	- **else** some isn't-value: Evaluation.Context.prototype.branch(that cond, that is, that isn't
-
- some value:
	- **AND** some other-value: Evaluation.Context.prototype.branch(that value, that other-value
	- **OR** some value:
	  ```javascript
	  function or(cond, onIsnt){
	      const res = Is(cond)
	      if (res) return cond
	  
	      this.evalion.log("logHolder", onIsnt.head)
	      return onIsnt
	  }
	  const Is = Syn.Evaluation.is
	  return or
	  ```
- **NOT** some value:
  ```javascript
  function not(cond){
      return Is(cond) ? No : Yes
  }
  const Is = Syn.Evaluation.is
  const No = Syn.Word("no")
  const Yes = Syn.Word("yes")
  return not
  ```
-
- **html** for some value: Syn.htmlFor(that value)
-
- **paste** text: Clipboard.readText.$call(self.Clipboard)
-
- **print** some **word**: let lookup that (that word); if that lookup then (print that lookup) else that word
- .